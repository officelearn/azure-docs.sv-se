---
title: Användningseffekt för Azure-programinsikter | Microsoft-dokument
description: Analysera hur olika egenskaper kan påverka konverteringsfrekvensen för delar av dina appar.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 787221c4df3f06029d19ee779a28bb763723f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671043"
---
# <a name="impact-analysis-with-application-insights"></a>Konsekvensanalys med Application Insights

Impact analyserar hur laddningstider och andra egenskaper påverkar konverteringsfrekvensen för olika delar av appen. För att uttrycka det mer exakt, upptäcker det hur **någon dimension** i en **sidvy,** **anpassad händelse**eller **begäran** påverkar användningen av en annan **sidvy** eller **anpassad händelse**. 

![Verktyg för påverkan](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Fortfarande inte säker på vad Impact gör?

Ett sätt att tänka på Impact är som det ultimata verktyget för att lösa argument med någon i ditt team om hur långsam i någon aspekt av din webbplats påverkar om användarna stannar kvar. Användare kan tolerera en viss långsamhet, men Impact ger dig insikt i hur du bäst balanserar optimering och prestanda för att maximera användarkonverteringen.

Men att analysera prestanda är bara en delmängd av Impact kapacitet. Eftersom Impact stöder anpassade händelser och dimensioner, svara på frågor som hur användaren webbläsare val korrelerar med olika omvandlingshastigheter är bara några klick bort.

![Konvertering av skärmbild efter webbläsare](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Application Insights-resursen måste innehålla sidvisningar eller anpassade händelser för att kunna använda verktyget Påverkan. [Lär dig hur du konfigurerar appen så att den samlar in sidvisningar automatiskt med JavaScript-SDK för programinsikter](../../azure-monitor/app/javascript.md). Tänk också på att eftersom du analyserar korrelation, provstorlek frågor.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Påverkar sidans inläsningstid hur många personer som konverterar på min sida?

Om du vill börja svara på frågor med verktyget Impact väljer du en första sidvy, en anpassad händelse eller en begäran.

![Verktyg för påverkan](./media/usage-impact/0002-dropdown.png)

1. Välj en sidvy i listrutan **För sidvyn.**
2. Lämna **analysrutan hur listrutan** på standardvalet **av Varaktighet** (I den här **kontexten** varaktighet är ett alias för **sidans inläsningstid**.)
3. Välj en anpassad händelse för effekterna av rullgardinsmenyn. **impacts the usage of** Den här händelsen bör motsvara ett gränssnittselement i sidvyn som du valde i steg 1.

![Skärmbild av resultat](./media/usage-impact/0003-results.png)

I det här fallet som **Produktsida** inläsningstid ökar omvandlingsfrekvensen till **Inköp produkt klickade** går ner. Baserat på fördelningen ovan kan en optimal sidladdningstid på 3,5 sekunder riktas för att uppnå en potentiell omvandlingsfrekvens på 55 %. Ytterligare prestandaförbättringar för att minska laddningstiden under 3,5 sekunder korrelerar för närvarande inte med ytterligare konverteringsfördelar.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Vad händer om jag spårar sidvisningar eller laddningstider på anpassade sätt?

Impact stöder både standardegenskaper och anpassade egenskaper och mätningar. Använd vad du vill. I stället för varaktighet använder du filter på de primära och sekundära händelserna för att få mer specifika.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Konverterar användare från olika länder eller regioner till olika priser?

1. Välj en sidvy i listrutan **För sidvyn.**
2. Välj "Land eller region" för **att analysera hur dess** listruta
3. För **effekterna av användningen av** listrutan väljer du en anpassad händelse som motsvarar ett gränssnittselement i sidvyn som du valde i steg 1.

I det här fallet passar resultaten inte längre in i en kontinuerlig x-axelmodell som de gjorde i det första exemplet. I stället presenteras en visualisering som liknar en segmenterad tratt. Sortera **efter användning** om du vill visa varianten av konvertering till din anpassade händelse baserat på land/region.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hur beräknar impact-verktyget dessa omräkningskurser?

Under huven förlitar sig impact-verktyget på [Pearsons korrelationskoefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Resultaten beräknas mellan -1 och 1 med -1 som representerar en negativ linjär korrelation och 1 som representerar en positiv linjär korrelation.

Den grundläggande uppdelningen av hur konsekvensanalys fungerar är följande:

Låt _A_ = huvudsidan visa / anpassad händelse / begäran du väljer i den första listrutan. (För**sidvyn).**

Låt _B_ = den sekundära sidvisning/anpassade händelse som du väljer (**påverkar användningen av**).

Effekt tittar på ett exempel på alla sessioner från användare i det valda tidsintervallet. För varje session letar den efter varje förekomst av _A_.

Sessioner delas sedan in i två olika typer av _undersessioner_ baserat på ett av två villkor:

- En konverterad undersession består av en session som slutar med en _B-händelse_ och omfattar alla _A-händelser_ som inträffar före _B_.
- En okonverterad undersession inträffar när alla _A:er_inträffar utan terminal _B_.

Hur effekten beräknas i slutändan varierar beroende på om vi analyserar efter mått eller dimension. För mått _A_är alla A:er i en undersession i genomsnitt. För dimensioner bidrar värdet för varje _A_ _1/N_ till det värde som tilldelats _B_ där _N_ är antalet _A_i undersessionen.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera användningsupplevelser börjar du skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan har skickat anpassade händelser eller sidvisningar kan du utforska användningsverktygen för att ta reda på hur användarna använder tjänsten.
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](usage-retention.md)
    - [Användarflöden](usage-flows.md)
    - [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
    - [Lägga till användarkontext](usage-send-user-context.md)
