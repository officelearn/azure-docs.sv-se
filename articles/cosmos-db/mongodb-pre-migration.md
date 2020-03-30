---
title: Steg före migrering för datamigrering till Azure Cosmos DB:s API för MongoDB
description: Det här dokumentet ger en översikt över förutsättningarna för en datamigrering från MongoDB till Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942084"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Steg före migrering för datamigreringar från MongoDB till Azure Cosmos DB:s API för MongoDB

Innan du migrerar dina data från MongoDB (antingen lokalt eller i molnet) till Azure Cosmos DB:s API för MongoDB bör du:

1. [Läs de viktigaste övervägandena om hur du använder Azure Cosmos DB:s API för MongoDB](#considerations)
2. [Välj ett alternativ för att migrera data](#options)
3. [Uppskatta det dataflöde som behövs för dina arbetsbelastningar](#estimate-throughput)
4. [Välj en optimal partitionsnyckel för dina data](#partitioning)
5. [Förstå den indexeringsprincip som du kan ange på dina data](#indexing)

Om du redan har slutfört ovanstående förutsättningar för migreringen kan du [migrera MongoDB-data till Azure Cosmos DB:s API för MongoDB med hjälp av Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md). Om du inte har skapat ett konto kan du dessutom bläddra bland någon av [snabbstarterna](create-mongodb-dotnet.md) som visar stegen för att skapa ett konto.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Överväganden vid användning av Azure Cosmos DB:s API för MongoDB

Följande är specifika egenskaper om Azure Cosmos DB:s API för MongoDB:

- **Kapacitetsmodell**: Databaskapacitet på Azure Cosmos DB baseras på en dataflödesbaserad modell. Den här modellen baseras på [begärandenheter per sekund](request-units.md), vilket är en enhet som representerar antalet databasåtgärder som kan utföras mot en samling per sekund. Den här kapaciteten kan allokeras på [databas- eller insamlingsnivå](set-throughput.md)och den kan etableras på en allokeringsmodell eller använda [autopilotmodellen](provision-throughput-autopilot.md).

- **Begäranheter:** Varje databasåtgärd har en associerad begärandeenheter (RU: er) kostnad i Azure Cosmos DB. När den körs subtraheras detta från den tillgängliga nivån för begärandenheter på en viss sekund. Om en begäran kräver fler ru:er än de ru/s som för närvarande allokerats finns det två alternativ för att lösa problemet - öka mängden ru:er eller vänta tills nästa sekund startar och försök sedan igen.

- **Elastisk kapacitet**: Kapaciteten för en viss samling eller databas kan ändras när som helst. Detta gör det möjligt för databasen att elastiskt anpassa sig till dataflödeskraven för din arbetsbelastning.

- **Automatisk sharding**: Azure Cosmos DB tillhandahåller ett automatiskt partitioneringssystem som bara kräver en fragment (eller en partitionsnyckel). Den [automatiska partitioneringsmekanismen](partition-data.md) delas över alla Azure Cosmos DB-API:er och möjliggör sömlösa data och genom skalning genom horisontell distribution.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Migreringsalternativ för Azure Cosmos DB:s API för MongoDB

[Azure Database Migration Service för Azure Cosmos DB:s API för MongoDB](../dms/tutorial-mongodb-cosmos-db.md) tillhandahåller en mekanism som förenklar datamigrering genom att tillhandahålla en fullständigt hanterad värdplattform, migreringsövervakningsalternativ och automatisk begränsningshantering. Den fullständiga listan med alternativ är följande:

|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Offline|[Verktyget Datamigrering](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Lätt att ställa in och stöder flera källor <br/>&bull;Inte lämplig för stora datamängder.|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Lätt att ställa in och stöder flera källor <br/>&bull;Använder azure Cosmos DB bulk executor-bibliotek <br/>&bull;Lämplig för stora datamängder <br/>&bull;Brist på kontrollpunkter innebär att alla problem under migreringen skulle kräva en omstart av hela migreringsprocessen<br/>&bull;Avsaknad av en död brev kö skulle innebära att några felaktiga filer kan stoppa hela migreringsprocessen <br/>&bull;Behöver anpassad kod för att öka läsflödet för vissa datakällor|
|Offline|[Befintliga Mongo Tools (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Lätt att ställa in och integration <br/>&bull;Behöver anpassad hantering för gasspjäll|
|Online|[Tjänsten för migrering av Azure-databas](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;Fullständigt hanterad migreringstjänst.<br/>&bull;Tillhandahåller värd- och övervakningslösningar för migreringsuppgiften. <br/>&bull;Lämplig för stora datamängder och tar hand om replikera live-ändringar <br/>&bull;Fungerar endast med andra MongoDB-källor|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>Uppskatta behovet av dataflöde för dina arbetsbelastningar

I Azure Cosmos DB etableras dataflödet i förväg och mäts i BEGÄRANdeenheter (RU) per sekund. Till skillnad från virtuella datorer eller lokala servrar är ru:er lätta att skala upp och ned när som helst. Du kan ändra antalet etablerade ru:er direkt. Mer information finns [i Begär enheter i Azure Cosmos DB](request-units.md).

Du kan använda [Azure Cosmos DB Capacity Calculator](https://cosmos.azure.com/capacitycalculator/) för att bestämma mängden begärandeenheter baserat på din databaskontokonfiguration, mängden data, dokumentstorlek och nödvändiga läsningar och skrivningar per sekund.

Följande är nyckelfaktorer som påverkar antalet rus som krävs:
- **Dokumentstorlek**: När storleken på ett objekt/dokument ökar ökar också antalet ru:er som förbrukas för att läsa eller skriva objektet/dokumentet.

- **Antal dokumentegenskaper**:Antalet ru:er som förbrukas för att skapa eller uppdatera ett dokument är relaterat till antalet, komplexiteten och längden på dess egenskaper. Du kan minska förbrukningen av begärandenhet för skrivåtgärder genom [att begränsa antalet indexerade egenskaper](mongodb-indexing.md).

- **Frågemönster**: Komplexiteten i en fråga påverkar hur många begärandeenheter som förbrukas av frågan. 

Det bästa sättet att förstå kostnaden för frågor är att använda exempeldata i Azure Cosmos DB och `getLastRequestStastistics` köra [exempelfrågor från MongoDB-skalet](connect-mongodb-account.md) med kommandot för att hämta begäranden, som kommer att mata ut antalet ru:er som förbrukas:

`db.runCommand({getLastRequestStatistics: 1})`

Det här kommandot kommer att mata ut ett JSON-dokument som liknar följande:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Du kan också använda [diagnostikinställningarna](cosmosdb-monitor-resource-logs.md) för att förstå frekvensen och mönstren för de frågor som körs mot Azure Cosmos DB. Resultaten från diagnostikloggarna kan skickas till ett lagringskonto, en EventHub-instans eller [Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Välj partitionsnyckel
Partitionering, även känd som Sharding, är en viktig fråga innan du migrerar data. Azure Cosmos DB använder fullständigt hanterad partitionering för att öka kapaciteten i en databas för att uppfylla lagrings- och dataflödeskraven. Den här funktionen behöver inte värdskap eller konfiguration av routningsservrar.   

På liknande sätt lägger partitioneringsfunktionen automatiskt till kapacitet och balanserar om data därefter. Mer information och rekommendationer om hur du väljer rätt partitionsnyckel för dina data finns i [artikeln Välja en partitionsnyckel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indexera dina data
Som standard tillhandahåller Azure Cosmos DB automatisk indexering på alla data som infogas. Indexeringsfunktionerna i Azure Cosmos DB inkluderar att lägga till sammansatta index, unika index och TTL-index (Time-to-live). Indexhanteringsgränssnittet mappas `createIndex()` till kommandot. Läs mer vid [Indexering i Azure Cosmos DB:s API för MongoDB](mongodb-indexing.md).

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migrerar automatiskt MongoDB-samlingar med unika index. De unika indexen måste dock skapas före migreringen. Azure Cosmos DB stöder inte skapandet av unika index, när det redan finns data i dina samlingar. Mer information finns [i Unika nycklar i Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Nästa steg
* [Migrera dina MongoDB-data till Cosmos DB med hjälp av databasmigreringstjänsten.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Etablera dataflöde på Azure Cosmos-behållare och databaser](set-throughput.md)
* [Partitionering i Azure Cosmos DB](partition-data.md)
* [Global distribution i Azure Cosmos DB](distribute-data-globally.md)
* [Indexering i Azure Cosmos DB](index-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
