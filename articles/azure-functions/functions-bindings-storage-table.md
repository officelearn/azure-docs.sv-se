---
title: Azure Table Storage-bindningar för Azure Functions
description: Lär dig hur du använder Azure Table Storage-bindningar i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: d647a643dafe50a5768261c2b09bf8c4154be469
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086248"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table Storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med lagrings bindningar i Azure Table i Azure Functions. Azure Functions stöder indata och utgående bindningar för Azure Table Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Tabellerna Storage-bindningar finns i [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Tabellerna Storage-bindningar finns i [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3. x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Indata

Använd data bindningen för Azure Table Storage för att läsa en tabell i ett Azure Storage-konto.

## <a name="input---example"></a>Indatamängds exempel

Se exempel språkspecifika:

* [C#Läs en entitet](#input---c-example---one-entity)
* [C#bind till IQueryable](#input---c-example---iqueryable)
* [C#bind till CloudTable](#input---c-example---cloudtable)
* [C#skript läsning en entitet](#input---c-script-example---one-entity)
* [C#skript bindning till IQueryable](#input---c-script-example---iqueryable)
* [C#skript bindning till CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>In- C# exempel – en entitet

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som läser en enskild tabell rad. 

Rad nyckel svärdet {queueTrigger} anger att rad nyckeln kommer från köns meddelande sträng.

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

### <a name="input---c-example---iqueryable"></a>Ingångs C# exempel – IQueryable

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som läser flera tabell rader. Observera att `MyPoco` klassen härleds från `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>In- C# exempel – CloudTable

`IQueryable`stöds inte i [Functions v2](functions-versions.md)-körningen. Ett alternativ är att använda en `CloudTable` metod parameter för att läsa tabellen med hjälp av Azure Storage SDK. Här är ett exempel på en 2. x-funktion som skickar en fråga till en Azure Functions logg tabell:

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

Mer information om hur du använder CloudTable finns i [komma igång med Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Exempel på C# inmatade skript – en entitet

I följande exempel visas en tabell indata-bindning i en *Function. JSON* -fil och [ C# skript](functions-reference-csharp.md) kod som använder bindningen. Funktionen använder en Queue-utlösare för att läsa en enskild tabell rad. 

Filen *Function. JSON* anger en `partitionKey` och en `rowKey`. `rowKey` Värdet {queueTrigger} anger att rad nyckeln kommer från köns meddelande sträng.

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

### <a name="input---c-script-example---iqueryable"></a>Exempel på C# inmatat skript-IQueryable

I följande exempel visas en tabell indata-bindning i en *Function. JSON* -fil och [ C# skript](functions-reference-csharp.md) kod som använder bindningen. Funktionen läser entiteter för en partitionsnyckel som anges i ett Queue-meddelande.

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

C# Skript koden lägger till en referens till Azure Storage SDK så att entitetstypen kan härledas från `TableEntity`:

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

### <a name="input---c-script-example---cloudtable"></a>Exempel på C# inmatade skript – CloudTable

`IQueryable`stöds inte i [Functions v2](functions-versions.md)-körningen. Ett alternativ är att använda en `CloudTable` metod parameter för att läsa tabellen med hjälp av Azure Storage SDK. Här är ett exempel på en 2. x-funktion som skickar en fråga till en Azure Functions logg tabell:

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

Mer information om hur du använder CloudTable finns i [komma igång med Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---f-example"></a>Indatamängds F# exempel

I följande exempel visas en tabell indata-bindning i en *Function. JSON* -fil och [ F# skript](functions-reference-fsharp.md) kod som använder bindningen. Funktionen använder en Queue-utlösare för att läsa en enskild tabell rad. 

Filen *Function. JSON* anger en `partitionKey` och en `rowKey`. `rowKey` Värdet {queueTrigger} anger att rad nyckeln kommer från köns meddelande sträng.

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

Här är den F# kod:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Exempel på inmatade JavaScript-skript

I följande exempel visas en tabell indata-bindning i en *Function. JSON* -fil och [JavaScript-kod](functions-reference-node.md) som använder bindningen. Funktionen använder en Queue-utlösare för att läsa en enskild tabell rad. 

Filen *Function. JSON* anger en `partitionKey` och en `rowKey`. `rowKey` Värdet {queueTrigger} anger att rad nyckeln kommer från köns meddelande sträng.

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

### <a name="input---java-example"></a>In-Java-exempel

I följande exempel visas en HTTP-utlöst funktion som returnerar det totala antalet objekt i en angiven partition i Table Storage.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>Indata - attribut
 
I [ C# klass bibliotek](functions-dotnet-class-library.md)använder du följande attribut för att konfigurera en tabell indatatyps bindning:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Attributets konstruktor tar tabellens namn, partitionsnyckel och rad nyckel. Den kan användas för en out-parameter eller i funktionens retur värde, som visas i följande exempel:

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

  Du kan ställa `Connection` in egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

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

  Ett fullständigt exempel finns i indatamängds C# -exempel.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Ger ett annat sätt att ange det lagrings konto som ska användas. Konstruktorn tar namnet på en app-inställning som innehåller en lagrings anslutnings sträng. Attributet kan användas på parametern, metoden eller klassnivå. I följande exempel visas klassen och metoden:

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

Lagrings kontot som ska användas fastställs i följande ordning:

* Den `Table` attributets `Connection` egenskapen.
* Den `StorageAccount` attribut som används i samma parameter som den `Table` attribut.
* Den `StorageAccount` attribut som används i funktionen.
* Den `StorageAccount` attribut som tillämpas på klassen.
* Standard lagrings kontot för app-inställningen ("AzureWebJobsStorage").

## <a name="input---java-annotations"></a>Inmatade Java-anteckningar

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@TableInput` anteckningen för parametrar vars värde kommer från Table Storage.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med\<valfria T >. 

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Table` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**type** | Saknas | Måste anges till `table`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure Portal.|
|**direction** | Saknas | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure Portal. |
|**name** | Saknas | Namnet på variabeln som representerar tabellen eller entiteten i funktions koden. | 
|**tableName** | **TableName** | Namnet på tabellen.| 
|**partitionKey** | **partitionKey** |Valfritt. Partitionsnyckel för den tabell entitet som ska läsas. I avsnittet [användning](#input---usage) finns information om hur du använder den här egenskapen.| 
|**rowKey** |**RowKey** | Valfritt. Rad nyckeln för den tabell entitet som ska läsas. I avsnittet [användning](#input---usage) finns information om hur du använder den här egenskapen.| 
|**take** |**Gå** | Valfritt. Det maximala antalet entiteter som ska läsas i Java Script. I avsnittet [användning](#input---usage) finns information om hur du använder den här egenskapen.| 
|**Synkroniseringsfilter** |**Synkroniseringsfilter** | Valfritt. Ett OData filter-uttryck för tabell indatatyp i Java Script. I avsnittet [användning](#input---usage) finns information om hur du använder den här egenskapen.| 
|**anslutning** |**anslutning** | Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel anger `connection` "unstorage" söker funktions körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna `AzureWebJobsStorage`app-inställningen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

Bindningen för tabell lagrings data har stöd för följande scenarier:

* **Läsa en rad i C# eller C# ett skript**

  Ange `partitionKey` och `rowKey`. Få åtkomst till tabell data med hjälp av en `T <paramName>`metod parameter. I C# skript `paramName` är det `name` värdet som anges i egenskapen för *Function. JSON*. `T`är vanligt vis en typ som implementerar `ITableEntity` eller härleder från. `TableEntity` Egenskaperna `filter` och`take` används inte i det här scenariot. 

* **Läs en eller flera rader i C# eller C# skriptet**

  Få åtkomst till tabell data med hjälp av en `IQueryable<T> <paramName>`metod parameter. I C# skript `paramName` är det `name` värdet som anges i egenskapen för *Function. JSON*. `T`måste vara en typ som implementerar `ITableEntity` eller härleds från `TableEntity`. Du kan använda `IQueryable` metoder för att utföra filtrering som krävs. Egenskaperna `partitionKey` ,`rowKey`, och`take` används inte i det här scenariot. `filter`  

  > [!NOTE]
  > `IQueryable`stöds inte i [Functions v2](functions-versions.md)-körningen. Ett alternativ är att [använda en CloudTable paramName-metod parameter](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) för att läsa tabellen med hjälp av Azure Storage SDK. Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

* **Läs en eller flera rader i Java Script**

  Ange egenskaperna `take`och. `filter` Ange `partitionKey` inte eller `rowKey`. Få åtkomst till entiteten för indatakälla (eller `context.bindings.<name>`entiteter) med hjälp av. De avserialiserade objekten har `RowKey` och `PartitionKey` egenskaper.

## <a name="output"></a>Output

Använd en Azure Table Storage utgående bindning för att skriva entiteter till en tabell i ett Azure Storage konto.

> [!NOTE]
> Den här utgående bindningen stöder inte uppdatering av befintliga entiteter. Använd åtgärden [från Azure Storage SDK](https://docs.microsoft.com/azure/cosmos-db/tutorial-develop-table-dotnet#delete-an-entity) för att uppdatera en befintlig entitet. `TableOperation.Replace`   

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som använder en http-utlösare för att skriva en enskild tabell rad. 

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

### <a name="output---c-script-example"></a>Resultat – exempel på C#-skript

I följande exempel visas en tabell utgående bindning i en *Function. JSON* -fil och [ C# skript](functions-reference-csharp.md) kod som använder bindningen. Funktionen skriver flera tabell enheter.

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

### <a name="output---f-example"></a>Utdata - F# exempel

I följande exempel visas en tabell utgående bindning i en *Function. JSON* -fil och [ F# skript](functions-reference-fsharp.md) kod som använder bindningen. Funktionen skriver flera tabell enheter.

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

Här är den F# kod:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Resultat – JavaScript-exempel

I följande exempel visas en tabell utgående bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver flera tabell enheter.

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

Använd [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar tabellens namn. Den kan användas för en `out` parameter eller i funktionens retur värde, som visas i följande exempel:

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

Du kan ställa `Connection` in egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

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

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

Du kan använda `StorageAccount` attributet för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i [indata-attribut](#input---attributes).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Table` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**type** | Saknas | Måste anges till `table`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure Portal.|
|**direction** | Saknas | Måste anges till `out`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure Portal. |
|**name** | Saknas | Variabel namnet som används i funktions koden som representerar tabellen eller entiteten. Ange till `$return` att referera till funktionens retur värde.| 
|**tableName** |**TableName** | Namnet på tabellen.| 
|**partitionKey** |**partitionKey** | Partitionsnyckel för den tabell entitet som ska skrivas. I [avsnittet användning](#output---usage) finns information om hur du använder den här egenskapen.| 
|**rowKey** |**RowKey** | Rad nyckeln för den tabell entitet som ska skrivas. I [avsnittet användning](#output---usage) finns information om hur du använder den här egenskapen.| 
|**anslutning** |**anslutning** | Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel anger `connection` "unstorage" söker funktions körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna `AzureWebJobsStorage`app-inställningen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

Bindningen för tabell lagrings utdata stöder följande scenarier:

* **Skriv en rad på valfritt språk**

  I C# och C# skript, kan du komma åt entiteten för utdata-tabellen med hjälp `out T paramName` av en metod parameter, till exempel eller funktionens retur värde. I C# skript `paramName` är det `name` värdet som anges i egenskapen för *Function. JSON*. `T`kan vara en serialiserbar typ om partitionsnyckel och rad nyckel anges av *Function. JSON* -filen eller `Table` -attributet. Annars måste vara en typ som innehåller `PartitionKey` och `RowKey` egenskaper. `T` I det här scenariot `T` `ITableEntity` implementeras eller härleds vanligt `TableEntity`vis från, men det behöver inte.

* **Skriv en eller flera rader i C# eller C# skriptet**

  I C# och C# skript, kan du komma åt entiteten för utdata-tabellen `ICollector<T> paramName` med `IAsyncCollector<T> paramName`hjälp av en metod parameter eller. I C# skript `paramName` är det `name` värdet som anges i egenskapen för *Function. JSON*. `T`Anger schemat för de entiteter som du vill lägga till. Är `T` vanligt vis härledda `TableEntity` från eller implementerar `ITableEntity`, men det behöver inte. Partitionsnyckel och rad nyckel värden i *Function. JSON* eller `Table` attributhierarkin används inte i det här scenariot.

  Ett alternativ är att använda en `CloudTable` metod parameter för att skriva till tabellen med hjälp av Azure Storage SDK. Om du försöker binda till `CloudTable` och få ett fel meddelande, se till att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x). Ett exempel på kod som binder till `CloudTable`finns i exemplen för inbindningar för [C#](#input---c-example---cloudtable) eller [ C# skript](#input---c-script-example---cloudtable) tidigare i den här artikeln.

* **Skriv en eller flera rader i Java Script**

  I JavaScript-funktioner får du åtkomst till tabellens utdata med hjälp av `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Tabell | [Tabell fel koder](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabell, kö | [Lagrings fel koder](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö | [Felsökning](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
