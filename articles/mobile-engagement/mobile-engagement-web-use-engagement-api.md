---
title: 'Azure Mobile Engagement Web API: er SDK | Microsoft Docs'
description: "De senaste uppdateringarna och procedurer för webbtjänst-SDK för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 54c22ce6a03e382b1bbde102bccc97deec249b30
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Använd Azure Mobile Engagement-API: et i ett webbprogram
Det här dokumentet är ett tillägg till det dokument som får du veta hur till [Mobile Engagement ska integreras i ett webbprogram](mobile-engagement-web-integrate-engagement.md). Det ger detaljerad information om hur du använder Azure Mobile Engagement-API: et för att rapportera programmet-statistik.

Mobile Engagement-API som tillhandahålls av den `engagement.agent` objekt. Standard Azure Mobile Engagement Web SDK alias är `engagement`. Du kan ändra detta alias från SDK-konfigurationen.

## <a name="mobile-engagement-concepts"></a>Koncept i Mobile Engagement
Följande delar förfina vanliga [koncept i Mobile Engagement](mobile-engagement-concepts.md) för webbplattformen.

### <a name="session-and-activity"></a>`Session` och `Activity`
Om användaren är inaktiv i mer än ett par sekunder mellan två aktiviteter, är användarens sekvensen av aktiviteter uppdelad i två olika sessioner. Dessa några sekunder kallas tidsgränsen för sessionen.

Om ditt webbprogram inte deklarera slutet av användaraktiviteter ensamt (genom att anropa den `engagement.agent.endActivity` funktionen), Mobile Engagement-servern automatiskt upphör att gälla inom tre minuter efter appen på sidan stängs sessionen. Detta kallas för sessionstidsgränsen server.

### `Crash`
Automatisk rapporter om undantagsfel utan felhantering JavaScript skapas inte som standard. Dock kan du rapportera krascher manuellt med hjälp av den `sendCrash` fungera (se avsnittet reporting krascher).

## <a name="reporting-activities"></a>Rapportering
Rapportering av användaraktivitet inkluderar när en användare startar en ny aktivitet och när användaren slutar den aktuella aktiviteten.

### <a name="user-starts-a-new-activity"></a>Användaren startar en ny aktivitet
    engagement.agent.startActivity("MyUserActivity");

Du måste anropa `startActivity()` varje gång användaraktivitet ändras. Det första anropet till funktionen startar en ny session.

### <a name="user-ends-the-current-activity"></a>Användaren slutar den aktuella aktiviteten
    engagement.agent.endActivity();

Du måste anropa `endActivity()` minst en gång när användaren är klar deras senaste aktivitet. Det informerar Mobile Engagement Web SDK att användaren är för närvarande inaktiv och att användarsessionen måste stängas när tidsgränsen för sessionen upphör att gälla. Om du anropar `startActivity()` innan tidsgränsen för sessionen upphör sessionen bara återupptas.

Eftersom det finns ingen tillförlitlig anrop för när navigator fönstret stängs, är det ofta svårt eller omöjligt att fånga slutet av användaraktiviteter inuti en webbmiljö. Det är därför Mobile Engagement-servern automatiskt upphör att gälla inom tre minuter efter appen på sidan stängs sessionen.

## <a name="reporting-events"></a>Rapporteringshändelser
Rapportering av händelser som täcker Sessionshändelser och fristående händelser.

### <a name="session-events"></a>Sessionshändelser
Sessionshändelser används normalt att rapportera åtgärder som utförs av en användare under användarens session.

**Exempel utan extra data:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Exempel med extra data:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Fristående händelser
Till skillnad från Sessionshändelser, kan det ske fristående händelser utanför en session.

För att använda ``engagement.agent.sendEvent`` i stället för ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Rapporterat fel
Rapportering om felen omfattar session fel och fristående fel.

### <a name="session-errors"></a>Sessionen fel
Sessionen fel används oftast för att rapportera fel som kan påverkar användaren under användarens session.

**Exempel utan extra data:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Exempel med extra data:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Fristående fel
Till skillnad från sessionen fel inträffa fristående fel utanför en session.

För att använda `engagement.agent.sendError` i stället för `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Rapportering av jobb
Rapportering av jobb omfattar rapportera fel och händelser som inträffar när ett jobb och rapportering av krascher.

**Exempel:**

Om du vill övervaka en AJAX-begäran, använder du följande:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Rapporterat fel under ett jobb
Fel kan vara relaterad till ett jobb som körs i stället för att den aktuella användarsessionen.

**Exempel:**

Om du vill rapportera ett fel om en begäran om AJAX misslyckas:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Rapporteringshändelser under ett jobb
Händelser kan vara relaterad till ett jobb som körs i stället för att den aktuella användarsessionen tack till den `engagement.agent.sendJobEvent` funktion.

Den här funktionen fungerar på samma sätt som `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Rapportering krascher
Använd den `sendCrash` funktionen rapporten kraschar manuellt.

Den `crashid` argumentet är en sträng som identifierar typ av krascher.
Den `crash` argumentet är vanligtvis stackspårning av krascher som en sträng.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Extra parametrar
Du kan koppla godtyckliga data till en händelse, fel, aktiviteter eller jobb.

Data kan vara ett JSON-objekt (men inte en matris eller primitiv typ).

**Exempel:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Begränsningar
Gränser som gäller för extra parametrar har områden i reguljära uttryck för nycklar, värdetyper och storlek.

#### <a name="keys"></a>Nycklar
Varje nyckel i objektet måste matcha följande reguljära uttryck:

    ^[a-zA-Z][a-zA-Z_0-9]*

Detta innebär att nycklar måste börja med minst en bokstav följt av bokstäver, siffror eller understreck (\_).

#### <a name="values"></a>Värden
Värden är begränsade till sträng, siffra och booleskt typer.

#### <a name="size"></a>Storlek
Tillägg är begränsade till 1 024 tecken per anrop (efter Mobile Engagement Web SDK kodar den i JSON).

## <a name="reporting-application-information"></a>Rapportering programinformation
Du kan manuellt rapportera spåra information (eller programspecifik information) med hjälp av den `sendAppInfo()` funktion.

Observera att denna information kan skickas inkrementellt. Det senaste värdet för en viss nyckel sparas för en specifik enhet.

Du kan använda en JSON-objekt för att abstrahera information om programmet som händelsen tillägg. Observera att matriser eller underordnade objekt behandlas som flat strängar (med JSON-serialisering).

**Exempel:**

Här följer ett exempel på koden för att skicka användarens kön och födelsedatum:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Begränsningar
Gränserna som gäller för information om programmet är i områden som reguljära uttryck för nycklar och storlek.

#### <a name="keys"></a>Nycklar
Varje nyckel i objektet måste matcha följande reguljära uttryck:

    ^[a-zA-Z][a-zA-Z_0-9]*

Detta innebär att nycklar måste börja med minst en bokstav följt av bokstäver, siffror eller understreck (\_).

#### <a name="size"></a>Storlek
Information om programmet är begränsat till 1 024 tecken per anrop (efter Mobile Engagement Web SDK kodar den i JSON).

I föregående exempel är JSON som skickas till servern 44 tecken:

    {"birthdate":"1983-12-07","gender":"female"}
