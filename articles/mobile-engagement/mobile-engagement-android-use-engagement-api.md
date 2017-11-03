---
title: "Hur du använder Engagement API på Android"
description: "Senaste Android SDK - hur du använder Engagement API på Android"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 09b62659-82ae-4a55-8784-fca0b6b22eaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: d353cd2fe47c54a0282cc5bb1b22b4a56e0cd82c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-android"></a>Hur du använder Engagement API på Android
Det här dokumentet är ett tillägg till dokumentet [Reporting avancerade alternativ för Android Mobile Engagement SDK](mobile-engagement-android-advanced-reporting.md). Det ger i djup information om hur du använder Engagement API för att rapportera programmet-statistik.

Kom ihåg att om du bara vill Engagement att rapportera programmets sessioner, aktiviteter, krascher och teknisk information sedan det enklaste sättet är att se alla dina `Activity` underordnade klasser ärver från motsvarande `EngagementActivity` klass.

Om du vill göra mer, till exempel om du behöver rapportera programmet specifika händelser, fel och jobb, eller om du vill rapportera ditt programs aktiviteter i ett annat sätt än den som implementerats i den `EngagementActivity` klasser, måste du använda Engagement-API.

Engagement-API som tillhandahålls av den `EngagementAgent` klass. En instans av den här klassen kan hämtas genom att anropa den `EngagementAgent.getInstance(Context)` statisk metod (Observera att den `EngagementAgent` objektet som returnerades är en singleton).

## <a name="engagement-concepts"></a>Koncept i engagement
Följande delar förfina vanliga [koncept i Mobile Engagement](mobile-engagement-concepts.md), för Android-plattformen.

### <a name="session-and-activity"></a>`Session` och `Activity`
Om användaren är fler än några få sekunder inaktiv mellan två *aktiviteter*, sedan hans sekvens av *aktiviteter* är uppdelad i två olika *sessioner*. Dessa några sekunder kallas ”sessionstidsgränsen”.

En *aktiviteten* är ofta kopplade till en skärm för programmet, det vill säga den *aktiviteten* när skärmen visas och stoppas när skärmen stängs: så är fallet när Engagement-SDK är integrerad med hjälp av den `EngagementActivity` klasser.

Men *aktiviteter* kan också kontrolleras manuellt med hjälp av Engagement-API. Detta gör för att dela en viss skärm i flera sub delar för att få mer information om användning av den här skärmen (till exempel hur ofta kända och hur länge dialogrutor används i den här skärmen).

## <a name="reporting-activities"></a>Rapportering
> [!IMPORTANT]
> Du behöver inte rapporten aktiviteter som beskrivs i det här avsnittet om du använder den `EngagementActivity` klassen och dess varianter enligt beskrivningen i hur du integrerar Engagement för Android-dokument.
> 
> 

### <a name="user-starts-a-new-activity"></a>Användaren startar en ny aktivitet
            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Du måste anropa `startActivity()` varje gång aktiviteten användarändringar. Det första anropet till funktionen startar en ny session.

Den bästa platsen för att anropa den här funktionen finns på varje aktivitet `onResume` återanrop.

### <a name="user-ends-his-current-activity"></a>Användaren avslutar sin aktuella aktiviteten
            EngagementAgent.getInstance(this).endActivity();

Du måste anropa `endActivity()` minst en gång när användaren är klar användarens sista aktivitet. Det informerar Engagement SDK att användaren är för närvarande inaktiv och användarsessionen måste stängas när tidsgränsen för sessionen upphör att gälla (om du anropar `startActivity()` innan tidsgränsen för sessionen upphör sessionen bara återupptas).

Den bästa platsen för att anropa den här funktionen finns på varje aktivitet `onPause` återanrop.

## <a name="reporting-events"></a>Rapporteringshändelser
### <a name="session-events"></a>Sessionshändelser
Sessionshändelser används vanligtvis för att rapportera åtgärder som utförs av en användare under sin session.

**Exempel utan extra data:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Exempel med extra data:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Fristående händelser
Strider mot Sessionshändelser, kan det ske fristående händelser utanför ramen för en session.

**Exempel:**

Anta att du vill rapporten händelser som inträffar när en broadcast mottagare utlöses:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

## <a name="reporting-errors"></a>Rapporterat fel
### <a name="session-errors"></a>Sessionen fel
Sessionen fel används vanligtvis för att rapportera fel som påverkar användaren under sin session.

**Exempel:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Fristående fel
Strider mot session fel inträffa fristående fel utanför ramen för en session.

**Exempel:**

I följande exempel visas hur du rapporterar ett fel när minnet blir låg på telefonen medan programmet processen körs.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

## <a name="reporting-jobs"></a>Rapportering av jobb
### <a name="example"></a>Exempel
Anta att du vill rapportera varaktighet logga in:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Rapportera fel under ett jobb
Fel kan vara relaterad till ett jobb som körs i stället för som rör den aktuella användarsessionen.

**Exempel:**

Anta att du vill rapportera ett fel under du logga in processen:

[...] offentliga void inloggning (kontexten kontext,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Rapporteringshändelser under ett jobb
Händelser kan vara relaterad till ett jobb som körs i stället för som rör den aktuella användarsessionen.

**Exempel:**

Anta att vi har ett sociala nätverk och vi använder ett jobb att rapportera den totala tiden under vilken användaren är ansluten till servern. Användaren kan vara ansluten i bakgrunden även när han använder ett annat program eller när telefonen är i viloläge, så det finns ingen session.

Användaren kan ta emot meddelanden från sina vänner, detta är en jobbhändelse.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

## <a name="extra-parameters"></a>Extra parametrar
Diverse uppgifter kan kopplas till händelser, fel, aktiviteter och jobb.

Dessa data kan vara strukturerad, används Androids paket klass (faktiskt, den fungerar som extra parametrar i Android avsikter). Observera att ett paket kan innehålla matriser eller ett annat paket instanser.

> [!IMPORTANT]
> Om du lägger till i parcelable eller serializable parametrar, kontrollerar du att deras `toString()` metoden implementeras för att returnera en läsbar sträng. Serialiserbara klasser som innehåller icke tillfälligt fält som inte kan serialiseras gör Android kraschar när du anropar`bundle.putSerializable("key",value);`
> 
> [!WARNING]
> Glesa matriser i extra parametrar stöds inte, det vill säga den kommer inte att serialisera som en matris. Du måste konvertera dem till standard matriser innan den används i extra parametrar.
> 
> 

### <a name="example"></a>Exempel
            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Begränsningar
#### <a name="keys"></a>Nycklar
Varje nyckel i den `Bundle` måste överensstämma med följande reguljära uttryck:

`^[a-zA-Z][a-zA-Z_0-9]*`

Det innebär att nycklar måste börja med minst en bokstav, följt av bokstäver, siffror eller understreck (\_).

#### <a name="size"></a>Storlek
Tillägg är begränsade till **1024** tecken per anrop (efter kodning i JSON av tjänsten Engagement).

I exemplet ovan är JSON som skickas till servern 58 tecken:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Rapportering programinformation
Du kan manuellt rapportera spåra information (eller andra program specifik information) med hjälp av den `sendAppInfo()` funktion.

Observera att denna information kan skickas inkrementellt: endast det senaste värdet för en viss nyckel sparas för en viss enhet.

Som händelsen tillägg används paket klassen för att abstrahera information om programmet, Observera att matriser eller underordnade paket kommer att behandlas som flat strängar (med JSON-serialisering).

### <a name="example"></a>Exempel
Här är ett kodexempel för att skicka användaren kön och födelsedatum:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Begränsningar
#### <a name="keys"></a>Nycklar
Varje nyckel i den `Bundle` måste överensstämma med följande reguljära uttryck:

`^[a-zA-Z][a-zA-Z_0-9]*`

Det innebär att nycklar måste börja med minst en bokstav, följt av bokstäver, siffror eller understreck (\_).

#### <a name="size"></a>Storlek
Information om programmet är begränsade till **1024** tecken per anrop (efter kodning i JSON av tjänsten Engagement).

I exemplet ovan är JSON som skickas till servern 44 tecken:

            {"expiration":"2016-12-07","status":"premium"}
