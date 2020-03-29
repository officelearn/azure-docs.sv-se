---
title: Azure Cosmos DB-indatabindning för funktioner 2.x
description: Lär dig att använda Azure Cosmos DB-indatabindningen i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: eabcf40e28927919215979ccc46fa029d19adbfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943425"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x"></a>Azure Cosmos DB-indatabindning för Azure Functions 2.x

Azure Cosmos DB-indatabindningen använder SQL API för att hämta en eller flera Azure Cosmos DB-dokument och skickar dem till indataparametern för funktionen. Dokument-ID:t och frågeparametrarna kan fastställas baserat på den utlösare som anropar funktionen.

Information om inställnings- och konfigurationsinformation finns i [översikten](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Om samlingen är [partitionerad](../cosmos-db/partition-data.md#logical-partitions)måste uppslagsåtgärder också ange värdet för partitionsnyckeln.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* [Köutlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-utlösare, slå upp ID från frågesträngen](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-utlösare, slå upp ID från vägdata](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-utlösare, slå upp ID från vägdata med SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-utlösare, hämta flera dokument med SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-utlösare, hämta flera dokument med DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Exemplen refererar `ToDoItem` till en enkel typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köutlösare, slå upp ID från JSON 

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enda dokument. Funktionen utlöses av ett kömeddelande som innehåller ett JSON-objekt. Köutlösaren tolkar JSON till ett `ToDoItemLookup`objekt av typen , som innehåller ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel `ToDoItem` används för att hämta ett dokument från den angivna databasen och samlingen.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, slå upp ID från frågesträngen

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel `ToDoItem` används för att hämta ett dokument från den angivna databasen och samlingen.

>[!NOTE]
>PARAMETERN HTTP-frågesträng är skiftlägeskänslig.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från vägdata

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder vägdata för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel `ToDoItem` används för att hämta ett dokument från den angivna databasen och samlingen.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-utlösare, slå upp ID från vägdata med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder vägdata för att ange id för att slå upp. Det ID:t används `ToDoItem` för att hämta ett dokument från den angivna databasen och samlingen.

Exemplet visar hur du använder ett `SqlQuery` bindningsuttryck i parametern. Du kan skicka flödesdata till parametern `SqlQuery` som visas, men för närvarande kan du inte skicka [frågesträngvärden](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Om du behöver fråga efter bara ID, rekommenderas att använda en slå upp, som [de tidigare exemplen](#http-trigger-look-up-id-from-query-string-c), eftersom det kommer att förbruka mindre [begäranheter](../cosmos-db/request-units.md). Punktläsningsåtgärder (GET) är [effektivare](../cosmos-db/optimize-cost-queries.md) än frågor efter ID.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Frågan anges i attributegenskapen. `SqlQuery`

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-utlösare, hämta flera dokument med DocumentClient

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Koden använder `DocumentClient` en instans som tillhandahålls av Azure Cosmos DB-bindningen för att läsa en lista över dokument. Instansen `DocumentClient` kan också användas för skrivåtgärder.

> [!NOTE]
> Du kan också använda [Gränssnittet IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) för att göra testningen enklare.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* [Köutlösare, slå upp ID från sträng](#queue-trigger-look-up-id-from-string-c-script)
* [Köutlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, slå upp ID från frågesträngen](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-utlösare, slå upp ID från vägdata](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-utlösare, hämta flera dokument med SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, hämta flera dokument med DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP-utlösarexemplen refererar `ToDoItem` till en enkel typ:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Köutlösare, slå upp ID från sträng

I följande exempel visas en Cosmos DB-indatabindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets textvärde.

Här är bindningsdata i *filen function.json:*

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köutlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB-indatabindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga med hjälp av en köutlösare för att anpassa frågeparametrarna.

Köutlösaren tillhandahåller `departmentId`en parameter . Ett kömeddelande `{ "departmentId" : "Finance" }` om returnerar alla poster för ekonomiavdelningen.

Här är bindningsdata i *filen function.json:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, slå upp ID från frågesträngen

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel `ToDoItem` används för att hämta ett dokument från den angivna databasen och samlingen.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Här är C# skriptkoden:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från vägdata

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder vägdata för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel `ToDoItem` används för att hämta ett dokument från den angivna databasen och samlingen.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Här är C# skriptkoden:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Frågan anges i attributegenskapen. `SqlQuery`

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Här är C# skriptkoden:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-utlösare, hämta flera dokument med DocumentClient

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Koden använder `DocumentClient` en instans som tillhandahålls av Azure Cosmos DB-bindningen för att läsa en lista över dokument. Instansen `DocumentClient` kan också användas för skrivåtgärder.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Här är C# skriptkoden:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Det här avsnittet innehåller följande exempel som läser ett enda dokument genom att ange ett ID-värde från olika källor:

* [Köutlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-utlösare, slå upp ID från frågesträngen](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-utlösare, slå upp ID från vägdata](#http-trigger-look-up-id-from-route-data-javascript)
* [Köutlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köutlösare, slå upp ID från JSON

I följande exempel visas en Cosmos DB-indatabindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets textvärde.

Här är bindningsdata i *filen function.json:*

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, slå upp ID från frågesträngen

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel `ToDoItem` används för att hämta ett dokument från den angivna databasen och samlingen.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från vägdata

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder vägdata för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel `ToDoItem` används för att hämta ett dokument från den angivna databasen och samlingen.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köutlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB-indatabindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga med hjälp av en köutlösare för att anpassa frågeparametrarna.

Köutlösaren tillhandahåller `departmentId`en parameter . Ett kömeddelande `{ "departmentId" : "Finance" }` om returnerar alla poster för ekonomiavdelningen.

Här är bindningsdata i *filen function.json:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Det här avsnittet innehåller följande exempel som läser ett enda dokument genom att ange ett ID-värde från olika källor:

* [Köutlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-python)
* [HTTP-utlösare, slå upp ID från frågesträngen](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-utlösare, slå upp ID från vägdata](#http-trigger-look-up-id-from-route-data-python)
* [Köutlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köutlösare, slå upp ID från JSON

I följande exempel visas en Cosmos DB-indatabindning i en *function.json-fil* och en [Python-funktion](functions-reference-python.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets textvärde.

Här är bindningsdata i *filen function.json:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är Python-koden:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, slå upp ID från frågesträngen

I följande exempel visas en [Python-funktion](functions-reference-python.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel `ToDoItem` används för att hämta ett dokument från den angivna databasen och samlingen.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Här är Python-koden:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från vägdata

I följande exempel visas en [Python-funktion](functions-reference-python.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder vägdata för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel `ToDoItem` används för att hämta ett dokument från den angivna databasen och samlingen.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Här är Python-koden:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köutlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB-indatabindning i en *function.json-fil* och en [Python-funktion](functions-reference-python.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga med hjälp av en köutlösare för att anpassa frågeparametrarna.

Köutlösaren tillhandahåller `departmentId`en parameter . Ett kömeddelande `{ "departmentId" : "Finance" }` om returnerar alla poster för ekonomiavdelningen.

Här är bindningsdata i *filen function.json:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är Python-koden:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare, slå upp ID från frågesträngen - Strängparameter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-utlösare, slå upp ID från frågesträngen - POJO-parameter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP-utlösare, slå upp ID från vägdata](#http-trigger-look-up-id-from-route-data-java)
* [HTTP-utlösare, slå upp ID från vägdata med SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-utlösare, hämta flera dokument från vägdata med SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Exemplen refererar `ToDoItem` till en enkel typ:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP-utlösare, slå upp ID från frågesträngen - Strängparameter

I följande exempel visas en Java-funktion som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel används för att hämta ett dokument från den angivna databasen och samlingen i strängformulär.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `@CosmosDBInput` om funktionsparametrar vars värde skulle komma från Cosmos DB.  Den här anteckningen kan användas med inbyggda Java-typer, `Optional<T>`POJOs eller nullable värden med .

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP-utlösare, slå upp ID från frågesträngen - POJO-parameter

I följande exempel visas en Java-funktion som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID- och partitionsnyckelvärdet för att slå upp. Det ID- och partitionsnyckelvärdet som används för att hämta ett dokument från den angivna databasen och samlingen. Dokumentet konverteras sedan till en ```ToDoItem``` instans av POJO som tidigare skapats och skickas som ett argument till funktionen.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från vägdata

I följande exempel visas en Java-funktion som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en vägparameter för att ange ID- och partitionsnyckelvärdet för att slå upp. Värdet för ID och partitionsnyckel används för att hämta ett dokument från ```Optional<String>```den angivna databasen och samlingen och returnera det som en .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-utlösare, slå upp ID från vägdata med SqlQuery

I följande exempel visas en Java-funktion som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en vägparameter för att ange id för att slå upp. Det ID:et används för att hämta ett dokument från den ```ToDoItem[]```angivna databasen och samlingen, vilket konverterar resultatuppsättningen till en , eftersom många dokument kan returneras, beroende på frågevillkoren.

> [!NOTE]
> Om du behöver fråga efter bara ID, rekommenderas att använda en slå upp, som [de tidigare exemplen](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), eftersom det kommer att förbruka mindre [begäranheter](../cosmos-db/request-units.md). Punktläsningsåtgärder (GET) är [effektivare](../cosmos-db/optimize-cost-queries.md) än frågor efter ID.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument från vägdata med SqlQuery

I följande exempel visas en Java-funktion som hämtar flera dokument. Funktionen utlöses av en HTTP-begäran som ```desc``` använder en vägparameter ```description``` för att ange vilken sträng som ska sökas efter i fältet . Söktermen används för att hämta en samling dokument från den angivna databasen ```ToDoItem[]``` och samlingen, konvertera resultatuppsättningen till a och skicka den som ett argument till funktionen.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

Attributets konstruktor tar databasnamnet och samlingsnamnet. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns [i följande konfigurationsavsnitt](#configuration).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Använd anteckningen på parametrar som `@CosmosDBOutput` skriver till Cosmos DB från [Java-funktionernas körningsbibliotek.](https://docs.microsoft.com/java/api/overview/azure/functions/runtime) Parametertypen anteckning bör `OutputBinding<T>`vara `T` , där är antingen en inbyggd Java-typ eller en POJO.

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `CosmosDB` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ**     | Saknas | Måste ställas `cosmosDB`in på .        |
|**riktning**     | Saknas | Måste ställas `in`in på .         |
|**Namn**     | Saknas | Namn på den bindningsparameter som representerar dokumentet i funktionen.  |
|**Databasename** |**DatabaseName** |Databasen som innehåller dokumentet.        |
|**collectionName (samlingsnamn)** |**CollectionName** | Namnet på samlingen som innehåller dokumentet. |
|**Id**    | **Id** | ID:et för det dokument som ska hämtas. Den här egenskapen stöder [bindningsuttryck](./functions-bindings-expressions-patterns.md). Ange inte både `id` egenskaperna för och **sqlQuery.** Om du inte anger någon av dem hämtas hela samlingen. |
|**sqlQuery (sqlQuery)**  |**SqlQuery (SqlQuery)**  | En Azure Cosmos DB SQL-fråga som används för att hämta flera dokument. Egenskapen stöder körningsbindningar, som `SELECT * FROM c where c.departmentId = {departmentId}`i det här exemplet: . Ange inte både `id` egenskaper `sqlQuery` och egenskaper. Om du inte anger någon av dem hämtas hela samlingen.|
|**anslutningStringInställning**     |**ConnectionStringSetting**|Namnet på appinställningen som innehåller din Azure Cosmos DB-anslutningssträng. |
|**partitionKey (partitionKey)**|**PartitionKey**|Anger partitionsnyckelvärdet för sökningen. Kan innehålla bindande parametrar. Det krävs för uppslag i [partitionerade](../cosmos-db/partition-data.md#logical-partitions) samlingar.|
|**preferredLocations**| **PreferredLocations**| (Valfritt) Definierar önskade platser (regioner) för geo-replikerade databaskonton i Azure Cosmos DB-tjänsten. Värdena ska kommaavskiljas. Till exempel "Östra USA, Södra centrala USA,Nordeuropa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

När funktionen avslutas sparas alla ändringar som görs i indatadokumentet via namngivna indataparametrar automatiskt.

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

När funktionen avslutas sparas alla ändringar som görs i indatadokumentet via namngivna indataparametrar automatiskt.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Uppdateringar görs inte automatiskt vid funktionsavgång. Använd `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` göra uppdateringar i stället. Se JavaScript-exemplet.

# <a name="python"></a>[Python](#tab/python)

Data görs tillgängliga för funktionen `DocumentList` via en parameter. Ändringar som görs i dokumentet sparas inte automatiskt.

# <a name="java"></a>[Java](#tab/java)

Från [Java-funktionernas körningsbibliotek](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)exponerar anteckningen [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) Cosmos DB-data för funktionen. Den här anteckningen kan användas med inbyggda Java-typer, `Optional<T>`POJOs eller nullable värden med .

---

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion när ett Azure Cosmos DB-dokument skapas eller ändras (utlösare)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Spara ändringar i ett Azure Cosmos DB-dokument (utdatabindning)](./functions-bindings-cosmosdb-v2-output.md)