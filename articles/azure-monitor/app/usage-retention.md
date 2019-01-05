---
title: Kvarhållning av Användaranalys för webbprogram med Azure Application Insights | Microsoft docs
description: Hur många användare som återvänder till din app?
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/03/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 4b21632f936093c0a48abf2d13389f22306045cc
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052912"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Kvarhållning av Användaranalys för webbprogram med Application Insights

Funktionen kvarhållning i [Azure Application Insights](../../application-insights/app-insights-overview.md) kan du analysera hur många användare som återvänder till din app och hur ofta de utföra vissa åtgärder eller uppnå mål. Om du kör en game plats, kan du jämföra antalet användare som gå tillbaka till platsen efter att förlora ett spel med antalet som returnerar efter vann. Den här kunskapen kan hjälpa dig att förbättra både din användarupplevelse och din strategi.

## <a name="get-started"></a>Kom igång

Om du ännu inte visas data i verktyget kvarhållning i Application Insights-portalen [Lär dig hur du kommer igång med verktyg för användning](usage-overview.md).

## <a name="the-retention-tool"></a>Kvarhållnings-verktyget

![Kvarhållningsverktyg](./media/usage-retention/retention.png)

1. I verktygsfältet kan du skapa nya rapporter för kvarhållning, öppna befintliga kvarhållning rapporter, spara den aktuella kvarhållning rapporten eller spara som, återställa ändringar som gjorts i sparade rapporter, uppdatera data i rapporten, dela rapport via e-post eller Direktlänk och få åtkomst till dokumentationen sidan. 
2. Kvarhållning visas alla användare som gjort något sedan kom tillbaka och gjorde något annat under en period som standard. Du kan välja olika kombinationer av händelser för att begränsa fokus på specifika användaraktiviteter.
3. Lägg till ett eller flera filter på Egenskaper. Exempelvis kan du fokusera på användare i ett visst land eller region. Klicka på **uppdatering** när du har angett filtren. 
4. Övergripande kvarhållningsdiagram visar en sammanfattning av lagringstid för användare i den valda tidsperioden. 
5. Rutnätet visar antalet användare som behålls enligt Frågeverktyget i #2. Varje rad motsvarar en kohort för användare som utförde en händelse i den tid som tidsperiod visas. Varje cell i raden visar hur många av den kohorten returneras minst en gång i en senare period. Vissa användare kan returnera i mer än en period. 
6. Insights-korten visar översta fem initierande händelser och översta fem returnerade händelser att ge användarna en bättre förståelse för deras kvarhållning rapporten. 

![Kvarhållning muspekaren](./media/usage-retention/hover.png)

Användarna kan hovra över celler i kvarhållnings-verktyget för att få åtkomst till analytics-knappen och verktygstips förklarar vad cellen innebär. Knappen Analytics tar användarna till analysverktyg med en i förväg fråga generera användare från cellen. 

## <a name="use-business-events-to-track-retention"></a>Använda affärshändelser för att spåra kvarhållning

För att få den mest användbara kvarhållning analysen kan du mäta händelser som representerar viktiga aktiviteter. 

Många användare kan till exempel öppna en sida i din app utan spelar spel som visas. Spåra bara sidvisningar skulle därför tillhandahålla en felaktig uppskattning av hur många som returneras för att spela upp spelet när du har haft nytta av den tidigare. Om du vill ha en tydlig bild av returnerar spelare, ska din app skicka en anpassad händelse när en användare faktiskt spelas upp.  

Det är bra att koda anpassade händelser som representerar viktiga åtgärder och använda dem för din analys för kvarhållning. Om du vill samla in spel resultatet, måste du skriva en rad med kod för att skicka en anpassad händelse till Application Insights. Om du skriver i webbsidans kod eller i Node.JS, ser ut så här:

```JavaScript
    appinsights.trackEvent("won game");
```

Eller i ASP.NET server-kod:

```csharp
   telemetry.TrackEvent("won game");
```

[Läs mer om hur du skriver anpassade händelser](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Nästa steg
- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan skicka anpassade händelser eller sidvisningar, utforska användningsverktygen om du vill veta hur användarna använder din tjänst.
    - [Användare, sessioner, händelser](usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Användarflöden](usage-flows.md)
    - [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
    - [Lägg till användarkontext](usage-send-user-context.md)


