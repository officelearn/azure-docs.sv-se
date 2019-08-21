---
title: Azure Blob Storage-bindningar för Azure Functions
description: Lär dig hur du använder Azure Blob Storage-utlösare och bindningar i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: e970afbfd9c302dc95667a7c356e95824838e9b4
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636461"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob Storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Blob Storage-bindningar i Azure Functions. Azure Functions stöder utlösare, indata och utgående bindningar för blobbar. Artikeln innehåller ett avsnitt för varje bindning:

* [BLOB-utlösare](#trigger)
* [BLOB-databindning](#input)
* [BLOB-utgående bindning](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Använd Event Grid-utlösaren i stället för Blob Storage-utlösaren för lagrings konton med enbart BLOB, för hög skala eller för att minska svars tiden. Mer information finns i avsnittet om [](#trigger) utlösare.

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Blob Storage-bindningarna finns i [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Blob Storage-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3. x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Blob Storage-utlösaren startar en funktion när en ny eller uppdaterad BLOB identifieras. BLOB-innehållet tillhandahålls som indata till funktionen.

[Event Grid](functions-bindings-event-grid.md) -utlösaren har inbyggt stöd för [BLOB-händelser](../storage/blobs/storage-blob-event-overview.md) och kan också användas för att starta en funktion när en ny eller uppdaterad BLOB identifieras. Ett exempel finns i [bilden ändra storlek med event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) själv studie kursen.

Använd Event Grid i stället för Blob Storage-utlösaren i följande scenarier:

* Blob Storage-konton
* Hög skala
* Minimera latens

### <a name="blob-storage-accounts"></a>Blob Storage-konton

[Blob Storage-konton](../storage/common/storage-account-overview.md#types-of-storage-accounts) stöds för BLOB-indata och utgående bindningar, men inte för BLOB-utlösare. Blob Storage-utlösare kräver ett allmänt lagrings konto.

### <a name="high-scale"></a>Hög skala

Hög skala kan lösas fritt som behållare som har fler än 100 000 blobbar i dem eller lagrings konton som har fler än 100 BLOB-uppdateringar per sekund.

### <a name="latency-issues"></a>Svars tids problem

Om din Function-app är i förbruknings planen kan det vara upp till 10 minuter att bearbeta nya blobbar om en Function-app har varit inaktiv. För att undvika den här fördröjningen kan du växla till en App Service plan med Always on Enabled. Du kan också använda en [Event Grid](functions-bindings-event-grid.md) -utlösare med ditt Blob Storage-konto. Ett exempel finns i [Event Grid själv studie kursen](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Kö Storage-utlösare

Förutom Event Grid är ett annat alternativ för att bearbeta blobar en kö Storage-utlösare, men det har inget inbyggt stöd för BLOB-händelser. Du skulle behöva skapa Kömeddelanden när du skapar eller uppdaterar blobbar. Ett exempel som förutsätter att du har gjort det finns i [exemplet på BLOB-ingångs bindning senare i den här artikeln](#input---example).

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som skriver en logg när en BLOB läggs till eller `samples-workitems` uppdateras i behållaren.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Strängen `{name}` i BLOB trigger-sökvägen `samples-workitems/{name}` skapar ett [bindnings uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktions koden för att komma åt fil namnet för den Utlös ande blobben. Mer information finns i [BLOB Name-mönster](#trigger---blob-name-patterns) längre fram i den här artikeln.

Mer information om `BlobTrigger` attributet finns i [trigger-attributes](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Utlösare – exempel på C#-skript

I följande exempel visas en BLOB-utlösnings bindning i en *Function. JSON* -fil och [python-kod](functions-reference-python.md) som använder bindningen. Funktionen skriver en logg när en BLOB läggs till eller uppdateras i `samples-workitems` [behållaren](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

Strängen `{name}` i BLOB trigger-sökvägen `samples-workitems/{name}` skapar ett [bindnings uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktions koden för att komma åt fil namnet för den Utlös ande blobben. Mer information finns i [BLOB Name-mönster](#trigger---blob-name-patterns) längre fram i den här artikeln.

Mer information om egenskaper för *Function. JSON* -filen finns i [konfigurations](#trigger---configuration) avsnittet förklarar dessa egenskaper.

Här är C# skript koden som binder till en `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Här är C# skript koden som binder till en `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

I följande exempel visas en BLOB-utlösnings bindning i en *Function. JSON* -fil och [JavaScript-kod](functions-reference-node.md) som använder bindningen. Funktionen skriver en logg när en BLOB läggs till eller uppdateras i `samples-workitems` behållaren.

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

Strängen `{name}` i BLOB trigger-sökvägen `samples-workitems/{name}` skapar ett [bindnings uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktions koden för att komma åt fil namnet för den Utlös ande blobben. Mer information finns i [BLOB Name-mönster](#trigger---blob-name-patterns) längre fram i den här artikeln.

Mer information om egenskaper för *Function. JSON* -filen finns i [konfigurations](#trigger---configuration) avsnittet förklarar dessa egenskaper.

Här är JavaScript-kod:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Utlös – python-exempel

I följande exempel visas en BLOB-utlösnings bindning i en *Function. JSON* -fil och [python-kod](functions-reference-python.md) som använder bindningen. Funktionen skriver en logg när en BLOB läggs till eller uppdateras i `samples-workitems` [behållaren](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

Strängen `{name}` i BLOB trigger-sökvägen `samples-workitems/{name}` skapar ett [bindnings uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktions koden för att komma åt fil namnet för den Utlös ande blobben. Mer information finns i [BLOB Name-mönster](#trigger---blob-name-patterns) längre fram i den här artikeln.

Mer information om egenskaper för *Function. JSON* -filen finns i [konfigurations](#trigger---configuration) avsnittet förklarar dessa egenskaper.

Här är python-koden:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Utlösare - Java-exemplet

I följande exempel visas en BLOB-utlösnings bindning i en *Function. JSON* -fil och [Java-kod](functions-reference-java.md) som använder bindningen. Funktionen skriver en logg när en BLOB läggs till eller uppdateras i `myblob` behållaren.

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

I [ C# klass bibliotek](functions-dotnet-class-library.md)använder du följande attribut för att konfigurera en BLOB-utlösare:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Attributets konstruktor tar en Sök vägs sträng som visar behållaren för att titta på och eventuellt ett [BLOB Name-mönster](#trigger---blob-name-patterns). Här är ett exempel:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Du kan ställa `Connection` in egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

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

  Ger ett annat sätt att ange det lagrings konto som ska användas. Konstruktorn tar namnet på en app-inställning som innehåller en lagrings anslutnings sträng. Attributet kan användas på parametern, metoden eller klassnivå. I följande exempel visas klassen och metoden:

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

Lagrings kontot som ska användas fastställs i följande ordning:

* Den `BlobTrigger` attributets `Connection` egenskapen.
* Den `StorageAccount` attribut som används i samma parameter som den `BlobTrigger` attribut.
* Den `StorageAccount` attribut som används i funktionen.
* Den `StorageAccount` attribut som tillämpas på klassen.
* Standard lagrings kontot för app-inställningen ("AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `BlobTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**type** | Saknas | Måste anges till `blobTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. Undantag anges i användnings [](#trigger---usage) avsnittet. |
|**name** | Saknas | Namnet på variabeln som representerar blobben i funktions koden. |
|**Sökväg** | **BlobPath** |Den [behållare](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) som ska övervakas.  Kan vara ett [BLOB Name-mönster](#trigger---blob-name-patterns). |
|**anslutning** | **anslutning** | Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel anger `connection` "unstorage" söker funktions körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna `AzureWebJobsStorage`app-inställningen.<br><br>Anslutnings strängen måste vara för ett allmänt lagrings konto, inte ett [Blob Storage-konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

I C# och C# -skript kan du använda följande parameter typer för den Utlös ande blobben:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* En POCO som är serialiserbar som JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> kräver "Inout"- `direction` bindning i *Function. JSON* eller `FileAccess.ReadWrite` i C# ett klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

Bindning till `string`, `Byte[]`eller Poco rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller `CloudBlockBlob` -typ. Mer information finns i [samtidighets-och minnes användning](#trigger---concurrency-and-memory-usage) längre fram i den här artikeln.

I Java Script får du åtkomst till BLOB- `context.bindings.<name from function.json>`data för indata med hjälp av.

## <a name="trigger---blob-name-patterns"></a>Utlösare – BLOB-namn mönster

Du kan ange ett BLOB Name-mönster i `path` egenskapen i *Function. JSON* eller i `BlobTrigger` attributhierarkin. Namn mönstret kan vara ett [filter eller ett bindnings uttryck](./functions-bindings-expressions-patterns.md). I följande avsnitt finns exempel.

### <a name="get-file-name-and-extension"></a>Hämta fil namn och tillägg

I följande exempel visas hur du binder till BLOB-filens namn och tillägg separat:

```json
"path": "input/{blobname}.{blobextension}",
```
Om blobben heter *original-Blob1. txt*, `blobname` är värdena för variablerna `blobextension` och i funktions koden *ursprungliga – Blob1* och *txt*.

### <a name="filter-on-blob-name"></a>Filtrera på BLOB-namn

Följande exempel utlöser bara blobar i `input` behållaren som börjar med strängen "original-":

```json
"path": "input/original-{name}",
```

Om BLOB-namnet är *original-Blob1. txt*, är `name` `Blob1`värdet för variabeln i funktions koden.

### <a name="filter-on-file-type"></a>Filtrera efter filtyp

Följande exempel utlöser endast på *png* -filer:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrera på klammerparenteser i fil namn

Om du vill söka efter klammerparenteser i fil namn, escapea klammerparenteser med två klammerparenteser. I följande exempel filtreras blobbar som har klammerparenteser i namnet:

```json
"path": "images/{{20140101}}-{name}",
```

Om blobben heter `name`  *{20140101}-Soundfile. mp3*, är variabelvärdet i funktions koden *Soundfile. mp3*.

## <a name="trigger---metadata"></a>Utlös – metadata

BLOB-utlösaren innehåller flera egenskaper för metadata. De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa värden har samma semantik som [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) -typen.

|Egenskap  |Type  |Beskrivning  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Sökvägen till den Utlös ande blobben.|
|`Uri`|`System.Uri`|Blobens URI för den primära platsen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blobens system egenskaper. |
|`Metadata` |`IDictionary<string,string>`|Användardefinierade metadata för blobben.|

Till exempel loggar följande C# skript och JavaScript-exempel sökvägen till den Utlös ande blobben, inklusive behållaren:

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

## <a name="trigger---blob-receipts"></a>Utlös BLOB-kvitton

Azure Functions runtime ser till att ingen BLOB-utlösnings funktion anropas mer än en gång för samma nya eller uppdaterade blob. För att avgöra om en specifik blob-version har bearbetats upprätthåller *BLOB*-kvitton.

Azure Functions lagrar BLOB-kvitton i en behållare med namnet *Azure-WebJobs-hosts* i Azure Storage-kontot för din Function-app (definieras `AzureWebJobsStorage`av appens inställning). Ett BLOB-kvitto har följande information:

* Funktionen triggerd (" *&lt;Function App Name >* . Funktionen. funktions namn > ", till exempel:  *&lt;* "MyFunctionApp.Functions.CopyBlob")
* Behållarens namn
* Blob-typ ("BlockBlob" eller "PageBlob")
* BLOB-namnet
* ETag (en BLOB-versions identifierare, till exempel: "0x8D1DC6E70A277EF")

Om du vill framtvinga en ombearbetning av en BLOB tar du bort BLOB-kvittot för blobben från behållaren *Azure-WebJobs-hosts* manuellt. Det kan hända att ombearbetningen inte sker omedelbart, men det är garanterat att det sker vid en senare tidpunkt.

## <a name="trigger---poison-blobs"></a>Utlös ande – Poison-blobbar

När en BLOB-utlösare Miss lyckas för en specifik BLOB, Azure Functions försök som fungerar totalt 5 gånger som standard.

Om alla fem försöken inte fungerar lägger Azure Functions till ett meddelande i en lagrings kö med namnet *WebJobs-en-Poison*. Queue-meddelandet för Poison-blobbar är ett JSON-objekt som innehåller följande egenskaper:

* FunctionId (i format  *&lt;funktionens program namn >* . Funktionen. funktions namn >)  *&lt;*
* BlobType ("BlockBlob" eller "PageBlob")
* ContainerName
* BlobName
* ETag (en BLOB-versions identifierare, till exempel: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Utlösare – samtidighet och minnes användning

BLOB-utlösaren använder en kö internt, så det maximala antalet samtidiga funktions anrop styrs av [köernas konfiguration i Host. JSON](functions-host-json.md#queues). Standardinställnings gränsen samtidighet till 24 anrop. Den här gränsen gäller separat för varje funktion som använder en BLOB-utlösare.

[Förbruknings planen](functions-scale.md#how-the-consumption-and-premium-plans-work) begränsar en Function-app på en virtuell dator (VM) till 1,5 GB minne. Minne används av varje intern körning av funktions instansen och av Functions-körningen. Om en BLOB-utlöst funktion läser in hela blobben i minnet är den maximala mängd minne som används av den funktionen bara för blobbar 24 * maximal BLOB-storlek. Till exempel skulle en Function-app med tre BLOB-utlöst funktioner och standardinställningarna ha ett maximalt antal per VM-concurrency på 3 * 24 = 72 funktions anrop.

Java Script-och Java-funktioner läser in hela blobben i C# minnet och fungerar om du binder till `string`, `Byte[]`, eller Poco.

## <a name="trigger---polling"></a>Utlös vid avsökning

Om den BLOB-behållare som övervakas innehåller fler än 10 000 blobbar (över alla behållare) genomsöker Functions-körningen loggfilerna för att se om det finns nya eller ändrade blobbar. Den här processen kan resultera i fördröjningar. En funktion kanske inte utlöses förrän flera minuter eller längre efter att blobben har skapats.

> [!WARNING]
> Dessutom [skapas lagrings loggar på grund av "bästa prestanda"](/rest/api/storageservices/About-Storage-Analytics-Logging) . Det finns ingen garanti för att alla händelser har registrerats. Under vissa omständigheter kan loggarna missas.
> 
> Om du behöver en snabbare eller mer tillförlitlig BLOB-bearbetning bör du överväga att skapa ett [Queue-meddelande](../storage/queues/storage-dotnet-how-to-use-queues.md) när du skapar blobben. Använd sedan en [Queue](functions-bindings-storage-queue.md) -utlösare i stället för en BLOB-utlösare för att bearbeta blobben. Ett annat alternativ är att använda Event Grid; i självstudien får du [automatiskt ändra storlek på överförda bilder med hjälp av event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Indata

Använd en indata-bindning för Blob Storage för att läsa blobbar.

## <a name="input---example"></a>Indatamängds exempel

Se exempel språkspecifika:

* [C#](#input---c-example)
* [C#-skript (.csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Indatamängds C# exempel

Följande exempel är en [ C# funktion](functions-dotnet-class-library.md) som använder en Queue-utlösare och en BLOB-bindning för inflöde. Queue-meddelandet innehåller namnet på blobben och funktionen loggar storleken på blobben.

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

### <a name="input---c-script-example"></a>Exempel på C# indatamängds skript

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [ C# skript kod (. CSX)](functions-reference-csharp.md) som använder bindningarna. Funktionen gör en kopia av en text-blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* - `queueTrigger` filen används egenskapen metadata för att ange `path` BLOB-namnet i egenskaperna:

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

### <a name="input---javascript-example"></a>Exempel på inmatade JavaScript-skript

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [JavaScript-kod](functions-reference-node.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* - `queueTrigger` filen används egenskapen metadata för att ange `path` BLOB-namnet i egenskaperna:

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

### <a name="input---python-example"></a>In-python-exempel

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [python-kod](functions-reference-python.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* - `queueTrigger` filen används egenskapen metadata för att ange `path` BLOB-namnet i egenskaperna:

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

Här är python-koden:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>In-Java-exempel

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare, slå upp BLOB-namn från frågesträng](#http-trigger-look-up-blob-name-from-query-string-java)
* [Köa utlösare, ta emot BLOB-namn från köa meddelande](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>HTTP-utlösare, slå upp BLOB-namn från frågesträng (Java)

 I följande exempel visas en Java-funktion som använder ```HttpTrigger``` anteckningen för att ta emot en parameter som innehåller namnet på en fil i en Blob Storage-behållare. Anteckningen läser sedan filen och skickar innehållet till funktionen som en ```byte[]```. ```BlobInput```

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Köa utlösare, ta emot BLOB-namn från köa meddelande (Java)

 I följande exempel visas en Java-funktion som använder ```QueueTrigger``` anteckningen för att ta emot ett meddelande som innehåller namnet på en fil i en Blob Storage-behållare. Anteckningen läser sedan filen och skickar innehållet till funktionen som en ```byte[]```. ```BlobInput```

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

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@BlobInput` anteckningen för parametrar vars värde kommer från en blob.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden `Optional<T>`med hjälp av.

## <a name="input---attributes"></a>Indata - attribut

Använd [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar vägen till blobben och en `FileAccess` parameter som visar Läs-eller Skriv behörighet, som visas i följande exempel:

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

Du kan ställa `Connection` in egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

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

Du kan använda `StorageAccount` attributet för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i [utlösare - attribut](#trigger---attributes).

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Blob` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**type** | Saknas | Måste anges till `blob`. |
|**direction** | Saknas | Måste anges till `in`. Undantag anges i användnings [](#input---usage) avsnittet. |
|**name** | Saknas | Namnet på variabeln som representerar blobben i funktions koden.|
|**Sökväg** |**BlobPath** | Sökvägen till blobben. |
|**anslutning** |**anslutning**| Namnet på en app-inställning som innehåller den [lagrings anslutnings sträng](../storage/common/storage-configure-connection-string.md) som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel anger `connection` "unstorage" söker funktions körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna `AzureWebJobsStorage`app-inställningen.<br><br>Anslutnings strängen måste vara avsedd för ett allmänt lagrings konto, inte ett [enbart BLOB-lagrings konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Saknas | **Åtkomst** | Anger om du kommer att läsa eller skriva. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

I C# och C# -skript kan du använda följande parameter typer för BLOB-inflödet-bindning:

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

<sup>1</sup> kräver "Inout"- `direction` bindning i *Function. JSON* eller `FileAccess.ReadWrite` i C# ett klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

Bindning till `string` eller `Byte[]` rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller `CloudBlockBlob` -typ. Mer information finns i [samtidighets-och minnes användning](#trigger---concurrency-and-memory-usage) tidigare i den här artikeln.

I Java Script får du åtkomst till BLOB `context.bindings.<name from function.json>`-data med hjälp av.

## <a name="output"></a>Output

Använd Blob Storage utgående bindningar för att skriva blobbar.

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

Följande exempel är en [ C# funktion](functions-dotnet-class-library.md) som använder en BLOB-utlösare och två utgående BLOB-bindningar. Funktionen utlöses genom att en avbildnings-BLOB skapas i behållaren för *exempel bilder* . Det skapar små och medel stora kopior av avbildnings-bloben.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    IImageFormat format;

    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageSmall, ImageSize.Small, format);
    }

    image.Position = 0;
    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageMedium, ImageSize.Medium, format);
    }
}

public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
{
    var dimensions = imageDimensionsTable[size];

    input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
    input.Save(output, format);
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

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [ C# skript kod (. CSX)](functions-reference-csharp.md) som använder bindningarna. Funktionen gör en kopia av en text-blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* - `queueTrigger` filen används egenskapen metadata för att ange `path` BLOB-namnet i egenskaperna:

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

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [JavaScript-kod](functions-reference-node.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* - `queueTrigger` filen används egenskapen metadata för att ange `path` BLOB-namnet i egenskaperna:

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

### <a name="output---python-example"></a>Utdata – python-exempel

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [python-kod](functions-reference-python.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* - `queueTrigger` filen används egenskapen metadata för att ange `path` BLOB-namnet i egenskaperna:

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

Här är python-koden:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Utdata – Java-exempel

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare med hjälp av OutputBinding](#http-trigger-using-outputbinding-java)
* [Köa utlösare med funktions retur värde](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-utlösare med hjälp av OutputBinding (Java)

 I följande exempel visas en Java-funktion som använder ```HttpTrigger``` anteckningen för att ta emot en parameter som innehåller namnet på en fil i en Blob Storage-behållare. Anteckningen läser sedan filen och skickar innehållet till funktionen som en ```byte[]```. ```BlobInput``` Anteckningen binder till ```OutputBinding outputItem```, som sedan används av funktionen för att skriva innehållet i bloben för indata till den konfigurerade lagrings behållaren. ```BlobOutput```

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Köa utlösare med funktions retur värde (Java)

 I följande exempel visas en Java-funktion som använder ```QueueTrigger``` anteckningen för att ta emot ett meddelande som innehåller namnet på en fil i en Blob Storage-behållare. Anteckningen läser sedan filen och skickar innehållet till funktionen som en ```byte[]```. ```BlobInput``` ```BlobOutput``` Anteckningen binder till funktionens retur värde, som sedan används av körnings miljön för att skriva innehållet i bloben för indata till den konfigurerade lagrings behållaren.

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

 I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@BlobOutput` anteckningen för funktions parametrar vars värde skrivs till ett objekt i Blob Storage.  Parameter typen bör vara `OutputBinding<T>`, där T är en inbyggd Java-typ eller en POJO.

## <a name="output---attributes"></a>Utdata - attribut

Använd [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar vägen till blobben och en `FileAccess` parameter som visar Läs-eller Skriv behörighet, som visas i följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Du kan ställa `Connection` in egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

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

Du kan använda `StorageAccount` attributet för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i [utlösare - attribut](#trigger---attributes).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Blob` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**type** | Saknas | Måste anges till `blob`. |
|**direction** | Saknas | Måste anges till `out` för en utgående bindning. Undantag anges i användnings [](#output---usage) avsnittet. |
|**name** | Saknas | Namnet på variabeln som representerar blobben i funktions koden.  Ange till `$return` att referera till funktionens retur värde.|
|**Sökväg** |**BlobPath** | Sökvägen till BLOB-behållaren. |
|**anslutning** |**anslutning**| Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel anger `connection` "unstorage" söker funktions körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna `AzureWebJobsStorage`app-inställningen.<br><br>Anslutnings strängen måste vara avsedd för ett allmänt lagrings konto, inte ett [enbart BLOB-lagrings konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Saknas | **Åtkomst** | Anger om du kommer att läsa eller skriva. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I C# och C# -skript kan du binda till följande typer för att skriva blobbar:

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

<sup>1</sup> kräver "i"- `direction` bindning i *Function. JSON* eller `FileAccess.Read` i C# ett klass bibliotek. Du kan dock använda behållarobjektet som körningen tillhandahåller för att utföra Skriv åtgärder, till exempel att ladda upp blobar till behållaren.

<sup>2</sup> kräver "Inout"- `direction` bindning i *Function. JSON* eller `FileAccess.ReadWrite` i C# ett klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

I async-funktioner, använder du det returnera värdet eller `IAsyncCollector` i stället för en `out` parametern.

Bindning till `string` eller `Byte[]` rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller `CloudBlockBlob` -typ. Mer information finns i [samtidighets-och minnes användning](#trigger---concurrency-and-memory-usage) tidigare i den här artikeln.


I Java Script får du åtkomst till BLOB `context.bindings.<name from function.json>`-data med hjälp av.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning |  Referens |
|---|---|
| Blob | [BLOB-felkoder](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tabell, kö |  [Lagrings fel koder](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö |  [Felsökning](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
