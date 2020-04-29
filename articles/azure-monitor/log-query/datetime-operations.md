---
title: Arbeta med datum tids värden i Azure Monitor logg frågor | Microsoft Docs
description: Beskriver hur du arbetar med datum-och tids data i Azure Monitor logg frågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655386"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Arbeta med datum tids värden i Azure Monitor logg frågor

> [!NOTE]
> Du bör slutföra [Kom igång med Analytics Portal](get-started-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Den här artikeln beskriver hur du arbetar med datum-och tids data i Azure Monitor logg frågor.


## <a name="date-time-basics"></a>Datum/tid-grunder
Frågespråket för Kusto har två huvud data typer kopplade till datum och tider: datetime och TimeSpan. Alla datum anges i UTC. Även om flera datetime-format stöds, är ISO8601-formatet föredra. 

Tidsintervallen uttrycks som en decimal följt av en tidsenhet:

|skrift   | tidsenhet    |
|:---|:---|
|d           | day          |
|h           | timme         |
|m           | minut       |
|s           | sekund       |
|millisekund          | tiden  |
|latens | latens  |
|mellanrummet        | nanosekund   |

Du kan skapa datetime-data genom att omvandla en sträng `todatetime` med operatorn. Om du till exempel vill granska VM-pulsslag som skickats under en specifik tidsram använder du `between` operatorn för att ange ett tidsintervall.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Ett annat vanligt scenario är att jämföra ett datetime-värde för närvarande. Om du till exempel vill visa alla pulsslag under de senaste två minuterna kan du använda `now` operatorn tillsammans med ett TimeSpan som motsvarar två minuter:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Det finns också en genväg till den här funktionen:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Den kortaste och mest läsbara metoden som använder `ago` operatorn:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Anta att du i stället för att känna till start-och slut tid vet du start tid och varaktighet. Du kan skriva om frågan på följande sätt:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Konvertera tidsenheter
Du kanske vill uttrycka ett datum/tid-värde eller TimeSpan i en annan tidsenhet än standardvärdet. Om du till exempel granskar fel händelser från de senaste 30 minuterna och behöver en beräknad kolumn som visar hur lång tid sedan händelsen inträffade:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo` Kolumnen innehåller värden som: "00:09:31.5118992", vilket innebär att de är formaterade som tt: mm: SS. fffffff. Om du vill formatera dessa värden till `numver` minuter sedan start tiden delar du värdet med "1 minut":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agg regeringar och Bucket med tidsintervall
Ett annat vanligt scenario är att du behöver få statistik under en viss tids period inom en viss tids kornig het. I det här scenariot `bin` kan en operator användas som en del av en sammanfatta-sats.

Använd följande fråga för att hämta antalet händelser som inträffat var 5: e minut under den senaste halvtimmen:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Den här frågan genererar följande tabell:  

|TimeGenerated (UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Ett annat sätt att skapa buckets resultat är att använda funktioner, till exempel `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Den här frågan ger följande resultat:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7 136|
|2018-07-29T00:00:00.000|12 315|
|2018-07-30T00:00:00.000|16 847|
|2018-07-31T00:00:00.000|12 616|
|2018-08-01T00:00:00.000|5 416|


## <a name="time-zones"></a>Tids zoner
Eftersom alla datetime-värden uttrycks i UTC är det ofta användbart att konvertera dessa värden till den lokala tids zonen. Använd till exempel den här beräkningen för att konvertera UTC till PST-tider:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Relaterade funktioner

| Kategori | Funktion |
|:---|:---|
| Konvertera data typer | [ToDateTime](/azure/kusto/query/todatetimefunction)  [ToTimeSpan](/azure/kusto/query/totimespanfunction)  |
| Avrunda värde till lager plats storlek | [plats](/azure/kusto/query/binfunction) |
| Hämta ett visst datum eller en angiven tid | [sedan](/azure/kusto/query/agofunction) [nu](/azure/kusto/query/nowfunction)   |
| Hämta en del av värdet | [datetime_part](/azure/kusto/query/datetime-partfunction) [getMonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getYear](/azure/kusto/query/getyearfunction) [DAYOFMONTH](/azure/kusto/query/dayofmonthfunction) [DAYOFWEEK](/azure/kusto/query/dayofweekfunction) [DAYOFYEAR](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Hämta ett relativt datum värde  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [EndOfMonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [StartOfMonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda [Kusto-frågespråket](/azure/kusto/query/) med Azure Monitor loggdata:

- [Strängåtgärder](string-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad frågeskrivning](advanced-query-writing.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)
