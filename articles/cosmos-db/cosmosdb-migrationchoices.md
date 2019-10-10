---
title: Alternativ för Cosmos DB migrering
description: Det här dokumentet beskriver de olika alternativen för att migrera dina lokala eller molnbaserade data till Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: bharathb
ms.openlocfilehash: 0a2423421c6dfda02646546a6e071c8c78396f2c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170690"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Alternativ för att migrera dina lokala eller molnbaserade data till Azure Cosmos DB

Du kan läsa in data från olika data källor till Azure Cosmos DB. Dessutom, eftersom Azure Cosmos DB stöder flera API: er, kan målen vara alla befintliga API: er. För att stödja migrations vägar från olika källor till de olika Azure Cosmos DB-API: erna finns det flera lösningar som ger specialiserad hantering för varje sökväg för migrering. Det här dokumentet listar de tillgängliga lösningarna och beskriver deras fördelar och begränsningar.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Faktorer som påverkar valet av Migreringsverktyg

Följande faktorer avgör valet av Migreringsverktyg:
* **Online vs offline-migrering**: många Migreringsverktyg tillhandahåller en sökväg för endast en migrering. Det innebär att program som har åtkomst till databasen kan råka ut för en drifts tid. Vissa migrations lösningar är ett sätt att utföra en Direktmigrering där en replikerings-pipeline har kon figurer ATS mellan källan och målet.

* **Data källa**: befintliga data kan finnas i olika data källor som Oracle DB2, DataStax Cassanda, Azure SQL Server, postgresql osv. Data kan också finnas i ett befintligt Azure Cosmos DB konto och syftet med migreringen kan vara att ändra data modellen eller partitionera om data i en behållare med en annan partitionsnyckel.

* **Azure Cosmos DB-API**: för SQL-API: et i Azure Cosmos DB finns det flera olika verktyg som har utvecklats av Azure Cosmos DB teamet som har stöd för olika migrerings scenarier. Alla andra API: er har sina egna specialiserade verktyg som utvecklats och underhålls av communityn. Eftersom Azure Cosmos DB stöder dessa API: er på nivån Wire Protocol, bör dessa verktyg fungera som de är medan man migrerar data till Azure Cosmos DB. De kan dock kräva anpassad hantering för begränsningar eftersom det här konceptet är begränsat till Azure Cosmos DB.

* **Storlek på data**: de flesta Migreringsverktyg fungerar bra för mindre data uppsättningar. När data uppsättningen överstiger några hundra gigabyte är alternativen för migrering begränsade. 

* **Förväntad varaktighet för migrering**: migreringar kan konfigureras för att ske i en långsam, stegvis hastighet som förbrukar mindre genomflöde eller som kan använda hela det data flöde som har allokerats på mål Azure Cosmos DBS behållaren och slutföra migreringen på mindre tid.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Anslutningen|[Migreringsverktyg för data](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; enkel att konfigurera och stödja flera källor <br/>&bull; lämpar sig inte för stora data mängder|
|Anslutningen|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; enkel att konfigurera och stödja flera källor <br/>&bull; använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull; lämpligt för stora data uppsättningar <br/>&bull; brist på kontroll punkt – det innebär att om ett problem inträffar under migreringen måste du starta om hela migreringsprocessen<br/>&bull; avsaknad av kö för obeställbara meddelanden – det innebär att några felaktiga filer kan stoppa hela migreringsprocessen.|
|Anslutningen|[Azure Cosmos DB Spark-anslutning](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull; lämpligt för stora data uppsättningar <br/>&bull; kräver en anpassad Spark-installation <br/>&bull; Spark är känsligt för inkonsekvenser i schemat och det kan vara ett problem under migreringen |
|Anslutningen|[Anpassat verktyg med Cosmos DB-bibliotek för Mass utförar](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull; tillhandahåller kontroll punkter, funktioner för obeställbara meddelanden som ökar återhämtnings kapaciteten för migreringen <br/>&bull; lämpat för mycket stora data uppsättningar (10 TB +)  <br/>&bull; kräver anpassad installation av verktyget som körs som ett App Service |
|Online|[Cosmos DB Functions + ChangeFeed-API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull; enkel att konfigurera <br/>&bull; fungerar bara om källan är en Azure Cosmos DB behållare <br/>&bull; lämpar sig inte för stora data mängder <br/>&bull; fångar inte bort borttagningar från käll behållaren |
|Online|[Tjänsten för anpassad migrering med ChangeFeed](https://aka.ms/CosmosDBMigrationSample)|&bull; tillhandahåller förlopps spårning <br/>&bull; fungerar bara om källan är en Azure Cosmos DB behållare <br/>&bull; fungerar även för större data uppsättningar <br/>&bull; kräver att användaren konfigurerar en App Service som värd för bytet av Change feed <br/>&bull; fångar inte bort borttagningar från käll behållaren|
|Online|[Striims](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull; fungerar med en stor mängd olika källor som Oracle, DB2, SQL Server <br/>&bull; enkelt att bygga ETL-pipelines och ger en instrument panel för övervakning <br/>&bull; stöder större data uppsättningar <br/>&bull; eftersom det här är ett tredjepartsverktyg, måste det köpas från Marketplace och installeras i användarens miljö|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo-API
|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Anslutningen|[Migreringsverktyg för data](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; enkel att konfigurera och stödja flera källor <br/>&bull; lämpar sig inte för stora data mängder|
|Anslutningen|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; enkel att konfigurera och stödja flera källor <br/>&bull; använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull; lämpligt för stora data uppsättningar <br/>&bull; brist på kontroll punkter innebär att eventuella problem under migreringen skulle kräva en omstart av hela migreringsprocessen<br/>&bull; avsaknad av kö för obeställbara meddelanden skulle innebära att några felaktiga filer kan stoppa hela migreringsprocessen <br/>&bull; behöver anpassad kod för att öka Läs data flödet för vissa data källor|
|Anslutningen|[Befintliga Mongo-verktyg (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; enkel att konfigurera och integrera <br/>&bull; kräver anpassad hantering för begränsningar|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull; lämpar sig för stora data uppsättningar och tar hand om att replikera Live-ändringar <br/>&bull; fungerar endast med andra MongoDB-källor|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra-API
|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Anslutningen|[cqlsh COPY-kommando](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull; enkel att konfigurera <br/>&bull; lämpar sig inte för stora data mängder <br/>&bull; fungerar bara när källan är en Cassandra-tabell|
|Anslutningen|[Kopiera tabell med Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull; kan använda Spark-funktioner för att parallellisera omvandling och inmatning <br/>&bull; behöver konfigureras med en anpassad princip för återförsök för att hantera begränsningar|
|Online|[Striims (från Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull; fungerar med en stor mängd olika källor som Oracle, DB2, SQL Server <br/>&bull; enkelt att bygga ETL-pipelines och ger en instrument panel för övervakning <br/>&bull; stöder större data uppsättningar <br/>&bull; eftersom det här är ett tredjepartsverktyg, måste det köpas från Marketplace och installeras i användarens miljö|
|Online|[Blitzz (från Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull; stöder större data uppsättningar <br/>&bull; eftersom det här är ett tredjepartsverktyg, måste det köpas från Marketplace och installeras i användarens miljö|

## <a name="other-apis"></a>Andra API: er
För andra API: er än SQL API, Mongo API och API för Cassandra, finns det olika verktyg som stöds av varje API: s befintliga eko system. 

**Table API** 
* [Migreringsverktyg för data](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin-API**
* [Graph utförar-bibliotek](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer genom att testa exempel programmen som använder bulk utförar-biblioteket i [.net](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md). 
* Bulk utförar-biblioteket är integrerat i Cosmos DB Spark-anslutningsprogrammet för mer information finns i artikeln [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Kontakta Azure Cosmos DB produkt teamet genom att öppna ett support ärende under "allmän råd givande" problem typ och "stora (TB +) migreringar" problem under typ för ytterligare hjälp med storskalig migrering. 
* Använd [Cosmos DB start program](https://azurecosmosdb.github.io/CosmosBootstrap/) för att påskynda skapandet eller migreringen av dina program på Azure Cosmos dB.

> [!div class="nextstepaction"]
> [Cosmos DB start program](https://azurecosmosdb.github.io/CosmosBootstrap/)
