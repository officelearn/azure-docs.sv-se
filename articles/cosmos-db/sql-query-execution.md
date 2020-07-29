---
title: Körning av SQL-fråga i Azure Cosmos DB
description: 'Lär dig hur du skapar en SQL-fråga och kör den i Azure Cosmos DB. Den här artikeln beskriver hur du skapar och kör en SQL-fråga med hjälp av REST API, .NET SDK, Java Script SDK och olika andra SDK: er.'
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74871269"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL-frågekörningen

Alla språk som kan göra HTTP/HTTPS-begäranden kan anropa Cosmos DB REST API. Cosmos DB erbjuder också programmerings bibliotek för programmeringsspråk för .NET, Node.js, Java Script och python. REST API och bibliotek som alla stöder frågor via SQL och .NET SDK har även stöd för [LINQ-frågor](sql-query-linq-to-sql.md).

I följande exempel visas hur du skapar en fråga och skickar den mot ett Cosmos-databas konto.

## <a name="rest-api"></a><a id="REST-API"></a>REST-API

Cosmos DB erbjuder en öppen RESTful-programmeringsmodell via HTTP. Resurs modellen består av en uppsättning resurser under ett databas konto, som en Azure-prenumeration tillhandahåller. Databas kontot består av en uppsättning *databaser*, som var och en kan innehålla flera *behållare*, vilket i sin tur innehåller *objekt*, UDF: er och andra resurs typer. Varje Cosmos DB resurs kan adresseras med hjälp av en logisk och stabil URI. En uppsättning resurser kallas för en *feed*. 

Den grundläggande interaktions modellen med dessa resurser är via HTTP-verben `GET` ,, `PUT` `POST` och `DELETE` , med sina standard tolkningar. Använd `POST` för att skapa en ny resurs, köra en lagrad procedur eller utfärda en Cosmos DB fråga. Frågor är alltid skrivskyddade åtgärder utan bieffekter.

I följande exempel visas en `POST` for SQL API-fråga mot exempel objekten. Frågan har ett enkelt filter i JSON- `name` egenskapen. `x-ms-documentdb-isquery`Contents-Type:- `application/query+json` huvudena anger att åtgärden är en fråga. Ersätt `mysqlapicosmosdb.documents.azure.com:443` med URI: n för ditt Cosmos DB-konto.

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

Nästa, mer komplex fråga returnerar flera resultat från en koppling:

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

Om resultatet av en fråga inte får plats på en sida, returnerar REST API en fortsättnings-token via `x-ms-continuation-token` svars huvudet. Klienter kan infoga sid brytningar genom att inkludera rubriken i de efterföljande resultaten. Du kan också styra antalet resultat per sida genom `x-ms-max-item-count` nummer rubriken.

Om en fråga har en agg regerings funktion som COUNT kan sidan fråga returnera ett delvis sammanställt värde över bara en resultat sida. Klienterna måste utföra en andra nivå agg regering över dessa resultat för att producera de slutliga resultaten. Till exempel sum över antalet som returneras på de enskilda sidorna för att returnera det totala antalet.

Om du vill hantera principen för data konsekvens för frågor använder du `x-ms-consistency-level` rubriken som i alla REST API begär Anden. Konsekvensen i sessionen kräver också att det senaste `x-ms-session-token` cookie-huvudet i förfrågan skickas. Den befrågade containerns indexeringsprincip kan också påverka konsekvensen för frågeresultatet. Med standard indexerings princip inställningarna för behållare är indexet alltid aktuellt med objekt innehållet, och frågeresultaten matchar den konsekvens som valts för data. Mer information finns i [Azure Cosmos DB konsekvens nivåer] [konsekvens-nivåer].

Om den konfigurerade index principen i behållaren inte stöder den angivna frågan returnerar Azure Cosmos DB server 400 "felaktig begäran". Det här fel meddelandet returnerar för frågor med sökvägar som uttryckligen utesluts från indexering. Du kan ange `x-ms-documentdb-query-enable-scan` rubriken för att tillåta att frågan utför en sökning när ett index inte är tillgängligt.

Du kan få detaljerade mått på frågekörningen genom `x-ms-documentdb-populatequerymetrics` att ange rubriken till `true` . Mer information finns i avsnittet om [SQL-frågemått för Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

.NET SDK stöder både LINQ- och SQL-frågor. I följande exempel visas hur du utför föregående filter fråga med .NET:

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

I följande exempel jämförs två egenskaper för jämställdhet inom varje objekt, och anonyma projektioner används.

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

I nästa exempel visas kopplingar, som uttrycks via LINQ `SelectMany` .

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

.NET-klienten itererar automatiskt genom alla sidor i frågeresultaten i `foreach` blocken, som du ser i föregående exempel. Frågealternativen som introducerades i [REST API](#REST-API) avsnittet är också tillgängliga i .NET SDK, med hjälp av `FeedOptions` `FeedResponse` klasserna och i- `CreateDocumentQuery` metoden. Du kan styra antalet sidor med `MaxItemCount` inställningen.

Du kan också uttryckligen styra växling genom `IDocumentQueryable` att skapa med hjälp av `IQueryable` objektet och sedan läsa `ResponseContinuationToken` värdena och skicka tillbaka dem som `RequestContinuationToken` i `FeedOptions` . Du kan ställa in `EnableScanInQuery` för att aktivera genomsökningar när frågan inte stöds av den konfigurerade indexerings principen. För partitionerade behållare kan du använda `PartitionKey` för att köra frågan mot en enda partition, även om Azure Cosmos DB kan extrahera detta automatiskt från frågetexten. Du kan använda `EnableCrossPartitionQuery` för att köra frågor mot flera partitioner.

Fler .NET-exempel med frågor finns i [Azure Cosmos dB .net-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3) i GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScript-API på serversidan

Azure Cosmos DB tillhandahåller en programmerings modell för att [köra JavaScript-baserad program](stored-procedures-triggers-udfs.md) logik direkt på behållare med hjälp av lagrade procedurer och utlösare. Den JavaScript-logik som registrerats på behållar nivån kan sedan utfärda databas åtgärder för objekten i den aktuella behållaren, omslutna i aktiviteter med omgivande syra.

I följande exempel visas hur du använder `queryDocuments` i Java Script Server API för att skapa frågor från lagrade procedurer och utlösare:

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
- [Azure Cosmos DB konsekvens nivåer](consistency-levels.md)
