---
title: "Azure DB Cosmos-bindningar för funktioner | Microsoft Docs"
description: "Förstå hur du använder Azure Cosmos DB utlösare och bindningar i Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.openlocfilehash: d05c0342e771e229a7175570ad227c4359980990
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="azure-cosmos-db-bindings-for-functions"></a>Azure DB Cosmos-bindningar för funktioner
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln förklarar hur du konfigurerar och koden Azure Cosmos DB bindningar i Azure Functions. Functions stöder utlösa indata och utdata bindningar för Azure Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Mer information om serverlösa databearbetning med Azure Cosmos DB finns [Azure Cosmos DB: serverlösa databasen datoranvändning med hjälp av Azure Functions](..\cosmos-db\serverless-computing-database.md).

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Azure DB Cosmos-utlösare

Azure Cosmos DB utlösaren använder den [Azure Cosmos DB ändra Feed](../cosmos-db/change-feed.md) att lyssna efter ändringar i partitioner. Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna.

Både den samling som övervakas och den samling som innehåller lån måste vara tillgänglig för utlösaren ska fungera.

Azure DB som Cosmos-utlösare stöder följande egenskaper:

|Egenskap  |Beskrivning  |
|---------|---------|
|**typ** | måste anges till `cosmosDBTrigger`. |
|**Namn** | Variabelnamnet som används i Funktionskoden som representerar en lista över dokument med ändringar. | 
|**riktning** | måste anges till `in`. Den här parametern anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**connectionStringSetting** | Namnet på en appinställning som innehåller den anslutningssträng som används för att ansluta till Azure DB som Cosmos-kontot som övervakas. |
|**databaseName** | Namnet på Azure DB som Cosmos-databasen med den samling som övervakas. |
|**Samlingsnamn** | Namnet på samlingen som övervakas. |
| **leaseConnectionStringSetting** | (Valfritt) Namnet på en appinställning som innehåller anslutningssträngen till tjänsten som innehåller lease-samling. När inte har angetts i `connectionStringSetting` värdet används. Den här parametern anges automatiskt när bindning skapas i portalen. |
| **leaseDatabaseName** | (Valfritt) Namnet på databasen som innehåller den samling som används för att lagra lån. Om inte värdet, för den `databaseName` inställningen används. Den här parametern anges automatiskt när bindning skapas i portalen. |
| **leaseCollectionName** | (Valfritt) Namnet på den samling som används för att lagra lån. Om värdet inte, `leases` används. |
| **createLeaseCollectionIfNotExists** | (Valfritt) Om värdet är `true`, samlingen lån skapas automatiskt när den inte redan finns. Standardvärdet är `false`. |
| **leaseCollectionThroughput** | (Valfritt) Definierar mängden begära enheter tilldelas när samlingen lån skapas. Den här inställningen är endast användas när `createLeaseCollectionIfNotExists` är inställd på `true`. Den här parametern anges automatiskt när bindning har skapats med hjälp av portalen.

>[!NOTE] 
>Anslutningssträngen som används för att ansluta till samlingen lån måste ha skrivbehörighet.

Dessa egenskaper kan ställas in på fliken integrera för funktionen i Azure-portalen eller genom att redigera den `function.json` projektfilen.

## <a name="using-an-azure-cosmos-db-trigger"></a>Med hjälp av en Azure DB som Cosmos-utlösare

Det här avsnittet innehåller exempel på hur du använder Azure DB som Cosmos-utlösare. I exemplen antar vi en utlösare metadata som ser ut som följande:

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
 
Ett exempel på hur du skapar en utlösare för Azure Cosmos DB från en funktionsapp i portalen finns [skapa en funktion som utlöses av Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md). 

### <a name="trigger-sample-in-c"></a>Utlösaren exemplet i C# #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>Utlösaren exemplet i JavaScript
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>DocumentDB-API-indatabindning
DocumentDB-API-indatabindning hämtar ett Azure DB som Cosmos-dokument och skickar den till namngivna Indataparametern för funktionen. Dokumentet ID kan bestämmas utifrån utlösaren som anropar funktionen. 

DocumentDB-API-indatabindning har följande egenskaper i *function.json*:

|Egenskap  |Beskrivning  |
|---------|---------|
|**Namn**     | Parameterns namn bindning som representerar dokumentet i funktionen.  |
|**typ**     | måste anges till `documentdb`.        |
|**databaseName** | Den databas som innehåller dokumentet.        |
|**Samlingsnamn**  | Namnet på den samling som innehåller dokumentet. |
|**ID**     | ID för dokumentet ska hämtas. Den här egenskapen stöder bindningar parametrar. Läs mer i [binda till anpassade inkommande egenskaper i ett uttryck för bindning](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression). |
|**sqlQuery**     | En Azure Cosmos-Databasens SQL-fråga som används för att hämta flera dokument. Frågan stöder runtime-bindningar, exempelvis i exempel: `SELECT * FROM c where c.departmentId = {departmentId}`.        |
|**anslutning**     |Namnet på appinställningen som innehåller Azure Cosmos DB anslutningssträngen.        |
|**riktning**     | måste anges till `in`.         |

Du kan inte ange både den **id** och **sqlQuery** egenskaper. Om ingen har angetts hämtas hela samlingen.

## <a name="using-a-documentdb-api-input-binding"></a>Med hjälp av en DocumentDB-API-indatabindning

* I C# och F # funktioner, när funktionen avslutas, sparas ändringar som görs till dokumentet via namngivna indataparametrar automatiskt. 
* I JavaScript-funktioner görs uppdateringar inte automatiskt vid utloggning av funktionen. Använd i stället `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` att göra uppdateringar. Finns det [JavaScript exempel](#injavascript).

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>Inkommande prov för enskilt dokument
Anta att du har följande DocumentDB API indata-bindning i den `bindings` matris med function.json:

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

Finns det språkspecifika-exempel som använder inkommande bindningen för att uppdatera dokumentets textvärde.

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Inkommande exemplet i C# #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Inkommande exemplet i F # #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>Inkommande exemplet i JavaScript

```javascript
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>Inkommande exemplet med flera dokument

Anta att du vill hämta flera dokument som anges av en SQL-fråga med en utlösare för kön för att anpassa Frågeparametrar. 

I det här exemplet, kö-utlösare innehåller en parameter `departmentId`. Ett kömeddelande för `{ "departmentId" : "Finance" }` returnerar alla poster för ekonomiavdelningen. Använd följande i *function.json*:

```
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

### <a name="input-sample-with-multiple-documents-in-c"></a>Inkommande exemplet med flera dokument i C#

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

### <a name="input-sample-with-multiple-documents-in-javascript"></a>Inkommande exemplet med flera dokument i JavaScript

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

## <a id="docdboutput"></a>DocumentDB-API-utdatabindning
DocumentDB-API-utdata bindning kan skriva du ett nytt dokument till en Azure Cosmos-DB-databas. Det har följande egenskaper i *function.json*:

|Egenskap  |Beskrivning  |
|---------|---------|
|**Namn**     | Parameterns namn bindning som representerar dokumentet i funktionen.  |
|**typ**     | måste anges till `documentdb`.        |
|**databaseName** | Den databas som innehåller den samling där dokumentet har skapats.     |
|**Samlingsnamn**  | Namnet på den samling där dokumentet har skapats. |
|**createIfNotExists**     | Ett booleskt värde som anger om samlingen skapas när det inte finns. Standardvärdet är *FALSKT*. Det beror på att skapas nya samlingar med reserverat dataflöde, vilket har kostnad effekter. Mer information finns i [sida med priser](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**anslutning**     |Namnet på appinställningen som innehåller Azure Cosmos DB anslutningssträngen.        |
|**riktning**     | måste anges till `out`.         |

## <a name="using-a-documentdb-api-output-binding"></a>Med hjälp av ett DocumentDB-API-utdatabindning
Det här avsnittet visar hur du använder din DocumentDB API-utdata bindningen i din funktionskoden.

När du skriver till Utdataparametern i din funktion skapas ett dokument i databasen. Det här dokumentet har en GUID som genererats automatiskt som dokument-ID. Du kan ange dokument-ID för utdata dokument genom att ange den `id` egenskap i JSON-objekt som överförts till output-parameter. 

>[!Note]  
>När du anger ID på ett befintligt dokument, hämtar den över det nya utdatadokumentet. 

Om du vill spara flera dokument kan du också binda till `ICollector<T>` eller `IAsyncCollector<T>` där `T` är en av typerna som stöds.

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>DocumentDB-API-utdata bindning exemplet
Anta att du har följande DocumentDB API-utdatabindning i den `bindings` matris med function.json:

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

Och du har en inkommande kö-bindning för en kö som tar emot JSON i följande format:

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Och du vill skapa Azure DB som Cosmos-dokument i följande format för varje post:

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Avsnittet språkspecifika exempel som använder denna utdata-bindning för att lägga till dokument i databasen.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [JavaScript](#outjavascript)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Utdata exemplet i C# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
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

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Utdata exemplet i F # #

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>Utdata exemplet i JavaScript

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
