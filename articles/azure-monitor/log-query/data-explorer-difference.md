---
title: Språkreferens för Azure Monitor Log Analytics | Microsoft Docs
description: Referensinformation för Datautforskaren frågespråk som används av Log Analytics. Innehåller ytterligare element som är specifika för Log Analytics och element som inte stöds i Log Analytics-frågor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: e905b90c405ad8adbc3084ce18fe765744cb8b5a
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53013772"
---
# <a name="log-analytics-query-language-differences"></a>Logga Analytics query language skillnader

Medan [Log Analytics](log-query-overview.md) bygger på [Azure Data Explorer](/azure/data-explorer) och använder den [samma frågespråket](/azure/kusto/query), versionen av språket har några skillnader. Den här artikeln identifierar element som skiljer sig mellan versionen av det språk som används för Datautforskaren och den version som används för Log Analytics-frågor.

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Data Explorer-element som inte stöds i Log Analytics
I följande avsnitt beskrivs element i Data Explorer frågespråk som inte stöds av Log Analytics.

### <a name="statements-not-supported-in-log-analytics"></a>Uttryck som inte stöds i Log Analytics

* [Alias](/azure/kusto/query/aliasstatement)
* [Frågeparametrar](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Funktioner som inte stöds i Log Analytics

* [Cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [Database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Operatörer som inte stöds i Log Analytics

* [Anslutning till flera kluster](/azure/kusto/query/joincrosscluster)
* [externaldata operator](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Plugin-program som inte stöds i Log Analytics

* [sql_request plugin-programmet](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Ytterligare operatorer i Log Analytics
Följande operatorer stöd för specifika funktioner i Log Analytics och är inte tillgängliga utanför Log Analytics.

* [App()](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>Nästa steg

- Hämta referenser till olika [resurser för att skriva Log Analytics-frågor](query-language.md).
- Få åtkomst till den fullständiga [referensdokumentation för Datautforskaren frågespråk](/azure/kusto/query/).
