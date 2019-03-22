---
title: Skicka lokaliserade meddelanden till iOS-enheter med Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du använder lokaliserade pushmeddelanden till iOS-enheter med hjälp av Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 527e9979b624970dd55b4300fe63c27386640ac4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886617"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Självstudier: Skicka lokaliserade meddelanden till iOS-enheter med Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Den här självstudien visar hur du använder den [mallar](notification-hubs-templates-cross-platform-push-messages.md) funktion i Azure Notification Hubs för att sända senaste nytt som har översatts av språk och enheten. I den här självstudien börjar du med iOS-app som skapats i [använda Notification Hubs för att skicka de senaste nyheterna]. När du är klar kan du registrera för kategorier som du är intresserad, anger du ett språk som ska ta emot meddelanden och får endast push-meddelanden för de valda kategorierna i det här språket.

Det finns två delar i det här scenariot:

* iOS-app kan klienten enheter att ange ett språk, samt att prenumerera på olika inkomna nyhetskategorier.
* Backend-skickar meddelanden med hjälp av den **taggen** och **mall** funktioner i Azure Notification Hubs.

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Uppdatera app-användargränssnittet
> * Skapa iOS-app
> * Skicka lokaliserade mallvarningar från .NET-konsolapp
> * Skicka lokaliserade mall meddelanden från enheten

## <a name="overview"></a>Översikt

I [använda Notification Hubs för att skicka de senaste nyheterna], du har skapat en app som används för **taggar** att prenumerera på meddelanden för olika nyhetskategorier. Många appar kan dock rikta in flera marknader och kräver lokalisering. Det innebär att innehållet av meddelanden själva måste lokaliserade och levereras till rätt uppsättning enheter. Den här självstudien visar hur du använder den **mall** funktion i Notification Hubs för att leverera enkelt aviseringar om lokaliserade inkomna nyheter.

> [!NOTE]
> Ett sätt att skicka lokaliserade meddelanden är att skapa flera versioner av varje tagg. Till exempel för att stödja engelska, franska och Mandarin, behöver du tre olika taggar för världsnyheter: ”world_en”, ”world_fr” och ”world_ch”. Du sedan måste skicka en översatt version av världsnyheter till var och en av dessa taggar. I det här avsnittet använder du mallar för att undvika ökningen av taggar och krav på flera meddelanden.

Mallar är ett sätt att ange hur en specifik enhet bör få ett meddelande på en hög nivå. Mallen anger det exakta nyttolastformatet genom att referera till egenskaper som ingår i meddelandet som skickas av appserverdelen. I ditt fall skickar du ett språk-oberoende meddelande som innehåller alla språk som stöds:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Sedan du se till att enheter ska registreras med en mall som refererar till rätt egenskap. Till exempel registrerar en iOS-app som du vill registrera dig för franska nyheter med följande syntax:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Mer information om mallar finns i [mallar](notification-hubs-templates-cross-platform-push-messages.md) artikeln.

## <a name="prerequisites"></a>Förutsättningar

* Slutför den [Push-meddelanden till specifika iOS-enheter](notification-hubs-ios-xplat-segmented-apns-push-notification.md) självstudiekursen och har du koden som är tillgängliga, eftersom den här självstudien bygger direkt på den koden.
* Visual Studio 2017 är valfritt.

## <a name="update-the-app-user-interface"></a>Uppdatera app-användargränssnittet

I det här avsnittet ska du ändra större News-appen som du skapade i avsnittet [använda Notification Hubs för att skicka de senaste nyheterna] att skicka lokaliserade senaste nytt med hjälp av mallar.

I din `MainStoryboard_iPhone.storyboard`, lägga till en segmenterade kontroll med tre språk: Engelska, franska och Mandarin.

![Skapa UI-storyboard iOS][13]

Kontrollera sedan att lägga till en IBOutlet i din ViewController.h enligt följande bild:

![Skapa kanaler för växlarna][14]

## <a name="build-the-ios-app"></a>Skapa iOS-app

1. I din `Notification.h` lägga till den `retrieveLocale` metoden och ändra arkivet och prenumerera på metoder som visas i följande kod:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    I din `Notification.m`, ändra den `storeCategoriesAndSubscribe` metoden genom att lägga till den `locale` parametern och lagrar den i användarens standardinställningar:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Ändra den *prenumerera* metoden och ta de nationella inställningarna:

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

    Du använder metoden `registerTemplateWithDeviceToken`, i stället för `registerNativeWithDeviceToken`. Du måste ange json-mallen och ett namn för mallen (som appen kanske vill registrera olika mallar) när du registrerar dig för en mall. Se till att registrera din kategorier som taggar, som du vill se till att ta emot meddelanden om dessa nyheter.

    Lägg till en metod för att hämta de nationella inställningarna från standard användarinställningar:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Nu när du ändrat den `Notifications` klass, som du behöver se till att den `ViewController` utnyttjar den nya `UISegmentControl`. Lägg till följande rad i den `viewDidLoad` metod för att se till att visa det språk som för närvarande är markerad:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    I din `subscribe` metod, ändra anrop till den `storeCategoriesAndSubscribe` till följande kod:

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

3. Slutligen kan du behöva uppdatera den `didRegisterForRemoteNotificationsWithDeviceToken` -metod i din AppDelegate.m så att du korrekt kan uppdatera din registrering när appen startas. Ändra anrop till den `subscribe` metoden för meddelanden med följande kod:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(valfritt) Skicka lokaliserade mallvarningar från .NET-konsolapp

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(valfritt) Skicka lokaliserade mall meddelanden från enheten

Om du inte har åtkomst till Visual Studio eller bara vill du testa att skicka lokaliserade mall-meddelanden direkt från appen på enheten. Du kan lägga till lokaliserade mallparametrarna till den `SendNotificationRESTAPI` metod som du definierade i föregående självstudie.

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

I den här självstudien får skickat du lokaliserade meddelanden till iOS-enheter. Gå vidare till nästa självstudie om du vill veta hur du push-meddelanden till specifika användare av iOS-appar:

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
