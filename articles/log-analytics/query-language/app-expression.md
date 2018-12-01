---
title: App() uttryck i Azure Log Analytics-fråga | Microsoft Docs
description: App-uttryck används i en Log Analytics-fråga för att hämta data från en viss Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration.
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
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: dc9884d72bc2e09c88bf1d5dc238ec5d1ff56430
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52681287"
---
# <a name="app-expression-in-log-analytics-query"></a>App() uttryck i Log Analytics-fråga

Den `app` uttryck används i en Log Analytics-fråga för att hämta data från en viss Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Detta är användbart att inkludera programdata i en Log Analytics-fråga och för att köra frågor mot data över flera program i en Application Insights-fråga.



## <a name="syntax"></a>Syntax

`app(`*identifierare*`)`


## <a name="arguments"></a>Argument

- *Identifieraren*: identifierar appen med någon av format i tabellen nedan.

| Identifierare | Beskrivning | Exempel
|:---|:---|:---|
| Resursnamn | Mänskliga läsbara namnet på appen (AKA ”komponentnamn”) | App("fabrikamapp") |
| Kvalificerat namn | Fullständigt namn för appen i formatet: ”subscriptionName/resourceGroup/componentName” | App('AI-prototype/Fabrikam/fabrikamapp') |
| ID | GUID för appen | App("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure-resurs-ID | Identifierare för Azure-resursen |App("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/Microsoft.Insights/Components/fabrikamapp") |


## <a name="notes"></a>Anteckningar

* Du måste ha läsbehörighet till programmet.
* Identifierar ett program med namnet förutsätter att det är unikt för alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet att frågan misslyckas på grund av tvetydighet. I det här fallet måste du använda en av de andra identifierarna.
* Använd relaterade uttrycket [arbetsytan](workspace-expression.md) till frågan i Log Analytics-arbetsytor.

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

- Se den [arbetsytan uttryck](workspace-expression.md) att referera till Log Analytics-arbetsyta.
- Läs om hur [Log Analytics-data](../../log-analytics/log-analytics-queries.md) lagras.