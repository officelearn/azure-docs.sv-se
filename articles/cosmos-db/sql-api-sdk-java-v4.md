---
title: Viktig information och resurser för Azure Cosmos DB Java SDK v4 för SQL API
description: Lär dig allt om Azure Cosmos DB Java SDK v4 för SQL API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: 35d83d11d631d94cad4781c69d985a73c70dde99
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677975"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
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
|**API-dokumentation** | [Referens dokumentation för Java API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html) |
|**Bidra till SDK** | [Azure SDK för Java Central lagrings platsen på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Kom igång** | [Snabb start: bygga en Java-app för att hantera Azure Cosmos DB SQL API-data](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [GitHub lagrings platsen med snabb starts kod](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Grundläggande kod exempel** | [Azure Cosmos DB: Java-exempel för SQL-API](sql-api-java-sdk-samples.md) · [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konsol program med ändrings flöde**| [Ändra feed – Java SDK v4-exempel](create-sql-api-java-changefeed.md) · [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Exempel på webbapp**| [Bygg en webbapp med Java SDK v4](sql-api-java-application.md) · [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Prestandatips**| [Prestandatips för Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Felsökning** | [Felsöka Java-SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrera till v4 från en äldre SDK** | [Migrera till Java V4-SDK](migrate-java-v4-sdk.md) |
| **Lägsta körnings tid som stöds**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB workshops och labb** |[Start sida för Cosmos DB workshops](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Versions historik

### <a name="401-beta4-unreleased"></a>4.0.1 – beta. 4 (frisläppt)
#### <a name="new-features"></a>Nya funktioner
* Fler exempel & berikade dokument lades till `CosmosClientBuilder` . 
* Uppdaterade `CosmosDatabase`  &  `CosmosContainer` API: er med throughputProperties för autoskalning/autopilot-stöd. 
* Har bytt namn `CosmosClientException` till `CosmosException` . 
* Ersatts `AccessCondition`  &  `AccessConditionType` av `ifMatchETag()`  &  `ifNoneMatchETag()` API: er. 
* Sammanfogade alla `Cosmos*AsyncResponse`  &  `CosmosResponse` typer till en enda `CosmosResponse` typ.
* Har bytt namn `CosmosResponseDiagnostics` till `CosmosDiagnostics` .  
* Omsluten `FeedResponseDiagnostics` `CosmosDiagnostics` . 
* `jackson`Beroende av Azure-cosmos & förlitar sig på Azure-Core. 
* Ersatt `CosmosKeyCredential` av `AzureKeyCredential` typ. 
* `ProxyOptions`API: er har lagts till i `GatewayConnectionConfig` . 
* SDK har uppdaterats för att använda `Instant` typ i stället för `OffsetDateTime` . 
* Ny Enum-typ har lagts till `OperationKind` . 
* Har bytt namn `FeedOptions` till `QueryRequestOptions` . 
* `getETag()`  &  `getTimestamp()` API: er har lagts till i `Cosmos*Properties` typerna. 
* Information har lagts till `userAgent` i `CosmosException`  &  `CosmosDiagnostics` . 
* Ett nytt rad avstånd har uppdaterats `Diagnostics` till rad i systemet. 

### <a name="401-beta3-2020-05-15"></a>4.0.1 – beta. 3 (2020-05-15)
#### <a name="new-features"></a>Nya funktioner
* Har lagt till stöd för autoskalning/autopilot-genomflöde i SDK.  
* Ersatt `ConnectionPolicy` med nya anslutnings konfigurationer. Exponerade `DirectConnectionConfig`  &  `GatewayConnectionConfig` API: er via `CosmosClientBuilder` för direktanslutna & Gateway mode-anslutnings konfigurationer.
* Flyttad `JsonSerializable`  &  `Resource` till implementerings paketet. 
* API har lagts `contentResponseOnWriteEnabled` till för CosmosClientBuilder, vilket inaktiverar fullständigt svars innehåll vid Skriv åtgärder.
* Exponerade `getETag()` API: er för svars typer.
* Flyttad `CosmosAuthorizationTokenResolver` till implementering. 
* API har bytt namn `preferredLocations`  &  `multipleWriteLocations` till `preferredRegions`  &  `multipleWriteRegions` . 
* Uppdaterat `reactor-core` till 3.3.5. release, `reactor-netty` till 0.9.7. release & `netty` till 4.1.49. Finale-versioner. 
* Stöd har lagts till `analyticalStoreTimeToLive` i SDK.     
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Problem med fast socket läcker med direkt TCP-klient.
* Åtgärdat `orderByQuery` med fel i fortsättnings-token.

### <a name="401-beta2-2020-04-21"></a>4.0.1 – beta. 2 (2020-04-21)
#### <a name="new-features"></a>Nya funktioner
* `CosmosClientException`utökar `AzureException` . 
* API: er har tagits bort `maxItemCount`  &  `requestContinuationToken` från `FeedOptions` i stället med `byPage()` API: er `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Infört `CosmosPermissionProperties` på offentlig yta för `Permission` API: er.
* Den borttagna `SqlParameterList` typen & ersatts av`List`
* Fast flera minnes läckor i direkt TCP-klient. 
* Stöd har lagts till för `DISTINCT` frågor. 
* Externa beroenden har tagits bort `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Flyttad `CosmosPagedFlux`  &  `CosmosPagedIterable` till `utils` paket. 
* Uppdaterad nettning till 4.1.45. slutlig & projekt reaktor till 3.3.3 version.
* Uppdaterade offentliga rest-kontrakt till `Final` klasser. 
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* `ChangeFeedProcessor`fel korrigering för hantering av partition delas & när partitionen inte hittas.
* `ChangeFeedProcessor`fel korrigering vid synkronisering av låne uppdateringar mellan olika trådar.

### <a name="401-beta1-2020-03-10"></a>4.0.1 – beta. 1 (2020-03-10)
#### <a name="new-features"></a>Nya funktioner 
* Paketet har uppdaterats till`com.azure.cosmos`
* `models`Paket för modell/rest-avtal har lagts till
* Paket har lagts till `utils` för `CosmosPagedFlux`  &  `CosmosPagedIterable` typer. 
* Uppdaterade offentliga API: er som ska användas i `Duration` SDK.
* Alla rest-kontrakt har lagts till i `models` paketet.
* `RetryOptions`har bytt namn till `ThrottlingRetryOptions` .
* `CosmosPagedFlux`  &  `CosmosPagedIterable` Sid brytnings typer har lagts till för API: er för frågor. 
* Stöd har lagts till för delning av TransportClient över flera instanser av CosmosClients med hjälp av ett nytt API i`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Fast konkurrens tillstånd `ArrayIndexOutOfBound` som orsakar undantag i StoreReader

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.