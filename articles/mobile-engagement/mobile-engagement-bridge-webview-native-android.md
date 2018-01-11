---
title: Bridge Android webbvy med inbyggda Mobile Engagement Android SDK
description: "Beskriver hur du skapar en brygga mellan webbvy kör Javascript och inbyggda Mobile Engagement Android SDK"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f4fc7b3c81747ec80974a99084eeb1acc311f11f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Bridge Android webbvy med inbyggda Mobile Engagement Android SDK
> [!div class="op_single_selector"]
> * [Android platslänksbrygga](mobile-engagement-bridge-webview-native-android.md)
> * [iOS platslänksbrygga](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Vissa mobila appar är utformade som en hybrid-app där själva appen utvecklas med hjälp av interna Android-utveckling men vissa eller alla skärmarna återges i en Android webbvy. Du kan fortfarande använda Mobile Engagement Android SDK i dessa appar och den här självstudiekursen beskrivs hur du gör detta. Exempelkoden nedan baseras på Android-dokumentationen [här](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Beskriver hur den här dokumenterade metoden kan användas för att genomföra samma för Mobile Engagement Android SDK: ns ofta använda metoderna, så att en webbvy från en hybrid-app kan också initiera begäranden om att spåra händelser, jobb, fel, app-info när skicka dem via vår Android SDK. 

1. Först och främst måste du kontrollera att du har gått igenom våra [komma igång-kursen](mobile-engagement-android-get-started.md) att integrera Mobile Engagement Android SDK i din hybrid-app. När du har gjort det, din `OnCreate` metoden kommer att se ut ungefär så här.  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. Nu se till att appen hybrid har en skärm med en webbvy på den. Koden för den kommer att likna följande där vi läser in en lokal HTML-fil **Sample.html** i webbvy i den `onCreate` metoden på skärmen. 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. Nu skapa en brygga-fil som heter **WebAppInterface** som skapar en omslutning över vissa ofta används Mobile Engagement Android SDK-metoder som använder den `@JavascriptInterface` metod som beskrivs i den [Android dokumentationen](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. Vi måste se till att den är associerad med vår webbvy när vi har skapat filen ovanför bridge. För att detta ska hända, måste du redigera din `SetWebview` metoden så att den ser ut som följande:
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. Ovanstående kodutdrag vi har ringt `addJavascriptInterface` associera vår bridge klass med vår webbvy och dessutom skapas en referens som kallas **EngagementJs** att anropa metoder från filen bridge. 
6. Nu skapa följande fil som kallas **Sample.html** i ditt projekt i en mapp som kallas **tillgångar** som läses in i webbvyn och där vi ringer metoderna från filen bridge.
   
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
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. Observera följande om filen HTML:
   
   * Den innehåller en uppsättning inkommande rutor där du kan ange data som ska användas som namn för din händelse, jobb, fel, AppInfo. När du klickar på knappen bredvid den görs ett anrop för Javascript som eventuellt anropar metoder från filen bridge att skicka det här anropet till Mobile Engagement Android SDK. 
   * Vi märkning på vissa statiska extra information för att händelser, jobb och även fel att demonstrera hur detta kan göras. Den här extra informationen skickas som en JSON-strängen, som om du tittar i den `WebAppInterface` filen parsas och placeras i en Android `Bundle` och skickas tillsammans med skickar händelser, jobb, fel. 
   * Ett jobb för Mobile Engagement har inletts med namnet du anger i textrutan, kör för 10 sekunder och stänga av. 
   * En Mobile Engagement appinfo eller taggen skickas med customer_name om du som statisk nyckeln och värdet som du angav i indata som värde för taggen. 
8. Kör appen och du ser nedan. Nu anger du ett namn för en testhändelser som liknar följande och klickar på **skicka** under den. 
   
    ![][1]
9. Nu om du går till den **övervakaren** fliken i din app och titta under **händelser -> information**, visas den här händelsen visas tillsammans med statisk appinfon som vi skickar. 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
