---
title: Azure Cosmos DB-resurspooler med Gremlin
description: Lär dig hur du skapar resurs-tokens och använder dem för att komma åt diagram databasen
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806913"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Azure Cosmos DB-resurspooler med Gremlin
Den här artikeln förklarar hur du använder [Cosmos DB-resursfiler](secure-access-to-data.md) för att få åtkomst till Graph Database via Gremlin SDK.

## <a name="create-a-resource-token"></a>Skapa en resurs-token

TinkerPop Gremlin SDK har inget API för att skapa resurs-token. Resurs-token är ett Cosmos DB koncept. Hämta [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)för att skapa resurs-token. Om ditt program behöver skapa resurs-tokens och använda dem för att få åtkomst till Graph-databasen behöver den två separata SDK: er.

Objekt modells-hierarki över resurs-token:
- **Cosmos DB konto** – den översta entiteten som har DNS kopplat till sig, till exempel`contoso.gremlin.cosmos.azure.com`
  - **Cosmos DB databas**
    - **Användarvänlig**
      - **Permission**
        - *Token* – en egenskap för **behörighets** objekt som anger vilka åtgärder som tillåts eller nekas.

En Resource-token `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`har ett format. Den här strängen är ogenomskinlig för klienterna och bör användas som-är utan ändring eller tolkning.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Använd en Resource-token
Resurs-token kan användas direkt som "Password"-egenskap vid konstruktion `GremlinServer` av klass.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Samma metod fungerar i alla TinkerPop Gremlin SDK: er.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Gräns

Ett enskilt Gremlin-konto kan utfärda ett obegränsat antal tokens, men bara upp till **100** token kan användas samtidigt inom **en timme**. Om programmet överskrider token-gränsen per timme, kommer autentiseringsbegäran att nekas med `"Exceeded allowed resource token limit of 100 that can be used concurrently"`ett fel meddelande. Det går inte att stänga aktiva anslutningar med vissa token för att frigöra platser för nya token. Cosmos DB Gremlin Database Engine håller reda på distinkta token under den senaste timmen innan autentiseringsbegäran.

## <a name="permission"></a>Behörighet

Vanliga fel program skickas `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`i när du använder Resource tokens. Det här felet returneras när Gremlin-Traversal försöker skriva en gräns eller ett hörn, men en `Read` resurs-token endast beviljar behörigheter. Undersök din genom gång om den innehåller något av följande steg: `.addV()` `.drop()`, `.addE()`, eller `.property()`.

## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomst kontroll](role-based-access-control.md) i Azure Cosmos DB
* [Lär dig hur du skyddar åtkomsten till data](secure-access-to-data.md) i Azure Cosmos DB
