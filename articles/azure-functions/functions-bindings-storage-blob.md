---
title: Azure Blob storage-bindningar för Azure Functions
description: Förstå hur du använder Azure Blob storage-utlösare och bindningar i Azure Functions.
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
ms.date: 02/12/2018
ms.author: tdykstra
ms.openlocfilehash: f74a44ed1b26458ad77e5de43a67a961aee70ec1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Blob storage bindningar i Azure Functions. Azure Functions stöder utlösa indata och utdata bindningar för BLOB. Artikeln innehåller ett avsnitt för varje bindning:

* [BLOB-utlösare](#trigger)
* [BLOB-indatabindning](#input)
* [BLOB-bindning för utdata](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Använd händelsen rutnätet utlösaren instead of-utlösare för Blob-lagring för endast blob-lagringskonton för hög skala eller för att undvika kall start fördröjningar. Mer information finns i [utlösaren](#trigger) avsnitt. 

## <a name="packages"></a>Paket

Blob storage-bindningar finns i den [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet. Källkoden för paketet är i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="trigger"></a>Utlösare

Blob storage-utlösare startar en funktion när en ny eller uppdaterad blob har identifierats. Blobbinnehållet tillhandahålls som indata för funktionen.

Den [händelse rutnätet utlösaren](functions-bindings-event-grid.md) har inbyggt stöd för [blob händelser](../storage/blobs/storage-blob-event-overview.md) och kan också användas för att starta en funktion när en ny eller uppdaterad blob har upptäckts. Ett exempel finns i [bildstorlek med händelsen rutnätet](../event-grid/resize-images-on-storage-blob-upload-event.md) kursen.

Använd händelsen rutnätet instead of-utlösare för Blob-lagring för följande scenarier:

* Endast BLOB storage-konton
* Hög skalning
* Kalla startfördröjningen

### <a name="blob-only-storage-accounts"></a>Endast BLOB storage-konton

[Endast BLOB storage-konton](../storage/common/storage-create-storage-account.md#blob-storage-accounts) stöds för blob-indata och utdata bindningar men inte för blob-utlösare. BLOB storage utlösare kräver ett allmänt lagringskonto.

### <a name="high-scale"></a>Hög skalning

Hög skalning löst kan definieras som behållare som har mer än 100 000 blobbar eller lagringskonton som har fler än 100 blob-uppdateringar per sekund.

### <a name="cold-start-delay"></a>Kalla startfördröjningen

Om appen funktionen finns på förbrukning planen, kan det vara upp till en 10 minuters fördröjning vid bearbetningen av nya blobbar om en funktionsapp är inaktiv. För att undvika fördröjningen kall-start kan du växla till en App Service-plan med alltid på aktiverat eller Använd en annan utlösare.

### <a name="queue-storage-trigger"></a>Queue storage utlösare

Ett annat alternativ för bearbetning av blobbar är Queue storage utlösaren utöver händelsen rutnätet, men den har inget inbyggt stöd för blob-händelser. Du skulle behöva skapa Kömeddelanden när du skapar eller uppdaterar blobbar. Ett exempel som förutsätter att du har gjort det, finns det [blob-indatabindning exemplet nedan](#input---example).

## <a name="trigger---example"></a>Utlösaren - exempel

Finns i det språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Utlösaren - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som skriver en logg när en blob läggs till eller uppdateras i den `samples-workitems` behållare.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Strängen `{name}` i utlösaren blobbsökvägen `samples-workitems/{name}` skapar en [bindande uttryck](functions-triggers-bindings.md#binding-expressions-and-patterns) som du kan använda i Funktionskoden åtkomst till namnet på den utlösande blobben. Mer information finns i [Blob-namnet mönster](#trigger---blob-name-patterns) senare i den här artikeln.

Mer information om den `BlobTrigger` attribut, se [utlösaren - attribut](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Utlösaren - exempel på C#-skript

I följande exempel visas en blob-utlösare bindning i en *function.json* fil och [C#-skript (.csx)](functions-reference-csharp.md) kod som använder bindningen. Funktionen skriver en logg när en blob läggs till eller uppdateras i den `samples-workitems` behållare.

Här är de bindande data den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Strängen `{name}` i utlösaren blobbsökvägen `samples-workitems/{name}` skapar en [bindande uttryck](functions-triggers-bindings.md#binding-expressions-and-patterns) som du kan använda i Funktionskoden åtkomst till namnet på den utlösande blobben. Mer information finns i [Blob-namnet mönster](#trigger---blob-name-patterns) senare i den här artikeln.

Mer information om *function.json* filegenskaper, finns det [Configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är C# skriptkod som binder till en `Stream`:

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Här är C# skriptkod som binder till en `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Utlösaren - JavaScript-exempel

I följande exempel visas en blob-utlösare bindning i en *function.json* fil och [JavaScript-kod](functions-reference-node.md) som använder bindningen. Funktionen skriver en logg när en blob läggs till eller uppdateras i den `samples-workitems` behållare.

Här är den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Strängen `{name}` i utlösaren blobbsökvägen `samples-workitems/{name}` skapar en [bindande uttryck](functions-triggers-bindings.md#binding-expressions-and-patterns) som du kan använda i Funktionskoden åtkomst till namnet på den utlösande blobben. Mer information finns i [Blob-namnet mönster](#trigger---blob-name-patterns) senare i den här artikeln.

Mer information om *function.json* filegenskaper, finns det [Configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>Utlösaren - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en blob-utlösare:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  Attributets konstruktorn har en sökvägssträng som anger att titta på behållaren och eventuellt en [blob namnmönstret](#trigger---blob-name-patterns). Här är ett exempel:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Du kan ange den `Connection` att ange storage-konto du använder, enligt följande exempel:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  En komplett exempel finns [utlösaren - C#-exempel](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Ger ett annat sätt att ange storage-konto som ska användas. Konstruktorn får samma namn som en appinställning som innehåller en anslutningssträng för lagring. Attributet kan användas i parametern, metoden eller klassnivå. I följande exempel visas klassnivå och metoden:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Storage-konto du använder bestäms i följande ordning:

* Den `BlobTrigger` attributets `Connection` egenskapen.
* Den `StorageAccount` attribut som används i samma parameter som den `BlobTrigger` attribut.
* Den `StorageAccount` attribut som används i funktionen.
* Den `StorageAccount` attribut som används i klassen.
* Standardkontot för lagring för funktionsapp (”AzureWebJobsStorage” appinställning).

## <a name="trigger---configuration"></a>Utlösaren - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `BlobTrigger` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | måste anges till `blobTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. Undantag anges i den [användning](#trigger---usage) avsnitt. |
|**Namn** | Saknas | Namnet på variabeln som representerar blob i funktionskoden. | 
|**Sökväg** | **BlobPath** |Behållare för övervakning.  Kan vara en [blob namnmönstret](#trigger-blob-name-patterns). | 
|**Anslutning** | **Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen för lagring för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Till exempel om du ställer in `connection` för ”MyStorage” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom Functions-runtime använder standard lagringsanslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.<br><br>Anslutningssträngen får inte vara för ett allmänt lagringskonto en [endast blob storage-konto](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösaren - användning

I C# och C#-skript, kan du använda följande parametertyper för utlösande blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* En POCO serialiserbara som JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> kräver ”inout” bindning `direction` i *function.json* eller `FileAccess.ReadWrite` i en C#-klassbiblioteket.

Bindning till `string`, `Byte[]`, eller POCO rekommenderas endast om blobbstorleken är liten, som hela blob innehållet är inlästa i minnet. Vanligtvis är det bättre att använda en `Stream` eller `CloudBlockBlob` typen. Mer information finns i [samtidighet och minnesanvändning](#trigger---concurrency-and-memory-usage) senare i den här artikeln.

I JavaScript, åt inkommande blobbdata med hjälp av `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Utlösaren - mönster för blob-namn

Du kan ange ett namn på mönster i blob i den `path` egenskap i *function.json* eller i den `BlobTrigger` Attributkonstruktorn. Namnmönstret kan vara en [filter eller bindande uttryck](functions-triggers-bindings.md#binding-expressions-and-patterns). Följande avsnitt innehåller exempel.

### <a name="get-file-name-and-extension"></a>Hämta filnamn och tillägg

I följande exempel visas hur du binda till blob-filnamnet och filnamnstillägget separat:

```json
"path": "input/{blobname}.{blobextension}",
```
Om blob heter *ursprungliga Blob1.txt*, värdena för den `blobname` och `blobextension` variabler i Funktionskoden är *ursprungliga Blob1* och *txt*.

### <a name="filter-on-blob-name"></a>Filtrera efter blobbnamnet

Följande exempel utlösare bara på blobbar i den `input` behållare som börjar med strängen ”ursprungliga-”:

```json
"path": "input/original-{name}",
```
 
Om blobbnamnet är *ursprungliga Blob1.txt*, värdet för den `name` variabeln i Funktionskoden är `Blob1`.

### <a name="filter-on-file-type"></a>Filtrera efter filtyp

I följande exempel utlöser bara på *.png* filer:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrera efter klammerparenteser i filnamn

Om du vill söka efter klammerparenteser i filnamn, escape-klammerparenteserna med hjälp av två klammerparenteser. Följande exempel filter för blob som innehåller klammerparenteser i namnet:

```json
"path": "images/{{20140101}}-{name}",
```

Om blob heter  *{20140101}-soundfile.mp3*, `name` variabelvärdet i Funktionskoden är *soundfile.mp3*. 

## <a name="trigger---metadata"></a>Utlösaren - metadata

Blob-utlösare innehåller flera metadataegenskaper för. De här egenskaperna kan användas som en del av bindande uttryck i andra bindningar eller parametrar i din kod. Dessa värden har samma semantik som den [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) typen.

|Egenskap   |Typ  |Beskrivning  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Sökvägen till den utlösande blobben.|
|`Uri`|`System.Uri`|Den blob-URI för den primära platsen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Blobens Systemegenskaper. |
|`Metadata` |`IDictionary<string,string>`|Användardefinierade metadata för blob.|

Följande C#-skript och JavaScript-exempel loggar du bara sökvägen till den utlösande blob, inklusive behållaren:

```csharp
public static void Run(string myBlob, string blobTrigger, TraceWriter log)
{
    log.Info($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Utlösaren - blob kvitton

Azure Functions-runtime garanterar att inga utlösare blob-funktionen anropas mer än en gång för samma nya eller uppdaterade blob. Att fastställa om en viss blob-version har bearbetats, upprätthåller *blob kvitton*.

Azure Functions lagrar blob inleveranser i en behållare med namnet *webjobs-azure-värdar* i Azure storage-konto för din funktionsapp (definieras av appinställningen `AzureWebJobsStorage`). En blob-inleverans har följande information:

* Funktionen utlösta (”*&lt;funktionen appnamn >*. Funktioner.  *&lt;funktionsnamn >*”, till exempel:” MyFunctionApp.Functions.CopyBlob ”)
* Behållarens namn
* Blob-datatyp (”BlockBlob” eller ”PageBlob”)
* Blobbnamnet
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

Om du vill tvinga ombearbetning av en blob, ta bort blob-inleverans för blobben från den *webjobs-azure-värdar* behållaren manuellt.

## <a name="trigger---poison-blobs"></a>Utlösaren - skadligt blobbar

När en blob utlösaren misslyckas åtgärden för en given blob Azure Functions försök som fungerar totalt 5 gånger som standard. 

Om alla 5 försök misslyckas Azure Functions läggs ett meddelande till en kö för lagring med namnet *webjobs-blobtrigger-poison*. Kömeddelande för skadligt BLOB är en JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet  *&lt;funktionen appnamn >*. Funktioner.  *&lt;funktionsnamn >*)
* BlobType (”BlockBlob” eller ”PageBlob”)
* ContainerName
* BlobName
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

## <a name="trigger---concurrency-and-memory-usage"></a>Utlösaren - samtidighet och minnesanvändning

Blob-utlösaren använder en kö internt, så det maximala antalet samtidiga funktionsanrop styrs av den [köer konfigurationen i host.json](functions-host-json.md#queues). Standardinställningarna begränsa samtidighet till 24 anrop. Den här begränsningen gäller separat för varje funktion som använder en blob-utlösare.

[Plan för förbrukning](functions-scale.md#how-the-consumption-plan-works) begränsar en funktionsapp på en virtuell dator (VM) till 1,5 GB minne. Minne används av varje samtidigt köra funktionen och Functions-runtime sig själv. Om en funktion som utlöses av blob läser in hela blob i minnet, den maximala mängd minne som används av funktionen för blobbar är 24 * maximala blob-storlek. Till exempel en funktionsapp med tre blob-utlösta funktioner och standardinställningar skulle ha en maximal per VM samtidighet på 3 * 24 = 72 fungera anrop.

JavaScript-funktioner läsa hela blob i minnet och C#-funktioner göra det om du binder till `string`, `Byte[]`, eller POCO.

## <a name="trigger---polling"></a>Utlösa - avsökning

Om blob-behållaren som övervakas innehåller fler än 10 000 blobbar, loggfiler funktioner runtime genomsökningar kan du titta på för nya eller ändrade BLOB. Den här processen kan orsaka försening. En funktion kan hämta aktiveras inte förrän flera minuter eller längre efter blob skapas. Dessutom [lagring loggfiler skapas på ”bästa prestanda”](/rest/api/storageservices/About-Storage-Analytics-Logging) basis. Det är inte säkert att alla händelser fångas. Loggar under vissa förhållanden kan missas. Om du behöver snabbare och mer tillförlitlig blob-bearbetning kan du skapa en [kömeddelande](../storage/queues/storage-dotnet-how-to-use-queues.md) när du skapar blob. Använd sedan en [kö utlösaren](functions-bindings-storage-queue.md) i stället för en blob-trigger för att bearbeta blob. Ett annat alternativ är att använda händelsen rutnät. Se Självstudierna [automatisera storleksändring upp bilder med hjälp av händelse rutnätet](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Indata

Använda en Blob storage-indatabindning för att läsa blobbar.

## <a name="input---example"></a>Indata - exempel

Finns i det språkspecifika:

* [C#](#input---c-example)
* [C#-skript (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Indata - C#-exempel

Följande exempel är en [C#-funktionen](functions-dotnet-class-library.md) som använder en kö-utlösare och en inkommande blob-bindning. Funktionen loggar storleken på blobben kömeddelandet innehåller namnet på blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```        

### <a name="input---c-script-example"></a>Indata - exempel på C#-skript

<!--Same example for input and output. -->

I följande exempel visas blob-indata och utdata bindningar i en *function.json* fil och [C#-skript (.csx)](functions-reference-csharp.md) kod som använder bindningar. Funktionen skapas en kopia av en text-blob. Funktionen utlöses av ett meddelande i kön som innehåller namnet på blob att kopiera. Ny blob heter *{originalblobname}-kopiera*.

I den *function.json* filen, den `queueTrigger` metadataegenskapen används för att ange blobbnamnet i den `path` egenskaper:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Indata - JavaScript-exempel

<!--Same example for input and output. -->

I följande exempel visas blob-indata och utdata bindningar i en *function.json* fil och [JavaScript-kod](functions-reference-node.md) som använder bindningar. Funktionen skapas en kopia av en blob. Funktionen utlöses av ett meddelande i kön som innehåller namnet på blob att kopiera. Ny blob heter *{originalblobname}-kopiera*.

I den *function.json* filen, den `queueTrigger` metadataegenskapen används för att ange blobbnamnet i den `path` egenskaper:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input---attributes"></a>Indata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

Den Attributkonstruktorn tar sökvägen till blob och en `FileAccess` parameter som anger läsning eller skrivning, som visas i följande exempel:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Du kan ange den `Connection` att ange storage-konto du använder, enligt följande exempel:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Du kan använda den `StorageAccount` -attribut som anger storage-konto på klass, metoden eller parametern-nivå. Mer information finns i [utlösaren - attribut](#trigger---attributes).

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Blob` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | måste anges till `blob`. |
|**riktning** | Saknas | måste anges till `in`. Undantag anges i den [användning](#input---usage) avsnitt. |
|**Namn** | Saknas | Namnet på variabeln som representerar blob i funktionskoden.|
|**Sökväg** |**BlobPath** | Sökvägen till blob. | 
|**Anslutning** |**Anslutning**| Namnet på en appinställning som innehåller anslutningssträngen för lagring för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Till exempel om du ställer in `connection` för ”MyStorage” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom Functions-runtime använder standard lagringsanslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.<br><br>Anslutningssträngen får inte vara för ett allmänt lagringskonto en [endast blob storage-konto](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|Saknas | **Åtkomst** | Anger om du läsning eller skrivning. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

I C# och C#-skript, kan du använda följande parametertyper för blob-indatabindning:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> kräver ”inout” bindning `direction` i *function.json* eller `FileAccess.ReadWrite` i en C#-klassbiblioteket.

Bindning till `string` eller `Byte[]` rekommenderas endast om blobbstorleken är liten, eftersom hela blobbinnehållet läses in i minnet. Vanligtvis är det bättre att använda en `Stream` eller `CloudBlockBlob` typen. Mer information finns i [samtidighet och minnesanvändning](#trigger---concurrency-and-memory-usage) tidigare i den här artikeln.

I JavaScript, få åtkomst till blob-data med `context.bindings.<name from function.json>`.

## <a name="output"></a>Resultat

Använda Blob storage utdata bindningar för att skriva BLOB.

## <a name="output---example"></a>Output - exempel

Finns i det språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Utdata - C#-exempel

Följande exempel är en [C#-funktionen](functions-dotnet-class-library.md) som använder en blob-utlösare och två utdata blob-bindningar. Funktionen utlöses av skapandet av en avbildning blob i den *exempel bilder* behållare. Storlek för små och medelstora kopior av avbildningsbloben skapas. 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="output---c-script-example"></a>Utdata - exempel på C#-skript

<!--Same example for input and output. -->

I följande exempel visas blob-indata och utdata bindningar i en *function.json* fil och [C#-skript (.csx)](functions-reference-csharp.md) kod som använder bindningar. Funktionen skapas en kopia av en text-blob. Funktionen utlöses av ett meddelande i kön som innehåller namnet på blob att kopiera. Ny blob heter *{originalblobname}-kopiera*.

I den *function.json* filen, den `queueTrigger` metadataegenskapen används för att ange blobbnamnet i den `path` egenskaper:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Utdata - JavaScript-exempel

<!--Same example for input and output. -->

I följande exempel visas blob-indata och utdata bindningar i en *function.json* fil- och [JavaScript-kod] (funktioner-referens-node.md) som använder bindningar. Funktionen skapas en kopia av en blob. Funktionen utlöses av ett meddelande i kön som innehåller namnet på blob att kopiera. Ny blob heter *{originalblobname}-kopiera*.

I den *function.json* filen, den `queueTrigger` metadataegenskapen används för att ange blobbnamnet i den `path` egenskaper:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="output---attributes"></a>Utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

Den Attributkonstruktorn tar sökvägen till blob och en `FileAccess` parameter som anger läsning eller skrivning, som visas i följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Du kan ange den `Connection` att ange storage-konto du använder, enligt följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

En komplett exempel finns [utdata - C#-exempel](#output---c-example).

Du kan använda den `StorageAccount` -attribut som anger storage-konto på klass, metoden eller parametern-nivå. Mer information finns i [utlösaren - attribut](#trigger---attributes).

## <a name="output---configuration"></a>Output - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Blob` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | måste anges till `blob`. |
|**riktning** | Saknas | Måste anges till `out` för en bindning för utdata. Undantag anges i den [användning](#output---usage) avsnitt. |
|**Namn** | Saknas | Namnet på variabeln som representerar blob i funktionskoden.  Ange till `$return` att referera till returvärde för funktion.|
|**Sökväg** |**BlobPath** | Sökvägen till blob. | 
|**Anslutning** |**Anslutning**| Namnet på en appinställning som innehåller anslutningssträngen för lagring för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Till exempel om du ställer in `connection` för ”MyStorage” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom Functions-runtime använder standard lagringsanslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.<br><br>Anslutningssträngen får inte vara för ett allmänt lagringskonto en [endast blob storage-konto](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|Saknas | **Åtkomst** | Anger om du läsning eller skrivning. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I C# och C#-skript kan binda du till följande typer att skriva BLOB:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> kräver ”i” bindning `direction` i *function.json* eller `FileAccess.Read` i en C#-klassbiblioteket. Du kan dock använda behållarobjekt som körningsmiljön tillhandahåller för att skrivåtgärder, till exempel överföra blobbar i behållaren.

<sup>2</sup> kräver ”inout” bindning `direction` i *function.json* eller `FileAccess.ReadWrite` i en C#-klassbiblioteket.

I async-funktion, använder du det returnera värdet eller `IAsyncCollector` i stället för en `out` parameter.

Bindning till `string` eller `Byte[]` rekommenderas endast om blobbstorleken är liten, eftersom hela blobbinnehållet läses in i minnet. Vanligtvis är det bättre att använda en `Stream` eller `CloudBlockBlob` typen. Mer information finns i [samtidighet och minnesanvändning](#trigger---concurrency-and-memory-usage) tidigare i den här artikeln.


I JavaScript, få åtkomst till blob-data med `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning |  Referens |
|---|---|
| Blob | [BLOB-felkoder](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blobb, tabell, kö |  [Felkoder för lagring](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blobb, tabell, kö |  [Felsökning](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en Snabbstart som använder en Blob storage-utlösare](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
