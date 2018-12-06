---
title: Standardegenskaper i Azure Monitor Log Analytics-poster | Microsoft Docs
description: Beskriver egenskaper som är gemensamma för olika datatyper i Azure Monitor Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: e4e921223676f4b5d64025c67914fc8b7c29a6fe
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974978"
---
# <a name="standard-properties-in-log-analytics-records"></a>Standardegenskaper i Log Analytics-poster
Data i [Log Analytics](../log-query/log-query-overview.md) lagras som en uppsättning poster, var och en med en viss datatyp som har en unik uppsättning egenskaper. Många datatyper har standardegenskaper som är gemensamma för flera typer. Den här artikeln beskriver de här egenskaperna och innehåller exempel på hur du kan använda dem i frågor.

Vissa av dessa egenskaper är fortfarande håller på att utvecklas, så du kan se dem i vissa datatyper, men har ännu inte i andra.

## <a name="timegenerated"></a>TimeGenerated
Den **TimeGenerated** egenskapen innehåller datum och tid då posten skapades. Det ger en gemensam egenskap ska användas för filtrering eller sammanfatta efter tid. När du väljer ett tidsintervall för en vy eller en instrumentpanel i Azure-portalen använder TimeGenerated för att filtrera resultaten.

### <a name="examples"></a>Exempel

Följande fråga returnerar antalet felhändelser som har skapats för varje dag under föregående vecka.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Typ
Den **typ** egenskapen innehåller namnet på tabellen att posten har hämtats från vilket kan också betraktas som posttypen. Den här egenskapen är användbar i frågor som kombinerar poster från flera tabeller, som de som använder den `search` operator för att skilja mellan poster av olika typer. **$table** kan användas i stället för **typ** på vissa platser.

### <a name="examples"></a>Exempel
Följande fråga returnerar antalet poster efter typ som samlas in med den senaste timmen.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_Resurs-ID
Den  **\_ResourceId** egenskapen innehåller en unik identifierare för den resurs som posten är associerad med. Detta ger dig en egenskap som är standard ska använda för att begränsa frågan till endast poster från en viss resurs eller att ansluta till relaterade data från flera tabeller.

För Azure-resurser, värdet för **_ResourceId** är den [Azure-resurs-ID-URL: en](../../azure-resource-manager/resource-group-template-functions-resource.md). Egenskapen är för närvarande begränsat till Azure-resurser, men det kommer att gälla till resurser utanför Azure, t.ex lokala datorer.

> [!NOTE]
> Vissa datatyper redan har fält som innehåller Azure-resurs-ID eller minst delar av den som prenumerations-ID. Även om de här fälten hålls för bakåtkompatibilitet, rekommenderar vi att du använder _ResourceId för att utföra korrelationen mellan eftersom det blir mer konsekvent.

### <a name="examples"></a>Exempel
Följande fråga kopplar ihop data om prestanda och händelse för varje dator. Den visar alla händelser med ID _101_ och processorbelastning över 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Följande fråga kopplingar _AzureActivity_ registrerar med _SecurityEvent_ poster. Den visar alla aktivitet åtgärder med användare som har loggat in till dessa datorer.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur [Log Analytics-data lagras](../log-query/log-query-overview.md).
- Få en lektion på [skriva frågor i Log Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Få en lektion på [koppla tabeller i Log Analytics-frågor](../../azure-monitor/log-query/joins.md).
