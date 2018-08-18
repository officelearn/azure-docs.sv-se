---
title: Arbeta med datum-tid-värdena i Azure Log Analytics-frågor | Microsoft Docs
description: Beskriver hur du arbetar med datum och tid-data i Log Analytics-frågor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 833548a4bfca83a8ee6971f05a4f308cc54d5b5d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190439"
---
# <a name="working-with-date-time-values-in-log-analytics-queries"></a>Arbeta med datum-tid-värdena i Log Analytics-frågor

> [!NOTE]
> Bör du genomföra [Kom igång med Analytics-portalen](get-started-analytics-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

Den här artikeln beskriver hur du arbetar med datum och tid-data i Log Analytics-frågor.


## <a name="date-time-basics"></a>Datum tid grunderna
Frågespråket i Log Analytics har två huvudsakliga datatyper som är associerade med datum och tid: datetime och tidsintervallet. Alla datum uttrycks i UTC. Finns stöd för flera datum/tid-format är att föredra ISO8601-format. 

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

Datum och tid kan skapas med instruktionsvideor en sträng med hjälp av den `todatetime` operator. Till exempel för att granska de virtuella datorn pulsslag skickas i en viss tidsperiod, kan du använda den [mellan operatorn](https://docs.loganalytics.io/docs/Language-Reference/Scalar-operators/between-operator) vilket är praktiskt att ange ett tidsintervall...

```OQL
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Ett annat vanligt scenario är att jämföra ett datetime aktuella. Till exempel om du vill se alla pulsslag under de senaste två minuterna, du kan använda den `now` operatör tillsammans med timespan som representerar två minuter:

```OQL
Heartbeat
| where TimeGenerated > now() - 2m
```

En genväg är också tillgängligt för den här funktionen:
```OQL
Heartbeat
| where TimeGenerated > now(-2m)
```

Den kortaste och mest läsbart metoden genom att använda den `ago` operator:
```OQL
Heartbeat
| where TimeGenerated > ago(2m)
```

Anta att i stället för att känna till start- och -tid, vet du starttiden och varaktigheten. Du kan skriva om frågan på följande sätt:

```OQL
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Konvertera tidsenheter
Det kan vara praktiskt att uttrycka en datetime eller timespan i en tidsenhet än standardvärdet. Exempel: Anta att du granskar felhändelser från de senaste 30 minuterna och behöver en beräknad kolumn som visar hur länge sedan händelsen har inträffat:

```OQL
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Du kan se den _timeAgo_ kolumnen innehåller värden som: ”00:09:31.5118992”, vilket innebär att de är formaterade som hh:mm:ss.fffffff. Om du vill formatera dessa värden till den _numver_ minuter sedan starttiden helt enkelt dela detta värde med ”1 minut”:

```OQL
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregeringar och bucket tidsintervall
Ett annat vanligt scenario är behovet av att få statistik över en viss tidsperiod i ett visst tidsintervall. För detta, en `bin` operatorn kan användas som en del av en summarize-sats.

Använd följande fråga för att få antalet händelser som inträffat var femte minut under de senaste halvtimme:

```OQL
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Detta resulterar i följande tabell:  
|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Ett annat sätt att skapa buckets med resultat är att använda funktioner, till exempel `startofday`:

```OQL
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Detta ger följande resultat:

|tidsstämpel|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416  |


## <a name="time-zones"></a>Tidszoner
Eftersom alla datum/tid-värden uttrycks i UTC, är det ofta bra att konvertera dessa till den lokala tidszonen. Till exempel använda den här beräkningen för att konvertera UTC till PST gånger:

```OQL
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Relaterade funktioner

| Kategori | Funktion |
|:---|:---|
| Konvertera-datatyper | [ToDateTime](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/todatetime())[totimespan  ](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/totimespan())  |
| Avrunda värdet till diskretiseringsstorlek | [bin](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/bin()) |
| Hämta ett visst datum eller tid | [sedan](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago()) [nu](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/now())   |
| Hämta en del av värde | [datetime_part](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/datetime_part()) [getmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getmonth()) [monthofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/monthofyear()) [getyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getyear()) [dag i månaden](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofmonth()) [dayofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofweek()) [dayofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofyear()) [weekofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/weekofyear()) |
| Hämta ett datum i förhållande till värde  | [endofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofday()) [endofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofweek()) [endofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofmonth()) [endofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofyear()) [startofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofday()) [startofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofweek()) [startofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofmonth()) [startofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofyear()) |

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda Log Analytics-frågespråket:

- [Strängåtgärder](string-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad fråga skrivning](advanced-query-writing.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)