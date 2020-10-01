---
title: Azure Monitor logg frågor | Microsoft Docs
description: Referenser till resurser för att lära dig hur du skriver logg frågor i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 080af271870362e6e0633aaf590820f2fadb6144
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607445"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor-loggfrågor

Azure Monitor loggar bygger på Azure Datautforskaren och Azure Monitor logg frågor använder en version av samma Kusto. [Dokumentationen för Kusto-frågespråket](/azure/kusto/query) innehåller all information om språket och bör vara din primära resurs för att skriva Azure Monitor logg frågor. Den här sidan innehåller länkar till andra resurser för att lära dig hur du skriver frågor och om skillnader med Azure Monitor implementering av språket.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Kom igång

- [Kom igång med Azure Monitor Log Analytics](get-started-portal.md) är en lektion för att skriva frågor och arbeta med resultat i Azure Portal.
- [Kom igång med Azure Monitor logg frågor](get-started-queries.md) är en lektion för att skriva frågor med hjälp av Azure Monitor loggdata.

## <a name="concepts"></a>Begrepp

- [Analysera loggdata i Azure Monitor](./log-query-overview.md) ger en kort översikt över logg frågor och beskriver hur Azure Monitor loggdata struktureras.
- Om du [visar och analyserar loggdata i Azure Monitor](./log-query-overview.md) beskrivs de portaler där du skapar och kör logg frågor.

## <a name="reference"></a>Referens

- Språk [referens för frågor](/azure/kusto/query) är den fullständiga språk referensen för Kusto-frågespråket.
- [Azure Monitor logg frågor språk skillnader](data-explorer-difference.md) beskriver skillnaderna mellan versioner av Kusto-frågespråket.
- [Standard egenskaper i Azure Monitor logg poster](../platform/log-standard-properties.md) beskriver egenskaper som är standard för alla Azure Monitor loggdata.
- [Utföra kors resurs logg frågor i Azure Monitor](./cross-workspace-query.md) beskriver hur du skriver logg frågor som använder data från flera Log Analytics arbets ytor och Application Insights program.

## <a name="examples"></a>Exempel

- Exempel [på Azure Monitor logg frågor](examples.md) innehåller exempel frågor som använder Azure Monitor loggdata.

## <a name="lessons"></a>Lektioner

- När du [arbetar med strängar i Azure Monitor logg frågor](string-operations.md) beskrivs hur du arbetar med sträng data.
- [Arbeta med datum tids värden i Azure Monitor logg frågor](datetime-operations.md) beskriver hur du arbetar med datum-och tids data.
- [Agg regeringar i Azure Monitor logg frågor](aggregations.md) och [avancerade agg regeringar i Azure Monitor logg frågor](advanced-aggregations.md) beskriver hur du sammanställer och sammanfattar data.
- [Kopplingar i Azure Monitor logg frågor](joins.md) beskriver hur du kopplar data från flera tabeller.
- Att [arbeta med JSON och data strukturer i Azure Monitor logg frågor](json-data-structures.md) beskriver hur du tolkar JSON-data.
- Om du [skriver avancerade logg frågor i Azure Monitor](advanced-query-writing.md) beskrivs strategier för att skapa komplexa frågor och återanvända kod.
- Att [skapa diagram och diagram från Azure Monitor logg frågor](charts.md) beskriver hur du visualiserar data från en logg fråga.

## <a name="cheatsheets"></a>Översiktsblad

- [SQL till Azure Monitor logg fråga](sql-cheatsheet.md) hjälper användare som redan är bekanta med SQL.
- [Splunk för att Azure Monitor logg fråga](splunk-cheatsheet.md) hjälper användare som redan är bekanta med Splunk.

## <a name="next-steps"></a>Nästa steg

- Få till gång till fullständig [referens dokumentation för Kusto-frågespråket](/azure/kusto/query/).

