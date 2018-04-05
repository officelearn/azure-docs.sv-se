---
title: Azure DB Cosmos-bindningar för funktioner
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
ms.openlocfilehash: ac869cc45d352bdeed16bb3ca926ec7a921d1f75
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="azure-cosmos-db-bindings-for-azure-functions"></a>Azure DB Cosmos-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) bindningar i Azure Functions. Azure Functions stöder utlösa indata och utdata bindningar för Azure Cosmos DB.

> [!NOTE]
> Den här bindningen hette ursprungligen DocumentDB. I funktion version 1.x, endast utlösaren har bytt namn Cosmos DB. behålla DocumentDB namnet indatabindning, utdata bindning och NuGet-paketet. I [funktioner version 2.x](functions-versions.md), bindningar och paketet också har bytt namn till Cosmos-DB. Den här artikeln använder 1.x-namn.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Paket

Cosmos-DB-bindningar för funktioner version 1.x finns i den [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet-paketet. För funktioner 2.x, paketet är [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB). Källkoden för bindningar finns i den [azure-webjobs-sdk-tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Utlösare

Azure Cosmos DB utlösaren använder den [Azure Cosmos DB ändra Feed](../cosmos-db/change-feed.md) att lyssna efter ändringar i partitioner. Ändra feeden publicerar infogningar och uppdateringar, inte borttagningar. 

## <a name="trigger---example"></a>Utlösaren - exempel

Finns i det språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Utlösaren - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som utlöser från en viss databas och samling.

```cs
    using System.Collections.Generic;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
            log.Info("Documents modified " + documents.Count);
            log.Info("First document Id " + documents[0].Id);
    }
```

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
|**Typ** || måste anges till `cosmosDBTrigger`. |
|**Riktning** || måste anges till `in`. Den här parametern anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** || Variabelnamnet som används i Funktionskoden som representerar en lista över dokument med ändringar. | 
|**connectionStringSetting**|**ConnectionStringSetting** | Namnet på en appinställning som innehåller den anslutningssträng som används för att ansluta till Azure DB som Cosmos-kontot som övervakas. |
|**databaseName**|**DatabaseName**  | Namnet på Azure DB som Cosmos-databasen med den samling som övervakas. |
|**Samlingsnamn** |**Samlingsnamn** | Namnet på samlingen som övervakas. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Valfritt) Namnet på en appinställning som innehåller anslutningssträngen till tjänsten som innehåller lease-samling. När inte har angetts i `connectionStringSetting` värdet används. Den här parametern anges automatiskt när bindning skapas i portalen. Anslutningssträngen för samlingen lån måste ha skrivbehörighet.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Valfritt) Namnet på databasen som innehåller den samling som används för att lagra lån. Om inte värdet, för den `databaseName` inställningen används. Den här parametern anges automatiskt när bindning skapas i portalen. |
|**leaseCollectionName** | **LeaseCollectionName** | (Valfritt) Namnet på den samling som används för att lagra lån. Om värdet inte, `leases` används. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Valfritt) Om värdet är `true`, samlingen lån skapas automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Valfritt) Definierar mängden begära enheter tilldelas när samlingen lån skapas. Den här inställningen är endast användas när `createLeaseCollectionIfNotExists` är inställd på `true`. Den här parametern anges automatiskt när bindning har skapats med hjälp av portalen.
| |**LeaseOptions** | Konfigurera alternativ genom att ange egenskaper i en instans av den [ChangeFeedHostOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.changefeedprocessor.changefeedhostoptions) klass.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösaren - användning

Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna. Både den samling som övervakas och den samling som innehåller lån måste vara tillgänglig för utlösaren ska fungera.

 >[!IMPORTANT]
 > Om flera funktioner har konfigurerats för att använda en Cosmos-DB-utlösare för samma samling, använder var och en av funktionerna som en dedikerad lease-samling. Annars utlöses endast en av funktionerna. 

Utlösaren anger inte om ett dokument har uppdateras eller infogas, bara innehåller själva dokumentet. Om du behöver hantera uppdateringar och infogningar på olika sätt kan du göra det genom att implementera värdefält för infogning eller update.

## <a name="input"></a>Indata

Azure DB som Cosmos-indatabindning hämtar ett eller flera Azure Cosmos DB dokument och skickar dem till Indataparametern för funktionen. Parametrarna för dokument-ID eller fråga kan bestämmas utifrån utlösaren som anropar funktionen. 

>[!NOTE]
> Inte använder Azure Cosmos DB indata eller utdata bindningar om du använder MongoDB-API för en Cosmos-DB-konto. Skadade data är möjligt.

## <a name="input---example-1"></a>Indata - exempel 1

Finns i det språkspecifika som läser ett dokument:

* [C#](#input---c-example)
* [C#-skript (.csx)](#input---c-script-example)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Indata - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som hämtar ett enskilt dokument från en viss databas och samling. 

Första, `Id` och `Maker` värden för en `CarReview` instans skickas till en kö. Cosmos-DB bindningen använder `Id` och `Maker` från kön meddelandet dokumentet kan hämtas från databasen.

```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace CosmosDB
    {
        public static class SingleEntry
        {
            [FunctionName("SingleEntry")]
            public static void Run(
                [QueueTrigger("car-reviews", Connection = "StorageConnectionString")] CarReview carReview,
                [DocumentDB("cars", "car-reviews", PartitionKey = "{maker}", Id= "{id}", ConnectionStringSetting = "CarReviewsConnectionString")] CarReview document,
                TraceWriter log)
            {
                log.Info( $"Selected Review - {document?.Review}"); 
            }
        }
    }
```

Här är den `CarReview` POCO:

 ```cs
    public class CarReview
    {
        public string Id { get; set; }
        public string Maker { get; set; }
        public string Description { get; set; }
        public string Model { get; set; }
        public string Image { get; set; }
        public string Review { get; set; }
    }
 ```

### <a name="input---c-script-example"></a>Indata - exempel på C#-skript

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

<a name="infsharp"></a>

### <a name="input---f-example"></a>Indata - F #-exempel

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

### <a name="input---javascript-example"></a>Indata - JavaScript-exempel

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

## <a name="input---example-2"></a>Indata - exempel 2

Finns i det språkspecifika som läser flera dokument:

* [C#](#input---c-example-2)
* [C#-skript (.csx)](#input---c-script-example-2)
* [JavaScript](#input---javascript-example-2)

### <a name="input---c-example-2"></a>Indata - C#-exempel 2

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som kör en SQL-fråga. Att använda den `SqlQuery` parameter, måste du installera den senaste betaversionen av `Microsoft.Azure.WebJobs.Extensions.DocumentDB` NuGet-paketet.

```csharp
    using System.Net;
    using System.Net.Http;
    using System.Collections.Generic;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;

    [FunctionName("CosmosDBSample")]
    public static HttpResponseMessage Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequestMessage req,
        [DocumentDB("test", "test", ConnectionStringSetting = "CosmosDB", SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")] IEnumerable<object> documents)
    {
        return req.CreateResponse(HttpStatusCode.OK, documents);
    }
```

### <a name="input---c-script-example-2"></a>Indata - C# skript exempel 2

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

### <a name="input---javascript-example-2"></a>Indata - JavaScript-exempel 2

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

## <a name="input---attributes"></a>Indata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribut.

Attributets konstruktorn har databasnamnet och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [konfigurationsavsnittet följande](#input---configuration). 

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `DocumentDB` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Typ**     || måste anges till `documentdb`.        |
|**Riktning**     || måste anges till `in`.         |
|**Namn**     || Parameterns namn bindning som representerar dokumentet i funktionen.  |
|**databaseName** |**DatabaseName** |Den databas som innehåller dokumentet.        |
|**Samlingsnamn** |**Samlingsnamn** | Namnet på den samling som innehåller dokumentet. |
|**id**    | **Id** | ID för dokumentet ska hämtas. Den här egenskapen stöder [bindningsuttryck](functions-triggers-bindings.md#binding-expressions-and-patterns). Du inte ange både den **id** och **sqlQuery** egenskaper. Om du inte anger någon hämtas hela samlingen. |
|**sqlQuery**  |**SqlQuery**  | En Azure Cosmos-Databasens SQL-fråga som används för att hämta flera dokument. Egenskapen stöder runtime-bindningar, som i följande exempel: `SELECT * FROM c where c.departmentId = {departmentId}`. Du inte ange både den **id** och **sqlQuery** egenskaper. Om du inte anger någon hämtas hela samlingen.|
|**Anslutning**     |**ConnectionStringSetting**|Namnet på appinställningen som innehåller Azure Cosmos DB anslutningssträngen.        |
|**PartitionKey**|**PartitionKey**|Anger partitionsnyckelvärde för sökningen. Kan omfatta bindande parametrar.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

I C# och F # funktioner, när funktionen avslutas, sparas ändringar som görs till dokumentet via namngivna indataparametrar automatiskt. 

I JavaScript-funktioner görs uppdateringar inte automatiskt vid utloggning av funktionen. Använd i stället `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` att göra uppdateringar. Finns det [JavaScript exempel](#input---javascript-example).

## <a name="output"></a>Utdata

Azure Cosmos DB utdata bindning kan skriva du ett nytt dokument till en Azure Cosmos-DB-databas. 

>[!NOTE]
> Inte använder Azure Cosmos DB indata eller utdata bindningar om du använder MongoDB-API för en Cosmos-DB-konto. Skadade data är möjligt.

## <a name="output---example"></a>Output - exempel

Finns i det språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Utdata - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som lägger till ett dokument till en databas med hjälp av informationen i meddelandet från kön lagring.

```cs
    using System;
    using Microsoft.Azure.WebJobs;

    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        document = new { Text = myQueueItem, id = Guid.NewGuid() };
    }
```

### <a name="output---c-script-example"></a>Utdata - exempel på C#-skript

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

### <a name="output---f-example"></a>Utdata - F #-exempel

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

### <a name="output---javascript-example"></a>Utdata - JavaScript-exempel

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
|**Typ**     || måste anges till `documentdb`.        |
|**Riktning**     || måste anges till `out`.         |
|**Namn**     || Parameterns namn bindning som representerar dokumentet i funktionen.  |
|**databaseName** | **DatabaseName**|Den databas som innehåller den samling där dokumentet har skapats.     |
|**Samlingsnamn** |**Samlingsnamn**  | Namnet på den samling där dokumentet har skapats. |
|**CreateIfNotExists**  |**CreateIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när det inte finns. Standardvärdet är *FALSKT* eftersom skapas nya samlingar med reserverat dataflöde, vilket har kostnad effekter. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**PartitionKey**|**PartitionKey** |När `CreateIfNotExists` är true, definierar partition Nyckelsökväg för samlingen som har skapats.|
|**collectionThroughput**|**CollectionThroughput**| När `CreateIfNotExists` är true, definierar den [genomströmning](../cosmos-db/set-throughput.md) för samlingen som har skapats.|
|**Anslutning**    |**ConnectionStringSetting** |Namnet på appinställningen som innehåller Azure Cosmos DB anslutningssträngen.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

När du skriver till Utdataparametern i din funktion skapas ett dokument i databasen. Det här dokumentet har en GUID som genererats automatiskt som dokument-ID. Du kan ange dokument-ID för utdatadokumentet genom att ange den `id` egenskap i JSON-objekt som överförts till output-parameter. 

> [!Note]  
> När du anger ID på ett befintligt dokument, hämtar den över det nya utdatadokumentet. 

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| CosmosDB | [Felkoder för CosmosDB](https://docs.microsoft.com/en-us/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en Snabbstart som använder en Cosmos-DB-utlösare](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Mer information om serverlösa databasen databearbetning med Cosmos DB](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
