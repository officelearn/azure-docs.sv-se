---
title: Logga aviserings frågor i Azure Monitor | Microsoft Docs
description: Innehåller rekommendationer om hur du skriver effektiva frågor för logg aviseringar i Azure Monitor uppdateringar och en process för att konvertera befintliga frågor.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667796"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Logga aviserings frågor i Azure Monitor
[Varnings regler som baseras på Azure Monitor loggar](alerts-unified-log.md) körs med jämna mellanrum, så du bör se till att de är skrivna för att minimera omkostnader och svars tider. Den här artikeln innehåller rekommendationer om hur du skriver effektiva frågor för logg aviseringar och en process för att konvertera befintliga frågor. 

## <a name="types-of-log-queries"></a>Typer av logg frågor
[Logg frågor i Azure Monitor](../log-query/log-query-overview.md) börjar med antingen en tabell eller en [Sök](/azure/kusto/query/searchoperator) -eller [union](/azure/kusto/query/unionoperator) -operator.

Följande fråga är till exempel begränsad till tabellen _SecurityEvent_ och söker efter ett särskilt händelse-ID. Det här är den enda tabell som frågan måste bearbeta.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Frågor som börjar med `search` eller `union` gör att du kan söka i flera kolumner i en tabell eller till och med flera tabeller. I följande exempel visas flera metoder för att söka på termen _minne_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Även om `search` och `union` är användbara under data utforskningen, är det mindre effektivt att söka igenom termer över hela data modellen än att använda en tabell eftersom de måste genomsöka över flera tabeller. Eftersom frågor i varnings regler körs med jämna mellanrum, kan det leda till en alltför hög omkostnader för att lägga till fördröjning i aviseringen. På grund av den här omkostnaderna bör frågor för logg aviserings regler i Azure alltid börja med en tabell för att definiera en tydlig omfattning, vilket förbättrar både frågans prestanda och relevansen för resultatet.

## <a name="unsupported-queries"></a>Frågor som inte stöds
Från och med 11 januari 2019, skapa eller ändra logg varnings regler som använder `search`, eller `union`-operatörer kommer inte att stödja i Azure Portal. Om dessa operatorer används i en varnings regel returneras ett fel meddelande. Befintliga varnings regler och aviserings regler som skapas och redige ras med Log Analytics API påverkas inte av den här ändringen. Du bör fortfarande överväga att ändra eventuella varnings regler som använder dessa typer av frågor, i syfte att förbättra effektiviteten.  

Logg varnings regler som använder [kors resurs frågor](../log-query/cross-workspace-query.md) påverkas inte av den här ändringen eftersom frågor om globala resurser använder `union`, vilket begränsar frågans omfång till vissa resurser. Detta är inte detsamma som `union *` som inte kan användas.  Följande exempel är giltigt i en logg aviserings regel:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Frågan över resurser](../log-query/cross-workspace-query.md) i logg aviseringar stöds i det nya [scheduledQueryRules-API: et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Som standard använder Azure Monitor den [äldre Log Analytics varnings-API: n](api-alerts.md) för att skapa nya logg aviserings regler från Azure Portal, såvida du inte växlar från [äldre API för logg aviseringar](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Efter växeln blir det nya API: t standardvärdet för nya varnings regler i Azure Portal och du kan skapa frågor om aviserings regler för kors resurs. Du kan skapa [frågor om kors resursfrågor](../log-query/cross-workspace-query.md) utan att göra växeln med hjälp av arm- [mallen för scheduledQueryRules-API](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – men den här varnings regeln kan hanteras även om [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och inte från Azure Portal.

## <a name="examples"></a>Exempel
I följande exempel finns logg frågor som använder `search` och `union` och innehåller steg som du kan använda för att ändra dessa frågor för användning med varnings regler.

### <a name="example-1"></a>Exempel 1
Du vill skapa en logg aviserings regel med hjälp av följande fråga som hämtar prestanda information med hjälp av `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Om du vill ändra den här frågan börjar du med att använda följande fråga för att identifiera tabellen som egenskaperna tillhör:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Resultatet av den här frågan visar att egenskapen _CounterName_ kommer från tabellen _perf_ . 

Du kan använda det här resultatet för att skapa följande fråga som du skulle använda för aviserings regeln:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Exempel 2
Du vill skapa en logg aviserings regel med hjälp av följande fråga som hämtar prestanda information med hjälp av `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Om du vill ändra den här frågan börjar du med att använda följande fråga för att identifiera tabellen som egenskaperna tillhör:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Resultatet av den här frågan visar att egenskapen _ObjectName_ och _CounterName_ kommer från tabellen _perf_ . 

Du kan använda det här resultatet för att skapa följande fråga som du skulle använda för aviserings regeln:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Exempel 3

Du vill skapa en logg aviserings regel med hjälp av följande fråga som använder både `search` och `union` för att hämta prestanda information: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Om du vill ändra den här frågan börjar du med att använda följande fråga för att identifiera tabellen som egenskaperna i den första delen av frågan tillhör: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Resultatet av den här frågan visar att alla dessa egenskaper kommer från tabellen _perf_ . 

Använd nu kommandot `union` med `withsource` för att identifiera vilken käll tabell som har bidragit till varje rad.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Resultatet av den här frågan visar att dessa egenskaper också kommer från tabellen _perf_ . 

Du kan använda dessa resultat för att skapa följande fråga som du skulle använda för aviserings regeln: 

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
Du vill skapa en logg aviserings regel med hjälp av följande fråga som sammanbinder resultatet av två `search`s frågor:

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
 

Om du vill ändra frågan börjar du med att använda följande fråga för att identifiera den tabell som innehåller egenskaperna på vänster sida av kopplingen: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Resultatet visar att egenskaperna på vänster sida av kopplingen tillhör _SecurityEvent_ -tabellen. 

Använd nu följande fråga för att identifiera tabellen som innehåller egenskaperna på höger sida av kopplingen: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Resultatet visar att egenskaperna till höger i kopplingen tillhör pulsslags tabellen. 

Du kan använda dessa resultat för att skapa följande fråga som du skulle använda för aviserings regeln: 


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
- Lär dig mer om [logg aviseringar](alerts-log.md) i Azure Monitor.
- Lär dig mer om [logg frågor](../log-query/log-query-overview.md).

