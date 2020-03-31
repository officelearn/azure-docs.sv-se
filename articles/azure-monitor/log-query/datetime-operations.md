---
title: Arbeta med datumtidsvärden i Azure Monitor-loggfrågor| Microsoft-dokument
description: Beskriver hur du arbetar med datum- och tidsdata i Azure Monitor-loggfrågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655386"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Arbeta med datumtidsvärden i Azure Monitor-loggfrågor

> [!NOTE]
> Du bör slutföra [Kom igång med Analytics-portalen](get-started-portal.md) [och Komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här artikeln beskrivs hur du arbetar med datum- och tidsdata i Azure Monitor-loggfrågor.


## <a name="date-time-basics"></a>Grunderna i datumtid
Kusto-frågespråket har två huvuddatatyper som är associerade med datum och tider: datumtid och tidsintervall. Alla datum uttrycks i UTC. Flera datetime-format stöds, men ISO8601-formatet är att föredra. 

Tidsintervall uttrycks som en decimal följt av en tidsenhet:

|Stenografi   | tidsenhet    |
|:---|:---|
|d           | day          |
|h           | timme         |
|m           | minut       |
|s           | sekund       |
|Ms          | Millisekund  |
|mikrosekt | mikrosekt  |
|Kryssa        | Nanosekund   |

Datumtider kan skapas genom att `todatetime` casta en sträng med operatorn. Om du till exempel vill granska den virtuella datorns `between` pulsslag som skickas inom en viss tidsram använder du operatorn för att ange ett tidsintervall.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Ett annat vanligt scenario är att jämföra en datetime med nuet. Om du till exempel vill visa alla pulsslag under `now` de senaste två minuterna kan du använda operatorn tillsammans med ett tidsintervall som representerar två minuter:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Det finns även en genväg för den här funktionen:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Den kortaste och mest läsbara `ago` metoden använder dock operatören:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Anta att i stället för att veta start- och sluttid, vet du starttiden och varaktigheten. Du kan skriva om frågan på följande sätt:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Konvertera tidsenheter
Du kanske vill uttrycka en datumtid eller tidsintervall i en annan tidsenhet än standard. Om du till exempel granskar felhändelser från de senaste 30 minuterna och behöver en beräknad kolumn som visar hur länge sedan händelsen inträffade:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Kolumnen `timeAgo` innehåller värden som: "00:09:31.5118992", vilket innebär att de är formaterade som hh:mm:ss.fffffff. Om du vill formatera dessa `numver` värden till minuternas minuter sedan starttiden delar du det värdet med "1 minut":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregeringar och bucketing efter tidsintervall
Ett annat vanligt scenario är behovet av att få statistik under en viss tidsperiod i en viss tidskorn. I det här `bin` fallet kan en operator användas som en del av en sammanfattningssats.

Använd följande fråga för att få antalet händelser som inträffade var femte minut under den senaste halvtimmen:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Den här frågan ger följande tabell:  

|TimeGenerated (UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Ett annat sätt att skapa resultatknår `startofday`är att använda funktioner, till exempel:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Den här frågan ger följande resultat:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Tidszoner
Eftersom alla datumvärden uttrycks i UTC är det ofta användbart att konvertera dessa värden till den lokala tidszonen. Använd till exempel den här beräkningen för att konvertera UTC till PST-tider:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Relaterade funktioner

| Kategori | Funktion |
|:---|:---|
| Konvertera datatyper | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Avrundat värde till lagerplatsstorlek | [Bin](/azure/kusto/query/binfunction) |
| Hämta ett visst datum eller en viss tid | [sedan](/azure/kusto/query/agofunction) [nu](/azure/kusto/query/nowfunction)   |
| Få en del av värdet | [datetime_part](/azure/kusto/query/datetime-partfunction) [fåmånadsmånader](/azure/kusto/query/monthofyearfunction) [fåårsdag](/azure/kusto/query/getyearfunction) [dagmånads](/azure/kusto/query/dayofmonthfunction) [getmonth](/azure/kusto/query/getmonthfunction) [veckodag](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [veckovecka i år](/azure/kusto/query/weekofyearfunction) |
| Hämta ett relativt datumvärde  | [endofday](/azure/kusto/query/endofdayfunction) [slutet av veckan](/azure/kusto/query/endofweekfunction) slutet [avmånmåndagen](/azure/kusto/query/endofmonthfunction) [slutet av året](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) start [ofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda [Kusto-frågespråket](/azure/kusto/query/) med Azure Monitor-loggdata:

- [Strängåtgärder](string-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad frågeskrivning](advanced-query-writing.md)
- [Går](joins.md)
- [Diagram](charts.md)
