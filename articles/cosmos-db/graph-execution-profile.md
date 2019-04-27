---
title: Utvärdera dina frågor med hjälp av funktionen för körning av profil för Azure Cosmos DB Gremlin-API
description: Lär dig hur du felsöker och förbättra dina Gremlin-frågor med hjälp av körningssteg för profilen.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888424"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Hur du använder profilen körningssteg för att utvärdera Gremlin-frågor

Den här artikeln innehåller en översikt över hur du använder profilen körningssteg för Gremlin-API för Azure Cosmos DB graph-databaser. Det här steget innehåller relevant information för felsökning och fråga optimeringar och den är kompatibel med alla Gremlin-frågor som kan utföras mot ett Gremlin-API för Cosmos DB-konto.

Om du vill använda det här steget, Lägg bara till den `executionProfile()` funktionsanropet i slutet av Gremlin-fråga. **Gremlin-fråga körs** och resultatet av åtgärden returnerar ett JSON-svarsobjekt med frågan körning profilen.

Exempel:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

När du anropar den `executionProfile()` steget svaret ska ha ett JSON-objekt som innehåller utförda Gremlin-steg och den totala tid det tog en matris med Cosmos DB runtime operatorer som instruktionen resulterade i.

> [!NOTE]
> Den här implementeringen för körning profilen har inte definierats i Apache Tinkerpop-specifikationen. Det är specifika för Azure Cosmos DB Gremlin API: er implementering.


## <a name="response-example"></a>Svar-exempel

Följande är ett kommenterat exempel på de utdata som returneras:

> [!NOTE]
> Det här exemplet markeras med kommentarer som förklarar den allmänna strukturen för svaret. Ett faktiska executionProfile svar innehåller inte några kommentarer.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
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
> ExecutionProfile-steget körs Gremlin-fråga. Detta inkluderar den `addV` eller `addE`steg, vilket resulterar i att skapa och genomför ändringarna som angavs i frågan. Programbegäran som genererats av Gremlin-fråga kommer därför också att debiteras.

## <a name="execution-profile-response-objects"></a>Svarsobjekt för körning av profil

Svaret på en funktion för executionProfile() resulterar i en hierarki av JSON-objekt med följande struktur:
  - **Gremlin-åtgärdsobjektet**: Representerar hela Gremlin-åtgärden som utfördes. Innehåller följande egenskaper.
    - `gremlin`: Den explicita Gremlin-instruktion som har utförts.
    - `totalTime`: Tiden, i millisekunder, som i samband med körningen av steget. 
    - `metrics`: En matris som innehåller var och en av Cosmos DB runtime-operatörer har körts för att slutföra frågan. Den här listan är sorterad i ordningen för körningen.
    
  - **Cosmos DB runtime operatörer**: Representerar var och en av komponenterna i hela Gremlin-åtgärden. Den här listan är sorterad i ordningen för körningen. Varje objekt innehåller följande egenskaper:
    - `name`: Namnet på operatorn. Det här är typ av steg som utvärderas och körs. Läs mer i tabellen nedan.
    - `time`: Mängden tid i millisekunder, som en viss operator tog.
    - `annotations`: Innehåller ytterligare information, som är specifika för den operator som har utförts.
    - `annotations.percentTime`: Den procentandel av den totala tiden som det tog för att utföra specifika operatorn.
    - `counts`: Antal objekt som returnerades från storage-skiktet av den här operatorn. Detta finns i den `counts.resultCount` skalärt värde inom.
    - `storeOps`: Representerar en lagringsåtgärd som kan spänna över en eller flera partitioner.
    - `storeOps.fanoutFactor`: Representerar antalet partitioner som används av den här åtgärden för specifika lagringsutrymmen.
    - `storeOps.count`: Representerar antalet resultat som returneras av den här Lagringsåtgärden.
    - `storeOps.size`: Representerar storlek i byte på resultatet av en viss lagringsåtgärd.

Cosmos DB Gremlin Runtime Operator|Beskrivning
---|---
`GetVertices`| Det här steget hämtar en predicated uppsättning objekt från lager för datapersistens. 
`GetEdges`| Det här steget hämtar kanterna gränsar till en uppsättning hörn. Det här steget kan resultera i en eller flera lagringsåtgärder.
`GetNeighborVertices`| Det här steget hämtar de hörn som är anslutna till en uppsättning kanter. Kanterna innehåller partitionen nycklar och ID: n med sin källa och mål hörn.
`Coalesce`| Det här steget konton för utvärderingen av två åtgärder när den `coalesce()` Gremlin-steg utförs.
`CartesianProductOperator`| Det här steget beräknar en kartesiansk produkt mellan två datauppsättningar. Vanligtvis körs varje gång predikat `to()` eller `from()` används.
`ConstantSourceOperator`| Det här steget beräknar ett uttryck för att skapa ett konstantvärde som ett resultat.
`ProjectOperator`| Det här steget förbereder och Serialiserar ett svar som använder resultatet av föregående åtgärder.
`ProjectAggregation`| Det här steget förbereder och Serialiserar ett svar för en sammanställd åtgärd.

> [!NOTE]
> Den här listan fortsätter att uppdateras när nya operatorer läggs till.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Exempel på hur du analyserar ett svar på körning av profil

Här följer några exempel på vanliga optimeringar som spotted med körning profil svaret:
  - Hemlig förgreningsfråga.
  - Ofiltrerade fråga.

### <a name="blind-fan-out-query-patterns"></a>Hemlig fan-out frågemönster

Anta att följande körning profil svar från en **partitionerade graph**:

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

Följande slutsatser kan göras från den:
- Frågan är en enkel ID-sökning, eftersom instruktionen Gremlin följer mönstret `g.V('id')`.
- Bedömning från den `time` svarstiden för den här frågan verkar vara hög eftersom det är mått, [mer än 10 MS för en enda punkt läsning åtgärd](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Om vi titta på den `storeOps` objekt kan vi se att den `fanoutFactor` är `5`, vilket innebär att [5 partitioner](https://docs.microsoft.com/azure/cosmos-db/partition-data) användes av den här åtgärden.

Som en ingående av den här analysen kan vi fastställer att den första frågan har åtkomst till fler partitioner än nödvändigt. Det kan åtgärdas genom att ange partitionsnyckel i frågan som ett predikat. Detta leder till mindre fördröjning och mindre kostnad per fråga. Läs mer om [grafpartitionering](graph-partitioning.md). En mer optimala frågeprestanda skulle vara `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Ofiltrerade frågemönster

Jämför följande två körning profil svar. För enkelhetens skull används i dessa exempel en enda partitionerade graf.

Den här första fråga hämtar alla hörn med etiketten `tweet` och hämtar sedan sina Närliggande hörn:

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

Lägg märke till profilen för samma fråga, men nu med ett ytterligare filter `has('lang', 'en')`, innan du börjar utforska intilliggande hörn:

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

Dessa två frågor har nåtts samma resultat, men den första som kräver mer programbegäran eftersom som krävdes för att iterera en större inledande datauppsättning innan fråga intilliggande objekt. Vi kan se indikatorer för det här beteendet när du jämför följande parametrar från båda svar:
- Den `metrics[0].time` värdet är högre upp i det första svaret som anger att den här enda steg tog längre tid att lösa.
- Den `metrics[0].counts.resultsCount` värdet är högre samt i det första svaret som anger att den initiala datauppsättningen arbeta var större.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [Gremlin-funktioner som stöds](gremlin-support.md) i Azure Cosmos DB. 
* Läs mer om den [Gremlin-API i Azure Cosmos DB](graph-introduction.md).
