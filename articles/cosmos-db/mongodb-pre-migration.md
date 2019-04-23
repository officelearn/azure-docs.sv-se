---
title: Stegen före migreringen för migrering av data från MongoDB till Azure Cosmos DB-API för MongoDB
description: Det här dokumentet innehåller en översikt över kraven för en migrering av data från MongoDB till Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014420"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Stegen före migreringen för migrering av data från MongoDB till Azure Cosmos DB-API för MongoDB

Innan du migrerar dina data från MongoDB (antingen lokalt eller i molnet (IaaS)) till Azure Cosmos DB-API för MongoDB, bör du:

1. [Skapa ett Azure Cosmos DB-konto](#create-account)
2. [Beräkna dataflöde som behövs för dina arbetsbelastningar](#estimate-throughput)
3. [Välj en optimal partitionsnyckel för dina data](#partitioning)
4. [Förstå den indexprincip som du kan ställa in på dina data](#indexing)

Om du redan har slutfört ovanstående förutsättningar för migrering finns i den [migrera MongoDB-data till Azure Cosmos DB-API för MongoDB](../dms/tutorial-mongodb-cosmos-db.md) för faktiska data migreringsanvisningar. Om inte det här dokumentet innehåller instruktioner för att hantera dessa krav. 

## <a id="create-account"></a> Skapa ett Azure Cosmos DB-konto 

Innan du påbörjar migreringen måste du [skapar ett Azure Cosmos-konto med Azure Cosmos DB API för MongoDB](create-mongodb-dotnet.md). 

När kontot skapas, kan du välja inställningar som [distribuera globalt](distribute-data-globally.md) dina data. Du har också alternativet att aktivera flera regioner skrivningar (eller flera huvudservrar configuration), där var och en av dina regioner både en skrivning och läses region.

![Skapa konto](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> Beräkna dataflöde behovet av dina arbetsbelastningar

Innan du påbörjar migreringen med hjälp av den [Database Migration Service (DMS)](../dms/dms-overview.md), bör du beräkna mängden dataflöde för att etablera för dina Azure Cosmos-databaser och samlingar.

Dataflöde kan etableras på antingen:

- Samling

- Databas

> [!NOTE]
> Du kan också ha en kombination av ovanstående, där vissa samlingar i en databas kan ha dedikerade etablerat dataflöde och andra kan dela dataflödet. Mer information finns i [ange dataflöde på en databas och en behållare](set-throughput.md) sidan.
>

Först bör du bestämma om du vill etablera databasen, på samlingsdataflödet eller en kombination av båda. I allmänhet rekommenderar vi att du konfigurerar ett dedikerat dataflöde på samlingsnivå. Etablering dataflöde på databasnivå kan samlingar i databasen för att dela det etablerade dataflödet. Med delade dataflöde, men det finns ingen garanti för en specifik genomströmning på varje enskild samling och du får inte förutsägbar prestanda på en specifik samling.

Om du inte är osäker på hur högt dataflöde bör vara reserverad för varje enskild samling kan välja du på databasnivå dataflöde. Du kan tänka på det etablerade dataflödet som konfigurerats på din Azure Cosmos-databas som en logisk likvärdiga med beräkningskapaciteten för en MongoDB-VM eller en fysisk server, men det mer kostnadseffektivt med möjlighet att skala Elastiskt. Mer information finns i [etablera dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md).

Om du etablerar dataflöde på databasnivå, måste alla samlingar som skapats i den här databasen skapas med en partition/shardnyckel. Mer information om partitionering finns i [partitionering och horisontell skalning i Azure Cosmos DB](partition-data.md). Om du inte anger en partition/shardnyckel under migreringen, Azure Database Migration Service automatiskt fyller nyckelfältet fragment med en *_id* attribut som genereras automatiskt för varje dokument.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Optimala antalet enheter för programbegäran (ru) att etablera

I Azure Cosmos DB dataflödet som etableras i förväg och mäts i enheter för programbegäran (RU) per sekund. Om du har arbetsbelastningar som körs MongoDB på en virtuell dator eller en lokal tänka på RU: er som en enkel abstraktion för fysiska resurser, till exempel storleken på en virtuell dator eller -en lokal server och de resurser som de har t.ex. minne, CPU, IOPs. 

Till skillnad från virtuella datorer eller lokala servrar, ru: er som är enkla att skala upp eller ned när som helst. Du kan ändra antalet etablerade ru: er på några sekunder och du debiteras bara för det maximala antalet enheter för programbegäran som du etablerar för en viss per timme. Mer information finns i [programbegäran i Azure Cosmos DB](request-units.md).

Följande är viktiga faktorer som påverkar antalet nödvändiga ru: er:
- **Objekt (t.ex, dokument) storleken**: Eftersom det ökar storleken på ett objekt/dokument, förbrukas antalet mediereserverade enheter för att läsa eller skriva objekt/dokument också ökar.
- **Objektet antal egenskaper**: Förutsatt att den [standard indexering](index-overview.md) för alla egenskaper, antalet mediereserverade enheter som används för att skriva ett objekt ökar när objektet egenskapen Antal ökar. Du kan minska resursförbrukningen för begäran-enhet för skrivåtgärder av [begränsningen av antalet indexerade egenskaper](index-policy.md).
- **Samtidiga åtgärder**: Begära enheter som används beror också på frekvensen med vilken annan CRUD-åtgärder (till exempel skrivningar, läsning, uppdateringar, borttagningar) och mer komplexa frågor körs. Du kan använda [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) att mata ut samtidighet behoven hos dina aktuella MongoDB-data.
- **Fråga efter mönster**: Komplexiteten för en fråga påverkar hur många enheter för programbegäran som förbrukas av frågan.

Om du exporterar JSON-filer med hjälp av [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) och förstå hur många skrivningar, läser, uppdaterar och tar bort som görs per sekund, kan du använda den [kapacitetsplaneringsverktyget för Azure Cosmos DB](https://www.documentdb.com/capacityplanner) att uppskatta den Antal enheter för programbegäran att etablera. Capacity planner ta inte med i kostnaden för mer komplexa frågor. Så om du har avancerade frågor på dina data ytterligare ru: er ska användas. Kalkylatorn förutsätter också att alla fält som ska indexeras sessionskonsekvens används. Det bästa sättet att förstå kostnaden för frågor är att migrera dina data (eller exempeldata) till Azure Cosmos DB, [ansluta till Cosmos-DB-slutpunkten](connect-mongodb-account.md) och kör en exempelfråga i MongoDB-gränssnittet med hjälp av den `getLastRequestStastistics` kommando för att hämta den kostnad för begäran, vilket kommer att skrivas ut antalet antalet förbrukade ru:

`db.runCommand({getLastRequestStatistics: 1})`

Det här kommandot genererar ett JSON-dokument som liknar följande:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

När du förstår hur många ru: er som används av en fråga och samtidigheten behöver för att fråga, kan du justera antalet etablerade ru: er. Optimera ru: er är inte en engångshändelse – du kontinuerligt bör optimera eller skala upp de ru som etableras, beroende på om du inte förväntade överbelastning, till skillnad från hård belastning och importera data.

## <a id="partitioning"></a>Välj din partitionsnyckel
Partitionering är en viktig aspekt av att överväga innan du migrerar till en globalt distribuerad databas som Azure Cosmos DB. Azure Cosmos DB använder partitionering för att skala enskilda behållare i en databas så att den passar skalbarhet och prestanda för ditt program. Vid partitionering, är objekten i en behållare indelade i olika delmängder som kallas logiska partitioner. Information och rekommendationer om hur du väljer rätt Partitionsnyckeln för dina data finns i den [välja en partitionsnyckel avsnittet](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indexera data
Som standard indexerar Azure Cosmos DB alla datafält vid inmatning. Du kan ändra den [indexeringspolicy](index-policy.md) i Azure Cosmos DB när som helst. I själva verket rekommenderas ofta inaktivera indexering när du migrerar data och sedan aktivera det igen när data finns redan i Cosmos DB. Mer information om indexering, kan du läsa mer om det i den [indexering i Azure Cosmos DB](index-overview.md) avsnittet. 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migrerar automatiskt MongoDB samlingar med unika index. Dock måste du skapa unika index före migreringen. Azure Cosmos DB stöder inte att skapa unika index när det redan finns data i samlingar. Mer information finns i [unika nycklar i Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Nästa steg
* [Migrera dina MongoDB-data till Cosmos DB med Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Etablera dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md)
* [Partitionering i Azure Cosmos DB](partition-data.md)
* [Global Distribution i Azure Cosmos DB](distribute-data-globally.md)
* [Indexering i Azure Cosmos DB](index-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
