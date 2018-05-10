---
title: Push-lokaliserade meddelanden till iOS-enheter med Azure Notification Hubs | Microsoft Docs
description: Lär dig använda lokaliserade push-meddelanden till iOS-enheter med hjälp av Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: b3d74086ee233da50138aff00d8da78aa0243a75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Självstudier: Lokaliserade Push-meddelanden till iOS-enheter med Azure Notification Hubs 
> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 

Den här kursen visar hur du använder den [mallar](notification-hubs-templates-cross-platform-push-messages.md) funktion i Azure Notification Hubs för att sända senaste nyheterna meddelanden som har översatts av språk och enheten. I den här kursen börjar du med iOS-app som skapats i [använda Notification Hubs för att skicka de senaste nyheterna]. När du är färdig du registrera dig för kategorier som du är intresserad, ange ett språk som ska ta emot meddelanden och får endast push-meddelanden i valda kategorier på det språket.

Det finns två delar i det här scenariot:

* iOS-app kan klienten enheter för att ange ett språk och prenumerera på olika senaste nyheterna kategorier.
* Backend-skickar meddelanden med hjälp av den **taggen** och **mallen** funktioner i Azure Notification Hubs.

I den här kursen kan du utföra följande steg:

> [!div class="checklist"]
> * Uppdatera användargränssnittet app
> * Skapa iOS-app
> * Skicka meddelanden om lokaliserade mallar från .NET-konsolapp
> * Skicka meddelanden om lokaliserade mallar från enheten


## <a name="overview"></a>Översikt
I [använda Notification Hubs för att skicka de senaste nyheterna], du har skapat en app som används för **taggar** att prenumerera på meddelanden om nyheterna i olika kategorier. Många appar dock mål flera marknader och kräver lokalisering. Det innebär att innehållet av meddelanden själva måste lokaliserade och levereras till rätt uppsättning enheter. Den här kursen visar hur du använder den **mallen** funktion i Notification Hubs för att leverera enkelt lokaliserade senaste nyheterna meddelanden.

> [!NOTE]
> Ett sätt att skicka lokaliserade meddelanden är att skapa flera versioner av varje tagg. Till exempel för att stödja engelska, franska och Mandarin, behöver du tre olika taggar för world news: ”world_en”, ”world_fr” och ”world_ch”. Du måste sedan skicka en lokaliserad version av world nyheter till var och en av dessa taggar. I det här avsnittet använder du mallar för att undvika alltför många taggar och kravet flera meddelanden.

Mallar är ett sätt att ange hur en specifik enhet bör få ett meddelande på en hög nivå. Mallen anger exakt nyttolastformatet genom att referera till egenskaper som ingår i meddelandet som skickas av din appens serverdel. I ditt fall kan du skicka ett språkvariant-oberoende meddelande som innehåller alla språk som stöds:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Du kontrollerar du att registrera enheter med en mall som refererar till egenskapen korrekt. Till exempel registrerar en iOS-app som du vill registrera för franska nyheter med följande syntax:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Mer information om mallar finns [mallar](notification-hubs-templates-cross-platform-push-messages.md) artikel.

## <a name="prerequisites"></a>Förutsättningar

- Slutför den [Push-meddelanden till specifika iOS-enheter](notification-hubs-ios-xplat-segmented-apns-push-notification.md) självstudier och har kod som är tillgängliga, eftersom den här kursen bygger direkt på koden.
- Visual Studio 2012 eller senare är valfritt.

## <a name="update-the-app-user-interface"></a>Uppdatera användargränssnittet app
I det här avsnittet kan du ändra bryta nyheter appen som du skapade i avsnittet [använda Notification Hubs för att skicka de senaste nyheterna] att skicka lokaliserade senaste nytt med hjälp av mallar.

Din MainStoryboard_iPhone.storyboard lägga till en segmenterade kontroll med tre språk: engelska, franska och Mandarin.

![][13]

Kontrollera sedan att lägga till en IBOutlet i din ViewController.h som visas i följande bild:

![][14]

## <a name="build-the-ios-app"></a>Skapa iOS-app
1. Lägg till i din Notification.h den *retrieveLocale* -metoden och ändra arkivet och prenumerera metoder som visas i följande kod:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    ```
    Ändra i din Notification.m den *storeCategoriesAndSubscribe* metoden genom att lägga till parametern språk och lagrar den i användarens standardinställningar:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
    ````
    Ändrar den *prenumerera* metod för att inkludera språkinställningen:
   
    ```obj-c
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
       ```
    You use the method *registerTemplateWithDeviceToken*, instead of *registerNativeWithDeviceToken*. When you register for a template, you have to provide the json template and also a name for the template (as the app might want to register different templates). Make sure to register your categories as tags, as you want to make sure to receive the notifciations for those news.
   
    Add a method to retrieve the locale from the user default settings:
   
    ```obj-c
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
    ```
2. Nu när du har ändrat klassen meddelanden måste du se till att ViewController gör användning av den nya UISegmentControl. Lägg till följande rad i den *viewDidLoad* metod för att se till att visa det språk som är markerade:
   
    ```obj-c
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
     ```  
    I din *prenumerera* metod, ändra anrop till den *storeCategoriesAndSubscribe* till följande kod:
   
    ```obj-c
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
    ```
3. Slutligen kan du behöva uppdatera den *didRegisterForRemoteNotificationsWithDeviceToken* metod i din AppDelegate.m så att registreringen kan uppdateras korrekt när appen startar. Ändra anrop till den *prenumerera* metod för meddelanden med följande kod:
   
    ```obj-c
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(valfritt) Skicka meddelanden om lokaliserade mallar från .NET-konsolapp
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(valfritt) Skicka meddelanden om lokaliserade mallar från enheten
Om du inte har åtkomst till Visual Studio eller bara testa skicka lokaliserade mall-meddelanden direkt från appen på enheten. Du kan lägga till lokaliserade mallens parametrar för att den `SendNotificationRESTAPI` metod som du definierade i föregående kursen.

    ```obj-c
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
    ```


## <a name="next-steps"></a>Nästa steg
I kursen får skickat du lokaliserade meddelanden till iOS-enheter. Information om hur du push-meddelanden till specifika användare för iOS-appar, fortsätter du till följande kursen: 

> [!div class="nextstepaction"]
>[Push-meddelanden till specifika användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[använda Notification Hubs för att skicka de senaste nyheterna]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
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
