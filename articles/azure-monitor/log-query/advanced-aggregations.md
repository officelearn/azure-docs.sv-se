---
title: Avancerade agg regeringar i Azure Monitor logg frågor | Microsoft Docs
description: Beskriver några av de mer avancerade agg regerings alternativen som är tillgängliga för Azure Monitor logg frågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: dba058dce09e958a2ae769d927a5569fb3e42113
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324564"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Avancerade agg regeringar i Azure Monitor logg frågor

> [!NOTE]
> Du bör slutföra [agg regeringar i Azure Monitor frågor](./aggregations.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här artikeln beskrivs några av de mer avancerade agg regerings alternativen som är tillgängliga för Azure Monitor frågor.

## <a name="generating-lists-and-sets"></a>Genererar listor och uppsättningar
Du kan använda `makelist` för att pivotera data efter ordningen på värdena i en viss kolumn. Du kanske till exempel vill utforska de vanligaste order händelserna i dina datorer. Du kan i princip pivotera data i ordningen EventIDs på varje dator. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Dator|list_EventID|
|---|---|
| DATOR1 | [704, 701, 1501, 1500, 1085, 704, 704, 701] |
| DATOR2 | [326 105 302 301 300 102] |
| ... | ... |

`makelist` genererar en lista i den ordning som data skickades till den. Om du vill sortera händelser från äldsta till nyaste använder du `asc` i order-instruktionen i stället för `desc` . 

Det är också användbart att skapa en lista med enbart distinkta värden. Detta kallas för en _uppsättning_ och kan genereras med `makeset` :

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Dator|list_EventID|
|---|---|
| DATOR1 | [704, 701, 1501, 1500, 1085] |
| DATOR2 | [326 105 302 301 300 102] |
| ... | ... |

Till `makelist` exempel `makeset` fungerar också med beställda data och skapar matriserna baserat på ordningen på de rader som skickas till den.

## <a name="expanding-lists"></a>Expandera listor
Den inverterade åtgärden för `makelist` eller `makeset` är `mvexpand` , som utökar en lista med värden för att separera rader. Den kan utökas över valfritt antal dynamiska kolumner, både JSON och array. Du kan till exempel kontrol lera *pulsslags* tabellen för lösningar som skickar data från datorer som har skickat ett pulsslag under den senaste timmen:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Dator | Lösningar | 
|--------------|----------------------|
| DATOR1 | "säkerhet", "uppdateringar", "changeTracking" |
| DATOR2 | "säkerhet", "uppdateringar" |
| computer3 | "program mot skadlig kod", "changeTracking" |
| ... | ... |

Används `mvexpand` för att visa varje värde i en separat rad i stället för en kommaavgränsad lista:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Dator | Lösningar | 
|--------------|----------------------|
| DATOR1 | Bullet |
| DATOR1 | uppdateringar |
| DATOR1 | ChangeTracking |
| DATOR2 | Bullet |
| DATOR2 | uppdateringar |
| computer3 | Skadlig kod |
| computer3 | ChangeTracking |
| ... | ... |


Du kan sedan använda `makelist` igen för att gruppera objekten och den här gången visas listan över datorer per lösning:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Lösningar | list_Computer |
|--------------|----------------------|
| Bullet | ["DATOR1", "DATOR2"] |
| uppdateringar | ["DATOR1", "DATOR2"] |
| ChangeTracking | ["DATOR1", "computer3"] |
| Skadlig kod | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Hantering av saknade lager platser
Ett användbart program i `mvexpand` är behovet av att fylla standardvärden i för saknade lager platser. Anta till exempel att du söker efter drift tiden för en viss dator genom att utforska dess pulsslag. Du vill också se källan till pulsslaget som finns i kolumnen _kategori_ . Normalt skulle vi använda en enkel sammanfattnings instruktion på följande sätt:

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

I dessa resultat, trots att Bucket som är associerat med "2017-06-06T19:00:00Z" saknas, eftersom det inte finns några pulsslags data för den timmen. Använd `make-series` funktionen för att tilldela tomma buckets-värde. Då skapas en rad för varje kategori med två extra mat ris kolumner, en för värden och en för matchande tidsbuckets:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategori | count_ | TimeGenerated |
|---|---|---|
| Direkt agent | [15, 60, 0, 55, 60, 57, 60,...] | ["2017-06-06T17:00:00.0000000 Z", "2017-06-06T18:00:00.0000000 Z", "2017-06-06T19:00:00.0000000 Z", "2017-06-06T20:00:00.0000000 Z", "2017-06-06T21:00:00.0000000 Z",...] |
| ... | ... | ... |

Det tredje elementet i *count_* matrisen är 0 som förväntat och det finns en matchande tidsstämpel på "2017-06-06T19:00:00.0000000 z" i _TimeGenerated_ -matrisen. Det här mat ris formatet är svårt att läsa. Används `mvexpand` för att expandera matriserna och producera samma format resultat som genereras av `summarize` :

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



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Begränsa resultaten till en uppsättning element:,, `let` `makeset` `toscalar` , `in`
Ett vanligt scenario är att välja namnen på vissa entiteter baserat på en uppsättning villkor och sedan filtrera en annan data uppsättning till den uppsättningen entiteter. Du kan till exempel hitta datorer där uppdateringar saknas och identifiera IP-adresser som de här datorerna anropar för att:


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

Se andra lektioner för att använda [Kusto-frågespråket](/azure/kusto/query/) med Azure Monitor loggdata:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar]()
- [JSON och datastrukturer](json-data-structures.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)

