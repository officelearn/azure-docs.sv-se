---
title: Så här konfigurerar du flera original i Azure Cosmos DB
description: Lär dig hur du konfigurerar multimaster i dina program i Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: 86f5d64391dd5312d8c51a5b639b790e62b6710d
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560261"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurera multimaster i dina program som använder Azure Cosmos DB

Om du vill använda funktionen flera huvudservrar i ditt program, måste du aktivera skrivningar flera regioner och konfigurera funktionen med flera värdar i Azure Cosmos DB. Om du vill konfigurera flera värdar, ange den region där programmet har distribuerats.

## <a id="netv2"></a>.NET SDK v2

Om du vill aktivera multimaster i ditt program ange `UseMultipleWriteLocations` till `true`. Ange dessutom `SetCurrentLocation` till regionen där programmet distribueras och där Azure Cosmos DB replikeras:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3 (förhandsversion)

Om du vill aktivera multimaster i ditt program ange `UseCurrentRegion` till regionen där programmet distribueras och där Cosmos DB replikeras:

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Om du vill aktivera multimaster i ditt program ange `policy.setUsingMultipleWriteLocations(true)` och ange `policy.setPreferredLocations` till regionen där programmet distribueras och där Cosmos DB replikeras:

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

## <a id="javascript"></a>Node.js, JavaScript och TypeScript-SDK: er

Om du vill aktivera multimaster i ditt program ange `connectionPolicy.UseMultipleWriteLocations` till `true`. Ange dessutom `connectionPolicy.PreferredLocations` till regionen där programmet distribueras och där Cosmos DB replikeras:

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

## <a id="python"></a>Python SDK

Om du vill aktivera multimaster i ditt program ange `connection_policy.UseMultipleWriteLocations` till `true`. Ange dessutom `connection_policy.PreferredLocations` till regionen där programmet distribueras och var Cosmos DB ska replikeras.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar:

* [Använda session tokens för att hantera konsekvens i Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Konflikttyper och principer för konfliktlösning i Azure Cosmos DB](conflict-resolution-policies.md)
* [Hög tillgänglighet i Azure Cosmos DB](high-availability.md)
* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Välj rätt konsekvensnivå i Azure Cosmos DB](consistency-levels-choosing.md)
* [Avvägningar gällande konsekvens, tillgänglighet och prestanda i Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Skala globalt etablerat dataflöde](scaling-throughput.md)
* [Global distribution: Bakom kulisserna](global-dist-under-the-hood.md)
