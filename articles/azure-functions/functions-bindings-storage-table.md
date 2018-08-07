---
title: Azure Table storage-bindningar för Azure Functions
description: Förstå hur du använder Azure Table storage-bindningar i Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: f42948f0f3acf1bacf6c80010489890f4b8d122b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523673"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Table storage-bindningar i Azure Functions. Azure Functions stöder indata och utdata bindningar för Azure Table storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Table storage-bindningar finns i den [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Table storage-bindningar finns i den [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 3.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="input"></a>Indata

Använda Azure Table storage indatabindningen för att läsa en tabell i ett Azure Storage-konto.

## <a name="input---example"></a>Indata - exempel

Se exempel språkspecifika:

* [C#-läsa en entitet](#input---c-example---one-entity)
* [C#-bindningar till IQueryable](#input---c-example---iqueryable)
* [C#-bindningar till CloudTable](#input---c-example---cloudtable)
* [C#-skript som läser en entitet](#input---c-script-example---one-entity)
* [C#-skript-bindningar till IQueryable](#input---c-script-example---iqueryable)
* [C#-skript-bindningar till CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example---one-entity"></a>Indata - C#-exempel – en entitet

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser en tabellrad. 

Rad nyckelvärdet ”{queueTrigger}” visar att Radnyckeln kommer från kön meddelande sträng.

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
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example---iqueryable"></a>Indata - C#-exempel – IQueryable

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser flera tabellrader. Observera att den `MyPoco` klassen härleds från `TableEntity`.

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
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="input---c-example---cloudtable"></a>Indata - C#-exempel – CloudTable

`IQueryable` stöds inte i den [v2 funktionskörningen](functions-versions.md). Ett alternativ är att använda en `CloudTable` Metodparametern för att läsa tabellen med hjälp av Azure Storage SDK. Här är ett exempel på en 2.x-funktion som frågar en Azure Functions log-tabell:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
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
            TraceWriter log)
        {
            log.Info($"C# Timer trigger function executed at: {DateTime.Now}");

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
                log.Info(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Läs mer om hur du använder CloudTable [komma igång med Azure Table storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Om du försöker binda till `CloudTable` och får ett felmeddelande, se till att du har en referens till [rätt Storage SDK version](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Indata - C#-skript-exempel – en entitet

I följande exempel visas en tabell indatabindning i en *function.json* fil och [C#-skript](functions-reference-csharp.md) kod som använder bindningen. Funktionen använder en kö-utlösare för att läsa en tabellrad. 

Den *function.json* filen anger en `partitionKey` och en `rowKey`. Den `rowKey` värdet ”{queueTrigger}” anger att Radnyckeln kommer från kön Meddelandesträngen.

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

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---iqueryable"></a>Indata - C#-Skriptexemplet - IQueryable

I följande exempel visas en tabell indatabindning i en *function.json* fil och [C#-skript](functions-reference-csharp.md) kod som använder bindningen. Funktionen läser entiteter för en partitionsnyckel som anges i ett kömeddelande.

Här är den *function.json* fil:

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

Den [configuration](#input---configuration) förklaras de här egenskaperna.

C#-skriptkoden läggs en referens till Azure Storage SDK så att entitetstypen kan härledas från `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---cloudtable"></a>Indata - C#-Skriptexemplet - CloudTable

`IQueryable` stöds inte i den [v2 funktionskörningen](functions-versions.md). Ett alternativ är att använda en `CloudTable` Metodparametern för att läsa tabellen med hjälp av Azure Storage SDK. Här är ett exempel på en 2.x-funktion som frågar en Azure Functions log-tabell:

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

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");

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
        log.Info(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Läs mer om hur du använder CloudTable [komma igång med Azure Table storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Om du försöker binda till `CloudTable` och får ett felmeddelande, se till att du har en referens till [rätt Storage SDK version](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---f-example"></a>Indata - F #-exempel

I följande exempel visas en tabell indatabindning i en *function.json* fil och [skriptu F #](functions-reference-fsharp.md) kod som använder bindningen. Funktionen använder en kö-utlösare för att läsa en tabellrad. 

Den *function.json* filen anger en `partitionKey` och en `rowKey`. Den `rowKey` värdet ”{queueTrigger}” anger att Radnyckeln kommer från kön Meddelandesträngen.

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

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är F #-kod:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Indata - JavaScript-exempel

I följande exempel visas en tabell indatabindning i en *function.json* fil och [JavaScript-kod](functions-reference-node.md) som använder bindningen. Funktionen använder en kö-utlösare för att läsa en tabellrad. 

Den *function.json* filen anger en `partitionKey` och en `rowKey`. Den `rowKey` värdet ”{queueTrigger}” anger att Radnyckeln kommer från kön Meddelandesträngen.

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

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes"></a>Indata - attribut
 
I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en indatabindning för tabellen:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)

  Attributets konstruktorn tar det tabellnamn, partitionsnyckel och radnyckel. Den kan användas på en out-parameter eller på det returnera värdet till funktionen, som visas i följande exempel:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Du kan ange den `Connection` egenskapen att ange storage-konto du använder, enligt följande exempel:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Ett komplett exempel finns i [indata - C#-exempel](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Innehåller ett annat sätt att ange konto för att använda. Konstruktorn tar namnet på en appinställning som innehåller en anslutningssträng för lagring. Attributet kan användas på parametern, metoden eller klassnivå. I följande exempel visas klassen och metoden:

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

Storage-konto du använder bestäms i följande ordning:

* Den `Table` attributets `Connection` egenskapen.
* Den `StorageAccount` attribut som används i samma parameter som den `Table` attribut.
* Den `StorageAccount` attribut som används i funktionen.
* Den `StorageAccount` attribut som tillämpas på klassen.
* Standardkontot för lagring för funktionsappen (”AzureWebJobsStorage” app-inställning).

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Table` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till `table`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure-portalen.|
|**riktning** | Saknas | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar tabell eller entitet i funktionskoden. | 
|**Tabellnamn** | **Tabellnamn** | Namnet på tabellen.| 
|**partitionKey** | **partitionKey** |Valfri. Partitionsnyckeln för entiteten tabellen att läsa. Se den [användning](#input---usage) avsnittet anvisningar om hur du använder den här egenskapen.| 
|**RowKey** |**RowKey** | Valfri. Radnyckel för entiteten tabellen att läsa. Se den [användning](#input---usage) avsnittet anvisningar om hur du använder den här egenskapen.| 
|**ta** |**ta** | Valfri. Det maximala antalet enheter för att läsa i JavaScript. Se den [användning](#input---usage) avsnittet anvisningar om hur du använder den här egenskapen.| 
|**Filter** |**Filter** | Valfri. En OData-filteruttryck för tabellen som indata i JavaScript. Se den [användning](#input---usage) avsnittet anvisningar om hur du använder den här egenskapen.| 
|**anslutning** |**anslutning** | Namnet på en appinställning som innehåller lagringsanslutningssträngen ska användas för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Exempel: Om du ställer in `connection` till ”MyStorage” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom funktionskörningen använder standard Storage anslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

Indatabindningen för Table storage har stöd för följande scenarier:

* **Läsa en rad i C# eller C#-skript**

  Ange `partitionKey` och `rowKey`. Komma åt informationen med hjälp av en metodparameter `T <paramName>`. I C#-skript, `paramName` har värdet som angetts i den `name` egenskapen för *function.json*. `T` Vanligtvis är en typ som implementerar `ITableEntity` eller härleds från `TableEntity`. Den `filter` och `take` egenskaper används inte i det här scenariot. 

* **Läsa en eller flera rader i C# eller C#-skript**

  Komma åt informationen med hjälp av en metodparameter `IQueryable<T> <paramName>`. I C#-skript, `paramName` har värdet som angetts i den `name` egenskapen för *function.json*. `T` måste vara en typ som implementerar `ITableEntity` eller härleds från `TableEntity`. Du kan använda `IQueryable` metoder för att göra någon filtrering krävs. Den `partitionKey`, `rowKey`, `filter`, och `take` egenskaper används inte i det här scenariot.  

  > [!NOTE]
  > `IQueryable` stöds inte i den [v2 funktionskörningen](functions-versions.md). Ett alternativ är att [använder en metodparameter för CloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) att läsa tabellen med hjälp av Azure Storage SDK. Om du försöker binda till `CloudTable` och får ett felmeddelande, se till att du har en referens till [rätt Storage SDK version](#azure-storage-sdk-version-in-functions-1x).

* **Läsa en eller flera rader i JavaScript**

  Ange den `filter` och `take` egenskaper. Konfigurerar inte `partitionKey` eller `rowKey`. Få åtkomst till den inkommande tabell (eller de entiteter) med hjälp av `context.bindings.<name>`. De avserialiserade objekt har `RowKey` och `PartitionKey` egenskaper.

## <a name="output"></a>Resultat

Använda en Azure Table storage-utdatabindning för att skriva entiteter till en tabell i ett Azure Storage-konto.

> [!NOTE]
> Den här utdatabindning har inte stöd för att uppdatera befintliga entiteter. Använd den `TableOperation.Replace` åtgärden [från Azure Storage SDK](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) att uppdatera en befintlig entitet.   

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

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
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Resultat – exempel på C#-skript

I följande exempel visas en tabell-utdatabindning i en *function.json* fil och [C#-skript](functions-reference-csharp.md) kod som använder bindningen. Funktionen skriver flera tabellenheter.

Här är den *function.json* fil:

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

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
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

### <a name="output---f-example"></a>Resultat – F #-exempel

I följande exempel visas en tabell-utdatabindning i en *function.json* fil och [skriptu F #](functions-reference-fsharp.md) kod som använder bindningen. Funktionen skriver flera tabellenheter.

Här är den *function.json* fil:

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

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är F #-kod:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Resultat – JavaScript-exempel

I följande exempel visas en tabell-utdatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver flera tabellenheter.

Här är den *function.json* fil:

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

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

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

## <a name="output---attributes"></a>Utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs).

Attributets konstruktorn tar tabellnamnet. Den kan användas på en `out` parametern eller returvärdet för funktionen som du ser i följande exempel:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Du kan ange den `Connection` egenskapen att ange storage-konto du använder, enligt följande exempel:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

Du kan använda den `StorageAccount` attribut för att ange storage-konto på klass, metod eller parametern-nivå. Mer information finns i [indata - attribut](#input---attributes).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Table` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till `table`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure-portalen.|
|**riktning** | Saknas | Måste anges till `out`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i Funktionskoden som representerar tabell eller enhet. Ange `$return` att referera till returvärde för funktion.| 
|**Tabellnamn** |**Tabellnamn** | Namnet på tabellen.| 
|**partitionKey** |**partitionKey** | Partitionsnyckeln för entiteten tabell att skriva. Se den [användning](#output---usage) anvisningar om hur du använder den här egenskapen.| 
|**RowKey** |**RowKey** | Radnyckel för entiteten tabell att skriva. Se den [användning](#output---usage) anvisningar om hur du använder den här egenskapen.| 
|**anslutning** |**anslutning** | Namnet på en appinställning som innehåller lagringsanslutningssträngen ska användas för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Exempel: Om du ställer in `connection` till ”MyStorage” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom funktionskörningen använder standard Storage anslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

Table storage utdata bindningen har stöd för följande scenarier:

* **Skriv en rad på alla språk**

  Få åtkomst till utdata-tabellentiteten i C# och C#-skript, genom att använda en metodparameter som `out T paramName` eller funktionen returvärde. I C#-skript, `paramName` har värdet som angetts i den `name` egenskapen för *function.json*. `T` kan vara valfri typ av serialiserbara om partitionsnyckel och radnyckel tillhandahålls av den *function.json* fil eller `Table` attribut. I annat fall `T` måste vara en typ som inkluderar `PartitionKey` och `RowKey` egenskaper. I det här scenariot `T` implementerar normalt `ITableEntity` eller härleds från `TableEntity`, men det behöver inte.

* **Skriv en eller flera rader i C# eller C#**

  Få åtkomst till utdata-tabellentiteten i C# och C#-skript, med hjälp av en metodparameter `ICollector<T> paramName` eller `IAsyncCollector<T> paramName`. I C#-skript, `paramName` har värdet som angetts i den `name` egenskapen för *function.json*. `T` Anger schemat för de entiteter som du vill lägga till. Normalt `T` härleds från `TableEntity` eller implementerar `ITableEntity`, men det behöver inte. Den partitionsnyckel och en rad viktiga värden i *function.json* eller `Table` attributkonstruktör används inte i det här scenariot.

  Ett alternativ är att använda en `CloudTable` Metodparametern för att skriva till tabellen med hjälp av Azure Storage SDK. Om du försöker binda till `CloudTable` och får ett felmeddelande, se till att du har en referens till [rätt Storage SDK version](#azure-storage-sdk-version-in-functions-1x). Ett exempel på kod som binder till `CloudTable`, finns i exemplen indatabindning för [C#](#input---c-example---cloudtable) eller [C#-skript](#input---c-script-example---cloudtable) tidigare i den här artikeln.

* **Skriv en eller flera rader i JavaScript**

  Komma åt tabellen utdata med i JavaScript-funktioner, `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Tabell | [Felkoder för tabell](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabell, kö | [Felkoder för lagring](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö | [Felsökning](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
