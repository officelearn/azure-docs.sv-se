---
title: Azure Functions Lagringstabellen bindningar | Microsoft Docs
description: "Förstå hur du använder Azure Storage-bindningar i Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
ms.openlocfilehash: 486b7c31c914ba7bb2d75e3f83ccf346a09104e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-storage-table-bindings"></a>Azure Functions lagring tabell bindningar
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln förklarar hur du konfigurerar och koden Azure Storage tabell bindningar i Azure Functions. Azure Functions stöder indata och utdata bindningar för Azure Storage-tabeller.

Tabellbindning Storage stöder följande scenarion:

* **Läs en enskild rad i en C# eller Node.js-funktion** – du kan ställa `partitionKey` och `rowKey`. Den `filter` och `take` egenskaper inte används i det här scenariot.
* **Läsa flera rader i en C#-funktion** -Functions-runtime ger en `IQueryable<T>` objektet som är bundet till tabellen. Typen `T` måste vara härledd från `TableEntity` eller implementerar `ITableEntity`. Den `partitionKey`, `rowKey`, `filter`, och `take` egenskaper inte används i det här scenariot; du kan använda den `IQueryable` objekt om du vill filtrera krävs. 
* **Läsa flera rader i en nod funktion** – ange den `filter` och `take` egenskaper. Konfigurerar inte `partitionKey` eller `rowKey`.
* **Skriv en eller flera rader i en C#-funktion** -Functions-runtime ger en `ICollector<T>` eller `IAsyncCollector<T>` bunden till tabellen där `T` anger schemat för de enheter som du vill lägga till. Normalt anger `T` härleds från `TableEntity` eller implementerar `ITableEntity`, men det behöver inte. Den `partitionKey`, `rowKey`, `filter`, och `take` egenskaper inte används i det här scenariot.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Lagring tabell indatabindning
Azure Storage-tabellen indatabindning kan du använda en tabell för lagring i din funktion. 

Lagring tabell indata för en funktion använder följande JSON-objekt i den `bindings` matris med function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

Observera följande: 

* Använd `partitionKey` och `rowKey` tillsammans för att läsa en enda entitet. De här egenskaperna är valfria. 
* `connection`måste innehålla namnet på en appinställning som innehåller en anslutningssträng för lagring. I Azure-portalen standardredigeraren i den **integrera** konfigurerar appinställningen för dig när du skapar en lagringsplats för kontot eller väljer en befintlig. Du kan också [konfigurera den här appen inställningen manuellt](functions-how-to-use-azure-function-app-settings.md#settings).  

<a name="inputusage"></a>

## <a name="input-usage"></a>Inkommande användning
I C#-funktioner, du binder till indatatabellen entitet (eller entiteter) med hjälp av en namngiven parameter i en funktionssignaturen som `<T> <name>`.
Där `T` är datatypen som du vill att deserialisera data till och `paramName` är det namn du angav i den [inkommande bindningen](#input). I Node.js-funktion, åtkomst till indatatabellen entitet (eller entiteter) med hjälp av `context.bindings.<name>`.

Indata kan avserialiseras i Node.js- eller C#-funktioner. Avserialiserat objekt har `RowKey` och `PartitionKey` egenskaper.

Du kan också binda till någon av följande typer i C#-funktioner, och Functions-runtime görs ett försök att deserialisera den informationen med hjälp av den typen:

* Typer som implementerar`ITableEntity`
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>Indata-exempel
Anta att du har följande function.json, som använder en utlösare för kön för att läsa en tabellrad. JSON anger `PartitionKey`  
 `RowKey`. `"rowKey": "{queueTrigger}"`Anger att Radnyckeln kommer från kön Meddelandesträngen.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
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
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Se exemplet språkspecifika som läser en enskild tabell entitet.

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Inkommande exemplet i C# #
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

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>Inkommande exemplet i F # #
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

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Inkommande exemplet i Node.js
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Lagringstabellen utdatabindning
Azure Storage tabellutdata bindning aktiverar tabell du skriver enheter till en i din funktion. 

Tabellen lagring utdata för en funktion använder följande JSON-objekt i den `bindings` matris med function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

Observera följande: 

* Använd `partitionKey` och `rowKey` tillsammans för att skriva en enda entitet. De här egenskaperna är valfria. Du kan också ange `PartitionKey` och `RowKey` när du skapar entitetsobjekt i funktionskoden.
* `connection`måste innehålla namnet på en appinställning som innehåller en anslutningssträng för lagring. I Azure-portalen standardredigeraren i den **integrera** konfigurerar appinställningen för dig när du skapar en lagringsplats för kontot eller väljer en befintlig. Du kan också [konfigurera den här appen inställningen manuellt](functions-how-to-use-azure-function-app-settings.md#settings). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Användning av utdata
I C#-funktioner, du binder till tabellutdata med hjälp av den namngivna `out` parameter i en funktionssignaturen som `out <T> <name>`, där `T` är datatypen som du vill serialisera data till och `paramName` är det namn du angav i den [utdatabindning](#output). I Node.js-funktion, du åtkomst till tabellen utdata med `context.bindings.<name>`.

Du kan serialisera objekt i Node.js- eller C#-funktioner. I C#-funktioner, kan du också binda till följande typer:

* Typer som implementerar`ITableEntity`
* `ICollector<T>`(för att skapa flera entiteter. Se [exempel](#outcsharp).)
* `IAsyncCollector<T>`(asynkrona versionen av `ICollector<T>`)
* `CloudTable`(med hjälp av Azure Storage SDK: N. Se [exempel](#readmulti).)

<a name="outputsample"></a>

## <a name="output-sample"></a>Exempel på utdata
Följande *function.json* och *run.csx* exempel visar hur du skriver flera tabellentiteter.

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
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Se exemplet språkspecifika som skapar flera tabellentiteter.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Utdata exemplet i C# #
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
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Utdata exemplet i F # #
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

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Utdata exemplet i Node.js
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

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>Exempel: Läsa flera tabellentiteter i C#  #
Följande *function.json* och C#-kodexempel läser entiteter för en partitionsnyckel som anges i kön meddelandet.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C#-kod lägger till en referens till Azure Storage SDK: N så att entitetstypen kan härledas från `TableEntity`.

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

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

