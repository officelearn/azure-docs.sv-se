---
title: Azure Blob storage-utdatabindning för Azure-funktioner
description: Lär dig hur du tillhandahåller Azure Blob-lagringsdata till en Azure-funktion.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277250"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Azure Blob storage-utdatabindning för Azure-funktioner

Med utdatabindningen kan du ändra och ta bort blob-lagringsdata i en Azure-funktion.

Information om inställnings- och konfigurationsinformation finns i [översikten](./functions-bindings-storage-blob.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

Följande exempel är en [C#-funktion](functions-dotnet-class-library.md) som använder en blob-utlösare och två utdatablolobbindningar. Funktionen utlöses genom att en bildblobb skapas i *exempelbildsbehållaren.* Det skapar små och medelstora kopior av bilden blob.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
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

}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

<!--Same example for input and output. -->

I följande exempel visas blob-indata och utdatabindningar i en *function.json-fil* och [C#script (.csx)](functions-reference-csharp.md) kod som använder bindningarna. Funktionen gör en kopia av en textblob. Funktionen utlöses av ett kömeddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *filen function.json* `queueTrigger` används egenskapen metadata för att ange `path` blob-namnet i egenskaperna:

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

<!--Same example for input and output. -->

I följande exempel visas blob-indata och utdatabindningar i en *function.json-fil* och [JavaScript-kod](functions-reference-node.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett kömeddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *filen function.json* `queueTrigger` används egenskapen metadata för att ange `path` blob-namnet i egenskaperna:

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

I följande exempel visas blob-indata och utdatabindningar i en *function.json-fil* och [Python-kod](functions-reference-python.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett kömeddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *filen function.json* `queueTrigger` används egenskapen metadata för att ange `path` blob-namnet i egenskaperna:

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är Python-koden:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare med Hjälp av OutputBinding](#http-trigger-using-outputbinding-java)
* [Köutlösare med hjälp av funktionsreturvärde](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-utlösare med Hjälp av OutputBinding (Java)

 I följande exempel visas en `HttpTrigger` Java-funktion som använder anteckningen för att ta emot en parameter som innehåller namnet på en fil i en blob-lagringsbehållare. Anteckningen `BlobInput` läser sedan filen och skickar dess innehåll `byte[]`till funktionen som en . Anteckningen `BlobOutput` binder till `OutputBinding outputItem`, som sedan används av funktionen för att skriva innehållet i indatabloben till den konfigurerade lagringsbehållaren.

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Köutlösare med hjälp av funktionsreturvärde (Java)

 I följande exempel visas en `QueueTrigger` Java-funktion som använder anteckningen för att ta emot ett meddelande som innehåller namnet på en fil i en blob-lagringsbehållare. Anteckningen `BlobInput` läser sedan filen och skickar dess innehåll `byte[]`till funktionen som en . Anteckningen `BlobOutput` binder till funktionsreturvärdet, som sedan används av körningen för att skriva innehållet i indatabloben till den konfigurerade lagringsbehållaren.

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

 I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `@BlobOutput` på funktionsparametrar vars värde skulle skrivas till ett objekt i blob-lagring.  Parametertypen ska `OutputBinding<T>`vara , där T är någon inbyggd Java-typ eller en POJO.

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Attributets konstruktor tar sökvägen till `FileAccess` blobben och en parameter som anger läsning eller skrivning, som visas i följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Du kan `Connection` ange egenskapen för att ange vilket lagringskonto som ska användas, som visas i följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Attributet `@BlobOutput` ger dig åtkomst till bloben som utlöste funktionen. Om du använder en bytematris `dataType` `binary`med attributet ställer du in på . Mer information finns i [utdataexemplet.](#example)

---

Ett fullständigt exempel finns i [Exempel på utdata](#example).

Du kan `StorageAccount` använda attributet för att ange lagringskontot på klass-, metod- eller parameternivå. Mer information finns i [Trigger - attribut](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `Blob` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas `blob`in på . |
|**riktning** | Saknas | Måste ställas `out` in på för en utdatabindning. Undantag anges i [avsnittet användning.](#usage) |
|**Namn** | Saknas | Namnet på variabeln som representerar blobben i funktionskoden.  Ställ `$return` in på att referera till funktionens returvärde.|
|**Sökvägen** |**BlobPath** | Sökvägen till blob-behållaren. |
|**Anslutning** |**Anslutning**| Namnet på en appinställning som innehåller anslutningssträngen Lagring som ska användas för den här bindningen. Om appinställningsnamnet börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till `connection` exempel anger "MyStorage" söker körtiden Funktioner efter en appinställning med namnet "AzureWebJobsMyStorage". Om du `connection` lämnar tom använder körtiden Funktioner standardanslutningssträngen för `AzureWebJobsStorage`lagring i appinställningen som heter .<br><br>Anslutningssträngen måste vara för ett allmänt lagringskonto, inte ett [lagringskonto med endast blob.](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|Saknas | **Åtkomst** | Anger om du kommer att läsa eller skriva. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

I JavaScript kommer du åt `context.bindings.<name from function.json>`blob-data med .

# <a name="python"></a>[Python](#tab/python)

Du kan deklarera funktionsparametrar som följande typer för att skriva ut till blob-lagring:

* Strängar som`func.Out(str)`
* Strömmar som`func.Out(func.InputStream)`

Mer information finns i [utdataexemplet.](#example)

# <a name="java"></a>[Java](#tab/java)

Attributet `@BlobOutput` ger dig åtkomst till bloben som utlöste funktionen. Om du använder en bytematris `dataType` `binary`med attributet ställer du in på . Mer information finns i [utdataexemplet.](#example)

---

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning |  Referens |
|---|---|
| Blob | [Blob-felkoder](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Tabell, Kö |  [Lagringsfelkoder](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabell, Kö |  [Troubleshooting](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) (Felsökning) |

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när blob-lagringsdata ändras](./functions-bindings-storage-blob-trigger.md)
- [Läsa bloblagringsdata när en funktion körs](./functions-bindings-storage-blob-input.md)
