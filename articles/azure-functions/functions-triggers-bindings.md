---
title: "Utlösare och bindningar i Azure Functions"
description: "Lär dig hur du använder utlösare och bindningar i Azure Functions för att ansluta din kodkörning online händelser och molnbaserade tjänster."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: a122271b5fdffd9db33a7dca5908e15f002041d7
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions-utlösare och bindningar begrepp

Den här artikeln är en översikt över utlösare och bindningar i Azure Functions. Här beskrivs funktioner som är gemensamma för alla bindningar och alla språk som stöds.

## <a name="overview"></a>Översikt

En *utlösaren* definierar hur en funktion har anropats. En funktion måste ha exakt en utlösare. Utlösare har associerade data, vilket är vanligtvis nyttolasten som utlöste funktionen.

Indata och utdata *bindningar* tillhandahåller en deklarativ metod för att ansluta till data från i din kod. Bindningar är valfria och en funktion kan ha flera indata och utdata bindningar. 

Utlösare och bindningar kan du undvika hardcoding information om de tjänster som du arbetar med. Du funktionen tar emot data (till exempel innehållet i ett kömeddelande) i funktionsparametrar. Du skickar data (till exempel för att skapa ett kömeddelande) med hjälp av returvärdet för funktionen, en `out` parameter, eller en [insamlingsobjekt](functions-reference-csharp.md#writing-multiple-output-values).

När du utvecklar funktioner med hjälp av Azure portal, utlösare och bindningar har konfigurerats i en *function.json* fil. Portalen innehåller ett gränssnitt för den här konfigurationen, men du kan redigera filen direkt genom att ändra till den **redigeraren**.

När du utvecklar funktioner genom att använda Visual Studio för att skapa en klassbiblioteket konfigurerar du utlösare och bindningar med pynta metoder och parametrar med attribut.

## <a name="supported-bindings"></a>Stöds bindningar

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Information om vilka bindningar finns i förhandsgranskningen eller godkänns för produktion finns [språk som stöds](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Exempel: kön utlösare och tabellen utdatabindning

Anta att du vill skriva en ny rad till Azure Table storage när ett nytt meddelande visas i Azure Queue storage. Det här scenariot kan implementeras med hjälp av en Azure Queue storage utlösare och Azure-tabellagring utdatabindning. 

Här är en *function.json* -filen för det här scenariot. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Det första elementet i den `bindings` matrisen är Queue storage utlösaren. Den `type` och `direction` egenskaper identifiera utlösaren. Den `name` egenskapen identifierar funktionsparametern som tar emot innehållet i kön meddelandet. Namnet på köns övervaka `queueName`, och anslutningssträngen i appinställningen som identifieras av `connection`.

Det andra elementet i den `bindings` matrisen är Azure Table Storage-utdatabindning. Den `type` och `direction` egenskaper identifiera bindningen. Den `name` egenskapen anger hur funktionen ger den nya tabellraden i det här fallet med hjälp av funktionen returvärdet. Namnet på tabellen `tableName`, och anslutningssträngen i appinställningen som identifieras av `connection`.

Visa och redigera innehållet i *function.json* i Azure-portalen klickar du på den **redigeraren** alternativet på den **integrera** för din funktion.

> [!NOTE]
> Värdet för `connection` är namnet på en appinställning som innehåller anslutningssträngen inte anslutningssträngen sig själv. Bindningar använder anslutningen strängar som lagras i appen inställningar för att tillämpa bäst rutin som *function.json* innehåller inte tjänsten hemligheter.

Här är C# skriptkod som fungerar med den här utlösaren och bindning. Observera att namnet på den parameter som innehåller innehållet i kön meddelandet är `order`; det här namnet är obligatoriskt eftersom den `name` egenskapsvärde i *function.json* är`order` 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Samma function.json fil kan användas med en JavaScript-funktion:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

I en klassbiblioteket, samma utlösare och bindningsinformationen &mdash; kön och tabellen namn, storage-konton fungerar parametrar för ingående och utgående &mdash; tillhandahålls av attribut:

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>Bindningen riktning

Alla utlösare och bindningar har en `direction` egenskap i den *function.json* fil:

- För utlösare är riktningen alltid`in`
- Inkommande och utgående bindningar använda `in` och`out`
- Vissa bindningar stöd för en särskild riktning `inout`. Om du använder `inout`, endast den **redigeraren** är tillgängliga i den **integrera** fliken.

När du använder [attribut i en klassbiblioteket](functions-dotnet-class-library.md) om du vill konfigurera utlösare och bindningar riktningen som anges i en attributkonstruktör eller härledas från parametertypen.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Använda Returtypen för funktionen för att returnera ett enda utflöde

Föregående exempel visar hur du använder funktionen returvärdet ge utdata till en bindning som anges i *function.json* med hjälp av särskilda värdet `$return` för den `name` egenskapen. (Detta stöds endast på språk som har ett returvärde, till exempel C# skript, JavaScript och F #.) Om en funktion har flera bindningar för utdata använder `$return` för endast en av bindningarna som utdata. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Exemplen nedan visar tillbaka hur typer som används med utdata bindningar i C# skript, JavaScript och F #.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>DataType-egenskapen för bindning

Använd parametertypen i .NET, definiera datatypen för indata. Till exempel använda `string` att binda till texten i en kö-utlösare, en bytematris läsa som binary och en anpassad typ att deserialisera ett POCO-objekt.

Språk som skrivs dynamiskt, till exempel JavaScript, använda den `dataType` egenskap i den *function.json* fil. Ange till exempel för att läsa innehållet i en HTTP-begäran i binärformat `dataType` till `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Andra alternativ för `dataType` är `stream` och `string`.

## <a name="resolving-app-settings"></a>Lösa app-inställningar

Som bästa praxis, hemligheter och anslutningssträngar ska hanteras med app-inställningar i stället för konfigurationsfiler. Detta begränsar åtkomst till dessa hemligheter och gör det säkert att lagra *function.json* i en offentlig källkontroll.

Appinställningar är också användbara när du vill ändra konfigurationen baserat på miljön. I en testmiljö kan du vill övervaka en annan kö eller blob storage-behållare.

Appinställningar är löst när ett värde som omges av procenttecken, `%MyAppSetting%`. Observera att den `connection` egenskapen för utlösare och bindningar är ett specialfall och löser automatiskt värden som app-inställningar. 

Följande exempel är en Azure Queue Storage-utlösare som använder en appinställning `%input-queue-name%` att definiera kön att utlösa på.

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
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-metadata-properties"></a>Utlösaren metadataegenskaper

Ange ytterligare metadatavärden förutom datanyttolasten som tillhandahålls av en utlösare (till exempel kön meddelandet som utlöste en funktion) många utlösare. Dessa värden kan användas som indataparametrar i C# och F # eller egenskaper på den `context.bindings` objekt i JavaScript. 

Till exempel stöder en Azure Queue storage-utlösare följande egenskaper:

* QueueTrigger - utlösa meddelandeinnehåll om en giltig sträng
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Dessa metadata-värdena är tillgängliga i *function.json* filegenskaper. Anta exempelvis att du använder en kö-utlösare och kön meddelandet innehåller namnet på en blob som du vill läsa. I den *function.json* filen som du kan använda `queueTrigger` metadataegenskapen i blob `path` egenskap, enligt följande exempel:

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

Information om metadataegenskaper för varje utlösare beskrivs i referensartikeln i fråga. Ett exempel finns [kö utlösaren metadata](functions-bindings-storage-queue.md#trigger---message-metadata). Dokumentation är också tillgänglig i den **integrera** för portalen, i den **dokumentationen** avsnittet nedan konfigurationsområde bindning.  

## <a name="binding-expressions-and-patterns"></a>Bindande uttryck och mönster

En av de viktigaste funktionerna i utlösare och bindningar är *bindningsuttryck*. Du kan definiera mönster för uttryck som sedan kan användas i andra bindningar eller koden i konfigurationen för en bindning. Utlösaren metadata kan också användas i bindande uttryck som visas i föregående avsnitt.

Anta att du vill ändra storlek på bilder i en viss behållare för blob storage, liknar den **storleksändring av** mallen i den **nya funktionen** sidan i Azure-portalen (finns i **prover**  scenariot). 

Här är den *function.json* definition:

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

Observera att den `filename` parameter används i både definition för blob-utlösare och blob-utdatabindning. Den här parametern kan också användas i funktionskod.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Samma möjligheten att använda bindande uttryck och mönster gäller attribut i klassbibliotek. Här är till exempel en funktion i en klassbiblioteket för storleksändring:

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
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

### <a name="create-guids"></a>Skapa GUID

Den `{rand-guid}` bindande uttryck skapar ett GUID. I följande exempel används en GUID för att skapa ett unikt blob-namn: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Aktuell tid

Bindningsuttrycket `DateTime` matchar `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>Binda till anpassade egenskaper för indata

Bindande uttryck kan även referera till egenskaper som är definierade i utlösaren nyttolasten sig själv. Du kanske vill binda dynamiskt till en blob storage-fil från ett filnamn som anges i en webhook.

Till exempel följande *function.json* använder en egenskap som kallas `BlobName` från nyttolasten utlösare:

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

Om du vill åstadkomma detta i C# och F #, måste du definiera en POCO som definierar vilka fält som ska avserialiseras i nyttolasten för utlösare.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JSON-deserialisering utförs automatiskt i JavaScript, och du kan använda egenskaperna direkt.

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

## <a name="configuring-binding-data-at-runtime"></a>Konfigurerar bindningsdata vid körning

I C# och andra .NET-språk, kan du använda en tvingande bindning mönster, till skillnad från deklarativ bindningar i *function.json* och attribut. Tvingande bindning är användbar när bindande parametrar måste beräknas vid körning i stället för design tidpunkt. Läs mer i [bindning under körning via tvingande bindningar](functions-reference-csharp.md#imperative-bindings) i C#-utvecklare.

## <a name="functionjson-file-schema"></a>schemat för Function.JSON

Den *function.json* schema som finns på [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="next-steps"></a>Nästa steg

Mer information om en specifik bindning finns i följande artiklar:

- [HTTP och webhooks](functions-bindings-http-webhook.md)
- [Timer](functions-bindings-timer.md)
- [Queue Storage](functions-bindings-storage-queue.md)
- [Blob Storage](functions-bindings-storage-blob.md)
- [Table Storage](functions-bindings-storage-table.md)
- [Händelsehubb](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Extern fil](functions-bindings-external-file.md)
