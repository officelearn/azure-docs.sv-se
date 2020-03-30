---
title: Regionala slutpunkter för Azure Cosmos DB Graph-databas
description: Lär dig hur du ansluter till närmaste graph-databasslutpunkt för ditt program
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 7aa1e0aa6bbbee9d40eb0d48318a8e2908a75f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897866"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Regionala slutpunkter för Azure Cosmos DB Graph-konto
Azure Cosmos DB Graph-databasen [distribueras globalt](distribute-data-globally.md) så att program kan använda flera lässlutpunkter. Program som behöver skrivåtkomst på flera platser bör aktivera [multi-master-funktioner.](how-to-multi-master.md)

Skäl att välja mer än en region:
1. **Vågrät lässkalbarhet** – när programbelastningen ökar kan det vara klokt att dirigera lästrafik till olika Azure-regioner.
2. **Lägre svarstid** - du kan minska nätverksfördröjningen omför varje traversal genom att dirigera läs- och skrivtrafik till närmaste Azure-region.

**Data hemvistkrav** uppnås genom att ange Azure Resource Manager-principen på Cosmos DB-konto. Kunden kan begränsa regioner där Cosmos DB replikerar data.

## <a name="traffic-routing"></a>Trafikroutning

Cosmos DB Graph databasmotor körs i flera regioner, som var och en innehåller flera kluster. Varje kluster har hundratals datorer. Cosmos DB Graph-konto DNS CNAME *accountname.gremlin.cosmos.azure.com* matchas till DNS En post i ett kluster. En enda IP-adress för en belastningsutjämnare döljer intern klustertopologi.

En regional DNS CNAME-post skapas för varje region i Cosmos DB Graph-konto. Format för den regionala slutpunkten är *accountname-region.gremlin.cosmos.azure.com*. Regionsegmentet för regional slutpunkt hämtas genom att ta bort alla blanksteg från [Azure-regionnamn.](https://azure.microsoft.com/global-infrastructure/regions) `"East US 2"` Region för `"contoso"` globalt databaskonto skulle till exempel ha ett DNS CNAME-contoso-eastus2.gremlin.cosmos.azure.com *contoso-eastus2.gremlin.cosmos.azure.com*

TinkerPop Gremlin klient är utformad för att fungera med en enda server. Programmet kan använda global skrivbar DNS CNAME för läs- och skrivtrafik. Regionmedvetna program bör använda regional slutpunkt för lästrafik. Använd regional slutpunkt för skrivtrafik endast om en viss region är konfigurerad för att acceptera skrivningar. 

> [!NOTE]
> Cosmos DB Graph-motorn kan acceptera skrivfunktionen i läsregion genom att proxyera trafik till skrivregionen. Det rekommenderas inte att skicka skrivningar till skrivskyddad region eftersom det ökar genomgående svarstid och är föremål för begränsningar i framtiden.

CNAME för globalt databaskonto pekar alltid på ett giltigt skrivområde. Under redundans för serversidan i skrivregionen uppdaterar Cosmos DB det globala databaskontot CNAME för att peka på en ny region. Om programmet inte kan hantera omdirigering av trafik efter redundans bör det använda det globala databaskontot DNS CNAME.

> [!NOTE]
> Cosmos DB dirigerar inte trafik baserat på den som ringer geografisk närhet. Det är upp till varje program att välja rätt region enligt unika programbehov.

## <a name="portal-endpoint-discovery"></a>Identifiering av portalslutpunkt

Det enklaste sättet att få en lista över regioner för Azure Cosmos DB Graph-konto är översiktsbladet i Azure-portalen. Det kommer att fungera för program som inte ändrar regioner ofta, eller har ett sätt att uppdatera listan via programkonfiguration.

![Hämta regioner i Cosmos DB Graph-konto från portalen](./media/how-to-use-regional-gremlin/get-end-point-portal.png )

Exempel nedan visar allmänna principer för att få tillgång till regionala Gremlin-slutpunkt. Ansökan bör överväga antal regioner att skicka trafiken till och antalet motsvarande Gremlin klienter att instansiera.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>Identifiering av SDK-slutpunkt

Programmet kan använda [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) för att identifiera läs- och skrivplatser för Graph-konto. Dessa platser kan ändras när som helst genom manuell omkonfiguration på serversidan eller automatisk redundans.

TinkerPop Gremlin SDK har inget API för att identifiera databaskontoregioner i Cosmos DB Graph. Program som behöver identifiering av slutpunkt för körning måste vara värd för två separata SDK:er i processutrymmet.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Nästa steg
* [Hantera databaskontokontroll](how-to-manage-database-account.md) i Azure Cosmos DB
* [Hög tillgänglighet](high-availability.md) i Azure Cosmos DB
* [Global distribution med Azure Cosmos DB - under huven](global-dist-under-the-hood.md)
* [Azure CLI-exempel](cli-samples.md) för Azure Cosmos DB
