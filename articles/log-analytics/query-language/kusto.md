---
title: Språkreferens för Azure Monitor Log Analytics | Microsoft Docs
description: Referensinformation för Kusto-språk som används av Log Analytics. Innehåller ytterligare element som är specifika för Log Analytics och element som inte stöds i Log Analytics-frågor.
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
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451076"
---
# <a name="log-analytics-query-language-reference"></a>Logga referens Analytics-frågespråket
[Logga analysfrågor](../log-analytics-queries.md) Använd samma frågespråk och motor som används av [Azure Data Explorer](/azure/data-explorer/). Du kan komma åt språkreferensen och annan information om språk från följande plats: [Kusto-Språkreferens](/azure/kusto/query)



## <a name="kusto-elements-not-support-in-log-analytics"></a>Kusto-element stöds inte i Log Analytics
Log Analytics-frågor via en implementering av Kusto, finns men det vissa Kusto-element som det inte stöder enligt beskrivningen i följande avsnitt.

### <a name="statements-not-supported-in-log-analytics"></a>Uttryck som inte stöds i Log Analytics

* [Alias](/kusto/query/aliasstatement)
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
För att stödja specifika funktioner för logganalys, finns följande ytterligare Kusto-operatorer som inte är tillgängliga utanför Log Analytics. 

* [App()](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om frågor i [Log Analytics](../log-analytics-queries.md).
- Gå igenom en lektion på skrivs på en [Log Analytics-fråga](/log-analytics/query-language/get-started-queries.md).
- Få åtkomst till den fullständiga [referensdokumentation för Kusto](/azure/kusto/query/).