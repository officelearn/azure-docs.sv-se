---
title: Azure Cosmos DB Gremlin-kompatibilitet med TinkerPop-funktioner
description: Referens för problem med diagram motorns programkompatibilitet
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644740"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin-kompatibilitet
Azure Cosmos DB diagram motor närmar sig följande steg Specifikation för [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , men det finns skillnader.

## <a name="behavior-differences"></a>Beteende skillnader

* Azure Cosmos DB Graph-motor kör ***Bredd-första*** genom gång medan TinkerPop Gremlin är djup – först. Med det här beteendet uppnås bättre prestanda i horisontellt skalbart system som Cosmos DB. 

## <a name="unsupported-features"></a>Funktioner som inte stöds

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** är ett datorspråk med oberoende specifikation för diagrambläddringar. Cosmos DB Graph har inte stöd för det än. Använd `GremlinClient.SubmitAsync()` och skicka Traversal som en text sträng.

* ***`property(set, 'xyz', 1)`*** ange kardinalitet stöds inte idag. Använd `property(list, 'xyz', 1)` i stället. Mer information finns i [Egenskaper för hörn med TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** tillåter att fråga grafer med deklarativ mönster matchning. Den här funktionen är inte tillgänglig.

* ***Objekt som egenskaper*** för hörn eller kanter stöds inte. Egenskaper kan bara vara primitiva typer eller matriser.

* ***Sortering efter mat ris egenskaper*** `order().by(<array property>)` stöds inte. Sortering stöds endast av primitiva typer.

* ***JSON-typer som inte är primitiva*** stöds inte. Använd `string`, `number`eller `true`/`false` typer. `null` värden stöds inte. 

* ***GraphSONv3*** -serialiseraren stöds inte för närvarande. Använd `GraphSONv2` serialiserare, läsare och skrivar klasser i anslutnings konfigurationen.

* **Lambda-uttryck och-funktioner** stöds inte för närvarande. Detta omfattar `.map{<expression>}`, `.by{<expression>}`och `.filter{<expression>}` funktioner. Om du vill veta mer och lära dig att skriva om dem med Gremlin-steg, se [en kommentar om lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)-tal.

* ***Transaktioner*** stöds inte på grund av systemets distribuerade natur.  Konfigurera lämplig konsekvens modell på Gremlin-kontot för att "läsa dina egna skrivningar" och Använd optimistisk samtidighet för att lösa skrivningar som står i konflikt med varandra.

## <a name="next-steps"></a>Nästa steg
* Besök [Cosmos DB User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) -sidan för att dela feedback och fokusera på funktioner som är viktiga för dig.
