---
title: Azure Cosmos DB bindningar för funktioner 2. x
description: Förstå hur du använder Azure Cosmos DB utlösare och bindningar i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, functions, Event Processing, dynamisk beräkning, Server lös arkitektur
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: d8aee88f6ef3f6a73beadfdf242d79d9b361de0a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469385"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB bindningar för Azure Functions 2. x

> [!div class="op_single_selector" title1="Välj den version av Azure Functions runtime som du använder: "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

Den här artikeln förklarar hur du arbetar med [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) -bindningar i Azure Functions 2. x. Azure Functions stöd för utlösare, indata och utgående bindningar för Azure Cosmos DB.

> [!NOTE]
> Den här artikeln är för [Azure Functions version 2. x](functions-versions.md).  Information om hur du använder dessa bindningar i functions 1. x finns [Azure Cosmos DB bindningar för Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> Den här bindningen hade ursprungligen namnet DocumentDB. I functions version 2. x är utlösaren, bindningarna och paketet alla namngivna Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>API: er som stöds

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Paket-funktioner 2. x

Azure Cosmos DB-bindningar för functions version 2. x finns i [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet-paketet, version 3. x. Käll koden för bindningarna finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Azure Cosmos DB-utlösaren använder [Azure Cosmos DB ändra feed](../cosmos-db/change-feed.md) för att lyssna efter infogningar och uppdateringar över partitioner. Ändrings flödet publicerar infogningar och uppdateringar, tas inte bort.

## <a name="trigger---example"></a>Utlös – exempel

Se språkspecifika exempel:

* [C#](#trigger---c-example)
* [C#skript (. CSX)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

Hoppa över utlösare exempel

### <a name="trigger---c-example"></a>Utlös – C# exempel

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som anropas när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

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

Hoppa över utlösare exempel

### <a name="trigger---c-script-example"></a>Utlösare – C# skript exempel

I följande exempel visas en Cosmos DB trigger-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster ändras.

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

Här är C# skript koden:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

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

Hoppa över utlösare exempel

### <a name="trigger---javascript-example"></a>Utlös – JavaScript-exempel

I följande exempel visas en Cosmos DB trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster ändras.

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

Här är JavaScript-koden:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

### <a name="trigger---java-example"></a>Utlös – Java-exempel

I följande exempel visas en Cosmos DB trigger-bindning i *Function. JSON* -filen och en [Java-funktion](functions-reference-java.md) som använder bindningen. Funktionen är involverad när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

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

Här är Java-koden:

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@CosmosDBTrigger` kommentar för parametrar vars värde kommer från Cosmos dB.  Den här anteckningen kan användas med interna Java-typer, Pojo eller null-värden med valfria\<T >.


Hoppa över utlösare exempel

### <a name="trigger---python-example"></a>Utlös – python-exempel

I följande exempel visas en Cosmos DB trigger-bindning i en *Function. JSON* -fil och en [python-funktion](functions-reference-python.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster ändras.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är python-koden:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

## <a name="trigger---c-attributes"></a>Utlös ande C# attribut

Använd attributet [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar databasens namn och samlings namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [trigger-Configuration](#trigger---configuration). Här är ett exempel på ett `CosmosDBTrigger`-attribut i en metodsignatur:

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

Ett fullständigt exempel finns i [Utlös ande- C# exempel](#trigger---c-example).


## <a name="trigger---configuration"></a>Utlös konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `CosmosDBTrigger`-attributet.

|function. JSON-egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** || Måste anges till `cosmosDBTrigger`. |
|**riktning** || Måste anges till `in`. Den här parametern anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** || Variabel namnet som används i funktions kod som representerar listan med dokument med ändringar. |
|**connectionStringSetting**|**ConnectionStringSetting** | Namnet på en app-inställning som innehåller anslutnings strängen som används för att ansluta till det Azure Cosmos DB-konto som övervakas. |
|**Databas**|**Databas**  | Namnet på Azure Cosmos DB databasen med den samling som övervakas. |
|**Samling** |**Samling** | Namnet på den samling som övervakas. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Valfritt Namnet på en app-inställning som innehåller anslutnings strängen till tjänsten som innehåller låne samlingen. Om värdet inte anges används `connectionStringSetting` svärdet. Den här parametern anges automatiskt när bindningen skapas i portalen. Anslutnings strängen för samlingen lån måste ha Skriv behörighet.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Valfritt Namnet på databasen som innehåller den samling som används för att lagra lån. När inställningen inte anges används värdet för `databaseName`-inställningen. Den här parametern anges automatiskt när bindningen skapas i portalen. |
|**leaseCollectionName** | **LeaseCollectionName** | Valfritt Namnet på den samling som används för att lagra lån. När den inte anges används värdet `leases`. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Valfritt När värdet är `true`skapas låne samlingen automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Valfritt Definierar mängden enheter för programbegäran som ska tilldelas när låne samlingen skapas. Den här inställningen används bara när `createLeaseCollectionIfNotExists` är inställt på `true`. Den här parametern anges automatiskt när bindningen skapas med hjälp av portalen.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Valfritt När den är aktive rad lägger den till ett prefix till de lån som skapats i leasing samlingen för den här funktionen, vilket effektivt tillåter två separata Azure Functions att dela samma Lease-samling med olika prefix.
|**feedPollDelay**| **FeedPollDelay**| Valfritt När det är inställt definierar den i millisekunder fördröjningen mellan att avsöka en partition efter nya ändringar i flödet, efter att alla aktuella ändringar har tömts. Standardvärdet är 5000 (5 sekunder).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Valfritt När det är inställt definierar den, i millisekunder, intervallet för att starta en aktivitet för att beräkna om partitioner distribueras jämnt mellan kända värd instanser. Standardvärdet är 13000 (13 sekunder).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Valfritt När det är inställt definierar den, i millisekunder, intervallet för vilken lånet tas i ett lån som representerar en partition. Om lånet inte förnyas inom det här intervallet kommer det att förfalla och ägarskapet av partitionen flyttas till en annan instans. Standardvärdet är 60000 (60 sekunder).
|**leaseRenewInterval**| **LeaseRenewInterval**| Valfritt När det är inställt definierar förnyelse intervallet i millisekunder för alla lån för partitioner som för närvarande innehas av en instans. Standardvärdet är 17000 (17 sekunder).
|**checkpointFrequency**| **CheckpointFrequency**| Valfritt När det är inställt definierar den, i millisekunder, intervallet mellan låne kontroll punkter. Standardvärdet är alltid efter varje funktions anrop.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Valfritt När den här egenskapen anges anger den maximala mängden objekt som tas emot per funktions anrop. Om åtgärder i den övervakade samlingen utförs genom lagrade procedurer bevaras [transaktions omfång](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) vid läsning av objekt från ändrings flödet. På grund av detta är det möjligt att antalet objekt som har tagits emot är högre än det angivna värdet, så att objekten som ändras av samma transaktion returneras som en del av en atomisk batch.
|**startFromBeginning**| **StartFromBeginning**| Valfritt När det är inställt, instrueras utlösaren att börja läsa ändringar från början av samlingens historik i stället för den aktuella tiden. Detta fungerar bara första gången utlösaren startar, precis som vid efterföljande körningar, så är kontroll punkterna redan lagrade. Om du anger detta till `true` när det redan har skapats lån har ingen påverkan.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlös användning

Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna. Både samlingen som övervakas och samlingen som innehåller lånet måste vara tillgängliga för att utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner har kon figurer ATS för att använda en Cosmos DB-utlösare för samma samling, ska varje funktion använda en dedikerad Lease-samling eller ange en annan `LeaseCollectionPrefix` för varje funktion. Annars aktive ras bara en av funktionerna. Information om prefixet finns i [avsnittet konfiguration](#trigger---configuration).

Utlösaren anger inte om ett dokument har uppdaterats eller infogats. det innehåller bara själva dokumentet. Om du behöver hantera uppdateringar och infogningar på olika sätt kan du göra det genom att implementera tidsstämpelfält för infogning eller uppdatering.

## <a name="input"></a>Indata

Azure Cosmos DB-databindningen använder SQL-API: t för att hämta ett eller flera Azure Cosmos DB dokument och skickar dem till Indataparametern för funktionen. Dokument-ID eller frågeparametrar kan bestämmas baserat på utlösaren som anropar funktionen.

## <a name="input---examples"></a>Inmatade exempel

Se språkspecifika exempel som läser ett enda dokument genom att ange ett ID-värde:

* [C#](#input---c-examples)
* [C#skript (. CSX)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[Hoppa över inmatade exempel](#input---attributes)

### <a name="input---c-examples"></a>Inmatade C# exempel

Det här avsnittet innehåller följande exempel:

* [Köa utlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-utlösare, slå upp ID från routa data med SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-utlösare, hämta flera dokument med SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-utlösare, hämta flera dokument med DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Exemplen refererar till en enkel `ToDoItem`s typ:

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Köa utlösare, leta upp ID frånC#JSON ()

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av ett Queue-meddelande som innehåller ett JSON-objekt. Queue-utlösaren parsar JSON till ett objekt med namnet `ToDoItemLookup`, som innehåller det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>HTTP-utlösare, leta upp ID frånC#frågesträng ()

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

>[!NOTE]
>Parametern HTTP-frågesträng är Skift läges känslig.
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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>HTTP-utlösare, leta upp ID frånC#rutt data ()

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>HTTP-utlösare, slå upp ID från flödes dataC#med SqlQuery ()

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

Exemplet visar hur du använder ett bindnings uttryck i `SqlQuery` parameter. Du kan skicka flödes data till parametern `SqlQuery` som visas, men för närvarande kan [du inte skicka frågesträngs värden](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP-utlösare, hämta flera dokument medC#SqlQuery ()

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. Frågan anges i egenskapen `SqlQuery` attribut.

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-utlösare, hämta flera dokument medC#DocumentClient ()

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar en lista med dokument. Funktionen utlöses av en HTTP-begäran. I koden används en `DocumentClient`-instans från Azure Cosmos DB-bindningen för att läsa en lista över dokument. `DocumentClient`-instansen kan också användas för Skriv åtgärder.

> [!NOTE]
> Du kan också använda [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) -gränssnittet för att göra testningen enklare.

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

[Hoppa över inmatade exempel](#input---attributes)

### <a name="input---c-script-examples"></a>Exempel på C# inmatade skript

Det här avsnittet innehåller följande exempel:

* [Köa utlösare, leta upp ID från sträng](#queue-trigger-look-up-id-from-string-c-script)
* [Köa utlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-utlösare, hämta flera dokument med SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, hämta flera dokument med DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Exempel på HTTP-utlösare hänvisar till en enkel `ToDoItem`s typ:

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Köa utlösare, leta upp ID frånC# sträng (skript)

I följande exempel visas en Cosmos DB-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets text värde.

Här är bindnings data i *Function. JSON* -filen:

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
I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är C# skript koden:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Köa utlösare, hämta flera dokument med SqlQueryC# (skript)

I följande exempel visas en Azure Cosmos DB-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga med hjälp av en Queue-utlösare för att anpassa frågeparametrar.

Utlösaren för kön innehåller en parameter `departmentId`. Ett köat meddelande i `{ "departmentId" : "Finance" }` skulle returnera alla poster för ekonomi avdelningen.

Här är bindnings data i *Function. JSON* -filen:

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

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är C# skript koden:

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>HTTP-utlösare, leta upp ID frånC# frågesträng (skript)

I följande exempel visas en [ C# skript funktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

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
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": false
}
```

Här är C# skript koden:

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>HTTP-utlösare, leta upp ID frånC# rutt data (skript)

I följande exempel visas en [ C# skript funktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder flödes data för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

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

Här är C# skript koden:

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP-utlösare, hämta flera dokument medC# SqlQuery (skript)

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

Här är C# skript koden:

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP-utlösare, hämta flera dokument medC# DocumentClient (skript)

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

Här är C# skript koden:

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

[Hoppa över inmatade exempel](#input---attributes)

### <a name="input---javascript-examples"></a>Exempel på inmatade JavaScript-skript

Det här avsnittet innehåller följande exempel som läser ett enda dokument genom att ange ett ID-värde från olika källor:

* [Köa utlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-javascript)
* [Köa utlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Köa utlösare, leta upp ID från JSON (Java Script)

I följande exempel visas en Cosmos DB-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets text värde.

Här är bindnings data i *Function. JSON* -filen:

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
I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>HTTP-utlösare, leta upp ID från frågesträng (Java Script)

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

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
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>HTTP-utlösare, leta upp ID från flödes data (Java Script)

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Köa utlösare, hämta flera dokument med SqlQuery (Java Script)

I följande exempel visas en Azure Cosmos DB-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga med hjälp av en Queue-utlösare för att anpassa frågeparametrar.

Utlösaren för kön innehåller en parameter `departmentId`. Ett köat meddelande i `{ "departmentId" : "Finance" }` skulle returnera alla poster för ekonomi avdelningen.

Här är bindnings data i *Function. JSON* -filen:

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

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

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

[Hoppa över inmatade exempel](#input---attributes)

### <a name="input---python-examples"></a>In-python-exempel

Det här avsnittet innehåller följande exempel som läser ett enda dokument genom att ange ett ID-värde från olika källor:

* [Köa utlösare, slå upp ID från JSON](#queue-trigger-look-up-id-from-json-python)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-python)
* [Köa utlösare, hämta flera dokument med SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-python"></a>Köa utlösare, leta upp ID från JSON (python)

I följande exempel visas en Cosmos DB-bindning i en *Function. JSON* -fil och en [python-funktion](functions-reference-python.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets text värde.

Här är bindnings data i *Function. JSON* -filen:

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

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är python-koden:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-python"></a>HTTP-utlösare, leta upp ID från frågesträng (python)

I följande exempel visas en [python-funktion](functions-reference-python.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

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
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Här är python-koden:

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-python"></a>HTTP-utlösare, leta upp ID från rutt data (python)

I följande exempel visas en [python-funktion](functions-reference-python.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID som ska sökas upp. Detta ID används för att hämta ett `ToDoItem`-dokument från den angivna databasen och samlingen.

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
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Här är python-koden:

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

[Hoppa över inmatade exempel](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>Köa utlösare, hämta flera dokument med SqlQuery (python)

I följande exempel visas en Azure Cosmos DB-bindning i en *Function. JSON* -fil och en [python-funktion](functions-reference-python.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga med hjälp av en Queue-utlösare för att anpassa frågeparametrar.

Utlösaren för kön innehåller en parameter `departmentId`. Ett köat meddelande i `{ "departmentId" : "Finance" }` skulle returnera alla poster för ekonomi avdelningen.

Här är bindnings data i *Function. JSON* -filen:

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

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är python-koden:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```


[Hoppa över inmatade exempel](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Inmatade F# exempel

I följande exempel visas en Cosmos DB-bindning i en *Function. JSON* -fil och en [ F# funktion](functions-reference-fsharp.md) som använder bindningen. Funktionen läser ett enda dokument och uppdaterar dokumentets text värde.

Här är bindnings data i *Function. JSON* -filen:

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

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är F# koden:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Det här exemplet kräver en `project.json`-fil som anger `FSharp.Interop.Dynamic` och `Dynamitey` NuGet-beroenden:

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

Information om hur du lägger till en `project.json`-fil finns i [ F# paket hantering](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>In-Java-exempel

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare, leta upp ID från frågesträngparametern-String-parameter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-utlösare, leta upp ID från frågesträng-POJO-parameter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP-utlösare, slå upp ID från flödes data](#http-trigger-look-up-id-from-route-data-java)
* [HTTP-utlösare, slå upp ID från routa data med SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-utlösare, hämta flera dokument från väg data med SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Exemplen refererar till en enkel `ToDoItem`s typ:

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

#### <a name="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>HTTP-utlösare, leta upp ID från frågesträng-sträng (Java)

I följande exempel visas en Java-funktion som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID som ska sökas upp. Detta ID används för att hämta ett dokument från den angivna databasen och samlingen i sträng format.

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
              partitionKey = "{Query.id}",
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

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@CosmosDBInput` kommentar på funktions parametrar vars värde kommer från Cosmos dB.  Den här anteckningen kan användas med interna Java-typer, Pojo eller null-värden med valfria\<T >.

#### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>HTTP-utlösare, leta upp ID från frågesträng-POJO parameter (Java)

I följande exempel visas en Java-funktion som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange det ID som ska sökas upp. Detta ID används för att hämta ett dokument från den angivna databasen och samlingen. Dokumentet konverteras sedan till en instans av ```ToDoItem``` POJO som tidigare skapats och skickades som ett argument till funktionen.

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
              partitionKey = "{Query.id}",
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

#### <a name="http-trigger-look-up-id-from-route-data-java"></a>HTTP-utlösare, leta upp ID från rutt data (Java)

I följande exempel visas en Java-funktion som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en väg parameter för att ange det ID som ska sökas upp. Detta ID används för att hämta ett dokument från den angivna databasen och samlingen, vilket returnerar det som en ```Optional<String>```.

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{id}",
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

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>HTTP-utlösare, slå upp ID från routa data med SqlQuery (Java)

I följande exempel visas en Java-funktion som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en väg parameter för att ange det ID som ska sökas upp. Detta ID används för att hämta ett dokument från den angivna databasen och samlingen, vilket konverterar resultat uppsättningen till en ```ToDoItem[]```, eftersom många dokument kan returneras, beroende på frågevillkor.

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

#### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>HTTP-utlösare, hämta flera dokument från väg data med SqlQuery (Java)

I följande exempel visas en Java-funktion som innehåller flera dokument. Funktionen utlöses av en HTTP-begäran som använder en väg parameter ```desc``` för att ange strängen som ska sökas efter i fältet ```description```. Sök termen används för att hämta en samling dokument från den angivna databasen och samlingen, vilket konverterar resultat uppsättningen till en ```ToDoItem[]``` och skickar den som ett argument till funktionen.

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

## <a name="input---attributes"></a>In-attribut

Använd attributet [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar databasens namn och samlings namn. Information om de inställningar och andra egenskaper som du kan konfigurera finns i [följande konfigurations avsnitt](#input---configuration).

## <a name="input---configuration"></a>Indatamängds konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `CosmosDB`-attributet.

|function. JSON-egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ**     || Måste anges till `cosmosDB`.        |
|**riktning**     || Måste anges till `in`.         |
|**Namn**     || Namnet på den bindnings parameter som representerar dokumentet i funktionen.  |
|**Databas** |**Databas** |Databasen som innehåller dokumentet.        |
|**Samling** |**Samling** | Namnet på den samling som innehåller dokumentet. |
|**identitet**    | **Id** | ID för det dokument som ska hämtas. Den här egenskapen stöder [bindnings uttryck](./functions-bindings-expressions-patterns.md). Ange inte egenskaperna för både **ID** och **sqlQuery** . Om du inte anger någon, hämtas hela samlingen. |
|**sqlQuery**  |**SqlQuery**  | En Azure Cosmos DB SQL-fråga som används för att hämta flera dokument. Egenskapen stöder runtime-bindningar, som i det här exemplet: `SELECT * FROM c where c.departmentId = {departmentId}`. Ange inte egenskaperna för både **ID** och **sqlQuery** . Om du inte anger någon, hämtas hela samlingen.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Namnet på den app-inställning som innehåller Azure Cosmos DB anslutnings strängen.        |
|**partitionKey**|**PartitionKey**|Anger partitionens nyckel värde för sökningen. Kan innehålla bindnings parametrar.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Inmatad användning

I C# och F# fungerar alla ändringar som görs i indatamängden via namngivna indataparametrar automatiskt när funktionen avslutas.

I JavaScript-funktioner görs inga uppdateringar automatiskt när funktionen avslutas. Använd i stället `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` för att göra uppdateringar. Se JavaScript-exemplet.

## <a name="output"></a>Resultat

Med Azure Cosmos DB utgående bindning kan du skriva ett nytt dokument till en Azure Cosmos DB-databas med hjälp av SQL-API: et.

## <a name="output---examples"></a>Utdata – exempel

Se språkspecifika exempel:

* [C#](#output---c-examples)
* [C#skript (. CSX)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)
* [Python](#output---python-examples)

Se även det [inmatade exemplet](#input---c-examples) som använder `DocumentClient`.

[Hoppa över utdata-exempel](#output---attributes)

### <a name="output---c-examples"></a>Utdata – C# exempel

Det här avsnittet innehåller följande exempel:

* Köa utlösare, skriva ett dokument
* Köa utlösare, skriva dokument med IAsyncCollector

Exemplen refererar till en enkel `ToDoItem`s typ:

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

[Hoppa över utdata-exempel](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Köa utlösare, skriva ettC#dokument ()

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som lägger till ett dokument i en databas med hjälp av data som anges i meddelande från Queue Storage.

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

[Hoppa över utdata-exempel](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Köa utlösare, skriva dokument medC#IAsyncCollector ()

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som lägger till en samling dokument i en databas med hjälp av data som finns i ett meddelande-JSON för köer.

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

[Hoppa över utdata-exempel](#output---attributes)

### <a name="output---c-script-examples"></a>Exempel på C# utdata-skript

Det här avsnittet innehåller följande exempel:

* Köa utlösare, skriva ett dokument
* Köa utlösare, skriva dokument med IAsyncCollector

[Hoppa över utdata-exempel](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Köa utlösare, skriva ettC# dokument (skript)

I följande exempel visas en Azure Cosmos DB utgående bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en indatatyps bindning för en kö som tar emot JSON i följande format:

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

Här är bindnings data i *Function. JSON* -filen:

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

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är C# skript koden:

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köa utlösare, skriva dokument med IAsyncCollector

Om du vill skapa flera dokument kan du binda till `ICollector<T>` eller `IAsyncCollector<T>` där `T` är en av de typer som stöds.

Det här exemplet refererar till en enkel `ToDoItem`s typ:

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

Här är function. JSON-filen:

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

Här är C# skript koden:

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

[Hoppa över utdata-exempel](#output---attributes)

### <a name="output---javascript-examples"></a>Utdata – JavaScript-exempel

I följande exempel visas en Azure Cosmos DB utgående bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en indatatyps bindning för en kö som tar emot JSON i följande format:

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

Här är bindnings data i *Function. JSON* -filen:

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

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

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

[Hoppa över utdata-exempel](#output---attributes)

### <a name="output---f-examples"></a>Utdata – F# exempel

I följande exempel visas en Azure Cosmos DB utgående bindning i en *Function. JSON* -fil och en [ F# funktion](functions-reference-fsharp.md) som använder bindningen. Funktionen använder en indatatyps bindning för en kö som tar emot JSON i följande format:

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

Här är bindnings data i *Function. JSON* -filen:

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
I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är F# koden:

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

Det här exemplet kräver en `project.json`-fil som anger `FSharp.Interop.Dynamic` och `Dynamitey` NuGet-beroenden:

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

Information om hur du lägger till en `project.json`-fil finns i [ F# paket hantering](functions-reference-fsharp.md#package).

### <a name="output---java-examples"></a>Utdata – Java-exempel

* [Köa utlösare, Spara meddelande till databas via retur värde](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-utlösare, spara ett dokument till databasen via retur värde](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-utlösare, spara ett dokument till databasen via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-utlösare, spara flera dokument till databasen via OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


#### <a name="queue-trigger-save-message-to-database-via-return-value-java"></a>Köa utlösare, Spara meddelande till databas via retur värde (Java)

I följande exempel visas en Java-funktion som lägger till ett dokument i en databas med data från ett meddelande i Queue Storage.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```

#### <a name="http-trigger-save-one-document-to-database-via-return-value-java"></a>HTTP-utlösare, spara ett dokument till databasen via retur värde (Java)

I följande exempel visas en Java-funktion vars signatur är kommenterad med ```@CosmosDBOutput``` och har returnerat värde av typen ```String```. JSON-dokumentet som returnerades av funktionen skrivs automatiskt till motsvarande CosmosDB-samling.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

#### <a name="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>HTTP-utlösare, spara ett dokument till databasen via OutputBinding (Java)

I följande exempel visas en Java-funktion som skriver ett dokument till CosmosDB via en ```OutputBinding<T>``` utdataparameter. Observera att i den här installationen är det den ```outputItem``` parameter som måste kommenteras med ```@CosmosDBOutput```, inte funktions-signaturen. Med hjälp av ```OutputBinding<T>``` kan din funktion dra nytta av bindningen för att skriva dokumentet till CosmosDB samtidigt som du också tillåter att ett annat värde returneras till funktions anrop, till exempel ett JSON-eller XML-dokument.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

#### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>HTTP-utlösare, spara flera dokument till databasen via OutputBinding (Java)

I följande exempel visas en Java-funktion som skriver flera dokument till CosmosDB via en ```OutputBinding<T>``` utdataparameter. Observera att i den här installationen är det den ```outputItem``` parameter som måste kommenteras med ```@CosmosDBOutput```, inte funktions-signaturen. Parametern output ```outputItem``` innehåller en lista över ```ToDoItem``` objekt som mallens parameter typ. Med hjälp av ```OutputBinding<T>``` kan din funktion dra nytta av bindningen för att skriva dokumenten till CosmosDB samtidigt som du också tillåter att ett annat värde returneras till funktions anrop, till exempel ett JSON-eller XML-dokument.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@CosmosDBOutput` kommentar för parametrar som ska skrivas till Cosmos dB.  Kommentar parameter typen ska vara ```OutputBinding<T>```, där T är antingen en ursprunglig Java-typ eller en POJO.

### <a name="output---python-examples"></a>Utdata – python-exempel

I följande exempel visas hur du skriver ett dokument till en Azure CosmosDB-databas som utdata till en funktion.

Bindnings definitionen definieras i *Function. JSON* där *typ* har angetts till `cosmosDB`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Om du vill skriva till databasen skickar du ett dokument objekt till `set` metoden för databas parametern.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

## <a name="output---attributes"></a>Utdata-attribut

Använd attributet [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar databasens namn och samlings namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utdata-konfiguration](#output---configuration). Här är ett exempel på ett `CosmosDB`-attribut i en metodsignatur:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Ett fullständigt exempel finns i utdata- C# exempel.

## <a name="output---configuration"></a>Utdata-konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `CosmosDB`-attributet.

|function. JSON-egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ**     || Måste anges till `cosmosDB`.        |
|**riktning**     || Måste anges till `out`.         |
|**Namn**     || Namnet på den bindnings parameter som representerar dokumentet i funktionen.  |
|**Databas** | **Databas**|Databasen som innehåller den samling där dokumentet skapas.     |
|**Samling** |**Samling**  | Namnet på den samling där dokumentet skapas. |
|**createIfNotExists**  |**CreateIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när den inte finns. Standardvärdet är *false* eftersom nya samlingar skapas med reserverat data flöde, vilket innebär kostnads konsekvenser. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |När `CreateIfNotExists` är true definierar partitionens nyckel Sök väg för den skapade samlingen.|
|**collectionThroughput**|**CollectionThroughput**| När `CreateIfNotExists` är true definierar [data flödet](../cosmos-db/set-throughput.md) för den skapade samlingen.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Namnet på den app-inställning som innehåller Azure Cosmos DB anslutnings strängen.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utmatnings användning

Som standard skapas ett dokument i din databas när du skriver till Utdataparametern i funktionen. Det här dokumentet har ett automatiskt genererat GUID som dokument-ID. Du kan ange dokument-ID: t för utdatafilen genom att ange egenskapen `id` i JSON-objektet som skickas till Utdataparametern.

> [!Note]
> När du anger ID: t för ett befintligt dokument skrivs det över av det nya utdata-dokumentet.

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Enheten | Referens |
|---|---|
| CosmosDB | [Felkoder för CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Host. JSON-inställningar

I det här avsnittet beskrivs globala konfigurations inställningar som är tillgängliga för den här bindningen i version 2. x. Mer information om globala konfigurations inställningar i version 2. x finns i [Host. JSON-referens för Azure Functions version 2. x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|GatewayMode|Gateway|Anslutnings läget som används av funktionen vid anslutning till Azure Cosmos DBs tjänsten. Alternativen är `Direct` och `Gateway`|
|Protokoll|https|Anslutnings protokollet som används av funktionen vid anslutning till Azure Cosmos DBs tjänsten.  Läs [här om du vill ha en förklaring av båda lägena](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|Saknas|Lease-prefix som ska användas för alla funktioner i en app.|

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om databas bearbetning utan server med Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
