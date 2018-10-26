---
title: Azure Cosmos DB-bindningar för Functions 2.x
description: Förstå hur du använder Azure Cosmos DB-utlösare och bindningar i Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: aa01513a2381143edcaa59b215bb97951f333118
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087720"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB-bindningar för Azure Functions 2.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

Den här artikeln förklarar hur du arbetar med [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) bindningar i Azure Functions 2.x. Azure Functions stöder utlösa, indata och utdata-bindningar för Azure Cosmos DB.

> [!NOTE]
> Den här artikeln är [Azure Functions version 2.x](functions-versions.md).  Information om hur du använder dessa bindningar i Functions 1.x, se [Azure Cosmos DB-bindningar för Azure Functions 1.x](functions-bindings-cosmosdb.md).
>
> Den här bindningen hette ursprungligen DocumentDB. I funktioner version är 2.x, utlösare, bindningar och paketet alla namngivna Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>API: er som stöds

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Azure Cosmos DB-bindningar för Functions version 2.x finns i den [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet-paketet, version 3.x. Källkoden för bindningarna finns i den [azure-webjobs-sdk-tilläggen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Azure Cosmos DB-utlösaren använder den [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) att lyssna efter infogningar och uppdateringar på flera partitioner. Ändringsflöde publicerar infogningar och uppdateringar, inte borttagningar.

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

[Hoppa över utlösare-exempel](#trigger---attributes)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som anropas när det finns infogas eller uppdateras i den angivna databasen och samlingen.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
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
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

[Hoppa över utlösare-exempel](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Utlösare – exempel på C#-skript

I följande exempel visas en Cosmos DB-utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster har ändrats.

Här är bindningsdata i den *function.json* fil:

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
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

[Hoppa över utlösare-exempel](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

I följande exempel visas en Cosmos DB-utlösare bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster har ändrats.

Här är bindningsdata i den *function.json* fil:

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

### <a name="trigger---java-example"></a>Utlösare - Java-exemplet

I följande exempel visas en Cosmos DB-utlösare bindande i *function.json* fil och en [Java funktionen](functions-reference-java.md) som använder bindningen. Funktionen ingår när det finns infogar eller uppdaterar i den angivna databasen och samlingen.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

Här är den Java-kod:

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            reateLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `@CosmosDBTrigger` anteckning om parametrar vars värde skulle hämtas från Cosmos DB.  Den här anteckningen kan användas med interna Java-typer, Pojo eller kan ha värdet null-värden med hjälp av valfritt<T>. 

## <a name="trigger---c-attributes"></a>Utlösare – C#-attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribut.

Attributets konstruktorn tar databasens namn och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utlösare - konfigurationen](#trigger---configuration). Här är en `CosmosDBTrigger` attributet exemplet i signaturen för metoden:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Ett komplett exempel finns i [utlösare – C#-exempel](#trigger---c-example).


## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `CosmosDBTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** || Måste anges till `cosmosDBTrigger`. |
|**riktning** || Måste anges till `in`. Den här parametern anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** || Variabelnamnet som används i Funktionskoden som representerar en lista över dokument med ändringar. | 
|**connectionStringSetting**|**connectionStringSetting** | Namnet på en appinställning som innehåller anslutningssträngen som används för att ansluta till Azure Cosmos DB-kontot som övervakas. |
|**databaseName**|**databaseName**  | Namnet på Azure Cosmos DB-databasen med den samling som övervakas. |
|**Samlingsnamn** |**Samlingsnamn** | Namnet på samlingen som övervakas. |
|**leaseConnectionStringSetting** | **leaseConnectionStringSetting** | (Valfritt) Namnet på en appinställning som innehåller anslutningssträngen till den tjänst som innehar lånet samlingen. När inte har angetts i `connectionStringSetting` värde som ska användas. Den här parametern anges automatiskt när bindningen skapas i portalen. Anslutningssträngen för lånsamlingen måste ha skrivbehörighet.|
|**leaseDatabaseName** |**leaseDatabaseName** | (Valfritt) Namnet på den databas som innehåller den samling som används för att lagra lån. När inte har värdet för den `databaseName` inställningen används. Den här parametern anges automatiskt när bindningen skapas i portalen. |
|**leaseCollectionName** | **leaseCollectionName** | (Valfritt) Namnet på den samling som används för att lagra lån. När inte har värdet `leases` används. |
|**createLeaseCollectionIfNotExists** | **createLeaseCollectionIfNotExists** | (Valfritt) När värdet `true`, lånsamlingen skapas automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (Valfritt) Definierar mängd Begäransenheter för att tilldela när lånsamlingen skapas. Den här inställningen är endast används när `createLeaseCollectionIfNotExists` är inställd på `true`. Den här parametern anges automatiskt när bindningen har skapats med hjälp av portalen.
|**leaseCollectionPrefix**| **leaseCollectionPrefix**| (Valfritt) När värdet den lägger till ett prefix till lån som skapats i lånsamling för den här funktionen så effektivt att två separata Azure-Functions för att dela samma lånsamling med hjälp av olika prefix.
|**feedPollDelay**| **feedPollDelay**| (Valfritt) När mängd, den definierar, i millisekunder, fördröjning mellan att avsöka en partition för nya ändringar på flödet, är när alla aktuella ändringar tömda. Standardvärdet är 5 000 (5 sekunder).
|**leaseAcquireInterval**| **leaseAcquireInterval**| (Valfritt) När värdet definierar den, i millisekunder, intervallet sätta igång en uppgift att beräkna om partitioner fördelas jämnt mellan kända ha instanser. Standardvärdet är 13000 (13 sekunder).
|**leaseExpirationInterval**| **leaseExpirationInterval**| (Valfritt) När värdet definierar den, i millisekunder, intervallet som fattas lånet om ett lån som representerar en partition. Om lånet inte förnyas inom intervallet, det gör att det upphör att gälla och ägarskap för partitionen flyttas till en annan instans. Standardvärdet är 60000 (60 sekunder).
|**leaseRenewInterval**| **leaseRenewInterval**| (Valfritt) När värdet definierar den, i millisekunder, förnyelseintervallet för alla lån för partitioner som för tillfället hålls av en instans. Standardvärdet är 17000 (17 sekunder).
|**checkpointFrequency**| **checkpointFrequency**| (Valfritt) När värdet definierar den, i millisekunder, hur många lån kontrollpunkter. Standardvärdet är alltid efter en lyckad funktionsanrop.
|**maxItemsPerInvocation**| **maxItemsPerInvocation**| (Valfritt) När värdet anpassar det den maximala mängden objekt tas emot per funktionsanrop.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna. Både den samling som övervakas och den samling som innehåller lån måste vara tillgänglig för utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner har konfigurerats för att använda en Cosmos DB-utlösare för samma samling, var och en av funktionerna bör använda en dedikerad lånsamling eller ange en annan `LeaseCollectionPrefix` för varje funktion. Annars kan aktiveras endast en av funktionerna. Information om prefixet som finns i den [konfigurationsavsnittet](#trigger---configuration).

Utlösaren anger inte om ett dokument har uppdateras eller infogas, den bara innehåller själva dokumentet. Om du vill hantera uppdateringar och infogningar på olika sätt, kan du göra det genom att implementera värdefält för infogning eller uppdaterar.

## <a name="input"></a>Indata

Azure Cosmos DB-indatabindning använder SQL-API för att hämta en eller flera Azure Cosmos DB-dokument och skickar dem till Indataparametern för funktionen. Dokument-ID eller frågeparametrar kan fastställas baserat på utlösare som anropar funktionen. 

## <a name="input---examples"></a>Indata - exempel

Se exempel språkspecifika som läser ett enskilt dokument genom att ange ett ID-värde:

* [C#](#input---c-examples)
* [C#-skript (.csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)

[Hoppa över inkommande exempel](#input---attributes)

### <a name="input---c-examples"></a>Indata - C#-exempel

Det här avsnittet innehåller följande exempel:

* [Köutlösare, leta upp ID från JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-utlösare, leta upp ID från dirigera data](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-utlösare, leta upp ID från dirigera data med SQL-fråga](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP utlösa får flera docs, med hjälp av SQL-fråga](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP utlösa får flera docs, med hjälp av DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Exemplen finns en enkel `ToDoItem` typ:

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Köutlösare, leta upp ID från JSON (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av ett kömeddelande som innehåller ett JSON-objekt. Kö-utlösare Parsar JSON till ett objekt med namnet `ToDoItemLookup`, som innehåller det ID du söker. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
namespace CosmosDBSamplesV2
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
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>HTTP-utlösare, leta upp ID från frågesträngen (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

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
                Id = "{Query.id}")] ToDoItem toDoItem,
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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>HTTP-utlösare, leta upp ID från dirigera data (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder dirigera data för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

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
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{id}")] ToDoItem toDoItem,
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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>HTTP-utlösare, leta upp ID från dirigera data med SQL-fråga (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder dirigera data för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen. 

Exemplet visar hur du använder en bindning uttryck i den `SqlQuery` parametern. Du kan skicka vidarebefordra data till den `SqlQuery` parameter som det visas, men för närvarande [du kan inte skicka frågesträngsvärden](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP utlösa får flera docs, med hjälp av SQL-fråga (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Frågan har angetts i den `SqlQuery` attributet egenskapen.

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP utlösa får flera docs, med hjälp av DocumentClient (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Koden använder en `DocumentClient` instans som tillhandahålls av Azure Cosmos DB-bindning för att läsa en lista över dokument. Den `DocumentClient` instans kan också användas för skrivåtgärder.

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

[Hoppa över inkommande exempel](#input---attributes)

### <a name="input---c-script-examples"></a>Indata - C#-exempelskript

Det här avsnittet innehåller följande exempel:

* [Köutlösare, leta upp ID från sträng](#queue-trigger-look-up-id-from-string-c-script)
* [Kö utlösaren, hämta flera, med hjälp av SQL-fråga](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-utlösare, leta upp ID från dirigera data](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP utlösa får flera docs, med hjälp av SQL-fråga](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP utlösa får flera docs, med hjälp av DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP-utlösaren exempel referera till en enkel `ToDoItem` typ:

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Köutlösare, leta upp ID från sträng (C#-skript)

I följande exempel visas en Cosmos DB-indatabindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är bindningsdata i den *function.json* fil:

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
Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Kö utlösaren, hämta flera, med hjälp av SQL-fråga (C#-skript)

I följande exempel visas en Azure Cosmos DB-indatabindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga som använder en kö-utlösare för att anpassa Frågeparametrar.

Kö-utlösare innehåller en parameter `departmentId`. Ett kömeddelande för `{ "departmentId" : "Finance" }` returneras alla poster för ekonomiavdelningen. 

Här är bindningsdata i den *function.json* fil:

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

Den [configuration](#input---configuration) förklaras de här egenskaperna.

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>HTTP-utlösare, leta upp ID från frågesträngen (C#-skript)

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

Här är den *function.json* fil:

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Här är C#-skriptkoden:

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>HTTP-utlösare, leta upp ID från dirigera data (C#-skript)

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder dirigera data för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

Här är den *function.json* fil:

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
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP utlösa får flera docs, med hjälp av SQL-fråga (C#-skript)

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Frågan har angetts i den `SqlQuery` attributet egenskapen.

Här är den *function.json* fil:

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

Här är C#-skriptkoden:

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP utlösa får flera docs, med hjälp av DocumentClient (C#-skript)

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Koden använder en `DocumentClient` instans som tillhandahålls av Azure Cosmos DB-bindning för att läsa en lista över dokument. Den `DocumentClient` instans kan också användas för skrivåtgärder.

Här är den *function.json* fil:

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

Här är C#-skriptkoden:

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

[Hoppa över inkommande exempel](#input---attributes)

### <a name="input---javascript-examples"></a>Indata - JavaScript-exempel

Det här avsnittet innehåller följande exempel som läser ett enskilt dokument genom att ange ett ID-värde från olika källor:

* [Köutlösare, leta upp ID från JSON](#queue-trigger-look-up-id-from-string-javascript)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-utlösare, leta upp ID från dirigera data](#http-trigger-look-up-id-from-route-data-javascript)
* [Kö utlösaren, hämta flera, med hjälp av SQL-fråga](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Köutlösare, leta upp ID från JSON (JavaScript)

I följande exempel visas en Cosmos DB-indatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är bindningsdata i den *function.json* fil:

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

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>HTTP-utlösare, leta upp ID från frågesträngen (JavaScript)

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

Här är den *function.json* fil:

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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>HTTP-utlösare, leta upp ID från dirigera data (JavaScript)

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

Här är den *function.json* fil:

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
      "type": "cosmosDB",
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

[Hoppa över inkommande exempel](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Kö utlösaren, hämta flera, med hjälp av SQL-fråga (JavaScript)

I följande exempel visas en Azure Cosmos DB-indatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga som använder en kö-utlösare för att anpassa Frågeparametrar.

Kö-utlösare innehåller en parameter `departmentId`. Ett kömeddelande för `{ "departmentId" : "Finance" }` returneras alla poster för ekonomiavdelningen. 

Här är bindningsdata i den *function.json* fil:

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

I följande exempel visas en Cosmos DB-indatabindning i en *function.json* fil och en [F #-funktion](functions-reference-fsharp.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
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

Det här exemplet kräver en `project.json` -fil som anger den `FSharp.Interop.Dynamic` och `Dynamitey` NuGet-beroenden:

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

Att lägga till en `project.json` fil, se [F #-pakethantering](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>Indata - Java-exempel

I följande exempel visas en Java-funktion som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. Detta ID används för att hämta ett ToDoItem-dokument från den angivna databasen och samlingen.

Här är den Java-kod:

```java
@FunctionName("getItem")
public String cosmosDbQueryById(
    @HttpTrigger(name = "req",
                  methods = {HttpMethod.GET},
                  authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> dummy,
    @CosmosDBInput(name = "database",
                      databaseName = "ToDoList",
                      collectionName = "Items",
                      leaseCollectionName = "",
                      id = "{Query.id}"
                      connectionStringSetting = "AzureCosmosDBConnection") Optional<String> item,
    final ExecutionContext context
 ) {
    return item.orElse("Not found");
 }
 ```

I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `@CosmosDBInput` anteckningen i funktionsparametrar vars värde skulle hämtas från Cosmos DB.  Den här anteckningen kan användas med interna Java-typer, Pojo eller kan ha värdet null-värden med hjälp av valfritt<T>. 

## <a name="input---attributes"></a>Indata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribut.

Attributets konstruktorn tar databasens namn och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [följande konfigurationsavsnittet](#input---configuration). 

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `CosmosDB` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**     || Måste anges till `cosmosDB`.        |
|**riktning**     || Måste anges till `in`.         |
|**Namn**     || Namnet på bindningsparametern som representerar dokumentet i funktionen.  |
|**databaseName** |**databaseName** |Den databas som innehåller dokumentet.        |
|**Samlingsnamn** |**Samlingsnamn** | Namnet på den samling som innehåller dokumentet. |
|**ID**    | **Id** | ID för dokumentet som ska hämtas. Den här egenskapen stöder [bindning uttryck](functions-triggers-bindings.md#binding-expressions-and-patterns). Inte ange både den **id** och **SQL-fråga** egenskaper. Om du inte anger någon hämtas hela samlingen. |
|**SQL-fråga**  |**SQL-fråga**  | En Azure Cosmos DB SQL-fråga som används för att hämta flera dokument. Egenskapen stöder runtime-bindningar, som i följande exempel: `SELECT * FROM c where c.departmentId = {departmentId}`. Inte ange både den **id** och **SQL-fråga** egenskaper. Om du inte anger någon hämtas hela samlingen.|
|**connectionStringSetting**     |**connectionStringSetting**|Namnet på den appinställning som innehåller din Azure Cosmos DB-anslutningssträng.        |
|**partitionKey**|**partitionKey**|Anger partitionsnyckelvärdet för sökningen. Omfatta bindande parametrar.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

I C# och F #-funktioner, när funktionen avslutas, sparas ändringar som görs till dokumentet via namngivna indataparametrar automatiskt. 

I JavaScript-funktioner görs uppdateringar inte automatiskt vid utloggning av funktionen. Använd i stället `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` att göra uppdateringar. Se den [JavaScript exempel](#input---javascript-example).

## <a name="output"></a>Resultat

I Azure Cosmos DB-utdatabindning kan skriva du ett nytt dokument till en Azure Cosmos DB-databas med hjälp av SQL-API. 

## <a name="output---examples"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-examples)
* [C#-skript (.csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)
* [Java](#output---java-example)

Se även de [inkommande exempel](#input---c-examples) som använder `DocumentClient`.

[Hoppa över exempel på utdata](#output---attributes)

### <a name="ouput---c-examples"></a>Utdata - C#-exempel

Det här avsnittet innehåller följande exempel:

* Köutlösare, Skriv ett dokument
* Köutlösare, Skriv docs med IAsyncCollector

Exemplen finns en enkel `ToDoItem` typ:

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

[Hoppa över exempel på utdata](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Köutlösare, Skriv ett dokument (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till ett dokument till en databas med hjälp av data som anges i meddelandet från Queue storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

[Hoppa över exempel på utdata](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Köutlösare, Skriv docs med IAsyncCollector (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till en samling dokument till en databas med hjälp av data i ett kömeddelande JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

[Hoppa över exempel på utdata](#output---attributes)

### <a name="output---c-script-examples"></a>Resultat – C#-exempelskript

Det här avsnittet innehåller följande exempel:

* Köutlösare, Skriv ett dokument
* Köutlösare, Skriv docs med IAsyncCollector

[Hoppa över exempel på utdata](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Köutlösare, Skriv ett dokument (C#-skript)

I följande exempel visar ett Azure Cosmos DB-utdatabindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en indatabindning i kö för en kö som tar emot JSON i följande format:

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

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köutlösare, Skriv docs med IAsyncCollector

Om du vill skapa flera dokument som du kan binda till `ICollector<T>` eller `IAsyncCollector<T>` där `T` är en av typerna som stöds.

Det här exemplet refererar till en enkel `ToDoItem` typ:

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

Här är filen function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Här är C#-skriptkoden:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

[Hoppa över exempel på utdata](#output---attributes)

### <a name="output---javascript-examples"></a>Resultat – JavaScript-exempel

I följande exempel visar ett Azure Cosmos DB-utdatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en indatabindning i kö för en kö som tar emot JSON i följande format:

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

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",     
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

### <a name="output---f-examples"></a>Resultat – F #-exempel

I följande exempel visar ett Azure Cosmos DB-utdatabindning i en *function.json* fil och en [F #-funktion](functions-reference-fsharp.md) som använder bindningen. Funktionen använder en indatabindning i kö för en kö som tar emot JSON i följande format:

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

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```
Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är F #-kod:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json
    open Microsoft.Extensions.Logging

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: ILogger) =
      log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Det här exemplet kräver en `project.json` -fil som anger den `FSharp.Interop.Dynamic` och `Dynamitey` NuGet-beroenden:

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

Att lägga till en `project.json` fil, se [F #-pakethantering](functions-reference-fsharp.md#package).

## <a name="output---java-examples"></a>Resultat – Java-exempel

I följande exempel visas en Java-funktion som lägger tillför ett dokument till en databas med data från ett meddelande i Queue storage.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database", databaseName = "ToDoList", collectionName = "Items", connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
     @QueueTrigger(name = "msg", queueName = "myqueue-items", connection = "AzureWebJobsStorage") String message,
     final ExecutionContext context
)  {
     return "{ id: " + System.currentTimeMillis() + ", Description: " + message + " }";
   }
```

I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `@CosmosDBOutput` anteckning på parametrar som ska skrivas till Cosmos DB.  Parametertypen anteckning ska vara OutputBinding<T>, där T är en inbyggd Java-typ eller en POJO.


## <a name="output---attributes"></a>Utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribut.

Attributets konstruktorn tar databasens namn och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utdata - konfigurationen](#output---configuration). Här är en `CosmosDB` attributet exemplet i signaturen för metoden:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `CosmosDB` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**     || Måste anges till `cosmosDB`.        |
|**riktning**     || Måste anges till `out`.         |
|**Namn**     || Namnet på bindningsparametern som representerar dokumentet i funktionen.  |
|**databaseName** | **databaseName**|Den databas som innehåller den samling där dokumentet skapas.     |
|**Samlingsnamn** |**Samlingsnamn**  | Namnet på den samling där dokumentet skapas. |
|**createIfNotExists**  |**createIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när den inte finns. Standardvärdet är *FALSKT* eftersom nya samlingar skapas med reserverat dataflöde, vilket har kostnad effekter. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**partitionKey** |När `CreateIfNotExists` är sant, definierar Nyckelsökväg partition för samlingen som har skapats.|
|**collectionThroughput**|**collectionThroughput**| När `CreateIfNotExists` är sant, definierar den [dataflöde](../cosmos-db/set-throughput.md) för samlingen som har skapats.|
|**connectionStringSetting**    |**connectionStringSetting** |Namnet på den appinställning som innehåller din Azure Cosmos DB-anslutningssträng.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

När du skriver till Utdataparametern i din funktion skapas ett dokument i databasen. Det här dokumentet har en automatiskt genererad GUID som dokument-ID Du kan ange ID för konfigurationsobjektdokument för utdatadokumentet genom att ange den `id` -egenskapen i JSON-objekt skickades till output-parameter. 

> [!Note]  
> När du anger ID för ett befintligt dokument, hämtar den skrivs över av det nya utdatadokumentet. 

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| CosmosDB | [Felkoder för CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om serverlös databehandling med Cosmos DB-databas](..\cosmos-db\serverless-computing-database.md)
* [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
