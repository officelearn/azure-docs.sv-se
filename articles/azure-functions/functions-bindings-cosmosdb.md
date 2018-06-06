---
title: Azure DB Cosmos-bindningar för funktioner 1.x
description: Förstå hur du använder Azure Cosmos DB utlösare och bindningar i Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: fb9e6eaca31849eb8ef15714978c1ec55b23e02f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796935"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure DB Cosmos-bindningar för Azure Functions 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Version 1 – allmänt tillgänglig](functions-bindings-cosmosdb.md)
> * [Version 2 – förhandsversion](functions-bindings-cosmosdb-v2.md)

Den här artikeln förklarar hur du arbetar med [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) bindningar i Azure Functions. Azure Functions stöder utlösa indata och utdata bindningar för Azure Cosmos DB.

> [!NOTE]
> Den här artikeln är Azure Functions 1.x.  Information om hur du använder dessa bindningar i funktioner 2.x finns [Azure Cosmos DB bindningar för Azure Functions 2.x](functions-bindings-cosmosdb-v2.md).
>
>Den här bindningen hette ursprungligen DocumentDB. I funktion version 1.x, endast utlösaren har bytt namn Cosmos DB. behålla DocumentDB namnet indatabindning, utdata bindning och NuGet-paketet.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - fungerar 1.x

Azure DB som Cosmos-bindningar för funktioner version 1.x finns i den [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet-paketet version 1.x. Källkoden för bindningar finns i den [azure-webjobs-sdk-tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Utlösare

Azure Cosmos DB utlösaren använder den [Azure Cosmos DB ändra Feed](../cosmos-db/change-feed.md) att lyssna efter infogningar och uppdateringar över partitioner. Ändra feeden publicerar infogningar och uppdateringar, inte borttagningar.

## <a name="trigger---example"></a>Utlösaren - exempel

Finns i det språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

[Hoppa över utlösaren exempel](#trigger---attributes)

### <a name="trigger---c-example"></a>Utlösaren - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som anropas när det finns infogas eller uppdateras i den angivna databasen och samlingen.

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

[Hoppa över utlösaren exempel](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Utlösaren - exempel på C#-skript

I följande exempel visas en Cosmos-DB-utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB poster har ändrats.

Här är de bindande data den *function.json* fil:

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

Här är skriptkod C#:
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

[Hoppa över utlösaren exempel](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Utlösaren - JavaScript-exempel

I följande exempel visas en Cosmos-DB-utlösare bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB poster har ändrats.

Här är de bindande data den *function.json* fil:

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

## <a name="trigger---attributes"></a>Utlösaren - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribut.

Attributets konstruktorn har databasnamnet och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utlösaren - konfiguration](#trigger---configuration). Här är en `CosmosDBTrigger` attributet exempel i en signatur:

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

En komplett exempel finns [utlösaren - C#-exempel](#trigger---c-example).

## <a name="trigger---configuration"></a>Utlösaren - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `CosmosDBTrigger` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** || måste anges till `cosmosDBTrigger`. |
|**riktning** || måste anges till `in`. Den här parametern anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** || Variabelnamnet som används i Funktionskoden som representerar en lista över dokument med ändringar. | 
|**ConnectionStringSetting**|**ConnectionStringSetting** | Namnet på en appinställning som innehåller den anslutningssträng som används för att ansluta till Azure DB som Cosmos-kontot som övervakas. |
|**DatabaseName**|**DatabaseName**  | Namnet på Azure DB som Cosmos-databasen med den samling som övervakas. |
|**Samlingsnamn** |**Samlingsnamn** | Namnet på samlingen som övervakas. |
|**LeaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Valfritt) Namnet på en appinställning som innehåller anslutningssträngen till tjänsten som innehåller lease-samling. När inte har angetts i `connectionStringSetting` värdet används. Den här parametern anges automatiskt när bindning skapas i portalen. Anslutningssträngen för samlingen lån måste ha skrivbehörighet.|
|**LeaseDatabaseName** |**LeaseDatabaseName** | (Valfritt) Namnet på databasen som innehåller den samling som används för att lagra lån. Om inte värdet, för den `databaseName` inställningen används. Den här parametern anges automatiskt när bindning skapas i portalen. |
|**LeaseCollectionName** | **LeaseCollectionName** | (Valfritt) Namnet på den samling som används för att lagra lån. Om värdet inte, `leases` används. |
|**CreateLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Valfritt) Om värdet är `true`, samlingen lån skapas automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**LeasesCollectionThroughput**| **LeasesCollectionThroughput**| (Valfritt) Definierar mängden begära enheter tilldelas när samlingen lån skapas. Den här inställningen är endast användas när `createLeaseCollectionIfNotExists` är inställd på `true`. Den här parametern anges automatiskt när bindning har skapats med hjälp av portalen.
|**LeaseCollectionPrefix**| **LeaseCollectionPrefix**| (Valfritt) När inställningen läggs ett prefix till lån som skapats i samlingen lån för den här funktionen så effektivt att två separata Azure-funktioner att dela samma lån samling genom att använda olika prefix.
|**FeedPollDelay**| **FeedPollDelay**| (Valfritt) När alla aktuella ändringar är att ta slut när mängd definierar, i millisekunder, fördröjning mellan avsöker en partition för nya ändringar i denna feed. Standardvärdet är 5000 (5 sekunder).
|**LeaseAcquireInterval**| **LeaseAcquireInterval**| (Valfritt) När inställningen definierar, i millisekunder intervallet som startar en aktivitet för att beräkna om partitioner fördelas jämnt mellan kända värddatorinstanser. Standardvärdet är 13000 (13 sekunder).
|**LeaseExpirationInterval**| **LeaseExpirationInterval**| (Valfritt) När inställningen definierar, i millisekunder det intervall som lånet utförs på ett lån som representerar en partition. Om lånet inte förnyas inom intervallet, kommer den att gälla och ägare för partitionen flyttas till en annan instans. Standardvärdet är 60000 (60 sekunder).
|**LeaseRenewInterval**| **LeaseRenewInterval**| (Valfritt) När inställningen definierar, i millisekunder, förnyelseintervall för alla lån för partitioner som för tillfället hålls av en instans. Standardvärdet är 17000 (17 sekunder).
|**CheckpointFrequency**| **CheckpointFrequency**| (Valfritt) När inställningen definierar, i millisekunder, intervallet mellan lån kontrollpunkter. Standardvärdet är alltid efter en lyckad funktionsanrop.
|**MaxItemsPerInvocation**| **MaxItemsPerInvocation**| (Valfritt) När inställningen anpassar den den maximala mängden objekt tas emot varje funktionsanrop.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösaren - användning

Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna. Både den samling som övervakas och den samling som innehåller lån måste vara tillgänglig för utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner har konfigurerats för att använda en Cosmos-DB-utlösare för samma samling, varje funktion ska använda en dedikerad lease-samling eller ange ett annat `LeaseCollectionPrefix` för varje funktion. Annars utlöses endast en av funktionerna. Information om prefixet som finns i [konfigurationsavsnittet](#trigger---configuration).

Utlösaren anger inte om ett dokument har uppdateras eller infogas, bara innehåller själva dokumentet. Om du behöver hantera uppdateringar och infogningar på olika sätt kan du göra det genom att implementera värdefält för infogning eller update.

## <a name="input"></a>Indata

Azure DB som Cosmos-indatabindning hämtar ett eller flera Azure Cosmos DB dokument och skickar dem till Indataparametern för funktionen. Parametrarna för dokument-ID eller fråga kan bestämmas utifrån utlösaren som anropar funktionen. 

>[!NOTE]
> Inte använder Azure Cosmos DB indata eller utdata bindningar om du använder MongoDB-API för en Cosmos-DB-konto. Skadade data är möjligt.

## <a name="input---examples"></a>Indata - exempel

Se exempel språkspecifika som läser ett enskilt dokument genom att ange ett ID-värde:

* [C#](#input---c-examples)
* [C#-skript (.csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)

[Hoppa över inkommande exempel](#input---attributes)

### <a name="input---c-examples"></a>Indata - C#-exempel

Det här avsnittet innehåller följande exempel:

* [Kön utlösning, leta upp ID från JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-utlösare, leta upp ID från vidarebefordra data](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-utlösare, leta upp ID från vidarebefordra data med hjälp av SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP Utlös, hämta flera, med hjälp av SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP Utlös, hämta flera, med DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Exemplen finns i en enkel `ToDoItem` typ:

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Kön utlösning, leta upp ID från JSON (C#)

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av ett meddelande i kön som innehåller ett JSON-objekt. Kö utlösaren Parsar JSON till ett objekt med namnet `ToDoItemLookup`, som innehåller ID att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>HTTP-utlösare, leta upp ID från frågesträng (C#)

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>HTTP-utlösare, leta upp ID från vägdata (C#)

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder vidarebefordra data för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>HTTP-utlösare, leta upp ID från vidarebefordra data med hjälp av SqlQuery (C#)

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder vidarebefordra data för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP Utlös, hämta flera, med hjälp av SqlQuery (C#)

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Frågan har angetts i den `SqlQuery` attributegenskapen.

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP Utlös, hämta flera, med DocumentClient (C#)

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Koden använder en `DocumentClient` instans som tillhandahålls av Azure Cosmos DB-bindning för att läsa en lista över dokument. Den `DocumentClient` instans kan också användas för skrivåtgärder.

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

[Hoppa över inkommande exempel](#input---attributes)

### <a name="input---c-script-examples"></a>Indata - C# exempel på skript

Det här avsnittet innehåller följande exempel som läser ett enskilt dokument genom att ange ID-värdet från olika källor:

* Kön utlösning, leta upp ID från meddelandet i kön
* Kön utlösning, leta upp ID från kön meddelandet med SqlQuery

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-c-script"></a>Kön utlösning, leta upp ID från meddelandet i kön (C# skript)

I följande exempel visas en inkommande Cosmos-DB-bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är de bindande data den *function.json* fil:

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
Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-using-sqlquery-c-script"></a>Kön utlösning, leta upp ID från kömeddelande med SqlQuery (C# skript)

I följande exempel visas en inkommande Azure DB som Cosmos-bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga som använder en kö utlösare för att anpassa Frågeparametrar.

Utlösaren kö innehåller en parameter `departmentId`. Ett kömeddelande för `{ "departmentId" : "Finance" }` returnerar alla poster för ekonomiavdelningen. 

Här är de bindande data den *function.json* fil:

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

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

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

[Hoppa över inkommande exempel](#input---attributes)

### <a name="input---javascript-examples"></a>Indata - JavaScript-exempel

Det här avsnittet innehåller följande exempel som läser ett enskilt dokument genom att ange ID-värdet från olika källor:

* Kön utlösning, leta upp ID från meddelandet i kön
* Kön utlösning, leta upp ID från kön meddelandet med Sqlquery

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-javascript"></a>Kön utlösning, leta upp ID från meddelandet i kön (JavaScript)

I följande exempel visas en inkommande Cosmos-DB-bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är de bindande data den *function.json* fil:

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
Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-using-sqlquery-javascript"></a>Kön utlösning, leta upp ID från kömeddelande med SqlQuery (JavaScript)

I följande exempel visas en inkommande Azure DB som Cosmos-bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga som använder en kö utlösare för att anpassa Frågeparametrar.

Utlösaren kö innehåller en parameter `departmentId`. Ett kömeddelande för `{ "departmentId" : "Finance" }` returnerar alla poster för ekonomiavdelningen. 

Här är de bindande data den *function.json* fil:

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

Den [configuration](#input---configuration) förklaras de här egenskaperna.

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

[Hoppa över inkommande exempel](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Indata - F #-exempel

I följande exempel visas en inkommande Cosmos-DB-bindning i en *function.json* fil och en [F # funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är de bindande data den *function.json* fil:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är F #-kod:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Det här exemplet kräver en `project.json` -fil som anger den `FSharp.Interop.Dynamic` och `Dynamitey` NuGet beroenden:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Att lägga till en `project.json` fil, se [F # paketet management](functions-reference-fsharp.md#package).

## <a name="input---attributes"></a>Indata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribut.

Attributets konstruktorn har databasnamnet och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [konfigurationsavsnittet följande](#input---configuration). 

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `DocumentDB` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ**     || måste anges till `documentdb`.        |
|**riktning**     || måste anges till `in`.         |
|**Namn**     || Parameterns namn bindning som representerar dokumentet i funktionen.  |
|**DatabaseName** |**DatabaseName** |Den databas som innehåller dokumentet.        |
|**Samlingsnamn** |**Samlingsnamn** | Namnet på den samling som innehåller dokumentet. |
|**ID**    | **Id** | ID för dokumentet ska hämtas. Den här egenskapen stöder [bindningsuttryck](functions-triggers-bindings.md#binding-expressions-and-patterns). Du inte ange både den **id** och **sqlQuery** egenskaper. Om du inte anger någon hämtas hela samlingen. |
|**SqlQuery**  |**SqlQuery**  | En Azure Cosmos-Databasens SQL-fråga som används för att hämta flera dokument. Egenskapen stöder runtime-bindningar, som i följande exempel: `SELECT * FROM c where c.departmentId = {departmentId}`. Du inte ange både den **id** och **sqlQuery** egenskaper. Om du inte anger någon hämtas hela samlingen.|
|**Anslutning**     |**ConnectionStringSetting**|Namnet på appinställningen som innehåller Azure Cosmos DB anslutningssträngen.        |
|**PartitionKey**|**PartitionKey**|Anger partitionsnyckelvärde för sökningen. Kan omfatta bindande parametrar.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

I C# och F # funktioner, när funktionen avslutas, sparas ändringar som görs till dokumentet via namngivna indataparametrar automatiskt. 

I JavaScript-funktioner görs uppdateringar inte automatiskt vid utloggning av funktionen. Använd i stället `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` att göra uppdateringar. Finns det [JavaScript exempel](#input---javascript-example).

## <a name="output"></a>Resultat

Azure Cosmos DB utdata bindning kan skriva du ett nytt dokument till en Azure Cosmos-DB-databas. 

>[!NOTE]
> Inte använder Azure Cosmos DB indata eller utdata bindningar om du använder MongoDB-API för en Cosmos-DB-konto. Skadade data är möjligt.

## <a name="output---example"></a>Output - exempel

Finns i det språkspecifika:

* [C#](#output---c-examples)
* [C#-skript (.csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)

Se även den [inkommande exempel](#input---c-examples) som använder `DocumentClient`.

[Hoppa över exempel på utdata](#output---attributes)

### <a name="ouput---c-examples"></a>Utdata - C#-exempel

Det här avsnittet innehåller följande exempel:

* Kön utlösning, Skriv ett dokument
* Kön utlösning, skriva dokument med hjälp av IAsyncCollector

Exemplen finns i en enkel `ToDoItem` typ:

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

[Hoppa över exempel på utdata](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Kön utlösning, Skriv ett dokument (C#)

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som lägger till ett dokument till en databas med hjälp av informationen i meddelandet från kön lagring.

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

[Hoppa över exempel på utdata](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Kön utlösning, skriva dokument med hjälp av IAsyncCollector (C#)

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som lägger till en samling dokument till en databas med hjälp av informationen i ett kömeddelande JSON.

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

[Hoppa över exempel på utdata](#output---attributes)

### <a name="output---c-script-examples"></a>Utdata - C# exempel på skript

I följande exempel visas en Azure Cosmos DB bindningen i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en inkommande kö-bindning för en kö som tar emot JSON i följande format:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funktionen skapar Azure Cosmos DB dokument i följande format för varje post:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Här är de bindande data den *function.json* fil:

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

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

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

Om du vill skapa flera dokument som du kan binda till `ICollector<T>` eller `IAsyncCollector<T>` där `T` är en av typerna som stöds.

[Hoppa över exempel på utdata](#output---attributes)

### <a name="output---javascript-examples"></a>Utdata - JavaScript-exempel

I följande exempel visas en Azure Cosmos DB bindningen i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen använder en inkommande kö-bindning för en kö som tar emot JSON i följande format:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funktionen skapar Azure Cosmos DB dokument i följande format för varje post:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Här är de bindande data den *function.json* fil:

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

Den [configuration](#output---configuration) förklaras de här egenskaperna.

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

[Hoppa över exempel på utdata](#output---attributes)

### <a name="output---f-examples"></a>Utdata - F #-exempel

I följande exempel visas en Azure Cosmos DB bindningen i en *function.json* fil och en [F # funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen använder en inkommande kö-bindning för en kö som tar emot JSON i följande format:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funktionen skapar Azure Cosmos DB dokument i följande format för varje post:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Här är de bindande data den *function.json* fil:

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
Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är F #-kod:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Det här exemplet kräver en `project.json` -fil som anger den `FSharp.Interop.Dynamic` och `Dynamitey` NuGet beroenden:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Att lägga till en `project.json` fil, se [F # paketet management](functions-reference-fsharp.md#package).

## <a name="output---attributes"></a>Utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribut.

Attributets konstruktorn har databasnamnet och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utdata - konfiguration](#output---configuration). Här är en `DocumentDB` attributet exempel i en signatur:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

En komplett exempel finns [utdata - C#-exempel](#output---c-example).

## <a name="output---configuration"></a>Output - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `DocumentDB` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ**     || måste anges till `documentdb`.        |
|**riktning**     || måste anges till `out`.         |
|**Namn**     || Parameterns namn bindning som representerar dokumentet i funktionen.  |
|**DatabaseName** | **DatabaseName**|Den databas som innehåller den samling där dokumentet har skapats.     |
|**Samlingsnamn** |**Samlingsnamn**  | Namnet på den samling där dokumentet har skapats. |
|**CreateIfNotExists**  |**CreateIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när det inte finns. Standardvärdet är *FALSKT* eftersom skapas nya samlingar med reserverat dataflöde, vilket har kostnad effekter. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**PartitionKey**|**PartitionKey** |När `CreateIfNotExists` är true, definierar partition Nyckelsökväg för samlingen som har skapats.|
|**CollectionThroughput**|**CollectionThroughput**| När `CreateIfNotExists` är true, definierar den [genomströmning](../cosmos-db/set-throughput.md) för samlingen som har skapats.|
|**Anslutning**    |**ConnectionStringSetting** |Namnet på appinställningen som innehåller Azure Cosmos DB anslutningssträngen.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

När du skriver till Utdataparametern i din funktion skapas ett dokument i databasen. Det här dokumentet har en GUID som genererats automatiskt som dokument-ID. Du kan ange dokument-ID för utdatadokumentet genom att ange den `id` egenskap i JSON-objekt som överförts till output-parameter. 

> [!Note]  
> När du anger ID på ett befintligt dokument, hämtar den över det nya utdatadokumentet. 

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| CosmosDB | [Felkoder för CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en Snabbstart som använder en Cosmos-DB-utlösare](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Mer information om serverlösa databasen databearbetning med Cosmos DB](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
