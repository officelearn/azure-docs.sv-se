---
title: Använd körningsprofilen för att utvärdera frågor i Azure Cosmos DB Gremlin API
description: Läs om hur du felsöker och förbättrar dina Gremlin-frågor med hjälp av körningsprofilsteget.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441855"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Använda stegen i körningsprofilen för att utvärdera Gremlin-frågor

Den här artikeln innehåller en översikt över hur du använder steget körningsprofil för Azure Cosmos DB Gremlin-API-grafdatabaser. Det här steget ger relevant information för felsökning och frågekörning, och den är kompatibel med alla Gremlin-frågor som kan utföras mot ett Cosmos DB Gremlin API-konto.

Om du vill använda det `executionProfile()` här steget lägger du bara till funktionsanropet i slutet av Gremlin-frågan. **Din Gremlin-fråga kommer att köras** och resultatet av åtgärden returnerar ett JSON-svarsobjekt med frågekörningsprofilen.

Ett exempel:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

När `executionProfile()` du har anropat steget kommer svaret att vara ett JSON-objekt som innehåller det utförda Gremlin-steget, den totala tiden det tog och en matris med Cosmos DB-runtime-operatorerna som satsen resulterade i.

> [!NOTE]
> Den här implementeringen för körningsprofil definieras inte i Apache Tinkerpop-specifikationen. Den är specifik för Implementeringen av Azure Cosmos DB Gremlin API.


## <a name="response-example"></a>Exempel på svar

Följande är ett kommenterat exempel på utdata som ska returneras:

> [!NOTE]
> Det här exemplet kommenteras med kommentarer som förklarar svarets allmänna struktur. Ett faktiskt exekveringProfilsvar innehåller inga kommentarer.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> Steget executionProfile kör Gremlin-frågan. Detta inkluderar `addV` `addE`stegen eller, vilket resulterar i att de ändringar som anges i frågan skapas. Därför debiteras även de begärandeenheter som genereras av Gremlin-frågan.

## <a name="execution-profile-response-objects"></a>Svarsobjekt för körningsprofil

Svaret från en funktionen executionProfile() ger en hierarki av JSON-objekt med följande struktur:
  - **Gremlin-åtgärdsobjekt**: Representerar hela Gremlin-åtgärden som utfördes. Innehåller följande egenskaper.
    - `gremlin`: Den uttryckliga Gremlin uttalande som utfördes.
    - `totalTime`: Den tid, i millisekunder, som utförandet av det steg som uppstått i. 
    - `metrics`: En matris som innehåller var och en av cosmos DB-körningsoperatorer som kördes för att uppfylla frågan. Den här listan sorteras i körningsordning.
    
  - **Cosmos DB runtime operatorer**: Representerar var och en av komponenterna i hela Gremlin-funktionen. Den här listan sorteras i körningsordning. Varje objekt innehåller följande egenskaper:
    - `name`: Operatörens namn. Det här är den typ av steg som utvärderades och kördes. Läs mer i tabellen nedan.
    - `time`: Tid, i millisekunder, som en viss operatör tog.
    - `annotations`: Innehåller ytterligare information som är specifik för operatorn som utfördes.
    - `annotations.percentTime`: Procentandel av den totala tid det tog att utföra den specifika operatorn.
    - `counts`: Antal objekt som returnerades från lagringslagret av den här operatorn. Detta finns i `counts.resultCount` skalärvärdet inom.
    - `storeOps`: Representerar en lagringsåtgärd som kan sträcka sig över en eller flera partitioner.
    - `storeOps.fanoutFactor`: Representerar antalet partitioner som den här specifika lagringsåtgärden har åtkomst till.
    - `storeOps.count`: Representerar antalet resultat som den här lagringsåtgärden returnerade.
    - `storeOps.size`: Representerar storleken i byte för resultatet av en viss lagringsåtgärd.

Cosmos DB Gremlin Runtime Operator|Beskrivning
---|---
`GetVertices`| Det här steget hämtar en förlagd uppsättning objekt från beständighetslagret. 
`GetEdges`| Det här steget hämtar kanterna som ligger intill en uppsättning hörn. Det här steget kan resultera i en eller flera lagringsåtgärder.
`GetNeighborVertices`| Det här steget hämtar de hörn som är anslutna till en uppsättning kanter. Kanterna innehåller partitionsnycklarna och ID:na för både käll- och målver.
`Coalesce`| Det här steget står för utvärderingen av två åtgärder när `coalesce()` Gremlin-steget körs.
`CartesianProductOperator`| Det här steget beräknar en kartesisk produkt mellan två datauppsättningar. Vanligtvis utförs när predikat `to()` eller `from()` används.
`ConstantSourceOperator`| Det här steget beräknar ett uttryck för att skapa ett konstant värde som ett resultat.
`ProjectOperator`| Det här steget förbereder och serialiserar ett svar med resultatet av föregående åtgärder.
`ProjectAggregation`| Det här steget förbereder och serialiserar ett svar för en aggregerad åtgärd.

> [!NOTE]
> Den här listan kommer att fortsätta att uppdateras när nya operatörer läggs till.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Exempel på hur du analyserar ett körningsprofilsvar

Följande är exempel på vanliga optimeringar som kan upptäckas med hjälp av svaret körningsprofil:
  - Blind fan-out fråga.
  - Ofiltrerad fråga.

### <a name="blind-fan-out-query-patterns"></a>Blinda utfläktningsfrågemönster

Anta följande körningsprofilsvar från ett **partitionerat diagram:**

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Följande slutsatser kan dras av den:
- Frågan är en enda ID-sökning, eftersom Gremlin-satsen följer mönstret `g.V('id')`.
- Att döma `time` av måttet verkar svarstiden för den här frågan vara hög eftersom den är [mer än 10 ms för en enda punktläsningsåtgärd](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Om vi tittar `storeOps` in i objektet, `fanoutFactor` `5`kan vi se att är , vilket innebär att [5 partitioner](https://docs.microsoft.com/azure/cosmos-db/partition-data) var nås av denna operation.

Som en slutsats av den här analysen kan vi fastställa att den första frågan är att komma åt fler partitioner än nödvändigt. Detta kan åtgärdas genom att ange partitioneringsnyckeln i frågan som ett predikat. Detta leder till mindre svarstid och lägre kostnad per fråga. Läs mer om [grafpartitionering](graph-partitioning.md). En mer optimal `g.V('tt0093640').has('partitionKey', 't1001')`fråga skulle vara .

### <a name="unfiltered-query-patterns"></a>Ofiltrerade frågemönster

Jämför följande två svar på körningsprofilen. För enkelhetens skull använder dessa exempel ett enda partitionerat diagram.

Den här första frågan hämtar alla `tweet` hörn med etiketten och hämtar sedan sina angränsande hörn:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Lägg märke till profilen för samma fråga, `has('lang', 'en')`men nu med ytterligare ett filter, innan du utforskar de intilliggande hörnen:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Dessa två frågor nådde samma resultat, men den första kräver fler begärandeenheter eftersom det behövs för att iterera en större inledande datauppsättning innan du frågar de intilliggande objekten. Vi kan se indikatorer på detta beteende när du jämför följande parametrar från båda svaren:
- Värdet `metrics[0].time` är högre i det första svaret, vilket indikerar att det här enskilda steget tog längre tid att lösa.
- Värdet `metrics[0].counts.resultsCount` är också högre i det första svaret, vilket indikerar att den ursprungliga arbetsdatauppsättningen var större.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [gremlin-funktionerna som stöds](gremlin-support.md) i Azure Cosmos DB. 
* Läs mer om [Gremlin-API:et i Azure Cosmos DB](graph-introduction.md).
