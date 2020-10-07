---
title: 'Azure Cosmos DB: SQL async Java API, SDK &-resurser'
description: Lär dig allt om SQL async Java API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 509c6e7a7940cb740a3dcbf81deed9f0d26c2bc8
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802862"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB asynkron Java SDK för SQL API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Våren data v2](sql-api-sdk-java-spring-v2.md)
> * [Våren data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-anslutningsprogram](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * FLESTA (/rest/api
> * [REST-resursprovider](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

SQL API async Java SDK skiljer sig från SQL API Java SDK genom att tillhandahålla asynkrona åtgärder med stöd för det [Netta biblioteket](https://netty.io/). Den befintliga [SQL API Java SDK](sql-api-sdk-java.md) har inte stöd för asynkrona åtgärder. 

> [!IMPORTANT]  
> Detta är *inte* den senaste Java SDK: n för Azure Cosmos DB! Överväg att använda [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) för ditt projekt. Om du vill uppgradera följer du anvisningarna i guiden [migrera till Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) och [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) . 
>

| |  |
|---|---|
| **SDK-hämtning** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-dokumentation** |[Referens dokumentation för Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable&preserve-view=true) | 
|**Bidra till SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Komma igång** | [Kom igång med asynkron Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Kodexempel** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Prestandatips**| [GitHub – viktigt](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Lägsta körnings tid som stöds**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

