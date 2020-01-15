---
title: Steg före migrering för datamigrering till Azure Cosmos DBs API för MongoDB
description: Det här dokumentet innehåller en översikt över förutsättningarna för en datamigrering från MongoDB till Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942084"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Steg före migrering för datamigrering från MongoDB till Azure Cosmos DB s API för MongoDB

Innan du migrerar dina data från MongoDB (antingen lokalt eller i molnet) till Azure Cosmos DB s API för MongoDB, bör du:

1. [Läs viktiga överväganden om hur du använder Azure Cosmos DBs API för MongoDB](#considerations)
2. [Välj ett alternativ för att migrera dina data](#options)
3. [Beräkna det data flöde som krävs för dina arbets belastningar](#estimate-throughput)
4. [Välj en optimal partitionsnyckel för dina data](#partitioning)
5. [Förstå indexerings principen som du kan ange för dina data](#indexing)

Om du redan har slutfört ovanstående krav för migrering kan du [migrera MongoDB-data till Azure Cosmos DB s API för MongoDB med hjälp av Azure Database migration service](../dms/tutorial-mongodb-cosmos-db.md). Om du inte har skapat ett konto kan du dessutom bläddra bland de [snabb starter](create-mongodb-dotnet.md) som visar stegen för att skapa ett konto.

## <a id="considerations"></a>Att tänka på när du använder Azure Cosmos DBs API för MongoDB

Följande är särskilda egenskaper för Azure Cosmos DB s API för MongoDB:

- **Kapacitets modell**: databasens kapacitet på Azure Cosmos DB baseras på en data flödes-baserad modell. Den här modellen är baserad på [enheter för programbegäran per sekund](request-units.md), som är en enhet som representerar antalet databas åtgärder som kan utföras mot en samling per sekund. Den här kapaciteten kan allokeras på [en databas eller samlings nivå](set-throughput.md)och kan tillhandahållas i en fördelnings modell, eller med hjälp av [autopilot-modellen](provision-throughput-autopilot.md).

- **Enheter för programbegäran**: varje databas åtgärd har en ru: er-kostnad (associerad Request units) i Azure Cosmos dB. Vid körning subtraheras detta från den tillgängliga enhets enhets nivån på en specifik sekund. Om en begäran kräver mer ru: er än den för närvarande allokerade RU/s finns det två alternativ för att lösa problemet, öka mängden ru: er eller vänta tills nästa sekund startar och försök sedan igen.

- **Elastisk kapacitet**: kapaciteten för en specifik samling eller databas kan ändras när som helst. Detta gör att databasen kan anpassas elastiskt till data flödes kraven för din arbets belastning.

- **Automatisk horisontell partitionering**: Azure Cosmos DB tillhandahåller ett automatiskt partitionerings system som bara kräver en Shard (eller en partitionsnyckel). [Mekanismen för automatisk partitionering](partition-data.md) delas över alla Azure Cosmos DB-API: er och möjliggör sömlös data och genom hela skalan genom horisontell distribution.

## <a id="options"></a>Migrations alternativ för Azure Cosmos DBs API för MongoDB

[Azure Database migration service för Azure Cosmos DB s API för MongoDB](../dms/tutorial-mongodb-cosmos-db.md) ger en mekanism som fören klar migreringen av data genom att tillhandahålla en helt hanterad värd plattform, alternativ för migrerings övervakning och automatisk begränsnings hantering. Den fullständiga listan med alternativ är följande:

|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Offline|[Datamigreringsverktyget](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; enkelt att konfigurera och stödja flera källor <br/>&bull; passar inte för stora data mängder.|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; enkelt att konfigurera och stödja flera källor <br/>&bull; använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull; lämplig för stora data uppsättningar <br/>&bull; brist på kontroll punkter innebär att eventuella problem under migreringen skulle kräva en omstart av hela migreringsprocessen<br/>&bull; avsaknad av en kö för obeställbara meddelanden skulle innebära att några felaktiga filer kan stoppa hela migreringsprocessen <br/>&bull; behöver anpassad kod för att öka Läs data flödet för vissa data källor|
|Offline|[Befintliga Mongo-verktyg (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; enkel att konfigurera och integrera <br/>&bull; behöver anpassad hantering för begränsningar|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; fullständigt hanterad migreringstjänster.<br/>&bull; tillhandahåller värdbaserade och övervaknings lösningar för migreringen. <br/>&bull; lämpligt för stora data uppsättningar och tar hand om att replikera Live-ändringar <br/>&bull; fungerar endast med andra MongoDB-källor|


## <a id="estimate-throughput"></a>Beräkna data flödes behovet för dina arbets belastningar

I Azure Cosmos DB allokeras data flödet i förväg och mäts i enheter för programbegäran (RU) per sekund. Till skillnad från virtuella datorer eller lokala servrar är ru: er enkelt att skala upp och ned när som helst. Du kan ändra antalet etablerade ru: er direkt. Mer information finns i [enheter för programbegäran i Azure Cosmos DB](request-units.md).

Du kan använda [Azure Cosmos DB kapacitets kalkylatorn](https://cosmos.azure.com/capacitycalculator/) för att fastställa hur många enheter för programbegäran som baseras på databas konto konfigurationen, mängden data, dokument storlek och nödvändiga läsningar och skrivningar per sekund.

Följande är viktiga faktorer som påverkar antalet ru: er som krävs:
- **Dokument storlek**: när ett objekts eller dokuments storlek ökar ökar antalet ru: er som förbrukas för att läsa eller skriva objektet/dokumentet också att öka.

- **Antal dokument egenskaper**: antalet ru: er som används för att skapa eller uppdatera ett dokument är relaterat till antal, komplexitet och längd på egenskaperna. Du kan minska användningen av begär ande enheter för Skriv åtgärder genom [att begränsa antalet indexerade egenskaper](mongodb-indexing.md).

- **Fråga mönster**: en frågas komplexitet påverkar hur många enheter för programbegäran som används av frågan. 

Det bästa sättet att förstå kostnaden för frågor är att använda exempel data i Azure Cosmos DB [och köra exempel frågor från MongoDB-gränssnittet](connect-mongodb-account.md) med hjälp av kommandot `getLastRequestStastistics` för att hämta begär ande avgiften, vilket kommer att returnera antalet ru: er som förbrukas:

`db.runCommand({getLastRequestStatistics: 1})`

Det här kommandot kommer att skriva ut ett JSON-dokument som liknar följande:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Du kan också använda [diagnostikinställningar](cosmosdb-monitor-resource-logs.md) för att förstå frekvensen och mönstren för de frågor som körs mot Azure Cosmos dB. Resultaten från diagnostikloggar kan skickas till ett lagrings konto, en EventHub-instans eller Azure- [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).  

## <a id="partitioning"></a>Välj partitionsnyckel
Partitionering, även kallat horisontell partitionering, är en viktig faktor innan data migreras. Azure Cosmos DB använder fullständigt hanterad partitionering för att öka kapaciteten i en databas för att uppfylla kraven för lagring och data flöde. Den här funktionen behöver inte vara värd för eller konfigurering av routningsservrar.   

På ett liknande sätt lägger partitionerings kapaciteten automatiskt till kapacitet och återskapar data enligt detta. Mer information och rekommendationer om hur du väljer rätt partitionsnyckel för dina data finns i artikeln om [att välja en partitionsnyckel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indexera dina data
Som standard tillhandahåller Azure Cosmos DB automatisk indexering för alla data som infogas. De indexerings funktioner som tillhandahålls av Azure Cosmos DB inkluderar att lägga till sammansatta index, unika index och Time to Live (TTL) index. Index hanterings gränssnittet mappas till `createIndex()`-kommandot. Läs mer vid [indexering i Azure Cosmos DBS API för MongoDB](mongodb-indexing.md).

[Azure Database migration service](../dms/tutorial-mongodb-cosmos-db.md) migrerar automatiskt MongoDB-samlingar med unika index. Däremot måste de unika indexen skapas innan migreringen. Azure Cosmos DB har inte stöd för att skapa unika index när det redan finns data i samlingarna. Mer information finns i [unika nycklar i Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Nästa steg
* [Migrera dina MongoDB-data till Cosmos DB med hjälp av Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Etablera data flöde i Azure Cosmos-behållare och databaser](set-throughput.md)
* [Partitionering i Azure Cosmos DB](partition-data.md)
* [Global distribution i Azure Cosmos DB](distribute-data-globally.md)
* [Indexering i Azure Cosmos DB](index-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
