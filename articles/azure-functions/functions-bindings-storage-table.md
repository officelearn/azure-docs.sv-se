---
title: Azure Table storage-bindningar för Azure-funktioner
description: Förstå hur du använder Azure Table-lagringsbindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: edeafb5730f06dac22fd9919ca42ea388d5fd0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277185"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table storage-bindningar för Azure-funktioner

I den här artikeln beskrivs hur du arbetar med Azure Table-lagringsbindningar i Azure Functions. Azure Functions stöder indata- och utdatabindningar för Azure Table-lagring.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - Funktioner 1.x

Tabelllagringsbindningarna finns i [Paketet Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, version 2.x. Källkoden för paketet finns i [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-databasen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket - Funktioner 2.x och högre

Tabelllagringsbindningarna finns i [paketet Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet, version 3.x. Källkoden för paketet finns i [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub-databasen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Indata

Använd indatabindningen för Azure Table-lagring för att läsa en tabell i ett Azure Storage-konto.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>En enhet

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser en enda tabellrad. För varje post som infogas i tabellen utlöses funktionen.

Radnyckelvärdet {queueTrigger} anger att radnyckeln kommer från kömeddelandesträngen.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som `MyPoco` läser flera `TableEntity`tabellrader där klassen härstammar från .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable (CloudTable)

`IQueryable`stöds inte i [2-körningen Funktioner v2](functions-versions.md). Ett alternativ är `CloudTable` att använda en metodparameter för att läsa tabellen med hjälp av Azure Storage SDK. Här är ett exempel på en funktion som frågar en Azure Functions loggtabell:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Mer information om hur du använder CloudTable finns i [Komma igång med Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Om du försöker `CloudTable` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

### <a name="one-entity"></a>En enhet

I följande exempel visas en tabellinmatningsbindning i en *function.json-fil* och [C#-skriptkod](functions-reference-csharp.md) som använder bindningen. Funktionen använder en köutlösare för att läsa en enda tabellrad. 

*Filen function.json* anger `partitionKey` a `rowKey`och a . Värdet `rowKey` {queueTrigger} anger att radnyckeln kommer från kömeddelandesträngen.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Konfigurationsavsnittet](#input---configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

I följande exempel visas en tabellinmatningsbindning i en *function.json-fil* och [C#-skriptkod](functions-reference-csharp.md) som använder bindningen. Funktionen läser entiteter för en partitionsnyckel som anges i ett kömeddelande.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Konfigurationsavsnittet](#input---configuration) förklarar dessa egenskaper.

C#-skriptkoden lägger till en referens till Azure Storage SDK så att entitetstypen kan härleda från: `TableEntity`

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable (CloudTable)

`IQueryable`stöds inte i körtiden Funktioner för [versionerna 2.x och senare)](functions-versions.md). Ett alternativ är `CloudTable` att använda en metodparameter för att läsa tabellen med hjälp av Azure Storage SDK. Här är ett exempel på en funktion som frågar en Azure Functions loggtabell:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Mer information om hur du använder CloudTable finns i [Komma igång med Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Om du försöker `CloudTable` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en tabellinmatningsbindning i en *function.json-fil* och [JavaScript-kod](functions-reference-node.md) som använder bindningen. Funktionen använder en köutlösare för att läsa en enda tabellrad. 

*Filen function.json* anger `partitionKey` a `rowKey`och a . Värdet `rowKey` {queueTrigger} anger att radnyckeln kommer från kömeddelandesträngen.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Konfigurationsavsnittet](#input---configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

En tabellrad 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en HTTP-utlöst funktion som returnerar en lista över personobjekt som finns i en angiven partition i Tabelllagring. I exemplet extraheras partitionsnyckeln från http-vägen och tableName och anslutningen kommer från funktionsinställningarna. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

TableInput-anteckningen kan också extrahera bindningarna från json-brödtexten för begäran, som följande exempel visar.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

I följande exempel används filtret för att fråga efter personer med ett visst namn i en Azure-tabell och antalet möjliga matchningar begränsas till 10 resultat.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Indata - attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

 I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du följande attribut för att konfigurera en tabellindatabindning:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Attributets konstruktor tar tabellnamn, partitionsnyckel och radnyckel. Attributet kan användas `out` på en parameter eller på funktionens returvärde, vilket visas i följande exempel:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Du kan `Connection` ange egenskapen för att ange vilket lagringskonto som ska användas, som visas i följande exempel:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Ett fullständigt exempel finns i Exemplet Ingång - C#.

* [LagringKontoAttribut](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Här kan du ange vilket lagringskonto som ska användas. Konstruktorn tar namnet på en appinställning som innehåller en lagringsanslutningssträng. Attributet kan användas på parameter-, metod- eller klassnivå. I följande exempel visas klassnivå och metodnivå:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Det lagringskonto som ska användas bestäms i följande ordning:

* Egenskapen `Table` för `Connection` attributet.
* Attributet `StorageAccount` som tillämpas på `Table` samma parameter som attributet.
* Attributet `StorageAccount` som används för funktionen.
* Attributet `StorageAccount` som används för klassen.
* Standardlagringskontot för funktionsappen ("AzureWebJobsStorage"-appinställningen).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `@TableInput` på parametrar vars värde skulle komma från Tabelllagring.  Den här anteckningen kan användas med inbyggda Java-typer, `Optional<T>`POJOs eller nullable värden med .

---

## <a name="input---configuration"></a>Ingång - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `Table` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas `table`in på . Den här egenskapen ställs in automatiskt när du skapar bindningen i Azure-portalen.|
|**riktning** | Saknas | Måste ställas `in`in på . Den här egenskapen ställs in automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar tabellen eller entiteten i funktionskoden. | 
|**Tablename** | **Tablename** | Namnet på tabellen.| 
|**partitionKey (partitionKey)** | **PartitionKey** |Valfri. Partitionsnyckeln för den tabellentitet som ska läsas. Mer information om hur du använder den här egenskapen finns i [avsnittet](#input---usage) användning.| 
|**radNyckel** |**RowKey** | Valfri. Radnyckeln för den tabellentitet som ska läsas. Mer information om hur du använder den här egenskapen finns i [avsnittet](#input---usage) användning.| 
|**Ta** |**Ta** | Valfri. Det maximala antalet entiteter som ska läsas i JavaScript. Mer information om hur du använder den här egenskapen finns i [avsnittet](#input---usage) användning.| 
|**Filter** |**Filter** | Valfri. Ett OData-filteruttryck för tabellindata i JavaScript. Mer information om hur du använder den här egenskapen finns i [avsnittet](#input---usage) användning.| 
|**Anslutning** |**Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen Lagring som ska användas för den här bindningen. Om appinställningsnamnet börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till `connection` exempel anger "MyStorage" söker körtiden Funktioner efter en appinställning med namnet "MyStorage". Om du `connection` lämnar tom använder körtiden Funktioner standardanslutningssträngen för `AzureWebJobsStorage`lagring i appinställningen som heter .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Inmatning - användning

# <a name="c"></a>[C#](#tab/csharp)

* **Läsa en rad i**

  Ställ `partitionKey` `rowKey`in och . Öppna tabelldata med hjälp `T <paramName>`av en metodparameter . I `paramName` C#-skriptet anges det `name` värde som anges i egenskapen *för function.json*. `T`är vanligtvis en typ `ITableEntity` som implementerar eller härleder från `TableEntity`. Egenskaperna `filter` `take` och används inte i det här scenariot.

* **Läsa en eller flera rader**

  Öppna tabelldata med hjälp `IQueryable<T> <paramName>`av en metodparameter . I `paramName` C#-skriptet anges det `name` värde som anges i egenskapen *för function.json*. `T`måste vara en typ `ITableEntity` som implementerar eller härleder från `TableEntity`. Du kan `IQueryable` använda metoder för att göra alla filtrering som krävs. Egenskaperna `partitionKey` `rowKey`, `filter`, `take` och används inte i det här scenariot.  

  > [!NOTE]
  > `IQueryable`stöds inte i [2-körningen Funktioner v2](functions-versions.md). Ett alternativ är att [använda en CloudTable paramName-metodparameter](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) för att läsa tabellen med hjälp av Azure Storage SDK. Om du försöker `CloudTable` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

* **Läsa en rad i**

  Ställ `partitionKey` `rowKey`in och . Öppna tabelldata med hjälp `T <paramName>`av en metodparameter . I `paramName` C#-skriptet anges det `name` värde som anges i egenskapen *för function.json*. `T`är vanligtvis en typ `ITableEntity` som implementerar eller härleder från `TableEntity`. Egenskaperna `filter` `take` och används inte i det här scenariot.

* **Läsa en eller flera rader**

  Öppna tabelldata med hjälp `IQueryable<T> <paramName>`av en metodparameter . I `paramName` C#-skriptet anges det `name` värde som anges i egenskapen *för function.json*. `T`måste vara en typ `ITableEntity` som implementerar eller härleder från `TableEntity`. Du kan `IQueryable` använda metoder för att göra alla filtrering som krävs. Egenskaperna `partitionKey` `rowKey`, `filter`, `take` och används inte i det här scenariot.  

  > [!NOTE]
  > `IQueryable`stöds inte i [2-körningen Funktioner v2](functions-versions.md). Ett alternativ är att [använda en CloudTable paramName-metodparameter](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) för att läsa tabellen med hjälp av Azure Storage SDK. Om du försöker `CloudTable` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[Javascript](#tab/javascript)

Ange `filter` egenskaper `take` och egenskaper. Ställ inte `partitionKey` in `rowKey`eller . Få tillgång till indatatabellentiteten (eller entiteterna) med . `context.bindings.<BINDING_NAME>` De de deserialiserade objekten har `RowKey` och `PartitionKey` egenskaper.

# <a name="python"></a>[Python](#tab/python)

Tabelldata skickas till funktionen som en JSON-sträng. Av serialisera meddelandet genom `json.loads` att anropa som visas i indataexemplet . [example](#input)

# <a name="java"></a>[Java](#tab/java)

[Attributet TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) ger dig åtkomst till tabellraden som utlöste funktionen.

---

## <a name="output"></a>Resultat

Använd en Azure Table Storage-utdatabindning för att skriva entiteter till en tabell i ett Azure Storage-konto.

> [!NOTE]
> Den här utdatabindningen stöder inte uppdatering av befintliga entiteter. Använd `TableOperation.Replace` åtgärden [från Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) för att uppdatera en befintlig entitet.

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som använder en HTTP-utlösare för att skriva en enda tabellrad. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en tabellutdatabindning i en *function.json-fil* och [C#-skriptkod](functions-reference-csharp.md) som använder bindningen. Funktionen skriver flera tabellentiteter.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Konfigurationsavsnittet](#output---configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en tabellutdatabindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver flera tabellentiteter.

Här är *filen function.json:*

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Konfigurationsavsnittet](#output---configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas hur du använder dataproduktionen för tabelllagring. Bindningen `table` konfigureras i *function.json* genom att `name` `tableName`tilldela `partitionKey`värden `connection`till , , och :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Följande funktion genererar ett unikt UUI för `rowKey` värdet och behåller meddelandet i Tabelllagring.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en Java-funktion som använder en HTTP-utlösare för att skriva en enda tabellrad.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

I följande exempel visas en Java-funktion som använder en HTTP-utlösare för att skriva flera tabellrader.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Utdata - attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Attributets konstruktor tar tabellnamnet. Attributet kan användas `out` på en parameter eller på funktionens returvärde, vilket visas i följande exempel:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Du kan `Connection` ange egenskapen för att ange vilket lagringskonto som ska användas, som visas i följande exempel:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [Exempel på Utdata - C#.](#output)

Du kan `StorageAccount` använda attributet för att ange lagringskontot på klass-, metod- eller parameternivå. Mer information finns i [Input - attribut](#input---attributes-and-annotations).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du [TableOutput-anteckningen](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) på parametrar för att skriva värden till tabelllagring.

Se [exemplet för mer information](#output).

---

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `Table` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas `table`in på . Den här egenskapen ställs in automatiskt när du skapar bindningen i Azure-portalen.|
|**riktning** | Saknas | Måste ställas `out`in på . Den här egenskapen ställs in automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i funktionskoden och som representerar tabellen eller entiteten. Ställ `$return` in på att referera till funktionens returvärde.| 
|**Tablename** |**Tablename** | Namnet på tabellen.| 
|**partitionKey (partitionKey)** |**PartitionKey** | Partitionsnyckeln för den tabellentitet som ska skrivas. Mer information om hur du använder den här egenskapen finns i [avsnittet](#output---usage) användning.| 
|**radNyckel** |**RowKey** | Radnyckeln för den tabellentitet som ska skrivas. Mer information om hur du använder den här egenskapen finns i [avsnittet](#output---usage) användning.| 
|**Anslutning** |**Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen Lagring som ska användas för den här bindningen. Om appinställningsnamnet börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till `connection` exempel anger "MyStorage" söker körtiden Funktioner efter en appinställning med namnet "MyStorage". Om du `connection` lämnar tom använder körtiden Funktioner standardanslutningssträngen för `AzureWebJobsStorage`lagring i appinställningen som heter .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

# <a name="c"></a>[C#](#tab/csharp)

Öppna utdatatabellentiteten med `ICollector<T> paramName` `IAsyncCollector<T> paramName` hjälp `T` av `PartitionKey` `RowKey` en metodparameter eller där egenskaperna och finns där. Dessa egenskaper åtföljs ofta `ITableEntity` av att `TableEntity`implementera eller ärva .

Alternativt kan du `CloudTable` använda en metodparameter för att skriva till tabellen med hjälp av Azure Storage SDK. Om du försöker `CloudTable` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Öppna utdatatabellentiteten med `ICollector<T> paramName` `IAsyncCollector<T> paramName` hjälp `T` av `PartitionKey` `RowKey` en metodparameter eller där egenskaperna och finns där. Dessa egenskaper åtföljs ofta `ITableEntity` av att `TableEntity`implementera eller ärva . Värdet `paramName` anges i egenskapen `name` för *function.json*.

Alternativt kan du `CloudTable` använda en metodparameter för att skriva till tabellen med hjälp av Azure Storage SDK. Om du försöker `CloudTable` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[Javascript](#tab/javascript)

Öppna utdatahändelsen `context.bindings.<name>` `<name>` med hjälp av var `name` är det värde som anges i egenskapen *för function.json*.

# <a name="python"></a>[Python](#tab/python)

Det finns två alternativ för att mata ut ett tabelllagringsradmeddelande från en funktion:

- **Returvärde**: `name` Ange egenskapen i `$return` *function.json* till . Med den här konfigurationen sparas funktionens returvärde som en tabelllagringsrad.

- **Imperativ:** Skicka ett värde till den [inställda](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) metoden för parametern som deklarerats som [uttyp.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Värdet som `set` skickas till sparas som ett Event Hub-meddelande.

# <a name="java"></a>[Java](#tab/java)

Det finns två alternativ för att mata ut en tabelllagringsrad från en funktion med hjälp av [TableStorageOutput-anteckningen:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet)

- **Returvärde**: Genom att använda anteckningen till själva funktionen sparas funktionens returvärde som en tabelllagringsrad.

- **Tvingande**: Om du uttryckligen vill ange meddelandevärdet använder du [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)anteckningen på en viss parameter av typen , där `T` egenskaperna `PartitionKey` och `RowKey` finns. Dessa egenskaper åtföljs ofta `ITableEntity` av att `TableEntity`implementera eller ärva .

---

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Tabell | [Tabellfelkoder](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Tabell, Kö | [Lagringsfelkoder](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabell, Kö | [Troubleshooting](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) (Felsökning) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)
