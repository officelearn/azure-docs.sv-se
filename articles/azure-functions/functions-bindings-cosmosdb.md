---
title: Azure Cosmos DB-bindningar för funktioner 1.x
description: Förstå hur du använder Azure Cosmos DB-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: e30b256d9fa43402c3b2c444aa1a0e0dc16cfdcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277549"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB-bindningar för Azure Functions 1.x

> [!div class="op_single_selector" title1="Välj den version av Azure Functions-körningen som du använder: "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

I den här artikeln beskrivs hur du arbetar med [Azure Cosmos](../cosmos-db/serverless-computing-database.md) DB-bindningar i Azure Functions. Azure Functions stöder utlösande, indata och utdatabindningar för Azure Cosmos DB.

> [!NOTE]
> Den här artikeln är avsedd för Azure Functions 1.x. Information om hur du använder dessa bindningar i Functions 2.x och senare finns i [Azure Cosmos DB-bindningar för Azure Functions 2.x](functions-bindings-cosmosdb-v2.md).
>
>Den här bindningen hette ursprungligen DocumentDB. I Functions version 1.x har endast utlösaren bytt namn till Cosmos DB. Indatabindningen, utdatabindningen och NuGet-paketet behåller DocumentDB-namnet.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Azure Cosmos DB-bindningar stöds endast för användning med SQL-API:n. För alla andra Azure Cosmos DB-API:er bör du komma åt databasen från din funktion med hjälp av den statiska klienten för ditt API, inklusive [Azure Cosmos DB:s API för MongoDB,](../cosmos-db/mongodb-introduction.md) [Cassandra API](../cosmos-db/cassandra-introduction.md), [Gremlin API](../cosmos-db/graph-introduction.md)och Table [API](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Paket - Funktioner 1.x

Azure Cosmos DB-bindningar för Funktioner version 1.x finns i [Paketet Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet, version 1.x. Källkoden för bindningarna finns i [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) GitHub-databasen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Utlösare

Azure Cosmos DB-utlösaren använder [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) för att lyssna efter infogningar och uppdateringar över partitioner. Ändringsflödet publicerar infogningar och uppdateringar, inte borttagningar.

## <a name="trigger---example"></a>Utlösare - exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som anropas när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en Cosmos DB-utlösare som binder i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster ändras.

Här är bindningsdata i *filen function.json:*

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

Här är C# skriptkoden:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en Cosmos DB-utlösare som binder i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster ändras.

Här är bindningsdata i *filen function.json:*

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

Här är JavaScript-koden:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Utlösare - attribut

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

Attributets konstruktor tar databasnamnet och samlingsnamnet. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [Utlösare - konfiguration](#trigger---configuration). Här är `CosmosDBTrigger` ett attributexempel i en metodsignatur:

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

Ett fullständigt exempel finns i [Utlösare - C#exempel](#trigger).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

---

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `CosmosDBTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas `cosmosDBTrigger`in på . |
|**riktning** | Saknas | Måste ställas `in`in på . Den här parametern ställs in automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i funktionskoden och som representerar listan över dokument med ändringar. |
|**anslutningStringInställning**|**ConnectionStringSetting** | Namnet på en appinställning som innehåller anslutningssträngen som används för att ansluta till Azure Cosmos DB-kontot som övervakas. |
|**Databasename**|**DatabaseName**  | Namnet på Azure Cosmos DB-databasen med samlingen som övervakas. |
|**collectionName (samlingsnamn)** |**CollectionName** | Namnet på den samling som övervakas. |
|**leConnectionStringSetting** | **LeaseConnectionStringSetting** | (Valfritt) Namnet på en appinställning som innehåller anslutningssträngen till tjänsten som innehåller lånesamlingen. När värdet inte `connectionStringSetting` har angetts används det. Den här parametern ställs in automatiskt när bindningen skapas i portalen. Anslutningssträngen för lånesamlingen måste ha skrivbehörighet.|
|**leasaDatabaseName** |**LeaseDatabaseName** | (Valfritt) Namnet på databasen som innehåller samlingen som används för att lagra lån. När inställningen inte har `databaseName` ställts in används värdet för inställningen. Den här parametern ställs in automatiskt när bindningen skapas i portalen. |
|**leaseCollectionName** | **LeaseCollectionName** | (Valfritt) Namnet på den samling som används för att lagra leasingavtal. När värdet inte `leases` har angetts används det. |
|**skapaLeaseCollectionIfNotExists** | **SkapaLeaseCollectionIfNotExists** | (Valfritt) När den `true`är inställd på skapas lånesamlingen automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasingerCollectionThroughput**| **LeasingUppsamlingGenomgenomströmning**| (Valfritt) Definierar mängden begärandeenheter som ska tilldelas när lånesamlingen skapas. Den här inställningen `createLeaseCollectionIfNotExists` används bara `true`När är inställd på . Den här parametern ställs in automatiskt när bindningen skapas med hjälp av portalen.
|**leCollectionPrefix**| **LeaseCollectionPrefix**| (Valfritt) När den är inställd läggs ett prefix till de lån som skapats i lånesamlingen för den här funktionen, vilket i praktiken gör att två separata Azure-funktioner kan dela samma Lånesamling med hjälp av olika prefix.
|**feedPollDelay**| **FeedPollDelay**| (Valfritt) När den är inställd definierar den i millisekunder fördröjningen mellan avsökningen av en partition för nya ändringar i flödet, när alla aktuella ändringar har tömts. Standard är 5000 (5 sekunder).
|**lendeRättInterval**| **LeaseAcquireInterval**| (Valfritt) När den är inställd definierar den i millisekunder intervallet för att starta en uppgift för att beräkna om partitioner fördelas jämnt mellan kända värdinstanser. Standard är 13000 (13 sekunder).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Valfritt) När den är inställd definierar den i millisekunder det intervall för vilket lånet tas på ett lån som representerar en partition. Om lånet inte förnyas inom det här intervallet kommer det att leda till att det upphör att gälla och ägarskapet av partitionen flyttas till en annan instans. Standard är 60000 (60 sekunder).
|**leasingRenewInterval**| **LeaseRenewInterval**| (Valfritt) När den är inställd definierar den i millisekunder förnyelseintervallet för alla lån för partitioner som för närvarande innehas av en instans. Standard är 17000 (17 sekunder).
|**checkpointFrequency**| **KontrollpunktFrekvent**| (Valfritt) När den är inställd definierar den i millisekunder intervallet mellan lånekontrollpunkter. Standard är alltid efter varje funktionsanrop.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Valfritt) När den är inställd anpassas det maximala antalet objekt som tas emot per funktionsanrop.
|**startFrånBeginning**| **StartFrånBesning**| (Valfritt) När den är inställd talar den om att utlösaren ska börja läsa ändringar från början av samlingens historik i stället för den aktuella tiden. Detta fungerar bara första gången utlösaren startar, som i efterföljande körningar, är kontrollpunkterna redan lagrade. Om du `true` ställer in detta på när det redan har skapats lån har ingen effekt.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

Utlösaren kräver en andra samling som den använder för att lagra _lån_ över partitionerna. Både samlingen som övervakas och samlingen som innehåller lånen måste vara tillgängliga för att utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner är konfigurerade för att använda en Cosmos DB-utlösare för samma samling, `LeaseCollectionPrefix` bör var och en av funktionerna använda en dedikerad lånesamling eller ange en annan för varje funktion. Annars utlöses bara en av funktionerna. Information om prefixet finns i [avsnittet Konfiguration](#trigger---configuration).

Utlösaren anger inte om ett dokument har uppdaterats eller infogats, utan själva dokumentet. Om du behöver hantera uppdateringar och infogar olika kan du göra det genom att implementera tidsstämpelfält för insättning eller uppdatering.

## <a name="input"></a>Indata

Azure Cosmos DB-indatabindningen använder SQL API för att hämta en eller flera Azure Cosmos DB-dokument och skickar dem till indataparametern för funktionen. Dokument-ID:t och frågeparametrarna kan fastställas baserat på den utlösare som anropar funktionen.

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

### <a name="queue-trigger-look-up-id-from-json"></a>Köutlösare, slå upp ID från JSON

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enda dokument. Funktionen utlöses av ett kömeddelande som innehåller ett JSON-objekt. Köutlösaren tolkar JSON till `ToDoItemLookup`ett objekt med namnet , som innehåller ID:t för att slå upp. Det ID:t används `ToDoItem` för att hämta ett dokument från den angivna databasen och samlingen.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, slå upp ID från frågesträngen

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange vilket ID som ska sökas upp. Det ID:t används `ToDoItem` för att hämta ett dokument från den angivna databasen och samlingen.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från vägdata

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder vägdata för att ange id för att slå upp. Det ID:t används `ToDoItem` för att hämta ett dokument från den angivna databasen och samlingen.

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

[Hoppa över indataexempel](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-utlösare, slå upp ID från vägdata med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder vägdata för att ange id för att slå upp. Det ID:t används `ToDoItem` för att hämta ett dokument från den angivna databasen och samlingen.

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

[Hoppa över indataexempel](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Frågan anges i attributegenskapen. `SqlQuery`

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

[Hoppa över indataexempel](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-utlösare, hämta flera dokument med DocumentClient (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Koden använder `DocumentClient` en instans som tillhandahålls av Azure Cosmos DB-bindningen för att läsa en lista över dokument. Instansen `DocumentClient` kan också användas för skrivåtgärder.

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

### <a name="queue-trigger-look-up-id-from-string"></a>Köutlösare, slå upp ID från sträng

I följande exempel visas en Cosmos DB-indatabindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets textvärde.

Här är bindningsdata i *filen function.json:*

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

[Konfigurationsavsnittet](#input---configuration) förklarar dessa egenskaper.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Konfigurationsavsnittet](#input---configuration) förklarar dessa egenskaper.

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

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange vilket ID som ska sökas upp. Det ID:t används `ToDoItem` för att hämta ett dokument från den angivna databasen och samlingen.

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

Här är C# skriptkoden:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från vägdata

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder vägdata för att ange id för att slå upp. Det ID:t används `ToDoItem` för att hämta ett dokument från den angivna databasen och samlingen.

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

Här är C# skriptkoden:

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

Här är C# skriptkoden:

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

Här är C# skriptkoden:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Det här avsnittet innehåller följande exempel:

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

[Konfigurationsavsnittet](#input---configuration) förklarar dessa egenskaper.

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

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange vilket ID som ska sökas upp. Det ID:t används `ToDoItem` för att hämta ett dokument från den angivna databasen och samlingen.

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

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enda dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange vilket ID som ska sökas upp. Det ID:t används `ToDoItem` för att hämta ett dokument från den angivna databasen och samlingen.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Konfigurationsavsnittet](#input---configuration) förklarar dessa egenskaper.

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

---

## <a name="input---attributes"></a>Indata - attribut

# <a name="c"></a>[C#](#tab/csharp)

I [klassbiblioteken C#](functions-dotnet-class-library.md)använder du attributet [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

Attributets konstruktor tar databasnamnet och samlingsnamnet. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns [i följande konfigurationsavsnitt](#input---configuration).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

---

## <a name="input---configuration"></a>Ingång - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `DocumentDB` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ**     | Saknas | Måste ställas `documentdb`in på .        |
|**riktning**     | Saknas | Måste ställas `in`in på .         |
|**Namn**     | Saknas | Namn på den bindningsparameter som representerar dokumentet i funktionen.  |
|**Databasename** |**DatabaseName** |Databasen som innehåller dokumentet.        |
|**collectionName (samlingsnamn)** |**CollectionName** | Namnet på samlingen som innehåller dokumentet. |
|**Id**    | **Id** | ID:et för det dokument som ska hämtas. Den här egenskapen stöder [bindningsuttryck](./functions-bindings-expressions-patterns.md). Ange inte egenskaperna för både **id** och **sqlQuery.** Om du inte anger någon av dem hämtas hela samlingen. |
|**sqlQuery (sqlQuery)**  |**SqlQuery (SqlQuery)**  | En Azure Cosmos DB SQL-fråga som används för att hämta flera dokument. Egenskapen stöder körningsbindningar, som `SELECT * FROM c where c.departmentId = {departmentId}`i det här exemplet: . Ange inte egenskaperna för både **id** och **sqlQuery.** Om du inte anger någon av dem hämtas hela samlingen.|
|**Anslutning**     |**ConnectionStringSetting**|Namnet på appinställningen som innehåller din Azure Cosmos DB-anslutningssträng.        |
|**partitionKey (partitionKey)**|**PartitionKey**|Anger partitionsnyckelvärdet för sökningen. Kan innehålla bindande parametrar.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Inmatning - användning

# <a name="c"></a>[C#](#tab/csharp)

När funktionen avslutas sparas alla ändringar som görs i indatadokumentet via namngivna indataparametrar automatiskt.

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

När funktionen avslutas sparas alla ändringar som görs i indatadokumentet via namngivna indataparametrar automatiskt.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Uppdateringar görs inte automatiskt vid funktionsavgång. Använd `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` göra uppdateringar i stället. Se [indataexemplet](#input).

---

## <a name="output"></a>Resultat

Med Azure Cosmos DB-utdatabindningen kan du skriva ett nytt dokument till en Azure Cosmos DB-databas med SQL API.

# <a name="c"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* Köutlösare, skriv ett dokument
* Köutlösare, skriva dokument med`IAsyncCollector`

Exemplen refererar `ToDoItem` till en enkel typ:

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

### <a name="queue-trigger-write-one-doc"></a>Köutlösare, skriv ett dokument

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till ett dokument i en databas med hjälp av data som tillhandahålls i meddelandet från kölagring.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köutlösare, skriva dokument med IAsyncCollector

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till en samling dokument i en databas med hjälp av data som tillhandahålls i ett kömeddelande JSON.

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* Köutlösare, skriv ett dokument
* Köutlösare, skriva dokument med`IAsyncCollector`

### <a name="queue-trigger-write-one-doc"></a>Köutlösare, skriv ett dokument

I följande exempel visas en Azure Cosmos DB-utdatabindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en köindatabindning för en kö som tar emot JSON i följande format:

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

Här är bindningsdata i *filen function.json:*

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

[Konfigurationsavsnittet](#output---configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köutlösare, skriva dokument med IAsyncCollector

Om du vill skapa flera `ICollector<T>` `IAsyncCollector<T>` dokument `T` kan du binda till eller var är en av de typer som stöds.

Det här exemplet refererar till en enkel `ToDoItem` typ:

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

Här är C# skriptkoden:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en Azure Cosmos DB-utdatabindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en köindatabindning för en kö som tar emot JSON i följande format:

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

Här är bindningsdata i *filen function.json:*

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

[Konfigurationsavsnittet](#output---configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

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

# <a name="c"></a>[C#](#tab/csharp)

I [klassbiblioteken C#](functions-dotnet-class-library.md)använder du attributet [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

Attributets konstruktor tar databasnamnet och samlingsnamnet. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [Utdata - konfiguration](#output---configuration). Här är `DocumentDB` ett attributexempel i en metodsignatur:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Ett fullständigt exempel finns i [Utdata](#output).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

---

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `DocumentDB` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ**     | Saknas | Måste ställas `documentdb`in på .        |
|**riktning**     | Saknas | Måste ställas `out`in på .         |
|**Namn**     | Saknas | Namn på den bindningsparameter som representerar dokumentet i funktionen.  |
|**Databasename** | **DatabaseName**|Databasen som innehåller samlingen där dokumentet skapas.     |
|**collectionName (samlingsnamn)** |**CollectionName**  | Namnet på den samling där dokumentet skapas. |
|**createIfNotExists**  |**CreateIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när den inte finns. Standardvärdet är *falskt* eftersom nya samlingar skapas med reserverat dataflöde, vilket har kostnadskonsekvenser. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey (partitionKey)**|**PartitionKey** |När `CreateIfNotExists` är sant definierar du sökvägen till partitionsnyckeln för den skapade samlingen.|
|**insamlingGenomströmning**|**InsamlingGenomströmning**| När `CreateIfNotExists` är sant definierar du [dataflödet](../cosmos-db/set-throughput.md) för den skapade samlingen.|
|**Anslutning**    |**ConnectionStringSetting** |Namnet på appinställningen som innehåller din Azure Cosmos DB-anslutningssträng.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

När du skriver till utdataparametern i funktionen skapas som standard ett dokument i databasen. Det här dokumentet har ett automatiskt genererat GUID som dokument-ID. Du kan ange dokument-ID för utdatadokumentet genom att ange egenskapen `id` i JSON-objektet som skickas till utdataparametern.

> [!Note]
> När du anger ID för ett befintligt dokument skrivs det över av det nya utdatadokumentet.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| CosmosDB | [CosmosDB-felkoder](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Nästa steg

* [Läs mer om serverlös databasdatoranvändning med Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
