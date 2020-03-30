---
title: Avancerade aggregeringar i Azure Monitor-loggfrågor| Microsoft-dokument
description: Beskriver några av de mer avancerade aggregeringsalternativen som är tillgängliga för Azure Monitor-loggfrågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: e5dc290a40342e0797001dde6cab90e12dd5cf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662186"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Avancerade aggregeringar i Azure Monitor-loggfrågor

> [!NOTE]
> Du bör slutföra [aggregeringar i Azure Monitor-frågor](./aggregations.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här artikeln beskrivs några av de mer avancerade aggregeringsalternativen som är tillgängliga för Azure Monitor-frågor.

## <a name="generating-lists-and-sets"></a>Generera listor och uppsättningar
Du kan `makelist` använda för att pivotera data efter värdeordningen i en viss kolumn. Du kanske till exempel vill utforska de vanligaste orderhändelserna på dina datorer. Du kan i huvudsak pivotera data efter ordningen för EventIDs på varje dator. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Dator|list_EventID|
|---|---|
| dator1 | [704,701,1501,1500,1085,704,704,701] |
| dator2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist`genererar en lista i den ordning som data skickades till den. Om du vill sortera händelser `asc` från äldsta till `desc`nyaste använder du i orderutdraget i stället för . 

Det är också användbart att skapa en lista med bara distinkta värden. Detta kallas en _uppsättning_ och `makeset`kan genereras med:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Dator|list_EventID|
|---|---|
| dator1 | [704,701,1501,1500,1085] |
| dator2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist`Liksom `makeset` fungerar också med ordnade data och genererar matriserna baserat på ordningen på de rader som skickas in i den.

## <a name="expanding-lists"></a>Utöka listor
Den omvända `makelist` funktionen `makeset` `mvexpand`av eller är , som utökar en lista med värden för att separera rader. Det kan expandera över valfritt antal dynamiska kolumner, både JSON och array. Du kan till exempel kontrollera tabellen *Pulsslag* efter lösningar som skickar data från datorer som har skickat pulsslag under den senaste timmen:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Dator | Lösningar | 
|--------------|----------------------|
| dator1 | "säkerhet", "uppdateringar", "changeTracking" |
| dator2 | "säkerhet", "uppdateringar" |
| dator3 | "antiMalware", "changeTracking" |
| ... | ... |

Används `mvexpand` för att visa varje värde på en separat rad i stället för en kommaavgränsad lista:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Dator | Lösningar | 
|--------------|----------------------|
| dator1 | "säkerhet" |
| dator1 | "uppdateringar" |
| dator1 | "changeTracking" |
| dator2 | "säkerhet" |
| dator2 | "uppdateringar" |
| dator3 | "antiMalware" |
| dator3 | "changeTracking" |
| ... | ... |


Du kan `makelist` sedan använda igen för att gruppera objekt tillsammans, och den här gången se listan över datorer per lösning:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Lösningar | list_Computer |
|--------------|----------------------|
| "säkerhet" | ["dator1", "dator2"] |
| "uppdateringar" | ["dator1", "dator2"] |
| "changeTracking" | ["dator1", "dator3"] |
| "antiMalware" | ["dator3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Hantering av papperskorgar som saknas
Ett användbart `mvexpand` program av är behovet av att fylla standardvärden i för saknade lagerplatser. Anta till exempel att du letar efter drifttiden för en viss dator genom att utforska dess pulsslag. Du vill också se källan till pulsslag _category_ som finns i kategorikolumnen. Normalt skulle vi använda en enkel sammanfatta uttalande enligt följande:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Kategori | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direkt agent | 2017-06-06T17:00:00Z | 15 |
| Direkt agent | 2017-06-06T18:00:00Z | 60 |
| Direkt agent | 2017-06-06T20:00:00Z | 55 |
| Direkt agent | 2017-06-06T21:00:00Z | 57 |
| Direkt agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

I dessa resultat om bucketen som är associerad med "2017-06-06T19:00:00Z" saknas eftersom det inte finns några pulsslagsdata för den timmen. Använd `make-series` funktionen för att tilldela ett standardvärde till tomma buckets. Detta genererar en rad för varje kategori med två extra matriskolumner, en för värden och en för matchande tidssegment:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategori | count_ | TimeGenerated |
|---|---|---|
| Direkt agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00.00000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Det tredje elementet i *den count_* matrisen är en 0 som förväntat, och det finns en matchande tidsstämpel på "2017-06-06T19:00:00.0000000Z" i _matrisen TimeGenerated._ Detta arrayformat är svårt att läsa dock. Används `mvexpand` för att expandera matriserna och producera `summarize`samma formatutdata som genereras av:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Kategori | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direkt agent | 2017-06-06T17:00:00Z | 15 |
| Direkt agent | 2017-06-06T18:00:00Z | 60 |
| Direkt agent | 2017-06-06T19:00:00Z | 0 |
| Direkt agent | 2017-06-06T20:00:00Z | 55 |
| Direkt agent | 2017-06-06T21:00:00Z | 57 |
| Direkt agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Begränsa resultaten till en uppsättning `let` `makeset`element: , , `toscalar``in`
Ett vanligt scenario är att välja namnen på vissa specifika entiteter baserat på en uppsättning villkor och sedan filtrera en annan datauppsättning ner till den uppsättningen entiteter. Du kan till exempel hitta datorer som är kända för att ha saknade uppdateringar och identifiera IPs som dessa datorer ropade till:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Nästa steg

Se andra lektioner för att använda [Kusto-frågespråket](/azure/kusto/query/) med Azure Monitor-loggdata:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Går](joins.md)
- [Diagram](charts.md)
