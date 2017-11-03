---
title: "Analytics - kraftfulla Sök och Frågeverktyg av Azure Application Insights | Microsoft Docs"
description: "Översikt över Analytics, kraftfullt diagnostiska sökverktyg av Application Insights. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 80a9e248ca50c11ef61a5c50c4986c4f8f4ead9d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="analytics-in-application-insights"></a>Analyser i Application Insights
Analytics är kraftfulla Sök- och verktyg för [Programinsikter](app-insights-overview.md). Analytics är ett Webbverktyg så att inga inställningar krävs. Om du redan har konfigurerat Application Insights för en av dina appar så att du kan analysera data i din app genom att öppna Analytics från din app [översikt bladet](app-insights-dashboards.md).

![Öppna portal.azure.com, öppna Application Insights-resursen och klicka på Analytics.](./media/app-insights-analytics/001.png)

Du kan också använda den [Analytics playground](https://go.microsoft.com/fwlink/?linkid=859557) som är en kostnadsfri demomiljö med mycket exempeldata.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Fråga efter data i Analytics
En typisk fråga börjar med ett tabellnamn följt av en serie *operatörer* avgränsade med `|`.
Till exempel ska vi ta reda på hur många förfrågningar vår app togs emot från olika länder under de senaste 3 timmarna:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Vi börjar med namnet på tabellen *begäranden* och lägga till via rörledningar element efter behov.  Först definierar vi ett tidsfilter om du vill granska bara poster från de senaste 3 timmarna.
Vi sedan räkna antalet poster per land (att data som hittas i kolumnen *client_CountryOrRegion*). Slutligen återge vi resultatet i ett cirkeldiagram.
<br>

![Frågeresultat](./media/app-insights-analytics/030.png)

Språket har många bra funktioner:

* [Filter](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) dina rådata app telemetri av alla fält, inklusive din anpassade egenskaper och mått.
* [Anslut](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) flera tabeller – korrelera begäranden med sidvisningar, beroendeanrop, undantag och loggspårningar.
* Kraftfulla statistiska [aggregeringar](https://docs.loganalytics.io/learn/tutorials/aggregations.html).
* Omedelbar och kraftfulla visualiseringar.
* [REST API](https://dev.applicationinsights.io/) som du kan använda för att köra frågor via programmering, till exempel från PowerShell.

Den [fullständig Språkreferens](https://go.microsoft.com/fwlink/?linkid=856079) information alla kommandon som stöds och uppdaterar regelbundet.

## <a name="next-steps"></a>Nästa steg
* Kom igång med den [Analytics-portalen](https://go.microsoft.com/fwlink/?linkid=856587)
* Kom igång [skriva frågor](https://go.microsoft.com/fwlink/?linkid=856078)
* Granska de [SQL-användarnas cheat blad](https://aka.ms/sql-analytics) översättningar av de vanligaste idioms.
* Testkör Analytics på vår [playground](https://analytics.applicationinsights.io/demo) om din app inte skickar data till Application Insights ännu.
* Titta på den [inledande video](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).