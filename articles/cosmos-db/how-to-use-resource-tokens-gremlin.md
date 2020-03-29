---
title: Använda Azure Cosmos DB-resurstoken med Gremlin SDK
description: Lär dig hur du skapar resurstoken och använder dem för att komma åt Graph-databasen.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 42f3c7f3351bddab429489dccf28587549d76e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897839"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Använda Azure Cosmos DB-resurstoken med Gremlin SDK

I den här artikeln beskrivs hur du använder [Azure Cosmos DB-resurstoken](secure-access-to-data.md) för att komma åt Graph-databasen via Gremlin SDK.

## <a name="create-a-resource-token"></a>Skapa en resurstoken

Apache TinkerPop Gremlin SDK har inget API att använda för att skapa resurstoken. Termen *resurstoken* är ett Azure Cosmos DB-koncept. Om du vill skapa resurstokens laddar du ned [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md). Om ditt program behöver skapa resurstoken och använda dem för att komma åt Graph-databasen krävs två separata SDK:er.

Objektmodellhierarkin ovanför resurstokens illustreras i följande disposition:

- **Azure Cosmos DB-konto** - Den översta entiteten som har `contoso.gremlin.cosmos.azure.com`en DNS associerad med sig (till exempel ).
  - **Azure Cosmos DB-databas**
    - **Användare**
      - **Behörighet**
        - **Token** - Egenskapen Permission object som anger vilka åtgärder som tillåts eller nekas.

En resurstoken använder `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`följande format: . Den här strängen är ogenomskinlig för klienterna och bör användas som den är, utan ändringar eller tolkning.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Använda en resurstoken
Du kan använda resurstoken direkt som egenskapen "lösenord" när du skapar klassen GremlinServer.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Samma tillvägagångssätt fungerar i alla TinkerPop Gremlin SDKs.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Gräns

Med ett enda Gremlin-konto kan du utfärda ett obegränsat antal tokens. Du kan dock bara använda upp till 100 token samtidigt inom 1 timme. Om ett program överskrider tokengränsen per timme nekas en autentiseringsbegäran och följande felmeddelande visas: "Överskriden tillåten resurstokengräns på 100 som kan användas samtidigt." Det fungerar inte att stänga aktiva anslutningar som använder specifika token för att frigöra platser för nya tokens. Azure Cosmos DB Gremlin databasmotor håller reda på unika tokens under timmen omedelbart före autentiseringsbegäran.

## <a name="permission"></a>Behörighet

Ett vanligt fel som program stöter på när de använder resurstoken är"Otillräckliga behörigheter som anges i auktoriseringshuvudet för motsvarande begäran. Försök igen med ett annat auktoriseringshuvud." Det här felet returneras när en Gremlin traversal försöker skriva en kant eller ett hörn men resurstoken ger läsbehörighet endast. *Read* Kontrollera din traversal för att se om den innehåller något av följande steg: *.addV()*, *.addE()*, *.drop()* eller *.property()*.

## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomstkontroll](role-based-access-control.md) i Azure Cosmos DB
* [Lär dig hur du skyddar åtkomsten till data](secure-access-to-data.md) i Azure Cosmos DB
