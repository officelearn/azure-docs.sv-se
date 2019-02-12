---
title: Workspace() uttryck i Azure Monitor log-fråga | Microsoft Docs
description: Arbetsytan uttryck används i en Azure Monitor log-fråga för att hämta data från en viss arbetsyta i samma resursgrupp, en annan resursgrupp eller en annan prenumeration.
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
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: 1d1bb3c99c82683dde9247da86e80d800fe06631
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993007"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>Workspace() uttryck i Azure Monitor log-fråga

Den `workspace` uttryck används i en Azure Monitor-fråga för att hämta data från en viss arbetsyta i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Detta är användbart att inkludera loggdata i en Application Insights-fråga och för att köra frågor mot data över flera arbetsytor i en loggfråga.


## <a name="syntax"></a>Syntax

`workspace(`*Identifier*`)`

## <a name="arguments"></a>Argument

- *Identifieraren*: Identifierar arbetsytan med något av format i tabellen nedan.

| Identifierare | Beskrivning | Exempel
|:---|:---|:---|
| Resursnamn | Mänskliga läsbart namn på arbetsyta (AKA ”komponentnamn”) | Workspace("contosoretail") |
| Kvalificerat namn | Fullständigt namn på arbetsytan i formatet: ”subscriptionName/resourceGroup/componentName” | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | GUID för arbetsytan | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure Resource ID | Identifierare för Azure-resursen | Workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/Workspaces/contosoretail") |


## <a name="notes"></a>Anteckningar

* Du måste ha läsbehörighet till arbetsytan.
* Ett relaterade uttryck är `app` som gör det möjligt att söka i Application Insights-program.

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

- Se den [app uttryck](app-expression.md) att referera till en Application Insights-app.
- Läs om hur [Azure Monitor data](log-query-overview.md) lagras.
- Få åtkomst till fullständig dokumentation för den [Datautforskaren frågespråk](/azure/kusto/query/).