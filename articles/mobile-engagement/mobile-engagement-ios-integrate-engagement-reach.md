---
title: "Azure Mobile Engagement iOS SDK nå Integration | Microsoft Docs"
description: "Senaste uppdateringarna och procedurer för iOS SDK för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: ba74e0c442ac10f096d465f989e03d2ceae8cd88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>Hur man integrerar Engagement når på iOS
Du måste följa proceduren integrering i den [hur du integrerar Engagement för iOS-dokument](mobile-engagement-ios-integrate-engagement.md) innan du följer den här guiden.

Den här dokumentationen kräver XCode 8. Om du verkligen är beroende av XCode 7 så att du kan använda den [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Det finns ett känt fel på den här tidigare version när du kör på iOS 10-enheter: systemmeddelanden är inte åtgärdade. Åtgärda detta måste du implementera föråldrad API `application:didReceiveRemoteNotification:` i din app delegera på följande sätt:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Vi rekommenderar inte den här lösningen** som detta beteende kan ändras i kommande (även mindre) iOS version uppgraderar eftersom den här iOS API är inaktuell. Du måste växla till XCode 8 så snart som möjligt.
>
>

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Konfigurera appen för att ta emot tysta push-meddelanden
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>Integreringen
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Bädda in Engagement nå SDK i din iOS-projekt
* Lägg till Reach sdk i Xcode-projektet. I Xcode, går du till **projekt \> Lägg till projektet** och välj den `EngagementReach` mapp.

### <a name="modify-your-application-delegate"></a>Ändra programdelegaten
* Importera modulen Engagement nå längst upp i implementeringsfilen:

      [...]
      #import "AEReachModule.h"
* Inuti metoden `applicationDidFinishLaunching:` eller `application:didFinishLaunchingWithOptions:`, skapar du en räckviddsmodul och skickar den till din befintliga initieringsrad:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]

        return YES;
      }
* Ändra **'icon.png'** med Avbildningsnamnet som du vill ha som ditt meddelandeikonen.
* Om du vill använda alternativet *Aktivitetsikon uppdateringsvärde* i Reach-kampanjer eller om du vill använda interna push \<SaaS Reach/API/kampanj format/intern Push\> kampanjer, måste du låta räckvidden modulen Hantera Aktivitetsikon ikonen sig själv (den tas bort automatiskt i programmet Aktivitetsikon och även återställa värdet lagras av Engagement varje gång programmet är igång eller foregrounded). Detta görs genom att lägga till följande rad efter initiering av Reach-modulen:

      [reach setAutoBadgeEnabled:YES];
* Om du vill hantera Reach data-push, måste du låta programdelegaten överensstämmer med de `AEReachDataPushDelegate` protokoll. Lägg till följande rad efter initiering av Reach-modulen:

      [reach setDataPushDelegate:self];
* Sedan kan du implementera metoderna `onDataPushStringReceived:` och `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` i programdelegaten:

      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }

      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>Kategori
Kategori-parametern är valfri när du skapar en Data-Push-kampanj och gör att du kan filtrera data push-meddelanden. Detta är användbart om du vill skicka olika typer av `Base64` data och vill identifiera typ innan parsningen dem.

**Tillämpningsprogrammet är nu redo att ta emot och visa reach innehållet!**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>Ta emot meddelanden och avsökningar när som helst
Engagement kan du skicka Reach-meddelanden till användarna när som helst med hjälp av Apple Push Notification Service.

Om du vill aktivera den här funktionen måste du förbereda ditt program för Apple push-meddelanden och ändra programdelegaten.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Förbered ditt program för Apple push-meddelanden
Följ guiden: [hur du förbereder ditt program för Apple Push-meddelanden](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Lägg till nödvändiga kod
*Ditt program bör nu ha en registrerad Apple push-certifikat Engagement klientdel.*

Om det inte är redan gjort måste du registrera ditt program för att ta emot push-meddelanden.

* Importera den `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>
* Lägg till följande rad när programmet startas (vanligtvis i `application:didFinishLaunchingWithOptions:`):

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

Måste du ange engagement enhetstoken som returneras av Apple-servrar. Detta görs i metoden med namnet `application:didRegisterForRemoteNotificationsWithDeviceToken:` i programdelegaten:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Slutligen har du informera Engagement SDK när programmet tar emot ett meddelande om fjärråtkomst. Göra genom att anropa metoden `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` i programdelegaten:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!IMPORTANT]
> Som standard kontrollerar Engagement når completionHandler. Om du vill att svara manuellt på den `handler` blockera i koden, kan du ange noll för den `handler` argumentet och kontroll slutförs blockera själv. Finns det `UIBackgroundFetchResult` typ för en lista över möjliga värden.
>
>

### <a name="full-example"></a>Fullständigt exempel
Här är ett fullständigt exempel på integrering:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>UNUserNotificationCenter ombud konfliktlösning

*Om varken ditt program eller en av tredjeparts-biblioteken implementerar en `UNUserNotificationCenterDelegate` och sedan kan du hoppa över den här delen.*

En `UNUserNotificationCenter` ombud används av SDK för att övervaka livscykeln för Engagement meddelanden på enheter som kör IOS 10 eller senare. SDK har sin egen implementering av den `UNUserNotificationCenterDelegate` protokoll, men det kan bara finnas ett `UNUserNotificationCenter` delegera per program. Andra ombud som lagts till i `UNUserNotificationCenter` objekt står i konflikt med en Engagement. Om SDK upptäcker din eller någon annan tredje parts ombud kommer den inte använda sin egen implementering som ger dig möjlighet att lösa konflikter. Du måste lägga till Engagement-kod till din egen ombud för att lösa konflikter.

Det finns två sätt att göra detta.

Förslag 1, genom att vidarebefordra ombudet anrop till SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Eller förslag 2, som ärver från den `AEUserNotificationHandler` klass

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Du kan fastställa om ett meddelande kommer från Engagement eller inte genom att ange dess `userInfo` ordlista till agenten `isEngagementPushPayload:` klassen metoden.

Se till att den `UNUserNotificationCenter` objektets ombud är inställd på ombudet i antingen den `application:willFinishLaunchingWithOptions:` eller `application:didFinishLaunchingWithOptions:` -metoden i programdelegaten.
Till exempel om du har implementerat ovan förslag 1:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="how-to-customize-campaigns"></a>Hur du anpassar kampanjer
### <a name="notifications"></a>Meddelanden
Det finns två typer av aviseringar: systemet och i app-meddelanden.

Systemmeddelanden hanteras av iOS och kan inte anpassas.

I appen meddelanden består av en vy som läggs till dynamiskt till det aktuella programmet-fönstret. Detta kallas en meddelandet överlägget. Meddelande överlägg är bra för en snabb integrering eftersom de behöver du inte att ändra vyn i ditt program.

#### <a name="layout"></a>Layout
Om du vill ändra utseendet på meddelanden i appen, kan du bara ändra filen `AENotificationView.xib` dina behov, så länge du hålla taggvärden och typer av befintliga undervyer.

Som standard visas i appen meddelanden längst ned på skärmen. Om du vill visa dem längst upp på skärmen, redigera angiven `AENotificationView.xib` och ändra den `AutoSizing` egenskapen för den huvudsakliga vyn så att den kan hållas överst i dess superview.

#### <a name="categories"></a>Kategorier
När du ändrar den tillhandahållna layouten, kan du ändra utseendet på alla meddelanden. Kategorier kan du definiera olika riktade verkar (möjligen beteenden) för meddelanden. En kategori kan anges när du skapar en Reach-kampanj. Tänk på att kategorier kan du anpassa meddelanden och avsökningar, som beskrivs senare i det här dokumentet.

Om du vill registrera en hanterare för kategori för meddelanden som du behöver lägga till ett anrop när reach-modulen har initierats.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`måste vara en instans av ett objekt som överensstämmer med protokollet `AENotifier`.

Du kan implementera metoder för protokollet själv eller du kan välja att reimplement befintlig klass `AEDefaultNotifier` som redan utför det mesta av arbetet.

Om du vill definiera om Aviseringsvy för en viss kategori kan du följa det här exemplet:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Det här enkla exemplet på kategorin förutsätter att du har en fil med namnet `MyNotificationView.xib` i programmets-paket. Om metoden inte går att hitta någon motsvarande `.xib`, visas inte meddelandet och Engagement kommer att bli ett meddelande i konsolen.

Den angivna nib filen bör beakta följande regler:

* Det får endast innehålla en vy.
* Undervyer ska vara av samma typ som som i den angivna nib-fil med namnet`AENotificationView.xib`
* Undervyer ska ha samma taggar som som inom den angivna nib fil med namnet`AENotificationView.xib`

> [!TIP]
> Bara kopiera angivna nib-filen som heter `AENotificationView.xib`, och börjar arbeta därifrån. Men tänk på att, vyn i den här nib-fil som är kopplad till klassen `AENotificationView`. Den här klassen omdefinieras metoden `layoutSubViews` flytta och ändra dess undervyer enligt kontext. Du kanske vill ersätta den med en `UIView` eller en anpassad vy klass.
>
>

Om du behöver djupare anpassning av meddelanden (om du exempelvis vill att läsa in vyn direkt från koden), rekommenderas att ta en titt på den angivna källa kod och klassen dokumentationen för `Protocol ReferencesDefaultNotifier` och `AENotifier`.

Observera att du kan använda samma meddelaren för flera kategorier.

Du kan också definieras standard meddelaren så här:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Meddelande-hantering
När du använder standardkategori, vissa livscykel metoder anropas på den `AEReachContent` objekt till rapporten statistik och uppdatera tillståndet kampanj:

* När meddelandet visas i programmet, den `displayNotification` metoden anropas (som rapporterar statistik) av `AEReachModule` om `handleNotification:` returnerar `YES`.
* Om meddelandet avvisas den `exitNotification` metoden anropas statistik rapporteras och nästa kampanjer kan nu bearbetas.
* Om du klickar på meddelandet `actionNotification` är anropas statistik rapporteras och relaterade åtgärder utförs.

Om din implementering av `AENotifier` kringgår standardbeteendet, måste du anropa metoderna livscykel själv. I följande exempel visas tillfällen där standardbeteendet kringgås:

* Du inte utökar `AEDefaultNotifier`, t.ex. du har implementerat kategori hantering från grunden.
* Du overrode `prepareNotificationView:forContent:`, se till att mappa minst `onNotificationActioned` eller `onNotificationExited` till en av dina U.I styr.

> [!WARNING]
> Om `handleNotification:` returnerar ett undantag innehållet tas bort och `drop` är anropas detta rapporteras i statistik och nästa kampanjer kan nu bearbetas.
>
>

#### <a name="include-notification-as-part-of-an-existing-view"></a>Inkludera meddelande som en del av en befintlig vy
Överlägg är bra för en snabb integrering men kan ibland inte vara praktiskt eller kan orsaka oönskade sidoeffekter.

Om du inte är nöjd med överlägget systemet i några av dina vyer kan anpassa du den för dessa vyer.

Du kan välja att inkludera layout för våra meddelanden i din befintliga vyer. Om du vill göra det, finns det två implementering format:

1. Lägga till vyn meddelanden med hjälp av gränssnittet builder

   * Öppna *gränssnitt Builder*
   * Placera 320 x 60 (eller 768 x 60 om du är på iPad) `UIView` där du vill att meddelanden ska visas
   * Ange värdet för taggen för den här vyn så att: **36822491**
2. Lägga till vyn meddelande via programmering. Lägg bara till följande kod när vyn har initierats:

       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`makrot finns i `AEDefaultNotifier.h`.

> [!NOTE]
> Standard-meddelaren identifierar automatiskt att meddelande layout ingår i den här vyn och kommer inte att lägga till ett överlägg för den.
>
>

### <a name="announcements-and-polls"></a>Meddelanden och avsökningar
#### <a name="layouts"></a>Layouter
Du kan ändra filerna `AEDefaultAnnouncementView.xib` och `AEDefaultPollView.xib` så länge som du behåller taggvärden och typer av befintliga undervyer.

#### <a name="categories"></a>Kategorier
##### <a name="alternate-layouts"></a>Alternativa layouter
Kampanjens kategori kan användas ha alternativa layouter för meddelanden och avsökningar som meddelanden.

Om du vill skapa en kategori för ett meddelande måste du utöka **AEAnnouncementViewController** och registrera den när reach-modulen har initierats:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> Varje gång en användare kommer klickar du på ett meddelande för ett meddelande i kategorin ”min\_kategori”, registrerade view-controller (i så fall `MyCustomAnnouncementViewController`) initieras genom att anropa metoden `initWithAnnouncement:` och vyn kommer att läggas till det aktuella programmet-fönstret.
>
>

I implementeringen av den `AEAnnouncementViewController` klass som du behöver läsa egenskapen `announcement` initieras din undervyer. Studera exemplet nedan, där två märker initierats med `title` och `body` egenskaper för den `AEReachAnnouncement` klass:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Om du inte vill att läsa in dina vyer själv, men du vill återanvända standard meddelande Visa layout, kan du bara se styrenheten för anpassad vy utökar klassen angivna `AEDefaultAnnouncementViewController`. I så fall kan kopiera filen nib `AEDefaultAnnouncementView.xib` och Byt namn på den så den kan läsas av anpassade view-controller (för en domänkontrollant med namnet `CustomAnnouncementViewController`, ska du anropa nib filen `CustomAnnouncementView.xib`).

Om du vill ersätta standardaktivitetskategori för meddelanden, helt enkelt registrera styrenheten anpassad vy för den kategori som definierats i `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Omröstningar kan anpassas på samma sätt:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Den här tiden, den angivna `MyCustomPollViewController` måste utöka `AEPollViewController`. Eller kan du utöka från standard domänkontrollant: `AEDefaultPollViewController`.

> [!IMPORTANT]
> Glöm inte att anropa antingen `action` (`submitAnswers:` för anpassade avsökningsdatum visa domänkontrollanter) eller `exit` metoden innan view controller avvisas. Annars statistik skickas inte (dvs. inga analyser på kampanjen) och mer allt nästa kampanjer meddelas inte förrän programmet startas.
>
>

##### <a name="implementation-example"></a>Exempel på implementering
I den här implementeringen anpassade meddelande vyn har lästs in från en extern xib-fil.

Precis som för avancerade notification anpassning rekommenderas att titta på källkoden för standardimplementeringen.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
