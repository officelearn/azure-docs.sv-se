---
title: Azure Cosmos DB-utdatabindning för funktioner 2.x
description: Lär dig att använda Azure Cosmos DB-utdatabindningen i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 636903c20e07f11a2fd919654cfaa62037171f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277770"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Azure Cosmos DB-utdatabindning för Azure Functions 2.x

Med Azure Cosmos DB-utdatabindningen kan du skriva ett nytt dokument till en Azure Cosmos DB-databas med SQL API.

Information om inställnings- och konfigurationsinformation finns i [översikten](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* [Köutlösare, skriv ett dokument](#queue-trigger-write-one-doc-c)
* [Köutlösare, skriva dokument med IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Exemplen refererar `ToDoItem` till en enkel typ:

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Köutlösare, skriv ett dokument

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till ett dokument i en databas med hjälp av data som tillhandahålls i meddelandet från kölagring.

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

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köutlösare, skriva dokument med IAsyncCollector

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till en samling dokument i en databas med hjälp av data som tillhandahålls i ett kömeddelande JSON.

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* [Köutlösare, skriv ett dokument](#queue-trigger-write-one-doc-c-script)
* [Köutlösare, skriva dokument med IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

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

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köutlösare, skriva dokument med IAsyncCollector

Om du vill skapa flera `ICollector<T>` `IAsyncCollector<T>` dokument `T` kan du binda till eller var är en av de typer som stöds.

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

Här är C# skriptkoden:

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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

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

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas hur du skriver ett dokument till en Azure CosmosDB-databas som utdata för en funktion.

Bindningsdefinitionen definieras i *function.json* `cosmosDB`där *typen* är inställd på .

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

Om du vill skriva till databasen `set` skickar du ett dokumentobjekt till metoden för databasparametern.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Köutlösare, spara meddelande i databasen via returvärde](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-utlösare, spara ett dokument i databasen via returvärde](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-utlösare, spara ett dokument i databasen via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-utlösare, spara flera dokument i databasen via OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Köutlösare, spara meddelande i databasen via returvärde

I följande exempel visas en Java-funktion som lägger till ett dokument i en databas med data från ett meddelande i Kölagring.

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
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP-utlösare, spara ett dokument i databasen via returvärde

I följande exempel visas en Java-funktion ```@CosmosDBOutput``` vars signatur är ```String```kommenterad med och har returvärde av typen . JSON-dokumentet som returneras av funktionen skrivs automatiskt till motsvarande CosmosDB-samling.

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

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP-utlösare, spara ett dokument i databasen via OutputBinding

I följande exempel visas en Java-funktion som skriver ett ```OutputBinding<T>``` dokument till CosmosDB via en utdataparameter. I det här ```outputItem``` exemplet måste parametern ```@CosmosDBOutput```kommenteras med , inte funktionssignaturen. Med ```OutputBinding<T>``` hjälp kan din funktion dra nytta av bindningen för att skriva dokumentet till CosmosDB samtidigt som du kan returnera ett annat värde till funktionsuppringaren, till exempel ett JSON- eller XML-dokument.

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

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP-utlösare, spara flera dokument i databasen via OutputBinding

I följande exempel visas en Java-funktion som skriver flera ```OutputBinding<T>``` dokument till CosmosDB via en utdataparameter. I det här ```outputItem``` exemplet kommenteras ```@CosmosDBOutput```parametern med , inte funktionssignaturen. Utdataparametern ```outputItem``` har ```ToDoItem``` en lista över objekt som mallparametertyp. Med ```OutputBinding<T>``` hjälp kan din funktion dra nytta av bindningen för att skriva dokumenten till CosmosDB samtidigt som du kan returnera ett annat värde till funktionsuppringaren, till exempel ett JSON- eller XML-dokument.

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

I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `@CosmosDBOutput` på parametrar som ska skrivas till Cosmos DB.  Parametertypen anteckning bör ```OutputBinding<T>```vara , där T antingen är en inbyggd Java-typ eller en POJO.

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

Attributets konstruktor tar databasnamnet och samlingsnamnet. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [Utdata - konfiguration](#configuration). Här är `CosmosDB` ett attributexempel i en metodsignatur:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Anteckningen `CosmosDBOutput` är tillgänglig för att skriva data till Cosmos DB. Du kan använda anteckningen på funktionen eller på en enskild funktionsparameter. När den används på funktionsmetoden är funktionens returvärde det som skrivs till Cosmos DB. Om du använder anteckningen med en parameter måste parameterns typ `OutputBinding<T>` `T` deklareras som en plats där en inbyggd Java-typ eller en POJO.If you use the annoteation with a parameter, the parameter's type must be declared as a where a native Java type or a POJO.

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `CosmosDB` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ**     | Saknas | Måste ställas `cosmosDB`in på .        |
|**riktning**     | Saknas | Måste ställas `out`in på .         |
|**Namn**     | Saknas | Namn på den bindningsparameter som representerar dokumentet i funktionen.  |
|**Databasename** | **DatabaseName**|Databasen som innehåller samlingen där dokumentet skapas.     |
|**collectionName (samlingsnamn)** |**CollectionName**  | Namnet på den samling där dokumentet skapas. |
|**createIfNotExists**  |**CreateIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när den inte finns. Standardvärdet är *falskt* eftersom nya samlingar skapas med reserverat dataflöde, vilket har kostnadskonsekvenser. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey (partitionKey)**|**PartitionKey** |När `CreateIfNotExists` är sant definierar den sökvägen till partitionsnyckeln för den skapade samlingen.|
|**insamlingGenomströmning**|**InsamlingGenomströmning**| När `CreateIfNotExists` är sant definierar den [dataflödet](../cosmos-db/set-throughput.md) för den skapade samlingen.|
|**anslutningStringInställning**    |**ConnectionStringSetting** |Namnet på appinställningen som innehåller din Azure Cosmos DB-anslutningssträng.        |
|**preferredLocations**| **PreferredLocations**| (Valfritt) Definierar önskade platser (regioner) för geo-replikerade databaskonton i Azure Cosmos DB-tjänsten. Värdena ska kommaavskiljas. Till exempel "Östra USA, Södra centrala USA,Nordeuropa". |
|**användaMultipleWriteLocations**| **AnvändaMultipleWriteLocations**| (Valfritt) När den `true` är `PreferredLocations`inställd på tillsammans med kan den utnyttja skrivningar med flera regioner i Azure Cosmos [DB-tjänsten.](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

När du skriver till utdataparametern i funktionen skapas som standard ett dokument i databasen. Det här dokumentet har ett automatiskt genererat GUID som dokument-ID. Du kan ange dokument-ID för utdatadokumentet genom att ange egenskapen `id` i JSON-objektet som skickas till utdataparametern.

> [!Note]
> När du anger ID för ett befintligt dokument skrivs det över av det nya utdatadokumentet.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| CosmosDB | [CosmosDB-felkoder](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>värd.json-inställningar

I det här avsnittet beskrivs de globala konfigurationsinställningar som är tillgängliga för den här bindningen i version 2.x. Mer information om globala konfigurationsinställningar i version 2.x finns i [host.json-referens för Azure Functions version 2.x](functions-host-json.md).

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------|
|GatewayMode (GatewayMode)|Gateway|Anslutningsläget som används av funktionen när du ansluter till Azure Cosmos DB-tjänsten. Alternativen `Direct` är och`Gateway`|
|Protokoll|Https( https)|Anslutningsprotokollet som används av funktionen vid anslutning till Azure Cosmos DB-tjänsten.  Läs [här för en förklaring av båda lägena](../cosmos-db/performance-tips.md#networking)|
|leasa Förefix|Saknas|Prefix för lån som ska användas för alla funktioner i en app.|

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion när ett Azure Cosmos DB-dokument skapas eller ändras (utlösare)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Läsa ett Azure Cosmos DB-dokument (indatabindning)](./functions-bindings-cosmosdb-v2-input.md)