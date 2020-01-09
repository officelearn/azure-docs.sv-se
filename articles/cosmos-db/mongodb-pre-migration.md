---
title: Steg före migrering för datamigrering till Azure Cosmos DBs API för MongoDB
description: Det här dokumentet innehåller en översikt över förutsättningarna för en datamigrering från MongoDB till Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445203"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Steg före migrering för datamigrering från MongoDB till Azure Cosmos DB s API för MongoDB

Innan du migrerar dina data från MongoDB (antingen lokalt eller i molnet (IaaS)) till Azure Cosmos DB s API för MongoDB, bör du:

1. [Skapa ett Azure Cosmos DB-konto](#create-account)
2. [Beräkna det data flöde som krävs för dina arbets belastningar](#estimate-throughput)
3. [Välj en optimal partitionsnyckel för dina data](#partitioning)
4. [Förstå indexerings principen som du kan ange för dina data](#indexing)

Om du redan har slutfört ovanstående krav för migrering, se [migrera MongoDB-data till Azure Cosmos DB s API för MongoDB](../dms/tutorial-mongodb-cosmos-db.md) för de faktiska stegen för data migrering. Annars innehåller det här dokumentet instruktioner för att hantera dessa krav. 

## <a id="create-account"></a>Skapa ett Azure Cosmos DB konto 

Innan du påbörjar migreringen måste du [skapa ett Azure Cosmos-konto med hjälp av Azure Cosmos DB s API för MongoDB](create-mongodb-dotnet.md). 

När kontot skapas kan du välja inställningar för att [globalt distribuera](distribute-data-globally.md) dina data. Du kan också välja att aktivera flera region skrivningar (eller multi-master-konfiguration), vilket gör att varje region kan vara både en Skriv-och Läs region.

![Skapa konto](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>Beräkna data flödes behovet för dina arbets belastningar

Innan du påbörjar migreringen med hjälp av [Database migration service (DMS)](../dms/dms-overview.md)bör du uppskatta mängden data flöde som ska etableras för dina Azure Cosmos-databaser och samlingar.

Data flödet kan tillhandahållas på något av följande:

- Samling

- Databas

> [!NOTE]
> Du kan också ha en kombination av ovanstående, där vissa samlingar i en databas kan ha dedikerat allokerat data flöde och andra kan dela data flödet. Mer information finns [på sidan Ange data flöde på en databas och en behållar](set-throughput.md) sida.
>

Först bör du bestämma om du vill etablera data flöde för databaser eller samlings nivåer, eller en kombination av båda. I allmänhet rekommenderar vi att du konfigurerar ett dedikerat data flöde på samlings nivå. Genom att tillhandahålla data flöde på databas nivå kan samlingar i databasen dela det tillhandahållna data flödet. Med delat data flöde finns det dock ingen garanti för ett specifikt data flöde på varje enskild samling och du får inte förutsägbara prestanda för någon viss samling.

Om du inte är säker på hur mycket data flöde som ska vara dedicerat till varje enskild samling kan du välja data flöde på databas nivå. Du kan tänka på det etablerade data flödet som kon figurer ATS i din Azure Cosmos-databas som en logisk motsvarighet till beräknings kapaciteten för en MongoDB-VM eller en fysisk server, men mer kostnads effektivt med möjligheten att göra en elastisk skalning. Mer information finns i [etablera data flöde på Azure Cosmos-behållare och databaser](set-throughput.md).

Om du etablerar data flöde på databas nivå måste alla samlingar som skapats i databasen skapas med en partition/Shard-nyckel. Mer information om partitionering finns [i partitionering och horisontell skalning i Azure Cosmos DB](partition-data.md). Om du inte anger en partition/Shard-nyckel under migreringen, fyller Azure Database Migration Service automatiskt i fältet Shard-nyckel med ett *_ID* -attribut som genereras automatiskt för varje dokument.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Optimalt antal begär ande enheter (ru: er) för att etablera

I Azure Cosmos DB allokeras data flödet i förväg och mäts i enheter för programbegäran (RU) per sekund. Om du har arbets belastningar som kör MongoDB på en virtuell dator eller lokalt, Tänk på att RU är som en enkel abstraktion för fysiska resurser, t. ex. för storleken på en virtuell dator eller på en lokal server och de resurser som de har, t. ex. minne, CPU, IOPs. 

Till skillnad från virtuella datorer eller lokala servrar är ru: er enkelt att skala upp och ned när som helst. Du kan ändra antalet etablerade ru: er inom några sekunder och du debiteras bara för det maximala antalet ru: er som du etablerar för en viss period på en timme. Mer information finns i [enheter för programbegäran i Azure Cosmos DB](request-units.md).

Följande är viktiga faktorer som påverkar antalet ru: er som krävs:
- **Objekt storlek (t. ex. dokument) storlek**: när ett objekts eller dokuments storlek ökar ökar antalet ru: er som förbrukas för att läsa eller skriva objektet/dokumentet också.
- **Antal objekt egenskaper**: förutsatt att [standard indexeringen](index-overview.md) används för alla egenskaper ökar antalet ru: er som används för att skriva ett objekt när antalet objekt egenskaper ökar. Du kan minska användningen av begär ande enheter för Skriv åtgärder genom [att begränsa antalet indexerade egenskaper](index-policy.md).
- **Samtidiga åtgärder**: enheter som för bruk ATS beror också på den frekvens med vilken olika CRUD åtgärder (t. ex. skrivningar, läsningar, uppdateringar, borttagningar) och mer komplexa frågor körs. Du kan använda [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) för att generera de samtidiga behoven för dina aktuella MongoDB-data.
- **Fråga mönster**: en frågas komplexitet påverkar hur många enheter för programbegäran som används av frågan.

Om du exporterar JSON-filer med [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) och förstår hur många skrivningar, läser, uppdaterar och tar bort som sker per sekund, kan du använda [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) för att beräkna det ursprungliga antalet ru: er som ska etableras. Kapacitets planeraren kostar inte mer komplexa frågor. Så om du har komplexa frågor om dina data används ytterligare ru: er. Kalkylatorn förutsätter också att alla fält är indexerade och att konsekvens används i sessionen. Det bästa sättet att förstå kostnaden för frågor är att migrera dina data (eller exempel data) till Azure Cosmos DB, [ansluta till Cosmos DB slut punkten](connect-mongodb-account.md) och köra en exempel fråga från MongoDB-gränssnittet med hjälp av `getLastRequestStastistics` kommandot för att hämta begär ande avgiften, vilket kommer att returnera antalet ru: er som förbrukas:

`db.runCommand({getLastRequestStatistics: 1})`

Det här kommandot kommer att skriva ut ett JSON-dokument som liknar följande:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

När du har förstått antalet ru: er som förbrukas av en fråga och samtidigheten för den frågan, kan du justera antalet etablerade ru: er. Optimering av ru: er är inte en engångs händelse – du bör kontinuerligt optimera eller skala upp den ru: er som tillhandahålls, beroende på om du inte förväntar dig en tung trafik, i stället för en kraftig arbets belastning eller importera data.

## <a id="partitioning"></a>Välj partitionsnyckel
Partitionering är en viktig aspekt innan du migrerar till en globalt distribuerad databas som Azure Cosmos DB. Azure Cosmos DB använder partitionering för att skala enskilda behållare i en databas för att uppfylla ditt programs skalbarhet och prestanda behov. I partitionering är objekten i en behållare indelade i distinkta del mängder som kallas logiska partitioner. Mer information och rekommendationer om hur du väljer rätt partitionsnyckel för dina data finns i [avsnittet välja en partitionsnyckel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indexera dina data
Som standard indexerar Azure Cosmos DB alla data fält vid inmatning. Du kan ändra [indexerings principen](index-policy.md) i Azure Cosmos DB när du vill. I själva verket rekommenderar vi ofta att du inaktiverar indexering när du migrerar data och sedan aktiverar det igen när data redan finns i Cosmos DB. Om du vill ha mer information om indexering kan du läsa mer om det i avsnittet [indexering i Azure Cosmos DB](index-overview.md) . 

[Azure Database migration service](../dms/tutorial-mongodb-cosmos-db.md) migrerar automatiskt MongoDB-samlingar med unika index. Däremot måste de unika indexen skapas innan migreringen. Azure Cosmos DB har inte stöd för att skapa unika index när det redan finns data i samlingarna. Mer information finns i [unika nycklar i Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Nästa steg
* [Migrera dina MongoDB-data till Cosmos DB med hjälp av Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Etablera data flöde i Azure Cosmos-behållare och databaser](set-throughput.md)
* [Partitionering i Azure Cosmos DB](partition-data.md)
* [Global distribution i Azure Cosmos DB](distribute-data-globally.md)
* [Indexering i Azure Cosmos DB](index-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
