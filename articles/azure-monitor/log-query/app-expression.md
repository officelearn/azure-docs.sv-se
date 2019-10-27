---
title: app ()-uttryck i Azure Monitor logg frågor | Microsoft Docs
description: App-uttrycket används i en Azure Monitor log-fråga för att hämta data från en speciell Application Insights-app i samma resurs grupp, en annan resurs grupp eller en annan prenumeration.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2019
ms.openlocfilehash: ffef73f88c8679d0b0be81222b1b61c4eaef5098
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933082"
---
# <a name="app-expression-in-azure-monitor-query"></a>app ()-uttryck i Azure Monitor fråga

`app`-uttrycket används i en Azure Monitor-fråga för att hämta data från en speciell Application Insights-app i samma resurs grupp, en annan resurs grupp eller en annan prenumeration. Detta är användbart om du vill inkludera program data i en Azure Monitor logg fråga och fråga efter data över flera program i en Application Insights fråga.



## <a name="syntax"></a>Syntax

`app(`*identifierare*`)`


## <a name="arguments"></a>Argument

- *Identifierare*: identifierar appen med något av formaten i tabellen nedan.

| Beteckning | Beskrivning | Exempel
|:---|:---|:---|
| Resursnamn | Namnet på appens läsliga namn (AKA "komponent namn") | app ("fabrikamapp") |
| Kvalificerat namn | Appens fullständiga namn i formatet: "subscriptionName/resourceGroup/componentName" | app (' AI-Prototype/Fabrikam/fabrikamapp ') |
| ID | Appens GUID | app ("988ba129-363e-4415-8fe7-8cbab5447518") |
| Resurs-ID för Azure | Identifierare för Azure-resursen |app ("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Anteckningar

* Du måste ha Läs behörighet till programmet.
* Att identifiera ett program med hjälp av namnet förutsätter att det är unikt för alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet går det inte att köra frågan på grund av tvetydighet. I det här fallet måste du använda någon av de andra identifierarna.
* Använd [arbets ytan](workspace-expression.md) relaterad-uttryck för att fråga i Log Analytics arbets ytor.
* App ()-uttrycket stöds för närvarande inte i Sök frågan när du använder Azure Portal för att skapa en [anpassad varnings regel för loggs ökning](../platform/alerts-log.md), om inte ett Application Insights program används som resurs för varnings regeln.

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

- Se ett [arbets ytans uttryck](workspace-expression.md) för att referera till en Log Analytics-arbetsyta.
- Läs om hur [Azure Monitor data](../../azure-monitor/log-query/log-query-overview.md) lagras.
- Få fullständig dokumentation för [Kusto-frågespråket](/azure/kusto/query/).
