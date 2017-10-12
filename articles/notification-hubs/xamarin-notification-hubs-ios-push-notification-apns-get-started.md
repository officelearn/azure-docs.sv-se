---
title: "iOS-pushmeddelanden med Notification Hubs för Xamarin-appar | Microsoft Docs"
description: "I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin-iOS-app."
services: notification-hubs
keywords: "push-meddelanden för ios, push-meddelanden, push-aviseringar, push-avisering"
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 72a81fa0deb34ace77b8fb9b1a4e6b24ee164b35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>iOS-pushmeddelanden med Notification Hubs för Xamarin-appar
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt
> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en iOS-app.
Du skapar en tom Xamarin.iOS-app som tar emot push-meddelanden med hjälp av [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). När du är klar kan du använda meddelandehubben för att sända push-meddelanden till alla enheter som kör appen. Den färdiga koden finns tillgänglig i exemplet [NotificationHubs-app][GitHub].

I den här självstudiekursen visas ett enkelt scenario för sändning av push-meddelanden med Notification Hubs.

## <a name="prerequisites"></a>Krav
För den här kursen behöver du följande:

* [Xcode 6.0][Install Xcode]
* En enhet som är kompatibel med iOS 7.0 (eller senare version)
* Medlemskap i ett utvecklarprogram för iOS
* [Xamarin Studio]
  
  > [!NOTE]
  > På grund av konfigurationskrav för iOS-pushmeddelanden måste du distribuera och testa exempelappen på en fysisk iOS-enhet (iPhone eller iPad) i stället för i simulatorn.
  > 
  > 

Du måste slutföra den här självstudiekursen innan du påbörjar någon annan kurs om Notification Hubs för Xamarin-iOS-appar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub"></a>Konfigurera meddelandehubben
I det här avsnittet går vi igenom hur du skapar en ny meddelandehubb och konfigurerar autentisering med APNS med **.p12**-pushcertifikatet som du skapade. Om du vill använda en meddelandehubb som du redan har skapat går du vidare till steg 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">

<li>

<p>Eftersom du vill konfigurera APN-anslutningen i Azure Portal öppnar du inställningarna för Notification Hubs och klickar på <b>Notification Services</b> och sedan på objektet <b>Apple (APNS)</b> i listan. När du har gjort det klickar du på <b>Ladda upp certifikat</b> och väljer <b>.p12</b>-certifikatet som du exporterade tidigare, samt lösenordet för certifikatet.</p>

<p>Var noga med att välja <b>Begränsat läge</b> eftersom du kommer att skicka push-meddelanden i en utvecklingsmiljö. Använd bara inställningen <b>Produktion</b> om du vill skicka push-meddelanden till användare som redan har köpt din app i butiken.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)

Din meddelandehubb har nu konfigurerats för att fungera med APNS och du har anslutningssträngar för att registrera din app och skicka push-meddelanden.

## <a name="connect-your-app-to-the-notification-hub"></a>Anslut appen till meddelandehubben
#### <a name="create-a-new-project"></a>Skapa ett nytt projekt
1. Skapa ett nytt iOS-projekt i Xamarin Studio och välj mallen **Enhetligt API** > **App enkel vy**.
   
     ![Xamarin Studio – Välj apptyp][31]
2. Lägg till en referens i Azure Messaging-komponenten. I vyn Lösning högerklickar du på mappen **Komponenter** för ditt projekt och väljer **Få fler komponenter**. Sök efter **Azure Messaging**-komponenten och lägg till den i projektet.
3. Lägg till följande using-instruktion i **AppDelegate.cs**:
   
        using WindowsAzure.Messaging;
4. Deklarera en instans av **SBNotificationHub**:
   
        private SBNotificationHub Hub { get; set; }
5. Skapa en **Constants.cs**-klass med följande variabler:
   
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
6. I **AppDelegate.cs** uppdaterar du **FinishedLaunching()** så resultatet blir som följer:
   
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
7. Åsidosätt metoden **RegisteredForRemoteNotifications()** i **AppDelegate.cs**:
   
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
8. Åsidosätt metoden **ReceivedRemoteNotification()** i **AppDelegate.cs**:
   
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
9. Skapa följande **ProcessNotification()**-metod i **AppDelegate.cs**:
   
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
   
   > [!NOTE]
   > Du kan välja att åsidosätta **FailedToRegisterForRemoteNotifications()** för att hantera vissa situationer, till exempel om det inte finns någon nätverksanslutning. Detta är särskilt viktigt om användaren kan starta appen i offline-läge (t.ex. flygplansläge) och du vill hantera scenarier för push-meddelanden som är specifika för din app.
   > 
   > 
10. Kör appen på enheten.

## <a name="sending-push-notifications"></a>Skicka push-meddelanden
Du kan testa att ta emot push-meddelanden i appen genom att skicka meddelanden i [Azure-portalen] via funktionen **Prova att skicka** i verktygsuppsättningen **Felsökning** till höger på sidan för meddelandehubben, så som visas på skärmen nedan.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Push-meddelanden skickas vanligtvis via en serverdelstjänst som Mobile Services eller ASP.NET med hjälp av ett kompatibelt bibliotek. Du kan också använda REST-API:er direkt för att skicka push-meddelanden om ett bibliotek inte är tillgängligt i ditt scenario. 

I den här enkla självstudiekursen visas hur du testar klientappen genom att skicka meddelanden med .NET SDK för meddelandehubbar i en konsolapp i stället för med en serverdelstjänst. Vi rekommenderar att du går vidare med självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) som nästa steg i att skicka meddelanden från en ASP.NET-serverdel. Följande åtgärder kan dock användas för att skicka meddelanden:

* **REST-gränssnitt**: Du kan använda push-meddelanden på alla serverdelsplattformar med [REST-gränssnittet](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **Microsoft Azure Notification Hubs .NET SDK**: I pakethanteraren Nuget för Visual Studio kör du [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js**: [Använda Notification Hubs från Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

**Mobile Apps**: För ett exempel på hur man skickar meddelanden från en Azure App Service Mobile Apps-serverdel som är integrerad med Notification Hubs, kan du gå till [Lägg till push-meddelanden i din mobilapp](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

* **Java/PHP**: Ett exempel på hur du skickar push-meddelanden med hjälp av REST-API:er finns i avsnittet Använda Notification Hubs från Java/PHP ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

#### <a name="optional-send-push-notifications-from-a-net-console-app"></a>(Valfritt) Skicka push-meddelanden från en .NET-konsolapp
I det här avsnittet skickar du meddelanden med en .NET-konsolapp. I det här exemplet växlar vi till en Windows-utvecklingsmiljö där Visual Studio redan har installerats.

1. Skapa en ny Visual C#-konsolapp i Visual Studio:
   
       ![Visual Studio - Create a new console application][213]
2. I Visual Studio klickar du på **Verktyg**, **NuGet Package Manager** och sedan på **Package Manager-konsolen**.
   
    Pakethanterarkonsolen bör visas dockad längst ned i Visual Studio-arbetsytan.
3. I fönstret för pakethanterarkonsolen ställer du in **standardprojektet** till det nya projektet för konsolappen. Sedan kör du följande kommando i konsolfönstret:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Då läggs en referens till i Azure Notification Hubs SDK med hjälp av <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs-NuGet-paketet</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
4. Öppna `Program.cs`-filen och lägg till följande `using`-instruktion, vilket gör så att du kan använda Azure-klasser och -funktioner i huvudklassen:
   
        using Microsoft.Azure.NotificationHubs;
5. Lägg till följande metod i `Program`-klassen (glöm inte att ersätta **anslutningssträngen** och **hubbnamnet**):
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }
6. Lägg till följande rader i `Main`-metoden:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Kör appen genom att trycka på F5. Inom några sekunder bör du se ett push-meddelande på enheten. Oavsett om du använder Wi-Fi eller ett mobilt nätverk måste du ha en aktiv internetanslutning på enheten.

Du hittar alla möjliga nyttolaster i Apples [programmeringsguide för lokala meddelanden och push-meddelanden].

#### <a name="optional-send-notifications-from-a-mobile-service"></a>(Valfritt) Skicka meddelanden med en mobiltjänst
I det här avsnittet skickar du push-meddelanden med en mobiltjänst via ett nodskript.

Följ [Komma igång med Mobile Services] för att skicka ett meddelande med en mobiltjänst. Gör sedan följande:

1. Logga in på den [Klassisk Azure-portal] och välj mobiltjänst.
2. Välj fliken **Schemaläggaren** högst upp.
   
       ![Azure Classic Portal - Scheduler][215]
3. Skapa ett nytt schemalagt jobb, infoga ett namn och välj **På begäran**.
   
       ![Azure Classic Portal - Create new job][216]
4. Klicka på jobbnamnet när jobbet skapats. Klicka på fliken **Skript** i det översta fältet.
5. Infoga följande skript i schemaläggarfunktionen. Ersätt platshållarna med namnet på din meddelandehubb och anslutningssträngen för *DefaultFullSharedAccessSignature* som du fick tidigare. Klicka på **Spara**.
   
        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                  "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );
6. Klicka på **Kör en gång** i det nedre fältet. Du bör få en avisering på enheten.

## <a name="next-steps"></a>Nästa steg
I det här enkla exemplet skickade du push-meddelanden till alla dina iOS-enheter. Mer information om hur du riktar in dig på specifika användare finns i självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare]. Om du vill dela in användarna efter intressegrupper läser du [Använda Notification Hubs för att skicka de senaste nyheterna]. Mer information om hur du använder Notification Hubs finns i [Riktlinjer för Notification Hubs] och i [Notification Hubs-instruktioner för iOS].

<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Komma igång med Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Klassisk Azure-portal]: https://manage.windowsazure.com/
[Riktlinjer för Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs-instruktioner för iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Använda Notification Hubs för att skicka push-meddelanden till användare]: /manage/services/notification-hubs/notify-users-aspnet
[Använda Notification Hubs för att skicka de senaste nyheterna]: /manage/services/notification-hubs/breaking-news-dotnet

[programmeringsguide för lokala meddelanden och push-meddelanden]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Azure-portalen]: https://portal.azure.com
