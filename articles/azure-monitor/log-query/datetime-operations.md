---
title: Arbeta med datum-tid-värdena i Azure Monitor loggfrågor | Microsoft Docs
description: Beskriver hur du arbetar med datum och tid-data i Azure Monitor log-frågor.
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
ms.openlocfilehash: 402511ba3c45e8bd12cb7f92ecd54f6084c8ada2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112365"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Arbeta med datum-tid-värdena i Azure Monitor log-frågor

> [!NOTE]
> Bör du genomföra [Kom igång med Analytics-portalen](get-started-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Den här artikeln beskriver hur du arbetar med datum och tid-data i Azure Monitor log-frågor.


## <a name="date-time-basics"></a>Datum tid grunderna
Kusto-frågespråk har två huvudsakliga datatyper som är associerade med datum och tid: datetime och tidsintervallet. Alla datum uttrycks i UTC. Finns stöd för flera datum/tid-format är att föredra ISO8601-format. 

Tidsintervallen uttrycks som ett decimaltal följt av en tidsenhet:

|Snabbformat   | tidsenhet    |
|:---|:---|
|d           | dag          |
|h           | timme         |
|m           | minut       |
|S           | sekund       |
|ms          | millisekund  |
|databehandlingsnoder | databehandlingsnoder  |
|skalstreck        | nanosekunder   |

Datum och tid kan skapas med instruktionsvideor en sträng med hjälp av den `todatetime` operator. Till exempel för att granska de virtuella datorn pulsslag skickas i en viss tidsperiod, använda den `between` operator för att ange ett tidsintervall.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Ett annat vanligt scenario är att jämföra ett datetime aktuella. Till exempel om du vill se alla pulsslag under de senaste två minuterna, du kan använda den `now` operatör tillsammans med timespan som representerar två minuter:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

En genväg är också tillgängligt för den här funktionen:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Den kortaste och mest läsbart metoden genom att använda den `ago` operator:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Anta att i stället för att känna till start- och -tid, vet du starttiden och varaktigheten. Du kan skriva om frågan på följande sätt:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Konvertera tidsenheter
Du kanske vill att uttrycka en datetime eller timespan i en tidsenhet än standardvärdet. Till exempel om du granskar felhändelser från de senaste 30 minuterna och behöver en beräknad kolumn som visar hur länge sedan händelsen har inträffat:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Den `timeAgo` kolumnen innehåller värden som: ”00:09:31.5118992”, vilket innebär att de är formaterade som hh:mm:ss.fffffff. Om du vill formatera dessa värden till den `numver` minuter sedan starttiden dela detta värde med ”1 minut”:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregeringar och bucket tidsintervall
Ett annat vanligt scenario är behovet av att få statistik över en viss tidsperiod i ett visst tidsintervall. Det här scenariot kan en `bin` operatorn kan användas som en del av en summarize-sats.

Använd följande fråga för att få antalet händelser som inträffat var femte minut under de senaste halvtimme:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Den här frågan resulterar i följande tabell:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Ett annat sätt att skapa buckets med resultat är att använda funktioner, till exempel `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Den här frågan ger följande resultat:

|tidsstämpel|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Tidszoner
Eftersom alla datum/tid-värden uttrycks i UTC, är det ofta bra att konvertera dessa värden till den lokala tidszonen. Till exempel använda den här beräkningen för att konvertera UTC till PST gånger:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Relaterade funktioner

| Category | Funktion |
|:---|:---|
| Konvertera-datatyper | [ToDateTime](/azure/kusto/query/todatetimefunction)[totimespan](/azure/kusto/query/totimespanfunction)  |
| Avrunda värdet till diskretiseringsstorlek | [bin](/azure/kusto/query/binfunction) |
| Hämta ett visst datum eller tid | [sedan](/azure/kusto/query/agofunction) [nu](/azure/kusto/query/nowfunction)   |
| Hämta en del av värde | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dag i månaden](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Hämta ett värde för relativt datum  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda den [Kusto-frågespråket](/azure/kusto/query/) logga data med Azure Monitor:

- [Strängåtgärder](string-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad fråga skrivning](advanced-query-writing.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)