---
title: Skicka meddelanden till iOS med hjälp av Azure Notification Hubs | Microsoft Docs
description: I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en iOS-app.
services: notification-hubs
documentationcenter: ios
keywords: push-meddelande, push-meddelanden, push-meddelanden i ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 520d01327b5809d453bb777165899770ea4c130b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884681"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Självstudier: Skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

I den här självstudiekursen kommer du att använda Azure Notification Hubs för att skicka meddelanden till ett iOS-program. Du skapar en tom iOS-app som tar emot push-meddelanden med hjälp av [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Generera filen för begäran om certifikatsignering
> * Registrera din app för push-meddelanden
> * Skapa en etableringsprofil för appen
> * Konfigurera din meddelandehubb för att skicka push-meddelanden till iOS
> * Anslut iOS-appen till meddelandehubbar
> * Skicka test-push-meddelanden
> * Verifiera att din app tar emot push-meddelanden

Den slutförda koden för den här självstudiekursen hittar du [på GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett aktivt Azure-konto. Om du inte har något konto kan du [skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free) på bara några minuter.
* [Windows Azure Messaging Framework]
* Den senaste versionen av [Xcode]
* En enhet som är kompatibel med iOS 10 (eller senare versioner)
* Medlemskap i [Apple Developer Program](https://developer.apple.com/programs/).
  
  > [!NOTE]
  > På grund av konfigurationskrav för push-meddelanden måste du distribuera och testa push-meddelanden på en fysisk iOS-enhet (iPhone eller iPad) i stället för på iOS-simulatorn.
  
Du måste slutföra den här självstudiekursen innan du börjar någon annan kurs om Notification Hubs för iOS-appar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Konfigurera din Notification Hub för att skicka push-meddelanden till iOS

I det här avsnittet skapar du en ny meddelandehubb och konfigurerar autentisering med APNS med hjälp av push-certifikatet **.p12** som du har skapat tidigare. Om du vill använda en meddelandehubb som du redan har skapat går du vidare till steg 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-your-notification-hub-with-apns-information"></a>Skapa din meddelandehubb med APNS-information

1. Välj **Apple (APNS)** under **Notification Services**.
2. Välj **Certifikat**.
3. Välj **filikonen**.
4. Välj **.p12**-filen som du exporterade tidigare.
5. Ange rätt **lösenord**.
6. Välj **Sandbox**-läge. Använd bara **Produktion** om du vill skicka push-meddelanden till användare som har köpt din app i butiken.

    ![Konfigurera APNS-certifikat i Azure Portal][7]

Du har nu konfigurerat meddelandehubben ed APNS och du har anslutningssträngar för att registrera din app och skicka push-meddelanden.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Anslut iOS-appen till Notification Hubs

1. Skapa ett nytt iOS-projekt i Xcode och välj mallen **Program enkel vy**.

    ![Xcode – Program enkel vy][8]

2. När du anger alternativ för ditt nya projekt, ska du använda samma **produktnamn** och **organisations-ID** som du använde när du gjorde inställningarna för ID:t för programpaket på Apple Developer-portalen.

    ![Xcode – projektalternativ][11]

3. Under projektnavigeringen klickar du på projektnamnet, klickar på fliken **Allmänt** och letar reda på **Signering**. Se till att välja rätt team för Apple Developer-kontot. XCode bör automatiskt hämta den etableringsprofil du har skapat tidigare baserat på paket-ID.

    Om du inte ser den nya etableringsprofil som du skapade i Xcode, kan du försöka uppdatera profilerna för din signeringsidentitet. Klicka på **Xcode** på menyraden, sedan på **Inställningar** och på fliken **Konto**. Därefter klickar du på knappen **Visa detaljer** och sedan på din signeringsidentitet. Slutligen klickar du på uppdateringsknappen i det nedre högra hörnet.

    ![Xcode – etableringsprofil][9]

4. Välj fliken **Funktioner** och se till att aktivera Push-meddelanden

    ![Xcode – push-funktioner][12]

5. Lägg till Azure Notification Hubs SDK-moduler.

   Du kan integrera Azure Notification Hubs SDK i din app med hjälp av [Cocoapods](https://cocoapods.org) eller genom att manuellt lägga till de binära filerna i projektet.

   - Integrering via Cocoapods

     Lägg till följande beroenden till din `podfile` att inkludera Azure Notification Hubs SDK i din app.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Kör `pod install` du installerar din nya pod och öppnar din `.xcworkspace`.

     > [!NOTE]
     > Om du ser ett fel som ```[!] Unable to find a specification for `AzureNotificationHubs-iOS` ``` när du kör `pod install`, kör `pod repo update` att hämta senaste poddarna från Cocoapods databasen och kör sedan `pod install`.

   - Integrering via Carthage

     Lägg till följande beroenden till din `Cartfile` att inkludera Azure Notification Hubs SDK i din app.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Sedan måste uppdatera och skapa beroenden:

     ```shell
     $ carthage update
     ```

     Mer information om hur du använder Carthage finns i den [Carthage GitHub-lagringsplatsen](https://github.com/Carthage/Carthage).

   - Integration genom att kopiera de binära filerna i projektet

     1. Ladda ned den [Azure Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs-ios/releases) framework tillhandahålls som en zip-fil och packa upp den.

     2. Högerklicka på ditt projekt i Xcode och klicka sedan på alternativet **Lägg till filer i** för att lägga till mappen **WindowsAzureMessaging.framework** till ditt Xcode-projekt. Välj **Alternativ** och se till att **Kopiera objekt vid behov** är markerat och klicka sedan på **Lägg till**.

        ![Packa upp Azure SDK][10]

6. Lägg till en ny rubrikfil i projektet med namnet `HubInfo.h`. Den här filen innehåller konstanterna för din meddelandehubb. Lägg till följande definitioner och ersätt platshållarnas textsträngar med ditt *hubbnamn* och den *DefaultListenSharedAccessSignature* som du skrivit ned tidigare.

    ```objc
    #ifndef HubInfo_h
    #define HubInfo_h

        #define HUBNAME @"<Enter the name of your hub>"
        #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"

    #endif /* HubInfo_h */
    ```

7. Öppna filen `AppDelegate.h` och lägg till följande importdirektiv:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    #import "HubInfo.h"
    ```
8. I din `AppDelegate.m`-fil lägger du till följande kod i den `didFinishLaunchingWithOptions`-metod som är baserad på din iOS-version. Den här koden registrerar din enhetshantering med APNS:

    ```objc
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
        UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```

9. Lägg till följande metoder i samma fil:

    ```objc
        - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                    notificationHubPath:HUBNAME];

        [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
            else {
                [self MessageBox:@"Registration Status" message:@"Registered"];
            }
        }];
        }

    -(void)MessageBox:(NSString *) title message:(NSString *)messageText
    {
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:messageText preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil];
        [alert addAction:okAction];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }
    ```

    Den här koden ansluter till meddelandehubben genom att använda den uppkopplingsinformation som du angav i HubInfo.h. Den lämnar sedan över enhetstoken till meddelandehubben för att den ska kunna skicka meddelanden.

10. Lägg till följande metod i samma fil för att visa en **UIAlert** om meddelandet tas emot medan appen är aktiv:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

11. Skapa och kör appen på din enhet för att kontrollera att det inte finns några fel.

## <a name="send-test-push-notifications"></a>Skicka test-push-meddelanden

Du kan testa att ta emot meddelanden i appen med alternativet *Skicka test* i [Azure Portal]. Den skickar ett test-push-meddelande till enheten.

![Azure Portal – Skicka test][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Verifiera att din app tar emot push-meddelanden

Om du vill testa push-meddelanden på iOS måste du distribuera appen till en fysisk iOS-enhet. Du kan inte skicka push-meddelanden för Apple genom att använda iOS-simulatorn.

1. Kör appen och verifiera att registreringen kan genomföras. Tryck sedan på **OK**.

    ![Registreringstest för push-meddelanden i iOS-appar][33]

2. Sedan skickar du ett test-push-meddelande från [Azure Portal], enligt beskrivningen ovan.

3. Push-meddelandena skickas till alla enheter som registrerats för att ta emot meddelanden från en viss Notification Hub.

    ![Test av att ta emot push-meddelanden i iOS-appar][35]

## <a name="next-steps"></a>Nästa steg

I det här enkla exemplet skickade du push-meddelanden till alla dina registrerade iOS-enheter. Information om hur du skickar meddelanden till specifika iOS-enheter finns i följande självstudiekurs:

> [!div class="nextstepaction"]
>[Skicka meddelanden till specifika enheter](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Windows Azure Messaging Framework]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com
