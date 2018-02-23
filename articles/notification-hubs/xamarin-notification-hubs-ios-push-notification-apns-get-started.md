---
title: "Kom igång med Azure Notification Hubs för Xamarin.iOS-appar | Microsoft Docs"
description: "I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin-iOS-app."
services: notification-hubs
keywords: "push-meddelanden för ios, push-meddelanden, push-aviseringar, push-avisering"
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: edb48cd8de9b1f7357c40cea73fa4b9a422cb8fa
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>Kom igång med Azure Notification Hubs för Xamarin.iOS-appar
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt

> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en iOS-app. Du skapar en tom Xamarin.iOS-app som tar emot push-meddelanden med hjälp av [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). 

När du är klar kan du använda meddelandehubben för att sända push-meddelanden till alla enheter som kör appen. Den färdiga koden finns tillgänglig i exemplet [NotificationHubs-app][GitHub].

I den här självstudiekursen visas ett enkelt scenario för sändning av push-meddelanden med Notification Hubs.

## <a name="prerequisites"></a>Nödvändiga komponenter
För den här kursen behöver du följande:

* Den senaste versionen av [XCode][Install Xcode]
* En enhet som är kompatibel med iOS 10 (eller senare version)
* Medlemskap i [Apple Developer Program](https://developer.apple.com/programs/).
* [Visual Studio för Mac]
  
  > [!NOTE]
  > På grund av konfigurationskrav för iOS-pushmeddelanden måste du distribuera och testa exempelappen på en fysisk iOS-enhet (iPhone eller iPad) i stället för i simulatorn.
  > 
  > 

Du måste slutföra den här självstudiekursen innan du påbörjar någon annan kurs om Notification Hubs för Xamarin.iOS-appar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Konfigurera din Notification Hub för att skicka push-meddelanden till iOS
Det här avsnittet vägleder dig genom stegen för att skapa en ny meddelandehubb och konfigurera autentisering med APNS med hjälp av push-certifikatet **.p12** som du har skapat tidigare. Om du vill använda en meddelandehubb som du redan har skapat går du vidare till steg 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Klicka på <b>Notification Services</b>-knappen och välj <b>Apple (APNS)</b>. Välj <b>Certifikat</b>, klicka på filikonen och välj den <b>.p12</b>-fil som du exporterade tidigare. Kontrollera också att du anger rätt lösenord.</p>

<p>Välj <b>Sandbox</b> eftersom det är detta läge som används för utveckling. Använd bara <b>Produktion</b> om du vill skicka push-meddelanden till användare som har köpt din app i butiken.</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![Konfigurera APNS i Azure Portal][6]

&emsp;&emsp;&emsp;&emsp;![Konfigurera APNS-certifikat i Azure Portal][7]

Din meddelandehubb har nu konfigurerats för att fungera med APNS och du har anslutningssträngar för att registrera din app och skicka push-meddelanden.

## <a name="connect-your-app-to-the-notification-hub"></a>Anslut appen till meddelandehubben
#### <a name="create-a-new-project"></a>Skapa ett nytt projekt
1. I Visual Studio skapar du ett nytt iOS-projekt och väljer mallen **Single View App** (App med enkel vy) och klickar på **Next** (Nästa)
   
     ![Välj Studio – Välj apptyp][31]

2. Ange appnamnet och organisations-ID och välj sedan **Next** (Nästa) och **Create** (Skapa)

3. I vyn Solution (Lösning) dubbelklickar du på *Into.plist* och under **Identity** (Identitet) ser du till att paket-ID:t matchar det som används när du skapar etableringsprofilen. Under **Signing** (Signering) kontrollerar du att ditt Developer-konto är markerat under **Team**, att "Automatically manage signing" (Hantera signering automatiskt) är markerat samt att signeringscertifikatet och etableringsprofilen väljs automatiskt.

    ![Visual Studio – iOS-appkonfiguration][32]

4. Lägg till Azure Messaging-paketet. I vyn Solution (Lösning) högerklickar du på projektet och väljer **Add** (Lägg till)  > **Add NuGet Packages** (Lägg till NuGet-paket). Sök efter **Xamarin.Azure.NotificationHubs.iOS** och lägg till paketet i projektet.

5. Lägg till en ny fil i klassen, ge den namnet **Constants.cs** och lägg till följande variabler och ersätt stränglitteralplatshållarna med *hubbnamnet* och *DefaultListenSharedAccessSignature* som noterats tidigare.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. Lägg till följande using-instruktion i **AppDelegate.cs**:
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. Deklarera en instans av **SBNotificationHub**:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. I **AppDelegate.cs** uppdaterar du **FinishedLaunching()** så resultatet blir som följer:
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. Åsidosätt metoden **RegisteredForRemoteNotifications()** i **AppDelegate.cs**:
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. Åsidosätt metoden **ReceivedRemoteNotification()** i **AppDelegate.cs**:
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. Skapa följande **ProcessNotification()**-metod i **AppDelegate.cs**:
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > Du kan välja att åsidosätta **FailedToRegisterForRemoteNotifications()** för att hantera vissa situationer, till exempel om det inte finns någon nätverksanslutning. Detta är särskilt viktigt om användaren kan starta appen i offline-läge (t.ex. flygplansläge) och du vill hantera scenarier för push-meddelanden som är specifika för din app.
  

12. Kör appen på enheten.

## <a name="sending-test-push-notifications"></a>Skicka test-push-meddelanden
Du kan testa att ta emot meddelanden i appen med alternativet *Skicka test* i [Azure Portal]. Ett test-push-meddelande skickas till enheten.

![Azure Portal – Skicka test][30]

Push-meddelanden skickas vanligtvis via en serverdelstjänst, till exempel Mobile Apps eller ASP.NET, med hjälp av ett kompatibelt bibliotek. Du kan också använda REST-API:et direkt för att skicka meddelanden om ett bibliotek inte är tillgängligt för din serverdel.

Som nästa steg rekommenderar vi att du går vidare med självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) som nästa steg i att skicka meddelanden från en ASP.NET-serverdel. Följande åtgärder kan dock användas för att skicka meddelanden:

Här är en lista med andra guider du kan läsa som handlar om att skicka meddelanden:
* REST-gränssnitt: Du kan använda push-meddelanden på alla serverdelsplattformar med [REST-gränssnittet](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **Microsoft Azure Notification Hubs .NET SDK**: I Nuget Package Manager för Visual Studio kör du [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* Node.js: [Använda Notification Hubs från Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* Java/PHP \**: Ett exempel på hur du skickar push-meddelanden med hjälp av REST-API:er finns i avsnittet Använda Notification Hubs från Java/PHP ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="next-steps"></a>Nästa steg
I det här enkla exemplet skickade du push-meddelanden till alla dina iOS-enheter. Mer information om hur du riktar in dig på specifika användare finns i självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare]. Om du vill dela in användarna efter intressegrupper läser du [Använda Notification Hubs för att skicka de senaste nyheterna]. Mer information om hur du använder Notification Hubs finns i [Riktlinjer för Notification Hubs] och i [Notification Hubs-instruktioner för iOS].

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Riktlinjer för Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs-instruktioner för iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio för Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Använda Notification Hubs för att skicka push-meddelanden till användare]: /manage/services/notification-hubs/notify-users-aspnet
[Använda Notification Hubs för att skicka de senaste nyheterna]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
