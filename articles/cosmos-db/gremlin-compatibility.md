---
title: Azure Cosmos DB Gremlin-kompatibilitet med TinkerPop-funktioner
description: Referens för problem med diagram motorns programkompatibilitet
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 1db7937cb574ce62986f25e0bfa688dc54b5c606
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700607"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin-kompatibilitet
Azure Cosmos DB diagram motor närmar sig följande steg Specifikation för [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , men det finns skillnader i implementeringen som är specifik för Azure Cosmos dB. Information om vilka Gremlin-steg som stöds finns i artikeln om [stöd för GREMLIN API Wire Protocol](gremlin-support.md) .

## <a name="behavior-differences"></a>Beteende skillnader

* Azure Cosmos DB Graph-motor kör ***Bredd-första*** genom gång medan TinkerPop Gremlin är djup – först. Med det här beteendet uppnås bättre prestanda i horisontellt skalbart system som Cosmos DB. 

## <a name="unsupported-features"></a>Funktioner som inte stöds

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** är ett datorspråk med oberoende specifikation för diagrambläddringar. Cosmos DB Graph har inte stöd för det än. Använd `GremlinClient.SubmitAsync()` och skicka Traversal som en text sträng.

* ***`property(set, 'xyz', 1)`*** Ange kardinalitet stöds inte idag. Använd `property(list, 'xyz', 1)` i stället. Mer information finns i [Egenskaper för hörn med TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* *** `match()` Steget*** är inte tillgängligt för tillfället. Det här steget tillhandahåller funktioner för deklarativ fråga.

* ***Objekt som egenskaper*** för hörn eller kanter stöds inte. Egenskaper kan bara vara primitiva typer eller matriser.

* ***Sortera efter mat ris egenskaper*** `order().by(<array property>)` stöds inte. Sortering stöds endast av primitiva typer.

* ***JSON-typer som inte är primitiva*** stöds inte. Användnings `string` -, `number` -eller- `true` / `false` typer. `null`värden stöds inte. 

* ***GraphSONv3*** -serialiseraren stöds inte för närvarande. Använd `GraphSONv2` serialiserare, läsare och skrivar klasser i anslutnings konfigurationen. Resultaten som returneras av Azure Cosmos DB Gremlin-API: t har inte samma format som GraphSON-formatet. 

* **Lambda-uttryck och-funktioner** stöds inte för närvarande. Detta omfattar `.map{<expression>}` `.by{<expression>}` funktionerna, och `.filter{<expression>}` . Om du vill veta mer och lära dig att skriva om dem med Gremlin-steg, se [en kommentar om lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)-tal.

* ***Transaktioner*** stöds inte på grund av systemets distribuerade natur.  Konfigurera lämplig konsekvens modell på Gremlin-kontot för att "läsa dina egna skrivningar" och Använd optimistisk samtidighet för att lösa skrivningar som står i konflikt med varandra.

## <a name="known-limitations"></a>Kända begränsningar

* **Index användning för Gremlin-frågor med `.V()` steg för steg-för-steg**: för närvarande kommer endast det första `.V()` anropet av en genom gång att använda indexet för att lösa eventuella filter eller predikat som är kopplade till den. Efterföljande anrop kommer inte att se indexet, vilket kan öka svars tiden och kostnaden för frågan.
    
    Vid antagande av standard indexering använder en typisk Read Gremlin-fråga som börjar med `.V()` steget parametrar i sina kopplade filtrerings steg, till exempel `.has()` eller `.where()` för att optimera frågans kostnad och prestanda. Ett exempel:

    ```java
    g.V().has('category', 'A')
    ```

    Men när mer än ett `.V()` steg ingår i Gremlin-frågan, kanske inte matchningen av data för frågan är optimal. Gör följande fråga som exempel:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    Den här frågan returnerar två grupper av hörn baserat på deras egenskap som kallas `category` . I det här fallet kommer endast det första anropet att `g.V().has('category', 'A')` använda indexet för att matcha hörnen baserat på egenskaperna för deras egenskaper.

    En lösning för den här frågan är att använda under steg, till exempel `.map()` och `union()` . Detta är WINS nedan:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    Du kan granska frågans prestanda genom att använda [Gremlin `executionProfile()` steg] (graf-Execution-Profile.MD.

## <a name="next-steps"></a>Nästa steg
* Besök [Cosmos DB User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) -sidan för att dela feedback och fokusera på funktioner som är viktiga för dig.
