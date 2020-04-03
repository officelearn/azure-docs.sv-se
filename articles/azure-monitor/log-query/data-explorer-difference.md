---
title: Skillnader i azure monitor-loggfrågor | Microsoft-dokument
description: Referensinformation för Kusto-frågespråk som används av Azure Monitor. Innehåller ytterligare element som är specifika för Azure Monitor och element som inte stöds i Azure Monitor-loggfrågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585704"
---
# <a name="azure-monitor-log-query-language-differences"></a>Skillnader i språkskillnader för Azure Monitor-loggfrågor

Medan [loggar i Azure Monitor](log-query-overview.md) är byggda på Azure Data [Explorer](/azure/data-explorer) och använder samma [Kusto-frågespråk,](/azure/kusto/query)har versionen av språket vissa skillnader. Den här artikeln identifierar element som skiljer sig åt mellan den version av språket som används för Data Explorer och den version som används för Azure Monitor-loggfrågor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>KQL-element som inte stöds i Azure Monitor
I följande avsnitt beskrivs element i Kusto-frågespråket som inte stöds av Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Satser som inte stöds i Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Frågeparametrar](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funktioner som inte stöds i Azure Monitor

* [kluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [databas()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatörer som inte stöds i Azure Monitor

* [Anslutning mellan kluster](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plugins stöds inte i Azure Monitor

* [Python plugin](/azure/kusto/query/pythonplugin)
* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Ytterligare operatörer i Azure Monitor
Följande operatörer stöder specifika Azure Monitor-funktioner och är inte tillgängliga utanför Azure Monitor.

* [app()](app-expression.md)
* [arbetsyta()](workspace-expression.md)

## <a name="next-steps"></a>Nästa steg

- Hämta referenser till olika [resurser för att skriva Azure Monitor-loggfrågor](query-language.md).
- Få tillgång till den fullständiga [referensdokumentationen för Kusto-frågespråket](/azure/kusto/query/).
