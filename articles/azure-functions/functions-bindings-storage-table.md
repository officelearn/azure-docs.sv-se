---
title: Azure Table storage bindningar för Azure Functions
description: Förstå hur du använder Azure Table storage bindningar i Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: 51b9f7bfd25da7dfd4ae9038f8dab70e9232b944
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724589"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table storage bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Table storage bindningar i Azure Functions. Azure Functions stöder indata och utdata bindningar för Azure Table storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - fungerar 1.x

Table storage bindningar finns i den [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet version 2.x. Källkoden för paketet är i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Table storage bindningar finns i den [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet version 3.x. Källkoden för paketet är i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="input"></a>Indata

Använda Azure Table storage indatabindning för att läsa en tabell i ett Azure Storage-konto.

## <a name="input---example"></a>Indata - exempel

Finns i det språkspecifika:

* [C#-läsa en entitet](#input---c-example-1)
* [C#-läsa flera entiteter](#input---c-example-2)
* [C# skript - läsa en entitet](#input---c-script-example-1)
* [C# skript - läsa flera entiteter](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Indata - C#-exempel 1

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som läser en tabellrad. 

Raden värdet för nyckeln ”{queueTrigger}” visar att Radnyckeln kommer från kön Meddelandesträngen.

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

### <a name="input---c-example-2"></a>Indata - C#-exempel 2

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som läser flera rader. Observera att den `MyPoco` klassen härleds från `TableEntity`.

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

  > [!NOTE]
  > `IQueryable` stöds inte i den [Functions-runtime v2](functions-versions.md). Ett alternativ är att [använder en CloudTable paramName metodparameter](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) att läsa tabellen med hjälp av Azure Storage SDK: N. Om du försöker att binda till `CloudTable` och får ett felmeddelande, se till att du har en referens till [rätt Storage SDK: N version](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example-1"></a>Indata - C# skript exempel 1

I följande exempel visas en tabell indatabindning i en *function.json* fil och [C# skript för](functions-reference-csharp.md) kod som använder bindningen. Funktionen använder en utlösare för kön för att läsa en tabellrad. 

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

Här är skriptkod C#:

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

### <a name="input---c-script-example-2"></a>Indata - C# skript exempel 2

I följande exempel visas en tabell indatabindning i en *function.json* fil och [C# skript för](functions-reference-csharp.md) kod som använder bindningen. Funktionen läser entiteter för en partitionsnyckel som anges i ett kömeddelande.

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

C#-skriptkod läggs en referens till Azure Storage SDK: N så att entitetstypen kan härledas från `TableEntity`:

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

### <a name="input---f-example"></a>Indata - F #-exempel

I följande exempel visas en tabell indatabindning i en *function.json* fil och [F # skript för](functions-reference-fsharp.md) kod som använder bindningen. Funktionen använder en utlösare för kön för att läsa en tabellrad. 

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

I följande exempel visas en tabell indatabindning i en *function.json* fil- och [JavaScript-kod] (funktioner-referens-node.md) som använder bindningen. Funktionen använder en utlösare för kön för att läsa en tabellrad. 

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
 
I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en tabell indatabindning:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)

  Attributets konstruktorn har tabellnamn, partitionsnyckel och radnyckel. Den kan användas på en out-parameter eller returvärdet för funktionen, som visas i följande exempel:

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

  Du kan ange den `Connection` att ange storage-konto du använder, enligt följande exempel:

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

  En komplett exempel finns [indata - C#-exempel](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Ger ett annat sätt att ange storage-konto som ska användas. Konstruktorn får samma namn som en appinställning som innehåller en anslutningssträng för lagring. Attributet kan användas i parametern, metoden eller klassnivå. I följande exempel visas klassnivå och metoden:

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
* Den `StorageAccount` attribut som används i klassen.
* Standardkontot för lagring för funktionsapp (”AzureWebJobsStorage” appinställning).

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Table` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | måste anges till `table`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure-portalen.|
|**riktning** | Saknas | måste anges till `in`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar den tabell eller en entitet i funktionskoden. | 
|**tableName** | **tableName** | Namnet på tabellen.| 
|**PartitionKey** | **PartitionKey** |Valfri. Partitionsnyckeln för att läsa tabellentiteten. Finns det [användning](#input---usage) avsnittet vägledning om hur du använder den här egenskapen.| 
|**RowKey** |**RowKey** | Valfri. Raden nyckeln för att läsa tabellentiteten. Finns det [användning](#input---usage) avsnittet vägledning om hur du använder den här egenskapen.| 
|**ta** |**ta** | Valfri. Det maximala antalet enheter att läsa i JavaScript. Finns det [användning](#input---usage) avsnittet vägledning om hur du använder den här egenskapen.| 
|**Filter** |**Filter** | Valfri. En OData-filteruttrycket för tabellen indata i JavaScript. Finns det [användning](#input---usage) avsnittet vägledning om hur du använder den här egenskapen.| 
|**Anslutning** |**Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen för lagring för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Till exempel om du ställer in `connection` för ”MyStorage” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom Functions-runtime använder standard lagringsanslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

Table storage inkommande bindningen stöder följande scenarion:

* **Läs en rad i C# eller C#-skript**

  Ange `partitionKey` och `rowKey`. Komma åt informationen med hjälp av en metodparameter `T <paramName>`. I C# skript `paramName` anges värdet i den `name` -egenskapen för *function.json*. `T` Vanligtvis är en typ som implementerar `ITableEntity` eller härleds från `TableEntity`. Den `filter` och `take` egenskaper inte används i det här scenariot. 

* **Läs en eller flera rader i C# eller C#-skript**

  Komma åt informationen med hjälp av en metodparameter `IQueryable<T> <paramName>`. I C# skript `paramName` anges värdet i den `name` -egenskapen för *function.json*. `T` måste vara en typ som implementerar `ITableEntity` eller härleds från `TableEntity`. Du kan använda `IQueryable` metoder för att utföra filtrering krävs. Den `partitionKey`, `rowKey`, `filter`, och `take` egenskaper inte används i det här scenariot.  

  > [!NOTE]
  > `IQueryable` stöds inte i den [Functions-runtime v2](functions-versions.md). Ett alternativ är att [använder en CloudTable paramName metodparameter](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) att läsa tabellen med hjälp av Azure Storage SDK: N. Om du försöker att binda till `CloudTable` och får ett felmeddelande, se till att du har en referens till [rätt Storage SDK: N version](#azure-storage-sdk-version-in-functions-1x).

* **Läs en eller flera rader i JavaScript**

  Ange den `filter` och `take` egenskaper. Konfigurerar inte `partitionKey` eller `rowKey`. Åtkomst till den inkommande tabell entitet (eller entiteter) med hjälp av `context.bindings.<name>`. Avserialiserat objekt har `RowKey` och `PartitionKey` egenskaper.

## <a name="output"></a>Resultat

Använd Azure Table storage utdata bindning skriva entiteter till en tabell i ett Azure Storage-konto.

> [!NOTE]
> Den här utdata bindningen stöder inte uppdatering befintliga entiteter. Använd den `TableOperation.Replace` åtgärden [från Azure Storage SDK: N](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) att uppdatera en befintlig entitet.   

## <a name="output---example"></a>Output - exempel

Finns i det språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Utdata - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som använder en HTTP-utlösare för att skriva en tabellrad. 

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

### <a name="output---c-script-example"></a>Utdata - exempel på C#-skript

I följande exempel visas en tabell bindning i en *function.json* fil och [C# skript för](functions-reference-csharp.md) kod som använder bindningen. Funktionen skriver flera tabellentiteter.

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

Här är skriptkod C#:

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

### <a name="output---f-example"></a>Utdata - F #-exempel

I följande exempel visas en tabell bindning i en *function.json* fil och [F # skript för](functions-reference-fsharp.md) kod som använder bindningen. Funktionen skriver flera tabellentiteter.

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

### <a name="output---javascript-example"></a>Utdata - JavaScript-exempel

I följande exempel visas en tabell bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen skriver flera tabellentiteter.

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

Attributets konstruktorn har tabellens namn. Den kan användas på ett `out` parametern eller returvärdet för funktionen som visas i följande exempel:

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

Du kan ange den `Connection` att ange storage-konto du använder, enligt följande exempel:

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

En komplett exempel finns [utdata - C#-exempel](#output---c-example).

Du kan använda den `StorageAccount` -attribut som anger storage-konto på klass, metoden eller parametern-nivå. Mer information finns i [indata - attribut](#input---attributes).

## <a name="output---configuration"></a>Output - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Table` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | måste anges till `table`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure-portalen.|
|**riktning** | Saknas | måste anges till `out`. Den här egenskapen anges automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i Funktionskoden som representerar den tabell eller enhet. Ange till `$return` att referera till returvärde för funktion.| 
|**tableName** |**tableName** | Namnet på tabellen.| 
|**PartitionKey** |**PartitionKey** | Partitionsnyckeln för tabellentiteten för att skriva. Finns det [användning](#output---usage) vägledning om hur du använder den här egenskapen.| 
|**RowKey** |**RowKey** | Raden nyckeln för tabellentiteten för att skriva. Finns det [användning](#output---usage) vägledning om hur du använder den här egenskapen.| 
|**Anslutning** |**Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen för lagring för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Till exempel om du ställer in `connection` för ”MyStorage” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom Functions-runtime använder standard lagringsanslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

Table storage utdata bindningen stöder följande scenarier:

* **Skriv en rad med valfritt språk**

  Få åtkomst till utdata tabellentiteten i C# och C# skript, med hjälp av en metodparameter som `out T paramName` eller funktionen returvärde. I C# skript `paramName` anges värdet i den `name` -egenskapen för *function.json*. `T` kan vara en serialiserbar typ. Om partitionsnyckel och radnyckel som tillhandahålls av den *function.json* filen eller `Table` attribut. Annars `T` måste vara en typ som innehåller `PartitionKey` och `RowKey` egenskaper. I det här scenariot `T` vanligtvis implementerar `ITableEntity` eller härleds från `TableEntity`, men det behöver inte.

* **Skriv en eller flera rader i C# eller C#**

  Få åtkomst till utdata tabellentiteten i C# och C# skript, med hjälp av en metodparameter `ICollector<T> paramName` eller `IAsyncCollector<T> paramName`. I C# skript `paramName` anges värdet i den `name` -egenskapen för *function.json*. `T` Anger schemat för de enheter som du vill lägga till. Normalt `T` härleds från `TableEntity` eller implementerar `ITableEntity`, men det behöver inte. Partitionsnyckeln och rad nyckeln värden i *function.json* eller `Table` Attributkonstruktorn används inte i det här scenariot.

  Ett alternativ är att använda en `CloudTable paramName` Metodparametern att skriva till tabellen med hjälp av Azure Storage SDK: N. Om du försöker att binda till `CloudTable` och får ett felmeddelande, se till att du har en referens till [rätt Storage SDK: N version](#azure-storage-sdk-version-in-functions-1x).

* **Skriv en eller flera rader i JavaScript**

  I JavaScript-funktioner, komma åt tabellen utdata med `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Tabell | [Felkoder för tabellen](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blobb, tabell, kö | [Felkoder för lagring](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blobb, tabell, kö | [Felsökning](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
