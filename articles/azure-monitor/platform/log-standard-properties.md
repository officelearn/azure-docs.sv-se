---
title: Standardegenskaper i Azure Monitor-loggposter | Microsoft-dokument
description: Beskriver egenskaper som är gemensamma för flera datatyper i Azure Monitor-loggar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274481"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Standardegenskaper i Azure Monitor Logs
Data i Azure Monitor Logs [lagras som en uppsättning poster i antingen en Log Analytics-arbetsyta eller Application Insights-program](../log-query/logs-structure.md), var och en med en viss datatyp som har en unik uppsättning egenskaper. Många datatyper har standardegenskaper som är vanliga för flera typer. I den här artikeln beskrivs dessa egenskaper och exempel på hur du kan använda dem i frågor.

> [!NOTE]
> Vissa standardegenskaper visas inte i schemavyn eller intellisense i Logganalys, och de visas inte i frågeresultat om du inte uttryckligen anger egenskapen i utdata.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated och tidsstämpel
Egenskaperna **TimeGenerated** (Log Analytics workspace) och **tidsstämpel** (Application Insights application) innehåller datum och tid då posten skapades av datakällan. Mer information [finns i information om inmatning av data i Azure Monitor.](data-ingestion-time.md)

**TimeGenerated** och **tidsstämpel** ger en gemensam egenskap som ska användas för filtrering eller sammanfattning efter tid. När du väljer ett tidsintervall för en vy eller instrumentpanel i Azure-portalen används TimeGenerated eller tidsstämpel för att filtrera resultaten. 

### <a name="examples"></a>Exempel

Följande fråga returnerar antalet felhändelser som skapats för varje dag föregående vecka.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Följande fråga returnerar antalet undantag som har skapats för varje dag föregående vecka.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TidKomlig
Egenskapen ** \_TimeReceived** innehåller datum och tid då posten togs emot av Azure Monitor-inmatningspunkten i Azure-molnet. Detta kan vara användbart för att identifiera svarstidsproblem mellan datakällan och molnet. Ett exempel skulle vara ett nätverksproblem som orsakar en fördröjning med data som skickas från en agent. Mer information [finns i information om inmatning av data i Azure Monitor.](data-ingestion-time.md)

Följande fråga ger den genomsnittliga svarstiden per timme för händelseposter från en agent. Detta inkluderar tiden från agenten till molnet och och den totala tiden för posten att vara tillgänglig för loggfrågor.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Typ och itemType
Egenskaperna **Typ** (Log Analytics workspace) och **itemType** (Application Insights application) innehåller namnet på den tabell som posten hämtades från som också kan ses som posttyp. Den här egenskapen är användbar i frågor som kombinerar poster `search` från flera tabeller, till exempel de som använder operatorn, för att skilja mellan poster av olika typer. **$table** kan användas i stället **för Typ** på vissa ställen.

### <a name="examples"></a>Exempel
Följande fråga returnerar antalet poster efter typ som samlats in under den senaste timmen.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_Itemid
Egenskapen ** \_ItemId** innehåller en unik identifierare för posten.


## <a name="_resourceid"></a>\_ResourceId
Egenskapen ** \_ResourceId** innehåller en unik identifierare för den resurs som posten är associerad med. Detta ger dig en standardegenskap som du kan använda för att begränsa frågan till endast poster från en viss resurs eller för att koppla relaterade data över flera tabeller.

För Azure-resurser är värdet **för _ResourceId** [Azure-resurs-ID-URL:en](../../azure-resource-manager/templates/template-functions-resource.md). Egenskapen är för närvarande begränsad till Azure-resurser, men den utökas till resurser utanför Azure, till exempel lokala datorer.

> [!NOTE]
> Vissa datatyper har redan fält som innehåller Azure-resurs-ID eller åtminstone delar av det som prenumerations-ID. Även om dessa fält behålls för bakåtkompatibilitet, rekommenderas att använda _ResourceId för att utföra korskorrelation eftersom det blir mer konsekvent.

### <a name="examples"></a>Exempel
Följande fråga kopplar prestanda- och händelsedata för varje dator. Den visar alla händelser med ett ID på _101_ och processoranvändning över 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Följande fråga sammanfogar _AzureActivity-poster_ med _SecurityEvent-poster._ Den visar alla aktivitetsåtgärder med användare som har loggat in på dessa datorer.

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

Följande frågetolkar **_ResourceId** och sammanställer fakturerade datavolymer per Azure-prenumeration.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Använd `union withsource = tt *` dessa frågor sparsamt eftersom sökningar över datatyper är dyra att köra.

## <a name="_isbillable"></a>\_Kan faktureras
Egenskapen ** \_IsBillable** anger om intjesterade data kan faktureras. Data med ** \_IsBillable** lika med _false_ samlas in gratis och faktureras inte till ditt Azure-konto.

### <a name="examples"></a>Exempel
Om du vill hämta en lista över datorer som skickar fakturerade datatyper använder du följande fråga:

> [!NOTE]
> Använd frågor `union withsource = tt *` med sparsamt som skannar över datatyper är dyra att köra. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Detta kan utökas för att returnera antalet datorer per timme som skickar fakturerade datatyper:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_FakturerasStorlek
Egenskapen ** \_FaktureradSize** anger storleken i byte av data som ska faktureras till ditt Azure-konto om ** \_IsBillable** är sant.


### <a name="examples"></a>Exempel
Om du vill se storleken på fakturerbara `_BilledSize` händelser som intas per dator använder du egenskapen som anger storleken i byte:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Om du vill se storleken på fakturerbara händelser som intas per prenumeration använder du följande fråga:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Om du vill se storleken på fakturerbara händelser som intas per resursgrupp använder du följande fråga:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Om du vill se antalet händelser som intas per dator använder du följande fråga:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Om du vill se antalet fakturerbara händelser som intas per dator använder du följande fråga: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Om du vill visa antalet fakturerbara datatyper från en viss dator använder du följande fråga:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur [Azure Monitor-loggdata lagras](../log-query/log-query-overview.md).
- Få en lektion om [att skriva loggfrågor](../../azure-monitor/log-query/get-started-queries.md).
- Få en lektion om [att gå med i tabeller i loggfrågor](../../azure-monitor/log-query/joins.md).
