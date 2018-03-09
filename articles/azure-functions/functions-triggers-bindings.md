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
ms.date: 02/07/2018
ms.author: glenga
ms.openlocfilehash: 559cfee1a8116703371a5641cf4534b7ad6f7578
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions-utlösare och bindningar begrepp

Den här artikeln är en översikt över utlösare och bindningar i Azure Functions. Här beskrivs funktioner som är gemensamma för alla bindningar och alla språk som stöds.

## <a name="overview"></a>Översikt

En *utlösaren* definierar hur en funktion har anropats. En funktion måste ha exakt en utlösare. Utlösare har associerade data, vilket är vanligtvis nyttolasten som utlöste funktionen.

Indata och utdata *bindningar* tillhandahåller en deklarativ metod för att ansluta till data från i din kod. Bindningar är valfria och en funktion kan ha flera indata och utdata bindningar. 

Utlösare och bindningar kan du undvika hardcoding information om de tjänster som du arbetar med. Funktionen tar emot data (till exempel innehållet i ett kömeddelande) i funktionsparametrar. Du skickar data (till exempel för att skapa ett kömeddelande) med hjälp av returvärdet för funktionen, en `out` parameter, eller en [insamlingsobjekt](functions-reference-csharp.md#writing-multiple-output-values).

När du utvecklar funktioner med hjälp av Azure portal, utlösare och bindningar har konfigurerats i en *function.json* fil. Portalen innehåller ett gränssnitt för den här konfigurationen, men du kan redigera filen direkt genom att ändra till den **redigeraren**.

När du utvecklar funktioner genom att använda Visual Studio för att skapa en klassbiblioteket konfigurerar du utlösare och bindningar med pynta metoder och parametrar med attribut.

## <a name="supported-bindings"></a>Stöds bindningar

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Information om vilka bindningar finns i förhandsgranskningen eller godkänns för produktion finns [språk som stöds](supported-languages.md).

## <a name="register-binding-extensions"></a>Registrera bindning tillägg

I version 2.x av Azure Functions-runtime måste du explicit registrera den [bindning tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) som du använder i appen funktion. 

Tillägg levereras som NuGet-paket, där paketet vanligtvis namn börjar med [microsoft.azure.webjobs.extensions](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions).  Hur du installerar och registrerar bindning tillägg beror på hur du utvecklar dina funktioner: 

+ [Lokalt i C# med hjälp av Visual Studio eller VS-kod](#precompiled-functions-c)
+ [Lokalt med hjälp av Azure Functions grundläggande verktyg](#local-development-azure-functions-core-tools)
+ [I Azure-portalen](#azure-portal-development) 

Det finns en grundläggande uppsättning bindningar i version 2.x som inte har angetts som tillägg. Du behöver inte registrera tillägg för följande utlösare och bindningar: HTTP-timer- och Azure Storage. 

Information om hur du ställer in en funktionsapp att använda version 2.x för Functions-runtime finns [så avsedda för Azure Functions-runtime versioner](set-runtime-version.md). Version 2.x för Functions-runtime är för närvarande under förhandsgranskning. 

Paketversionerna visas i det här avsnittet tillhandahålls endast som exempel. Kontrollera den [NuGet.org plats](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions) att fastställa vilken version av ett visst tillägg som krävs av andra beroenden i funktionen appen.    

###  <a name="local-c-development-using-visual-studio-or-vs-code"></a>Lokala C# utveckling med hjälp av Visual Studio eller VS-kod 

När du använder Visual Studio eller Visual Studio-koden för att utveckla lokalt funktioner i C#, behöver du bara lägga till NuGet-paket för tillägget. 

+ **Visual Studio**: Använd NuGet Package Manager-verktyg. Följande [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) installerar Azure DB som Cosmos-tillägget från Package Manager-konsolen:

    ```
    Install-Package Microsoft.Azure.WebJobs.Extensions.CosmosDB -Version 3.0.0-beta6 
    ```
+ **Visual Studio Code**: du kan installera paket från en kommandotolk med hjälp av den [dotnet lägga till paket](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) kommandot i .NET-CLI på följande sätt:

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
    ```

### <a name="local-development-azure-functions-core-tools"></a>Lokal utveckling Azure Functions grundläggande verktyg

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

### <a name="azure-portal-development"></a>Azure portal-utveckling

När du skapar en funktion eller lägga till en bindning till en befintlig funktion, uppmanas du när tillägget för utlösare eller bindning som läggs till kräver registrering.   

När en varning visas för det specifika tillägget installeras, klickar du på **installera** att registrera tillägget. Du behöver bara installera varje tillägg en gång för en viss funktionsapp. 

>[!Note] 
>I portalen-installationen kan ta upp till 10 minuter på en plan för användning.

## <a name="example-trigger-and-binding"></a>Exempel utlösaren och bindning

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

Det andra elementet i den `bindings` matrisen är Azure Table Storage-utdatabindning. Den `type` och `direction` egenskaper identifiera bindningen. Den `name` egenskapen anger hur funktionen ger den nya tabellraden, i det här fallet med hjälp av funktionen returvärde. Namnet på tabellen `tableName`, och anslutningssträngen i appinställningen som identifieras av `connection`.

Visa och redigera innehållet i *function.json* i Azure-portalen klickar du på den **redigeraren** alternativet på den **integrera** för din funktion.

> [!NOTE]
> Värdet för `connection` är namnet på en appinställning som innehåller anslutningssträngen inte anslutningssträngen sig själv. Bindningar använder anslutningen strängar som lagras i appen inställningar för att tillämpa bäst rutin som *function.json* innehåller inte tjänsten hemligheter.

Här är C# skriptkod som fungerar med den här utlösaren och bindning. Observera att namnet på den parameter som innehåller innehållet i kön meddelandet är `order`; det här namnet är obligatoriskt eftersom den `name` egenskapsvärde i *function.json* är `order` 

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

I en klassbiblioteket, samma utlösare och bindningsinformationen &mdash; kön och tabellen namn, storage-konton fungerar parametrar för ingående och utgående &mdash; tillhandahålls av attribut i stället för en function.json-fil. Här är ett exempel:

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

- För utlösare är riktningen alltid `in`
- Inkommande och utgående bindningar använda `in` och `out`
- Vissa bindningar stöd för en särskild riktning `inout`. Om du använder `inout`, endast den **redigeraren** är tillgängliga i den **integrera** fliken.

När du använder [attribut i en klassbiblioteket](functions-dotnet-class-library.md) om du vill konfigurera utlösare och bindningar riktningen som anges i en attributkonstruktör eller härledas från parametertypen.

## <a name="using-the-function-return-value"></a>Med hjälp av returvärde för funktion

Språk som har ett returvärde binder du en output-bindning till det returnera värdet:

* I en C#-klassbiblioteket, gäller utdata bindning-attribut för metoden returvärdet.
* På andra språk, ange den `name` egenskap i *function.json* till `$return`.

Om du behöver skriva mer än ett objekt kan använda en [insamlingsobjekt](functions-reference-csharp.md#writing-multiple-output-values) i stället för returvärdet. Om det finns flera utdata-bindningar, kan du använda det returnera värdet för en av dem.

Finns i det språkspecifika:

* [C#](#c-example)
* [C#-skript (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C#-exempel

Här följer C#-kod som använder det returnera värdet för en bindning för utdata, följt av ett async-exempel:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Exempel på C#-skript

Här är utdata-bindning den *function.json* fil:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är den C# skriptkod, följt av ett async-exempel:

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>F #-exempel

Här är utdata-bindning den *function.json* fil:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är F #-kod:

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>JavaScript-exempel

Här är utdata-bindning den *function.json* fil:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

I JavaScript, går det returnera värdet i den andra parametern för `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
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

## <a name="binding-expressions-and-patterns"></a>Bindande uttryck och mönster

En av de viktigaste funktionerna i utlösare och bindningar är *bindningsuttryck*. I den *function.json* fil och parametrar och kod, du kan använda uttryck som matchar värden från olika källor.

De flesta uttryck identifieras genom att omsluta dem i klammerparenteser. Till exempel i en kö Utlösarfunktion `{queueTrigger}` matchar meddelandetexten för kön. Om den `path` -egenskapen för en blob utdata bindning är `container/{queueTrigger}` och funktionen utlöses av ett kömeddelande `HelloWorld`, en blob med namnet `HelloWorld` skapas.

Typer av bindande uttryck

* [App-inställningar](#binding-expressions---app-settings)
* [Utlösaren filnamn](#binding-expressions---trigger-file-name)
* [Utlösaren metadata](#binding-expressions---trigger-metadata)
* [JSON-nyttolaster](#binding-expressions---json-payloads)
* [GUID för nytt](#binding-expressions---create-guids)
* [Aktuellt datum och tid](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Bindande uttryck - app-inställningar

Som bästa praxis, hemligheter och anslutningssträngar ska hanteras med app-inställningar i stället för konfigurationsfiler. Detta begränsar åtkomst till dessa hemligheter och gör det säkert att lagra filer som *function.json* i offentliga källa kontrollen databaser.

Appinställningar är också användbara när du vill ändra konfigurationen baserat på miljön. I en testmiljö kan du vill övervaka en annan kö eller blob storage-behållare.

Appen inställningen bindande uttryck identifieras annorlunda från andra bindande uttryck: de kapslas in i procenttecken snarare än klammerparenteser. Till exempel om bindningssökväg för blob-utdata är `%Environment%/newblob.txt` och `Environment` app Inställningsvärdet är `Development`, en blob som kommer att skapas i den `Development` behållare.

När funktionen körs lokalt app inställningsvärden komma från den *local.settings.json* fil.

Observera att den `connection` egenskapen för utlösare och bindningar är ett specialfall och automatiskt löser värden som app-inställningar, utan procenttecken. 

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

### <a name="binding-expressions---trigger-file-name"></a>Bindande uttryck - utlösaren filnamn

Den `path` för en Blob utlösare kan vara ett mönster som kan du referera till namnet på den utlösande blobben i andra bindningar och fungera kod. Mönstret kan även inkludera filtreringskriterier som anger vilka blobbar kan utlösa ett funktionsanrop.

Till exempel i följande Blob-utlösaren bindning, den `path` mönstret är `sample-images/{filename}`, vilket skapar ett uttryck för bindning med namnet `filename`:

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
    ...
```

Uttrycket `filename` kan sedan användas i en output-bindning för att ange namnet på blob som skapas:

```json
    ...
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

Funktionskoden har åtkomst till den här samma värde med hjälp av `filename` som ett parameternamn:

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

Samma möjligheten att använda bindande uttryck och mönster gäller attribut i klassbibliotek. I följande exempel konstruktorparametrarna attributet är samma `path` värden som den föregående *function.json* exempel: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

Du kan också skapa uttryck för delar av filnamn, till exempel tillägget. Mer information om hur du använder uttryck och mönster i Blob-sökvägen finns på [lagring bindning blobbreferens](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Bindande uttryck - utlösaren metadata

Ange ytterligare metadatavärden förutom datanyttolasten som tillhandahålls av en utlösare (till exempel innehållet i kön meddelandet som utlöste en funktion), många utlösare. Dessa värden kan användas som indataparametrar i C# och F # eller egenskaper på den `context.bindings` objekt i JavaScript. 

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

### <a name="binding-expressions---json-payloads"></a>Bindande uttryck - JSON-nyttolaster

När en utlösare nyttolasten är JSON kan referera du till dess egenskaper i konfigurationen för andra bindningar i samma funktion och funktionskoden.

I följande exempel visas den *function.json* -filen för en webhook-funktion som tar emot ett blob-namn i JSON: `{"BlobName":"HelloWorld.txt"}`. Blob-indatabindning läser blob och HTTP utdata bindning returnerar blobbinnehållet i HTTP-svaret. Observera att Blob-indatabindning hämtar blobbnamnet av refererar direkt till den `BlobName` egenskap (`"path": "strings/{BlobName}"`)

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
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
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

Att arbeta i C# och F #, behöver du en klass som definierar de fält som ska avserialiseras som i följande exempel:

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

I JavaScript utförs automatiskt JSON-deserialisering.

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

#### <a name="dot-notation"></a>Punktnotation

Om vissa av egenskaperna i JSON-nyttolast-objekt med egenskaper kan referera du till dem direkt med hjälp av punktnotation. Anta exempelvis att din JSON ser ut så här:

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

Du kan gå direkt till `FileName` som `BlobName.FileName`. Med den här JSON-format, som här är vad den `path` egenskapen i föregående exempel skulle se ut:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

I C# behöver du två klasser:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

### <a name="binding-expressions---create-guids"></a>Bindande uttryck - skapa GUID

Den `{rand-guid}` bindande uttryck skapar ett GUID. Följande blob-sökväg i en `function.json` skapar en blob med ett namn, till exempel *50710cb5-84b9 - 4d 9 87 d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Bindande uttryck - aktuell tid

Bindningsuttrycket `DateTime` matchar `DateTime.UtcNow`. Följande blob-sökväg i en `function.json` skapar en blob med ett namn, till exempel *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>Bindning under körning

I C# och andra .NET-språk, kan du använda en tvingande bindning mönster, till skillnad från deklarativ bindningar i *function.json* och attribut. Tvingande bindning är användbar när bindande parametrar måste beräknas vid körning i stället för design tidpunkt. Mer information finns i [C#-utvecklare](functions-dotnet-class-library.md#binding-at-runtime) eller [C# skript för utvecklare](functions-reference-csharp.md#binding-at-runtime).

## <a name="functionjson-file-schema"></a>schemat för Function.JSON

Den *function.json* schema som finns på [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Hantering av Bindningsfel

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Länkar till alla relevanta fel ämnen för olika tjänster som stöds av funktioner finns i [bindning felkoder](functions-bindings-error-pages.md#binding-error-codes) avsnitt i den [Azure Functions felhantering](functions-bindings-error-pages.md) översiktsavsnittet.  

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
