---
title: Azure Functions bindnings uttryck och mönster
description: Lär dig att skapa olika Azure Functions bindnings uttryck baserat på vanliga mönster.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 9b9e39776e519a91a4464532e11e85da711087b3
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766256"
---
# <a name="azure-functions-binding-expression-patterns"></a>Mönster för Azure Functions bindnings uttryck

En av de mest kraftfulla funktionerna i [utlösare och bindningar](./functions-triggers-bindings.md) är *bindnings uttryck*. I *Function. JSON* -filen och i funktions parametrar och kod kan du använda uttryck som matchar värden från olika källor.

De flesta uttryck identifieras genom att de omges av klammerparenteser. I en kö utlösnings funktion kan `{queueTrigger}` till exempel matchas mot meddelande texten i kön. Om `path`-egenskapen för en BLOB-utgående bindning är `container/{queueTrigger}` och funktionen utlöses av ett Queue-meddelande `HelloWorld`skapas en blob med namnet `HelloWorld`.

Typer av bindnings uttryck

* [Appinställningar](#binding-expressions---app-settings)
* [Utlösarens fil namn](#trigger-file-name)
* [Utlös ande metadata](#trigger-metadata)
* [JSON-nyttolaster](#json-payloads)
* [Nytt GUID](#create-guids)
* [Aktuellt datum och aktuell tid](#current-time)

## <a name="binding-expressions---app-settings"></a>Bindnings uttryck – app-inställningar

Som bästa praxis bör hemligheter och anslutnings strängar hanteras med hjälp av appinställningar, i stället för konfigurationsfiler. Detta begränsar åtkomsten till dessa hemligheter och gör det säkert att lagra filer som *Function. JSON* i offentliga käll kontrolls databaser.

Appinställningar är också användbara när du vill ändra konfigurationen baserat på miljön. I en test miljö kan du till exempel vilja övervaka en annan kö eller behållare för blob-lagring.

Bindnings uttryck för program inställning identifieras annorlunda än andra bindnings uttryck: de omsluts av procent tecken i stället för klammerparenteser. Om du till exempel anger sökvägen för BLOB-utgående bindning `%Environment%/newblob.txt` och värdet för `Environment`-appens värde är `Development`skapas en BLOB i `Development` containern.

När en funktion körs lokalt kommer appens inställnings värden från den *lokala. Settings. JSON* -filen.

Observera att egenskapen `connection` för utlösare och bindningar är ett specialfall och automatiskt löser värden som appinställningar, utan procent tecken. 

I följande exempel är en Azure Queue Storage-utlösare som använder en app-inställning `%input-queue-name%` för att definiera kön som ska aktive ras.

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

Du kan använda samma metod i klass bibliotek:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Utlösarens fil namn

`path` för en BLOB-utlösare kan vara ett mönster som gör att du kan referera till namnet på den Utlös ande blobben i andra bindningar och funktions kod. Mönstret kan även innehålla filter villkor som anger vilka blobbar som kan utlösa ett funktions anrop.

I följande exempel för BLOB-utlösare är `path` mönstret `sample-images/{filename}`, vilket skapar ett bindnings uttryck med namnet `filename`:

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

Uttrycket `filename` kan sedan användas i en utgående bindning för att ange namnet på den blob som skapas:

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

Funktions koden har åtkomst till samma värde genom att använda `filename` som parameter namn:

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

Samma möjlighet att använda bindnings uttryck och mönster gäller för attribut i klass bibliotek. I följande exempel är attributhierarkin parametrarna samma `path` värden som föregående *funktion. JSON* -exempel: 

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

Du kan också skapa uttryck för delar av fil namnet. I följande exempel utlöses funktionen endast på fil namn som matchar ett mönster: `anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Mer information om hur du använder uttryck och mönster i BLOB Path-strängen finns i [bindnings referens för Storage BLOB](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Utlös ande metadata

Förutom den data nytto last som tillhandahålls av en utlösare (t. ex. innehållet i köobjektet som utlöste en funktion) tillhandahåller många utlösare ytterligare metadata-värden. Dessa värden kan användas som indataparametrar i C# och F# eller egenskaper för `context.bindings`-objektet i Java Script. 

En Azure Queue Storage-utlösare stöder till exempel följande egenskaper:

* QueueTrigger – utlöser meddelande innehåll om en giltig sträng
* DequeueCount
* expirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Dessa metadata-värden är tillgängliga i egenskaperna *Function. JSON* -fil. Anta till exempel att du använder en utlösare för kön och att Queue-meddelandet innehåller namnet på en blob som du vill läsa. I *Function. JSON* -filen kan du använda egenskapen `queueTrigger` metadata i egenskapen BLOB `path`, som du ser i följande exempel:

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

Information om metadata-egenskaper för varje utlösare beskrivs i motsvarande referens artikel. Ett exempel finns i [kö-utlösarens metadata](functions-bindings-storage-queue.md#trigger---message-metadata). Dokumentation finns också på fliken **integrera** i portalen i avsnittet **dokumentation** under området för bindnings konfiguration.  

## <a name="json-payloads"></a>JSON-nyttolaster

När en utlösare nytto Last är JSON kan du referera till dess egenskaper i konfigurationen för andra bindningar i samma funktion och i funktions kod.

I följande exempel visas *Function. JSON* -filen för en webhook-funktion som tar emot ett BLOB-namn i json: `{"BlobName":"HelloWorld.txt"}`. En BLOB-databindning läser bloben och HTTP-databindningen returnerar BLOB-innehållet i HTTP-svaret. Observera att BLOB-databindningen hämtar BLOB-namnet genom att referera direkt till `BlobName`-egenskapen (`"path": "strings/{BlobName}"`)

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

För att detta ska fungera C# i F#och behöver du en klass som definierar de fält som ska deserialiseras, som i följande exempel:

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

I Java Script utförs JSON-deserialisering automatiskt.

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

### <a name="dot-notation"></a>Punkt notation

Om några av egenskaperna i JSON-nyttolasten är objekt med egenskaper kan du referera till dem direkt med hjälp av punkt notation. Anta till exempel att din JSON ser ut så här:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Du kan referera direkt till `FileName` som `BlobName.FileName`. Med det här JSON-formatet ser du hur `path`-egenskapen i föregående exempel ser ut så här:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

I C#behöver du två klasser:

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

Ett GUID skapas i `{rand-guid}` binding-uttryck. Följande BLOB-sökväg i en `function.json`-fil skapar en blob med ett namn som *50710cb5-84b9-4d87-9d83-a03d6976a682. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Aktuell tid

Bindnings uttrycket `DateTime` matchar `DateTime.UtcNow`. Följande BLOB-sökväg i en `function.json`-fil skapar en blob med ett namn som *2018-02-16T17-59 -55z. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Bindning vid körning

I C# och andra .net-språk kan du använda ett tvingande bindnings mönster, till skillnad från deklarativ bindningar i *Function. JSON* och attribut. Tvingande bindning är användbart när bindnings parametrar måste beräknas vid körning i stället för design tid. Mer information finns i referens för [ C# utvecklare](functions-dotnet-class-library.md#binding-at-runtime) eller [ C# skript utvecklare](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Använda Azures funktions retur värde](./functions-bindings-return-value.md)
