<properties
    pageTitle="Komma igång med Azure Mobile Engagement för iOS i Swift | Microsoft Azure"
    description="Lär dig hur du använder Azure Mobile Engagement med analyser och push-meddelanden för iOS-appar."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />


# Komma igång med Azure Mobile Engagement för iOS-appar i Swift

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I det här avsnittet beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i ett iOS-program.
I den här självstudiekursen skapar du en tom iOS-app som samlar in grundläggande data och tar emot push-meddelanden via Apple Push Notification System (APNS).

Följande krävs för den här självstudiekursen:

+ XCode 8, som du kan installera från Mac App Store
+ [Mobile Engagement iOS SDK]
+ Certifikat för push-meddelanden (.p12) som kan hämtas från Apple Dev Center

> [AZURE.NOTE] Den här kursen använder Swift version 3.0. 

Du måste slutföra den här självstudiekursen innan du påbörjar någon annan kurs om Mobile Engagement och iOS-appar.

> [AZURE.NOTE] Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer info om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a id="setup-azme"></a>Konfigurera Mobile Engagement för din iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Anslut appen till Mobile Engagement-serverdelen

I den här kursen behandlas en ”grundläggande integration”, vilket är den minsta uppsättningen som krävs för att samla in data och skicka ett push-meddelande. Den fullständiga integrationsdokumentationen finns i [Mobile Engagement iOS SDK-integration](mobile-engagement-ios-sdk-overview.md)

Vi skapar en grundläggande app i XCode för att demonstrera integrationen:

###Skapa ett nytt iOS-projekt

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###Ansluta appen till Mobile Engagement-serverdelen

1. Ladda ned [Mobile Engagement iOS SDK]
2. Extrahera .tar.gz-filen till en mapp på datorn
3. Högerklicka på projektet och välj sedan Lägg till filer i ...

    ![][1]

4. Navigera till mappen dit du extraherade SDK, markera mappen `EngagementSDK` och klicka sedan på OK.

    ![][2]

5. Öppna fliken `Build Phases` och lägg till ramverk enligt nedan med hjälp av menyn `Link Binary With Libraries`:

    ![][3]

8. Skapa en interimshuvudfil för att kunna använda SDK:ns Objective C-API:er genom att välja Arkiv > Ny(tt) > Fil > iOS > Källa > Huvudfil.

    ![][4]

9. Redigera interimshuvudfilen och exponera Objective-C-koden i Mobile Engagement för Swift-koden genom att lägga till följande importer:

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. Gå till Build Settings (Versionsinställningar) och se till att inställningen för interimshuvudfilen med Objective-C har en sökväg till huvudfilen under Swift Compiler – Code Generation (Swift-kompilator – Kodgenerering). Här följer ett exempel på en sökväg: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (beroende på sökvägen)**

    ![][6]

11. Gå tillbaka till Azure-portalen via appsidan *Anslutningsinformation* och kopiera anslutningssträngen

    ![][5]

12. Klistra in anslutningssträngen i delegaten `didFinishLaunchingWithOptions`

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Aktivera realtidsövervakning

Du måste skicka minst en skärm (aktivitet) till Mobile Engagement-serverdelen för att kunna börja skicka data och se till att användarna är aktiva.

1. Öppna filen **ViewController.swift** byt ut basklassen **ViewController** mot **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>Anslut appen med realtidsövervakning

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivera push-meddelanden och meddelanden i appen

Med Mobile Engagement kan du samverka med användarna, nå ut till dem och köra kampanjer med push-meddelanden och meddelanden i appen. Modulen som används för det heter REACH och finns i Mobile Engagement-portalen.
I följande avsnitt konfigurerar du appen för att ta emot dem.

### Konfigurera appen för att ta emot tysta push-meddelanden

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### Lägg till Reach-biblioteket i projektet

1. Högerklicka på ditt projekt
2. Välj `Add file to ...`
3. Navigera till mappen dit du extraherade SDK
4. Välj mappen `EngagementReach`
5. Klicka på Lägg till
6. Redigera interimshuvudfilen och exponera Objective-C-koden i Mobile Engagement med Reach-huvuden genom att lägga till följande importer:

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### Ändra programdelegaten

1. Inifrån metoden `didFinishLaunchingWithOptions` skapar du en räckviddsmodul och skickar den till din befintliga initieringsrad för Engagement:

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###Konfigurera appen för att ta emot push-meddelanden med APNS
1. Lägg till följande rad i metoden `didFinishLaunchingWithOptions`:

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. Lägg till `didRegisterForRemoteNotificationsWithDeviceToken`-metoden på följande sätt:

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Lägg till `didReceiveRemoteNotification:fetchCompletionHandler:`-metoden på följande sätt:

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png



<!--HONumber=Oct16_HO3-->


