<properties
    pageTitle="Komma igång med Azure Mobile Engagement för iOS i Objective C | Microsoft Azure"
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
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />


# Komma igång med Azure Mobile Engagement för iOS-appar i Objective C

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I det här avsnittet beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i ett iOS-program.
I den här självstudiekursen skapar du en tom iOS-app som samlar in grundläggande data och tar emot push-meddelanden via Apple Push Notification System (APNS).

Följande krävs för den här självstudiekursen:

+ XCode 8, som du kan installera från Mac App Store
+ [Mobile Engagement iOS SDK]

Du måste slutföra den här självstudiekursen innan du påbörjar någon annan kurs om Mobile Engagement och iOS-appar.

> [AZURE.NOTE] Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer info om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).

##<a id="setup-azme"></a>Konfigurera Mobile Engagement för din iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Anslut appen till Mobile Engagement-serverdelen

I den här kursen behandlas en ”grundläggande integration”, vilket är den minsta uppsättningen som krävs för att samla in data och skicka ett push-meddelande. Den fullständiga integrationsdokumentationen finns i [Mobile Engagement iOS SDK-integration](mobile-engagement-ios-sdk-overview.md)

Vi skapar en grundläggande app i XCode för att demonstrera integrationen.

###Skapa ett nytt iOS-projekt

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###Ansluta appen till Mobile Engagement-serverdelen

1. Ladda ned [Mobile Engagement iOS SDK].
2. Extrahera .tar.gz-filen till en mapp på datorn.
3. Högerklicka på projektet och välj sedan **Lägg till filer i**.

    ![][1]

4. Navigera till mappen dit du extraherade SDK, markera mappen `EngagementSDK` och klicka sedan på **OK**.

    ![][2]

5. Öppna fliken **Build Phases** (Byggfaser) och gå till menyn **Link Binary With Libraries** (Länka binära värden till bibliotek) och lägg till ramverk enligt nedan:

    ![][3]

6. För **XCode 7** – lägg till `libxml2.tbd` istället för `libxml2.dylib`.

7. Gå tillbaka till Azure-portalen via appsidan **Anslutningsinformation** och kopiera anslutningssträngen.

    ![][4]

8. Lägg till följande rad med kod i filen **AppDelegate.m**.

        #import "EngagementAgent.h"

9. Klistra in anslutningssträngen i delegaten `didFinishLaunchingWithOptions`.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [...]
            //[EngagementAgent setTestLogEnabled:YES];
   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
        }

10. `setTestLogEnabled` är ett valfritt uttryck som aktiverar SDK-loggar som sedan kan användas till att identifiera problem. 

##<a id="monitor"></a>Aktivera realtidsövervakning

För att kunna börja skicka data och försäkra dig om att användarna är aktiva måste du skicka minst en skärm (aktivitet) till Mobile Engagement-serverdelen.

1. Öppna filen **ViewController.h** och importera **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Ersätt superklassen för **ViewController**-gränssnittet med `EngagementViewController`:

    `@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Anslut appen med realtidsövervakning

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivera push-meddelanden och meddelanden i appen

Med Mobile Engagement kan du samverka med användarna, och köra kampanjer med push-meddelanden och meddelanden i appen. Modulen som används för det heter REACH och finns i Mobile Engagement-portalen.
I följande avsnitt konfigurerar du appen för att ta emot dem.

### Konfigurera appen för att ta emot tysta push-meddelanden

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]  

### Lägg till Reach-biblioteket i projektet

1. Högerklicka på ditt projekt.
2. Välj **Lägg till fil i**.
3. Navigera till mappen dit du extraherade SDK.
4. Välj mappen `EngagementReach`.
5. Klicka på **Lägg till**.

### Ändra programdelegaten

1. I filen **AppDeletegate.m** importerar du räckviddsmodulen för Engagement.

        #import "AEReachModule.h"

2. Inifrån metoden `application:didFinishLaunchingWithOptions` skapar du en räckviddsmodul och skickar den till din befintliga initieringsrad för Engagement:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

###Konfigurera appen för att ta emot push-meddelanden med APNS

1. Lägg till följande rad i metoden `application:didFinishLaunchingWithOptions`:

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

2. Lägg till `application:didRegisterForRemoteNotificationsWithDeviceToken`-metoden på följande sätt:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }

3. Lägg till `didFailToRegisterForRemoteNotificationsWithError`-metoden på följande sätt:

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           
           NSLog(@"Failed to get token, error: %@", error);
        }

4. Lägg till `didReceiveRemoteNotification:fetchCompletionHandler`-metoden på följande sätt:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png




<!--HONumber=Sep16_HO3-->


