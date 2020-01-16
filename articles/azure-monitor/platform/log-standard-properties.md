---
title: Standard egenskaper i Azure Monitor logg poster | Microsoft Docs
description: Beskriver egenskaper som är gemensamma för flera data typer i Azure Monitor loggar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 31a6c53ec269c512ad641fcdc10469ccf16a1fe9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979747"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Standard egenskaper i Azure Monitor loggar
Data i Azure Monitor loggar [lagras som en uppsättning poster i antingen en Log Analytics arbets yta eller ett Application Insights program](../log-query/logs-structure.md), var och en med en viss datatyp som har en unik uppsättning egenskaper. Många data typer har standard egenskaper som är gemensamma för flera typer. Den här artikeln beskriver dessa egenskaper och innehåller exempel på hur du kan använda dem i frågor.

> [!NOTE]
> Några av standard propert-fälten visas inte i diagramvyn eller IntelliSense i Log Analytics och de visas inte i frågeresultaten om du inte uttryckligen anger egenskapen i utdata.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated och tidsstämpel
Egenskaperna **TimeGenerated** (Log Analytics arbetsyte) och **tidsstämpel** (Application Insights program) innehåller datum och tid då posten skapades av data källan. Mer information finns i [inmatnings tiden för logg data i Azure Monitor](data-ingestion-time.md) .

**TimeGenerated** och **tidsstämpel** tillhandahåller en gemensam egenskap som används för filtrering eller sammanfattning per tid. När du väljer ett tidsintervall för en vy eller instrument panel i Azure Portal, används TimeGenerated eller timestamp för att filtrera resultaten. 

### <a name="examples"></a>Exempel

Följande fråga returnerar antalet fel händelser som har skapats för varje dag under föregående vecka.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Följande fråga returnerar antalet undantag som har skapats för varje dag under föregående vecka.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
Egenskapen **\_TimeReceived** innehåller datum och tid då posten togs emot av Azure Monitor inmatnings punkt i Azure-molnet. Detta kan vara användbart för att identifiera svars tids problem mellan data källan och molnet. Ett exempel är ett nätverks problem som orsakar en fördröjning med data som skickas från en agent. Mer information finns i [inmatnings tiden för logg data i Azure Monitor](data-ingestion-time.md) .

Följande fråga ger den genomsnittliga svars tiden per timme för händelse poster från en agent. Detta inkluderar tiden från agenten till molnet och den totala tiden för posten som ska vara tillgänglig för logg frågor.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Typ och itemType
Egenskaperna **typ** (Log Analytics arbets yta) och **itemType** (Application Insights program) innehåller namnet på tabellen som posten hämtades från, som också kan betraktas som post typen. Den här egenskapen är användbar i frågor som kombinerar poster från flera tabeller, t. ex. sådana som använder `search`-operatorn för att skilja mellan olika typer av poster. **$Table** kan användas i stället för **typ** på vissa platser.

### <a name="examples"></a>Exempel
Följande fråga returnerar antalet poster efter den typ som samlats in den senaste timmen.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
Egenskapen **\_Itemid** innehåller en unik identifierare för posten.


## <a name="_resourceid"></a>\_ResourceId
Egenskapen **\_ResourceID** innehåller en unik identifierare för resursen som posten är associerad med. Detta ger dig en standard egenskap som används för att begränsa din fråga till endast poster från en viss resurs eller för att koppla samman data över flera tabeller.

För Azure-resurser är värdet för **_ResourceId** [URL: en för Azure-resurs-ID](../../azure-resource-manager/templates/template-functions-resource.md). Egenskapen är för närvarande begränsad till Azure-resurser, men den kommer att utökas till resurser utanför Azure, till exempel lokala datorer.

> [!NOTE]
> Vissa data typer har redan fält som innehåller Azure-resurs-ID eller minst delar av det som prenumerations-ID. Dessa fält bevaras för bakåtkompatibilitet, men vi rekommenderar att du använder _ResourceId för att utföra kors korrelation eftersom det blir mer konsekvent.

### <a name="examples"></a>Exempel
Följande fråga ansluter prestanda-och händelse data för varje dator. Den visar alla händelser med ID _101_ och processor belastning över 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Följande fråga ansluter _AzureActivity_ -poster med _SecurityEvent_ -poster. Den visar alla aktivitets åtgärder med användare som har loggat in på de här datorerna.

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

Följande fråga analyserar **_ResourceId** och aggregerar fakturerade data volymer per Azure-prenumeration.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Använd dessa `union withsource = tt *` frågor sparsamt eftersom det är dyrt att köra genomsökningar över data typer.

## <a name="_isbillable"></a>\_fakturerbar
Egenskapen **\_fakturerbar** anger om inmatade data är fakturerbara. Data med **\_fakturerbar** som är lika med _falskt_ samlas in kostnads fritt och debiteras inte ditt Azure-konto.

### <a name="examples"></a>Exempel
Använd följande fråga om du vill hämta en lista över datorer som skickar fakturerings data typer:

> [!NOTE]
> Använd frågor med `union withsource = tt *` sparsamt eftersom det är dyrt att köra sökningar över data typer. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Detta kan utökas för att returnera antalet datorer per timme som skickar fakturerings data typer:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
Egenskapen **\_BilledSize** anger storleken i byte på data som ska faktureras till ditt Azure-konto om **\_fakturerbar** är true.


### <a name="examples"></a>Exempel
Om du vill se storleken på fakturerbara händelser per dator använder du egenskapen `_BilledSize` som anger storleken i byte:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Om du vill se storleken på fakturerbara händelser per prenumeration använder du följande fråga:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Om du vill se storleken på fakturerbara händelser per resurs grupp använder du följande fråga:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Om du vill se antalet händelser som matas in per dator använder du följande fråga:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Om du vill se antalet fakturerbara händelser per dator, använder du följande fråga: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Om du vill se antalet fakturerbara data typer från en speciell dator använder du följande fråga:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur [Azure Monitor loggdata lagras](../log-query/log-query-overview.md).
- Få en lektion om att [skriva logg frågor](../../azure-monitor/log-query/get-started-queries.md).
- Få en lektion om att [koppla tabeller i logg frågor](../../azure-monitor/log-query/joins.md).
