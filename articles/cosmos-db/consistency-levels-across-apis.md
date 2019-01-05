---
title: Konsekvensnivåer och API:er för Azure Cosmos DB
description: 'För att förstå konsekvensnivåerna i API: er i Azure Cosmos DB.'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 76ebbc8cc8dbea4b7f8f8226cf1d8570a421e8cf
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034343"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsekvensnivåer och API:er för Azure Cosmos DB

Fem konsekvensmodeller erbjuds av Azure Cosmos DB stöds internt av Azure Cosmos DB SQL API. När du använder Azure Cosmos DB är SQL API standardinställningen. 

Azure Cosmos DB erbjuder även inbyggt stöd för wire protocol-kompatibla API: er för populära databaser. Databaser innehåller MongoDB, Apache Cassandra, Gremlin och Azure Table storage. Dessa databaser erbjuder inte exakt definierade konsekvensmodeller eller omfattas av serviceavtalet garantier för konsekvensnivåer. De tillhandahåller normalt endast en delmängd av de fem konsekvensmodeller erbjuds av Azure Cosmos DB. Standard-konsekvensnivå som konfigurerats på Azure Cosmos DB-kontot används för SQL-API, Gremlin-API och tabell-API. 

I följande avsnitt visas mappningen mellan datakonsekvens som begärts av en klient OSS-drivrutinen för Apache Cassandra 4.x och MongoDB 3.4. Det här dokumentet visar även motsvarande Azure Cosmos DB-konsekvensnivåer för Apache Cassandra och MongoDB.

## <a id="cassandra-mapping"></a>Mappning mellan Apache Cassandra och Azure Cosmos DB-konsekvensnivåer

Den här tabellen visar ”läsningskontinuitet” mappningen mellan Apache Cassandra 4.x klient- och standard-konsekvensnivå i Azure Cosmos DB. Tabellen visar flera regioner och en region.

| **Apache Cassandra 4.x** | **Azure Cosmos DB (flera region)** | **Azure Cosmos DB (enskild region)** |
| - | - | - |
| ETT TVÅ TRE | Konsekvent prefix | Konsekvent prefix |
| LOCAL_ONE | Konsekvent prefix | Konsekvent prefix |
| KVORUM, ALLA, SERIE | Begränsad föråldring är standardinställningen. Stark är i privat förhandsversion. | Stark |
| LOCAL_QUORUM | Begränsad föråldring | Stark |
| LOCAL_SERIAL | Begränsad föråldring | Stark |

## <a id="mongo-mapping"></a>Mappning mellan MongoDB 3.4 och Azure Cosmos DB-konsekvensnivåer

I följande tabell visas ”Läs funderingar” mappningen mellan MongoDB 3.4 och standard-konsekvensnivå i Azure Cosmos DB. Tabellen visar flera regioner och en region.

| **MongoDB 3.4** | **Azure Cosmos DB (flera region)** | **Azure Cosmos DB (enskild region)** |
| - | - | - |
| Linearizable | Stark | Stark |
| Majoritet | Begränsad föråldring | Stark |
| Lokal | Konsekvent prefix | Konsekvent prefix |

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvensnivåer och kompatibiliteten mellan Azure Cosmos DB API: er med öppen källkod-API: er. Se följande artiklar:

* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [MongoDB-funktioner som stöds av Azure Cosmos DB: s API för MongoDB](mongodb-feature-support.md)
* [Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra-API](cassandra-support.md)