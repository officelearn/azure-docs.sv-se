---
title: Azure Monitor Log Analytics och Kusto språkskillnader | Microsoft Docs
description: Beskriver skillnaderna mellan Log Analytics-frågor och core Kusto-språk.
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
ms.openlocfilehash: 109ffa6abb34dad6a00210a5c2c726bdfdde094f
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184918"
---
# <a name="log-analytics-and-kusto-language-differences"></a>Log Analytics och Kusto språkskillnader
[Logga analysfrågor](../log-analytics-queries.md) skrivs med den [Kusto språk](/azure/kusto/query). Det finns några skillnader från standardspråket och Log Analytics-implementering dock enligt beskrivningen i den här artikeln.


## <a name="statements-not-supported-in-log-analytics"></a>Uttryck som inte stöds i Log Analytics
Följande uttryck stöds inte i Log Analytics.

* [Alias](/kusto/query/aliasstatement)
* [Frågeparametrar](/azure/kusto/query/queryparametersstatement)

## <a name="functions-not-supported-in-log-analytics"></a>Funktioner som inte stöds i Log Analytics
Följande funktioner stöds inte i Log Analytics.

* [Cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [Database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

## <a name="operators-not-supported-in-log-analytics"></a>Operatörer som inte stöds i Log Analytics
Följande operatorer stöds inte i Log Analytics.

* [Anslutning till flera kluster](/azure/kusto/query/joincrosscluster)
* [externaldata operator](/azure/kusto/query/externaldata-operator)

## <a name="plugins-not-supported-in-log-analytics"></a>Plugin-program som inte stöds i Log Analytics
Följande plugin-program stöds inte i Log Analytics.
* [sql_request plugin-programmet](/azure/kusto/query/sqlrequestplugin)


## <a name="log-analytics-specific-operators"></a>Log Analytics specifika operatorer
* [App()](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om frågor i [Log Analytics](../log-analytics-queries.md).
- Gå igenom en lektion på skrivs på en [Log Analytics-fråga](/log-analytics/query-language/get-started-queries.md).
- Få åtkomst till den fullständiga [referensdokumentation för Kusto](/azure/kusto/query/).