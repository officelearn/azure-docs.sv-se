---
title: Avancerade frågor i Azure Log Analytics | Microsoft Docs
description: Den här artikeln innehåller en självstudie för att skriva frågor i Log Analytics Analytics-portalen.
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
ms.openlocfilehash: 72c151fec0637822411f8cac44f4e13a8df96445
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190367"
---
# <a name="writing-advanced-queries-in-log-analytics"></a>Skriva avancerade frågor i Log Analytics

> [!NOTE]
> Bör du genomföra [Kom igång med Analytics-portalen](get-started-analytics-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

## <a name="reusing-code-with-let"></a>Återanvända kod med låter
Använd `let` att tilldela resultaten till en variabel och referera till det senare i frågan:

```OQL
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Du kan också tilldela variabler konstanta värden. Det ger stöd för en metod för att ställa in parametrar för de fält som du behöver ändra varje gång du kör frågan. Ändra de här parametrarna efter behov. Till exempel för att beräkna ledigt utrymme och ledigt minne (i percentiler) under en viss tidsperiod:

```OQL
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Detta gör det enkelt att ändra på början av sluttid nästa gång du kör frågan.

### <a name="local-functions-and-parameters"></a>Lokala funktioner och parametrar
Använd `let` -uttryck för att skapa funktioner som kan användas i samma fråga. Till exempel definiera en funktion som tar ett datetime-fält (i UTC-format) och konverterar den till ett standardformat i USA. 

```OQL
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="functions"></a>Functions
Du kan spara en fråga med ett funktionsalias så att den kan refereras av andra frågor. Till exempel returnerar följande standard fråga alla saknade säkerhetsuppdateringar som rapporteras i den sista dagen:

```OQL
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Du kan spara den här frågan som en funktion och ge den ett alias som _security_updates_last_day_. Du kan sedan använda den i en annan fråga för att söka efter SQL-relaterade nödvändiga säkerhetsuppdateringar:

```OQL
security_updates_last_day | where Title contains "SQL"
```

Om du vill spara en fråga som en funktion, Välj den **spara** knappen i portalen och ändra **Spara som** till _funktionen_. Funktionens alias kan innehålla bokstäver, siffror och understreck, men måste börja med en bokstav eller ett understreck.

> [!NOTE]
> Det är möjligt i Log Analytics-frågor, men för närvarande inte för Application Insights frågor att spara en funktion.


## <a name="print"></a>Skriva ut
`print` Returnerar en tabell med en enda kolumn och en enskild rad som visar resultatet av en beräkning. Det här används ofta i fall där du behöver en enkel calcuation. Till exempel vill hitta den aktuella tiden i PST och lägga till en kolumn med EST:

```OQL
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>DataTable
`datatable` När du vill definiera en uppsättning data. Du anger ett schema och en uppsättning värden och sedan skicka tabellen i alla andra element i fråga. Till exempel för att skapa en tabell med RAM-användning och beräkna sina medelvärdet per timme:

```OQL
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

DataTable konstruktioner är också mycket användbara när du skapar en uppslagstabell. Till exempel för att mappa tabelldata, till exempel händelse-ID från den _SecurityEvent_ tabellen för att händelsetyper visas någon annanstans, skapa en uppslagstabell med händelsetyper genom att använda `datatable` och delta i denna datatable med  _SecurityEvent_ data:

```OQL
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda Log Analytics-frågespråket:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)