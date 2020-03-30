---
title: Skapa diagram och diagram från Azure Monitor-loggfrågor | Microsoft-dokument
description: Beskriver olika visualiseringar i Azure Monitor för att visa dina loggdata på olika sätt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670329"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Skapa diagram och diagram från Azure Monitor-loggfrågor

> [!NOTE]
> Du bör slutföra [avancerade aggregeringar i Azure Monitor-loggfrågor](advanced-aggregations.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här artikeln beskrivs olika visualiseringar i Azure Monitor för att visa dina loggdata på olika sätt.

## <a name="charting-the-results"></a>Kartlägga resultaten
Börja med att granska hur många datorer det finns per operativsystem under den senaste timmen:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Som standard visas resultaten som en tabell:

![Tabell](media/charts/table-display.png)

Om du vill få en bättre vy väljer du **Diagram**och väljer alternativet **Cirkel** för att visualisera resultaten:

![Cirkeldiagram](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Tidsscheman
Visa medelvärdet, 50:e och 95:e percentilen processortid i lagerplatser på 1 timme. Frågan genererar flera serier och du kan sedan välja vilken serie som ska visas i tidsschemat:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Välj visningsalternativet **för linjediagram:**

![Linjediagram](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Referenslinje

En referensrad kan hjälpa dig att enkelt identifiera om måttet har överskridit ett visst tröskelvärde. Om du vill lägga till en linje i ett diagram utökar du datauppsättningen med en konstant kolumn:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Referenslinje](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Flera dimensioner
Flera uttryck i `by` satsen `summarize` för att skapa flera rader i resultaten, en för varje kombination av värden.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

När du visar resultatet som ett diagram används `by` den första kolumnen från satsen. I följande exempel visas ett staplat stapeldiagram med _EventID._ Dimensioner måste `string` vara av typen, så i det här exemplet kastas _EventID_ till strängen. 

![EventID för stapeldiagram](media/charts/charts-and-diagrams-multiDimension1.png)

Du kan växla mellan genom att välja listrutan med kolumnnamnet. 

![Kontotyp för stapeldiagram](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda [Kusto-frågespråket](/azure/kusto/query/) med Azure Monitor-loggdata:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad frågeskrivning](advanced-query-writing.md)
- [Går](joins.md)
