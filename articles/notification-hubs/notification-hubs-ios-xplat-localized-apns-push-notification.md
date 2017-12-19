---
title: "Notification Hubs lokaliserade bryta nyheter kursen för iOS"
description: "Lär dig hur du använder Azure Service Bus Notification Hubs för att skicka lokaliserade senaste nyheterna meddelanden (iOS)."
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: fd2b7d9dfd4f432bbcbaa3ed76f8bec0b9677e17
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Använda Notification Hubs för att skicka lokaliserade senaste nyheterna till iOS-enheter
> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du använder den [mallar](notification-hubs-templates-cross-platform-push-messages.md) funktion i Azure Notification Hubs för att sända senaste nyheterna meddelanden som har översatts av språk och enheten. I den här kursen börjar du med iOS-app som skapats i [använda Notification Hubs för att skicka de senaste nyheterna]. När du är klar kommer du att kunna registrera för kategorier som du vill, ange ett språk som ska ta emot meddelanden och får endast push-meddelanden i valda kategorier på det språket.

Det finns två delar i det här scenariot:

* iOS-app kan klienten enheter för att ange ett språk och prenumerera på olika senaste nyheterna kategorier.
* backend-skickar meddelanden med hjälp av den **taggen** och **mallen** feautres i Azure Notification Hubs.

## <a name="prerequisites"></a>Krav
Du måste redan har slutfört den [använda Notification Hubs för att skicka de senaste nyheterna] självstudier och har kod som är tillgängliga, eftersom den här kursen bygger direkt på koden.

Visual Studio 2012 eller senare är valfritt.

## <a name="template-concepts"></a>Mall-begrepp
I [använda Notification Hubs för att skicka de senaste nyheterna] du skapat en app som används för **taggar** att prenumerera på meddelanden om nyheterna i olika kategorier.
Många appar dock mål flera marknader och kräver lokalisering. Detta innebär att innehållet i meddelandena själva måste lokaliserade och levereras till rätt uppsättning enheter.
I det här avsnittet visar vi hur du använder den **mallen** funktion i Notification Hubs för att leverera enkelt lokaliserade senaste nyheterna meddelanden.

Obs: ett sätt att skicka lokaliserade meddelanden är att skapa flera versioner av varje tagg. Till exempel för att stödja engelska, franska och Mandarin, vi behöver tre olika taggar för world news: ”world_en”, ”world_fr” och ”world_ch”. Vi har sedan skicka en lokaliserad version av world nyheter till var och en av dessa taggar. Vi använder mallar för att undvika alltför många taggar och kravet flera meddelanden i det här avsnittet.

Mallar är ett sätt att ange hur en specifik enhet bör få ett meddelande på en hög nivå. Mallen anger exakt nyttolastformatet genom att referera till egenskaper som ingår i meddelandet som skickas av din appens serverdel. I vårt fall skickar vi ett språkvariant-oberoende meddelande som innehåller alla språk som stöds:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Vi kommer sedan att säkerställa att registrera enheter med en mall som refererar till egenskapen korrekt. Exempelvis kan registrera en iOS-app som du vill registrera för franska nyheter följande:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Mallar är en mycket kraftfull funktion kan du läsa mer om i vår [mallar](notification-hubs-templates-cross-platform-push-messages.md) artikel.

## <a name="the-app-user-interface"></a>Användargränssnittet för app
Vi kommer nu att ändra bryta nyheter appen som du skapade i avsnittet [använda Notification Hubs för att skicka de senaste nyheterna] att skicka lokaliserade senaste nytt med hjälp av mallar.

Lägga till en segmenterade kontroll med de tre språken som vi stöder din MainStoryboard_iPhone.storyboard: engelska, franska och Mandarin.

![][13]

Kontrollera sedan att lägga till en IBOutlet i din ViewController.h enligt nedan:

![][14]

## <a name="building-the-ios-app"></a>Skapa iOS-app
1. Lägg till i din Notification.h den *retrieveLocale* -metoden och ändra arkivet och prenumerera metoder som visas nedan:
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    Ändra i din Notification.m den *storeCategoriesAndSubscribe* metoden genom att lägga till parametern språk och lagrar den i användarens standardinställningar:
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
   
    Ändrar den *prenumerera* metod för att inkludera språkinställningen:
   
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
   
    Observera hur vi använder metoden *registerTemplateWithDeviceToken*, i stället för *registerNativeWithDeviceToken*. När vi registrerar för en mall behöver vi json-mall och ett namn för mallen (som vår app kanske vill registrera olika mallar). Se till att registrera din kategorier som taggar, som vi vill se till att ta emot notifciations för dessa nyheter.
   
    Lägg till en metod för att hämta språkinställningen från standardinställningar för användare:
   
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
2. Nu när vi ändrade vår meddelanden klass har vi se till att vår ViewController gör användning av den nya UISegmentControl. Lägg till följande rad i den *viewDidLoad* metod för att se till att visa det språk som är markerade:
   
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
   
    I din *prenumerera* metod, ändra anrop till den *storeCategoriesAndSubscribe* till följande:
   
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
3. Slutligen kan du behöva uppdatera den *didRegisterForRemoteNotificationsWithDeviceToken* metod i din AppDelegate.m så att registreringen kan uppdateras korrekt när appen startar. Ändra anrop till den *prenumerera* metod för meddelanden med följande:
   
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(valfritt) Skicka meddelanden om lokaliserade mallar från .NET-konsolapp.
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(valfritt) Skicka meddelanden om lokaliserade mallar från enheten
Om du inte har åtkomst till Visual Studio eller bara testa skicka lokaliserade mall-meddelanden direkt från appen på enheten.  Du kan enkelt lägga till lokaliserade mallens parametrar för att den `SendNotificationRESTAPI` metod som du definierade i föregående kursen.

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder mallar finns:

* [Meddela användare med Notification Hubs: ASP.NET]
* [Meddela användare med Notification Hubs: Mobile Services]

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[använda Notification Hubs för att skicka de senaste nyheterna]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Meddela användare med Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Meddela användare med Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
