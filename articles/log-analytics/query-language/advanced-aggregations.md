---
title: Avancerade aggregeringar i Azure Log Analytics-frågor | Microsoft Docs
description: Beskriver några av de mer avancerade aggregering alternativen som är tillgängliga för Log Analytics-frågor.
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
ms.openlocfilehash: 661ff7c07ba2bb17eb5830b38bb39e1c3e80bb55
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45602916"
---
# <a name="advanced-aggregations-in-log-analytics-queries"></a>Avancerade aggregeringar i Log Analytics-frågor

> [!NOTE]
> Bör du genomföra [aggregeringar i Log Analytics-frågor](./aggregations.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Den här artikeln beskriver några av de mer avancerade aggregering alternativen som är tillgängliga för Log Analytics-frågor.

## <a name="generating-lists-and-sets"></a>Skapa listor och uppsättningar
Du kan använda `makelist` att pivotera data av ordningen värden i en viss kolumn. Du kanske vill utforska de vanligaste spelas upp ordning händelser på dina datorer. Du kan i princip pivotera data av ordningen EventIDs på varje dator. 

```KQL
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```
|Dator|list_EventID|
|---|---|
| Dator1 | [704,701,1501,1500,1085,704,704,701] |
| Dator2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` genererar en lista i den ordning som data skickades till den. Om du vill sortera händelser från äldsta till nyaste använda `asc` i order-instruktion i stället för `desc`. 

Det är också användbart för att skapa en lista över bara distinkta värden. Detta kallas en _ange_ och kan genereras med `makeset`:

```KQL
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```
|Dator|list_EventID|
|---|---|
| Dator1 | [704,701,1501,1500,1085] |
| Dator2 | [326,105,302,301,300,102] |
| ... | ... |

Som `makelist`, `makeset` också fungerar med sorterade data och genererar matriser baserat på ordningen de rader som har skickats till den.

## <a name="expanding-lists"></a>Utöka listor
Inverterade driften av `makelist` eller `makeset` är `mvexpand`, vilket utökar en lista med värden att avgränsa rader. Det kan expandera över valfritt antal dynamiska kolumner, såväl JSON-matris. Du kan exempelvis kontrollera den *pulsslag* tabellen för lösningar som skickar data från datorer som har skickat ett pulsslag under den senaste timmen:

```KQL
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```
| Dator | Lösningar | 
|--------------|----------------------|
| Dator1 | ”säkerhet”, ”uppdateringar”, ”changeTracking” |
| Dator2 | ”säkerhet”, ”uppdateringar” |
| Dator3 | ”program mot skadlig kod”, ”changeTracking” |
| ... | ... | ... |

Använd `mvexpand` att visa alla värden i en separat rad i stället för en CSV-lista:

Pulsslag | där TimeGenerated > ago(1) | Dator-projekt, dela (lösningar, ””,) | mvexpand lösningar
```
| Computer | Solutions | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... | ... |
```

Du kan sedan använda `makelist` igen objekt tillsammans att gruppera och nu se en lista över datorer per lösning:

```KQL
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```
|Lösningar | list_Computer |
|--------------|----------------------|
| ”säkerhet” | [”computer1”, ”computer2”] |
| ”uppdateringar” | [”computer1”, ”computer2”] |
| ”changeTracking” | [”computer1”, ”Dator3”] |
| ”program mot skadlig kod” | [”Dator3”] |
| ... | ... |

## <a name="handling-missing-bins"></a>Hantering av lagerplatser som saknas
En användbar tillämpning av `mvexpand` uppstår ofta behovet av att fylla standardvärdena i för saknas lagerplatser. Anta exempelvis att du letar efter drifttiden för en viss dator genom att utforska dess pulsslag. Vill du också se orsaken pulsslag som finns i den _kategori_ kolumn. Normalt använder vi ett enkelt summerar instruktionen på följande sätt:

```KQL
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```
| Kategori | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direktagent | 2017-06-06T17:00:00Z | 15 |
| Direktagent | 2017-06-06T18:00:00Z | 60 |
| Direktagent | 2017-06-06T20:00:00Z | 55 |
| Direktagent | 2017-06-06T21:00:00Z | 57 |
| Direktagent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

I den här resulterar dock en bucket som är associerade med ”2017-06-06T19:00:00Z” saknar eftersom det inte finns några heartbeat-data för den timmen. Använd den `make-series` funktionen för att tilldela ett standardvärde till tom buckets. Detta genererar en rad för varje kategori med två extra matris kolumner, en för värden och en matchande tid buckets:

```KQL
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategori | count_ | TimeGenerated |
|---|---|---|
| Direktagent | [15,60,0,55,60,57,60,...] | [”2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z”,...] |
| ... | ... | ... |

Det tredje elementet i den *count_* matrisen är 0 som förväntat och det finns en matchande tidsstämpeln för ”2017-06-06T19:00:00.0000000Z” i den _TimeGenerated_ matris. Den här matrisformat är svåra att läsa om. Använd `mvexpand` Expandera matriserna och producerar utdata som genereras av samma format `summarize`:

```KQL
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```
| Kategori | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direktagent | 2017-06-06T17:00:00Z | 15 |
| Direktagent | 2017-06-06T18:00:00Z | 60 |
| Direktagent | 2017-06-06T19:00:00Z | 0 |
| Direktagent | 2017-06-06T20:00:00Z | 55 |
| Direktagent | 2017-06-06T21:00:00Z | 57 |
| Direktagent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Begränsa resultaten till en uppsättning element: `let`, `makeset`, `toscalar`, `in`
Ett vanligt scenario är att välja namnen på vissa specifika entiteter som baseras på en uppsättning villkor och sedan filtrera en annan datauppsättning till den uppsättningen entiteter. Du kan till exempel söka efter datorer som saknar uppdateringar och identifiera IP-adresser som de här datorerna som påpekas att:


```KQL
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
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