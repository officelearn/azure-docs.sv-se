---
title: Aggregeringar i Azure Monitor-loggfrågor| Microsoft-dokument
description: Beskriver aggregeringsfunktioner i Azure Monitor-loggfrågor som erbjuder användbara sätt att analysera dina data.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670312"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Aggregeringar i Azure Monitor-loggfrågor

> [!NOTE]
> Du bör slutföra [Kom igång med Analytics-portalen](get-started-portal.md) [och Komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här artikeln beskrivs aggregeringsfunktioner i Azure Monitor-loggfrågor som erbjuder användbara sätt att analysera dina data. Dessa funktioner fungerar `summarize` alla med operatorn som producerar en tabell med aggregerade resultat av indatatabellen.

## <a name="counts"></a>Räknas

### <a name="count"></a>count
Räkna antalet rader i resultatuppsättningen efter att eventuella filter har tillämpats. I följande exempel returneras det totala antalet rader i _tabellen Perf_ från de senaste 30 minuterna. Resultatet returneras i en kolumn med namnet *count_* om du inte tilldelar det ett visst namn:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

En tidsschemavisualisering kan vara användbart för att se en trend över tid:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Utdata från det här exemplet visar trendraden perf-postantal i 5 minuters intervall:

![Räkna trend](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Använd `dcount` `dcountif` och för att räkna distinkta värden i en viss kolumn. Följande fråga utvärderar hur många olika datorer som har skickat pulsslag under den senaste timmen:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Om du bara vill räkna de `dcountif`Linux-datorer som skickade pulsslag använder du:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Utvärdera undergrupper
Om du vill utföra ett antal eller andra aggregeringar på undergrupper i dina data använder du nyckelordet. `by` Om du till exempel vill räkna antalet olika Linux-datorer som skickade pulsslag i varje land/region:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|USA    | 19                  |
|Kanada           | 3                   |
|Irland          | 0                   |
|Storbritannien   | 0                   |
|Nederländerna      | 2                   |


Om du vill analysera ännu mindre undergrupper av `by` dina data lägger du till ytterligare kolumnnamn i avsnittet. Du kanske till exempel vill räkna de olika datorerna från varje land/region per OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentiler och varians
Vid utvärdering av numeriska värden är `summarize avg(expression)`en vanlig praxis att genomsnitt dem med . Medelvärden påverkas av extrema värden som kännetecknar endast ett fåtal fall. Om du vill åtgärda problemet kan du `median` `variance`använda mindre känsliga funktioner som eller .

### <a name="percentile"></a>Percentil
Om du vill söka `percentile` efter medianvärdet använder du funktionen med ett värde för att ange percentilen:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Du kan också ange olika percentiler för att få ett aggregerat resultat för varje:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Detta kan visa att vissa datorprocessorer har liknande medianvärden, men medan vissa är stadiga runt medianen har andra datorer rapporterat mycket lägre och högre CPU-värden vilket innebär att de upplevde toppar.

### <a name="variance"></a>Varians
Om du vill utvärdera variansen direkt för ett värde använder du standardavvikelse- och variansmetoderna:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Ett bra sätt att analysera stabiliteten i CPU-användningen är att kombinera stdev med medianberäkningen:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Se andra lektioner för att använda [Kusto-frågespråket](/azure/kusto/query/) med Azure Monitor-loggdata:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad frågeskrivning](advanced-query-writing.md)
- [Går](joins.md)
- [Diagram](charts.md)
