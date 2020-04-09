---
title: Alternativ för migrering av Cosmos DB
description: Det här dokumentet beskriver de olika alternativen för att migrera lokala data eller molndata till Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 34698a215477abdd7d68c3dfe050657ecf049690
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984903"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Alternativ för att migrera lokala data eller molndata till Azure Cosmos DB

Du kan läsa in data från olika datakällor till Azure Cosmos DB. Eftersom Azure Cosmos DB stöder flera API:er kan målen dessutom vara någon av de befintliga API:erna. För att stödja migreringsvägar från de olika källorna till de olika Azure Cosmos DB-API:erna finns det flera lösningar som tillhandahåller specialiserad hantering för varje migreringsväg. Det här dokumentet listar tillgängliga lösningar och beskriver deras fördelar och begränsningar.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Faktorer som påverkar valet av migreringsverktyg

Följande faktorer avgör valet av migreringsverktyget:
* **Online vs offline migrering:** Många migreringsverktyg ger en väg för att göra en engångsmigrering bara. Det innebär att de program som kommer åt databasen kan uppleva en period av driftstopp. Vissa migreringslösningar är ett sätt att göra en direktmigrering där det finns en replikeringspipeline som ställts in mellan källan och målet.

* **Datakälla:** Befintliga data kan finnas i olika datakällor som Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL, etc. Data kan också finnas i ett befintligt Azure Cosmos DB-konto och avsikten med migreringen kan vara att ändra datamodellen eller partitionera om data i en behållare med en annan partitionsnyckel.

* **Azure Cosmos DB API:** För SQL API i Azure Cosmos DB finns det en mängd olika verktyg som utvecklats av Azure Cosmos DB-teamet som hjälper till i de olika migreringsscenarierna. Alla andra API: er har sin egen specialiserade uppsättning verktyg som utvecklats och underhålls av samhället. Eftersom Azure Cosmos DB stöder dessa API:er på trådprotokollnivå, bör dessa verktyg fungera som de är medan du migrerar data till Azure Cosmos DB också. De kan dock kräva anpassad hantering för begränsningar eftersom det här konceptet är specifikt för Azure Cosmos DB.

* **Datastorlek:** De flesta migreringsverktyg fungerar mycket bra för mindre datauppsättningar. När datauppsättningen överstiger några hundra gigabyte är alternativen för migreringsverktyg begränsade. 

* **Förväntad migreringsvaraktighet:** Migreringar kan konfigureras för att ske i en långsam, inkrementell takt som förbrukar mindre dataflöde eller kan använda hela dataflödet som etablerats på azure Cosmos DB-behållaren och slutföra migreringen på kortare tid.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Offline|[Verktyget Datamigrering](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Lätt att ställa in och stöder flera källor <br/>&bull;Inte lämplig för stora datamängder|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Lätt att ställa in och stöder flera källor <br/>&bull;Använder azure Cosmos DB bulk executor-bibliotek <br/>&bull;Lämplig för stora datamängder <br/>&bull;Brist på kontrollpunkter - Det innebär att om ett problem uppstår under migreringen, måste du starta om hela migreringsprocessen<br/>&bull;Brist på en kö för obeställbara brev - Det innebär att några felaktiga filer kan stoppa hela migreringsprocessen.|
|Offline|[Azure Cosmos DB Spark-anslutning](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Använder azure Cosmos DB bulk executor-bibliotek <br/>&bull;Lämplig för stora datamängder <br/>&bull;Behöver en anpassad Spark-konfiguration <br/>&bull;Spark är känslig för schemainkonsekvenser och detta kan vara ett problem under migreringen |
|Offline|[Anpassat verktyg med Cosmos DB bulk executor bibliotek](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Tillhandahåller kontrollpunkter, funktioner för obeställbara bokstäver som ökar migrationens återhämtningsförmåga <br/>&bull;Lämplig för mycket stora datamängder (10 TB+)  <br/>&bull;Kräver anpassad installation av det här verktyget som körs som en apptjänst |
|Online|[Cosmos DB-funktioner + ChangeFeed-API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Lätt att ställa in <br/>&bull;Fungerar bara om källan är en Azure Cosmos DB-behållare <br/>&bull;Inte lämplig för stora datamängder <br/>&bull;Tar inte bort från källbehållaren |
|Online|[Anpassad migreringstjänst med ChangeFeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Ger förloppsspårning <br/>&bull;Fungerar bara om källan är en Azure Cosmos DB-behållare <br/>&bull;Fungerar även för större datamängder <br/>&bull;Kräver att användaren konfigurerar en apptjänst som värd för ändringsflödesprocessorn <br/>&bull;Tar inte bort från källbehållaren|
|Online|[Striim (Striim)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Fungerar med en mängd olika källor som Oracle, DB2, SQL Server <br/>&bull;Lätt att bygga ETL-rörledningar och ger en instrumentpanel för övervakning <br/>&bull;Stöder större datauppsättningar <br/>&bull;Eftersom detta är ett verktyg från tredje part måste det köpas från marknadsplatsen och installeras i användarens miljö|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Offline|[Verktyget Datamigrering](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Lätt att ställa in och stöder flera källor <br/>&bull;Inte lämplig för stora datamängder|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Lätt att ställa in och stöder flera källor <br/>&bull;Använder azure Cosmos DB bulk executor-bibliotek <br/>&bull;Lämplig för stora datamängder <br/>&bull;Brist på kontrollpunkter innebär att alla problem under migreringen skulle kräva en omstart av hela migreringsprocessen<br/>&bull;Avsaknad av en död brev kö skulle innebära att några felaktiga filer kan stoppa hela migreringsprocessen <br/>&bull;Behöver anpassad kod för att öka läsflödet för vissa datakällor|
|Offline|[Befintliga Mongo Tools (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Lätt att ställa in och integration <br/>&bull;Behöver anpassad hantering för gasspjäll|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Använder azure Cosmos DB bulk executor-bibliotek <br/>&bull;Lämplig för stora datamängder och tar hand om replikera live-ändringar <br/>&bull;Fungerar endast med andra MongoDB-källor|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Offline|[cqlsh KOPIERA, kommando](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Lätt att ställa in <br/>&bull;Inte lämplig för stora datamängder <br/>&bull;Fungerar bara när källan är en Cassandra-tabell|
|Offline|[Kopiera tabell med Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Kan använda Spark-funktioner för att parallellisera omvandling och intag <br/>&bull;Behöver konfiguration med en anpassad återförsöksprincip för att hantera begränsningar|
|Online|[Striim (från Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Fungerar med en mängd olika källor som Oracle, DB2, SQL Server <br/>&bull;Lätt att bygga ETL-rörledningar och ger en instrumentpanel för övervakning <br/>&bull;Stöder större datauppsättningar <br/>&bull;Eftersom detta är ett verktyg från tredje part måste det köpas från marknadsplatsen och installeras i användarens miljö|
|Online|[Blitzz (från Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Stöder större datauppsättningar <br/>&bull;Eftersom detta är ett verktyg från tredje part måste det köpas från marknadsplatsen och installeras i användarens miljö|

## <a name="other-apis"></a>Andra API:er
För andra API:er än SQL API, Mongo API och Cassandra API finns det olika verktyg som stöds av var och en av API:nas befintliga ekosystem. 

**Tabell-API** 
* [Verktyget Datamigrering](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzKopia](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin-API**
* [Diagram bulk executor bibliotek](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Gnista](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Nästa steg

* Läs mer genom att prova exempelprogrammen som använder massutdrivarbiblioteket i [.NET](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md). 
* Massutnämningsbiblioteket är integrerat i Cosmos DB Spark-kopplingen, mer information finns i azure [cosmos DB Spark-anslutningsartikeln.](spark-connector.md)  
* Kontakta Azure Cosmos DB-produktteamet genom att öppna en supportbiljett under problemtypen "Allmän rådgivning" och "Stora (TB+) migreringar" för ytterligare hjälp med storskaliga migreringar.
