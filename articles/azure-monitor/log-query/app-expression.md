---
title: App() uttryck i Azure Monitor loggfrågor | Microsoft Docs
description: App-uttryck används i en Azure Monitor log-fråga för att hämta data från en viss Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration.
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
ms.date: 01/25/2019
ms.author: bwren
ms.openlocfilehash: 0abe2c869a05d359f692db105eb647f9e6a69df8
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266432"
---
# <a name="app-expression-in-azure-monitor-query"></a>App() uttryck i Azure Monitor-fråga

Den `app` uttryck används i en Azure Monitor-fråga för att hämta data från en viss Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Detta är användbart att inkludera programdata i en Azure Monitor log-fråga och för att köra frågor mot data över flera program i en Application Insights-fråga.



## <a name="syntax"></a>Syntax

`app(`*Identifier*`)`


## <a name="arguments"></a>Argument

- *Identifieraren*: Identifierar appen med någon av format i tabellen nedan.

| Identifierare | Beskrivning | Exempel
|:---|:---|:---|
| Resursnamn | Mänskliga läsbara namnet på appen (AKA ”komponentnamn”) | app("fabrikamapp") |
| Kvalificerat namn | Fullständigt namn för appen i formatet: ”subscriptionName/resourceGroup/componentName” | App('AI-prototype/Fabrikam/fabrikamapp') |
| ID | GUID för appen | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure Resource ID | Identifierare för Azure-resursen |App("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/Microsoft.Insights/Components/fabrikamapp") |


## <a name="notes"></a>Anteckningar

* Du måste ha läsbehörighet till programmet.
* Identifierar ett program med namnet förutsätter att det är unikt för alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet att frågan misslyckas på grund av tvetydighet. I det här fallet måste du använda en av de andra identifierarna.
* Använd relaterade uttrycket [arbetsytan](workspace-expression.md) till frågan i Log Analytics-arbetsytor.
* App() uttrycket stöds för närvarande inte i frågan med Azure-portalen för att skapa en [aviseringsregeln för anpassad sökning](../platform/alerts-log.md), såvida inte ett Application Insights-program kan användas som resursen för regeln.

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

- Se den [arbetsytan uttryck](workspace-expression.md) att referera till en Log Analytics-arbetsyta.
- Läs om hur [Azure Monitor data](../../azure-monitor/log-query/log-query-overview.md) lagras.
- Få åtkomst till fullständig dokumentation för den [Kusto-frågespråket](/azure/kusto/query/).