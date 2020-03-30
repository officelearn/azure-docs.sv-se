---
title: Skicka lokaliserade push-meddelanden till iOS med Azure Notification Hubs | Microsoft-dokument
description: Lär dig hur du använder push-lokaliserade meddelanden till iOS-enheter med hjälp av Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a8614156be5d516d16aff698b604cf0e661d7311
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72385646"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Självstudiekurs: Skicka lokaliserade push-meddelanden till iOS med Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Den här självstudien visar hur du använder [mallfunktionen](notification-hubs-templates-cross-platform-push-messages.md) i Azure Notification Hubs för att sända senaste nyhetsmeddelanden som har lokaliserats efter språk och enhet. I den här självstudien börjar du med iOS-appen som skapats i [Använd meddelandehubbar för att skicka senaste nytt]. När du är klar kan du registrera dig för kategorier som du är intresserad av, ange ett språk som du vill ta emot aviseringarna på och endast få push-meddelanden för de valda kategorierna på det språket.

Det finns två delar i det här scenariot:

* iOS-appen gör det möjligt för klientenheter att ange ett språk och prenumerera på olika senaste nyhetskategorier.
* Backend-sändningen sänder meddelandena med hjälp av **tagg-** och **mallfunktionerna** i Azure Notification Hubs.

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Uppdatera appens användargränssnitt
> * Skapa iOS-appen
> * Skicka lokaliserade mallmeddelanden från .NET-konsolappen
> * Skicka lokaliserade mallmeddelanden från enheten

## <a name="overview"></a>Översikt

I [Använd meddelandehubbar för att skicka nyheter]har du skapat en app som använde **taggar** för att prenumerera på aviseringar för olika nyhetskategorier. Många appar riktar sig dock till flera marknader och kräver lokalisering. Det innebär att innehållet i anmälningarna själva måste lokaliseras och levereras till rätt uppsättning enheter. Den här självstudien visar hur du använder **mallfunktionen** i Meddelandehubbar för att enkelt leverera lokaliserade nyhetsmeddelanden.

> [!NOTE]
> Ett sätt att skicka lokaliserade meddelanden är att skapa flera versioner av varje tagg. Till exempel, för att stödja engelska, franska och mandarin, skulle du behöva tre olika taggar för världsnyheter: "world_en", "world_fr" och "world_ch". Du skulle då behöva skicka en lokaliserad version av världen nyheter till var och en av dessa taggar. I det här avsnittet använder du mallar för att undvika spridning av taggar och kravet på att skicka flera meddelanden.

Mallar är ett sätt att ange hur en viss enhet ska få ett meddelande. Mallen anger det exakta nyttolastformatet genom att referera till egenskaper som ingår i meddelandet som skickas av appserverdelen. I ditt fall skickar du ett språk-agnostiker som innehåller alla språk som stöds:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Sedan kan du se till att enheter registrerar sig med en mall som refererar till rätt egenskap. Till exempel en iOS-app som vill registrera sig för franska nyhetsregister med hjälp av följande syntax:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Mer information om mallar finns i artikel [Mallar.](notification-hubs-templates-cross-platform-push-messages.md)

## <a name="prerequisites"></a>Krav

* Slutför [push-meddelandena till specifika iOS-enheter](notification-hubs-ios-xplat-segmented-apns-push-notification.md) handledning och ha koden tillgänglig, eftersom den här självstudien bygger direkt på den koden.
* Visual Studio 2019 är valfritt.

## <a name="update-the-app-user-interface"></a>Uppdatera appens användargränssnitt

I det här avsnittet ändrar du appen Breaking News som du skapade i avsnittet [Använd meddelandehubbar för att skicka senaste nytt] för att skicka lokaliserade nyheter med hjälp av mallar.

Lägg `MainStoryboard_iPhone.storyboard`till en segmenterad kontroll med de tre språken: engelska, franska och mandarin i .

![Skapa iOS UI-storyboard][13]

Se sedan till att lägga till en IBOutlet i ViewController.h som visas i följande bild:

![Skapa uttag för switcharna][14]

## <a name="build-the-ios-app"></a>Skapa iOS-appen

1. `Notification.h`Lägg till `retrieveLocale` metoden och ändra metoderna för butik och prenumerera enligt följande kod:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    Ändra `Notification.m` `storeCategoriesAndSubscribe` metoden genom att lägga `locale` till parametern och lagra den i användarens standardvärden:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Ändra sedan *prenumerationsmetoden* så att den inkluderar språk:

    ```objc
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

    Du använder `registerTemplateWithDeviceToken`metoden i `registerNativeWithDeviceToken`stället för . När du registrerar dig för en mall måste du ange json-mallen och även ett namn på mallen (eftersom appen kanske vill registrera olika mallar). Se till att registrera dina kategorier som taggar, eftersom du vill vara säker på att få meddelanden för dessa nyheter.

    Lägg till en metod för att hämta språkinställningen från användarens standardinställningar:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Nu när du `Notifications` har ändrat klassen måste `ViewController` du se till `UISegmentControl`att den använder den nya . Lägg till följande `viewDidLoad` rad i metoden för att se till att visa det språk som för närvarande är markerat:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Ändra sedan `subscribe` samtalet till `storeCategoriesAndSubscribe` följande kod i din metod:

    ```objc
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

3. Slutligen måste du uppdatera `didRegisterForRemoteNotificationsWithDeviceToken` metoden i din AppDelegate.m, så att du kan uppdatera din registrering korrekt när appen startar. Ändra samtalet till `subscribe` metoden för meddelanden med följande kod:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(valfritt) Skicka lokaliserade mallmeddelanden från .NET-konsolappen

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(valfritt) Skicka lokaliserade mallmeddelanden från enheten

Om du inte har tillgång till Visual Studio eller bara vill testa att skicka de lokaliserade mallmeddelandena direkt från appen på enheten. Du kan lägga till de `SendNotificationRESTAPI` lokaliserade mallparametrarna i den metod som du definierade i föregående självstudie.

```objc
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

I den här självstudien skickade du lokaliserade meddelanden till iOS-enheter. Om du vill veta hur du skickar meddelanden till specifika användare av iOS-appar går du vidare till följande självstudiekurs:

> [!div class="nextstepaction"]
>[Skicka meddelanden till specifika användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Använda Notification Hubs för att skicka de senaste nyheterna]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
