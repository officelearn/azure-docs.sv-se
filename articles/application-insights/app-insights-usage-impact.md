---
title: "Azure Application Insights användning påverkan | Microsoft docs"
description: "Analysera hur olika egenskaper potentiellt påverka konvertering priser för delar av dina appar."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/25/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: d76db02647ce878343f60fc84cf063c5b7833438
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="impact-analysis-with-application-insights"></a>Konsekvensanalys med Application Insights

Påverkan analyserar hur laddningstider och andra egenskaper påverkar konvertering priser för olika delar av din app. För att placera mer exakt den upptäcker hur **någon dimension** av en **sidvy**, **anpassade händelsen**, eller **begäran** påverkar användningen av en olika **sidvy** eller **anpassade händelsen**. 

![Påverkan verktyget](./media/app-insights-usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Fortfarande inte vet vad påverkan gör?

Ett sätt att tänka på inverkan är som det slutgiltiga verktyget för att reglera argument med någon i gruppen om hur påverkar långsamt arbete i någon del av din webbplats om användarna behålla användarna. När användarna kan tolerera mängden långsamt arbete, ger påverkan insyn i hur bäst att balansera optimering och prestanda för att maximera användaren konvertering.

Men Analysera prestanda är bara en delmängd av påverkans funktioner. Eftersom påverkan stöder anpassade händelser och dimensioner, är bara några få klick bort svar på frågor som hur användare av webbläsare korrelera med olika hastigheter för konvertering.

![Skärmbild konvertering av webbläsare](./media/app-insights-usage-impact/0004-browsers.png)

> [!NOTE]
> Application Insights-resursen måste innehålla sidvisningar eller anpassade händelser du använder verktyget påverkan. [Lär dig att konfigurera din app att samla in sidvisningar automatiskt med Application Insights JavaScript SDK](app-insights-javascript.md). Tänk också på att eftersom du analyserar korrelation, exempel storlek frågor.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Är sidinläsningstiden påverkar hur många personer konvertera på sidan?

För att börja besvara frågor med verktyget påverkan, väljer du ett första sidan Visa, anpassade händelsen eller begäran.

![Påverkan verktyget](./media/app-insights-usage-impact/0002-dropdown.png)

1. Välj en sida från den **för vyn sida** listrutan.
2. Lämna den **analysera hur dess** listrutan på standardvalet **varaktighet** (i det här sammanhanget **varaktighet** är ett alias för **sidinläsningstiden**.)
3. För den **påverkar användningen av** listrutan väljer du en anpassad händelse. Den här händelsen ska motsvara ett UI-element i vyn sida som du valde i steg 1.

![Skärmbild av resultat](./media/app-insights-usage-impact/0003-results.png)

I den här instansen som **produktsidan** inläsningstid ökar konverteringstakt till **Köp produkt klickade på** kraschar. Baserat på distributionsplatsen ovan, kan en optimal sidan belastningen varaktighet 3.5 sekunder vara mål för att uppnå en potentiell 55% konverteringstakt. Ytterligare prestandaförbättringar för att minska inläsningstiden nedan 3.5 sekunder för närvarande inte korrelerar med ytterligare konvertering fördelar.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Vad händer om jag spåra sidvisningar eller läsa in gånger på anpassade sätt?

Påverkan stöder både standard- och anpassade egenskaper och mått. Använd vad du vill. I stället för varaktighet, använda filter på de primära och sekundära händelserna för att få mer specifik.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Konverterar användare från olika länder eller regioner med olika hastigheter?

1. Välj en sida från den **för vyn sida** listrutan.
2. Välj ”land eller region” i **analysera hur dess** listrutan
3. För den **påverkar användningen av** listrutan en anpassad händelse som motsvarar ett UI-element i vyn sida du valde i steg 1.

I det här fallet resultatet inte längre passar in i en modell för kontinuerlig x-axeln som de gjorde i det första exemplet. I stället visas en liknande segmenterade Trattens visualisering. Sortera efter **användning** att visa variationen för konvertering till din anpassade händelse baserat på land.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hur beräknas dessa priser för konvertering av verktyget påverkan?

Under huven, verktyget inverkan som förlitar sig på [kvadratvärdet korrelationen] (https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Resultaten beräknas mellan 1 och 1 med -1 som representerar noll korrelation och 1 som motsvarar en positiv korrelation.

Grundläggande fördelningen av hur konsekvensanalys fungerar är följande:

Låt _A_ = huvudsidan vy/custom event/begäran du väljer i den första nedrullningsbara listrutan. (**För vyn sida**).

Låt _B_ = sekundär sidan Visa/anpassade händelsen du väljer (**påverkar användningen av**).

Påverkan tittar på ett sampel från alla sessioner från användare i det valda tidsintervallet. För varje session, det ser ut för varje förekomst av _A_.

Sessioner delas sedan upp i två olika typer av _subsessions_ baserat på en av två villkor:

- En konverterade undersession består av en session som slutar med en _B_ händelse och omfattar alla _A_ händelser som inträffar före _B_.
- En subsession inträffar när alla _A_har sker utan en terminal _B_.

Hur beräknas slutligen påverkan varierar beroende på om vi analyserar genom mått eller dimension. För alla mätvärden _A_har en undersession är ett genomsnitt. Medan dimensions värdet för varje _A_ bidrar _1/N_ att värdet som tilldelas _B_ där _N_ är antalet _A_är i undersessionen.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du skickar redan anpassade händelser eller sidvisningar, utforska verktygen användning om du vill veta hur användarna använder din tjänst.
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](app-insights-usage-retention.md)
    - [Användarflöden](app-insights-usage-flows.md)
    - [Arbetsböcker](app-insights-usage-workbooks.md)
    - [Lägg till användarkontext](app-insights-usage-send-user-context.md)