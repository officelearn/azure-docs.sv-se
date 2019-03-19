---
title: 'Azure Cosmos DB: Java-exempel för SQL-API:et'
description: Hitta Java-exempel på GitHub för vanliga uppgifter med Azure Cosmos DB SQL API, inklusive CRUD-åtgärder.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2018
ms.author: sngun
ms.openlocfilehash: c0b776bfbae1f79b000c553b8318e273699a2fa3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542449"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Java-exempel för SQL-API:et

> [!div class="op_single_selector"]
> * [.NET-exempel](sql-api-dotnet-samples.md)
> * [Java-exempel](sql-api-java-samples.md)
> * [Async Java-exempel](sql-api-async-java-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure-kodexempelgalleri](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

De senaste exempelprogrammen som utför CRUD-åtgärder och andra vanliga åtgärder på Azure Cosmos DB-resurser på GitHub-lagringsplatsen [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java). Den här artikeln innehåller:

* Länkar till uppgifterna i var och en av Java-projektfilerna. 
* Länkar till det relaterade API-referensinnehållet.

**Förutsättningar**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Din Visual Studio-prenumeration ger dig krediter varje månad som du kan använda för Azure-betaltjänster.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Du behöver följande för att köra det här exempelprogrammet:

* Java Development Kit 7
* Microsoft Azure DocumentDB Java SDK

Du kan även använda Maven för att få de senaste Microsoft Azure DocumentDB Java SDK-binärfilerna och använda dem i projektet. Maven lägger automatiskt till alla nödvändiga beroenden. Du kan även ladda ned beroendena direkt som listas i filen pom.xml och lägga till dem till din byggsökväg.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**Köra exempelprogrammen**

Klona lagringsplatsexemplet:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Du kan köra exemplen antingen i Eclipse eller från kommandoraden med Maven.

Så här kör du från Eclipse:
* Läs in den överordnade projekthuvudfilen pom.xml i Eclipse. Den ska läsa in documentdb-examples automatiskt.
* När du ska köra exemplen behöver du en giltig Azure Cosmos DB-slutpunkt. Slutpunkterna läses från `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`.
* Du kan skicka dina autentiseringsuppgifter för slutpunkten som VM-argument i Eclipse JUnit Run Config eller så kan du placera dina autentiseringsuppgifter för slutpunkten i AccountCredentials.java.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Nu kan du köra exemplen som JUnit-tester i Eclipse.

Så här kör du från kommandoraden:
* Du kan även köra exempel med Maven:
* Kör Maven och skicka autentiseringsuppgifterna Azure Cosmos DB-slutpunkten:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Varje exempel är självständigt. Det konfigurerar sig själv och rensar upp efter sig. Exemplen gör flera anrop till [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection). Varje gång det sker faktureras din prenumeration för en timmes användning för prestandanivån för den skapade samlingen. 
   > 
   > 

## <a name="database-examples"></a>Databasexempel
Den [DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) filen visar hur du utför följande uppgifter. Läs mer om Azure Cosmos-databaserna innan du kör följande exempel, i [arbetar med databaser, behållare och objekt](databases-containers-items.md) konceptuella artikeln. 

| Aktivitet | API-referens |
| --- | --- |
| [Skapa och läsa en databas](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |
| [Skapa och ta bort en databas](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedatabase) |
| [Skapa och fråga en databas](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydatabases) |

## <a name="collection-examples"></a>Samlingsexempel
Den [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) filen visar hur du utför följande uppgifter. Läs om Azure Cosmos-samlingar innan du kör följande exempel i [arbetar med databaser, behållare och objekt](databases-containers-items.md) konceptuella artikeln.

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en samling med en enskild partition](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Skapa en samling med flera partitioner](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions) |
| [Ta bort en samling](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletecollection) |

## <a name="document-examples"></a>Exempel på dokument
Den [DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) filen visar hur du utför följande uppgifter. Läs mer om Azure Cosmos-dokument innan du kör följande exempel, i [arbetar med databaser, behållare och objekt](databases-containers-items.md) konceptuella artikeln.

| Aktivitet | API-referens |
| --- | --- |
| [Skapa, läsa och ta bort ett dokument](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) |
| [Skapa ett dokument med en programmerbar dokumentdefinition](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Dokument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |

## <a name="indexing-examples"></a>Indexeringsexempel
Den [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) filen visar hur du utför följande uppgifter. Läs mer om indexering i Azure Cosmos DB innan du kör följande exempel, i [indexeringsprinciper](index-policy.md), [indexering typer](index-types.md), och [indexering sökvägar](index-paths.md) konceptuella artiklar. 

| Aktivitet | API-referens |
| --- | --- |
| [Skapa ett index och ange indexeringsprincip](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy) |

Mer information om indexering finns i [Azure Cosmos DB indexeringsprinciper](index-policy.md).

## <a name="query-examples"></a>Exempelfrågor
Den [DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) filen visar hur du utför följande uppgifter. Mer information om SQL-referens för frågor i Azure Cosmos DB innan du kör följande exempel finns [SQL-fråga exempel](how-to-sql-query.md) konceptuella artikeln. 


| Aktivitet | API-referens |
| --- | --- |
| [Utföra en enkel dokumentfråga på flera olika partitioner](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setenablecrosspartitionquery) |
| [Sortera efter fråga](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse.getqueryiterator) |

Mer information om hur du skriver frågor finns i [SQL query within Azure Cosmos DB](how-to-sql-query.md) (SQL-fråga i Azure Cosmos DB).

## <a name="offer-examples"></a>Exempel på erbjudanden
Filen [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en samling och ange dataflöde](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions.setofferthroughput) |
| [Läsa en samling för att hitta det associerade erbjudandet](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.queryoffers) |

## <a name="partition-key-examples"></a>Partitionsnyckelexempel
Den [SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) filen visar hur du utför följande uppgifter. Mer information om partitionering och partitionsnycklar i Azure Cosmos DB innan du kör följande exempel, se [partitionering](partitioning-overview.md) konceptuella artikeln. 


| Aktivitet | API-referens |
| --- | --- |
| [Skapa en samling med en enskild partition](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Ändra dataflödeserbjudandet för en samling med en enskild partition](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Exempel på lagrad procedur
Den [StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) filen visar hur du utför följande uppgifter. Läs mer om programmering av serversidan i Azure Cosmos DB innan du kör följande exempel, i [lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md) konceptuella artikeln. 


| Aktivitet | API-referens |
| --- | --- |
| [Skapa en lagrad procedur](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createstoredprocedure) |
| [Köra en lagrad procedur med argument](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
| [Köra en lagrad procedur med ett objektargument](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
