---
title: Azure Monitor-loggfrågor | Microsoft Docs
description: Referenser till resurser för att lära dig hur du skriver loggfrågor i Azure Monitor.
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
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: ecbea91cef69c80975dd105c35f526b357033a6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425970"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor log-frågor
Azure Monitor-loggar bygger på Azure Data Explorer och Azure Monitor log-frågor använder en version av samma Kusto-frågespråket. Den [Kusto-fråga språkdokumentation](/azure/kusto/query) har alla detaljer för språket och ska vara din primära resurs för att skriva Azure Monitor log-frågor. Den här sidan innehåller länkar till andra resurser för att lära dig hur du skriver frågor och på skillnader med Azure Monitor-implementering för språket.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Komma igång

- [Kom igång med Azure Monitor Log Analytics](get-started-portal.md) är en lektion för att skriva frågor och arbeta med resultat i Azure-portalen.
- [Kom igång med Azure Monitor log-frågor](get-started-queries.md) är en lektion för att skriva frågor med Azure Monitor-loggdata.

## <a name="concepts"></a>Begrepp
- [Analysera loggdata i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) ger en kort översikt över log frågar och beskriver hur Azure Monitor-loggdata är strukturerad.
- [Visa och analysera loggdata i Azure Monitor](../../azure-monitor/log-query/portals.md) förklarar portalerna där du skapar och kör loggfrågor.

## <a name="reference"></a>Referens

- [Frågespråksreferens](/azure/kusto/query) är klar Språkreferens för Kusto-frågespråket.
- [Azure Monitor log-fråga språkskillnader](data-explorer-difference.md) beskriver skillnaderna mellan versioner av Kusto-frågespråket.
- [Standardegenskaper i Azure Monitor loggposter](../../azure-monitor/platform/log-standard-properties.md) beskriver egenskaper som är standard för alla Azure Monitor-loggdata.
- [Utföra loggfrågor mellan resurser i Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) beskriver hur du skriver loggfrågor som använder data från flera Log Analytics-arbetsytor och Application Insights-program.


## <a name="examples"></a>Exempel

- [Azure Monitor log-fråga exempel](examples.md) innehåller exempel på frågor med hjälp av Azure Monitor-loggdata.



## <a name="lessons"></a>Lektioner

- [Arbeta med strängar i Azure Monitor loggfrågor](string-operations.md) beskriver hur du arbetar med.
- [Arbeta med datum-tid-värdena i Azure Monitor loggfrågor](datetime-operations.md) beskriver hur du arbetar med datum och tid. 
- [Aggregeringar i Azure Monitor logga frågor](aggregations.md) och [avancerade aggregeringar i Azure Monitor loggfrågor](advanced-aggregations.md) beskrivs hur du sammanställer och sammanfattar data.
- [Kopplingar i Azure Monitor loggfrågor](joins.md) beskriver hur du ansluter till data från flera tabeller.
- [Arbeta med JSON och datastrukturer i Azure Monitor loggfrågor](json-data-structures.md) beskriver hur du parsa json-data.
- [Skriva avancerade logga frågor i Azure Monitor](advanced-query-writing.md) beskrivs strategier för att skapa komplexa frågor och återanvända kod.
- [Skapar diagram och diagram från Azure Monitor loggfrågor](charts.md) beskriver hur du visualisera data från en loggfråga.

## <a name="cheatsheets"></a>Översiktsblad

-  [SQL Azure Monitor log-frågan](sql-cheatsheet.md) hjälper användare som redan är bekant med SQL.
-  [Splunk till Azure Monitor loggfråga](sql-cheatsheet.md) hjälper användare som redan är bekant med Splunk.
 
## <a name="next-steps"></a>Nästa steg

- Få åtkomst till den fullständiga [referensdokumentation för det Kusto-frågespråket](/azure/kusto/query/).