---
title: Aggregeringar i Azure Monitor logga frågor | Microsoft Docs
description: Beskriver aggregeringsfunktioner i Azure Monitor log-frågor som erbjuder ett bra sätt att analysera dina data.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 864e9586082ed95bf17135414ec4b879e3034ace
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267350"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Aggregeringar i Azure Monitor log-frågor

> [!NOTE]
> Bör du genomföra [Kom igång med Analytics-portalen](get-started-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Den här artikeln beskriver aggregeringsfunktioner i Azure Monitor log-frågor som erbjuder ett bra sätt att analysera dina data. Alla funktioner använder den `summarize` operator som du skapar en tabell med aggregerade resultatet av indatatabellen.

## <a name="counts"></a>Antal

### <a name="count"></a>count
Räkna antalet rader i resultatmängden efter eventuella filter har använts. I följande exempel returneras det totala antalet rader i den _Perf_ tabell från de senaste 30 minuterna. Resultatet returneras i en kolumn med namnet *count_* om du inte tilldelar den ett visst namn:


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

En tidsdiagram visualisering kan vara användbart att se en trend över tid:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Utdata från det här exemplet visar perf antalet poster i trendlinjen i 5 minuters intervall:

![Antal trend](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount dcountif
Använd `dcount` och `dcountif` att räkna distinkta värden i en viss kolumn. Följande fråga utvärderar hur många olika datorer skickat pulsslag under den senaste timmen:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Om du vill räkna endast de Linux-datorer som har skickat pulsslag, använda `dcountif`:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Utvärdera undergrupper
Om du vill utföra ett antal eller andra aggregeringar på undergrupper i dina data kan du använda den `by` nyckelord. Till exempel vill räkna antalet olika Linux-datorer som har skickat pulsslag i varje land/region:

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


Om du vill analysera ännu mindre undergrupper av dina data, lägger du till ytterligare en kolumn som ska den `by` avsnittet. Du kanske vill räkna distinkta datorer från varje land/region per OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentiler och varians
När du utvärderar numeriska värden, det är vanligt att genomsnittlig dem med hjälp av `summarize avg(expression)`. Medelvärden påverkas av extrema värden som utmärker endast några ärenden. Om du vill åtgärda problemet, kan du använda mindre känsliga funktioner som `median` eller `variance`.

### <a name="percentile"></a>Percentil
Hitta medianvärdet i `percentile` funktion med ett värde för att ange den: e percentilen:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Du kan också ange olika percentilerna för att få ett aggregerat resultat för varje:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Detta kan visa att en dator CPU: er har liknande median värden, men vissa är konstant runt medianvärdet, andra datorer har rapporterat mycket lägre och högre CPU-värden vilket innebär att de råkade ut toppar.

### <a name="variance"></a>Varians
För att utvärdera direkt variansen för ett värde, använder du standardavvikelse och varians metoder:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Ett bra sätt att analysera stabiliteten för CPU-användningen är att kombinera stdev med median beräkningen:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Se andra lektioner för att använda den [Kusto-frågespråket](/azure/kusto/query/) logga data med Azure Monitor:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad fråga skrivning](advanced-query-writing.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)
