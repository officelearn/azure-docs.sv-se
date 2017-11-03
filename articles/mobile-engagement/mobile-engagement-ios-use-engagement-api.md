---
title: "Hur du använder Engagement API på iOS"
description: "Senaste iOS SDK - använda Engagement API på iOS"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: a31424da98205e97bdf57010cccfd044360f03dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-ios"></a>Hur du använder Engagement API på iOS
Det här dokumentet är ett tillägg till dokumentet hur du integrerar Engagement för iOS: ger i djup information om hur du använder Engagement API för att rapportera programmet-statistik.

Kom ihåg att om du bara vill Engagement att rapportera programmets sessioner, aktiviteter, krascher och teknisk information sedan det enklaste sättet är att se alla dina anpassade `UIViewController` objekt ärver från motsvarande `EngagementViewController` klass.

Om du vill göra mer, till exempel om du behöver rapportera programmet specifika händelser, fel och jobb, eller om du vill rapportera ditt programs aktiviteter i ett annat sätt än den som implementerats i den `EngagementViewController` klasser, måste du använda Engagement-API.

Engagement-API som tillhandahålls av den `EngagementAgent` klass. En instans av den här klassen kan hämtas genom att anropa den `[EngagementAgent shared]` statisk metod (Observera att den `EngagementAgent` objektet som returnerades är en singleton).

Innan API-anrop i `EngagementAgent` objektet måste initieras genom att anropa metoden`[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

## <a name="engagement-concepts"></a>Koncept i engagement
Följande delar förfina vanliga [koncept i Mobile Engagement](mobile-engagement-concepts.md) för iOS-plattformen.

### <a name="session-and-activity"></a>`Session` och `Activity`
En *aktiviteten* är ofta kopplade till en skärm för programmet, det vill säga den *aktiviteten* när skärmen visas och stoppas när skärmen stängs: så är fallet när Engagement-SDK är integrerad med hjälp av den `EngagementViewController` klasser.

Men *aktiviteter* kan också kontrolleras manuellt med hjälp av Engagement-API. Detta gör för att dela en viss skärm i flera sub delar för att få mer information om användning av den här skärmen (till exempel hur ofta kända och hur länge dialogrutor används i den här skärmen).

## <a name="reporting-activities"></a>Rapportering
### <a name="user-starts-a-new-activity"></a>Användaren startar en ny aktivitet
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Du måste anropa `startActivity()` varje gång aktiviteten användarändringar. Det första anropet till funktionen startar en ny session.

### <a name="user-ends-his-current-activity"></a>Användaren avslutar sin aktuella aktiviteten
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> Du bör **aldrig** anropa den här funktionen själv, utom om du vill dela en användning av programmet till flera sessioner: ett anrop till den här funktionen skulle avslutas den aktuella sessionen omedelbart, så ett efterföljande anrop till `startActivity()` börjar en ny session. Den här funktionen kallas automatiskt av SDK när programmet stängs.
> 
> 

## <a name="reporting-events"></a>Rapporteringshändelser
### <a name="session-events"></a>Sessionshändelser
Sessionshändelser används vanligtvis för att rapportera åtgärder som utförs av en användare under sin session.

**Exempel utan extra data:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Exempel med extra data:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Fristående händelser
Strider mot Sessionshändelser, kan fristående händelser användas för en session.

**Exempel:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>Rapporterat fel
### <a name="session-errors"></a>Sessionen fel
Sessionen fel används vanligtvis för att rapportera fel som påverkar användaren under sin session.

**Exempel:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Fristående fel
Strider mot session fel, kan fristående fel användas för en session.

**Exempel:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>Rapportering av jobb
**Exempel:**

Anta att du vill rapportera varaktighet logga in:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Rapportera fel under ett jobb
Fel kan vara relaterad till ett jobb som körs i stället för som rör den aktuella användarsessionen.

**Exempel:**

Anta att du vill rapportera ett fel under din inloggningen:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Händelser under ett jobb
Händelser kan vara relaterad till ett jobb som körs i stället för som rör den aktuella användarsessionen.

**Exempel:**

Anta att vi har ett sociala nätverk och vi använder ett jobb att rapportera den totala tiden under vilken användaren är ansluten till servern. Användaren kan ta emot meddelanden från sina vänner, detta är en jobbhändelse.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>Extra parametrar
Diverse uppgifter kan kopplas till händelser, fel, aktiviteter och jobb.

Dessa data kan vara strukturerad, används Ios's NSDictionary klass.

Observera att tillägg kan innehålla `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` eller andra `NSDictionary` instanser.

> [!NOTE]
> Extraparametern serialiseras i JSON. Om du vill skicka olika objekt än de som beskrivs ovan, måste du implementera följande metod i klassen:
> 
> -(NSString*) JSONRepresentation;
> 
> Metoden måste returnera en JSON-representation av objektet.
> 
> 

### <a name="example"></a>Exempel
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Begränsningar
#### <a name="keys"></a>Nycklar
Varje nyckel i den `NSDictionary` måste överensstämma med följande reguljära uttryck:

`^[a-zA-Z][a-zA-Z_0-9]*`

Det innebär att nycklar måste börja med minst en bokstav, följt av bokstäver, siffror eller understreck (\_).

#### <a name="size"></a>Storlek
Tillägg är begränsade till **1024** tecken per anrop (efter kodning i JSON av Engagement agenten).

I exemplet ovan är JSON som skickas till servern 58 tecken:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Rapportering programinformation
Du kan manuellt rapportera spåra information (eller andra program specifik information) med hjälp av den `sendAppInfo:` funktion.

Observera att denna information kan skickas inkrementellt: endast det senaste värdet för en viss nyckel sparas för en viss enhet.

Som extra händelse, den `NSDictionary` klassen används för att abstrahera information om programmet, Observera att matriser eller underordnade ordlistor behandlas som flat strängar (med JSON-serialisering).

**Exempel:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Begränsningar
#### <a name="keys"></a>Nycklar
Varje nyckel i den `NSDictionary` måste överensstämma med följande reguljära uttryck:

`^[a-zA-Z][a-zA-Z_0-9]*`

Det innebär att nycklar måste börja med minst en bokstav, följt av bokstäver, siffror eller understreck (\_).

#### <a name="size"></a>Storlek
Information om programmet är begränsade till **1024** tecken per anrop (efter kodning i JSON av Engagement agenten).

I exemplet ovan är JSON som skickas till servern 44 tecken:

    {"birthdate":"1983-12-07","gender":"female"}
