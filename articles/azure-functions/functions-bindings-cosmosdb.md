---
title: Azure Cosmos DB-bindningar för Functions 1.x
description: Förstå hur du använder Azure Cosmos DB-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 18bbfd1b54947bb88ba8f06c65a17b90430b38a3
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305218"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB-bindningar för Azure Functions 1.x

> [!div class="op_single_selector" title1="Välj den version av Azure Functions runtime som du använder: "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

Den här artikeln förklarar hur du arbetar med [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) -bindningar i Azure Functions. Azure Functions stöder utlösa, indata och utdata-bindningar för Azure Cosmos DB.

> [!NOTE]
> Den här artikeln är för Azure Functions 1.x. Information om hur du använder dessa bindningar i functions 2. x finns i [Azure Cosmos DB bindningar för Azure Functions 2. x](functions-bindings-cosmosdb-v2.md).
>
>Den här bindningen hette ursprungligen DocumentDB. I funktioner version 1.x, endast utlösaren har bytt namn Cosmos DB. den indatabindning och utdatabindning NuGet-paketet kan du behålla DocumentDB-namn.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Azure Cosmos DB-bindningar stöds endast för användning med SQL-API:n. För alla andra Azure Cosmos DB-API: er ska du komma åt databasen från din funktion med hjälp av den statiska klienten för ditt API, inklusive [Azure Cosmos DB s API för MongoDB](../cosmos-db/mongodb-introduction.md), [API för Cassandra](../cosmos-db/cassandra-introduction.md), [Gremlin API](../cosmos-db/graph-introduction.md)och [tabell-API](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Azure Cosmos DB-bindningar för functions version 1. x finns i [Microsoft. Azure. WebJobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet-paketet, version 1. x. Käll koden för bindningarna finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Utlösare

Azure Cosmos DB-utlösaren använder [Azure Cosmos DB ändra feed](../cosmos-db/change-feed.md) för att lyssna efter infogningar och uppdateringar över partitioner. Ändringsflöde publicerar infogningar och uppdateringar, inte borttagningar.

## <a name="trigger---example"></a>Utlösare - exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som anropas när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en Cosmos DB trigger-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster har ändrats.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är C#-skriptkoden:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Cosmos DB trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster har ändrats.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är JavaScript-kod:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Utlösare - attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd attributet [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktorn tar databasens namn och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [trigger-Configuration](#trigger---configuration). Här är ett exempel på ett `CosmosDBTrigger`-attribut i en metodsignatur:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Ett fullständigt exempel finns i [Utlös ande- C# exempel](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

---

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `CosmosDBTrigger`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** || Måste anges till `cosmosDBTrigger`. |
|**riktning** || Måste anges till `in`. Den här parametern anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** || Variabelnamnet som används i Funktionskoden som representerar en lista över dokument med ändringar. |
|**connectionStringSetting**|**ConnectionStringSetting** | Namnet på en appinställning som innehåller anslutningssträngen som används för att ansluta till Azure Cosmos DB-kontot som övervakas. |
|**Databas**|**Databas**  | Namnet på Azure Cosmos DB-databasen med den samling som övervakas. |
|**Samling** |**Samling** | Namnet på samlingen som övervakas. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Valfritt) Namnet på en appinställning som innehåller anslutningssträngen till den tjänst som innehar lånet samlingen. Om värdet inte anges används `connectionStringSetting` svärdet. Den här parametern anges automatiskt när bindningen skapas i portalen. Anslutningssträngen för lånsamlingen måste ha skrivbehörighet.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Valfritt) Namnet på den databas som innehåller den samling som används för att lagra lån. När inställningen inte anges används värdet för `databaseName`-inställningen. Den här parametern anges automatiskt när bindningen skapas i portalen. |
|**leaseCollectionName** | **LeaseCollectionName** | (Valfritt) Namnet på den samling som används för att lagra lån. När den inte anges används värdet `leases`. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Valfritt När värdet är `true`skapas låne samlingen automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Valfritt) Definierar mängd Begäransenheter för att tilldela när lånsamlingen skapas. Den här inställningen används bara när `createLeaseCollectionIfNotExists` är inställt på `true`. Den här parametern anges automatiskt när bindningen skapas med hjälp av portalen.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Valfritt) När värdet den lägger till ett prefix till lån som skapats i lånsamling för den här funktionen så effektivt att två separata Azure-Functions för att dela samma lånsamling med hjälp av olika prefix.
|**feedPollDelay**| **FeedPollDelay**| (Valfritt) När mängd, den definierar, i millisekunder, fördröjning mellan att avsöka en partition för nya ändringar på flödet, är när alla aktuella ändringar tömda. Standardvärdet är 5 000 (5 sekunder).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Valfritt) När värdet definierar den, i millisekunder, intervallet sätta igång en uppgift att beräkna om partitioner fördelas jämnt mellan kända ha instanser. Standardvärdet är 13000 (13 sekunder).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Valfritt) När värdet definierar den, i millisekunder, intervallet som fattas lånet om ett lån som representerar en partition. Om lånet inte förnyas inom intervallet, det gör att det upphör att gälla och ägarskap för partitionen flyttas till en annan instans. Standardvärdet är 60000 (60 sekunder).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Valfritt) När värdet definierar den, i millisekunder, förnyelseintervallet för alla lån för partitioner som för tillfället hålls av en instans. Standardvärdet är 17000 (17 sekunder).
|**checkpointFrequency**| **CheckpointFrequency**| (Valfritt) När värdet definierar den, i millisekunder, hur många lån kontrollpunkter. Standardvärdet är alltid efter varje funktions anrop.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Valfritt) När värdet anpassar det den maximala mängden objekt tas emot per funktionsanrop.
|**startFromBeginning**| **StartFromBeginning**| Valfritt När det är inställt, instrueras utlösaren att börja läsa ändringar från början av samlingens historik i stället för den aktuella tiden. Detta fungerar bara första gången utlösaren startar, precis som vid efterföljande körningar, så är kontroll punkterna redan lagrade. Om du anger detta till `true` när det redan har skapats lån har ingen påverkan.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna. Både den samling som övervakas och den samling som innehåller lån måste vara tillgänglig för utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner har kon figurer ATS för att använda en Cosmos DB-utlösare för samma samling, ska varje funktion använda en dedikerad Lease-samling eller ange en annan `LeaseCollectionPrefix` för varje funktion. Annars kan aktiveras endast en av funktionerna. Information om prefixet finns i [avsnittet konfiguration](#trigger---configuration).

Utlösaren anger inte om ett dokument har uppdateras eller infogas, den bara innehåller själva dokumentet. Om du vill hantera uppdateringar och infogningar på olika sätt, kan du göra det genom att implementera värdefält för infogning eller uppdaterar.

## <a name="input"></a>Indata

Azure Cosmos DB-indatabindning använder SQL-API för att hämta en eller flera Azure Cosmos DB-dokument och skickar dem till Indataparametern för funktionen. Dokument-ID eller frågeparametrar kan fastställas baserat på utlösare som anropar funktionen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* [Köa utlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-utlösare, slå upp ID från routa data med SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-utlösare, hämta flera dokument med SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-utlösare, hämta flera dokument med DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Exemplen refererar till en enkel `ToDoItem`s typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köa utlösare, slå upp ID från JSON

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av ett kömeddelande som innehåller ett JSON-objekt. Queue-utlösaren parsar JSON till ett objekt med namnet `ToDoItemLookup`, som innehåller det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder dirigera data för att ange ID för att leta upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Hoppa över inmatade exempel](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-utlösare, slå upp ID från routa data med SqlQuery

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder dirigera data för att ange ID för att leta upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Hoppa över inmatade exempel](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Frågan anges i egenskapen `SqlQuery` attribut.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Hoppa över inmatade exempel](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP utlösa får flera docs, med hjälp av DocumentClient (C#)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. I koden används en `DocumentClient`-instans från Azure Cosmos DB-bindningen för att läsa en lista över dokument. `DocumentClient`-instansen kan också användas för Skriv åtgärder.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* [Köa utlösare, leta upp ID från sträng](#queue-trigger-look-up-id-from-string-c-script)
* [Köa utlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-utlösare, hämta flera dokument med SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, hämta flera dokument med DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Exempel på HTTP-utlösare hänvisar till en enkel `ToDoItem`s typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Köa utlösare, leta upp ID från sträng

I följande exempel visas en Cosmos DB-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skriptkoden:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
        inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köa utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga som använder en kö-utlösare för att anpassa Frågeparametrar.

Utlösaren för kön innehåller en parameter `departmentId`. Ett köat meddelande i `{ "departmentId" : "Finance" }` skulle returnera alla poster för ekonomi avdelningen.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skriptkoden:

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [ C# skript funktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

Här är *Function. JSON* -filen:

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Här är C#-skriptkoden:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [ C# skript funktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder dirigera data för att ange ID för att leta upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

Här är *Function. JSON* -filen:

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Här är C#-skriptkoden:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [ C# skript funktion](functions-reference-csharp.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Frågan anges i egenskapen `SqlQuery` attribut.

Här är *Function. JSON* -filen:

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
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Här är C#-skriptkoden:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-utlösare, hämta flera dokument med DocumentClient

I följande exempel visas en [ C# skript funktion](functions-reference-csharp.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. I koden används en `DocumentClient`-instans från Azure Cosmos DB-bindningen för att läsa en lista över dokument. `DocumentClient`-instansen kan också användas för Skriv åtgärder.

Här är *Function. JSON* -filen:

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
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Här är C#-skriptkoden:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Det här avsnittet innehåller följande exempel:

* [Köa utlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-javascript)
* [Köa utlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)


<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köa utlösare, slå upp ID från JSON

I följande exempel visas en Cosmos DB-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-kod:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

Här är *Function. JSON* -filen:

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Här är JavaScript-kod:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

Här är *Function. JSON* -filen:

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Här är JavaScript-kod:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köa utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga som använder en kö-utlösare för att anpassa Frågeparametrar.

Utlösaren för kön innehåller en parameter `departmentId`. Ett köat meddelande i `{ "departmentId" : "Finance" }` skulle returnera alla poster för ekonomi avdelningen.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-kod:

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

---

## <a name="input---attributes"></a>Indata - attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd attributet [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktorn tar databasens namn och samlingens namn. Information om de inställningar och andra egenskaper som du kan konfigurera finns i [följande konfigurations avsnitt](#input---configuration).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

---

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `DocumentDB`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**     || Måste anges till `documentdb`.        |
|**riktning**     || Måste anges till `in`.         |
|**Namn**     || Namnet på bindningsparametern som representerar dokumentet i funktionen.  |
|**Databas** |**Databas** |Den databas som innehåller dokumentet.        |
|**Samling** |**Samling** | Namnet på den samling som innehåller dokumentet. |
|**identitet**    | **Id** | ID för dokumentet som ska hämtas. Den här egenskapen stöder [bindnings uttryck](./functions-bindings-expressions-patterns.md). Ange inte egenskaperna för både **ID** och **sqlQuery** . Om du inte anger någon hämtas hela samlingen. |
|**sqlQuery**  |**SqlQuery**  | En Azure Cosmos DB SQL-fråga som används för att hämta flera dokument. Egenskapen stöder runtime-bindningar, som i det här exemplet: `SELECT * FROM c where c.departmentId = {departmentId}`. Ange inte egenskaperna för både **ID** och **sqlQuery** . Om du inte anger någon hämtas hela samlingen.|
|**anslutningen**     |**ConnectionStringSetting**|Namnet på den appinställning som innehåller din Azure Cosmos DB-anslutningssträng.        |
|**partitionKey**|**PartitionKey**|Anger partitionsnyckelvärdet för sökningen. Omfatta bindande parametrar.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

När funktionen avslutas sparas alla ändringar som gjorts i indatamängds dokumentet via namngivna indataparametrar automatiskt.

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

När funktionen avslutas sparas alla ändringar som gjorts i indatamängds dokumentet via namngivna indataparametrar automatiskt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Uppdateringar görs inte automatiskt när funktionen avslutas. Använd i stället `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` för att göra uppdateringar. Se [exemplet på indatamängden](#input).

---

## <a name="output"></a>Resultat

I Azure Cosmos DB-utdatabindning kan skriva du ett nytt dokument till en Azure Cosmos DB-databas med hjälp av SQL-API.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* Köutlösare, Skriv ett dokument
* Köa utlösare, skriv dokument med `IAsyncCollector`

Exemplen refererar till en enkel `ToDoItem`s typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Köutlösare, Skriv ett dokument

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som lägger till ett dokument i en databas med hjälp av data som anges i meddelande från Queue Storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köutlösare, Skriv docs med IAsyncCollector

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som lägger till en samling dokument i en databas med hjälp av data som finns i ett meddelande-JSON för köer.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* Köutlösare, Skriv ett dokument
* Köa utlösare, skriv dokument med `IAsyncCollector`

### <a name="queue-trigger-write-one-doc"></a>Köutlösare, Skriv ett dokument

I följande exempel visas en Azure Cosmos DB utgående bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en indatabindning i kö för en kö som tar emot JSON i följande format:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funktionen skapar Azure Cosmos DB-dokument i följande format för varje post:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skriptkoden:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köutlösare, Skriv docs med IAsyncCollector

Om du vill skapa flera dokument kan du binda till `ICollector<T>` eller `IAsyncCollector<T>` där `T` är en av de typer som stöds.

Det här exemplet refererar till en enkel `ToDoItem`s typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Här är filen function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Här är C#-skriptkoden:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Azure Cosmos DB utgående bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en indatabindning i kö för en kö som tar emot JSON i följande format:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funktionen skapar Azure Cosmos DB-dokument i följande format för varje post:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-kod:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

---

## <a name="output---attributes"></a>Utdata - attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd attributet [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktorn tar databasens namn och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utdata-konfiguration](#output---configuration). Här är ett exempel på ett `DocumentDB`-attribut i en metodsignatur:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Ett fullständigt exempel finns i [utdata](#output).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

---

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `DocumentDB`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**     || Måste anges till `documentdb`.        |
|**riktning**     || Måste anges till `out`.         |
|**Namn**     || Namnet på bindningsparametern som representerar dokumentet i funktionen.  |
|**Databas** | **Databas**|Den databas som innehåller den samling där dokumentet skapas.     |
|**Samling** |**Samling**  | Namnet på den samling där dokumentet skapas. |
|**createIfNotExists**  |**CreateIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när den inte finns. Standardvärdet är *false* eftersom nya samlingar skapas med reserverat data flöde, vilket innebär kostnads konsekvenser. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |När `CreateIfNotExists` är true definierar partitionens nyckel Sök väg för den skapade samlingen.|
|**collectionThroughput**|**CollectionThroughput**| När `CreateIfNotExists` är true definierar [data flödet](../cosmos-db/set-throughput.md) för den skapade samlingen.|
|**anslutningen**    |**ConnectionStringSetting** |Namnet på den appinställning som innehåller din Azure Cosmos DB-anslutningssträng.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

När du skriver till Utdataparametern i din funktion skapas ett dokument i databasen. Det här dokumentet har en automatiskt genererad GUID som dokument-ID Du kan ange dokument-ID: t för utdatafilen genom att ange egenskapen `id` i JSON-objektet som skickas till Utdataparametern.

> [!Note]
> När du anger ID för ett befintligt dokument, hämtar den skrivs över av det nya utdatadokumentet.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| CosmosDB | [Felkoder för CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om databas bearbetning utan server med Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
