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
ms.openlocfilehash: ed08b90b9e216ee8713bfe445e98144bf2ba02d4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244194"
---
# <a name="consistency-levels-and-cosmos-db-apis"></a>Konsekvensnivåer och Cosmos DB API: er

Fem konsekvensmodeller stöds internt av SQL-API som är standard-API: et när du använder Cosmos DB. Förutom SQL-API finns Cosmos DB även inbyggt stöd för protokollet kompatibla API: er för populära databaser, till exempel MongoDB, Apache Cassandra, Gremlin och Azure-tabeller. De här databaserna erbjuder varken exakt definierade konsekvensmodeller eller SLA-reglerad garantier för konsekvensnivåerna och ges vanligtvis med endast en delmängd av de fem konsekvensmodeller som Cosmos DB erbjuder. Standard-konsekvensnivå som konfigurerats för Cosmos-kontot används för SQL-API, Gremlin-API och tabell-API.

Följande tabell visar mappningen mellan den datakonsekvens som begärts av en klient OSS-drivrutinen för Apache Cassandra 4.x och MongoDB 3.4 när du använder Cassandra API och MongoDB API respektive och motsvarande Cosmos DB-konsekvensnivåer.

## <a id="cassandra-mapping"></a>Mappning av Apache Cassandra och Cosmos DB-konsekvensnivåer

Tabellen nedan visar mappningen av läsningskontinuitet mellan Apache Cassandra 4.x-klienten och Cosmos DB ”standard” konsekvensnivå för både en flera regioner och en region distribution.

| **Apache Cassandra 4.x** | **Cosmos DB (flera region)** | **Cosmos DB (en region)** |
| - | - | - |
| ETT TVÅ TRE | Konsekvent prefix | Konsekvent prefix |
| LOCAL_ONE | Konsekvent prefix | Konsekvent prefix |
| KVORUM, ALLA, SERIE | Begränsad föråldring (standard) eller starkt (i privat förhandsvisning) | Stark |
| LOCAL_QUORUM | Begränsad föråldring | Stark |
| LOCAL_SERIAL | Begränsad föråldring | Stark |

## <a id="mongo-mapping"></a>Mappning mellan MongoDB 3.4 och Cosmos DB-konsekvensnivåer

Tabellen nedan visar mappningen av ”skrivskyddade frågor” av MongoDB 3.4 och Cosmos DB ”standard” konsekvensnivå för både en flera regioner och en region distribution.

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