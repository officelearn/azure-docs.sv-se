---
title: 'Azure Cosmos DB: bulk utförar Java API, SDK &-resurser'
description: Lär dig allt om bulk-utförar Java API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB bulk utförar Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: c826b9f813c30a50d8b88b2cca1b188c328465b0
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171658"
---
# <a name="java-bulk-executor-library-download-information"></a>Java-bibliotek för Mass utförar: Hämta information

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

| |  |
|---|---|
|**Beskrivning**|Med bulk utförar-biblioteket kan klient program utföra Mass åtgärder i Azure Cosmos DB konton. i bulk utförar-biblioteket finns BulkImport-och BulkUpdate-namnrymder. BulkImport-modulen kan samla in dokument på ett optimerat sätt, så att det data flöde som har allokerats för en samling förbrukas i maximal omfattning. BulkUpdate-modulen kan massredigera befintliga data i Azure Cosmos-behållare som korrigeringsfiler.|
|**SDK-hämtning**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Mass utförar-bibliotek i GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-dokumentation**| [Referens dokumentation för Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Kom igång**|[Kom igång med bulk utförar Library Java SDK](bulk-executor-java.md)|
|**Lägsta körnings tid som stöds**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Viktig information

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Korrigering för DocumentAnalyzer. Java för att korrekt extrahera kapslade nyckel värden från JSON.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Lägg till funktioner i BulkDelete-åtgärder för att försöka igen vid vissa fel och returnera även en lista över fel till användaren som kan provas igen.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Uppdatering för Cosmos SDK-version 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Korrigera för "mergeAll" om du vill fortsätta med "ID" och partitionsnyckel så att alla korrigerade dokument egenskaper som placeras efter "ID" och partitionsnyckel läggs till i listan över uppdaterade objekt.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Uppdatera start grad för samtidighet till 1 och tillagda fel söknings loggar för minibatch.


