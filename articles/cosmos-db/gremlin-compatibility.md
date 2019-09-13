---
title: Azure Cosmos DB Gremlin-kompatibilitet med TinkerPop-funktioner
description: Referens för problem med diagram motorns programkompatibilitet
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: d58d1ea50251c3568161ce008db2672ff0fd6372
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911056"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin-kompatibilitet
Azure Cosmos DB diagram motor närmar sig följande steg Specifikation för [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , men det finns skillnader.

## <a name="behavior-differences"></a>Beteende skillnader

* Azure Cosmos DB Graph-motor kör ***Bredd-första*** genom gång medan TinkerPop Gremlin är djup – först. Med det här beteendet uppnås bättre prestanda i horisontellt skalbart system som Cosmos DB. 

## <a name="unsupported-features"></a>Funktioner som inte stöds

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)***  är ett datorspråk med oberoende specifikation för diagrambläddringar. Cosmos DB Graph har inte stöd för det än. Använd ```GremlinClient.SubmitAsync()``` och skicka Traversal som en text sträng.

* ***```property(set, 'xyz', 1)```*** Ange kardinalitet stöds inte idag. Använd ```property(list, 'xyz', 1)``` i stället.

* ***```match()```*** tillåter att fråga grafer med deklarativ mönster matchning. Den här funktionen är inte tillgänglig.

* ***Objekt som egenskaper*** för hörn eller kanter stöds inte. Egenskaper kan bara vara primitiva typer eller matriser.

* ***Sortera efter mat ris egenskaper*** ```.order().by(<array property>)``` stöds inte. Sortering stöds endast av primitiva typer.

* ***JSON-typer som inte är primitiva*** stöds inte. ```string```Användnings ```number```-, ```true``` -eller / -typer```false``` . ```null```värden stöds inte. 

* ***GraphSONv3*** -serialisering är inte tillgänglig idag.

* ***Transaktioner*** stöds inte på grund av systemets distribuerade natur.  Konfigurera lämplig konsekvens modell på Gremlin-kontot för att "läsa dina egna skrivningar" och Använd optimistisk samtidighet för att lösa skrivningar som står i konflikt med varandra.

## <a name="next-steps"></a>Nästa steg
* Besök [Cosmos DB User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) -sidan för att dela feedback och fokusera på funktioner som är viktiga för dig.
