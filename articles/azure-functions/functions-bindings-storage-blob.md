---
title: Azure Blob Storage-bindningar för Azure Functions
description: Lär dig hur du använder Azure Blob Storage-utlösare och bindningar i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, functions, Event Processing, dynamisk beräkning, Server lös arkitektur
ms.service: azure-functions
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: b565a48b56162d19a07f0f54bfe780b7dda04b96
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177403"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob Storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Blob Storage-bindningar i Azure Functions. Azure Functions stöder utlösare, indata och utgående bindningar för blobbar. Artikeln innehåller ett avsnitt för varje bindning:

* [BLOB-utlösare](#trigger)
* [BLOB-databindning](#input)
* [BLOB-utgående bindning](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Använd Event Grid-utlösaren i stället för Blob Storage-utlösaren för lagrings konton med enbart BLOB, för hög skala eller för att minska svars tiden. Mer information finns i avsnittet om [utlösare](#trigger) .

## <a name="packages---functions-1x"></a>Paket-funktioner 1. x

Blob Storage-bindningarna finns i [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x. Käll koden för paketet finns i [Azure-WebJobs-SDK GitHub-](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Paket-funktioner 2. x

Blob Storage-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3. x. Käll koden för paketet finns i [Azure-WebJobs-SDK GitHub-](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Blob Storage-utlösaren startar en funktion när en ny eller uppdaterad BLOB identifieras. BLOB-innehållet tillhandahålls som indata till funktionen.

[Event Grid-utlösaren](functions-bindings-event-grid.md) har inbyggt stöd för [BLOB-händelser](../storage/blobs/storage-blob-event-overview.md) och kan också användas för att starta en funktion när en ny eller uppdaterad BLOB identifieras. Ett exempel finns i [bilden ändra storlek med event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) själv studie kursen.

Använd Event Grid i stället för Blob Storage-utlösaren i följande scenarier:

* Blob Storage-konton
* Hög skala
* Minimera latens

### <a name="blob-storage-accounts"></a>Blob Storage-konton

[Blob Storage-konton](../storage/common/storage-account-overview.md#types-of-storage-accounts) stöds för BLOB-indata och utgående bindningar, men inte för BLOB-utlösare. Blob Storage-utlösare kräver ett allmänt lagrings konto.

### <a name="high-scale"></a>Hög skala

Hög skala kan lösas fritt som behållare som har fler än 100 000 blobbar i dem eller lagrings konton som har fler än 100 BLOB-uppdateringar per sekund.

### <a name="latency-issues"></a>Svars tids problem

Om din Function-app är i förbruknings planen kan det vara upp till 10 minuter att bearbeta nya blobbar om en Function-app har varit inaktiv. För att undvika den här fördröjningen kan du växla till en App Service plan med Always on Enabled. Du kan också använda en [Event Grid-utlösare](functions-bindings-event-grid.md) med ditt Blob Storage-konto. Ett exempel finns i [Event Grid själv studie kursen](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Kölagringsutlösare

Förutom Event Grid är ett annat alternativ för att bearbeta blobar en kö Storage-utlösare, men det har inget inbyggt stöd för BLOB-händelser. Du skulle behöva skapa Kömeddelanden när du skapar eller uppdaterar blobbar. Ett exempel som förutsätter att du har gjort det finns i [exemplet på BLOB-ingångs bindning senare i den här artikeln](#input---example).

## <a name="trigger---example"></a>Utlös – exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som skriver en logg när en BLOB läggs till eller uppdateras i behållaren `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Strängen `{name}` i BLOB-utlösaren sökväg `samples-workitems/{name}` skapar ett [bindnings uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktions koden för att komma åt fil namnet för den Utlös ande blobben. Mer information finns i [BLOB Name-mönster](#trigger---blob-name-patterns) längre fram i den här artikeln.

Mer information om attributet `BlobTrigger` finns i avsnittet [trigger-attributes](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en BLOB-utlösnings bindning i en *Function. JSON* -fil och kod som använder bindningen. Funktionen skriver en logg när en BLOB läggs till eller uppdateras i [behållaren](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems`.

Här är bindnings data i *Function. JSON* -filen:

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

Strängen `{name}` i BLOB-utlösaren sökväg `samples-workitems/{name}` skapar ett [bindnings uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktions koden för att komma åt fil namnet för den Utlös ande blobben. Mer information finns i [BLOB Name-mönster](#trigger---blob-name-patterns) längre fram i den här artikeln.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en BLOB-utlösnings bindning i en *Function. JSON* -fil och [JavaScript-kod](functions-reference-node.md) som använder bindningen. Funktionen skriver en logg när en BLOB läggs till eller uppdateras i behållaren `samples-workitems`.

Här är *Function. JSON* -filen:

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

Strängen `{name}` i BLOB-utlösaren sökväg `samples-workitems/{name}` skapar ett [bindnings uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktions koden för att komma åt fil namnet för den Utlös ande blobben. Mer information finns i [BLOB Name-mönster](#trigger---blob-name-patterns) längre fram i den här artikeln.

Mer information om egenskaper för *Function. JSON* -filen finns i [konfigurations](#trigger---configuration) avsnittet förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I följande exempel visas en BLOB-utlösnings bindning i en *Function. JSON* -fil och [python-kod](functions-reference-python.md) som använder bindningen. Funktionen skriver en logg när en BLOB läggs till eller uppdateras i [behållaren](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems`.

Här är *Function. JSON* -filen:

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

Strängen `{name}` i BLOB-utlösaren sökväg `samples-workitems/{name}` skapar ett [bindnings uttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktions koden för att komma åt fil namnet för den Utlös ande blobben. Mer information finns i [BLOB Name-mönster](#trigger---blob-name-patterns) längre fram i den här artikeln.

Mer information om egenskaper för *Function. JSON* -filen finns i [konfigurations](#trigger---configuration) avsnittet förklarar dessa egenskaper.

Här är python-koden:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

I följande exempel visas en BLOB-utlösnings bindning i en *Function. JSON* -fil och [Java-kod](functions-reference-java.md) som använder bindningen. Funktionen skriver en logg när en BLOB läggs till eller uppdateras i behållaren `myblob`.

Här är *Function. JSON* -filen:

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

Här är Java-koden:

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

---

## <a name="trigger---attributes"></a>Utlös ande attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

  Du kan ställa in egenskapen `Connection` för att ange det lagrings konto som ska användas, som du ser i följande exempel:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Ett fullständigt exempel finns i [utlösare exempel](#trigger---example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Ger ett annat sätt att ange det lagrings konto som ska användas. Konstruktorn tar namnet på en app-inställning som innehåller en lagrings anslutnings sträng. Attributet kan användas på parameter-, metod-eller klass nivå. I följande exempel visas klass nivå och metod nivå:

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

* Egenskapen `Connection` för attributet `BlobTrigger`.
* Attributet `StorageAccount` som används för samma parameter som attributet `BlobTrigger`.
* Attributet `StorageAccount` som används för funktionen.
* Attributet `StorageAccount` som används för klassen.
* Standard lagrings kontot för app-inställningen ("AzureWebJobsStorage").

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

Attributet `@BlobTrigger` används för att ge dig åtkomst till den blob som utlöste funktionen. Mer information finns i [utlösnings exemplet](#trigger---example) .

---

## <a name="trigger---configuration"></a>Utlös konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och attributet `BlobTrigger`.

|function. JSON-egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste vara inställd på `blobTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal.|
|**riktning** | Ej tillämpligt | Måste vara inställd på `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal. Undantag anges i [användnings](#trigger---usage) avsnittet. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar blobben i funktions koden. |
|**path** | **BlobPath** |Den [behållare](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) som ska övervakas.  Kan vara ett [BLOB Name-mönster](#trigger---blob-name-patterns). |
|**anslutningen** | **Anslutning** | Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` till "telestorage" söker Functions-körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den app-inställning som heter `AzureWebJobsStorage`.<br><br>Anslutnings strängen måste vara för ett allmänt lagrings konto, inte ett [Blob Storage-konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlös användning

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till BLOB-data med `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Få åtkomst till BLOB-data via parametern som anges som [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Mer information finns i [utlösnings exemplet](#trigger---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Attributet `@BlobTrigger` används för att ge dig åtkomst till den blob som utlöste funktionen. Mer information finns i [utlösnings exemplet](#trigger---example) .

---

## <a name="trigger---blob-name-patterns"></a>Utlösare – BLOB-namn mönster

Du kan ange ett BLOB Name-mönster i egenskapen `path` i *Function. JSON* eller i konstruktorn `BlobTrigger`. Namn mönstret kan vara ett [filter eller ett bindnings uttryck](./functions-bindings-expressions-patterns.md). I följande avsnitt finns exempel.

### <a name="get-file-name-and-extension"></a>Hämta fil namn och tillägg

I följande exempel visas hur du binder till BLOB-filens namn och tillägg separat:

```json
"path": "input/{blobname}.{blobextension}",
```

Om blobben heter *original-Blob1. txt*, är värdena för variablerna `blobname` och @no__t 2 i funktions koden *ursprungliga – Blob1* och *txt*.

### <a name="filter-on-blob-name"></a>Filtrera på BLOB-namn

Följande exempel utlöser bara blobar i behållaren `input` som börjar med strängen "original-":

```json
"path": "input/original-{name}",
```

Om BLOB-namnet är *original-Blob1. txt*, är värdet för variabeln `name` i funktions kod `Blob1`.

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

Om blobben heter *@no__t -1-Soundfile. mp3*, är värdet för @no__t 2-variabeln i funktions koden *Soundfile. mp3*.

## <a name="trigger---metadata"></a>Utlös – metadata

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Metadata är inte tillgängliga i python.

# <a name="javatabjava"></a>[Java](#tab/java)

Metadata är inte tillgängliga i Java.

---

## <a name="trigger---blob-receipts"></a>Utlös BLOB-kvitton

Azure Functions runtime ser till att ingen BLOB-utlösnings funktion anropas mer än en gång för samma nya eller uppdaterade blob. För att avgöra om en specifik blob-version har bearbetats upprätthåller *BLOB-kvitton*.

Azure Functions lagrar BLOB-kvitton i en behållare med namnet *Azure-WebJobs-hosts* i Azure Storage-kontot för din Function-app (definieras av appens inställning `AzureWebJobsStorage`). Ett BLOB-kvitto har följande information:

* Den Utlös ande funktionen (" *&lt;function app name >* . Funktionen. *&lt;function namn >* ", till exempel:" MyFunctionApp. functions. CopyBlob ")
* Behållarens namn
* Blob-typ ("BlockBlob" eller "PageBlob")
* BLOB-namnet
* ETag (en BLOB versions identifierare, till exempel: "0x8D1DC6E70A277EF")

Om du vill framtvinga en ombearbetning av en BLOB tar du bort BLOB-kvittot för blobben från behållaren *Azure-WebJobs-hosts* manuellt. Det kan hända att ombearbetningen inte sker omedelbart, men det är garanterat att det sker vid en senare tidpunkt.

## <a name="trigger---poison-blobs"></a>Utlös ande – Poison-blobbar

När en BLOB-utlösare Miss lyckas för en specifik BLOB, Azure Functions försök som fungerar totalt 5 gånger som standard.

Om alla fem försöken inte fungerar lägger Azure Functions till ett meddelande i en lagrings kö med namnet *WebJobs-en-Poison*. Queue-meddelandet för Poison-blobbar är ett JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet *&lt;function app name >* . Funktionen. *&lt;function namn >* )
* BlobType ("BlockBlob" eller "PageBlob")
* ContainerName
* BlobName
* ETag (en BLOB versions identifierare, till exempel: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Utlösare – samtidighet och minnes användning

BLOB-utlösaren använder en kö internt, så det maximala antalet samtidiga funktions anrop styrs av [köernas konfiguration i Host. JSON](functions-host-json.md#queues). Standardinställnings gränsen samtidighet till 24 anrop. Den här gränsen gäller separat för varje funktion som använder en BLOB-utlösare.

[Förbruknings planen](functions-scale.md#how-the-consumption-and-premium-plans-work) begränsar en Function-app på en virtuell dator (VM) till 1,5 GB minne. Minne används av varje intern körning av funktions instansen och av Functions-körningen. Om en BLOB-utlöst funktion läser in hela blobben i minnet är den maximala mängd minne som används av den funktionen bara för blobbar 24 * maximal BLOB-storlek. Till exempel skulle en Function-app med tre BLOB-utlöst funktioner och standardinställningarna ha ett maximalt antal per VM-concurrency på 3 * 24 = 72 funktions anrop.

Java Script-och Java-funktioner läser in hela blobben i C# minnet och fungerar om du binder till `string`, `Byte[]` eller Poco.

## <a name="trigger---polling"></a>Utlös vid avsökning

Om den BLOB-behållare som övervakas innehåller fler än 10 000 blobbar (över alla behållare) genomsöker Functions-körningen loggfilerna för att se om det finns nya eller ändrade blobbar. Den här processen kan resultera i fördröjningar. En funktion kanske inte utlöses förrän flera minuter eller längre efter att blobben har skapats.

> [!WARNING]
> Dessutom [skapas lagrings loggar på grund av "bästa prestanda"](/rest/api/storageservices/About-Storage-Analytics-Logging) . Det finns ingen garanti för att alla händelser har registrerats. Under vissa omständigheter kan loggarna missas.
> 
> Om du behöver en snabbare eller mer tillförlitlig BLOB-bearbetning bör du överväga att skapa ett [Queue-meddelande](../storage/queues/storage-dotnet-how-to-use-queues.md) när du skapar blobben. Använd sedan en [Queue-utlösare](functions-bindings-storage-queue.md) i stället för en BLOB-utlösare för att bearbeta blobben. Ett annat alternativ är att använda Event Grid; i självstudien får du [automatiskt ändra storlek på överförda bilder med hjälp av event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Indata

Använd en indata-bindning för Blob Storage för att läsa blobbar.

## <a name="input---example"></a>Indatamängds exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [ C# skript kod (. CSX)](functions-reference-csharp.md) som använder bindningarna. Funktionen gör en kopia av en text-blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* -filen används egenskapen `queueTrigger` metadata för att ange BLOB-namnet i `path`-egenskaperna:

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

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är C# skript koden:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [JavaScript-kod](functions-reference-node.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* -filen används egenskapen `queueTrigger` metadata för att ange BLOB-namnet i `path`-egenskaperna:

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

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [python-kod](functions-reference-python.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* -filen används egenskapen `queueTrigger` metadata för att ange BLOB-namnet i `path`-egenskaperna:

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

I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är python-koden:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare, slå upp BLOB-namn från frågesträng](#http-trigger-look-up-blob-name-from-query-string)
* [Köa utlösare, ta emot BLOB-namn från köa meddelande](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-utlösare, slå upp BLOB-namn från frågesträng

 I följande exempel visas en Java-funktion som använder `HttpTrigger`-anteckningen för att ta emot en parameter som innehåller namnet på en fil i en Blob Storage-behållare. @No__t-0-anteckningen läser sedan filen och skickar innehållet till funktionen som en `byte[]`.

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Köa utlösare, ta emot BLOB-namn från köa meddelande

 I följande exempel visas en Java-funktion som använder `QueueTrigger`-anteckningen för att ta emot ett meddelande som innehåller namnet på en fil i en Blob Storage-behållare. @No__t-0-anteckningen läser sedan filen och skickar innehållet till funktionen som en `byte[]`.

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

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du kommentaren `@BlobInput` på parametrar vars värde kommer från en blob.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>`.

---

## <a name="input---attributes"></a>In-attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar sökvägen till blobben och en `FileAccess`-parameter som visar Läs-eller Skriv åtgärder, som visas i följande exempel:

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

Du kan ställa in egenskapen `Connection` för att ange det lagrings konto som ska användas, som du ser i följande exempel:

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

Du kan använda attributet `StorageAccount` för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i avsnittet om [Utlösar-attribut](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

Attributet `@BlobInput` ger dig åtkomst till den blob som utlöste funktionen. Om du använder en byte mat ris med attributet anger du `dataType` till `binary`. Mer information finns i [indata-exemplet](#input---example) .

---

## <a name="input---configuration"></a>Indatamängds konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och attributet `Blob`.

|function. JSON-egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste vara inställd på `blob`. |
|**riktning** | Ej tillämpligt | Måste vara inställd på `in`. Undantag anges i [användnings](#input---usage) avsnittet. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar blobben i funktions koden.|
|**path** |**BlobPath** | Sökvägen till blobben. |
|**anslutningen** |**Anslutning**| Namnet på en app-inställning som innehåller den [lagrings anslutnings sträng](../storage/common/storage-configure-connection-string.md) som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` till "telestorage" söker Functions-körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den app-inställning som heter `AzureWebJobsStorage`.<br><br>Anslutnings strängen måste vara avsedd för ett allmänt lagrings konto, inte ett [enbart BLOB-lagrings konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Ej tillämpligt | **Åtkomst** | Anger om du kommer att läsa eller skriva. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Inmatad användning

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till BLOB-data med hjälp av `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Få åtkomst till BLOB-data via parametern som anges som [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Mer information finns i [indata-exemplet](#input---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Attributet `@BlobInput` ger dig åtkomst till den blob som utlöste funktionen. Om du använder en byte mat ris med attributet anger du `dataType` till `binary`. Mer information finns i [indata-exemplet](#input---example) .

---

## <a name="output"></a>Resultat

Använd Blob Storage utgående bindningar för att skriva blobbar.

## <a name="output---example"></a>Utdata-exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [ C# skript kod (. CSX)](functions-reference-csharp.md) som använder bindningarna. Funktionen gör en kopia av en text-blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* -filen används egenskapen `queueTrigger` metadata för att ange BLOB-namnet i `path`-egenskaperna:

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

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är C# skript koden:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [JavaScript-kod](functions-reference-node.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* -filen används egenskapen `queueTrigger` metadata för att ange BLOB-namnet i `path`-egenskaperna:

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

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *Function. JSON* -fil och [python-kod](functions-reference-python.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *Function. JSON* -filen används egenskapen `queueTrigger` metadata för att ange BLOB-namnet i `path`-egenskaperna:

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

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är python-koden:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare med hjälp av OutputBinding](#http-trigger-using-outputbinding-java)
* [Köa utlösare med funktions retur värde](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-utlösare med hjälp av OutputBinding (Java)

 I följande exempel visas en Java-funktion som använder `HttpTrigger`-anteckningen för att ta emot en parameter som innehåller namnet på en fil i en Blob Storage-behållare. @No__t-0-anteckningen läser sedan filen och skickar innehållet till funktionen som en `byte[]`. @No__t-0-anteckningen binder till `OutputBinding outputItem`, som sedan används av funktionen för att skriva innehållet i bloben för indata till den konfigurerade lagrings behållaren.

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

 I följande exempel visas en Java-funktion som använder `QueueTrigger`-anteckningen för att ta emot ett meddelande som innehåller namnet på en fil i en Blob Storage-behållare. @No__t-0-anteckningen läser sedan filen och skickar innehållet till funktionen som en `byte[]`. @No__t-0-anteckningen binder till funktionens retur värde, som sedan används av körnings miljön för att skriva innehållet i bloben för indata till den konfigurerade lagrings behållaren.

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

 I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du kommentaren `@BlobOutput` på funktions parametrar vars värde skrivs till ett objekt i Blob Storage.  Parameter typen ska vara `OutputBinding<T>`, där T är en inbyggd Java-typ eller en POJO.

---

## <a name="output---attributes"></a>Utdata-attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar sökvägen till blobben och en `FileAccess`-parameter som visar Läs-eller Skriv åtgärder, som visas i följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Du kan ställa in egenskapen `Connection` för att ange det lagrings konto som ska användas, som du ser i följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

Attributet `@BlobOutput` ger dig åtkomst till den blob som utlöste funktionen. Om du använder en byte mat ris med attributet anger du `dataType` till `binary`. Mer information finns i [utdata-exemplet](#output---example) .

---

Ett fullständigt exempel finns i [output-exemplet](#output---example).

Du kan använda attributet `StorageAccount` för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i avsnittet om [Utlösar-attribut](#trigger---attributes).

## <a name="output---configuration"></a>Utdata-konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och attributet `Blob`.

|function. JSON-egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste vara inställd på `blob`. |
|**riktning** | Ej tillämpligt | Måste vara inställd på `out` för en utgående bindning. Undantag anges i [användnings](#output---usage) avsnittet. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar blobben i funktions koden.  Ange till `$return` om du vill referera till funktionens retur värde.|
|**path** |**BlobPath** | Sökvägen till BLOB-behållaren. |
|**anslutningen** |**Anslutning**| Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` till "telestorage" söker Functions-körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den app-inställning som heter `AzureWebJobsStorage`.<br><br>Anslutnings strängen måste vara avsedd för ett allmänt lagrings konto, inte ett [enbart BLOB-lagrings konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Ej tillämpligt | **Åtkomst** | Anger om du kommer att läsa eller skriva. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utmatnings användning

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I Java Script får du åtkomst till BLOB-data med hjälp av `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Du kan deklarera funktions parametrar som följande typer för att skriva ut till Blob Storage:

* Strängar som `func.Out(str)`
* Strömmar som `func.Out(func.InputStream)`

Mer information finns i [utdata-exemplet](#output---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Attributet `@BlobOutput` ger dig åtkomst till den blob som utlöste funktionen. Om du använder en byte mat ris med attributet anger du `dataType` till `binary`. Mer information finns i [utdata-exemplet](#output---example) .

---

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Enheten |  Referens |
|---|---|
| Blob | [BLOB-felkoder](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tabell, kö |  [Lagrings fel koder](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö |  [Troubleshooting](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) (Felsökning) |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
