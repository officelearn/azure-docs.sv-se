---
title: Azure Application Insights användning inverkan | Microsoft docs
description: Analysera hur olika egenskaper potentiellt påverka konverteringstakt för delar av dina appar.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/25/2018
ms.reviewer: daviste
ms.author: mbullwin
ms.openlocfilehash: 9188776fdd213f01523069b08bd898f48bee57a4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648476"
---
# <a name="impact-analysis-with-application-insights"></a>Konsekvensanalys med Application Insights

Påverkan analyserar hur inläsningstider och andra egenskaper påverkar konverteringstakt för olika delar av appen. Om du vill placera mer exakt som identifieras hur **någon annan dimension** av en **Sidvisning**, **anpassad händelse**, eller **begäran** påverkar användningen av en olika **Sidvisning** eller **anpassad händelse**. 

![Påverkan verktyget](./media/app-insights-usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Fortfarande osäker vad inverkan gör?

Ett sätt att tänka på påverkan är som ultimate verktyg för att reglera argument med någon i teamet om hur långsamhet i någon aspekt av webbplatsen påverkar om användare behålla. Även om användarna kan tolerera fel på en viss mängd långsamhet, ger påverkan inblick i hur detta bäst att balansera optimering och prestanda för att maximera användarkonvertering.

Men det är bara en delmängd av påverkans funktioner att analysera prestanda. Eftersom påverkan har stöd för anpassade händelser och dimensioner, är svar på frågor som hur användarens webbläsare val korrelera med olika växelkurser bara några få klick.

![Skärmbild konvertering av webbläsare](./media/app-insights-usage-impact/0004-browsers.png)

> [!NOTE]
> Application Insights-resursen måste innehålla sidvisningar eller anpassade händelser och använder verktyget påverkan. [Lär dig hur du ställer in din app för att samla in sidvisningar automatiskt med Application Insights JavaScript SDK](app-insights-javascript.md). Också tänka på att eftersom du analyserar så kommer exempel storlek är viktig.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Sidinläsningstiden påverkar hur många personer konvertera på sidan?

Välj en inledande Sidvisning, anpassad händelse eller begäran om du vill börja svara på frågor med verktyget påverkan.

![Påverkan verktyget](./media/app-insights-usage-impact/0002-dropdown.png)

1. Välj en Sidvisning från den **för sidvisning** listrutan.
2. Lämna den **analysera hur dess** listmenyerna på standardinställningen **varaktighet** (i det här sammanhanget **varaktighet** är ett alias för **sidinläsningstiden**.)
3. För den **påverkar användningen av** listrutan, Välj en anpassad händelse. Den här händelsen ska motsvara ett UI-element i vyn sidan som du valde i steg 1.

![Skärmbild av resultat](./media/app-insights-usage-impact/0003-results.png)

I den här instansen som **produktsidan** inläsningstid ökar konverteringstakt till **Köp produkt klickade** kraschar. Baserat på distributionsplatsdatorn ovan, kan en optimal sidan belastningen varaktighet 3,5 sekunder riktas för att uppnå en potentiell 55% konverteringsgraden. Ytterligare prestandaförbättringar för att minska inläsningstid nedan 3,5 sekunder för närvarande inte stämmer med ytterligare konvertering fördelar.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Vad händer om jag spåra sidvisningar eller läsa in gånger på anpassade sätt?

Påverkan har stöd för både standardentiteter och anpassade egenskaper och mätningar av. Använda vad du vill. I stället för kommer att använda filter på de primära och sekundära händelserna för att bli lite mer specifika.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Konverterar användare från olika länder eller regioner med olika hastigheter?

1. Välj en Sidvisning från den **för sidvisning** listrutan.
2. Välj ”land eller region” i **analysera hur dess** listrutan
3. För den **påverkar användningen av** listrutan en anpassad händelse som motsvarar ett UI-element på sidan vyn du valde i steg 1.

I det här fallet resultatet inte längre får plats i en kontinuerlig x-axel-modell som de gjorde i det första exemplet. I stället visas en visualisering som liknar en segmenterade tratt. Sortera efter **användning** att visa variationen för konvertering till din anpassade händelse baserat på land.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hur beräknas priserna konvertering av verktyget inverkan?

Under huven, verktyget inverkan förlitar sig på [Pearson korrelationskoefficienten] (https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Resultaten beräknas mellan 1 och 1 med -1 som representerar noll Korrelations- och 1 som motsvarar en positiv korrelation.

Grundläggande uppdelning av hur konsekvensanalys fungerar är följande:

Låt _A_ = huvudsidan vy/anpassat event/begäran du väljer i den första listrutan. (**För sidvisning**).

Låt _B_ = sekundära sidan Visa/anpassad händelse som du väljer (**påverkar användningen av**).

Påverkan tittar på ett sampel från alla sessioner från användare i det valda tidsintervallet. För varje session den letar efter varje förekomst av _A_.

Sessioner delas sedan upp i två olika typer av _subsessions_ baserat på en av två villkor:

- En konverterade undersession består av en session som slutar med en _B_ händelse och innehåller alla _A_ händelser som inträffar före _B_.
- En inte är konverterat subsession inträffar när alla _A_användarens sker utan en terminal _B_.

Hur beräknas slutligen inverkan varierar beroende på om vi analyserar av mått eller per dimension. För alla mått _A_är en undersession är ett genomsnitt. Medan dimensions värdet för varje _A_ bidrar _1/N_ att värdet som tilldelas _B_ där _N_ är antalet _A_är i undersessionen.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan skicka anpassade händelser eller sidvisningar, utforska användningsverktygen om du vill veta hur användarna använder din tjänst.
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](app-insights-usage-retention.md)
    - [Användarflöden](app-insights-usage-flows.md)
    - [Arbetsböcker](app-insights-usage-workbooks.md)
    - [Lägg till användarkontext](app-insights-usage-send-user-context.md)