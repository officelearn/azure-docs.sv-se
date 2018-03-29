---
title: SQL API Python-exempel för Azure Cosmos DB | Microsoft Docs
description: Hitta Python exemplen på github för vanliga uppgifter i Azure Cosmos-databasen, inklusive CRUD-åtgärder.
keywords: Python-exempel
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: python
ms.assetid: 7f4f8db3-e9db-4645-92ef-7819d486a349
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 03/14/2018
ms.author: mimig
ms.openlocfilehash: ce9a7d071575b30e0150b23291292bddd0c65f1f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python-exempel

> [!div class="op_single_selector"]
> * [.NET-exempel](sql-api-dotnet-samples.md)
> * [Java-exempel](sql-api-java-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure kod exempel-galleriet](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Exempel lösningar som utför CRUD-åtgärder och andra vanliga åtgärder på Azure DB som Cosmos-resurser som ingår i den [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) GitHub-lagringsplatsen. Den här artikeln innehåller:

* Länkar till aktiviteter i varje Python-exempel projektfiler. 
* Länkar till relaterade API: N referera till innehåll.

**Förutsättningar**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): din Visual Studio-prenumeration ger dig krediter varje månad som du kan använda för Azure-betaltjänster.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Du måste också den [Python SDK](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Varje exempel är självständigt; den konfigurerar sig själv och rensar efter sig själv. I prover utfärda flera anrop till [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Varje gång det är klart faktureras prenumerationen under en timme för användning. Läs mer om Azure Cosmos DB fakturering [priser för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Databas-exempel
Den [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) -filen för den [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) projektet visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en databas](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDatabase) |
| [Läs en databas-ID: t](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDatabase) |
| [Lista databaser för ett konto](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client. ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Ta bort en databas](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |

## <a name="collection-examples"></a>Exempel för samlingen
Den [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) -filen för den [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) projektet visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en samling](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Läs en lista med alla samlingar i en-databas](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L209) |[document_client.ReadCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadCollections) |
| [Hämta en samling med ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadCollection) |
| [Ändra genomflödet av en samling](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L168-L172) | [document_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)|
| [Ta bort en samling](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#DeleteCollection) |

## <a name="document-examples"></a>Dokument-exempel
Den [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) -filen för den [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) projektet visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Skapa ett dokument](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocument](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDocument) |
| [Skapa en samling av dokument](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDocuments) |
| [Läsa ett dokument-ID: t](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[document_client.ReadDocument](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDocument) |
| [Läsa alla dokument i en samling](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[document_client.ReadDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDocuments) |

## <a name="indexing-examples"></a>Indexering exempel
Den [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) -filen för den [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) projektet visar hur du utför följande uppgifter:

| Aktivitet | API-referens |
| --- | --- |
| [Använda manuell (i stället för automatisk) indexering](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) |[IndexingPolicy.Automatic](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Uteslut sökvägar för angivna dokumentet från indexet](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) |[IndexingPolicy.ExcludedPaths](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Undanta ett dokument från indexet](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[documents.IndexingDirective.Exclude](http://azure.github.io/azure-documentdb-python/_modules/pydocumentdb/documents.html#IndexingDirective) |
| [Inställning för indexering](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[documents.IndexingMode](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.documents.html#IndexingMode) |
| [Använd intervallet index i strängar](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) |[IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Utföra en omvandling av index](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[document_client.ReplaceCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReplaceCollection) |

## <a name="query-examples"></a>frågan exempel
Exempelprojekten visar även hur du utför följande fråga:

| Aktivitet | API-referens |
| --- | --- |
| [Fråga ett konto för en databas](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Frågan för dokument](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[document_client.QueryDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#QueryDocuments) |
| [Tvinga en intervallet utföras på en indexerad hash-sökväg](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[http_constants.HttpHeaders.EnableScanInQuery](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.http_constants.html#pydocumentdb.http_constants.HttpHeaders.EnableScanInQuery) |
