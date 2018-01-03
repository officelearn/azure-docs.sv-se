---
title: "Azure Blob storage-bindningar för Azure Functions"
description: "Förstå hur du använder Azure Blob storage-utlösare och bindningar i Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: c170b3e4addaed2ec870c4a518e8f74b3ca4b952
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Blob storage bindningar i Azure Functions. Azure Functions stöder utlösa indata och utdata bindningar för BLOB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [Endast BLOB storage-konton](../storage/common/storage-create-storage-account.md#blob-storage-accounts) stöds inte. BLOB storage-utlösare och bindningar kräver ett allmänt lagringskonto. 

## <a name="trigger"></a>Utlösare

Använd en Blob storage-utlösare för att starta en funktion när en ny eller uppdaterad blob har upptäckts. Blobbinnehållet tillhandahålls som indata för funktionen.

> [!NOTE]
> När du använder en blob-utlösare på en plan för förbrukning, kan det finnas upp till en 10 minuters fördröjning vid bearbetningen av nya blobbar när en funktionsapp är inaktiv. När funktionen appen körs bearbetas blobbar omedelbart. Överväg att något av följande alternativ för att undvika den här första fördröjningen:
> - Använda en apptjänstplan med alltid på aktiverad.
> - Använd en annan funktion för att utlösa blob bearbetning, till exempel ett kömeddelande som innehåller blobbnamnet på. Ett exempel finns i [blob-i/o-bindningar exempel senare i den här artikeln](#input--output---example).

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
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

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

I följande exempel visas en blob-utlösare bindning i en *function.json* fil- och [JavaScript-kod] (funktioner-referens-node.md) som använder bindningen. Funktionen skriver en logg när en blob läggs till eller uppdateras i den `samples-workitems` behållare.

Här är den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>Utlösaren - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en blob-utlösare:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs), som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

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

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

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
|**sökväg** | **BlobPath** |Behållare för övervakning.  Kan vara en [blob namnmönstret](#trigger-blob-name-patterns). | 
|**anslutning** | **Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen för lagring för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Till exempel om du ställer in `connection` för ”MyStorage” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom Functions-runtime använder standard lagringsanslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.<br><br>Anslutningssträngen får inte vara för ett allmänt lagringskonto en [endast blob storage-konto](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösaren - användning

Få åtkomst till blob-data i C# och C# skript, med hjälp av en metodparameter som `Stream paramName`. I C# skript `paramName` anges värdet i den `name` -egenskapen för *function.json*. Du kan binda till någon av följande typer:

* `TextReader`
* `Stream`
* `ICloudBlob`(kräver ”inout” bindning riktning i *function.json*)
* `CloudBlockBlob`(kräver ”inout” bindning riktning i *function.json*)
* `CloudPageBlob`(kräver ”inout” bindning riktning i *function.json*)
* `CloudAppendBlob`(kräver ”inout” bindning riktning i *function.json*)

Som anges är en del av dessa typer kräver en `inout` bindning riktning i *function.json*. Den här riktningen stöds inte av standardredigeraren i Azure-portalen så du måste använda redigeraren.

Om texten blobbar förväntas, du kan binda till den `string` typen. Detta rekommenderas endast om blobbstorleken är liten, som hela blobbinnehållet läses in i minnet. Vanligtvis är det bättre att använda en `Stream` eller `CloudBlockBlob` typen.

I JavaScript, åt inkommande blobbdata med hjälp av `context.bindings.<name>`.

## <a name="trigger---blob-name-patterns"></a>Utlösaren - mönster för blob-namn

Du kan ange ett namn på mönster i blob i den `path` egenskap i *function.json* eller i den `BlobTrigger` Attributkonstruktorn. Namnmönstret kan vara en [filter eller bindande uttryck](functions-triggers-bindings.md#binding-expressions-and-patterns).

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

Om blob heter *{20140101}-soundfile.mp3*, `name` variabelvärdet i Funktionskoden är *soundfile.mp3*. 

### <a name="get-file-name-and-extension"></a>Hämta filnamn och tillägg

I följande exempel visas hur du binda till blob-filnamnet och filnamnstillägget separat:

```json
"path": "input/{blobname}.{blobextension}",
```
Om blob heter *ursprungliga Blob1.txt*, värdet för den `blobname` och `blobextension` variabler i Funktionskoden är *ursprungliga Blob1* och *txt*.

## <a name="trigger---metadata"></a>Utlösaren - metadata

Blob-utlösare innehåller flera metadataegenskaper för. De här egenskaperna kan användas som en del av bindande uttryck i andra bindningar eller parametrar i din kod. Dessa värden har samma semantik som den [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) typen.


|Egenskap  |Typ  |Beskrivning  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Sökvägen till den utlösande blobben.|
|`Uri`|`System.Uri`|Den blob-URI för den primära platsen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Blobens Systemegenskaper. |
|`Metadata` |`IDictionary<string,string>`|Användardefinierade metadata för blob.|

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

## <a name="trigger---polling-for-large-containers"></a>Utlösaren - avsökning för stora behållare

Om blob-behållaren som övervakas innehåller fler än 10 000 blobbar, loggfiler funktioner runtime genomsökningar kan du titta på för nya eller ändrade BLOB. Den här processen kan orsaka försening. En funktion kan hämta aktiveras inte förrän flera minuter eller längre efter blob skapas. Dessutom [lagring loggfiler skapas på ”bästa prestanda”](/rest/api/storageservices/About-Storage-Analytics-Logging) basis. Det är inte säkert att alla händelser fångas. Loggar under vissa förhållanden kan missas. Om du behöver snabbare och mer tillförlitlig blob-bearbetning kan du skapa en [kömeddelande](../storage/queues/storage-dotnet-how-to-use-queues.md) när du skapar blob. Använd sedan en [kö utlösaren](functions-bindings-storage-queue.md) i stället för en blob-trigger för att bearbeta blob. Ett annat alternativ är att använda händelsen rutnät. Se Självstudierna [automatisera storleksändring upp bilder med hjälp av händelse rutnätet](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input--output"></a>Indata och utdata

Använda Blob storage indata och utdata bindningar för att läsa och skriva BLOB.

## <a name="input--output---example"></a>Indata och utdata - exempel

Finns i det språkspecifika:

* [C#](#input--output---c-example)
* [C#-skript (.csx)](#input--output---c-script-example)
* [JavaScript](#input--output---javascript-example)

### <a name="input--output---c-example"></a>Indata och utdata - C#-exempel

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

### <a name="input--output---c-script-example"></a>Indata och utdata - exempel på C#-skript

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

Den [configuration](#input--output---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input--output---javascript-example"></a>Indata och utdata - JavaScript-exempel

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

Den [configuration](#input--output---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input--output---attributes"></a>Indata och utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

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

En komplett exempel finns [indata och utdata - C#-exempel](#input--output---c-example).

Du kan använda den `StorageAccount` -attribut som anger storage-konto på klass, metoden eller parametern-nivå. Mer information finns i [utlösaren - attribut](#trigger---attributes).

## <a name="input--output---configuration"></a>Indata och utdata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Blob` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | måste anges till `blob`. |
|**riktning** | Saknas | Måste anges till `in` för en indatabindning eller out för en bindning för utdata. Undantag anges i den [användning](#input--output---usage) avsnitt. |
|**Namn** | Saknas | Namnet på variabeln som representerar blob i funktionskoden.  Ange till `$return` att referera till returvärde för funktion.|
|**sökväg** |**BlobPath** | Sökvägen till blob. | 
|**anslutning** |**Anslutning**| Namnet på en appinställning som innehåller anslutningssträngen för lagring för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Till exempel om du ställer in `connection` för ”MyStorage” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom Functions-runtime använder standard lagringsanslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.<br><br>Anslutningssträngen får inte vara för ett allmänt lagringskonto en [endast blob storage-konto](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|Saknas | **Åtkomst** | Anger om du läsning eller skrivning. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input--output---usage"></a>Indata och utdata - användning

I C#-klassbibliotek och C# skript för åtkomst till blob med hjälp av en metodparameter som `Stream paramName`. I C# skript `paramName` anges värdet i den `name` -egenskapen för *function.json*. Du kan binda till någon av följande typer:

* `out string`
* `TextWriter` 
* `TextReader`
* `Stream`
* `ICloudBlob`(kräver ”inout” bindning riktning i *function.json*)
* `CloudBlockBlob`(kräver ”inout” bindning riktning i *function.json*)
* `CloudPageBlob`(kräver ”inout” bindning riktning i *function.json*)
* `CloudAppendBlob`(kräver ”inout” bindning riktning i *function.json*)

Som anges är en del av dessa typer kräver en `inout` bindning riktning i *function.json*. Den här riktningen stöds inte av standardredigeraren i Azure-portalen så du måste använda redigeraren.

Om du läser text blobbar, du kan binda till en `string` typen. Den här typen rekommenderas endast om blobbstorleken är liten, som hela blobbinnehållet läses in i minnet. Vanligtvis är det bättre att använda en `Stream` eller `CloudBlockBlob` typen.

I JavaScript, få åtkomst till blob-data med `context.bindings.<name>`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en Snabbstart som använder en Blob storage-utlösare](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
