---
title: Agg regeringar i Azure Monitor logg frågor | Microsoft Docs
description: Beskriver agg regerings funktioner i Azure Monitor logg frågor som erbjuder användbara sätt att analysera dina data.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670312"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agg regeringar i Azure Monitor logg frågor

> [!NOTE]
> Du bör slutföra [Kom igång med Analytics Portal](get-started-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här artikeln beskrivs agg regerings funktioner i Azure Monitor logg frågor som erbjuder användbara sätt att analysera dina data. Dessa funktioner fungerar med `summarize`-operatorn som skapar en tabell med sammanställda resultat för tabellen indatamängd.

## <a name="counts"></a>Antal

### <a name="count"></a>count
Räkna antalet rader i resultat uppsättningen efter att filter har tillämpats. I följande exempel returneras det totala antalet rader i _perf_ -tabellen från de senaste 30 minuterna. Resultatet returneras i en kolumn med namnet *count_* om du inte tilldelar det ett särskilt namn:


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

En timechart visualisering kan vara användbar för att se en trend över tid:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Utdata från det här exemplet visar trend linjen med antal prestanda poster i intervall om 5 minuter:

![Antal trender](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount, dcountif
Använd `dcount` och `dcountif` för att räkna distinkta värden i en viss kolumn. Följande fråga utvärderar hur många distinkta datorer som har skickat pulsslag under den senaste timmen:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Använd `dcountif`för att räkna antalet Linux-datorer som har skickat pulsslag:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Utvärderar under grupper
Om du vill utföra ett antal eller andra agg regeringar för under grupper i dina data använder du nyckelordet `by`. Om du till exempel vill räkna antalet distinkta Linux-datorer som har skickat pulsslag i varje land/region:

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


Om du vill analysera ännu mindre under grupper av dina data lägger du till ytterligare kolumn namn i `by` avsnittet. Du kanske till exempel vill räkna de distinkta datorerna från varje land/region per OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentiler och varians
När du utvärderar numeriska värden är en vanlig metod att beräkna dem med hjälp av `summarize avg(expression)`. Genomsnitt påverkas av extrema värden som enbart kännetecknar några få fall. För att lösa problemet kan du använda mindre känsliga funktioner, till exempel `median` eller `variance`.

### <a name="percentile"></a>Percentil
Du hittar median värdet genom att använda funktionen `percentile` med ett värde för att ange percentilen:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Du kan också ange olika percentiler för att få ett sammanställt resultat för varje:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Detta kan visa att vissa dator processorer har liknande median värden, men vissa är konstanta runt median, men andra datorer har rapporterat mycket lägre och högre processor värden, vilket innebär att de har haft höga mängder.

### <a name="variance"></a>Varians
Om du vill utvärdera var Ian sen för ett värde direkt använder du standard avvikelser och varians metoder:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Ett bra sätt att analysera CPU-användningens stabilitet är att kombinera STDAV med beräkningen av median:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Se andra lektioner för att använda [Kusto-frågespråket](/azure/kusto/query/) med Azure Monitor loggdata:

- [Sträng åtgärder](string-operations.md)
- [Datum-och tids åtgärder](datetime-operations.md)
- [Avancerade agg regeringar](advanced-aggregations.md)
- [JSON och data strukturer](json-data-structures.md)
- [Avancerad fråge utskrift](advanced-query-writing.md)
- [Kopplingar](joins.md)
- [Hierarkidiagram](charts.md)
