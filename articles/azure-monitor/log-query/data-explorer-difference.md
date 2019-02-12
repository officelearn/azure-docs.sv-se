---
title: Azure Monitor log-fråga språkskillnader | Microsoft Docs
description: Referensinformation för Datautforskaren frågespråk som används av Azure Monitor. Innehåller ytterligare element som är specifika för Azure Monitor och element som inte stöds i Azure Monitor log-frågor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.openlocfilehash: 9c58796fa19ffb6d38582c809f7bb6ca948bd92c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003644"
---
# <a name="azure-monitor-log-query-language-differences"></a>Azure Monitor log-fråga språkskillnader

Medan [loggar i Azure Monitor](log-query-overview.md) bygger på [Azure Data Explorer](/azure/data-explorer) och använder den [samma frågespråket](/azure/kusto/query), versionen av språket har några skillnader. Den här artikeln identifierar element som skiljer sig mellan versionen av det språk som används för Datautforskaren och den version som används för Azure Monitor log-frågor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="data-explorer-elements-not-supported-in-azure-monitor"></a>Data Explorer-element som inte stöds i Azure Monitor
I följande avsnitt beskrivs element i Data Explorer frågespråk som inte stöds av Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Uttryck som inte stöds i Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Frågeparametrar](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funktioner som inte stöds i Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatörer som inte stöds i Azure Monitor

* [Anslutning till flera kluster](/azure/kusto/query/joincrosscluster)
* [externaldata operator](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plugin-program som inte stöds i Azure Monitor

* [sql_request plugin-programmet](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Ytterligare operatorer i Azure Monitor
Följande operatorer stöd för specifika funktioner i Azure Monitor och är inte tillgängliga utanför Azure Monitor.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Nästa steg

- Hämta referenser till olika [resurser för att skriva Azure Monitor logga frågor](query-language.md).
- Få åtkomst till den fullständiga [referensdokumentation för Datautforskaren frågespråk](/azure/kusto/query/).
