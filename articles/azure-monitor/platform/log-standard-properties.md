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
ms.date: 01/14/2019
ms.author: bwren
ms.openlocfilehash: abcf3100dc5252db9e3a5e7b446417333a9b37ca
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321899"
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

## <a name="resourceid"></a>\_ResourceId
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

## <a name="isbillable"></a>\_IsBillable
Den  **\_IsBillable** egenskapen anger om insamlade data är fakturerbara. Data med  **\_IsBillable** lika _FALSKT_ samlas in kostnadsfritt och debiteras inte för ditt Azure-konto.

### <a name="examples"></a>Exempel
Om du vill hämta en lista över datorer som skickar faktureras datatyper, använder du följande fråga:

> [!NOTE]
> Använda frågor med `union withsource = tt *` sparsamt sökningar över data datatyper är dyrt att köra. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Detta kan utökas för att returnera antalet datorer per timme som skickar faktureras datatyper:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="billedsize"></a>\_BilledSize
Den  **\_BilledSize** egenskapen anger storleken i byte för data som kommer att debiteras för ditt Azure-konto om  **\_IsBillable** är sant.

### <a name="examples"></a>Exempel
Visa storleken på faktureringsbara händelser matas in per dator att använda den `_BilledSize` egenskapen som tillhandahåller storlek i byte:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Om du vill se antalet händelser som matas in per dator, använder du följande fråga:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Om du vill se antalet faktureringsbara händelser matas in per dator, använder du följande fråga: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Om du vill se antalet för fakturerbar datatyper skickar data till en specifik dator använder du följande fråga:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```


## <a name="next-steps"></a>Nästa steg

- Läs mer om hur [Log Analytics-data lagras](../log-query/log-query-overview.md).
- Få en lektion på [skriva frågor i Log Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Få en lektion på [koppla tabeller i Log Analytics-frågor](../../azure-monitor/log-query/joins.md).
