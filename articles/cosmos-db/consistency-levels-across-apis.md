---
title: Konsekvensnivåer och API:er för Azure Cosmos DB
description: 'För att förstå konsekvensnivåerna i API: er i Azure Cosmos DB.'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: b620ca76cfea296e504afffd91852308a01575db
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002024"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsekvensnivåer och API:er för Azure Cosmos DB

Fem konsekvensmodeller erbjuds av Azure Cosmos DB stöds av SQL-API. När du använder Azure Cosmos DB är SQL API standardinställningen. 

Azure Cosmos DB erbjuder även inbyggt stöd för wire protocol-kompatibla API: er för populära databaser. Databaser innehåller MongoDB, Apache Cassandra, Gremlin och Azure Table storage. Dessa databaser erbjuder inte exakt definierade konsekvensmodeller eller omfattas av serviceavtalet garantier för konsekvensnivåer. De tillhandahåller normalt endast en delmängd av de fem konsekvensmodeller erbjuds av Azure Cosmos DB. Standard-konsekvensnivå som konfigurerats för Azure Cosmos-kontot används för SQL-API, Gremlin-API och tabell-API. 

I följande avsnitt visas mappningen mellan datakonsekvens som begärts av en klient OSS-drivrutinen för Apache Cassandra, MongoDB och motsvarande konsekvensnivåer i Azure Cosmos DB.

## <a id="cassandra-mapping"></a>Mappning mellan Apache Cassandra och Azure Cosmos DB-konsekvensnivåer

Tabellen nedan beskrivs olika konsekvens kombinationen kan använda mot API för Cassandra och motsvarande interna konsekvens på mappningen av Cosmos DB. Alla kombinationer av Apache Cassandra-Skriv- och Läs lägen stöds internt av Cosmos DB. I alla kombinationer av Apache Cassandra-Skriv- och Läs konsekvensmodell ger Cosmos DB samma eller högre konsekvens garanterar än Apache Cassandra. Cosmos DB tillhandahåller dessutom ge hög tålighet garanterar än Apache Cassandra även i svagaste läget för skrivning.

I följande tabell visas de **skriva konsekvens mappning** mellan Azure Cosmos DB och Cassandra:

| Cassandra | Azure Cosmos DB | Garanti |
| - | - | - |
|Alla|Stark  | Lineariserbarhet |
| EACH_QUORUM   | Stark    | Lineariserbarhet | 
| KVORUM ÄR SERIELL |  Stark |    Lineariserbarhet |
| LOCAL_QUORUM, TRE, TVÅ, EN, LOCAL_ONE, ALLA | Konsekvent prefix |Global konsekvent Prefix |
| EACH_QUORUM   | Stark    | Lineariserbarhet |
| KVORUM ÄR SERIELL |  Stark |    Lineariserbarhet |
| LOCAL_QUORUM, TRE, TVÅ, EN, LOCAL_ONE, ALLA | Konsekvent prefix | Global konsekvent Prefix |
| KVORUM ÄR SERIELL | Stark   | Lineariserbarhet |
| LOCAL_QUORUM, TRE, TVÅ, EN, LOCAL_ONE, ALLA | Konsekvent prefix | Global konsekvent Prefix |
| LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE    | Begränsad föråldring | <ul><li>Begränsad föråldring.</li><li>I de flesta K versioner eller tiden t bakom.</li><li>Läs senaste allokerade värdet i regionen.</li></ul> |
| EN LOCAL_ONE, ALLA   | Konsekvent prefix | Per region konsekvent Prefix |

I följande tabell visas de **Läs konsekvens mappning** mellan Azure Cosmos DB och Cassandra:

| Cassandra | Azure Cosmos DB | Garanti |
| - | - | - |
| ALLA KVORUM, SERIENUMMER, LOCAL_QUORUM, LOCAL_SERIAL, TRE, TVÅ, EN, LOCAL_ONE | Stark  | Lineariserbarhet|
| ALLA KVORUM, SERIENUMMER, LOCAL_QUORUM, LOCAL_SERIAL, TRE, TVÅ   |Stark |   Lineariserbarhet |
|LOCAL_ONE, EN | Konsekvent prefix | Global konsekvent Prefix |
| ALLA, KVORUM, SERIE   | Stark    | Lineariserbarhet |
| LOCAL_ONE, EN, LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE |  Konsekvent prefix   | Global konsekvent Prefix |
| LOCAL_ONE, EN, TVÅ, TRE, LOCAL_QUORUM, KVORUM |    Konsekvent prefix   | Global konsekvent Prefix |
| ALLA KVORUM, SERIENUMMER, LOCAL_QUORUM, LOCAL_SERIAL, TRE, TVÅ   |Stark |   Lineariserbarhet |
| LOCAL_ONE, EN    | Konsekvent prefix | Global konsekvent Prefix|
| ALLA KVORUM är seriell stark Linjärbarhetsgaranti
LOCAL_ONE, EN, LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE  |Konsekvent prefix  | Global konsekvent Prefix |
|Alla    |Stark |Lineariserbarhet |
| LOCAL_ONE, EN, TVÅ, TRE, LOCAL_QUORUM, KVORUM  |Konsekvent prefix  |Global konsekvent Prefix|
|ALLA KVORUM är seriell stark Linjärbarhetsgaranti
LOCAL_ONE, EN, LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE  |Konsekvent prefix  |Global konsekvent Prefix |
|Alla    |Stark | Lineariserbarhet |
| LOCAL_ONE, EN, TVÅ, TRE, LOCAL_QUORUM, KVORUM  | Konsekvent prefix | Global konsekvent Prefix |
| KVORUM, LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE |  Begränsad föråldring   | <ul><li>Begränsad föråldring.</li><li>I de flesta K versioner eller tiden t bakom. </li><li>Läs senaste allokerade värdet i regionen.</li></ul>
| LOCAL_ONE, EN |Konsekvent prefix | Per region konsekvent Prefix |
| LOCAL_ONE, EN, TVÅ, TRE, LOCAL_QUORUM, KVORUM  | Konsekvent prefix | Per region konsekvent Prefix |


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