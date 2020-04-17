---
title: Azure Cosmos DB Gremlin-kompatibilitet med TinkerPop-funktioner
description: Referensdokumentation Problem med grafmotorkompatibilitet
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449892"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin-kompatibilitet
Azure Cosmos DB Graph-motorn följer noga Apache TinkerPop-korsstegsspecifikationen men det finns skillnader. [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)

## <a name="behavior-differences"></a>Beteendeskillnader

* Azure Cosmos DB ***Graph-motorn körs viddens första*** traversal medan TinkerPop Gremlin är djup-först. Detta ger bättre prestanda i horisontellt skalbara system som Cosmos DB. 

## <a name="unsupported-features"></a>Funktioner som inte stöds

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** är ett datorspråk med oberoende specifikation för diagrambläddringar. Cosmos DB Graph stöder det inte ännu. Använd `GremlinClient.SubmitAsync()` och skicka traversal som en textsträng.

* ***`property(set, 'xyz', 1)`*** kardinalitet stöds inte idag. Använd `property(list, 'xyz', 1)` i stället. Mer information finns i [Vertex-egenskaper med TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** tillåter att du frågar grafer med deklarativ mönstermatchning. Den här funktionen är inte tillgänglig.

* ***Objekt som egenskaper*** på hörn eller kanter stöds inte. Egenskaper kan bara vara primitiva typer eller matriser.

* Det går inte ***att sortera efter matrisegenskaper.*** `order().by(<array property>)` Sortering stöds endast av primitiva typer.

* ***Icke-primitiva JSON-typer*** stöds inte. Använd `string` `number`, `true` / `false` eller typer. `null`värden stöds inte. 

* ***GraphSONv3*** serialiserare stöds för närvarande inte. Använd `GraphSONv2` serialiserare, läsare och författare i anslutningskonfigurationen. Resultaten som returneras av Azure Cosmos DB Gremlin API har inte samma format som GraphSON-formatet. 

* **Lambda-uttryck och funktioner** stöds för närvarande inte. Detta inkluderar `.map{<expression>}` `.by{<expression>}` `.filter{<expression>}` funktionerna , och . Mer information och hur du skriver om dem med Gremlin-stegen finns i [En anteckning om Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Transaktioner*** stöds inte på grund av systemets distribuerade karaktär.  Konfigurera lämplig konsekvensmodell på Gremlin-kontot för att "läsa dina egna skrivningar" och använd optimistisk samtidighet för att lösa motstridiga skrivningar.

## <a name="next-steps"></a>Nästa steg
* Besök [Cosmos DB-användarens röstsida](https://feedback.azure.com/forums/263030-azure-cosmos-db) för att dela feedback och hjälpa teamet att fokusera på funktioner som är viktiga för dig.
