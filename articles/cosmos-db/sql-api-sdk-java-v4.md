---
title: Viktig information och resurser för Azure Cosmos DB Java SDK v4 för SQL API
description: Lär dig allt om Azure Cosmos DB Java SDK v4 för SQL API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: cdbb6d0f26b6a999d289e7e0ddbd8cb45dac8e47
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665203"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: viktig information och resurser
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

Azure Cosmos DB Java SDK v4 for Core (SQL) kombinerar ett asynkront API och ett Sync-API till en maven-artefakt. V4 SDK ger förbättrade prestanda, nya API-funktioner och asynkront stöd baserat på projekt reaktor och det [Netta biblioteket](https://netty.io/). Användare kan förväntas förbättra prestanda med Azure Cosmos DB Java SDK v4 jämfört med [Azure Cosmos DB asynkron Java SDK v2](sql-api-sdk-async-java.md) och [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Den här viktig information gäller endast Azure Cosmos DB Java SDK v4. Om du använder en äldre version än v4 kan du läsa om hur du uppgraderar till v4 i [Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide.
>
> Här är tre steg för att komma igång snabbt!
> 1. Installera den [lägsta Java-körningen som stöds, JDK 8,](/java/azure/jdk/?view=azure-java-stable) så att du kan använda SDK: n.
> 2. Arbeta via [snabb starts guiden för Azure Cosmos DB Java SDK v4 som ger](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) dig till gång till maven-artefakten och går igenom grundläggande Azure Cosmos DB förfrågningar.
> 3. Läs [prestanda tipsen](performance-tips-java-sdk-v4-sql.md) för Azure Cosmos DB Java SDK v4 och [fel söknings](troubleshoot-java-sdk-v4-sql.md) guider för att optimera SDK för ditt program.
>
> [Azure Cosmos DB workshops och Labs](https://aka.ms/cosmosworkshop) är en annan fantastisk resurs för att lära dig hur du använder Azure Cosmos DB Java SDK v4!
>

| |  |
|---|---|
| **SDK-hämtning** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-dokumentation** |Referens dokumentation för Java API|
|**Bidra till SDK** | [Azure SDK för Java Central lagrings platsen på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Kom igång** | [Snabb start: bygga en Java-app för att hantera Azure Cosmos DB SQL API-data](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [GitHub lagrings platsen med snabb starts kod](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Grundläggande kod exempel** | Azure Cosmos DB: Java-exempel för SQL-API · [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konsol program med ändrings flöde**| [Ändra feed – Java SDK v4-exempel](create-sql-api-java-changefeed.md) · [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Exempel på webbapp**| [Bygg en webbapp med Java SDK v4](sql-api-java-application.md) · [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Prestandatips**| [Prestandatips för Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Felsökning** | [Felsöka Java-SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrera till v4 från en äldre SDK** | [Migrera till Java V4-SDK](migrate-java-v4-sdk.md) |
| **Lägsta körnings tid som stöds**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB workshops och labb** |[Start sida för Cosmos DB workshops](https://aka.ms/cosmosworkshop)

