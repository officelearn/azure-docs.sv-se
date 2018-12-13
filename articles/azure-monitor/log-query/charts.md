---
title: Skapar diagram och diagram från Azure Log Analytics-frågor | Microsoft Docs
description: Beskriver olika visualiseringar i Azure Log Analytics för att visa dina data på olika sätt.
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
ms.openlocfilehash: 049e50f5800194c4126003f7e5ff7ae60b3de768
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186233"
---
# <a name="creating-charts-and-diagrams-from-log-analytics-queries"></a>Skapar diagram och diagram från Log Analytics-frågor

> [!NOTE]
> Bör du genomföra [avancerade aggregeringar i Log Analytics-frågor](advanced-aggregations.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Den här artikeln beskrivs olika visualiseringar i Azure Log Analytics för att visa dina data på olika sätt.

## <a name="charting-the-results"></a>Diagram resultaten
Starta genom att granska hur många datorer som finns under den senaste timmen per operativsystem:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Som standard visar resultat som en tabell:

![Tabell](media/charts/table-display.png)

Välj för att få en bättre vy **diagram**, och välj den **cirkel** alternativet för att visualisera resultat:

![Cirkeldiagram](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
Visa medelvärde, 50: e och 95: e percentilerna för processortid i lagerplatser 1 timme. Frågan genererar flera serier och sedan kan du välja vilka serien för att visa i diagrammet tid:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Välj den **rad** diagrammets Visningsalternativ:

![Linjediagram](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Referenslinje

Med hjälp av en referensrad kan du lätt att identifiera om måttet överskrider ett visst tröskelvärde. Utöka datauppsättningen med en konstant kolumn för att lägga till en rad i ett diagram:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Referenslinje](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Flera dimensioner
Flera uttryck i den `by` -satsen för `summarize` skapa flera rader i resultatet, en för varje kombination av värden.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

När du visar resultatet som ett diagram, används den första kolumnen från den `by` satsen. I följande exempel visas ett staplat diagram med de _händelse-ID._ Dimensioner måste vara av `string` skriver det i det här exemplet på _EventID_ är att konvertera till en sträng. 

![Stapeldiagram händelse-ID](media/charts/charts-and-diagrams-multiDimension1.png)

Du kan växla mellan genom att välja listrutan med kolumnnamnet. 

![Stapeldiagram AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda Log Analytics-frågespråket:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad fråga skrivning](advanced-query-writing.md)
- [Kopplingar](joins.md)