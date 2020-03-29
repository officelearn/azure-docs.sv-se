---
title: arbetsyteuttryck i Azure Monitor-loggfrågan | Microsoft-dokument
description: Arbetsyteuttrycket används i en Azure Monitor-loggfråga för att hämta data från en viss arbetsyta i samma resursgrupp, en annan resursgrupp eller en annan prenumeration.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 255888acf5da6149b6a964b23ed038b99715481c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364959"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>arbetsspace()-uttryck i Azure Monitor-loggfrågan

Uttrycket `workspace` används i en Azure Monitor-fråga för att hämta data från en viss arbetsyta i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Detta är användbart för att inkludera loggdata i en Application Insights-fråga och för att fråga data över flera arbetsytor i en loggfråga.


## <a name="syntax"></a>Syntax

`workspace(`*Identifierare*`)`

## <a name="arguments"></a>Argument

- *Identifierare*: Identifierar arbetsytan med något av formaten i tabellen nedan.

| Identifierare | Beskrivning | Exempel
|:---|:---|:---|
| Resursnamn | Läsbart namn på arbetsytan (AKA"-namnet) | arbetsyta("contosoretail") |
| Kvalificerat namn | Arbetsytas fullständiga namn i formuläret "subscriptionName/resourceGroup/componentName" | arbetsyta('Contoso/ContosoResource/ContosoWorkspace') |
| ID | GUID för arbetsytan | arbetsyta("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure-resurs-ID | Identifierare för Azure-resursen | arbetsyta("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Anteckningar

* Du måste ha läsbehörighet till arbetsytan.
* Ett relaterat `app` uttryck är som gör att du kan fråga över Application Insights-program.

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

- Se [apputtrycket](app-expression.md) för att referera till en application insights-app.
- Läs om hur [Azure Monitor-data](log-query-overview.md) lagras.
- Få tillgång till fullständig dokumentation för [Kusto-frågespråket](/azure/kusto/query/).
