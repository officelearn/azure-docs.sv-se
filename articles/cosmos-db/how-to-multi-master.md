---
title: Så här konfigurerar du skrivningar i flera regioner i Azure Cosmos DB
description: 'Lär dig hur du konfigurerar flera regions skrivningar för dina program genom att använda olika SDK: er i Azure Cosmos DB.'
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/10/2020
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 35c0b5529cd9ada612caf4884683fbeaacb25b33
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100141"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurera flera region skrivningar i dina program som använder Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

När ett konto har skapats med flera Skriv regioner aktiverade måste du göra två ändringar i programmet till ConnectionPolicy för DocumentClient för att aktivera funktionerna för flera regioner och flera värdar i Azure Cosmos DB. I ConnectionPolicy anger du UseMultipleWriteLocations till sant och skickar namnet på den region där programmet distribueras till SetCurrentLocation. Detta fyller i egenskapen PreferredLocations baserat på Geo-närhet från den plats som har skickats. Om en ny region senare läggs till i kontot, behöver programmet inte uppdateras eller omdistribueras. det kommer automatiskt att identifiera den närmare regionen och automatiskt starta den om en regional händelse inträffar.

> [!Note]
> Cosmos-konton som ursprungligen konfigureras med en enda Skriv region kan konfigureras till flera Skriv regioner utan tids gräns. Läs mer i [Konfigurera flera-Write-regioner](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Om du vill aktivera skrivningar i flera regioner i ditt program anger `UseMultipleWriteLocations` du till `true` . Ange också `SetCurrentLocation` den region där programmet distribueras och där Azure Cosmos DB replikeras:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

Om du vill aktivera skrivningar i flera regioner i ditt program, anger du `ApplicationRegion` den region där programmet distribueras och var Cosmos DB replikeras:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Alternativt kan du använda `CosmosClientBuilder` och `WithApplicationRegion` för att uppnå samma resultat:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Java v4 SDK

Om du vill aktivera skrivningar i flera regioner i ditt program, anropa `.multipleWriteRegionsEnabled(true)` och `.preferredRegions(preferredRegions)` i klient byggare, där `preferredRegions` är ett `List` element som innehåller ett-element, som är den region där programmet distribueras och där Cosmos DB replikeras:

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) asynkront API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Synkronisera](#tab/api-sync)

   [Java SDK v4](sql-api-sdk-java-v4.md) (maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> Asynkron Java v2 SDK

Java v2 SDK använde maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Om du vill aktivera flera regioner i ditt program ställer du in `policy.setUsingMultipleWriteLocations(true)` och anger `policy.setPreferredLocations` den region där programmet distribueras och där Cosmos DB replikeras:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, Java Script och TypeScript SDK: er

Om du vill aktivera skrivningar i flera regioner i ditt program anger `connectionPolicy.UseMultipleWriteLocations` du till `true` . Ange också `connectionPolicy.PreferredLocations` den region där programmet distribueras och där Cosmos DB replikeras:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Om du vill aktivera skrivningar i flera regioner i ditt program anger `connection_policy.UseMultipleWriteLocations` du till `true` . Ange också `connection_policy.PreferredLocations` den region där programmet distribueras och där Cosmos DB replikeras.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar:

* [Använd sessionstoken för att hantera konsekvens i Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Konflikttyper och principer för konfliktlösning i Azure Cosmos DB](conflict-resolution-policies.md)
* [Hög tillgänglighet i Azure Cosmos DB](high-availability.md)
* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Välj rätt konsekvens nivå i Azure Cosmos DB](./consistency-levels.md)
* [Konsekvens, tillgänglighet och prestanda kompromisser i Azure Cosmos DB](./consistency-levels.md)
* [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](./consistency-levels.md)
* [Skala etablerat dataflöde globalt](./request-units.md)
* [Global distribution: under huven](global-dist-under-the-hood.md)