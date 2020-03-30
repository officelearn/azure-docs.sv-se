---
title: Azure Blob-lagringsindatabindning för Azure-funktioner
description: Lär dig hur du tillhandahåller Azure Blob-lagringsdata till en Azure-funktion.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e074d7d74c0c5f020cb8086124634b25012927db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77202156"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Azure Blob-lagringsindatabindning för Azure-funktioner

Indatabindningen gör att du kan läsa blob lagringsdata som indata till en Azure-funktion.

Information om inställnings- och konfigurationsinformation finns i [översikten](./functions-bindings-storage-blob.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

Följande exempel är en [C#-funktion](functions-dotnet-class-library.md) som använder en köutlösare och en indatablolobbindning. Kömeddelandet innehåller namnet på blobben och funktionen loggar storleken på blobben.

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är Python-koden:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="java"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare, slå upp blobnamn från frågesträngen](#http-trigger-look-up-blob-name-from-query-string)
* [Köutlösare, ta emot blobnamn från kömeddelande](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-utlösare, slå upp blobnamn från frågesträngen

 I följande exempel visas en `HttpTrigger` Java-funktion som använder anteckningen för att ta emot en parameter som innehåller namnet på en fil i en blob-lagringsbehållare. Anteckningen `BlobInput` läser sedan filen och skickar dess innehåll `byte[]`till funktionen som en .

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Köutlösare, ta emot blobnamn från kömeddelande

 I följande exempel visas en `QueueTrigger` Java-funktion som använder anteckningen för att ta emot ett meddelande som innehåller namnet på en fil i en blob-lagringsbehållare. Anteckningen `BlobInput` läser sedan filen och skickar dess innehåll `byte[]`till funktionen som en .

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

I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `@BlobInput` på parametrar vars värde skulle komma från en blob.  Den här anteckningen kan användas med inbyggda Java-typer, `Optional<T>`POJOs eller nullable värden med .

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Attributets konstruktor tar sökvägen till `FileAccess` blobben och en parameter som anger läsning eller skrivning, som visas i följande exempel:

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

Du kan `Connection` ange egenskapen för att ange vilket lagringskonto som ska användas, som visas i följande exempel:

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

Du kan `StorageAccount` använda attributet för att ange lagringskontot på klass-, metod- eller parameternivå. Mer information finns i [Utlösare - attribut och anteckningar](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Attributet `@BlobInput` ger dig åtkomst till bloben som utlöste funktionen. Om du använder en bytematris `dataType` `binary`med attributet ställer du in på . Mer information finns i [indataexemplet.](#example)

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `Blob` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas `blob`in på . |
|**riktning** | Saknas | Måste ställas `in`in på . Undantag anges i [avsnittet användning.](#usage) |
|**Namn** | Saknas | Namnet på variabeln som representerar blobben i funktionskoden.|
|**Sökvägen** |**BlobPath** | Sökvägen till blobben. |
|**Anslutning** |**Anslutning**| Namnet på en appinställning som innehåller [anslutningssträngen Lagring](../storage/common/storage-configure-connection-string.md) som ska användas för den här bindningen. Om appinställningsnamnet börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till `connection` exempel anger "MyStorage" söker körtiden Funktioner efter en appinställning med namnet "AzureWebJobsMyStorage". Om du `connection` lämnar tom använder körtiden Funktioner standardanslutningssträngen för `AzureWebJobsStorage`lagring i appinställningen som heter .<br><br>Anslutningssträngen måste vara för ett allmänt lagringskonto, inte ett [lagringskonto med endast blob.](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|Saknas | **Åtkomst** | Anger om du kommer att läsa eller skriva. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Åtkomst blob `context.bindings.<NAME>` data `<NAME>` med var matchar det värde som definieras i *function.json*.

# <a name="python"></a>[Python](#tab/python)

Få åtkomst till blob-data via parametern som skrivs som [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Mer information finns i [indataexemplet.](#example)

# <a name="java"></a>[Java](#tab/java)

Attributet `@BlobInput` ger dig åtkomst till bloben som utlöste funktionen. Om du använder en bytematris `dataType` `binary`med attributet ställer du in på . Mer information finns i [indataexemplet.](#example)

---

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när blob-lagringsdata ändras](./functions-bindings-storage-blob-trigger.md)
- [Skriva bloblagringsdata från en funktion](./functions-bindings-storage-blob-output.md)
