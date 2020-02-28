---
title: Skapa diagram och diagram från Azure Monitor logg frågor | Microsoft Docs
description: Beskriver olika visualiseringar i Azure Monitor för att visa dina loggdata på olika sätt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670329"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Skapa diagram och diagram från Azure Monitor logg frågor

> [!NOTE]
> Du bör slutföra [avancerade agg regeringar i Azure Monitor logg frågor](advanced-aggregations.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här artikeln beskrivs olika visualiseringar i Azure Monitor att visa dina loggdata på olika sätt.

## <a name="charting-the-results"></a>Diagramerar resultaten
Börja med att granska hur många datorer det finns per operativ system under den senaste timmen:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Som standard visas resultatet som en tabell:

![Tabell](media/charts/table-display.png)

För att få en bättre vy väljer du **diagram**och väljer alternativet **cirkel** för att visualisera resultaten:

![Cirkel diagram](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
Visa genomsnitts-, 50-och 95-percentiler för processor tid på lager platser om 1 timme. Frågan genererar flera serier och du kan sedan välja vilken serie som ska visas i tids diagrammet:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Välj visnings alternativ för **linje** diagram:

![Linjediagram](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Referens linje

En referens rad kan hjälpa dig att enkelt identifiera om måttet överskred ett särskilt tröskelvärde. Om du vill lägga till en rad i ett diagram utökar du data uppsättningen med en konstant kolumn:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Referens linje](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Flera dimensioner
Flera uttryck i `by`-satsen i `summarize` skapa flera rader i resultatet, en för varje kombination av värden.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

När du visar resultatet som ett diagram, använder den den första kolumnen från `by`-satsen. I följande exempel visas ett stående stapeldiagram med hjälp av _EventID._ Dimensionerna måste vara av `string` typ, så i det här exemplet omvandlas _EventID_ till sträng. 

![EventID för stapeldiagram](media/charts/charts-and-diagrams-multiDimension1.png)

Du kan växla mellan genom att markera List rutan med kolumn namnet. 

![Stapeldiagram för stapeldiagram](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda [Kusto-frågespråket](/azure/kusto/query/) med Azure Monitor loggdata:

- [Sträng åtgärder](string-operations.md)
- [Datum-och tids åtgärder](datetime-operations.md)
- [Agg regerings funktioner](aggregations.md)
- [Avancerade agg regeringar](advanced-aggregations.md)
- [JSON och data strukturer](json-data-structures.md)
- [Avancerad fråge utskrift](advanced-query-writing.md)
- [Kopplingar](joins.md)
