---
title: SQL API Python-exempel för Azure Cosmos DB
description: Hitta Python-exempel på GitHub för vanliga uppgifter i Azure Cosmos DB, inklusive CRUD-åtgärder.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.openlocfilehash: 3b92ae206aa623e948a612f31b48213421bf9da3
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798499"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python-exempel

> [!div class="op_single_selector"]
> * [.NET v2 SDK-exempel](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-exempel](sql-api-dotnet-v3sdk-samples.md)
> * [Exempel för Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure-kodexempelgalleri](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Exempellösningarna som utför CRUD-åtgärder och andra vanliga åtgärder på Azure Cosmos DB-resurser finns på GitHub-lagringsplatsen [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python). Den här artikeln innehåller:

* Länkar till uppgifterna i var och en av Python-exempelprojektfilerna.
* Länkar till det relaterade API-referensinnehållet.

## <a name="prerequisites"></a>Krav

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): din Visual Studio-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Du behöver också [Python SDK](sql-api-sdk-python.md).

   > [!NOTE]
   > Varje exempel är självständigt. Det konfigurerar sig själv och rensar upp efter sig. Exemplen utfärdar flera anrop till `CosmosClient.CreateContainer` . Varje gång det är klart faktureras prenumerationen för en timmes användning. Mer information om Azure Cosmos DB-fakturering finns i [Prissättning för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="database-examples"></a>Databasexempel

Exemplet [database_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) python visar hur du utför följande uppgifter. Information om Azure Cosmos-databaser innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](databases-containers-items.md) konceptuell artikel.

| Uppgift | API-referens |
| --- | --- |
| [Skapa en databas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient. create_database|
| [Läsa en databas via ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient. get_database_client|
| [Fråga databaserna](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient. query_databases|
| [Lista databaser för ett konto](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient. list_databases|
| [Ta bort en databas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient. delete_database|

## <a name="container-examples"></a>Containerexempel

Exemplet [container_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) python visar hur du utför följande uppgifter. Mer information om Azure Cosmos-samlingarna innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](databases-containers-items.md) konceptuell artikel.

| Uppgift | API-referens |
| --- | --- |
| [Fråga för en behållare](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |databas. query_containers |
| [Skapa en container](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |databas. create_container |
| [Lista alla behållare i en databas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |databas. list_containers |
| [Hämta en behållare med dess ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |databas. get_container_client |
| [Hantera behållares etablerade data flöde](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container. read_offer, container. replace_throughput|
| [Ta bort en container](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |databas. delete_container |

## <a name="item-examples"></a>Objektexempel

Exemplet [item_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) python visar hur du utför följande uppgifter. Information om Azure Cosmos-dokumenten innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](databases-containers-items.md) konceptuell artikel.

| Uppgift | API-referens |
| --- | --- |
| [Skapa objekt i en behållare](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container. create_item |
| [Läsa ett objekt efter dess ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container. read_item |
| [Läs alla objekt i en behållare](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container. read_all_items |
| [Fråga ett objekt efter dess ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container. query_items |
| [Ersätta ett objekt](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container. replace_items |
| [Upsert ett objekt](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container. upsert_item |
| [Ta bort ett objekt](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container. delete_item |
| [Hämta ändrings flödet för objekt i en behållare](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container. query_items_change_feed |

## <a name="indexing-examples"></a>Indexeringsexempel

Exemplet [index_management. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) python visar hur du utför följande uppgifter. Om du vill veta mer om indexering i Azure Cosmos DB innan du kör följande exempel, se [indexerings principer](index-policy.md), [indexerings typer](index-types.md)och [indexerings Sök vägar](index-paths.md) konceptuella artiklar.

| Uppgift | API-referens |
| --- | --- |
| [Uteslut ett enskilt objekt från indexering](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Använd manuell indexering med objekt som har indexerats](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | dokument. IndexingDirective. include |
| [Uteslut sökvägar från indexering](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Definiera sökvägar som ska undantas i `IndexingPolicy` egenskapen |
| [Använda intervallindex i strängar](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Definiera indexerings princip med intervall index för data typen String. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Utföra en indextransformering](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |Database. replace_container (Använd den uppdaterade indexerings principen)|
| [Använd genomsökningar när det bara finns hash-index på sökvägen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | Ange `enable_scan_in_query=True` och `enable_cross_partition_query=True` vid fråga om objekten |