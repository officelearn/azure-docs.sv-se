---
title: Azure Mobile Engagement iOS SDK-Integration | Microsoft Docs
description: "Senaste uppdateringarna och procedurer för iOS SDK för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 01fdbb43c21ac6932e8462f4a6507fc63e50542d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-on-ios"></a>Hur du integrerar Engagement för iOS
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
>
>

Den här proceduren beskriver det enklaste sättet att aktivera Engagement analyser och övervakningsfunktionerna i ditt iOS-program.

Kräver Engagement SDK iOS7 + och Xcode 8 +: av distributionsmålet för ditt program måste vara minst iOS 7.

> [!NOTE]
> Om du verkligen är beroende av XCode 7 så att du kan använda den [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Det finns ett känt fel på Reach-modulen för den här tidigare version när du kör på iOS 10 enheter Se [reach-modulen integration](mobile-engagement-ios-integrate-engagement-reach.md) för mer information. Om du använder SDK v3.2.4 sedan bara hoppa den `UserNotifications.framework` importera i nästa steg.
>
>

Följande steg är tillräckligt för att aktivera rapporten i loggar som behövs för att beräkna all statistik om användare, sessioner, aktiviteter, krascher och Technicals. Rapporten loggar som behövs för att beräkna andra statistik som händelser, fel och jobb måste göras manuellt med hjälp av Engagement-API (se [hur du använder avancerade Mobile Engagement API-märkning i din iOS-app](mobile-engagement-ios-use-engagement-api.md) eftersom statistiken beroende program.

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>Bädda in Engagement SDK i din iOS-projekt
* Hämta iOS SDK från [här](http://aka.ms/qk2rnj).
* Lägg till Engagement-SDK för iOS-projekt: i Xcode, högerklicka på projektet och välj **”Lägg till filer i...”** och välj den `EngagementSDK` mapp.
* Engagement kräver ytterligare ramverk ska fungera: öppna ditt projekt i Projektutforskaren, och välj rätt mål. Öppna den **”Build-faser”** fliken och i den **”länka binär med bibliotek”** menyn Lägg till dessa ramverk:

  * `UserNotifications.framework`-Ange länken som`Optional`
  * `AdSupport.framework`-Ange länken som`Optional`
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> AdSupport framework kan tas bort. Engagement måste det här ramverket för att samla in IDFA. Men du kan inaktivera IDFA-insamling \<ios-sdk-engagement-idfa\> att följa den nya principen Apple om detta ID.
>
>

## <a name="initialize-the-engagement-sdk"></a>Initiera Engagement SDK
Du behöver ändra Programdelegaten:

* Importera Engagement agenten längst upp i implementeringsfilen:

      [...]
      #import "EngagementAgent.h"
* Initiera Engagement inuti metoden '**applicationDidFinishLaunching:**'eller'**program: didFinishLaunchingWithOptions:**':

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>Grundläggande reporting
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Rekommenderad metod: överlagra din `UIViewController` klasser
För att aktivera rapporten i alla loggar som krävs av Engagement att beräkna användare, sessioner, aktiviteter, krascher och tekniska statistik, kan du bara göra alla dina `UIViewController` underordnade klasser ärver från den `EngagementViewController` klasser (samma regel för `UITableViewController`  -\> `EngagementTableViewController`).

**Utan Engagement:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Med Engagement:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Alternativ metod: anropa `startActivity()` manuellt
Om du inte kan eller inte vill överlagra din `UIViewController` klasser, i stället kan du starta dina aktiviteter genom att anropa `EngagementAgent`'s metoder direkt.

> [!IMPORTANT]
> IOS SDK automatiskt anropar den `endActivity()` metoden när programmet stängs. Därför är det *hög* rekommenderas att anropa den `startActivity` metod när aktiviteten användaren ändrar och *aldrig* anropa den `endActivity` metoden, eftersom den här metoden anropas tvingar aktuellt sessionen avslutas.
>
>

## <a name="location-reporting"></a>Platsrapportering
Apple användarvillkoren tillåter inte att program kan använda spårning för endast statistik ändamål. Därför rekommenderas att aktivera plats rapporter bara om programmet använder också platsen för spårning av en annan anledning.

Från och med iOS 8, måste du ange en beskrivning av hur din app använder platstjänster genom att ange en sträng för nyckeln [NSLocationWhenInUseUsageDescription] eller [NSLocationAlwaysUsageDescription]i filen Info.plist för din app. Lägg till nyckel NSLocationAlwaysUsageDescription om du vill rapporten platsen i bakgrunden med Engagement. Lägg till nyckel NSLocationWhenInUseUsageDescription i andra fall. Observera att du måste både NSLocationAlwaysAndWhenInUseUsageDescription och NSLocationWhenInUseUsageDescription rapportens bakgrund plats på iOS 11.

### <a name="lazy-area-location-reporting"></a>Områdesrapportering
Områdesrapportering kan rapportera land, region och ort som är kopplade till enheter. Den här typen av plats reporting används endast nätverksplatser (baserat på cellen ID eller Wi-Fi). Området enheten rapporteras som mest en gång per session. GPS används aldrig och därmed den här typen av plats rapport har bara ett fåtal (ska säga Nej) inverkan på batteridrift.

Rapporterat områden används för att beräkna geografiska statistik om användare, sessioner, händelser och fel. De kan också användas som kriterium i Reach-kampanjer. Det senaste kända området som rapporterats för en enhet kan hämtas tack till den [Device API].

Om du vill aktivera områdesrapportering, lägger du till följande rad efter initiering Engagement agenten:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Rapportering av platsen i realtid
Realtid plats reporting kan rapportera latituden och longituden som är kopplade till enheter. Den här typen av plats reporting använder endast nätverksplatser (baserat på cellen ID eller Wi-Fi) som standard och reporting är bara aktivt när programmet körs i förgrunden (dvs. under en session).

Realtid platser är *inte* används för att beräkna statistik. Deras endast syfte är att tillåta användning av realtid geobegränsning \<Reach-målgruppen-geofencing\> kriterium i Reach-kampanjer.

För att aktivera rapportering om realtid plats, lägger du till följande rad efter initiering Engagement agenten:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS baseras reporting
Som standard används rapportering av platsen i realtid endast baserat nätverksplatser. Lägg till om du vill aktivera användning av GPS-baserade platser (vilket är mycket mer exakt):

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Rapportering i bakgrunden
Som standard är realtid plats reporting endast aktiv när programmet körs i förgrunden (dvs. under en session). Om du vill aktivera reporting även i bakgrunden, lägger du till:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> När programmet körs i bakgrunden, endast nätverksbaserat platser rapporteras, även om du har aktiverat GPS.
>
>

Implementering av den här funktionen ska ringa [startMonitoringSignificantLocationChanges] när programmet försätts i bakgrunden. Tänk på att den automatiskt ska starta programmet i bakgrunden om en ny plats händelse anländer.

## <a name="advanced-reporting"></a>Avancerad rapportering
Du kan också om du vill rapportera programmet specifika händelser, fel och jobb du behöver använda Engagement API via metoderna i det `EngagementAgent` klass. Ett objekt av den här klassen kan hämtas genom att anropa den `[EngagementAgent shared]` statisk metod.

Engagement-API kan använda alla Engagement avancerade funktioner och beskrivs i hur du använder API: et Engagement på iOS (samt som i den tekniska dokumentationen för den `EngagementAgent` klassen).

## <a name="disable-idfa-collection"></a>Inaktivera IDFA-insamling
Som standard Engagement kommer att använda den [IDFA] att unikt identifiera en användare. Men om du inte använder annonserar någon annanstans i appen, du kan inte avvisa granskningsprocess App Store. IDFA-insamling kan inaktiveras genom att lägga till preprocessor makrot `ENGAGEMENT_DISABLE_IDFA` i filen pch (eller i den `Build Settings` av programmet). Detta säkerställer att det inte finns några referenser till `ASIdentifierManager`, `advertisingIdentifier` eller `isAdvertisingTrackingEnabled` i bygga program.

Integrering i den **prefix.pch** fil:

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Du kan kontrollera att IDFA-insamling korrekt är inaktiverad i ditt program genom att kontrollera Engagement test loggarna. Se integrering testa\<ios sdk-engagement-test-idfa\> dokumentationen för mer information.

## <a name="disable-log-reporting"></a>Inaktivera rapportering av logg
### <a name="using-a-method-call"></a>Med hjälp av ett metodanrop
Om du vill Engagement slutar skicka loggar, kan du anropa:

    [[EngagementAgent shared] setEnabled:NO];

Det här anropet är beständiga: den använder `NSUserDefaults` att lagra informationen.

Du kan aktivera loggen reporting igen genom att anropa funktionen samma med `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integrering i inställningar-paket
I stället för att anropa den här funktionen kan du också integrera inställningen direkt i din befintliga `Settings.bundle` fil. Strängen `engagement_agent_enabled` måste användas som en identifierare för inställningar och den måste vara kopplad till en växel växla (`PSToggleSwitchSpecifier`).

Följande exempel visar `Settings.bundle` visar hur du implementerar den:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
