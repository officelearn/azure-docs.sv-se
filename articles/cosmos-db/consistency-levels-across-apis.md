---
title: 'Konsekvensnivåer och Azure Cosmos DB API: er | Microsoft Docs'
description: 'För att förstå konsekvensnivåerna i API: er i Azure Cosmos DB.'
keywords: konsekvens, azure cosmos db, azure, modeller, mongodb, cassandra, diagram, tabell, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 974531cd5907e4f69e7d064125d3e51fa4974949
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956391"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsekvensnivåer och Azure Cosmos DB API: er

De fem konsekvensmodeller erbjuds av Azure Cosmos DB stöds internt av Cosmos DB SQL API, vilket är standard-API: et när du använder Cosmos DB. Förutom SQL-API finns Cosmos DB även inbyggt stöd för protokollet kompatibla API: er för populära databaser, till exempel MongoDB, Apache Cassandra, Gremlin och Azure-tabeller. Dessa databaser som varken erbjuder exakt definierade konsekvensmodeller och inte heller garantierna som omfattas av serviceavtalet för konsekvensnivåerna. De här databaserna tillhandahåller vanligtvis endast en delmängd av de fem konsekvensmodeller erbjuds av Cosmos DB. Standard-konsekvensnivå som du konfigurerar för Cosmos-kontot används för SQL-API, Gremlin-API och tabell-API.

I följande avsnitt visas mappningen mellan den datakonsekvens som begärts av en klient OSS-drivrutinen för Apache Cassandra 4.x och MongoDB 3.4 när du använder Cassandra API och MongoDB API respektive och motsvarande Cosmos DB-konsekvensnivåer.

## <a id="cassandra-mapping"></a>Mappning mellan Apache Cassandra och Cosmos DB-konsekvensnivåer

I följande tabell visas ”läsningskontinuitet” mappningen mellan Apache Cassandra 4.x klient- och standard-konsekvensnivå i Cosmos DB för både i flera regioner och en region.

| **Apache Cassandra 4.x** | **Cosmos DB (flera region)** | **Cosmos DB (en region)** |
| - | - | - |
| ETT TVÅ TRE | Konsekvent prefix | Konsekvent prefix |
| LOCAL_ONE | Konsekvent prefix | Konsekvent prefix |
| KVORUM, ALLA, SERIE | Begränsad föråldring (standard) eller starkt (i privat förhandsvisning) | Stark |
| LOCAL_QUORUM | Begränsad föråldring | Stark |
| LOCAL_SERIAL | Begränsad föråldring | Stark |

## <a id="mongo-mapping"></a>Mappning mellan MongoDB 3.4 och Cosmos DB-konsekvensnivåer

I följande tabell visas ”Läs funderingar” mappningen mellan MongoDB 3.4 och standard-konsekvensnivå i Cosmos DB för både i flera regioner och en region.

| **MongoDB 3.4** | **Cosmos DB (flera region)** | **Cosmos DB (en region)** |
| - | - | - |
| Linearizable | Stark | Stark |
| Majoritet | Begränsad föråldring | Stark |
| Lokal | Konsekvent prefix | Konsekvent prefix |

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvensnivåer och kompatibiliteten mellan Cosmos DB API: er med öppen källkod-API: er i följande artiklar:

* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [MongoDB-funktioner som stöds av Cosmos DB MongoDB API](mongodb-feature-support.md)
* [Apache Cassandra-funktioner som stöds av Cosmos DB Cassandra-API](cassandra-support.md)