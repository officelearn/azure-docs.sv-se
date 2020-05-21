---
title: 'Azure Cosmos DB SQL API: Java SDK v4-exempel'
description: Hitta Java-exempel på GitHub för vanliga uppgifter med Azure Cosmos DB SQL API, inklusive CRUD-åtgärder.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: db291b735ed6456a007446ad23373ff25e806aab
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727172"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: Java SDK v4-exempel

> [!div class="op_single_selector"]
> * [.NET v2 SDK-exempel](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-exempel](sql-api-dotnet-v3sdk-samples.md)
> * [Exempel för Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure-kodexempelgalleri](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Läs mer om Java SDK v4 i Azure Cosmos DB Java SDK v4 [viktig](sql-api-sdk-java-v4.md)information, [maven-lagringsplats](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4- [prestanda tips](performance-tips-java-sdk-v4-sql.md)och Azure Cosmos DB Java SDK v4- [fel söknings guide](troubleshoot-java-sdk-v4-sql.md) för mer information. Om du använder en äldre version än v4 kan du läsa om hur du uppgraderar till v4 i [Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): din Visual Studio-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

De senaste exempel programmen som utför CRUD-åtgärder och andra vanliga åtgärder på Azure Cosmos DB-resurser ingår i GitHub-lagringsplatsen [Azure-Cosmos-Java-SQL-API-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) . Den här artikeln innehåller:

* Länkar till uppgifterna i var och en av Java-projektfilerna. 
* Länkar till det relaterade API-referensinnehållet.

**Förutsättningar**

Du behöver följande för att köra det här exempelprogrammet:

* Java Development Kit 8
* Azure Cosmos DB Java SDK v4

Du kan också använda Maven för att hämta de senaste Azure Cosmos DB Java SDK v4-binärfilerna för användning i projektet. Maven lägger automatiskt till alla nödvändiga beroenden. Du kan även ladda ned beroendena direkt som listas i filen pom.xml och lägga till dem till din byggsökväg.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Köra exempelprogrammen**

Klona lagringsplatsexemplet:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Du kan köra exemplen med antingen en IDE (Sol förmörkelse, IntelliJ eller VSCODE) eller från kommando raden med hjälp av Maven.

De här miljövariablerna måste anges

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account master key
```

för att ge exempel Läs-och skriv åtkomst till ditt konto.

Om du vill köra ett exempel anger du dess huvud klass 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

där *Sample. Synchronicity. MainClass* kan vara
* crudquickstart. Sync. SampleCRUDQuickstart
* crudquickstart. async. SampleCRUDQuickstartAsync
* indexmanagement. Sync. SampleIndexManagement
* indexmanagement. async. SampleIndexManagementAsync
* StoredProcedure. Sync. SampleStoredProcedure
* StoredProcedure. async. SampleStoredProcedureAsync
* changefeed. SampleChangeFeedProcessor *(Changefeed har bara ett asynkront exempel, inget Sync-exempel.)* ... osv...

> [!NOTE]
> Varje exempel är självständigt. Det konfigurerar sig själv och rensar upp efter sig. Exemplen utfärdar flera anrop för att skapa en `CosmosContainer` . Varje gång det sker faktureras din prenumeration för en timmes användning för prestandanivån för den skapade samlingen. 
> 
> 

## <a name="database-examples"></a>Databasexempel
[Databasen CRUD exempel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) filen visar hur du utför följande uppgifter. Information om Azure Cosmos-databaser innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](databases-containers-items.md) konceptuell artikel. 

| Uppgift | API-referens |
| --- | --- |
| [Skapa en databas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L76-L84) | CosmosClient. createDatabaseIfNotExists |
| [Läsa en databas via ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L86-L94) | CosmosClient. getDatabase |
| [Läsa alla databaser](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L96-L111) | CosmosClient.readAllDatabases |
| [Ta bort en databas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L113-L122) | CosmosDatabase. Delete |

## <a name="collection-examples"></a>Samlingsexempel
[Exempel filen Collection CRUD-exempel](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) visar hur du utför följande uppgifter. Mer information om Azure Cosmos-samlingarna innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](databases-containers-items.md) konceptuell artikel.

| Uppgift | API-referens |
| --- | --- |
| [Skapa en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L94-106) | CosmosDatabase. createContainerIfNotExists |
| [Ändra konfigurerade prestanda i en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L108-116) | CosmosContainer.replaceProvisionedThroughput |
| [Hämta en samling via ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L118-126) | CosmosDatabase.getContainer |
| [Läsa alla samlingar i en databas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L128-143) | CosmosDatabase.readAllContainers |
| [Ta bort en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L145-154) | CosmosContainer. Delete |

## <a name="autoscale-collection-examples"></a>Exempel för autoskalning samling

Om du vill veta mer om autoskalning innan du kör dessa exempel kan du ta en titt på de här instruktionerna för att aktivera autoskalning i ditt [konto](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) och i dina [databaser och behållare](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale).

[CRUD exempel filen för autoskalning av databasen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) visar hur du utför följande uppgifter.

| Uppgift | API-referens |
| --- | --- |
| [Skapa en databas med angivet Max data flöde för autoskalning](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L76-L87) | CosmosClient. createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

[CRUD exempel filen för autoskalning i samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) visar hur du utför följande uppgifter. 

| Uppgift | API-referens |
| --- | --- |
| [Skapa en samling med angivet autoskalning Max-genomflöde](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L95-L107) | CosmosDatabase. createContainerIfNotExists |
| [Ändra konfigurerat högsta data flöde för autoskalning av en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L109-L117) | CosmosContainer.replaceThroughput |
| [Läs automatisk skalning av data flödes konfigurationen för en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L119-L130) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Samlings exempel för analys lagring

I [CRUD exempel filen för analys lagrings samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) visas hur du utför följande uppgifter. Om du vill veta mer om Azure Cosmos-samlingarna innan du kör följande exempel kan du läsa om Azure Cosmos DB Synapse och analys lager.

| Uppgift | API-referens |
| --- | --- |
| [Skapa en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L94-L109) | CosmosDatabase. createContainerIfNotExists |

## <a name="document-examples"></a>Exempel på dokument
Filen [CRUD exempel för dokument](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) visar hur du utför följande uppgifter. Information om Azure Cosmos-dokumenten innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](databases-containers-items.md) konceptuell artikel.

| Uppgift | API-referens |
| --- | --- |
| [Skapa ett dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L127-L141) | CosmosContainer.createItem |
| [Läsa ett dokument efter ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L143-L154) | CosmosContainer.readItem |
| [Läsa alla dokument i en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L156-171) | CosmosContainer.readAllItems |
| [Fråga för dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L173-L187) | CosmosContainer.queryItems |
| [Ersätta ett dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L189-L204) | CosmosContainer.replaceItem |
| [Upsertera ett dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L206-L219) | CosmosContainer.upsertItem |
| [Ta bort ett dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L302-L310) | CosmosContainer.deleteItem |
| [Ersätta ett dokument med villkorlig ETag-kontroll](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L221-L261) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Läs dokument endast om dokumentet har ändrats](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L263-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Indexeringsexempel
[Exempel filen Collection CRUD-exempel](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) visar hur du utför följande uppgifter. Om du vill veta mer om indexering i Azure Cosmos DB innan du kör följande exempel, se [indexerings principer](index-policy.md), [indexerings typer](index-types.md)och [indexerings Sök vägar](index-paths.md) konceptuella artiklar. 

| Uppgift | API-referens |
| --- | --- |
| Undanta ett dokument från indexet | ExcludedIndex<br>IndexingPolicy |
| Använd Lazy-indexering | IndexingPolicy.IndexingMode |
| [Inkludera angivna dokument Sök vägar i indexet](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L142-L147) | IndexingPolicy.IncludedPaths |
| [Undanta angivna dokument Sök vägar från indexet](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L149-L154) | IndexingPolicy.ExcludedPaths |
| [Skapa ett sammansatt index](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L170-L188) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Tvinga en intervallsökning av en hash-indexerad sökväg | FeedOptions.EnableScanInQuery |
| Använda intervall index i strängar | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Utföra en indextransformering | - |
| [Skapa ett geospatialt index](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L156-L168) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

Mer information om indexering finns i [Azure Cosmos DB indexeringsprinciper](index-policy.md).

## <a name="query-examples"></a>Exempelfrågor
I [fråge exempel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) filen visas hur du utför följande uppgifter med hjälp av SQL-frågespråket. Om du vill veta mer om SQL-frågan i Azure Cosmos DB innan du kör följande exempel kan du läsa [mer i SQL-fr åga for Azure Cosmos DB](how-to-sql-query.md). 

| Uppgift | API-referens |
| --- | --- |
| [Fråga för alla dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L194-L198) | CosmosContainer.queryItems |
| [Fråga för likhet med ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L279-183) | CosmosContainer.queryItems |
| [Fråga för olikhet med != och NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L285-L293) | CosmosContainer.queryItems |
| [Fråga med intervalloperatorer som >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L295-L300) | CosmosContainer.queryItems |
| [Fråga med intervalloperatorer mot strängar](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L302-L307) | CosmosContainer.queryItems |
| [Fråga med ORDER BY](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L309-L314) | CosmosContainer.queryItems |
| [Fråga med distinkt](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L316-L321) | CosmosContainer.queryItems |
| [Fråga med mängd funktioner](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L323-L331) | CosmosContainer.queryItems |
| [Arbeta med underdokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L333-L341) | CosmosContainer.queryItems |
| [Fråga med intradokumentkopplingar](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L343-L365) | CosmosContainer.queryItems |
| [Fråga med operatorer av sträng, matematik och matris](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L367-L378) | CosmosContainer.queryItems |
| [Fråga med parametriserad SQL med SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L380-L409) |CosmosContainer.queryItems |
| [Fråga med explicit sidindelning](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L200-L254) | CosmosContainer.queryItems |
| [Fråga partitionerade samlingar parallellt](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L256-L277) | CosmosContainer.queryItems |
| Fråga med ORDER BY för partitionerade samlingar | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Exempel på ändringsflöde 
Exempel filen för [change feed processor](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) visar hur du utför följande uppgifter. Information om hur du ändrar feed i Azure Cosmos DB innan du kör följande exempel finns i [läsa Azure Cosmos DB ändra feed](read-change-feed.md) och [ändra feed-processor](change-feed-processor.md).

| Uppgift | API-referens |
| --- | --- |
| [Grundläggande funktioner för att ändra feed](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Läs ändrings flöde från en angiven tid | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Läs ändrings flöde från början](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) | - |

## <a name="server-side-programming-examples"></a>Programmeringsexempel på serversidan

Exempel filen för [lagrade procedurer](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) visar hur du utför följande uppgifter. Om du vill lära dig mer om Server sidans programmering i Azure Cosmos DB innan du kör följande exempel, se [lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md).

| Uppgift | API-referens |
| --- | --- |
| [Skapa en lagrad procedur](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L124-L143) | CosmosScripts.createStoredProcedure |
| [Köra en lagrad procedur](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L160-L174) | CosmosStoredProcedure. Execute |
| [Ta bort en lagrad procedur](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L176-L186) | CosmosStoredProcedure. Delete |

## <a name="user-management-examples"></a>Exempel för användarhantering
Exempel filen för användar hantering visar hur du utför följande uppgifter:

| Uppgift | API-referens |
| --- | --- |
| Skapa en användare | - |
| Ange behörigheter för en samling eller dokument | - |
| Hämta en lista över an användares behörigheter |- |