---
title: Kvarhållning av Användaranalys för webbprogram med Azure Application Insights | Microsoft docs
description: Hur många användare tillbaka till din app?
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: bafe9579cc783dbc5c851e626e3b9bea0c559520
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Kvarhållning av Användaranalys för webbprogram med Application Insights

Funktionen kvarhållning i [Azure Application Insights](app-insights-overview.md) kan du analysera hur många användare tillbaka till din app och hur ofta de utföra vissa åtgärder eller uppnå mål. Om du kör en spel plats, kan du jämföra antalet användare som gå tillbaka till platsen efter att förlora spel med nummer som returneras efter vann. Den här kunskapen kan hjälpa dig att förbättra både användarupplevelsen och din strategi.

## <a name="get-started"></a>Kom igång

Om du ännu inte se data i verktyget kvarhållning i Application Insights-portalen [Lär dig att komma igång med verktyg för användning](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>Verktyget kvarhållning

![Kvarhållningsverktyg](./media/app-insights-usage-retention/retention.png)

1. Verktygsfältet tillåter användare att skapa nya kvarhållning rapporter, öppna den befintliga kvarhållning rapporter, spara den aktuella kvarhållning rapporten eller spara som återställa ändringar som gjorts för sparade rapporter, uppdatera data i rapporten, dela rapport via e-post eller Direktlänk och komma åt dokumentationssidan. 
2. Som standard visas alla användare som har något Kom tillbaka sedan och uppfyllde något annat under en period kvarhållning. Du kan välja en annan kombination av händelser för att begränsa fokus på specifika användaraktiviteter.
3. Lägg till ett eller flera filter på Egenskaper. Du kan fokusera på användare i ett visst land eller region. Klicka på **uppdatering** när du har ställt in filtren. 
4. Övergripande kvarhållning diagrammet visar en sammanfattning av användardokumentation mellan den valda tidsperioden. 
5. Rutnätet visar antalet användare som behålls enligt Frågeverktyget i #2. Varje rad representerar en kommittén av användare som utförde alla händelser under den tiden tidsperiod visas. Varje cell i raden visar hur många av den kommittén returneras minst en gång i en senare tid. Vissa användare kan returnera i mer än en period. 
6. Insikter kort Visa översta fem initierande händelser och översta fem returnerade händelser att ge användarna en bättre förståelse för deras kvarhållning rapporten. 

![Kvarhållning musen hovra](./media/app-insights-usage-retention/hover.png)

Användare kan hovra över celler i verktyget kvarhållning för att komma åt analytics-knappen och verktygstips förklarar vad det innebär att cellen. Knappen Analytics tar användarna till verktyget Analytics med en i förväg fråga att generera användare från cellen. 

## <a name="use-business-events-to-track-retention"></a>Använda affärshändelser för att spåra kvarhållning

Mät händelser som representerar viktiga aktiviteter för att få den mest användbara kvarhållning analysen. 

Många användare kan till exempel öppna en sida i appen utan att spela spel som visas. Spåra bara sidvisningar skulle därför innehåller en felaktig beräkning av hur många personer återgå till spel efter att använda det tidigare. Om du vill ha en tydlig bild av returnerar spelare bör appen skicka en anpassad händelse när en användare spelar faktiskt.  

Det är bra att Platskod anpassade händelser som representerar viktiga åtgärder, och använda dem för kvarhållning analyser. Om du vill samla in resultatet av denna spel, måste du skriva en rad med kod för att skicka en anpassad händelse till Application Insights. Om du skriver i koden för webbsidan eller Node.JS, ser ut så här:

```JavaScript
    appinsights.trackEvent("won game");
```

Eller i serverkoden för ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Mer information om anpassade händelser skrivs](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Nästa steg
- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du skickar redan anpassade händelser eller sidvisningar, utforska verktygen användning om du vill veta hur användarna använder din tjänst.
    - [Användare, sessioner, händelser](app-insights-usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Användarflöden](app-insights-usage-flows.md)
    - [Arbetsböcker](app-insights-usage-workbooks.md)
    - [Lägg till användarkontext](app-insights-usage-send-user-context.md)


