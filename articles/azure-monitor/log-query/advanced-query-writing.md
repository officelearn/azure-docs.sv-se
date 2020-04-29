---
title: Avancerade frågor i Azure Monitor | Microsoft Docs
description: Den här artikeln innehåller en själv studie kurs om hur du använder Analytics Portal för att skriva frågor i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670295"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Skriva avancerade frågor i Azure Monitor

> [!NOTE]
> Du bör slutföra [Kom igång med Azure Monitor Log Analytics](get-started-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Återanvända kod med Let
Använd `let` för att tilldela resultat till en variabel och referera till den senare i frågan:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Du kan också tilldela variabler konstanta värden. Detta stöder en metod för att ställa in parametrar för de fält som du behöver ändra varje gång du kör frågan. Ändra parametrarna efter behov. För att till exempel beräkna ledigt disk utrymme och ledigt minne (i percentiler) i ett angivet tidsintervall:

```Kusto
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

Detta gör det enkelt att ändra starten på slut tiden nästa gången du kör frågan.

### <a name="local-functions-and-parameters"></a>Lokala funktioner och parametrar
Använd `let` instruktioner för att skapa funktioner som kan användas i samma fråga. Du kan till exempel definiera en funktion som tar ett datum/tid-fält (i UTC-format) och konverterar det till ett standard format för USA. 

```Kusto
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

## <a name="print"></a>Skriv ut
`print`Returnerar en tabell med en enda kolumn och en enskild rad, som visar resultatet av en beräkning. Detta används ofta i fall där du behöver en enkel beräkning. Om du till exempel vill hitta den aktuella tiden i PST och lägga till en kolumn med EST:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>DataTable
`datatable`gör att du kan definiera en uppsättning data. Du kan ange ett schema och en uppsättning värden och sedan skicka tabellen till andra frågeuttryck. Till exempel för att skapa en tabell med RAM-användning och beräkna deras genomsnittliga värde per timme:

```Kusto
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

DataTable-konstruktioner är också mycket användbara när du skapar en uppslags tabell. Om du till exempel vill mappa tabell data, till exempel händelse-ID: n från tabellen _SecurityEvent_ , till händelse typer som anges någon annan stans, skapar du `datatable` en uppslags tabell med händelse typerna med hjälp av och ansluter till denna DataTable med _SecurityEvent_ -

```Kusto
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
Se andra lektioner för att använda [Kusto-frågespråket](/azure/kusto/query/) med Azure Monitor loggdata:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)
