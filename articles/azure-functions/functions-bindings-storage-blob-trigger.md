---
title: Azure Blob-lagringsutlösare för Azure-funktioner
description: Lär dig hur du kör en Azure-funktion när Azure Blob-lagringsdata ändras.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 61fbaf37577efdab0b147d437ae78fc4df0764cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084965"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Azure Blob-lagringsutlösare för Azure-funktioner

Blob-lagringsutlösaren startar en funktion när en ny eller uppdaterad blob identifieras. Blob-innehållet tillhandahålls som [indata till funktionen](./functions-bindings-storage-blob-input.md).

Azure Blob-lagringsutlösaren kräver ett allmänt lagringskonto. Om du vill använda ett blob-konto, eller om ditt program har särskilda behov, går du igenom alternativen för att använda den här utlösaren.

Information om inställnings- och konfigurationsinformation finns i [översikten](./functions-bindings-storage-blob.md).

## <a name="alternatives"></a>Alternativ

### <a name="event-grid-trigger"></a>Utlösare av händelserutnät

[Utlösaren för händelserutnät](functions-bindings-event-grid.md) har också inbyggt stöd för [blob-händelser](../storage/blobs/storage-blob-event-overview.md). Använd händelserutnät i stället för Blob-lagringsutlösaren för följande scenarier:

- **Lagringskonton med endast blob:** [Lagringskonton med endast Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) stöds för blob-indata- och utdatabindningar men inte för blob-utlösare.

- **Hög skala:** Hög skala kan definieras löst som behållare som har mer än 100 000 blobbar i dem eller lagringskonton som har fler än 100 blob-uppdateringar per sekund.

- **Minimera svarstiden**: Om din funktionsapp finns i förbrukningsplanen kan det ta upp till 10 minuters fördröjning i bearbetningen av nya blobbar om en funktionsapp har gått inaktiv. För att undvika den här svarstiden kan du växla till en App Service-plan med Always On aktiverat. Du kan också använda en [event grid-utlösare](functions-bindings-event-grid.md) med ditt Blob-lagringskonto. Ett exempel finns i [självstudien Händelserutnät](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Se [bildstorleken med händelserutnätssjälvstudien](../event-grid/resize-images-on-storage-blob-upload-event.md) för ett exempel på händelserutnät.

### <a name="queue-storage-trigger"></a>Kölagringsutlösare

En annan metod för bearbetning av blobbar är att skriva kömeddelanden som motsvarar blobbar som skapas eller ändras och sedan använda en [kölagringsutlösare](./functions-bindings-storage-queue.md) för att börja bearbeta.

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skriver en logg `samples-workitems` när en blob läggs till eller uppdateras i behållaren.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Strängen `{name}` i blob-utlösarbanan `samples-workitems/{name}` skapar ett [bindningsuttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktionskod för att komma åt filnamnet på den utlösande blobben. Mer information finns i [Blob-namnmönster](#blob-name-patterns) senare i den här artikeln.

Mer information om `BlobTrigger` attributet finns i [attribut och anteckningar](#attributes-and-annotations).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en blob-utlösare som binder i en *function.json-fil* och kod som använder bindningen. Funktionen skriver en logg när en blob läggs `samples-workitems` till eller uppdateras i [behållaren](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Här är bindningsdata i *filen function.json:*

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

Strängen `{name}` i blob-utlösarbanan `samples-workitems/{name}` skapar ett [bindningsuttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktionskod för att komma åt filnamnet på den utlösande blobben. Mer information finns i [Blob-namnmönster](#blob-name-patterns) senare i den här artikeln.

Mer information om *function.json-filegenskaper* finns i avsnittet [Konfiguration](#configuration) som förklarar dessa egenskaper.

Här är C # skriptkod som `Stream`binder till en:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Här är C # skriptkod som `CloudBlockBlob`binder till en:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en blob-utlösare som binder i en *function.json-fil* och [JavaScript-kod](functions-reference-node.md) som använder bindningen. Funktionen skriver en logg när en blob läggs `samples-workitems` till eller uppdateras i behållaren.

Här är *filen function.json:*

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

Strängen `{name}` i blob-utlösarbanan `samples-workitems/{name}` skapar ett [bindningsuttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktionskod för att komma åt filnamnet på den utlösande blobben. Mer information finns i [Blob-namnmönster](#blob-name-patterns) senare i den här artikeln.

Mer information om *function.json-filegenskaper* finns i avsnittet [Konfiguration](#configuration) som förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en blob-utlösare som binder i en *function.json-fil* och [Python-kod](functions-reference-python.md) som använder bindningen. Funktionen skriver en logg när en blob läggs `samples-workitems` till eller uppdateras i [behållaren](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Här är *filen function.json:*

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

Strängen `{name}` i blob-utlösarbanan `samples-workitems/{name}` skapar ett [bindningsuttryck](./functions-bindings-expressions-patterns.md) som du kan använda i funktionskod för att komma åt filnamnet på den utlösande blobben. Mer information finns i [Blob-namnmönster](#blob-name-patterns) senare i den här artikeln.

Mer information om *function.json-filegenskaper* finns i avsnittet [Konfiguration](#configuration) som förklarar dessa egenskaper.

Här är Python-koden:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Den här funktionen skriver en logg när en `myblob` blob läggs till eller uppdateras i behållaren.

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

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du följande attribut för att konfigurera en blob-utlösare:

* [BlobTriggerAttribut](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Attributets konstruktor tar en sökvägssträng som anger behållaren att titta på och eventuellt ett [blobnamnsmönster](#blob-name-patterns). Här är ett exempel:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Du kan `Connection` ange egenskapen för att ange vilket lagringskonto som ska användas, som visas i följande exempel:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Ett fullständigt exempel finns i [exempel på utlösare](#example).

* [LagringKontoAttribut](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Här kan du ange vilket lagringskonto som ska användas. Konstruktorn tar namnet på en appinställning som innehåller en lagringsanslutningssträng. Attributet kan användas på parameter-, metod- eller klassnivå. I följande exempel visas klassnivå och metodnivå:

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

Det lagringskonto som ska användas bestäms i följande ordning:

* Egenskapen `BlobTrigger` för `Connection` attributet.
* Attributet `StorageAccount` som tillämpas på `BlobTrigger` samma parameter som attributet.
* Attributet `StorageAccount` som används för funktionen.
* Attributet `StorageAccount` som används för klassen.
* Standardlagringskontot för funktionsappen ("AzureWebJobsStorage"-appinställningen).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Attributet `@BlobTrigger` används för att ge dig åtkomst till bloben som utlöste funktionen. Mer information finns i [utlösarexempeln.](#example)

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `BlobTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas `blobTrigger`in på . Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste ställas `in`in på . Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen. Undantag anges i [avsnittet användning.](#usage) |
|**Namn** | Saknas | Namnet på variabeln som representerar blobben i funktionskoden. |
|**Sökvägen** | **BlobPath** |[Behållaren](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) att övervaka.  Kan vara ett [blobnamnsmönster](#blob-name-patterns). |
|**Anslutning** | **Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen Lagring som ska användas för den här bindningen. Om appinställningsnamnet börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till `connection` exempel anger "MyStorage" söker körtiden Funktioner efter en appinställning med namnet "AzureWebJobsMyStorage". Om du `connection` lämnar tom använder körtiden Funktioner standardanslutningssträngen för `AzureWebJobsStorage`lagring i appinställningen som heter .<br><br>Anslutningssträngen måste vara för ett allmänt lagringskonto, inte ett [Blob-lagringskonto](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Åtkomst blob `context.bindings.<NAME>` data `<NAME>` med var matchar det värde som definieras i *function.json*.

# <a name="python"></a>[Python](#tab/python)

Få åtkomst till blob-data via parametern som skrivs som [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Mer information finns i [utlösarexempeln.](#example)

# <a name="java"></a>[Java](#tab/java)

Attributet `@BlobTrigger` används för att ge dig åtkomst till bloben som utlöste funktionen. Mer information finns i [utlösarexempeln.](#example)

---

## <a name="blob-name-patterns"></a>Blob-namnmönster

Du kan ange ett blobnamnsmönster i egenskapen `path` i *function.json* eller i `BlobTrigger` attributkonstruktorn. Namnmönstret kan vara ett [filter eller ett bindningsuttryck](./functions-bindings-expressions-patterns.md). Följande avsnitt innehåller exempel.

### <a name="get-file-name-and-extension"></a>Hämta filnamn och filtillägg

I följande exempel visas hur du binder till blob-filnamnet och tillägget separat:

```json
"path": "input/{blobname}.{blobextension}",
```

Om blobben heter *original-Blob1.txt*är `blobname` värdena för och `blobextension` variablerna i funktionskoden *original-Blob1* och *txt*.

### <a name="filter-on-blob-name"></a>Filtrera efter blobnamn

Följande exempel utlöser endast blobbar `input` i behållaren som börjar med strängen "original-":

```json
"path": "input/original-{name}",
```

Om blobnamnet är *original-Blob1.txt*är `Blob1` `name` värdet för variabeln i funktionskoden .

### <a name="filter-on-file-type"></a>Filtrera på filtyp

I följande exempel utlöses endast *PNG-filer:*

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrera på klammerparenteser i filnamn

Om du vill leta efter klammerparenteser i filnamnen kan du escape klammerparenteserna med hjälp av två klammerparenteser. I följande exempel filtreras blobbar som har klammerparenteser i namnet:

```json
"path": "images/{{20140101}}-{name}",
```

Om blobben heter `name` * {20140101}-soundfile.mp3*är variabelvärdet i funktionskoden *soundfile.mp3*.

## <a name="metadata"></a>Metadata

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Metadata är inte tillgängligt i Python.

# <a name="java"></a>[Java](#tab/java)

Metadata är inte tillgängligt i Java.

---

## <a name="blob-receipts"></a>Blob-kvitton

Azure Functions-körningen säkerställer att ingen blob-utlösarfunktion anropas mer än en gång för samma nya eller uppdaterade blob. För att avgöra om en viss blob-version har bearbetats underhålls *blobinleveranser*.

Azure Functions lagrar blob-kvitton i en behållare med namnet *azure-webjobs-hosts* i `AzureWebJobsStorage`Azure storage-account for your function-appen (definierad av appinställningen ). Ett blob-kvitto har följande information:

* Den utlösta funktionen ("*&lt;funktionsappnamn>*. Funktioner. funktionsnamn>", till exempel: "MyFunctionApp.Functions.CopyBlob") * &lt; *
* Behållarnamnet
* Blob-typen ("BlockBlob" eller "PageBlob")
* Blob-namnet
* ETag (en blob-versionsidentifierare, till exempel: "0x8D1DC6E70A277EF")

Om du vill tvinga fram upparbetning av en blob tar du bort blob-inleveransen för bloben från behållaren *azure-webjobs-hosts* manuellt. Även om upparbetning kanske inte sker omedelbart, är det garanterat att ske vid en senare tidpunkt. Om du vill bearbeta om direkt kan *scaninfo-blob* i *azure-webjobs-hosts/blobscaninfo* uppdateras. Alla blobbar med en senast ändrad tidsstämpel efter att egenskapen `LatestScan` har skannats igen.

## <a name="poison-blobs"></a>Giftblobar

När en blob-utlösarfunktion misslyckas för en viss blob, försöker Azure Functions som fungerar totalt 5 gånger som standard.

Om alla 5 försök misslyckas lägger Azure Functions till ett meddelande i en lagringskö med namnet *webjobs-blobtrigger-poison*. Det maximala antalet återförsök kan konfigureras. Samma MaxDequeueCount-inställning används för hantering av giftblobbhantering och hantering av giftkömeddelande. Kömeddelandet för giftblobar är ett JSON-objekt som innehåller följande egenskaper:

* FunctionId (i * &lt;formatfunktionsappens namn>*. Funktioner. funktionsnamn>) * &lt; *
* BlobType ("BlockBlob" eller "PageBlob")
* ContainerName
* BlobName (BlobName)
* ETag (en blob-versionsidentifierare, till exempel: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Samtidighet och minnesanvändning

Blob-utlösaren använder en kö internt, så det maximala antalet samtidiga funktionsanrop styrs av [kökonfigurationen i host.json](functions-host-json.md#queues). Standardinställningarna begränsar samtidighet till 24 anrop. Den här gränsen gäller separat för varje funktion som använder en blob-utlösare.

[Förbrukningsplanen](functions-scale.md#how-the-consumption-and-premium-plans-work) begränsar en funktionsapp på en virtuell dator (VM) till 1,5 GB minne. Minne används av varje samtidigt köra funktionsinstans och av själva funktionerkörningen. Om en blob-utlöst funktion läser in hela blobben i minnet, är det maximala minne som används av den funktionen bara för blobbar 24 * maximal blob-storlek. Till exempel skulle en funktionsapp med tre blob-utlösta funktioner och standardinställningarna ha en maximal samtidighet per virtuell dator på 3*24 = 72 funktionsangeringar.

JavaScript- och Java-funktioner läser in hela blobben i minnet, och C#-funktioner gör det om du binder till `string`, `Byte[]`eller POCO.

## <a name="polling"></a>Polling

Avsökning fungerar som en hybrid mellan att inspektera loggar och köra periodiska behållarsökningar. Blobbar skannas i grupper om 10 000 åt gången med en fortsättningstoken som används mellan intervallen.

> [!WARNING]
> Dessutom [skapas lagringsloggar på "bästa ansträngning"-basis.](/rest/api/storageservices/About-Storage-Analytics-Logging) Det finns ingen garanti för att alla händelser fångas. Under vissa förhållanden kan loggar missas.
> 
> Om du behöver snabbare eller mer tillförlitlig blob-bearbetning kan du överväga att skapa ett [kömeddelande](../storage/queues/storage-dotnet-how-to-use-queues.md) när du skapar blobben. Använd sedan en [köutlösare](functions-bindings-storage-queue.md) i stället för en blob-utlösare för att bearbeta blobben. Ett annat alternativ är att använda Händelserutnät. se självstudien [Automatisera storleksändring uppladdade bilder med Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Nästa steg

- [Läsa bloblagringsdata när en funktion körs](./functions-bindings-storage-blob-input.md)
- [Skriva bloblagringsdata från en funktion](./functions-bindings-storage-blob-output.md)
