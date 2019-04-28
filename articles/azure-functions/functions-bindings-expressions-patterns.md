---
title: Azure Functions-bindningar uttryck och mönster
description: Lär dig att skapa olika Azure Functions-bindning uttryck baserat på vanliga mönster.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/20/2019
ms.author: v-junlch
ms.openlocfilehash: 0c1dbbae5e4be965f195b5ea4fc88b1bc5fb4f87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437879"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions bindning mönster för uttryck

En av de mest kraftfulla funktionerna i [utlösare och bindningar](./functions-triggers-bindings.md) är *bindning uttryck*. I den *function.json* fil och du kan använda uttryck som matchas till värden från olika källor i funktionsparametrar och kod.

De flesta uttryck identifieras genom att omsluta dem anger mellan klammerparenteser. Till exempel i en kö i utlösningsfunktion `{queueTrigger}` motsvarar meddelandetext för kön. Om den `path` -egenskapen för en blob-utdata bindningen är `container/{queueTrigger}` och funktionen som utlöses av ett kömeddelande `HelloWorld`, en blob med namnet `HelloWorld` skapas.

Typer av uttryck för bindning

* [Appinställningar](#binding-expressions---app-settings)
* [Filnamn för utlösare](#trigger-file-name)
* [Utlösaren metadata](#trigger-metadata)
* [JSON-nyttolaster](#json-payloads)
* [Nytt GUID](#create-guids)
* [Aktuellt datum och tid](#current-time)

## <a name="binding-expressions---app-settings"></a>Uttryck för bindning - appinställningar

Som bästa praxis, ska hemligheter och anslutningssträngar hanteras med appinställningar i stället för konfigurationsfiler. Detta begränsar åtkomsten till dessa hemligheter och gör det säkert att lagra filer som *function.json* i offentliga källa källkontrollsdatabaser.

Appinställningar är också användbara när du vill ändra konfigurationen som baseras på vilken miljö. Till exempel i en testmiljö kan du övervaka en annan kö eller blob storage-behållare.

App inställningen bindning uttryck identifieras på olika sätt från andra uttryck för bindning: de placeras i procenttecken snarare än av klammerparenteser. Till exempel om bindningssökväg för blob-utdata är `%Environment%/newblob.txt` och `Environment` app Inställningsvärdet är `Development`, en blob skapas i den `Development` behållare.

När en funktion körs lokalt, app inställningen värdena kommer från den *local.settings.json* fil.

Observera att den `connection` egenskap av utlösare och bindningar är ett specialfall och löser automatiskt värden som appinställningar utan procenttecken. 

I följande exempel är en Azure Queue Storage-utlösare som använder en appinställning `%input-queue-name%` att definiera kön ska utlösas på.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Du kan använda samma metod i klassbibliotek:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Filnamn för utlösare

Den `path` för en Blob utlösare kan vara ett mönster som kan du referera till namnet på den utlösande bloben i andra bindningar och funktionen kod. Mönstret kan även inkludera filtreringskriterier som anger vilka blobar kan utlösa ett funktionsanrop.

I den följande Blob-utlösare som binder, till exempel den `path` mönstret är `sample-images/{filename}`, vilket skapar en bindning uttryck med namnet `filename`:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

Uttrycket `filename` kan sedan användas i en utdatabindning för att ange namnet på bloben håller på att skapas:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Function-koden har åtkomst till den här samma värde med hjälp av `filename` som ett parameternamn:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Samma möjlighet att använda bindningen uttryck och mönster gäller attribut i klassbibliotek. I följande exempel attributet konstruktor parametrarna är samma `path` värden som föregående *function.json* exempel: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Du kan också skapa uttryck för delar av filnamnet, till exempel tillägget. Läs mer om hur du använder uttryck och mönster i Blob-sökvägen den [referens för Storage blob-bindning](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Utlösaren metadata

Ange ytterligare metadatavärden förutom datanyttolasten som tillhandahålls av en utlösare (t.ex innehållet i kömeddelandet som en funktion som utlöses av), många utlösare. Dessa värden kan användas som indataparametrar i C# och F# eller egenskaper på den `context.bindings` objekt i JavaScript. 

Till exempel stöder en utlösare för Azure Queue storage följande egenskaper:

* QueueTrigger - utlösa meddelandeinnehåll om en giltig sträng
* DequeueCount
* expirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Dessa metadatavärdena är tillgängliga i *function.json* filegenskaper. Anta exempelvis att du använder en kö-utlösare och kömeddelandet innehåller namnet på en blob som du vill läsa. I den *function.json* -fil som du kan använda `queueTrigger` metadata-egenskap i blob `path` egenskapen, som visas i följande exempel:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Metadata-egenskaperna för varje utlösare beskrivs i referensartikeln i fråga. Ett exempel finns i [kö utlösaren metadata](functions-bindings-storage-queue.md#trigger---message-metadata). Dokumentation är också tillgängligt i den **integrera** fliken i portalen, i den **dokumentation** avsnittet nedan konfigurationsområde bindning.  

## <a name="json-payloads"></a>JSON-nyttolaster

När en utlösare nyttolast är JSON, kan du referera till dess egenskaper i konfigurationen för andar bindningar i samma funktion och i funktionskoden.

I följande exempel visas den *function.json* -filen för en webhook-funktion som tar emot ett blobnamn i JSON: `{"BlobName":"HelloWorld.txt"}`. En Blob-indatabindning läser blob och HTTP utdata bindning returnerar blob-innehållet i HTTP-svaret. Observera att Blob-indatabindning hämtar blobnamnet genom att referera direkt till den `BlobName` egenskapen (`"path": "strings/{BlobName}"`)

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

För detta ska fungera C# och F#, behöver du en klass som definierar de fält som ska avserialiseras, som i följande exempel:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

I JavaScript utförs automatiskt JSON-deserialisering.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Punktnotation

Om vissa av egenskaperna i JSON-nyttolast finns objekt med egenskaper kan du referera till dem direkt med hjälp av punktnotation. Anta exempelvis att din JSON som ser ut så här:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Du kan gå direkt till `FileName` som `BlobName.FileName`. Med den här JSON-format du här är vad den `path` -egenskapen i föregående exempel skulle se ut:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

I C# behöver du två klasser:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Skapa GUID

Den `{rand-guid}` bindning uttrycket skapar ett GUID. Följande blob sökväg i en `function.json` skapar en blob med ett namn som liknar *50710cb5-84b9 - 4d 87 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Aktuell tid

Uttryck för bindning `DateTime` motsvarar `DateTime.UtcNow`. Följande blob sökväg i en `function.json` skapar en blob med ett namn som liknar *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Bindningen vid körning

I C# och andra .NET-språk, kan du använda ett viktigt bindning mönster, till skillnad från de deklarativa bindningarna i *function.json* och attribut. Tvingande bindning är användbar när bindande parametrar behöver beräknas vid körning i stället för design tidpunkt. Mer information finns i den [C#-utvecklarreferens](functions-dotnet-class-library.md#binding-at-runtime) eller [utvecklarreferens för C#-skript](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Med hjälp av Azure funktionsreturvärde](./functions-bindings-return-value.md)

<!-- Update_Description: link update -->