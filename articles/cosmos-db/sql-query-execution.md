---
title: SQL-frågekörning i Azure Cosmos DB
description: Lär dig hur du skapar en SQL-fråga och kör den i Azure Cosmos DB. I den här artikeln beskrivs hur du skapar och kör en SQL-fråga med REST API, .Net SDK, JavaScript SDK och flera andra SDK:er.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871269"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL-frågekörning

Alla språk som kan göra HTTP/HTTPS-begäranden kan anropa Cosmos DB REST API. Cosmos DB erbjuder även programmeringsbibliotek för programmeringsspråken .NET, Node.js, JavaScript och Python. REST API och bibliotek alla stöder frågor via SQL, och .NET SDK stöder också [LINQ fråga .](sql-query-linq-to-sql.md)

Följande exempel visar hur du skapar en fråga och skickar den mot ett Cosmos-databaskonto.

## <a name="rest-api"></a><a id="REST-API"></a>REST API

Cosmos DB erbjuder en öppen RESTful-programmeringsmodell via HTTP. Resursmodellen består av en uppsättning resurser under ett databaskonto, som en Azure-prenumeration avsättningar. Databaskontot består av en uppsättning *databaser*, som var och en kan innehålla flera *behållare*, som i sin tur innehåller *objekt,* UDF och andra resurstyper. Varje Cosmos DB-resurs kan adresseras med hjälp av en logisk och stabil URI. En uppsättning resurser kallas *för en feed*. 

Den grundläggande `GET`interaktionsmodellen med dessa resurser `PUT`är `POST`via `DELETE`HTTP-verben , , och , med sina standardtolkningar. Används `POST` för att skapa en ny resurs, köra en lagrad procedur eller utfärda en Cosmos DB-fråga. Frågor är alltid skrivskyddade åtgärder utan bieffekter.

Följande exempel visar `POST` en för en SQL API-fråga mot exempelobjekten. Frågan har ett enkelt filter `name` på JSON-egenskapen. Rubrikerna `x-ms-documentdb-isquery` och Innehållstyp: `application/query+json` anger att åtgärden är en fråga. Ersätt `mysqlapicosmosdb.documents.azure.com:443` med URI för ditt Cosmos DB-konto.

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

Nästa, mer komplexa fråga returnerar flera resultat från en koppling:

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

Om en frågas resultat inte får plats på en enda sida returnerar REST API en fortsättningstoken via `x-ms-continuation-token` svarshuvudet. Klienter kan paginera resultat genom att inkludera huvudet i de efterföljande resultaten. Du kan också styra antalet resultat `x-ms-max-item-count` per sida via nummerrubriken.

Om en fråga har en aggregeringsfunktion som ANTAL kan frågesidan returnera ett delvis aggregerat värde över endast en resultatsida. Klienter måste utföra en aggregering på andra nivån över dessa resultat för att ge de slutliga resultaten. Till exempel summan över de antal som returneras på de enskilda sidorna för att returnera det totala antalet.

Om du vill hantera principen om datakonsekvens för frågor använder du `x-ms-consistency-level` huvudet som i alla REST API-begäranden. Sessionskonsekvens kräver också att `x-ms-session-token` du upprepar det senaste cookie-huvudet i frågebegäran. Den befrågade containerns indexeringsprincip kan också påverka konsekvensen för frågeresultatet. Med standardindexeringsprincipinställningarna för behållare är indexet alltid aktuellt med artikelinnehållet och frågeresultaten matchar den konsekvens som valts för data. Mer information finns i [Azure Cosmos DB konsekvensnivåer][konsekvensnivåer].

Om den konfigurerade indexeringsprincipen på behållaren inte kan stödja den angivna frågan returnerar Azure Cosmos DB-servern 400 "Felaktig begäran". Det här felmeddelandet returnerar för frågor med sökvägar som uttryckligen är undantagna från indexering. Du kan `x-ms-documentdb-query-enable-scan` ange rubriken så att frågan kan utföra en genomsökning när ett index inte är tillgängligt.

Du kan få detaljerade mått på `x-ms-documentdb-populatequerymetrics` frågekörning genom att ange huvudet till `true`. Mer information finns i avsnittet om [SQL-frågemått för Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

.NET SDK stöder både LINQ- och SQL-frågor. I följande exempel visas hur du utför den föregående filterfrågan med .NET:

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

I följande exempel jämförs två egenskaper för likhet inom varje objekt och anonyma projektioner används.

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

I nästa exempel visas kopplingar, `SelectMany`uttryckta via LINQ .

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

.NET-klienten itererar automatiskt igenom alla sidor `foreach` med frågeresultat i blocken, vilket visas i föregående exempel. De frågealternativ som introduceras i avsnittet [REST API](#REST-API) är också tillgängliga `FeedOptions` `FeedResponse` i .NET SDK, med hjälp av och klasser i `CreateDocumentQuery` metoden. Du kan styra antalet sidor `MaxItemCount` med hjälp av inställningen.

Du kan också uttryckligen styra `IDocumentQueryable` växling genom att skapa med objektet, `IQueryable` sedan genom att läsa `ResponseContinuationToken` värdena och skicka tillbaka dem som `RequestContinuationToken` i `FeedOptions`. Du kan `EnableScanInQuery` ange att aktivera genomsökningar när frågan inte stöds av den konfigurerade indexeringsprincipen. För partitionerade behållare kan `PartitionKey` du använda för att köra frågan mot en enda partition, även om Azure Cosmos DB automatiskt kan extrahera detta från frågetexten. Du kan `EnableCrossPartitionQuery` använda för att köra frågor mot flera partitioner.

Mer .NET-exempel med frågor finns i [Azure Cosmos DB .NET-exemplen](https://github.com/Azure/azure-cosmos-dotnet-v3) i GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScript-API på serversidan

Azure Cosmos DB tillhandahåller en programmeringsmodell för [att köra JavaScript-baserad programlogik](stored-procedures-triggers-udfs.md) direkt på behållare, med hjälp av lagrade procedurer och utlösare. JavaScript-logiken som registrerats på behållarnivå kan sedan utfärda databasåtgärder på objekten i den angivna behållaren, insvept i omgivande ACID-transaktioner.

I följande exempel visas `queryDocuments` hur du använder i JavaScript-server-API:et för att skapa frågor inifrån lagrade procedurer och utlösare:

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
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Konsekvensnivåer för Azure Cosmos DB](consistency-levels.md)
