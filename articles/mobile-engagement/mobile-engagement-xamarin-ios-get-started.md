<properties
    pageTitle="Kom igång med Azure Mobile Engagement för Xamarin.iOS"
    description="Lär dig hur du använder Azure Mobile Engagement med analyser och push-meddelanden för Xamarin.iOS-appar."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager=""
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="03/25/2016"
    ms.author="piyushjo" />

# Kom igång med Azure Mobile Engagement för Xamarin.iOS-appar

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I den här artikeln beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i ett Xamarin.iOS-program.
I den här kursen får du skapa en tom Xamarin.iOS-app som samlar in grundläggande data och tar emot push-meddelanden via Apple Push Notification System (APNS).

För den här kursen behöver du följande:

+ [Xamarin Studio](http://xamarin.com/studio). Du kan även använda Visual Studio med Xamarin men i den här kursen används Xamarin Studio. Det finns installationsanvisningar i avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
+ [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer info om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).

##<a id="setup-azme"></a>Konfigurera Mobile Engagement för din iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Ansluta appen till Mobile Engagement-serverdelen

I den här kursen behandlas en ”grundläggande integration”, vilket är den minsta uppsättningen som krävs för att samla in data och skicka ett push-meddelande.

Vi kommer att skapa en grundläggande app i Xamarin för att demonstrera integrationen:

###Skapa ett nytt Xamarin.iOS-projekt

1. Starta Xamarin Studio. Gå till **File** -> **New** -> **Solution** (Arkiv -> Ny -> Lösning). 

    ![][1]

2. Välj **Single View App** (App med enkel vy) och kontrollera att det valda språket är **C#**. Klicka sedan på **Next** (Nästa).

    ![][2]

3. Fyll i **appens namn** och **organisations-ID:t**, och klicka sedan på **Next** (Nästa). 

    ![][3]

    > [AZURE.IMPORTANT] Kontrollera att publiceringsprofilen som du så småningom ska använda för att distribuera iOS-appen använder ett app-ID som exakt matchar det paket-ID som du har här. 

4. Uppdatera **projektets namn**, **lösningens namn** och **platsen** om det behövs, och klicka på **Create** (Skapa).

    ![][4]
 
Xamarin Studio skapar demoappen där Mobile Engagement ska integreras. 

###Ansluta appen till Mobile Engagement-serverdelen

1. Högerklicka på mappen **Packages** (Paket) i lösningsfönstret och välj **Add Packages** (Lägg till paket).

    ![][5]

2. Sök efter **Microsoft Azure Mobile Engagement Xamarin SDK** och lägg till den i lösningen.  

    ![][6]
   
3. Öppna **AppDelegate.cs** och lägg till följande med instruktionen:

        using Microsoft.Azure.Engagement.Xamarin;

4. Lägg till följande i metoden **FinishedLaunching** för att initiera anslutningen till Mobile Engagement-serverdelen. Var noga med att lägga till din **ConnectionString**. I den här koden används även en **NotificationIcon**-platshållare som läggs till av Mobile Engagement SDK. Det kan hända att du vill ersätta den. 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>Aktivera realtidsövervakning

För att kunna börja skicka data och försäkra dig om att användarna är aktiva måste du skicka minst en skärm till Mobile Engagement-serverdelen.

1. Öppna **ViewController.cs** och lägg till följande med instruktionen:

        using Microsoft.Azure.Engagement.Xamarin;

2. Ersätt klassen från vilken `ViewController` ärver från `UIViewController` till `EngagementViewController`. 

##<a id="monitor"></a>Ansluta appen med realtidsövervakning

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivera push-meddelanden och meddelanden i appen

Med Mobile Engagement kan du samverka med användarna, och köra kampanjer med push-meddelanden och meddelanden i appen. Modulen som används för det heter REACH och finns i Mobile Engagement-portalen.
I följande avsnitt konfigurerar du appen för att ta emot dem.

### Ändra programdelegaten

1. Öppna **AppDelegate.cs** och lägg till följande med instruktionen:

        using System; 

2. Lägg sedan till följande i metoden `FinishedLaunching` för att registrera för push-meddelanden efter `EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Till sist ska du uppdatera eller lägga till följande metoder:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. Bekräfta i lösningens **Info.plist**-fil att **Bundle Identifier** (Paket-ID) matchar det **App ID** som du har i din etableringsprofil i Apple Dev Center. 

    ![][7]

5. Kontrollera i samma **Info.plist**-fil att du har markerat **Enable Background Modes** (Aktivera bakgrundslägen) och **Remote Notifications** (Fjärrmeddelanden). 

    ![][8]

6. Kör appen på den enhet som du har associerat med den här publiceringsprofilen. 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png



<!--HONumber=Jun16_HO2-->


