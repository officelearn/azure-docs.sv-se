---
title: 'Azure Cosmos DB: SQL async Java API, SDK &-resurser'
description: Lär dig allt om SQL async Java API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: c2cc8663896f9513d5b6ccfb024fac8b826b0d5d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660483"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB asynkron Java SDK för SQL API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-ändra feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

SQL API async Java SDK skiljer sig från SQL API Java SDK genom att tillhandahålla asynkrona åtgärder med stöd för det [Netta biblioteket](https://netty.io/). Den befintliga [SQL API Java SDK](sql-api-sdk-java.md) har inte stöd för asynkrona åtgärder. 

> [!IMPORTANT]  
> Detta är *inte* den senaste Java SDK: n för Azure Cosmos DB! Överväg att använda [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) för ditt projekt. Om du vill uppgradera följer du anvisningarna i guiden [migrera till Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) och [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) . 
>

| |  |
|---|---|
| **SDK-hämtning** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-dokumentation** |[Referens dokumentation för Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Bidra till SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Kom igång** | [Kom igång med asynkron Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Kodexempel** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Prestandatips**| [GitHub – viktigt](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Lägsta körnings tid som stöds**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>Vanliga frågor
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

