---
title: "Överbrygga iOS webbvy med inbyggda Mobile Engagement iOS SDK"
description: "Beskriver hur du skapar en brygga mellan webbvy kör Javascript och inbyggda Mobile Engagement iOS SDK"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 35f7bdbeb480122513ae2a0b04a6d8cfd426802a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Överbrygga iOS webbvy med inbyggda Mobile Engagement iOS SDK
> [!div class="op_single_selector"]
> * [Android platslänksbrygga](mobile-engagement-bridge-webview-native-android.md)
> * [iOS platslänksbrygga](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Vissa mobila appar är utformade som en hybrid-app där själva appen utvecklas med hjälp av inbyggda iOS Objective-C-utveckling men vissa eller alla skärmarna återges i en iOS webbvy. Du kan fortfarande använda Mobile Engagement iOS SDK i dessa appar och den här självstudiekursen beskrivs hur du gör detta. 

Det finns två sätt att göra detta även om båda är odokumenterade:

* Först beskrivs en på den här [länk](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) som innebär att registrera en `UIWebViewDelegate` på webbvy och catch-och-omedelbart-Avbryt en ändring i Javascript. 
* Andra en baseras på detta [WWDC 2013 session](https://developer.apple.com/videos/play/wwdc2013/615), en metod som är tydligare än först och som vi följer för den här handboken. Observera att den här metoden fungerar bara i iOS7 och senare. 

Följ stegen nedan för iOS överbrygga exempel:

1. Först och främst måste du kontrollera att du har gått igenom våra [komma igång-kursen](mobile-engagement-ios-get-started.md) att integrera Mobile Engagement iOS SDK i din hybrid-app. Alternativt kan aktivera du också test loggning på följande sätt så att du kan se SDK-metoder som vi utlöser dem från webbvyn. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. Nu se till att appen hybrid har en skärm med en webbvy på den. Du kan lägga till den till den `Main.storyboard` av appen. 
3. Associera den här webbvy med din **ViewController** genom att klicka och dra webbvyn från View Controller scen till den `ViewController.h` redigera skärmen, att den placeras precis nedanför den `@interface` rad. 
4. När du gör det visas en dialogruta som frågar om ett namn. Ange namnet som **webbvy**. Din `ViewController.h` fil ska se ut ungefär så här:
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. Vi kommer att uppdatera den `ViewController.m` filen senare, men vi kommer först att skapa filen brygga som skapar en omslutning över vissa vanliga Mobile Engagement iOS SDK-metoder. Skapa en ny rubrikfil kallas **EngagementJsExports.h** som använder den `JSExport` mekanism som beskrivs i denna [session](https://developer.apple.com/videos/play/wwdc2013/615) att exponera interna iOS-metoder. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. Därefter skapar vi den andra delen av filen bridge. Skapa en fil med namnet **EngagementJsExports.m** som innehåller implementeringen skapar faktiska omslutningar genom att anropa Mobile Engagement iOS SDK-metoder. Även Observera att vi parsning av `extras` som skickas från webbvy javascript och skicka som i en `NSMutableDictionary` objekt som ska skickas med metodanrop Engagement SDK.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. Nu kommer tillbaka den **ViewController.m** och uppdatera den med följande kod: 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. Observera följande om den **ViewController.m** fil:
   
   * I den `loadWebView` metod, vi läser in en lokal HTML-fil som kallas **LocalPage.html** som koden som vi granskar nästa. 
   * I den `webViewDidFinishLoad` -metoden som vi ta tag i `JsContext` och associera vår vinjettklass med den. Detta gör att anropa vår wrapper SDK metoder genom att använda handtaget **EngagementJs** från webbvyn. 
9. Skapa en fil med namnet **LocalPage.html** med följande kod:
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. Observera följande om filen HTML:
    
    * Den innehåller en uppsättning inkommande rutor där du kan ange data som ska användas som namn för din händelse, jobb, fel, AppInfo. När du klickar på knappen bredvid den görs ett anrop för Javascript som eventuellt anropar metoder från filen bridge att skicka det här anropet till Mobile Engagement iOS SDK. 
    * Vi märkning på vissa statiska extra information för att händelser, jobb och även fel att demonstrera hur detta kan göras. Den här extra informationen skickas som en JSON-strängen, som om du tittar i den `EngagementJsExports.m` filen parsas och skickas tillsammans med skickar händelser, jobb, fel. 
    * Ett jobb för Mobile Engagement har inletts med namnet du anger i textrutan, kör för 10 sekunder och stänga av. 
    * En Mobile Engagement appinfo eller taggen skickas med customer_name om du som statisk nyckeln och värdet som du angav i indata som värde för taggen. 
11. Kör appen och du ser nedan. Nu anger du ett namn för en testhändelser som liknar följande och klickar på **skicka** bredvid den. 
    
     ![][1]
12. Nu om du går till den **övervakaren** fliken i din app och titta under **händelser -> information**, visas den här händelsen visas tillsammans med statisk appinfon som vi skickar. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
