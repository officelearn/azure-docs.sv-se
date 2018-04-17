---
title: 'Azure Cosmos DB: Java-exempel för SQL-API | Microsoft Docs'
description: Hitta Java-exempel på GitHub för vanliga uppgifter med hjälp av Azure Cosmos DB SQL API, inklusive CRUD-åtgärder.
keywords: NoSQL-exempel
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: java
ms.assetid: d824d517-903e-4d82-ab0a-09fc3b984c84
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/08/2018
ms.author: sngun
ms.openlocfilehash: 3372de0218f20b29a03e0cc7caabea44cea10ded
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Java-exempel för SQL-API

> [!div class="op_single_selector"]
> * [.NET-exempel](sql-api-dotnet-samples.md)
> * [Java-exempel](sql-api-java-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure kod exempel-galleriet](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Senaste exempelprogrammen som utför CRUD-åtgärder och andra vanliga åtgärder på Azure DB som Cosmos-resurser som ingår i den [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) GitHub-lagringsplatsen. Den här artikeln innehåller:

* Länkar till aktiviteter i varje exempel Java projektfiler. 
* Länkar till relaterade API: N referera till innehåll.

**Förutsättningar**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): din Visual Studio-prenumeration ger dig krediter varje månad som du kan använda för Azure-betaltjänster.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Du behöver följande för att köra det här exempelprogrammet:

* Java Development Kit 7
* Microsoft Azure DocumentDB Java SDK

Du kan eventuellt använda Maven för att hämta de senaste Microsoft Azure DocumentDB Java SDK binärfilerna för användning i ditt projekt. Maven lägger automatiskt till alla nödvändiga beroenden. Annars kan du direkt hämta beroenden som anges i filen pom.xml och lägga till dem i din byggsökväg.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**Kör exempelprogrammen**

Klona lagringsplatsen exempel:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Du kan köra exemplen antingen i Eclipse eller från kommandoraden med Maven.

Köra från Eclipse:
* Läsa in huvudsakliga överordnade pom.xml projektfilen i Eclipse; Det bör laddar documentdb-exempel automatiskt.
* Om du vill köra exemplen, behöver du en giltig Azure Cosmos DB-slutpunkt. Slutpunkterna läses från `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`.
* Du kan ange dina autentiseringsuppgifter för slutpunkten som VM-argument i Eclipse JUnit Kör Config eller du kan placera dina autentiseringsuppgifter för slutpunkten i AccountCredentials.java.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Du kan nu köra exemplen som JUnit tester i Eclipse.

Köra från kommandoraden:
* Ett annat sätt för att köra exempel är att använda maven:
* Kör Maven och skicka autentiseringsuppgifterna Azure Cosmos DB-slutpunkt:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Varje exempel är självständigt; den konfigurerar sig själv och rensar efter sig själv. I prover utfärda flera anrop till [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection). Varje gång det är klart faktureras prenumerationen 1 timmen för användning för prestandanivån på den samling som har skapats. 
   > 
   > 

## <a name="database-examples"></a>Databas-exempel
Den [DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) filen visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa och läsa en-databas](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._resource.setid) |
| [Skapa och ta bort en databas](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedatabase) |
| [Skapa och frågar en-databas](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydatabases) |

## <a name="collection-examples"></a>Exempel för samlingen
Den [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) filen visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en enda partition samling](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Skapa en anpassad samling med flera partition](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [Dokumentsamling](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_collection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._partition_key_definition)<br>[RequestOptions](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._request_options) |
| [Ta bort en samling](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletecollection) |

## <a name="document-examples"></a>Dokument-exempel
Den [DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) filen visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa, läsa och ta bort ett dokument](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.deletedocument) |
| [Skapa ett dokument med en dokumentdefinition av programmerbara](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Dokumentet](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document)<br>[Resource.setId](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._resource.setid) |

## <a name="indexing-examples"></a>Indexering exempel
Den [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) filen visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa ett index och en uppsättning indexering princip](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._index)<br>[IndexingPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._indexing_policy) |

Mer information om indexering finns [Azure Cosmos DB indexering principer](indexing-policies.md).

## <a name="query-examples"></a>frågan exempel
Den [DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) filen visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Utföra en enkel cross-partition dokument-fråga](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_options.setenablecrosspartitionquery) |
| [Order by-fråga](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_response.getqueryiterator) |

Mer information om hur du skriver frågor finns [SQL-frågan i Azure Cosmos DB](sql-api-sql-query.md).

## <a name="offer-examples"></a>Erbjudande-exempel
Den [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) filen visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en samling och dataflöde](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection)<br>[RequestOptions.setOfferThroughput ](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._request_options.setofferthroughput) |
| [Läs en samling för att hitta det associera erbjudanden](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.queryoffers) |

## <a name="partition-key-examples"></a>Partitionen viktiga exempel
Den [SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) filen visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en enda partition samling](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Ändra genomströmning erbjudandet för en enskild partition samling](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer) |

## <a name="stored-procedure-examples"></a>Exempel för lagrad procedur
Den [StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) filen visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en lagrad procedur](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._stored_procedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createstoredprocedure) |
| [Köra en lagrad procedur med argument](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
| [Kör en lagrad procedur med ett argument för objekt](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
