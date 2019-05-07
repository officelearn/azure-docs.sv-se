---
title: Azure Blob storage-bindningar för Azure Functions
description: Förstå hur du använder Azure Blob storage-utlösare och bindningar i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: e4ec13453c204885f38b10272e76245e641fbef9
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203597"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Blob storage-bindningar i Azure Functions. Azure Functions stöder utlöser indata och utdatabindningar för blobar. Artikeln innehåller ett avsnitt för varje bindning:

* [BLOB-utlösare](#trigger)
* [BLOB-indatabindning](#input)
* [BLOB-utdatabindning](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Använda Event Grid-utlösaren i stället för Blob storage-utlösare för endast blob storage-konton, för hög skala, eller för att minska svarstiden. Mer information finns i den [utlösaren](#trigger) avsnittet.

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Blob storage-bindningar finns i den [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Blob storage-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Blob storage-utlösare startar en funktion när en ny eller uppdaterad blob har identifierats. Blob-innehållet anges som indata för funktionen.

Den [Event Grid-utlösare](functions-bindings-event-grid.md) har inbyggt stöd för [blob händelser](../storage/blobs/storage-blob-event-overview.md) och kan också användas för att starta en funktion när en ny eller uppdaterad blob har identifierats. Ett exempel finns i den [bildstorlek med Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) självstudien.

Använda Event Grid i stället för Blob storage-utlösare för följande scenarier:

* Blob Storage-konton
* Hög skala
* Minimerar svarstiderna

### <a name="blob-storage-accounts"></a>Blob Storage-konton

[BLOB storage-konton](../storage/common/storage-account-overview.md#types-of-storage-accounts) stöds för blob-indata och utdata bindningar men inte för blobutlösare. BLOB storage-utlösare kräver ett allmänt lagringskonto.

### <a name="high-scale"></a>Hög skala

Hög skala löst kan definieras som behållare som har fler än 100 000 blobar i dem eller konton som har fler än 100 blob uppdateringar per sekund.

### <a name="latency-issues"></a>Problem med nätverkssvarstiden

Om din funktionsapp finns på förbrukningsplanen, kan det vara upp till en 10 minuters fördröjning vid bearbetningen av nya blobbar om en funktionsapp är inaktiv. För att undvika den här fördröjningen kan växla du till en App Service-plan med alltid aktiverad. Du kan också använda en [Event Grid-utlösare](functions-bindings-event-grid.md) med Blob storage-kontot. Ett exempel finns i den [Event Grid självstudien](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Queue storage-utlösare

Ett annat alternativ för att bearbeta blobar är Queue storage-utlösare förutom Event Grid, men den har inget inbyggt stöd för blob-händelser. Du skulle behöva skapa Kömeddelanden när du skapar eller uppdaterar blobar. Ett exempel där det förutsätts att du har gjort det, finns i den [blob indatabindning exempel senare i den här artikeln](#input---example).

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skriver en logg när en blob läggs till eller uppdateras i den `samples-workitems` behållare.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Strängen `{name}` i blob-utlösare sökvägen `samples-workitems/{name}` skapar en [bindning uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i Funktionskoden till namnet på den utlösande blobben. Mer information finns i [Blob-namnet mönster](#trigger---blob-name-patterns) senare i den här artikeln.

Mer information om den `BlobTrigger` attributet, se [utlösare - attribut](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Utlösare – exempel på C#-skript

I följande exempel visas en blob-utlösare bindning i en *function.json* fil och [Python-kod](functions-reference-python.md) som använder bindningen. Funktionen skriver en logg när en blob läggs till eller uppdateras i den `samples-workitems` [behållare](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Här är bindningsdata i den *function.json* fil:

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

Strängen `{name}` i blob-utlösare sökvägen `samples-workitems/{name}` skapar en [bindning uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i Funktionskoden till namnet på den utlösande blobben. Mer information finns i [Blob-namnet mönster](#trigger---blob-name-patterns) senare i den här artikeln.

Mer information om *function.json* filegenskaper, finns i den [Configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden som binder till en `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Här är C#-skriptkoden som binder till en `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

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

Strängen `{name}` i blob-utlösare sökvägen `samples-workitems/{name}` skapar en [bindning uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i Funktionskoden till namnet på den utlösande blobben. Mer information finns i [Blob-namnet mönster](#trigger---blob-name-patterns) senare i den här artikeln.

Mer information om *function.json* filegenskaper, finns i den [Configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Utlösare – Python-exempel

I följande exempel visas en blob-utlösare bindning i en *function.json* fil och [Python-kod](functions-reference-python.md) som använder bindningen. Funktionen skriver en logg när en blob läggs till eller uppdateras i den `samples-workitems` [behållare](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Här är den *function.json* fil:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Strängen `{name}` i blob-utlösare sökvägen `samples-workitems/{name}` skapar en [bindning uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i Funktionskoden till namnet på den utlösande blobben. Mer information finns i [Blob-namnet mönster](#trigger---blob-name-patterns) senare i den här artikeln.

Mer information om *function.json* filegenskaper, finns i den [Configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är Python-kod:

```python
import logging
import azure.functions as func

def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Utlösare - Java-exemplet

I följande exempel visas en blob-utlösare bindning i en *function.json* fil och [Java-kod](functions-reference-java.md) som använder bindningen. Funktionen skriver en logg när en blob läggs till eller uppdateras i den `myblob` behållare.

Här är den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Här är den Java-kod:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```


## <a name="trigger---attributes"></a>Utlösare - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en blob-utlösare:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  Attributets konstruktorn tar en sökvägssträng som anger behållaren kan du titta på och eventuellt en [blob namnmönstret](#trigger---blob-name-patterns). Här är ett exempel:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Du kan ange den `Connection` egenskapen att ange storage-konto du använder, enligt följande exempel:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Ett komplett exempel finns i [utlösare – C#-exempel](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Innehåller ett annat sätt att ange konto för att använda. Konstruktorn tar namnet på en appinställning som innehåller en anslutningssträng för lagring. Attributet kan användas på parametern, metoden eller klassnivå. I följande exempel visas klassen och metoden:

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
* Den `StorageAccount` attribut som tillämpas på klassen.
* Standardkontot för lagring för funktionsappen (”AzureWebJobsStorage” app-inställning).

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `BlobTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till `blobTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. Undantag anges i den [användning](#trigger---usage) avsnittet. |
|**name** | Saknas | Namnet på variabeln som representerar blob i funktionskoden. |
|**path** | **BlobPath** |Den [behållare](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) att övervaka.  Kan vara en [blob namnmönstret](#trigger---blob-name-patterns). |
|**anslutning** | **Anslutning** | Namnet på en appinställning som innehåller lagringsanslutningssträngen ska användas för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Exempel: Om du ställer in `connection` till ”MyStorage” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom funktionskörningen använder standard Storage anslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.<br><br>Anslutningssträngen får inte vara för ett allmänt lagringskonto, en [Blob storage-konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

I C# och C#-skript, kan du använda följande parametertyper av för utlösande blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* En POCO serializable som JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> kräver ”inut” bindning `direction` i *function.json* eller `FileAccess.ReadWrite` i en C#-klassbibliotek.

Om du försöker binda till någon av Storage SDK-typer och får ett felmeddelande, kontrollerar du att du har en referens till [rätt Storage SDK version](#azure-storage-sdk-version-in-functions-1x).

Bindning till `string`, `Byte[]`, eller POCO rekommenderas bara om blobstorleken är liten, som hela blob innehållet läses in i minnet. Vanligtvis är det bättre att använda en `Stream` eller `CloudBlockBlob` typen. Mer information finns i [samtidighet och minnesanvändning](#trigger---concurrency-and-memory-usage) senare i den här artikeln.

Få åtkomst till indata-blob-data med i JavaScript, `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Utlösare - mönster för blob-namn

Du kan ange ett namnmönster i blob i den `path` -egenskapen i *function.json* eller i den `BlobTrigger` attributkonstruktör. Namnet har formatet kan vara en [filter eller bindning uttryck](./functions-bindings-expressions-patterns.md). I följande avsnitt innehåller exempel.

### <a name="get-file-name-and-extension"></a>Hämta filnamn och tillägg

I följande exempel visas hur du binder till blob-filnamn och tillägg separat:

```json
"path": "input/{blobname}.{blobextension}",
```
Om blobben som heter *ursprungliga Blob1.txt*, värdena för den `blobname` och `blobextension` variabler i Funktionskoden är *ursprungliga Blob1* och *txt*.

### <a name="filter-on-blob-name"></a>Filtrera efter namn på blob

Följande exempel utlösare endast för blobbar i den `input` behållaren som börjar med strängen ”original-”:

```json
"path": "input/original-{name}",
```

Om blobnamnet är *ursprungliga Blob1.txt*, värdet för den `name` variabel i Funktionskoden är `Blob1`.

### <a name="filter-on-file-type"></a>Filtrera efter typ

I följande exempel utlöser bara på *.png* filer:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrera på klammerparenteser i filnamn

Om du vill söka efter klammerparenteser i filnamn, escape-klammerparenteserna med hjälp av två klammerparenteser. Följande exempel filter för blobar som har klammerparenteser i namnet:

```json
"path": "images/{{20140101}}-{name}",
```

Om blobben som heter  *{20140101}-soundfile.mp3*, `name` variabelvärdet i Funktionskoden är *soundfile.mp3*.

## <a name="trigger---metadata"></a>Utlösare - metadata

Blob-utlösare innehåller flera metadataegenskaper. De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa värden har samma semantik som den [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) typen.

|Egenskap   |Typ  |Beskrivning  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Sökvägen till den utlösande blobben.|
|`Uri`|`System.Uri`|Den blob-URI för den primära platsen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blobens Systemegenskaper. |
|`Metadata` |`IDictionary<string,string>`|Den användardefinierade metadata för bloben.|

Till exempel logga följande C#-skript och JavaScript-exempel sökvägen till en utlösande blob, inklusive behållaren:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Utlösare – blob kvitton

Azure Functions-runtime säkerställer att ingen blob-Utlösarfunktion anropas flera gånger för samma nya eller uppdaterade blob. Att fastställa om en viss blob-version har bearbetats, den upprätthåller *blob kvitton*.

Azure Functions-butiker blob kvitton i en behållare med namnet *webjobs-azure-värdar* i Azure storage-konto för din funktionsapp (definieras av appinställningen `AzureWebJobsStorage`). En blob-inleverans har följande information:

* Utlösta funktionen (”*&lt;funktionsappens namn >*. Funktioner.  *&lt;funktionsnamn >*”, till exempel: "MyFunctionApp.Functions.CopyBlob")
* Behållarens namn
* Blobtyp (”BlockBlob” eller ”PageBlob”)
* Blobnamnet
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

Framtvinga ombearbetning av en blob genom att ta bort blob-kvitto på den blobben från den *webjobs-azure-värdar* behållaren manuellt. Även om ombearbetning inte inträffa omedelbart, har det säkert ska ske vid en senare tidpunkt.

## <a name="trigger---poison-blobs"></a>Utlösare – skadliga blobar

När en blob-Utlösarfunktion misslyckas för en viss blob, Azure Functions återförsök som fungerar totalt 5 gånger som standard.

Om alla 5 försök misslyckas, Azure Functions lägger till ett meddelande till en lagringskö med namnet *webjobs-blobtrigger-poison*. Kömeddelande för skadliga BLOB är ett JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet  *&lt;funktionsappens namn >*. Funktioner.  *&lt;funktionsnamn >*)
* BlobType (”BlockBlob” eller ”PageBlob”)
* ContainerName
* BlobName
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

## <a name="trigger---concurrency-and-memory-usage"></a>Utlösare - samtidighet och minnesanvändning

Blob-utlösare används internt, en kö så att det maximala antalet samtidiga funktionsanrop styrs av den [köer konfigurationen i host.json](functions-host-json.md#queues). Standardinställningarna begränsa samtidighet till 24 anrop. Den här gränsen gäller separat för varje funktion som använder en blob-utlösare.

[Med förbrukningsplanen](functions-scale.md#how-the-consumption-and-premium-plans-work) begränsar en funktionsapp på en virtuell dator (VM) till 1,5 GB minne. Minne används av varje funktion-instans som körs samtidigt och av funktionskörningen själva. Om en funktion som utlöses av blob läser in hela blob i minnet, maximalt minne som används av funktionen för blobar är 24 * maximala sidblobens storlek. Till exempel en funktionsapp med tre blob-utlösta funktioner och standardinställningarna skulle ha en maximal per VM samtidighet på 3 * 24 = 72 fungera anrop.

JavaScript och Java läsa in hela blob i minnet, och C# funktioner gör det om du binder till `string`, `Byte[]`, eller POCO.

## <a name="trigger---polling"></a>Utlösa - avsökning

Om blob-behållaren som övervakas innehåller fler än 10 000 BLOB (över alla behållare), loggfiler Functions runtime genomsökningar att se upp för nya eller ändrade blobar. Den här processen kan orsaka försening. En funktion kan hämta aktiveras inte förrän flera minuter eller längre när bloben har skapats.

> [!WARNING]
> Dessutom [lagringsloggar skapas på en ”bästa prestanda”](/rest/api/storageservices/About-Storage-Analytics-Logging) basis. Det är inte säkert att alla händelser ska samlas in. Under vissa förhållanden kan du missats loggar.
> 
> Om du behöver snabbare och mer tillförlitlig blob bearbetning kan du skapa en [kömeddelande](../storage/queues/storage-dotnet-how-to-use-queues.md) när du skapar bloben. Använd sedan en [köutlösare](functions-bindings-storage-queue.md) i stället för en blob-utlösare för att bearbeta blob. Ett annat alternativ är att använda Event Grid; finns i självstudierna [automatisera storleksändring av överförda bilder med Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Indata

Använda en indatabindning för Blob-lagring för att läsa BLOB-objekt.

## <a name="input---example"></a>Indata - exempel

Se exempel språkspecifika:

* [C#](#input---c-example)
* [C#-skript (.csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Indata - C#-exempel

Följande exempel är en [C#-funktion](functions-dotnet-class-library.md) som använder en kö-utlösare och en indata-blob-bindning. Kömeddelandet innehåller namnet på bloben och funktionen loggar storleken på blobben.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="input---c-script-example"></a>Indata - exempel på C#-skript

<!--Same example for input and output. -->

I följande exempel visas blob indata och utdatabindningar i en *function.json* fil och [C#-skript (.csx)](functions-reference-csharp.md) kod som använder bindningarna. Funktionen skapar en kopia av en text-blob. Funktionen utlöses av ett kömeddelande som innehåller namnet på blobben att kopiera. Den nya bloben heter *{originalblobname}-kopia*.

I den *function.json* filen, den `queueTrigger` metadata-egenskap för att ange blobnamnet på i den `path` egenskaper:

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

Här är C#-skriptkoden:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Indata - JavaScript-exempel

<!--Same example for input and output. -->

I följande exempel visas blob indata och utdatabindningar i en *function.json* fil och [JavaScript-kod](functions-reference-node.md) som använder bindningarna. Funktionen skapar en kopia av en blob. Funktionen utlöses av ett kömeddelande som innehåller namnet på blobben att kopiera. Den nya bloben heter *{originalblobname}-kopia*.

I den *function.json* filen, den `queueTrigger` metadata-egenskap för att ange blobnamnet på i den `path` egenskaper:

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

### <a name="input---python-example"></a>Indata - Python-exempel

<!--Same example for input and output. -->

I följande exempel visas blob indata och utdatabindningar i en *function.json* fil och [Python-kod](functions-reference-python.md) som använder bindningarna. Funktionen skapar en kopia av en blob. Funktionen utlöses av ett kömeddelande som innehåller namnet på blobben att kopiera. Den nya bloben heter *{originalblobname}-kopia*.

I den *function.json* filen, den `queueTrigger` metadata-egenskap för att ange blobnamnet på i den `path` egenskaper:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är Python-kod:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Indata - Java-exempel

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare, leta upp blobnamnet från frågesträng](#http-trigger-look-up-blob-name-from-query-string-java)
* [Kö utlösaren, ta emot blobnamnet från kömeddelande](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>HTTP-utlösare, leta upp blobnamnet från frågesträngen (Java)

 I följande exempel visas en Java-funktion som använder den ```HttpTrigger``` anteckning att ta emot en parameter med namnet på en fil i ett blob storage-behållare. Den ```BlobInput``` anteckning sedan läser filen och skickar innehållet ska fungera som en ```byte[]```.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Kö utlösaren, ta emot blobnamnet från kömeddelande (Java)

 I följande exempel visas en Java-funktion som använder den ```QueueTrigger``` kommentar till ett meddelande som innehåller namnet på en fil i ett blob storage-behållare. Den ```BlobInput``` anteckning sedan läser filen och skickar innehållet ska fungera som en ```byte[]```.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `@BlobInput` anteckning om parametrar vars värde skulle komma från en blob.  Den här anteckningen kan användas med interna Java-typer, Pojo eller kan ha värdet null-värden med hjälp av `Optional<T>`.

## <a name="input---attributes"></a>Indata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Attributets konstruktorn använder sökvägen till blobben och en `FileAccess` parameter som anger läs- eller skrivtransaktion, som visas i följande exempel:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Du kan ange den `Connection` egenskapen att ange storage-konto du använder, enligt följande exempel:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Du kan använda den `StorageAccount` attribut för att ange storage-konto på klass, metod eller parametern-nivå. Mer information finns i [utlösare - attribut](#trigger---attributes).

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Blob` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till `blob`. |
|**direction** | Saknas | Måste anges till `in`. Undantag anges i den [användning](#input---usage) avsnittet. |
|**name** | Saknas | Namnet på variabeln som representerar blob i funktionskoden.|
|**path** |**BlobPath** | Sökvägen till blobben. |
|**anslutning** |**Anslutning**| Namnet på en appinställning som innehåller den [lagringsanslutningssträng](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-azure-storage-account) ska användas för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Exempel: Om du ställer in `connection` till ”MyStorage” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom funktionskörningen använder standard Storage anslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.<br><br>Anslutningssträngen får inte vara för ett allmänt lagringskonto, en [endast blob storage-konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Saknas | **Åtkomst** | Anger om du läser eller skriver. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

I C# och C#-skript, kan du använda följande parametertyper av för blob-indatabindning:

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

<sup>1</sup> kräver ”inut” bindning `direction` i *function.json* eller `FileAccess.ReadWrite` i en C#-klassbibliotek.

Om du försöker binda till någon av Storage SDK-typer och får ett felmeddelande, kontrollerar du att du har en referens till [rätt Storage SDK version](#azure-storage-sdk-version-in-functions-1x).

Bindning till `string` eller `Byte[]` rekommenderas endast om blobstorleken är liten, eftersom hela blob-innehållet läses in i minnet. Vanligtvis är det bättre att använda en `Stream` eller `CloudBlockBlob` typen. Mer information finns i [samtidighet och minnesanvändning](#trigger---concurrency-and-memory-usage) tidigare i den här artikeln.

I JavaScript, få åtkomst till blob-data med `context.bindings.<name from function.json>`.

## <a name="output"></a>Resultat

Använd utdatabindningar för Blob storage för att skriva BLOB-objekt.

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

Följande exempel är en [C#-funktion](functions-dotnet-class-library.md) som använder en blob-utlösare och två blob utdatabindningar. Funktionen som utlöses av skapandet av en avbildningsblob i den *Exempelbilder* behållare. Små och medelstora kopior av avbildningsblobben skapas.

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

### <a name="output---c-script-example"></a>Resultat – exempel på C#-skript

<!--Same example for input and output. -->

I följande exempel visas blob indata och utdatabindningar i en *function.json* fil och [C#-skript (.csx)](functions-reference-csharp.md) kod som använder bindningarna. Funktionen skapar en kopia av en text-blob. Funktionen utlöses av ett kömeddelande som innehåller namnet på blobben att kopiera. Den nya bloben heter *{originalblobname}-kopia*.

I den *function.json* filen, den `queueTrigger` metadata-egenskap för att ange blobnamnet på i den `path` egenskaper:

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

Här är C#-skriptkoden:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Resultat – JavaScript-exempel

<!--Same example for input and output. -->

I följande exempel visas blob indata och utdatabindningar i en *function.json* fil och [JavaScript-kod](functions-reference-node.md) som använder bindningarna. Funktionen skapar en kopia av en blob. Funktionen utlöses av ett kömeddelande som innehåller namnet på blobben att kopiera. Den nya bloben heter *{originalblobname}-kopia*.

I den *function.json* filen, den `queueTrigger` metadata-egenskap för att ange blobnamnet på i den `path` egenskaper:

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

### <a name="output---python-example"></a>Resultat – Python-exempel

<!--Same example for input and output. -->

I följande exempel visas blob indata och utdatabindningar i en *function.json* fil och [Python-kod](functions-reference-python.md) som använder bindningarna. Funktionen skapar en kopia av en blob. Funktionen utlöses av ett kömeddelande som innehåller namnet på blobben att kopiera. Den nya bloben heter *{originalblobname}-kopia*.

I den *function.json* filen, den `queueTrigger` metadata-egenskap för att ange blobnamnet på i den `path` egenskaper:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är Python-kod:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Resultat – Java-exempel

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare, med hjälp av OutputBinding](#http-trigger-using-outputbinding-java)
* [Köutlösare, med hjälp av funktionsreturvärde](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-utlösare, med hjälp av OutputBinding (Java)

 I följande exempel visas en Java-funktion som använder den ```HttpTrigger``` anteckning att ta emot en parameter med namnet på en fil i ett blob storage-behållare. Den ```BlobInput``` anteckning sedan läser filen och skickar innehållet ska fungera som en ```byte[]```. Den ```BlobOutput``` anteckning Binder till ```OutputBinding outputItem```, som sedan används av funktionen för att skriva innehållet i indata-blob till konfigurerade behållaren.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Köutlösare, med hjälp av returvärde för funktion (Java)

 I följande exempel visas en Java-funktion som använder den ```QueueTrigger``` kommentar till ett meddelande som innehåller namnet på en fil i ett blob storage-behållare. Den ```BlobInput``` anteckning sedan läser filen och skickar innehållet ska fungera som en ```byte[]```. Den ```BlobOutput``` anteckning Binder till returvärdet funktionen, som sedan används av körningen för att skriva innehållet i indata-blob till behållaren konfigurerad lagring.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `@BlobOutput` anteckningen i funktionsparametrar vars värde skulle skrivas till ett objekt i blob storage.  Parametertypen ska vara `OutputBinding<T>`, där T är alla interna Java-typ eller en POJO.

## <a name="output---attributes"></a>Utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Attributets konstruktorn använder sökvägen till blobben och en `FileAccess` parameter som anger läs- eller skrivtransaktion, som visas i följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Du kan ange den `Connection` egenskapen att ange storage-konto du använder, enligt följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

Du kan använda den `StorageAccount` attribut för att ange storage-konto på klass, metod eller parametern-nivå. Mer information finns i [utlösare - attribut](#trigger---attributes).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Blob` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till `blob`. |
|**direction** | Saknas | Måste anges till `out` för en utdatabindning. Undantag anges i den [användning](#output---usage) avsnittet. |
|**name** | Saknas | Namnet på variabeln som representerar blob i funktionskoden.  Ange `$return` att referera till returvärde för funktion.|
|**path** |**BlobPath** | Sökvägen till blob-behållaren. |
|**anslutning** |**Anslutning**| Namnet på en appinställning som innehåller lagringsanslutningssträngen ska användas för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Exempel: Om du ställer in `connection` till ”MyStorage” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom funktionskörningen använder standard Storage anslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.<br><br>Anslutningssträngen får inte vara för ett allmänt lagringskonto, en [endast blob storage-konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Saknas | **Åtkomst** | Anger om du läser eller skriver. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I C# och C#-skript kan binda du till följande typer av att skriva BLOB-objekt:

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

<sup>1</sup> kräver ”i” bindning `direction` i *function.json* eller `FileAccess.Read` i en C#-klassbibliotek. Du kan dock använda behållarobjekt som körningen tillhandahåller för att skriva-åtgärder, till exempel ladda upp blobar i behållaren.

<sup>2</sup> kräver ”inut” bindning `direction` i *function.json* eller `FileAccess.ReadWrite` i en C#-klassbibliotek.

Om du försöker binda till någon av Storage SDK-typer och får ett felmeddelande, kontrollerar du att du har en referens till [rätt Storage SDK version](#azure-storage-sdk-version-in-functions-1x).

I async-funktioner, använder du det returnera värdet eller `IAsyncCollector` i stället för en `out` parametern.

Bindning till `string` eller `Byte[]` rekommenderas endast om blobstorleken är liten, eftersom hela blob-innehållet läses in i minnet. Vanligtvis är det bättre att använda en `Stream` eller `CloudBlockBlob` typen. Mer information finns i [samtidighet och minnesanvändning](#trigger---concurrency-and-memory-usage) tidigare i den här artikeln.


I JavaScript, få åtkomst till blob-data med `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning |  Referens |
|---|---|
| Blob | [BLOB-felkoder](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Table, Queue |  [Felkoder för lagring](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Felsökning](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
