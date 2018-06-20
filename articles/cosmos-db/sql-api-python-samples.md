---
title: SQL API Python-exempel för Azure Cosmos DB | Microsoft Docs
description: Hitta Python-exempel på github för vanliga uppgifter i Azure Cosmos DB, inklusive CRUD-åtgärder.
keywords: python-exempel
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 16c8f89218f735ed2c469ff03431dd35a9282e56
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831068"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python-exempel

> [!div class="op_single_selector"]
> * [.NET-exempel](sql-api-dotnet-samples.md)
> * [Java-exempel](sql-api-java-samples.md)
> * [Async Java-exempel](sql-api-async-java-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure-kodexempelgalleri](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Exempellösningarna som utför CRUD-åtgärder och andra vanliga åtgärder på Azure Cosmos DB-resurser på GitHub-lagringsplatsen [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python). Den här artikeln innehåller:

* Länkar till uppgifterna i var och en av Python-exempelprojektfilerna. 
* Länkar till det relaterade API-referensinnehållet.

**Förutsättningar**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): din Visual Studio-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Du behöver också [Python SDK](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Varje exempel är självständigt. Det konfigurerar sig själv och rensar upp efter sig. Exemplen gör flera anrop till [document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Varje gång det är klart faktureras prenumerationen för en timmes användning. Mer information om Azure Cosmos DB-fakturering finns i [Prissättning för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Databasexempel
Filen [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) i projektet [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en databas](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDatabase) |
| [Läsa en databas via ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDatabase) |
| [Lista databaser för ett konto](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Ta bort en databas](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |

## <a name="collection-examples"></a>Samlingsexempel
Filen [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) i projektet [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en samling](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Läsa en lista med alla samlingar i en databas](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L209) |[document_client.ReadCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadCollections) |
| [Hämta en samling via ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadCollection) |
| [Ändra dataflödet för en samling](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L168-L172) | [document_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)|
| [Ta bort en samling](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#DeleteCollection) |

## <a name="document-examples"></a>Exempel på dokument
Filen [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) i projektet [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa ett dokument](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocument](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDocument) |
| [Skapa en samling med dokument](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDocuments) |
| [Läsa ett dokument via ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[document_client.ReadDocument](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDocument) |
| [Läsa alla dokument i en samling](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[document_client.ReadDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDocuments) |

## <a name="indexing-examples"></a>Indexeringsexempel
Filen [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) i projektet [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Använda manuell (istället för automatisk) indexering](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) |[IndexingPolicy.Automatic](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Undanta angivna dokumentsökvägar från indexet](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) |[IndexingPolicy.ExcludedPaths](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Undanta ett dokument från indexet](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[documents.IndexingDirective.Exclude](http://azure.github.io/azure-documentdb-python/_modules/pydocumentdb/documents.html#IndexingDirective) |
| [Ange indexeringsläge](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[documents.IndexingMode](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.documents.html#IndexingMode) |
| [Använda intervallindex i strängar](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) |[IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Utföra en indextransformering](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[document_client.ReplaceCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReplaceCollection) |

## <a name="query-examples"></a>Exempelfrågor
Exempelprojekten visar även hur du utför följande fråga:

| Aktivitet | API-referens |
| --- | --- |
| [Fråga ett konto för en databas](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Fråga för dokument](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[document_client.QueryDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#QueryDocuments) |
| [Tvinga en intervallsökning av en hash-indexerad sökväg](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[http_constants.HttpHeaders.EnableScanInQuery](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.http_constants.html#pydocumentdb.http_constants.HttpHeaders.EnableScanInQuery) |
