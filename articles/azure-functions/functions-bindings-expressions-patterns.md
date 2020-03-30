---
title: Azure Functions binder uttryck och mönster
description: Lär dig att skapa olika Azure Functions-bindningsuttryck baserat på vanliga mönster.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277653"
---
# <a name="azure-functions-binding-expression-patterns"></a>Bindningsuttrycksmönster för Azure Functions

En av de mest kraftfulla funktionerna [i triggers och bindningar](./functions-triggers-bindings.md) är *bindande uttryck*. I *filen function.json* och i funktionsparametrar och kod kan du använda uttryck som matchar till värden från olika källor.

De flesta uttryck kan identifieras genom att de omsluts av klammerparenteser. I en köutlösarefunktion `{queueTrigger}` matchas till exempel till kömeddelandetexten. Om `path` egenskapen för en blob-utdatabindning är `container/{queueTrigger}` `HelloWorld`och funktionen utlöses av ett kömeddelande skapas en blob med namnet. `HelloWorld`

Typer av bindande uttryck

* [Appinställningar](#binding-expressions---app-settings)
* [Filnamn för utlösare](#trigger-file-name)
* [Utlösarmetadata](#trigger-metadata)
* [JSON-nyttolaster](#json-payloads)
* [Ny GUID](#create-guids)
* [Aktuell dag och tid](#current-time)

## <a name="binding-expressions---app-settings"></a>Bindningsuttryck - appinställningar

Som bästa praxis bör hemligheter och anslutningssträngar hanteras med hjälp av appinställningar i stället för konfigurationsfiler. Detta begränsar åtkomsten till dessa hemligheter och gör det säkert att lagra filer som *function.json* i databaser för kontroll av offentlig källkod.

Appinställningar är också användbara när du vill ändra konfigurationen baserat på miljön. I en testmiljö kanske du till exempel vill övervaka en annan kö- eller bloblagringsbehållare.

Appinställningsbindningsuttryck identifieras på ett annat sätt än andra bindningsuttryck: de är insvepta i procenttecken i stället för klammerparenteser. Till exempel om blob utdata bindningssökvägen `%Environment%/newblob.txt` är och `Environment` appinställningsvärdet är `Development`, skapas en blob i behållaren. `Development`

När en funktion körs lokalt kommer appinställningsvärdena från filen *local.settings.json.*

Observera att `connection` egenskapen för utlösare och bindningar är ett specialfall och automatiskt löser värden som appinställningar, utan procenttecken. 

Följande exempel är en Azure Queue Storage-utlösare som använder en appinställning `%input-queue-name%` för att definiera kön som ska utlösas på.

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

För `path` en Blob-utlösare kan vara ett mönster som låter dig referera till namnet på den utlösande bloben i andra bindningar och funktionskod. Mönstret kan också innehålla filtreringsvillkor som anger vilka blobbar som kan utlösa en funktionsangerop.

I följande Blob-utlösarbindning `path` är `sample-images/{filename}`mönstret till exempel `filename`, vilket skapar ett bindningsuttryck med namnet :

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

Uttrycket `filename` kan sedan användas i en utdatabindning för att ange namnet på bloben som skapas:

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

Funktionskoden har åtkomst till `filename` samma värde med hjälp av som parameternamn:

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

Samma möjlighet att använda bindningsuttryck och mönster gäller för attribut i klassbibliotek. I följande exempel är attributkonstruktorparametrarna samma `path` värden som de föregående *function.json-exemplen:* 

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

Du kan också skapa uttryck för delar av filnamnet. I följande exempel utlöses funktionen endast på filnamn som matchar ett mönster:`anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Mer information om hur du använder uttryck och mönster i Blob-sökvägen finns i [referensen för storage blob-bindning](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Utlösarmetadata

Förutom den datanyttolast som tillhandahålls av en utlösare (till exempel innehållet i kömeddelandet som utlöste en funktion), ger många utlösare ytterligare metadatavärden. Dessa värden kan användas som indataparametrar i `context.bindings` C# och F# eller egenskaper på objektet i JavaScript. 

En Azure Queue storage-utlösare stöder till exempel följande egenskaper:

* QueueTrigger - utlöser meddelandeinnehåll om en giltig sträng
* DequeueCount (olikartade)
* ExpirationTime
* Id
* InsertionTime (InfogningStid)
* NextVisibleTime NästaVisibleTime
* PopReceipt (19900)

Dessa metadatavärden är tillgängliga i *function.json-filegenskaper.* Anta till exempel att du använder en köutlösare och kömeddelandet innehåller namnet på en blob som du vill läsa. I *filen function.json* kan `queueTrigger` du använda egenskapen `path` metadata i blob-egenskapen, som visas i följande exempel:

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

Information om metadataegenskaper för varje utlösare beskrivs i motsvarande referensartikel. Ett exempel finns i [metadata för köutlösare](functions-bindings-storage-queue-trigger.md#message-metadata). Dokumentation finns också på fliken **Integrera** i portalen, i avsnittet **Dokumentation** under konfigurationsområdet för bindning.  

## <a name="json-payloads"></a>JSON-nyttolaster

När en utlösarnyttolast är JSON kan du referera till dess egenskaper i konfigurationen för andra bindningar i samma funktion och i funktionskod.

I följande exempel visas *filen function.json* för en webhook-funktion som `{"BlobName":"HelloWorld.txt"}`får ett blob-namn i JSON: . En Blob-indatabindning läser bloben och HTTP-utdatabindningen returnerar blob-innehållet i HTTP-svaret. Observera att Blob-indatabindningen hämtar blob-namnet genom att referera direkt till egenskapen `BlobName` (`"path": "strings/{BlobName}"`)

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

För att detta ska fungera i C# och F#behöver du en klass som definierar de fält som ska avserialiseras, som i följande exempel:

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

I JavaScript utförs JSON-deserialisering automatiskt.

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

Om några av egenskaperna i din JSON-nyttolast är objekt med egenskaper kan du referera till dem direkt med hjälp av punktatering. Anta till exempel att din JSON ser ut så här:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Du kan referera `FileName` `BlobName.FileName`direkt till som . Med det här JSON-formatet `path` skulle det här ut för egenskapen i föregående exempel se ut:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

I C#, skulle du behöva två klasser:

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

## <a name="create-guids"></a>Skapa GUID:er

Bindningsuttrycket `{rand-guid}` skapar ett GUID. Följande blob-sökväg `function.json` i en fil skapar en blob med ett namn som *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Aktuell tid

Det bindande `DateTime` uttrycket `DateTime.UtcNow`matchas till . Följande blob-sökväg `function.json` i en fil skapar en blob med ett namn som *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Bindning vid körning

I C# och andra .NET-språk kan du använda ett tvingande bindningsmönster, i motsats till deklarativa bindningarna i *function.json* och attribut. Tvingande bindning är användbart när bindningsparametrar måste beräknas vid körning i stället för designtid. Mer information finns i [C#-utvecklarreferensen](functions-dotnet-class-library.md#binding-at-runtime) eller [referensen för C#-skriptutvecklare](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Med returvärdet för Azure-funktionen](./functions-bindings-return-value.md)
