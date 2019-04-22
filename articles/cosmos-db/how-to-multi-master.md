---
title: Så här konfigurerar du flera original i Azure Cosmos DB
description: Lär dig hur du konfigurerar flera original i dina program i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: b862c59002369662d37b6d6a9de28370b0000497
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682278"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Så konfigurerar du flera original i dina program som använder Azure Cosmos DB

Om du vill använda flera huvudservrar funktionen i ditt program måste aktivera skrivningar flera regioner och konfigurera funktionen med flera värdar i Azure Cosmos DB. Flera värdar konfigureras genom att ange den region där programmet har distribuerats.

## <a id="netv2"></a>.NET SDK v2

Aktivera multimaster i din program `UseMultipleWriteLocations` true och konfigurera `SetCurrentLocation` till den region där programmet distribueras och Azure Cosmos DB replikeras.

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

För att aktivera flera original i dina program konfigurerar du `UseCurrentRegion` till den region där programmet distribueras, så replikeras Cosmos DB.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Aktivera multimaster i din program `policy.setUsingMultipleWriteLocations(true)` och konfigurera `policy.setPreferredLocations` till den region där programmet distribueras och Cosmos DB replikeras.

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

## <a id="javascript"></a>Node.js, JavaScript, TypeScript SDK

För att aktivera flera original i dina program konfigurerar du `connectionPolicy.UseMultipleWriteLocations` till sant och konfigurerar `connectionPolicy.PreferredLocations` till den region där programmet distribueras, så replikeras Cosmos DB.

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

För att aktivera flera original i dina program konfigurerar du `connection_policy.UseMultipleWriteLocations` till sant och konfigurerar `connection_policy.PreferredLocations` till den region där programmet distribueras, så replikeras Cosmos DB.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Nästa steg

Därefter kan du läsa följande artiklar:

* [Använda sessionstoken för att hantera konsekvens i Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Konflikttyper och principer för konfliktlösning i Azure Cosmos DB](conflict-resolution-policies.md)
* [Hög tillgänglighet i Azure Cosmos DB](high-availability.md)
* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Välja rätt konsekvensnivå i Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisser avseende konsekvens, tillgänglighet och prestanda i Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Skala globalt etablerat dataflöde](scaling-throughput.md)
* [Global distribution – under huven](global-dist-under-the-hood.md)
