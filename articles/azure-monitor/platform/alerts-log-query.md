---
title: Loggaviseringsfrågor i Azure Monitor | Microsoft-dokument
description: Ger rekommendationer om hur du skriver effektiva frågor för loggaviseringar i Azure Monitor-uppdateringar och en process för att konvertera befintliga frågor.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667796"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Loggaviseringsfrågor i Azure Monitor
[Varningsregler baserade på Azure Monitor-loggar](alerts-unified-log.md) körs med jämna mellanrum, så du bör se till att de skrivs för att minimera omkostnader och svarstid. Den här artikeln innehåller rekommendationer om hur du skriver effektiva frågor för loggaviseringar och en process för att konvertera befintliga frågor. 

## <a name="types-of-log-queries"></a>Typer av loggfrågor
[Loggfrågor i Azure Monitor](../log-query/log-query-overview.md) börjar med antingen en tabell eller en [sök-](/azure/kusto/query/searchoperator) eller [unionsoperator.](/azure/kusto/query/unionoperator)

Följande fråga begränsas till till _säkerhetshändelsetabellen_ och söker efter specifikt händelse-ID. Det här är den enda tabell som frågan måste bearbeta.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Frågor som börjar `search` `union` med eller låter dig söka över flera kolumner i en tabell eller till och med flera tabeller. Följande exempel visar flera metoder för att söka i termen _Minne:_

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Även `search` `union` om och är användbara under datautforskning, söka termer över hela datamodellen, de är mindre effektiva än att använda en tabell eftersom de måste skanna över flera tabeller. Eftersom frågor i varningsregler körs med jämna mellanrum kan detta resultera i överdriven overhead-lägger till svarstid till aviseringen. På grund av den här omkostnader bör frågor om loggaviseringsregler i Azure alltid börja med en tabell för att definiera ett tydligt omfång, vilket förbättrar både frågeprestanda och resultatens relevans.

## <a name="unsupported-queries"></a>Frågor som inte stöds
Från och med 11 januari 2019 stöds inte `search`att `union` skapa eller ändra loggvarningsregler som använder eller operatörer i Azure-portalen. Om du använder dessa operatorer i en varningsregel returneras ett felmeddelande. Befintliga varningsregler och varningsregler som skapats och redigerats med Log Analytics API påverkas inte av den här ändringen. Du bör fortfarande överväga att ändra alla varningsregler som använder dessa typer av frågor men för att förbättra deras effektivitet.  

Loggaviseringsregler med [frågor mellan resurser](../log-query/cross-workspace-query.md) påverkas inte av den här `union`ändringen eftersom resursfrågor använder flera resurser , vilket begränsar frågeomfånget till specifika resurser. Detta motsvarar inte `union *` som inte kan användas.  Följande exempel skulle vara giltigt i en loggvarningsregel:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Fråga över flera resurser](../log-query/cross-workspace-query.md) i loggaviseringar stöds i det nya [schemalagdaQueryRules API:et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Som standard använder Azure Monitor det [äldre Log Analytics Alert API](api-alerts.md) för att skapa nya loggaviseringsregler från Azure-portalen, såvida du inte växlar från äldre [loggvarningar API](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Efter växeln blir det nya API:et standard för nya varningsregler i Azure-portalen och du kan skapa regler för frågeloggar mellan resurser. Du kan skapa frågeloggvarningsregler för flera resurser utan att växla genom att använda [ARM-mallen för scheduledQueryRules API](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – men den här aviseringsregeln är hanterbar men [schemalagdQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och inte från Azure-portalen. [cross-resource query](../log-query/cross-workspace-query.md)

## <a name="examples"></a>Exempel
Följande exempel är loggfrågor `search` som `union` använder och och innehåller steg som du kan använda för att ändra dessa frågor för användning med varningsregler.

### <a name="example-1"></a>Exempel 1
Du vill skapa en loggaviseringsregel med hjälp `search`av följande fråga som hämtar prestandainformation med: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Om du vill ändra den här frågan börjar du med följande fråga för att identifiera den tabell som egenskaperna tillhör:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Resultatet av den här frågan visar att egenskapen _CounterName_ kom från _tabellen Perf._ 

Du kan använda det här resultatet för att skapa följande fråga som du skulle använda för varningsregeln:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Exempel 2
Du vill skapa en loggaviseringsregel med hjälp `search`av följande fråga som hämtar prestandainformation med: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Om du vill ändra den här frågan börjar du med följande fråga för att identifiera den tabell som egenskaperna tillhör:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Resultatet av den här frågan visar att egenskapen _ObjectName_ och _CounterName_ kom från _tabellen Perf._ 

Du kan använda det här resultatet för att skapa följande fråga som du skulle använda för varningsregeln:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Exempel 3

Du vill skapa en loggaviseringsregel med `search` `union` hjälp av följande fråga som använder båda och för att hämta prestandainformation: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Om du vill ändra den här frågan börjar du med följande fråga för att identifiera den tabell som egenskaperna i den första delen av frågan tillhör: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Resultatet av den här frågan skulle visa att alla dessa egenskaper kom från _tabellen Perf._ 

Använd `union` nu `withsource` med kommandot för att identifiera vilken källtabell som har bidragit med varje rad.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Resultatet av den här frågan visar att dessa egenskaper också kom från _tabellen Perf._ 

Du kan använda dessa resultat för att skapa följande fråga som du skulle använda för varningsregeln: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>Exempel 4
Du vill skapa en loggaviseringsregel med hjälp `search` av följande fråga som sammanfogar resultatet av två frågor:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

Om du vill ändra frågan börjar du med följande fråga för att identifiera tabellen som innehåller egenskaperna till vänster i kopplingen: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Resultatet anger att egenskaperna till vänster i kopplingen tillhör _tabellen SecurityEvent._ 

Använd nu följande fråga för att identifiera tabellen som innehåller egenskaperna till höger om kopplingen: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Resultatet indikerar att egenskaperna till höger om kopplingen tillhör tabellen Pulsslag. 

Du kan använda dessa resultat för att skapa följande fråga som du skulle använda för varningsregeln: 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [loggaviseringar](alerts-log.md) i Azure Monitor.
- Läs mer om [loggfrågor](../log-query/log-query-overview.md).

