---
title: Så här konfigurerar du flera original i Azure Cosmos DB
description: Lär dig hur du konfigurerar multi-master för dina program med hjälp av olika SDK:er i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873649"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurera multi-master i dina program som använder Azure Cosmos DB

När ett konto har skapats med flera skrivregioner aktiverade måste du göra två ändringar i ditt program till ConnectionPolicy för DocumentClient för att aktivera multi-master- och multi-homing-funktionerna i Azure Cosmos DB. I ConnectionPolicy anger du UseMultipleWriteLocations till true och skickar namnet på den region där programmet distribueras till SetCurrentLocation. Detta fyller i egenskapen PreferredLocations baserat på geografisk närhet från platsen som skickas in. Om en ny region senare läggs till i kontot behöver programmet inte uppdateras eller distribueras om, det identifieras automatiskt i den närmare regionen och kommer automatiskt att hem på den om en regional händelse inträffar.

> [!Note]
> Cosmos-konton som ursprungligen konfigurerades med en enda skrivregion kan konfigureras till flera skrivregioner (dvs. multi-master) med noll nedtid. Mer information finns i [Konfigurera regioner med flera skrivningar](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Om du vill aktivera multi-master `true`i programmet ställer du in på `UseMultipleWriteLocations` . Ange `SetCurrentLocation` också till den region där programmet distribueras och där Azure Cosmos DB replikeras:

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

Om du vill aktivera multi-master i ditt program anger du `ApplicationRegion` till den region där programmet distribueras och där Cosmos DB replikeras:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Du kan också använda `CosmosClientBuilder` `WithApplicationRegion` och uppnå samma resultat:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-async-sdk"></a><a id="java"></a>Java Async SDK

Om du vill aktivera multi-master i ditt program ställer du in `policy.setUsingMultipleWriteLocations(true)` och ställer in `policy.setPreferredLocations` på den region där programmet distribueras och där Cosmos DB replikeras:

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

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Nod.js, JavaScript och TypeScript SDK:er

Om du vill aktivera multi-master `true`i programmet ställer du in på `connectionPolicy.UseMultipleWriteLocations` . Ange `connectionPolicy.PreferredLocations` också till den region där programmet distribueras och där Cosmos DB replikeras:

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

Om du vill aktivera multi-master `true`i programmet ställer du in på `connection_policy.UseMultipleWriteLocations` . Ange `connection_policy.PreferredLocations` också till den region där programmet distribueras och där Cosmos DB replikeras.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar:

* [Använda sessionstoken för att hantera konsekvens i Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Konflikttyper och principer för konfliktlösning i Azure Cosmos DB](conflict-resolution-policies.md)
* [Hög tillgänglighet i Azure Cosmos DB](high-availability.md)
* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Välj rätt konsekvensnivå i Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisser om konsekvens, tillgänglighet och prestanda i Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Tillgänglighets- och prestandaavvägningar för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Skala etablerat dataflöde globalt](scaling-throughput.md)
* [Global fördelning: Under huven](global-dist-under-the-hood.md)
