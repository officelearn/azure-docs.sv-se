---
title: Standard kolumner i Azure Monitor logg poster | Microsoft Docs
description: Beskriver kolumner som är gemensamma för flera data typer i Azure Monitor loggar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: dc3d119479d2dce45b286463f3d6a76410220dd0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014228"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Standard kolumner i Azure Monitor loggar
Data i Azure Monitor loggar [lagras som en uppsättning poster i antingen en Log Analytics arbets yta eller ett Application Insights program](./data-platform-logs.md), var och en med en viss datatyp som har en unik uppsättning kolumner. Många data typer kommer att ha standard kolumner som är gemensamma för flera typer. Den här artikeln beskriver de här kolumnerna och innehåller exempel på hur du kan använda dem i frågor.

Arbets ytans program i Application Insights lagra sina data i en Log Analytics arbets yta och använda samma standard kolumner som andra tabeller i arbets ytan. Klassiska program lagrar sina data separat och har olika standard kolumner enligt vad som anges i den här artikeln.

> [!NOTE]
> Några av standard kolumnerna visas inte i diagramvyn eller IntelliSense i Log Analytics och de visas inte i frågeresultaten om du inte uttryckligen anger kolumnen i utdata.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated och tidsstämpel
Kolumnerna **TimeGenerated** (Log Analytics-arbetsyta) och **tidsstämpel** (Application Insights program) innehåller datum och tid då posten skapades av data källan. Mer information finns i [inmatnings tiden för logg data i Azure Monitor](data-ingestion-time.md) .

**TimeGenerated** och **tidsstämpel** tillhandahåller en gemensam kolumn som ska användas för filtrering eller sammanfattning per tid. När du väljer ett tidsintervall för en vy eller instrument panel i Azure Portal, används TimeGenerated eller timestamp för att filtrera resultaten. 

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
Kolumnen **\_ TimeReceived** innehåller datum och tid då posten togs emot av Azure Monitor inmatnings punkt i Azure-molnet. Detta kan vara användbart för att identifiera svars tids problem mellan data källan och molnet. Ett exempel är ett nätverks problem som orsakar en fördröjning med data som skickas från en agent. Mer information finns i [inmatnings tiden för logg data i Azure Monitor](data-ingestion-time.md) .

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
Kolumnerna **typ** (Log Analytics arbets yta) och **itemType** (Application Insights program) innehåller namnet på tabellen som posten hämtades från, som också kan betraktas som post typen. Den här kolumnen är användbar i frågor som kombinerar poster från flera tabeller, t. ex. sådana som använder `search` operatorn, för att skilja mellan olika typer av poster. **$Table** kan användas i stället för **typ** på vissa platser.

### <a name="examples"></a>Exempel
Följande fråga returnerar antalet poster efter den typ som samlats in den senaste timmen.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
Kolumnen **\_ Itemid** innehåller en unik identifierare för posten.


## <a name="_resourceid"></a>\_ResourceId
Kolumnen **\_ ResourceID** innehåller en unik identifierare för resursen som posten är associerad med. Detta ger dig en standard kolumn som du kan använda för att begränsa din fråga till endast poster från en viss resurs eller för att koppla samman relaterade data över flera tabeller.

För Azure-resurser är värdet för **_ResourceId** [URL: en för Azure-resurs-ID](../../azure-resource-manager/templates/template-functions-resource.md). Kolumnen är för närvarande begränsad till Azure-resurser, men den kommer att utökas till resurser utanför Azure, till exempel lokala datorer.

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

## <a name="_isbillable"></a>\_Fakturerbar
Kolumnen **\_ fakturerbar** anger om inmatade data är fakturerbara. Data med **\_ fakturerbar** som är lika med `false` samlas in kostnads fritt och debiteras inte ditt Azure-konto.

### <a name="examples"></a>Exempel
Använd följande fråga om du vill hämta en lista över datorer som skickar fakturerings data typer:

> [!NOTE]
> Använd frågor med `union withsource = tt *` sparsamhet som genomsökningar över data typer är dyra att köra. 

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
Kolumnen **\_ BilledSize** anger storleken i byte på data som ska faktureras till ditt Azure-konto om **\_ fakturerbar** är sant.


### <a name="examples"></a>Exempel
Om du vill se storleken på fakturerbara händelser per dator, använder du `_BilledSize` kolumnen som innehåller storleken i byte:

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
- Få en lektion om att [skriva logg frågor](../log-query/get-started-queries.md).
- Få en lektion om att [koppla tabeller i logg frågor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).