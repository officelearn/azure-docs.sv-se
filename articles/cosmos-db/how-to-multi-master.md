---
title: Så här konfigurerar du flera original i Azure Cosmos DB
description: Lär dig hur du konfigurerar flera huvud i dina program i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: a5232101d4e5d13fb4c95268311e06b56fa81e65
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356214"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurera flera huvud i dina program som använder Azure Cosmos DB

När ett konto har skapats med flera Skriv regioner aktiverade måste du göra två ändringar i programmet till ConnectionPolicy för DocumentClient för att aktivera flera huvud-och multi-värdar-funktioner i Azure Cosmos DB. I ConnectionPolicy anger du UseMultipleWriteLocations till sant och skickar namnet på den region där programmet distribueras till SetCurrentLocation. Detta fyller i egenskapen PreferredLocations baserat på Geo-närhet från den plats som har skickats. Om en ny region senare läggs till i kontot, behöver programmet inte uppdateras eller omdistribueras. det kommer automatiskt att identifiera den närmare regionen och automatiskt starta den om en regional händelse inträffar.

> [!Note]
> Cosmos-konton som ursprungligen konfigureras med en enda Skriv region kan konfigureras till flera Skriv regioner (dvs. multi-master) utan tids gräns. Läs mer i [Konfigurera flera-Write-regioner](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a id="netv2"></a>.NET SDK v2

Om du vill aktivera multi-master i programmet anger `UseMultipleWriteLocations` du `true`till. Ange `SetCurrentLocation` också den region där programmet distribueras och där Azure Cosmos DB replikeras:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3

Om du vill aktivera multi-master i programmet anger `ApplicationRegion` du den region där programmet distribueras och där Cosmos DB replikeras:

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

## <a id="java"></a>Java Async SDK

Om du vill aktivera multi-master i ditt program `policy.setUsingMultipleWriteLocations(true)` ställer du `policy.setPreferredLocations` in och anger den region där programmet distribueras och där Cosmos DB replikeras:

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

## <a id="javascript"></a>Node. js, Java Script och TypeScript SDK: er

Om du vill aktivera multi-master i programmet anger `connectionPolicy.UseMultipleWriteLocations` du `true`till. Ange `connectionPolicy.PreferredLocations` också den region där programmet distribueras och där Cosmos DB replikeras:

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

Om du vill aktivera multi-master i programmet anger `connection_policy.UseMultipleWriteLocations` du `true`till. Ange `connection_policy.PreferredLocations` också den region där programmet distribueras och där Cosmos DB replikeras.

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
* [Konsekvens nivåer i Azure Cosmos DB](consistency-levels.md)
* [Välj rätt konsekvens nivå i Azure Cosmos DB](consistency-levels-choosing.md)
* [Avvägningar gällande konsekvens, tillgänglighet och prestanda i Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Globalt skalning av allokerat data flöde](scaling-throughput.md)
* [Global distribution: Bakom kulisserna](global-dist-under-the-hood.md)
