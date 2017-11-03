---
title: Azure Functions Blob Storage-bindningar | Microsoft Docs
description: "Förstå hur du använder Azure Storage-utlösare och bindningar i Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 79b9dbee89a4e33a1768343b9242d6b2b1118355
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Azure Functions Blob storage-bindningar
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln beskriver hur du konfigurerar och arbetar med Azure Blob storage bindningar i Azure Functions. Azure Functions stöder utlösaren indata och utdata bindningar för Azure Blob storage. Funktioner som är tillgängliga i alla bindningar finns [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> En [blob storage-konto](../storage/common/storage-create-storage-account.md#blob-storage-accounts) stöds inte. BLOB storage-utlösare och bindningar kräver ett allmänt lagringskonto. 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>BLOB storage-utlösare och bindningar

Med Azure Blob storage utlösaren anropas Funktionskoden när en ny eller uppdaterad blob har identifierats. Blobbinnehållet tillhandahålls som indata för funktionen.

Definiera ett blob storage utlösare med den **integrera** fliken i Functions-portalen. Portalen skapar följande definitionen i den **bindningar** avsnitt i *function.json*:

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

BLOB-indata och utdata bindningar definieras med hjälp av `blob` som bindningstypen:

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* Den `path` egenskapen stöder bindande uttryck och filterparametrarna. Se [namn mönster](#pattern).
* Den `connection` egenskapen måste innehålla namnet på en appinställning som innehåller en anslutningssträng för lagring. I Azure-portalen standardredigeraren i den **integrera** appinställningen du konfigurerar när du väljer ett lagringskonto.

> [!NOTE]
> När du använder en blob-utlösare på en plan för förbrukning, kan det finnas upp till en 10 minuters fördröjning vid bearbetningen av nya blobbar när en funktionsapp är inaktiv. När funktionen appen körs bearbetas blobbar omedelbart. Överväg att något av följande alternativ för att undvika den här första fördröjningen:
> - Använda en apptjänstplan med alltid på aktiverad.
> - Använd en annan funktion för att utlösa blob bearbetning, till exempel ett kömeddelande som innehåller blobbnamnet på. Ett exempel finns [kön utlösare med blob inkommande bindningen](#input-sample).

<a name="pattern"></a>

### <a name="name-patterns"></a>Namnet mönster
Du kan ange ett namn på mönster i blob i den `path` -egenskap som kan vara ett filter eller bindande uttryck. Se [bindande uttryck och mönster](functions-triggers-bindings.md#binding-expressions-and-patterns).

Till exempel för att filtrera till blobbar som börjar med strängen ”ursprungliga”, använder du följande definition. Den här sökvägen hittar en blob med namnet *ursprungliga Blob1.txt* i den *inkommande* behållare och värdet för den `name` variabeln i Funktionskoden är `Blob1`.

```json
"path": "input/original-{name}",
```

Använda två mönster för att binda till blob-filnamnet och filnamnstillägget separat. Den här sökvägen hittar också en blob med namnet *ursprungliga Blob1.txt*, och värdet för den `blobname` och `blobextension` variabler i Funktionskoden är *ursprungliga Blob1* och *txt*.

```json
"path": "input/{blobname}.{blobextension}",
```

Du kan begränsa filtypen för blobbar med ett fast värde för filnamnstillägget. Till exempel för att utlösa endast på PNG-filer, använder du följande mönster:

```json
"path": "samples/{name}.png",
```

Klammerparenteser är specialtecken i namnet mönster. Om du vill ange blob-namn som innehåller klammerparenteser i namnet escape du klammerparenteserna med två klammerparenteser. I följande exempel söker efter en blob med namnet *{20140101}-soundfile.mp3* i den *bilder* -behållaren och `name` variabelvärdet i Funktionskoden är *soundfile.mp3*. 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>Utlösaren metadata

Blob-utlösare innehåller flera metadataegenskaper för. De här egenskaperna kan användas som en del av bindningar uttryck i andra bindningar eller parametrar i din kod. Dessa värden har samma semantik som [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

- **BlobTrigger**. Skriv `string`. Den utlösande blobbsökvägen
- **URI**. Skriv `System.Uri`. Den blob-URI för den primära platsen.
- **Egenskaper för**. Skriv `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`. Blobens Systemegenskaper.
- **Metadata**. Skriv `IDictionary<string,string>`. Användardefinierade metadata för blob.

<a name="receipts"></a>

### <a name="blob-receipts"></a>BLOB kvitton
Azure Functions-runtime garanterar att inga utlösare blob-funktionen anropas mer än en gång för samma nya eller uppdaterade blob. Att fastställa om en viss blob-version har bearbetats, upprätthåller *blob kvitton*.

Azure Functions lagrar blob inleveranser i en behållare med namnet *webjobs-azure-värdar* i Azure storage-konto för din funktionsapp (definieras av appinställningen `AzureWebJobsStorage`). En blob-inleverans har följande information:

* Funktionen utlösta (”*&lt;funktionen appnamn >*. Funktioner.  *&lt;funktionsnamn >*”, till exempel:” MyFunctionApp.Functions.CopyBlob ”)
* Behållarens namn
* Blob-datatyp (”BlockBlob” eller ”PageBlob”)
* Blobbnamnet
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

Om du vill tvinga ombearbetning av en blob, ta bort blob-inleverans för blobben från den *webjobs-azure-värdar* behållaren manuellt.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Hantering av skadligt blobbar
När en blob utlösaren misslyckas åtgärden för en given blob Azure Functions försök som fungerar totalt 5 gånger som standard. 

Om alla 5 försök misslyckas Azure Functions läggs ett meddelande till en kö för lagring med namnet *webjobs-blobtrigger-poison*. Kömeddelande för skadligt BLOB är en JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet  *&lt;funktionen appnamn >*. Funktioner.  *&lt;funktionsnamn >*)
* BlobType (”BlockBlob” eller ”PageBlob”)
* ContainerName
* BlobName
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

### <a name="blob-polling-for-large-containers"></a>BLOB avsöker för stora behållare
Om blob-behållaren som övervakas innehåller fler än 10 000 blobbar, loggfiler funktioner runtime genomsökningar kan du titta på för nya eller ändrade BLOB. Den här processen är inte i realtid. En funktion kan hämta aktiveras inte förrän flera minuter eller längre efter blob skapas. Dessutom [lagring loggfiler skapas på ”bästa prestanda”](/rest/api/storageservices/About-Storage-Analytics-Logging) basis. Det är inte säkert att alla händelser fångas. Loggar under vissa förhållanden kan missas. Om du behöver snabbare och mer tillförlitlig blob-bearbetning kan du skapa en [kömeddelande](../storage/queues/storage-dotnet-how-to-use-queues.md) när du skapar blob. Använd sedan en [kö utlösaren](functions-bindings-storage-queue.md) i stället för en blob-trigger för att bearbeta blob.

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>Med hjälp av en blob-utlösare och indatabindning
Med .NET-funktioner, åtkomst till blob-data med en metodparameter `Stream paramName`. Här, `paramName` är det värde som du angav i den [konfiguration av utlösare](#trigger). Med Node.js-funktion, åtkomst till inkommande blob-data med hjälp av `context.bindings.<name>`.

I .NET, kan du binda till någon av typerna i listan nedan. Om används som en indatabindning vissa av dessa typer kräver en `inout` bindning riktning i *function.json*. Den här riktningen stöds inte av standardredigeraren, så du måste använda redigeraren.

* `TextReader`
* `Stream`
* `ICloudBlob`(kräver ”inout” bindning riktning)
* `CloudBlockBlob`(kräver ”inout” bindning riktning)
* `CloudPageBlob`(kräver ”inout” bindning riktning)
* `CloudAppendBlob`(kräver ”inout” bindning riktning)

Om texten blobbar förväntas, du kan också binda till en .NET `string` typen. Detta rekommenderas endast om blobbstorleken är liten, som hela blobbinnehållet läses in i minnet. Vanligtvis är det bättre att använda en `Stream` eller `CloudBlockBlob` typen.

## <a name="trigger-sample"></a>Utlösaren exempel
Anta att du har följande function.json som definierar en blob storage-utlösare:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

Se exemplet språkspecifika loggar innehållet i varje blob som läggs till i behållaren övervakade.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>BLOB-utlösare exemplen i C# #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Utlösaren exemplet i Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>Med hjälp av en blob-utdatabindning

I .NET-funktioner, bör du antingen genom att använda en `out string` parameter i funktionssignaturen eller Använd en av typerna i listan nedan. I Node.js-funktion, du åtkomst till blob utdata med `context.bindings.<name>`.

Du kan spara till någon av följande typer i .NET-funktioner:

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>Kön utlösare med blob inkommande och utgående exempel
Anta att du har följande function.json som definierar en [Queue Storage utlösaren](functions-bindings-storage-queue.md)blob-lagring indata och utdata för ett blob-lagring. Observera användningen av den `queueTrigger` metadataegenskapen. i blob ingående och utgående `path` egenskaper:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Se exemplet språkspecifika som kopierar inkommande blob till utdata-blob.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>Exempel för BLOB-bindning i C# #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out Stream myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Exempel för BLOB-bindning i Node.js

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

