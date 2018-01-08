---
title: "Kom igång med Azure Notification Hubs för iOS-appar | Microsoft Docs"
description: "I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en iOS-app."
services: notification-hubs
documentationcenter: ios
keywords: push-meddelande, push-meddelanden, push-meddelanden i ios
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 0e9e7ab196eef790b74074be319cd8122cf3ff5c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-ios-apps"></a>Kom igång med Azure Notification Hubs för iOS-appar
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt

> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).
> 
> 

I den här självstudiekursen kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en iOS-app. Du skapar en tom iOS-app som tar emot push-meddelanden med hjälp av [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

När du är klar kan du använda meddelandehubben för att sända push-meddelanden till alla enheter som kör appen.

## <a name="before-you-begin"></a>Innan du börjar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Den slutförda koden för den här självstudiekursen hittar du [på GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>Nödvändiga komponenter
För den här kursen behöver du följande:

* [Windows Azure Messaging Framework]
* Den senaste versionen av [Xcode]
* En enhet som är kompatibel med iOS 10 (eller senare versioner)
* Medlemskap i [Apple Developer Program](https://developer.apple.com/programs/).
  
  > [!NOTE]
  > På grund av konfigurationskrav för push-meddelanden måste du distribuera och testa push-meddelanden på en fysisk iOS-enhet (iPhone eller iPad) i stället för på iOS-simulatorn.
  > 
  > 

Du måste slutföra den här självstudiekursen innan du börjar någon annan kurs om Notification Hubs för iOS-appar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Konfigurera din Notification Hub för att skicka push-meddelanden till iOS
Det här avsnittet vägleder dig genom stegen för att skapa en ny meddelandehubb och konfigurera autentisering med APNS med hjälp av push-certifikatet **.p12** som du har skapat tidigare. Om du vill använda en meddelandehubb som du redan har skapat går du vidare till steg 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Under <b>Notification Services</b> väljer du <b>Apple (APNS)</b>. Välj <b>Certifikat</b>, klicka på filikonen och välj den <b>.p12</b>-fil som du exporterade tidigare. Kontrollera också att du anger rätt lösenord.</p>

<p>Välj <b>Sandbox</b> eftersom det är detta läge som används för utveckling. Använd bara <b>Produktion</b> om du vill skicka push-meddelanden till användare som har köpt din app i butiken.</p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![Konfigurera APNS i Azure Portal][6]

&emsp;&emsp;&emsp;&emsp;![Konfigurera APNS-certifikat i Azure Portal][7]

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
   
5. Ladda ned [Windows Azure Messaging Framework] och packa upp filen. Högerklicka på ditt projekt i Xcode och klicka sedan på alternativet **Lägg till filer i** för att lägga till mappen **WindowsAzureMessaging.framework** till ditt Xcode-projekt. Välj **Alternativ** och se till att **Kopiera objekt vid behov** är markerat och klicka sedan på **Lägg till**.

    ![Packa upp Azure SDK][10]

6. Lägg till en ny rubrikfil i projektet med namnet **HubInfo.h**. Den här filen ska innehålla konstanterna för din meddelandehubb. Lägg till följande definitioner och ersätt platshållarnas textsträngar med ditt *hubbnamn* och den *DefaultListenSharedAccessSignature* som du skrivit ned tidigare.

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. Öppna filen **AppDelegate.h** och lägg till följande importdirektiv:

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. I filen **AppDelegate.m** lägger du till följande kod i metoden **didFinishLaunchingWithOptions** baserat på din version av iOS. Den här koden registrerar din enhetshantering med APNS:

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. Lägg till följande metoder i samma fil:

    ```obj-c
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
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    Den här koden ansluter till meddelandehubben genom att använda den uppkopplingsinformation som du angav i HubInfo.h. Den lämnar sedan över enhetstoken till meddelandehubben för att den ska kunna skicka meddelanden.

10. Lägg till följande metod i samma fil för att visa en **UIAlert** om meddelandet tas emot medan appen är aktiv:

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. Skapa och kör appen på din enhet för att kontrollera att det inte finns några fel.

## <a name="send-test-push-notifications"></a>Skicka test-push-meddelanden
Du kan testa att ta emot meddelanden i appen med alternativet *Skicka test* i [Azure Portal]. Ett test-push-meddelande skickas till enheten.

![Azure Portal – Skicka test][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="checking-if-your-app-can-receive-push-notifications"></a>Kontrollera om din app kan ta emot push-meddelanden
Om du vill testa push-meddelanden på iOS måste du distribuera appen till en fysisk iOS-enhet. Du kan inte skicka push-meddelanden för Apple genom att använda iOS-simulatorn.

1. Kör appen och verifiera att registreringen kan genomföras. Tryck sedan på **OK**.
   
    ![Registreringstest för push-meddelanden i iOS-appar][33]
2. Sedan skickar du ett test-push-meddelande från [Azure Portal], enligt beskrivningen ovan. 

3. Push-meddelandena skickas till alla enheter som registrerats för att ta emot meddelanden från en viss Notification Hub.
   
    ![Test av att ta emot push-meddelanden i iOS-appar][35]

## <a name="next-steps"></a>Nästa steg
I det här enkla exemplet skickade du push-meddelanden till alla dina registrerade iOS-enheter. Ett rekommenderat nästa steg i utbildningen är att börja med kursen [Meddela användare för iOS med .NET-serverdel i Azure Notification Hubs]. I den här guiden får du hjälp med att göra så att en serverdel skickar push-meddelanden med taggar. 

Om du vill dela in användarna efter intressegrupper, kan du även gå vidare till självstudiekursen [Använda Notification Hubs för att skicka de senaste nyheterna]. 

Allmän information om Notification Hubs finns i [Riktlinjer om Notification Hubs].

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
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Riktlinjer om Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Meddela användare för iOS med .NET-serverdel i Azure Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Använda Notification Hubs för att skicka de senaste nyheterna]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com
