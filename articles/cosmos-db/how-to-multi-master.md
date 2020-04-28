---
title: Så här konfigurerar du flera original i Azure Cosmos DB
description: 'Lär dig hur du konfigurerar multi-master för dina program genom att använda olika SDK: er i Azure Cosmos DB.'
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873649"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurera flera huvud i dina program som använder Azure Cosmos DB

När ett konto har skapats med flera Skriv regioner aktiverade måste du göra två ändringar i programmet till ConnectionPolicy för DocumentClient för att aktivera flera huvud-och multi-värdar-funktioner i Azure Cosmos DB. I ConnectionPolicy anger du UseMultipleWriteLocations till sant och skickar namnet på den region där programmet distribueras till SetCurrentLocation. Detta fyller i egenskapen PreferredLocations baserat på Geo-närhet från den plats som har skickats. Om en ny region senare läggs till i kontot, behöver programmet inte uppdateras eller omdistribueras. det kommer automatiskt att identifiera den närmare regionen och automatiskt starta den om en regional händelse inträffar.

> [!Note]
> Cosmos-konton som ursprungligen konfigureras med en enda Skriv region kan konfigureras till flera Skriv regioner (dvs. multi-master) utan tids gräns. Läs mer i [Konfigurera flera-Write-regioner](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

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

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

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

## <a name="java-async-sdk"></a><a id="java"></a>Java Async SDK

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

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node. js, Java Script och TypeScript SDK: er

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

## <a name="python-sdk"></a><a id="python"></a>Python SDK

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
* [Konsekvens, tillgänglighet och prestanda kompromisser i Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](consistency-levels-tradeoffs.md)
* [Skala etablerat dataflöde globalt](scaling-throughput.md)
* [Global distribution: under huven](global-dist-under-the-hood.md)
