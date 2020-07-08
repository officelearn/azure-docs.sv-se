---
title: Alternativ för Cosmos DB migrering
description: Det här dokumentet beskriver de olika alternativen för att migrera dina lokala eller molnbaserade data till Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: a1b8ddba84920d8d3b6871ab404081d3b24c72e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261978"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Alternativ för att migrera dina lokala eller molnbaserade data till Azure Cosmos DB

Du kan läsa in data från olika data källor till Azure Cosmos DB. Dessutom, eftersom Azure Cosmos DB stöder flera API: er, kan målen vara alla befintliga API: er. För att stödja migrations vägar från olika källor till de olika Azure Cosmos DB-API: erna finns det flera lösningar som ger specialiserad hantering för varje sökväg för migrering. Det här dokumentet listar de tillgängliga lösningarna och beskriver deras fördelar och begränsningar.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Faktorer som påverkar valet av Migreringsverktyg

Följande faktorer avgör valet av Migreringsverktyg:
* **Online vs offline-migrering**: många Migreringsverktyg tillhandahåller en sökväg för endast en migrering. Det innebär att program som har åtkomst till databasen kan råka ut för en drifts tid. Vissa migrations lösningar är ett sätt att utföra en Direktmigrering där en replikerings-pipeline har kon figurer ATS mellan källan och målet.

* **Data källa**: befintliga data kan finnas i olika data källor som Oracle DB2, DataStax Cassanda, Azure SQL Database, postgresql osv. Data kan också finnas i ett befintligt Azure Cosmos DB konto och syftet med migreringen kan vara att ändra data modellen eller partitionera om data i en behållare med en annan partitionsnyckel.

* **Azure Cosmos DB-API**: för SQL-API: et i Azure Cosmos DB finns det flera olika verktyg som har utvecklats av Azure Cosmos DB teamet som har stöd för olika migrerings scenarier. Alla andra API: er har sina egna specialiserade verktyg som utvecklats och underhålls av communityn. Eftersom Azure Cosmos DB stöder dessa API: er på nivån Wire Protocol, bör dessa verktyg fungera som de är medan man migrerar data till Azure Cosmos DB. De kan dock kräva anpassad hantering för begränsningar eftersom det här konceptet är begränsat till Azure Cosmos DB.

* **Storlek på data**: de flesta Migreringsverktyg fungerar bra för mindre data uppsättningar. När data uppsättningen överstiger några hundra gigabyte är alternativen för migrering begränsade. 

* **Förväntad varaktighet för migrering**: migreringar kan konfigureras för att ske i en långsam, stegvis hastighet som förbrukar mindre data flöde eller kan använda hela data flödet som har allokerats på mål Azure Cosmos DBS behållaren och slutföra migreringen på kortare tid.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Offline|[Migreringsverktyg för data](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Enkelt att konfigurera och stödja flera källor <br/>&bull;Passar inte för stora data mängder|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Enkelt att konfigurera och stödja flera källor <br/>&bull;Använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull;Lämplig för stora data uppsättningar <br/>&bull;Brist på kontroll punkt – det innebär att om ett problem inträffar under migreringen måste du starta om hela migreringsprocessen<br/>&bull;Avsaknad av kö för obeställbara meddelanden – det innebär att några felaktiga filer kan stoppa hela migreringsprocessen.|
|Offline|[Azure Cosmos DB Spark-anslutning](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull;Lämplig för stora data uppsättningar <br/>&bull;Kräver en anpassad Spark-installation <br/>&bull;Spark är känsligt för inkonsekvenser i schemat och kan vara ett problem under migreringen |
|Offline|[Anpassat verktyg med Cosmos DB-bibliotek för Mass utförar](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Tillhandahåller kontroll punkter, funktioner för obeställbara meddelanden som ökar återhämtnings kapaciteten för migreringen <br/>&bull;Lämplig för mycket stora data uppsättningar (10 TB +)  <br/>&bull;Kräver anpassad installation av det här verktyget som körs som en App Service |
|Online|[Cosmos DB Functions + ChangeFeed-API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Enkelt att konfigurera <br/>&bull;Fungerar bara om källan är en Azure Cosmos DB behållare <br/>&bull;Passar inte för stora data mängder <br/>&bull;Fångar inte in borttagningar från käll behållaren |
|Online|[Tjänsten för anpassad migrering med ChangeFeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Tillhandahåller förlopps spårning <br/>&bull;Fungerar bara om källan är en Azure Cosmos DB behållare <br/>&bull;Fungerar även för större data uppsättningar <br/>&bull;Kräver att användaren konfigurerar en App Service som värd för bytet av Change-feed <br/>&bull;Fångar inte in borttagningar från käll behållaren|
|Online|[Striims](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Fungerar med en stor mängd olika källor som Oracle, DB2, SQL Server <br/>&bull;Lätt att bygga ETL-pipelines och ger en instrument panel för övervakning <br/>&bull;Stöder större data uppsättningar <br/>&bull;Eftersom det här är ett tredjepartsverktyg måste det köpas in från Marketplace och installeras i användarens miljö|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo-API
|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Offline|[Migreringsverktyg för data](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Enkelt att konfigurera och stödja flera källor <br/>&bull;Passar inte för stora data mängder|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Enkelt att konfigurera och stödja flera källor <br/>&bull;Använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull;Lämplig för stora data uppsättningar <br/>&bull;Brist på kontroll punkter innebär att eventuella problem under migreringen skulle kräva en omstart av hela migreringsprocessen<br/>&bull;Brist på en kö för obeställbara meddelanden skulle innebära att några felaktiga filer kan stoppa hela migreringsprocessen <br/>&bull;Behöver anpassad kod för att öka Läs data flödet för vissa data källor|
|Offline|[Befintliga Mongo-verktyg (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Enkelt att konfigurera och integrera <br/>&bull;Kräver anpassad hantering för begränsningar|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull;Lämplig för stora data uppsättningar och sköter att replikera Live-ändringar <br/>&bull;Fungerar endast med andra MongoDB-källor|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Offline|[cqlsh COPY-kommando](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Enkelt att konfigurera <br/>&bull;Passar inte för stora data mängder <br/>&bull;Fungerar bara när källan är en Cassandra-tabell|
|Offline|[Kopiera tabell med Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Kan använda Spark-funktioner för att parallellisera omvandling och inmatning <br/>&bull;Behöver konfigureras med en anpassad princip för återförsök för att hantera begränsningar|
|Online|[Striims (från Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Fungerar med en stor mängd olika källor som Oracle, DB2, SQL Server <br/>&bull;Lätt att bygga ETL-pipelines och ger en instrument panel för övervakning <br/>&bull;Stöder större data uppsättningar <br/>&bull;Eftersom det här är ett tredjepartsverktyg måste det köpas in från Marketplace och installeras i användarens miljö|
|Online|[Blitzz (från Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Stöder större data uppsättningar <br/>&bull;Eftersom det här är ett tredjepartsverktyg måste det köpas in från Marketplace och installeras i användarens miljö|

## <a name="other-apis"></a>Andra API:er
För andra API: er än SQL API, Mongo API och API för Cassandra, finns det olika verktyg som stöds av varje API: s befintliga eko system. 

**Tabell-API** 
* [Migreringsverktyg för data](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin-API**
* [Graph utförar-bibliotek](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer genom att testa exempel programmen som använder bulk utförar-biblioteket i [.net](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md). 
* Bulk utförar-biblioteket är integrerat i Cosmos DB Spark-anslutningsprogrammet för mer information finns i artikeln [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Kontakta Azure Cosmos DB produkt teamet genom att öppna ett support ärende under "allmän råd givande" problem typ och "stora (TB +) migreringar" problem under typ för ytterligare hjälp med storskalig migrering.
