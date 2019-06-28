---
title: SQL-frågekörning i Azure Cosmos DB
description: Lär dig mer om SQL-frågekörning i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342488"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL-frågekörning

Alla språk som stöder HTTP/HTTPS-förfrågningar kan anropa REST-API i Cosmos DB. Cosmos DB erbjuder också programmeringsbibliotek för .NET, Node.js, JavaScript och Python programmeringsspråk. REST API och bibliotek alla stöd för frågor via SQL och .NET SDK stöder också [LINQ-frågor](sql-query-linq-to-sql.md).

I följande exempel visas hur du skapar en fråga och skickar den mot ett Cosmos DB-databaskonto.

## <a id="REST-API"></a>REST API

Cosmos DB erbjuder en öppen RESTful-programmeringsmodell via HTTP. Vilken resursmodell som består av en uppsättning resurser under ett databaskonto som etablerar en Azure-prenumeration. Kontot som består av en uppsättning *databaser*, som kan innehålla flera *behållare*, vilket i sin tur innehåller *objekt*, användardefinierade funktioner och andra typer av resurser. Varje Cosmos DB-resurs är adresserbara via en logisk och stabil URI. En uppsättning resurser kallas en *feed*. 

Grundläggande interaction modellen med dessa resurser är via HTTP-verb `GET`, `PUT`, `POST`, och `DELETE`, med sina standard tolkningar. Använd `POST` för att skapa en ny resurs, kör en lagrad procedur eller utfärda en Cosmos DB-fråga. Frågor är alltid skrivskyddade åtgärder utan bieffekter.

Följande exempel visar en `POST` för en SQL API-frågor mot en exempelobjekt. Frågan har en enkel filter på JSON- `name` egenskapen. Den `x-ms-documentdb-isquery` och Content-Type: `application/query+json` rubriker anger att åtgärden är en fråga. Ersätt `mysqlapicosmosdb.documents.azure.com:443` med URI: N för Cosmos DB-kontot.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Resultatet är:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Nästa och mer komplexa frågan returnerar flera resultat från en koppling:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Resultatet är: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Om en frågas resultat inte får plats på en enda sida, REST-API: et returnerar ett fortsättningstoken via den `x-ms-continuation-token` svarshuvudet. Klienter kan sidbryta resultat genom att inkludera rubriken i efterföljande resultaten. Du kan också styra hur många resultat per sida via den `x-ms-max-item-count` nummer rubrik.

Om en fråga har en Aggregeringsfunktion som antal, kan sidan returnera en delvis aggregerat värde endast en resultatsida. Klienter måste utföra en andra nivån aggregering över dessa resultat för att skapa de slutliga resultaten. Till exempel summera över antal returneras i de enskilda sidorna att returnera det totala antalet.

Hantera Datapolicy för konsekvens för frågor med den `x-ms-consistency-level` rubrik som i alla REST API-begäranden. Sessionskonsekvens kräver också eko senast `x-ms-session-token` cookiehuvud i query-fråga. Den befrågade containerns indexeringsprincip kan också påverka konsekvensen för frågeresultatet. Med standardvärdet indexering principinställningar för behållare, indexet är alltid uppdaterad med objekt-innehållet och frågeresultat matcha konsekvens som valts för data. Mer information finns i [Azure Cosmos DB-konsekvensnivåer] [konsekvensnivåer].

Om den konfigurera indexprincip för behållaren inte stöder den angivna frågan, returnerar 400 ”Felaktig begäran” i Azure Cosmos DB-servern. Det här felmeddelandet returnerar för frågor med sökvägar som uttryckligen är undantagen från indexering. Du kan ange den `x-ms-documentdb-query-enable-scan` rubrik så att frågan för att utföra en genomsökning när ett index är inte tillgänglig.

Du kan få detaljerade mätvärden om frågekörning genom att ange den `x-ms-documentdb-populatequerymetrics` sidhuvud till `true`. Mer information finns i avsnittet om [SQL-frågemått för Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

.NET SDK stöder både LINQ- och SQL-frågor. I följande exempel visas hur du utför den föregående filtreringsfrågan med .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

I följande exempel Jämför två egenskaper sinsemellan i varje objekt och använder anonym projektioner.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

I nästa exempel visas kopplingar, uttryckt genom LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET-klienten automatiskt går igenom alla sidor i frågeresultaten i den `foreach` blockerar, som visas i föregående exempel. Frågealternativ introducerades i den [REST API](#REST-API) avsnittet är också tillgängliga i .NET-SDK med hjälp av den `FeedOptions` och `FeedResponse` klasser i den `CreateDocumentQuery` metoden. Du kan styra hur många sidor med hjälp av den `MaxItemCount` inställningen.

Du kan också uttryckligen styra sidindelning genom att skapa `IDocumentQueryable` med hjälp av den `IQueryable` objekt sedan genom att läsa den `ResponseContinuationToken` värden och skicka dem tillbaka som `RequestContinuationToken` i `FeedOptions`. Du kan ange `EnableScanInQuery` aktivera sökningar när frågan inte stöds av den konfigurera indexprincip. Du kan använda för partitionerad behållare `PartitionKey` att köra frågan mot en enskild partition, även om Azure Cosmos DB automatiskt extrahera detta från frågetexten. Du kan använda `EnableCrossPartitionQuery` att köra frågor mot flera partitioner.

Mer .NET-exempel med frågor finns i [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet) i GitHub.

## <a id="JavaScript-server-side-API"></a>JavaScript-API på serversidan

Azure Cosmos DB är en programmeringsmodell för [körning av JavaScript-baserat program](stored-procedures-triggers-udfs.md) logik direkt på behållare, med hjälp av lagrade procedurer och utlösare. JavaScript-logik som registrerats på behållarenivån kan sedan utfärda databasåtgärder i objekt av den angivna behållaren, och är inneslutna i omgivande ACID-transaktioner.

I följande exempel visas hur du använder `queryDocuments` i JavaScript-server API att göra förfrågningar från insidan lagrade procedurer och utlösare:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB-konsekvensnivåer](consistency-levels.md)
