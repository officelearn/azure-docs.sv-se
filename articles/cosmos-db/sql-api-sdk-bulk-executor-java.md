---
title: 'Azure Cosmos DB: bulk utförar Java API, SDK &-resurser'
description: Lär dig allt om bulk-utförar Java API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB bulk utförar Java SDK.
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836452"
---
# <a name="java-bulk-executor-library-download-information"></a>Java-bibliotek för Mass utförar: Hämta information

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-ändra feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
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

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Korrigering för DocumentAnalyzer. Java för att korrekt extrahera kapslade nyckel värden från JSON.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Lägg till funktioner i BulkDelete-åtgärder för att försöka igen vid vissa fel och returnera även en lista över fel till användaren som kan provas igen.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Uppdatering för Cosmos SDK-version 2.4.7.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Korrigera för "mergeAll" om du vill fortsätta med "ID" och partitionsnyckel så att alla korrigerade dokument egenskaper som placeras efter "ID" och partitionsnyckel läggs till i listan över uppdaterade objekt.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Uppdatera start grad för samtidighet till 1 och tillagda fel söknings loggar för minibatch.


