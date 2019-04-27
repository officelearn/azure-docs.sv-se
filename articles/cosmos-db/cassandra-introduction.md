---
title: Introduktion till Cassandra-API:et för Azure Cosmos DB
description: Läs hur du kan använda Azure Cosmos DB för att lyfta och skifta befintliga program och skapa nya program med Cassandra-API:t med hjälp av de Cassandra-drivrutiner och CQL som du redan är bekant med.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9d948164177b7acd2df722078ae590f409c8e795
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899107"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Introduktion till Cassandra-API:et för Azure Cosmos DB

Cassandra-API:et för Azure Cosmos DB kan användas som datalager för appar som är skrivna för [Apache Cassandra](https://cassandra.apache.org/). Det betyder att ditt befintliga Cassandra-program, genom att använda befintliga [Apache-drivrutiner](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) som är kompatibla med CQLv4, nu kan kommunicera med Cassandra-API:et för Azure Cosmos DB. Ofta kan du växla från att använda Apache Cassandra till att använda Cassandra-API:et för Azure Cosmos DB genom att bara ändra en anslutningssträng. 

Cassandra-API:t låter dig interagera med data som lagras i Azure Cosmos DB med hjälp av Cassandra Query Language (CQL), Cassandra-baserade verktyg (till exempel cqlsh) och de Cassandra-klientdrivrutiner som du redan är bekant med.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Vad är fördelen med att använda Apache Cassandra API:t för Azure Cosmos DB?

**Hantering utan åtgärder**: Som en fullständigt hanterad molntjänst eliminerar Cassandra-API:et för Azure Cosmos DB det tidskrävande arbetet med att hantera och övervaka en mängd olika inställningar i operativsystem, JVM och yaml-filer samt deras interaktioner. Azure Cosmos DB erbjuder övervakning av dataflödet, svarstider, lagring, tillgänglighet och konfigurerbara aviseringar.

**Prestandahantering**: Azure Cosmos DB tillhandahåller läsningar och skrivningar med garanterat korta svarstider inom den 99:e percentilen, backade av serviceavtalen. Användarna behöver inte bekymra sig om driftsomkostnader för att säkerställa höga prestanda och snabba läsningar och skrivningar. Det innebär att användarna inte behöver lösa schemaläggningsproblem, hantera tombstones eller skapa bloomfilter och repliker manuellt. Med Azure Cosmos DB slipper du ägna tid åt dessa problem så att du kan fokusera på programlogiken.

**Möjlighet att använda befintlig kod och verktyg**: Azure Cosmos DB erbjuder kompatibilitet på trådprotokollsnivå med befintliga SDK:er och verktyg för Cassandra. Den här kompatibiliteten garanterar att du kan använda din befintliga kodbas med Cassandra-API:et för Azure Cosmos DB med minimala ändringar.

**Dataflöde och lagringselasticitet**: Azure Cosmos DB tillhandahåller garanterad genomströmning i alla regioner och kan skala det etablerade dataflödet via Azure-portalen, PowerShell eller CLI. Du kan skala lagringsutrymmet och dataflödet elastiskt för dina tabeller efter behov med förutsägbara prestanda.

**Global distribution och tillgänglighet**: Med Azure Cosmos DB kan du distribuera data över alla Azure-regioner globalt och tillhandahålla data lokalt med snabb dataåtkomst och hög tillgänglighet. Azure Cosmos DB erbjuder 99,99 % hög tillgänglighet inom en region och 99,999 % läs- och skrivtillgänglighet i flera regioner utan ytterligare driftkostnader. Läs mer i artikeln [Distribuera data globalt](distribute-data-globally.md). 

**Val av konsekvensnivåer:** Med Azure Cosmos DB kan du välja mellan fem väldefinierade konsekvensnivåer för bästa möjliga balans mellan konsekvens och prestanda. Dessa konsekvensnivåer är stark, begränsat föråldrad, session, konsekvent prefix och eventuell. Dessa väldefinierade, praktiska och intuitiva konsekvensnivåer hjälper utvecklare att göra exakta avvägningarna mellan konsekvens, tillgänglighet och svarstider. Läs mer i artikeln om [konsekvensnivåer](consistency-levels.md). 

**Enterprise-klass**: Azure Cosmos DB erbjuder [kompatibilitetscertifieringar](https://www.microsoft.com/trustcenter) så att användare kan använda plattformen på ett säkert sätt. Azure Cosmos DB erbjuder även kryptering i vila och i rörelse, IP-brandvägg och granskningsloggar för kontrollplansaktiviteter.

## <a name="next-steps"></a>Nästa steg

* Du kan snabbt komma igång med att utveckla följande språkspecifika appar för att skapa och hantera data för Cassandra-API:et:
  - [Node.js-app](create-cassandra-nodejs.md)
  - [.NET-app](create-cassandra-dotnet.md)
  - [Python-app](create-cassandra-python.md)

* Kom igång med att [skapa ett konto, en databas och en tabell för Cassandra-API:et](create-cassandra-api-account-java.md) med hjälp av ett Java-program.

* [Läs in exempeldata i tabellen för Cassandra-API:et](cassandra-api-load-data.md) med hjälp av ett Java-program.

* [Fråga efter data från kontot för Cassandra-API:et](cassandra-api-query-data.md) med hjälp av ett Java-program.

* Mer information om Apache Cassandra-funktioner som stöds av Cassandra-API:et för Azure Cosmos DB finns i artikeln om [Cassandra-stöd](cassandra-support.md).

* Läs våra [vanliga frågor och svar](faq.md#cassandra).
