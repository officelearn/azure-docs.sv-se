---
title: Analytics - kraftfull sökning och Frågeverktyg för Azure Application Insights | Microsoft Docs
description: 'Översikt över analys, kraftfulla diagnostik sökverktyget för Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: c41444f94e4685d246de225500c8a5beefc74944
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065621"
---
# <a name="analytics-in-application-insights"></a>Analys i Application Insights
Analytics är kraftfullt Sök och fråga i Verktyg för [Application Insights](../../application-insights/app-insights-overview.md). Analytics är ett Webbverktyg så att ingen installation krävs. Om du redan har konfigurerat Application Insights för en av dina appar så att du kan analysera din Apps data genom att öppna Analytics från din app [översiktsbladet](../../azure-monitor/app/app-insights-dashboards.md).

![Öppna portal.azure.com, öppna Application Insights-resursen och klicka på Analytics.](./media/analytics/001.png)

Du kan också använda den [Analytics playground](https://go.microsoft.com/fwlink/?linkid=859557) som är en kostnadsfri demomiljö med mycket exempeldata.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Fråga efter data i Analytics
En typisk frågan börjar med ett tabellnamn följt av en serie *operatörer* avgränsade med `|`.
Exempelvis kan vi ta reda på hur många begäranden vår app som togs emot från olika länder under de senaste 3 timmarna:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Vi börjar med tabellnamnet *begäranden* och lägga till via rörledningar element efter behov.  Först definierar vi ett tillfälligt filter för att granska bara poster från de senaste 3 timmarna.
Vi sedan räknar antalet poster per land (att data som hittas i kolumnen *client_CountryOrRegion*). Slutligen kan rendera vi resultatet i ett cirkeldiagram.
<br>

![Frågeresultat](./media/analytics/030.png)

Språket har många attraktiva funktioner:

* [Filtret](/azure/kusto/query/whereoperator) apptelemetrin raw genom att ett fält, inklusive anpassade egenskaper och mått.
* [Ansluta till](/azure/kusto/query/joinoperator) flera tabeller - korrelera begäranden med sidvisningar, beroendeanrop, undantag och loggspårningar.
* Kraftfulla statistiska [aggregeringar](/azure/kusto/query/summarizeoperator).
* Omedelbar och kraftfulla visualiseringar.
* [REST API](https://dev.applicationinsights.io/) att du kan använda för att köra frågor via programmering, till exempel från PowerShell.

Den [fullständig Språkreferens](https://go.microsoft.com/fwlink/?linkid=856079) beskriver alla kommandon som stöds och uppdaterar regelbundet.

## <a name="next-steps"></a>Nästa steg
* Kom igång med den [Analytics-portalen](https://go.microsoft.com/fwlink/?linkid=856587)
* Kom igång [skriva frågor](https://go.microsoft.com/fwlink/?linkid=856078)
* Granska den [SQL-användarnas Lathund](https://aka.ms/sql-analytics) för översättningar av de vanligaste idioms.
* Testkör Analytics på vår [playground](https://analytics.applicationinsights.io/demo) om din app inte skickar data till Application Insights ännu.
* Titta på den [introduktionsvideon](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).
