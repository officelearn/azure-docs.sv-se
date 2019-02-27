---
title: Så konfigurerar du flera original i Azure Cosmos DB
description: Lär dig hur du konfigurerar flera original i dina program i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 84c8e2921602bb653c0b1ef0adffd3d89e91bd78
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312148"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Så konfigurerar du flera original i dina program som använder Azure Cosmos DB

För att kunna använda funktioner för flera original i dina program behöver  du aktivera skrivningar i flera regioner och konfigurera multihoming-funktion. Multihoming konfigureras genom att den aktuella regionen anges där programmet distribueras.

## <a id="netv2"></a>.NET SDK v2

För att aktivera flera original i dina program anger du `UseMultipleWriteLocations` till sant och konfigurerar `SetCurrentLocation` till den region där programmet distribueras och Azure Cosmos DB replikeras.

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

För att aktivera flera original i dina program konfigurerar du `policy.setUsingMultipleWriteLocations(true)` till sant och konfigurerar `policy.setPreferredLocations` till den region där programmet distribueras, så replikeras Cosmos DB.

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

Läs mer om flera huvudservrar, global distribution och konsekvens i Azure Cosmos DB. Se följande artiklar:

* [Använda sessionstoken för att hantera konsekvens i Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)

* [Konflikttyper och principer för konfliktlösning i Azure Cosmos DB](conflict-resolution-policies.md)

* [Hög tillgänglighet i Azure Cosmos DB](high-availability.md)

* [Välja rätt konsekvensnivå i Azure Cosmos DB](consistency-levels-choosing.md)

* [Kompromisser avseende konsekvens, tillgänglighet och prestanda i Azure Cosmos DB](consistency-levels-tradeoffs.md)
