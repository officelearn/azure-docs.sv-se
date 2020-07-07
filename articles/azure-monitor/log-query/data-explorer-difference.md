---
title: Azure Monitor skillnader i frågespråk för loggen | Microsoft Docs
description: Referensinformation för Kusto-frågespråket som används av Azure Monitor. Innehåller ytterligare element som är speciella för Azure Monitor och element som inte stöds i Azure Monitor logg frågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80585704"
---
# <a name="azure-monitor-log-query-language-differences"></a>Skillnader i språk i Azure Monitor logg frågor

Även om [loggar i Azure Monitor](log-query-overview.md) bygger på [Azure datautforskaren](/azure/data-explorer) och använder samma [Kusto-frågespråk](/azure/kusto/query), har versionen av språket vissa skillnader. Den här artikeln identifierar element som skiljer sig från den version av språket som används för Datautforskaren och den version som används för Azure Monitor logg frågor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>KQL-element stöds inte i Azure Monitor
I följande avsnitt beskrivs elementen i Kusto-frågespråket som inte stöds av Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instruktioner stöds inte i Azure Monitor

* [Aliasuppsättning](/azure/kusto/query/aliasstatement)
* [Frågeparametrar](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funktioner som inte stöds i Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current (), current_cursor ()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatorer stöds inte i Azure Monitor

* [Anslutning mellan kluster](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plugin-program stöds inte i Azure Monitor

* [Python-plugin](/azure/kusto/query/pythonplugin)
* [sql_request-plugin-program](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Ytterligare operatörer i Azure Monitor
Följande operatörer stöder vissa Azure Monitor funktioner och är inte tillgängliga utanför Azure Monitor.

* [app ()](app-expression.md)
* [arbets yta ()](workspace-expression.md)

## <a name="next-steps"></a>Nästa steg

- Hämta referenser till olika [resurser för att skriva Azure Monitor logg frågor](query-language.md).
- Få till gång till fullständig [referens dokumentation för Kusto-frågespråk](/azure/kusto/query/).
