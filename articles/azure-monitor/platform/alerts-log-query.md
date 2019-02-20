---
title: Logga aviseringsfrågor i Azure Monitor | Microsoft Docs
description: Innehåller rekommendationer om hur du skriver effektiva frågor för loggaviseringar i Azure Monitor-uppdateringar och en process för att konvertera befintliga frågor.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: c50c1a111f037b74176b5ca2cf8af518b2d3ffa0
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429391"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Aviseringen loggfrågor i Azure Monitor
[Aviseringsregler baserat på Azure Monitor-loggar](alerts-unified-log.md) körs med jämna mellanrum, så bör du se till att de är skrivna för att minimera kostnader och svarstid. Den här artikeln innehåller rekommendationer om hur du skriver effektiva frågor för aviseringar och en process för att konvertera befintliga frågor. 

## <a name="types-of-log-queries"></a>Typer av loggfrågor
[Frågor i Log Analytics](../log-query/log-query-overview.md) börja med antingen en tabell eller en [search](/azure/kusto/query/searchoperator) eller [union](/azure/kusto/query/unionoperator) operator.

Till exempel följande fråga är begränsad till den _SecurityEvent_ tabell och söker efter specifika händelse-ID. Det här är den enda tabell som frågan måste bearbeta.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Frågor som börjar med `search` eller `union` gör att du kan söka i flera kolumner i en tabell eller även flera tabeller. I följande exempel visas flera metoder för att söka termen _minne_:

```Kusto
search "Memory"

search * | where == "Memory"

search ObjectName: "Memory"

search ObjectName == "Memory"

union * | where ObjectName == "Memory"
```
 

Även om `search` och `union` är användbart under datagranskning, söka villkoren över hela datamodellen, som de är mindre effektivt än att använda en tabell eftersom de måste genomsöka från flera tabeller. Eftersom frågor i Varningsregler körs med jämna mellanrum, kan detta resultera i höga omkostnader som att lägga till svarstid till aviseringen. Frågor för loggvarningsregler i Azure bör alltid börja med en tabell för att definiera en tydlig scope, vilket förbättrar både frågeprestanda och upplevelsen av resultaten på grund av det här arbetet.

## <a name="unsupported-queries"></a>Frågor som inte stöds
Startar januari 11,2019, skapa eller ändra loggaviseringsregler som använder `search`, eller `union` operatörer kan inte stöds i Azure-portalen. Med de här operatorerna i en varningsregel returneras ett felmeddelande. Befintliga Varningsregler och Varningsregler skapas och redigeras med Log Analytics API påverkas inte av den här ändringen. Du bör fortfarande ändra någon avisering regler som använder dessa typer av frågor om för att förbättra sin effektivitet.  

Logga in aviseringsregler med [mellan resurser frågor](../log-query/cross-workspace-query.md) påverkas inte av den här ändringen eftersom mellan resurser frågor använder `union`, vilket begränsar det specificerade omfånget till specifika resurser. Detta är inte detsamma `union *` som inte kan användas.  I följande exempel är giltiga i ett loggvarningsregel:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Fråga mellan resurser](../log-query/cross-workspace-query.md) i loggen för aviseringar stöds i den nya [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Azure Monitor använder som standard den [äldre Log Analytics avisering API](api-alerts.md) för att skapa nya log Varningsregler från Azure-portalen om du växlar från [äldre Log aviseringar API](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Efter växeln, nya API: et blir standard för nya aviseringsregler i Azure-portalen och du kan skapa mellan resurser fråga log aviseringar regler. Du kan skapa [mellan resurser fråga](../log-query/cross-workspace-query.md) loggar Varningsregler utan att göra växeln med hjälp av den [ARM-mall för scheduledQueryRules API](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – men den här aviseringsregeln kan hanteras dock [ scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och inte från Azure-portalen.

## <a name="examples"></a>Exempel
I följande exempel inkluderar loggfrågor som använder `search` och `union` samt åtgärder som du kan använda för att ändra de här frågorna för användning med Varningsregler.

### <a name="example-1"></a>Exempel 1
Du vill skapa en loggvarningsregel med följande fråga som hämtar prestanda informationen med hjälp av `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Om du vill ändra den här frågan börjar du med följande fråga för att identifiera tabellen som tillhör egenskaperna:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Resultatet av den här frågan visas som den _CounterName_ egenskapen kommer ifrån den _Perf_ tabell. 

Du kan använda det här resultatet för att skapa följande fråga som du skulle använda för regeln:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Exempel 2
Du vill skapa en loggvarningsregel med följande fråga som hämtar prestanda informationen med hjälp av `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Om du vill ändra den här frågan börjar du med följande fråga för att identifiera tabellen som tillhör egenskaperna:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Resultatet av den här frågan visas som den _ObjectName_ och _CounterName_ egenskapen kommer ifrån den _Perf_ tabell. 

Du kan använda det här resultatet för att skapa följande fråga som du skulle använda för regeln:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Exempel 3

Du vill skapa en loggvarningsregel med följande fråga som använder både `search` och `union` att hämta information om programprestanda: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Om du vill ändra den här frågan börjar du med följande fråga för att identifiera tabellen som egenskaper i den första delen av frågan tillhör: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Resultatet av den här frågan visar alla de här egenskaperna ifrån den _Perf_ tabell. 

Nu använda `union` med `withsource` kommando för att identifiera vilka källtabellen har bidragit med varje rad.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Resultatet av den här frågan visar att de här egenskaperna också kom från den _Perf_ tabell. 

Du kan använda dessa resultat för att skapa följande fråga som du skulle använda för regeln: 

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
Du vill skapa en loggvarningsregel med följande fråga som kopplar ihop resultaten av två `search` frågor:

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
 

Om du vill ändra frågan börjar du med hjälp av följande fråga för att identifiera den tabell som innehåller egenskaperna på vänster sida i kopplingen: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Resultatet anger att egenskaperna i vänster sida i kopplingen tillhör _SecurityEvent_ tabell. 

Använd nu följande fråga för att identifiera den tabell som innehåller egenskaperna på höger sida av kopplingen: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Resultatet anger att egenskaperna på höger sida i kopplingen tillhör pulsslag tabell. 

Du kan använda dessa resultat för att skapa följande fråga som du skulle använda för regeln: 


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
- Lär dig mer om [loggaviseringar](alerts-log.md) i Azure Monitor.
- Lär dig mer om [logga frågor](../log-query/log-query-overview.md).

