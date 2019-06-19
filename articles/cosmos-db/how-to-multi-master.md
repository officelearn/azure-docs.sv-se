---
title: Så här konfigurerar du flera original i Azure Cosmos DB
description: Lär dig hur du konfigurerar multimaster i dina program i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: e33aa10673f4c4b061f8961959f20b7b3416ec84
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204790"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurera multimaster i dina program som använder Azure Cosmos DB

Om du vill använda funktionen flera huvudservrar i ditt program, måste du aktivera skrivningar flera regioner och konfigurera funktionen med flera värdar i Azure Cosmos DB. Skicka namnet på regionen där programmet har distribuerats till egenskapen SetCurrentLocation om du vill konfigurera flera värdar. Detta automatiskt fyller egenskapen PreferredLocations baserat på geo-närhet från platsen som har skickats in. Om en ny region senare läggs till kontot, programmet behöver inte uppdateras eller omdistribueras och identifierar automatiskt regionen närmare kommer automatisk-startsidan in det görs en regional händelse.

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
