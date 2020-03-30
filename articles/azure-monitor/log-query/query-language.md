---
title: Azure Monitor-loggfrågor | Microsoft-dokument
description: Referenser till resurser för att lära dig hur du skriver loggfrågor i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: fc943ac3cf82d22a58d0ba3390ad8d9bbee6a4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669395"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor-loggfrågor
Azure Monitor-loggar bygger på Azure Data Explorer och Azure Monitor-loggfrågor använder en version av samma Kusto-frågespråk. [Kusto-frågespråkdokumentationen](/azure/kusto/query) innehåller all information för språket och bör vara din primära resurs för att skriva Azure Monitor-loggfrågor. Den här sidan innehåller länkar till andra resurser för att lära dig hur du skriver frågor och om skillnader med Azure Monitor-implementeringen av språket.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Komma igång

- [Komma igång med Azure Monitor Log Analytics](get-started-portal.md) är en lektion för att skriva frågor och arbeta med resultat i Azure-portalen.
- [Komma igång med Azure Monitor-loggfrågor](get-started-queries.md) är en lektion för att skriva frågor med hjälp av Azure Monitor-loggdata.

## <a name="concepts"></a>Begrepp
- [Analysera loggdata i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) ger en kort översikt över loggfrågor och beskriver hur Azure Monitor-loggdata är strukturerade.
- [Om du visar och analyserar loggdata i Azure Monitor](../../azure-monitor/log-query/portals.md) förklaras de portaler där du skapar och kör loggfrågor.

## <a name="reference"></a>Referens

- [Referens för frågespråk](/azure/kusto/query) är den fullständiga språkreferensen för Kusto-frågespråket.
- [Azure Monitor-loggfrågorsspråksskillnader](data-explorer-difference.md) beskriver skillnader mellan versioner av Kusto-frågespråket.
- [Standardegenskaper i Azure Monitor-loggposter](../../azure-monitor/platform/log-standard-properties.md) beskriver egenskaper som är standard för alla Azure Monitor-loggdata.
- [Utföra resursövergripande loggfrågor i Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) beskriver hur du skriver loggfrågor som använder data från flera Log Analytics-arbetsytor och Application Insights-program.


## <a name="examples"></a>Exempel

- [Azure Monitor-loggfrågefrågeexempel](examples.md) innehåller exempelfrågor med hjälp av Azure Monitor-loggdata.



## <a name="lessons"></a>Lektioner

- [När du arbetar med strängar i Azure Monitor-loggfrågor](string-operations.md) beskrivs hur du arbetar med strängdata.
- [När du arbetar med datumtidsvärden i Azure Monitor-loggfrågor](datetime-operations.md) beskrivs hur du arbetar med datum- och tidsdata. 
- [Aggregeringar i Azure Monitor-loggfrågor](aggregations.md) och [avancerade aggregeringar i Azure Monitor-loggfrågor](advanced-aggregations.md) beskriver hur du sammanställer och sammanfattar data.
- [Går med i Azure Monitor-loggfrågor](joins.md) beskriver hur du ansluter data från flera tabeller.
- [Genom att arbeta med JSON och datastrukturer i Azure Monitor-loggfrågor](json-data-structures.md) beskrivs hur du tolkar json-data.
- [Genom att skriva avancerade loggfrågor i Azure Monitor](advanced-query-writing.md) beskrivs strategier för att skapa komplexa frågor och återanvända kod.
- [När du skapar diagram och diagram från Azure Monitor-loggfrågor](charts.md) beskrivs hur du visualiserar data från en loggfråga.

## <a name="cheatsheets"></a>Översiktsblad

-  [SQL till Azure Monitor loggfråga](sql-cheatsheet.md) hjälper användare som redan är bekanta med SQL.
-  [Splunk till Azure Monitor loggfråga](splunk-cheatsheet.md) hjälper användare som redan är bekanta med Splunk.
 
## <a name="next-steps"></a>Nästa steg

- Få tillgång till den fullständiga [referensdokumentationen för Kusto-frågespråket](/azure/kusto/query/).
