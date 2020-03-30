---
title: app() uttryck i Azure Monitor-loggfrågor | Microsoft-dokument
description: Apputtrycket används i en Azure Monitor-loggfråga för att hämta data från en specifik Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2019
ms.openlocfilehash: 5502df1cd119c0f63c65945d73431a17282ebc0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670270"
---
# <a name="app-expression-in-azure-monitor-query"></a>app() uttryck i Azure Monitor-fråga

Uttrycket `app` används i en Azure Monitor-fråga för att hämta data från en specifik Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Detta är användbart för att inkludera programdata i en Azure Monitor-loggfråga och att fråga data över flera program i en Application Insights-fråga.



## <a name="syntax"></a>Syntax

`app(`*Identifierare*`)`


## <a name="arguments"></a>Argument

- *Identifierare*: Identifierar appen med något av formaten i tabellen nedan.

| Identifierare | Beskrivning | Exempel
|:---|:---|:---|
| Resursnamn | Läsbart namn på appen (AKA"-namnet) | app("fabrikamapp") |
| Kvalificerat namn | Appens fullständiga namn i formuläret "subscriptionName/resourceGroup/componentName" | app("AI-Prototype/Fabrikam/fabrikamapp") |
| ID | GUID för appen | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure-resurs-ID | Identifierare för Azure-resursen |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Anteckningar

* Du måste ha läsbehörighet till programmet.
* Identifiera ett program med dess namn förutsätter att det är unikt för alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet misslyckas frågan på grund av tvetydigheten. I det här fallet måste du använda någon av de andra identifierarna.
* Använd arbetsytan för relaterade [uttryck](workspace-expression.md) för att fråga över Log Analytics-arbetsytor.
* Uttrycket app() stöds för närvarande inte i sökfrågan när du använder Azure-portalen för att skapa en [anpassad loggsökaviseringsregel](../platform/alerts-log.md), såvida inte ett Application Insights-program används som resurs för aviseringsregeln.

## <a name="examples"></a>Exempel

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Nästa steg

- Se [arbetsytans uttryck](workspace-expression.md) för att referera till en Log Analytics-arbetsyta.
- Läs om hur [Azure Monitor-data](../../azure-monitor/log-query/log-query-overview.md) lagras.
- Få tillgång till fullständig dokumentation för [Kusto-frågespråket](/azure/kusto/query/).
