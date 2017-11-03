---
title: Azure Mobile Engagement iOS SDK uppgradera proceduren | Microsoft Docs
description: "Senaste uppdateringarna och procedurer för iOS SDK för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 72a9e493-3f14-4e52-b6e2-0490fd04b184
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: 37c7f133d079186f828d58cabce0d2a259efd085
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-procedures"></a>Uppgraderingsprocesser
Om du redan har integrerat en äldre version av Engagement till programmet, måste du Tänk på följande när du uppgraderar SDK.

För varje ny version av SDK måste du först ersätta (ta bort och importera på nytt i xcode) mapparna EngagementSDK och EngagementReach.

## <a name="from-300-to-400"></a>Från 3.0.0 till 4.0.0
### <a name="xcode-8"></a>XCode 8
XCode 8 är obligatoriskt från version 4.0.0 av SDK.

> [!NOTE]
> Om du verkligen är beroende av XCode 7 så att du kan använda den [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Det finns ett känt fel på reach-modulen för den här tidigare version när du kör på iOS 10-enheter: systemmeddelanden är inte åtgärdade. Åtgärda detta måste du implementera föråldrad API `application:didReceiveRemoteNotification:` i din app delegera på följande sätt:
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Vi rekommenderar inte den här lösningen** som detta beteende kan ändras i kommande (även mindre) iOS version uppgraderar eftersom den här iOS API är inaktuell. Du måste växla till XCode 8 så snart som möjligt.
> 
> 

### <a name="usernotifications-framework"></a>UserNotifications framework
Du måste lägga till den `UserNotifications` ramverk i din Build Phases.

Öppna ditt projekt i Projektutforskaren, och välj rätt mål. Öppna den **”Build-faser”** fliken och i den **”länka binär med bibliotek”** menyn Lägg till framework `UserNotifications.framework` -ange länken som`Optional`

### <a name="application-push-capability"></a>Programmet push-funktion
XCode 8 kan återställa din app push-funktion, kontrollera den den `capability` fliken i ditt valda målet.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Lägg till den nya iOS 10 registrering Meddelandekoden
Äldre kodfragmentet att registrera app för meddelanden fungerar fortfarande men använder föråldrad API: er när du kör på iOS 10.

Importera den `User Notification` framework:

        #import <UserNotifications/UserNotifications.h> 

I programdelegaten `application:didFinishLaunchingWithOptions` metoden Ersätt:

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

av:

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

## <a name="from-200-to-300"></a>Från 2.0.0 till 3.0.0
Bort stöd för iOS 4.X. Startar från den här versionen av distributionsmålet för ditt program måste vara minst iOS 6.

Om du använder Reach i ditt program, måste du lägga till `remote-notification` värde till den `UIBackgroundModes` matris i filen Info.plist för att kunna ta emot meddelanden för fjärråtkomst.

Metoden `application:didReceiveRemoteNotification:` måste ersättas med `application:didReceiveRemoteNotification:fetchCompletionHandler:` i programdelegaten.

”AEPushDelegate.h” är föråldrad gränssnitt och du måste ta bort alla referenser. Detta inbegriper att ta bort `[[EngagementAgent shared] setPushDelegate:self]` och delegatmetoder från programdelegaten:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1160-to-200"></a>Från 1.16.0 till 2.0.0
Nedan beskrivs hur du migrerar en SDK-integration från tjänsten Capptain som erbjuds av Capptain SAS i en app med Azure Mobile Engagement.
Kontakta Capptain webbplats om du vill migrera till 1.16 först och sedan använda följande procedur om du migrerar från en tidigare version.

> [!IMPORTANT]
> Capptain och Mobile Engagement är inte samma tjänster och proceduren nedan visar hur du migrerar klientappen endast. Migrera SDK i appen kommer inte migrera dina data från servrar som Capptain till Mobile Engagement-servrar
> 
> 

### <a name="agent"></a>Agent
Metoden `registerApp:` har ersatts av den nya metoden `init:`. Programdelegaten måste uppdateras och Använd anslutningssträng:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

SmartAd spårning har tagits bort från SDK som du behöver bara ta bort alla förekomster av `AETrackModule` klass

### <a name="class-name-changes"></a>Klassen namnändringar
Som en del av den rebranding finns det några klassen/filnamn som behöver ändras.

Alla klasser prefixet ”CP” ändras med ”AE” prefix.

Exempel:

* `CPModule.h`har bytt namn till `AEModule.h`.

Alla klasser prefixet ”Capptain” ändras med prefixet ”Engagement”.

Exempel:

* Klassen `CapptainAgent` ändras till `EngagementAgent`.
* Klassen `CapptainTableViewController` ändras till `EngagementTableViewController`.
* Klassen `CapptainUtils` ändras till `EngagementUtils`.
* Klassen `CapptainViewController` ändras till `EngagementViewController`.

