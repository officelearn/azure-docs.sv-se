---
title: Azure Monitor Log Analytics-frågespråket | Microsoft Docs
description: Referenser till resurser för att lära dig hur du skriver frågor i Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: bwren
ms.openlocfilehash: 493c48fe87158ee357d98eb0b0d1bd2a4fad5b2f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843792"
---
# <a name="log-analytics-query-language"></a>Logga Analytics-frågespråket
Log Analytics tillhandahåller Logginsamling och analys för Azure Monitor. Den bygger på Azure Data Explorer och använder en version av samma frågespråk. Den [språkdokumentation för Azure Data Explorer fråga](/azure/kusto/query) har alla detaljer för språket och ska vara din primära resurs för att skriva Log Analytics-frågor. Den här sidan innehåller länkar till andra resurser för att lära dig hur du skriver frågor och på skillnader med Log Analytics-implementering för språket.

## <a name="getting-started"></a>Komma igång

- [Kom igång med Log Analytics i Azure-portalen](get-started-analytics-portal.md) är en lektion för att skriva frågor och arbeta med resultat i Azure-portalen.
-  [Kom igång med frågor i Log Analytics](get-started-queries.md) är en lektion för att skriva frågor med Log Analytics-data.

## <a name="concepts"></a>Begrepp
- [Analysera Log Analytics-data i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) ger en kort översikt över log frågar och beskriver hur Log Analytics-data är strukturerad.
- [Visa och analysera data i Log Analytics](../log-analytics-log-search-portals.md) förklarar portalerna där du skapar och kör Log Analytics-frågor.

## <a name="reference"></a>Referens

- [Frågespråksreferens](/azure/kusto/query) är klar Språkreferens för frågespråket i Datautforskaren.
- [Logga Analytics query language skillnader](data-explorer-difference.md) beskriver skillnaderna mellan versioner av frågespråket i Datautforskaren.
- [Standardegenskaper i Log Analytics-poster](../log-analytics-standard-properties.md) beskriver egenskaper som är standard för alla Log Analytics-data.
- [Utföra mellan resurser loggsökningar i Log Analytics](../log-analytics-cross-workspace-search.md) beskriver hur du skriver frågor som använder data från flera Log Analytics-arbetsytor och Application Insights-program.


## <a name="examples"></a>Exempel

- [Logga Analytics-fråga exempel](examples.md) innehåller exempel på frågor med Log Analytics-data.



## <a name="lessons"></a>Lektioner

- [Arbeta med strängar i Log Analytics-frågor](string-operations.md) beskriver hur du arbetar med.
- [Arbeta med datum-tid-värdena i Log Analytics-frågor](datetime-operations.md) beskriver hur du arbetar med datum och tid. 
- [Aggregeringar i Log Analytics-frågor](aggregations.md) och [avancerade aggregeringar i Log Analytics-frågor](advanced-aggregations.md) beskrivs hur du sammanställer och sammanfattar data.
- [Kopplingar i Log Analytics-frågor](joins.md) beskriver hur du ansluter till data från flera tabeller.
- [Arbeta med JSON och datastrukturer i Log Analytics-frågor](json-data-structures.md) beskriver hur du parsa json-data.
- [Skriva avancerade frågor i Log Analytics](advanced-query-writing.md) beskrivs strategier för att skapa komplexa frågor och återanvända kod.
- [Skapar diagram och diagram från Log Analytics-frågor](charts.md) beskriver hur du visualisera data från en fråga.

## <a name="cheatsheets"></a>Översiktsblad

-  [SQL till Log Analytics Lathund till frågespråket](sql-cheatsheet.md) hjälper användare som redan är bekant med SQL.
-  [Splunk till Log Analytics Lathund till frågespråket](sql-cheatsheet.md) hjälper användare som redan är bekant med Splunk.
 
## <a name="next-steps"></a>Nästa steg

- Få åtkomst till den fullständiga [referensdokumentation för frågespråket i Data Explorer](/azure/kusto/query/).