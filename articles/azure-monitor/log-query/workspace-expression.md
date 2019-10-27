---
title: uttryck för arbets yta () i Azure Monitor logg fråga | Microsoft Docs
description: Uttrycket för arbets ytan används i en Azure Monitor logg fråga för att hämta data från en speciell arbets yta i samma resurs grupp, en annan resurs grupp eller en annan prenumeration.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: f26b9534fbf95cc301ae782d47ab7030988fa469
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932838"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>uttryck för arbets yta () i Azure Monitor logg fråga

`workspace`-uttrycket används i en Azure Monitor-fråga för att hämta data från en speciell arbets yta i samma resurs grupp, en annan resurs grupp eller en annan prenumeration. Detta är användbart om du vill inkludera loggdata i en Application Insights fråga och fråga efter data över flera arbets ytor i en logg fråga.


## <a name="syntax"></a>Syntax

`workspace(`*identifierare*`)`

## <a name="arguments"></a>Argument

- *Identifierare*: identifierar arbets ytan med något av formaten i tabellen nedan.

| Beteckning | Beskrivning | Exempel
|:---|:---|:---|
| Resursnamn | Människots läsbara namn på arbets ytan (AKA "komponent namn") | arbets yta ("ContosoRetail") |
| Kvalificerat namn | Fullständigt namn på arbets ytan i formatet: "subscriptionName/resourceGroup/componentName" | arbets yta (contoso/ContosoResource/ContosoWorkspace) |
| ID | GUID för arbets ytan | arbets yta ("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Resurs-ID för Azure | Identifierare för Azure-resursen | arbets yta ("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Anteckningar

* Du måste ha Läs behörighet till arbets ytan.
* Ett relaterat uttryck är `app` som gör det möjligt att fråga i Application Insights program.

## <a name="examples"></a>Exempel

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
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

- Se [app-uttrycket](app-expression.md) för att referera till en Application Insights-app.
- Läs om hur [Azure Monitor data](log-query-overview.md) lagras.
- Få fullständig dokumentation för [Kusto-frågespråket](/azure/kusto/query/).
