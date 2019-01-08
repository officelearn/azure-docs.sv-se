---
title: Utlösare och bindningar i Azure Functions
description: Lär dig hur du använder utlösare och bindningar i Azure Functions för att ansluta din kodkörning till online-händelser och molnbaserade tjänster.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/24/2018
ms.author: cshoe
ms.openlocfilehash: a44b348e0c41e96c575555f2b5c275e196284c5b
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074561"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions-utlösare och bindningar begrepp

Den här artikeln är en översikt över utlösare och bindningar i Azure Functions. Här beskrivs funktioner som är gemensamma för alla bindningar och alla språk som stöds.

## <a name="overview"></a>Översikt

En *utlösaren* definierar hur en funktion som anropas. En funktion måste ha exakt en utlösare. Utlösare har associerade data, vilket vanligtvis är nyttolasten som utlöste funktionen.

Indata och utdata *bindningar* tillhandahåller en deklarativ metod för att ansluta till data från i din kod. Bindningar är valfria och en funktion kan ha flera indatafiler och utdatabindningar. 

Utlösare och bindningar kan du undvika hardcoding information om de tjänster som du arbetar med. Funktionen tar emot data (till exempel innehållet i ett kömeddelande) i funktionsparametrar. Du kan skicka data (t.ex, för att skapa ett kömeddelande) med hjälp av det returnera värdet för funktionen. Olika sätt att skicka data finns i C# och C#-skript, `out` parametrar och [insamlaren objekt](functions-reference-csharp.md#writing-multiple-output-values).

När du utvecklar funktioner med hjälp av Azure portal, utlösare och bindningar har konfigurerats i en *function.json* fil. Portalen innehåller ett användargränssnitt för den här konfigurationen men du kan redigera filen direkt genom att ändra till den **Avancerad redigerare**.

När du utvecklar funktioner med Visual Studio för att skapa en Klassbiblioteket kan konfigurera du utlösare och bindningar som att dekorera metoder och parametrar med attribut.

## <a name="example-trigger-and-binding"></a>Exempel utlösare och -bindning

Anta att du vill skriva en ny rad till Azure Table storage när ett nytt meddelande visas i Azure Queue storage. Det här scenariot kan implementeras med hjälp av en kö för Azure storage-utlösare och en Azure Table storage-utdatabindning. 

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

Det första elementet i den `bindings` matrisen är Queue storage-utlösare. Den `type` och `direction` egenskaper identifiera utlösaren. Den `name` egensapen identifierar funktionsparametern som tar emot kö meddelandeinnehåll. Namnet på kön som ska övervaka `queueName`, och anslutningssträngen i appinställningen som identifieras av `connection`.

Det andra elementet i den `bindings` matrisen är Azure Table Storage-utdatabindning. Den `type` och `direction` egenskaper identifiera bindningen. Den `name` egenskap anger hur funktionen ger den nya tabellraden, i det här fallet returnerar värdet med hjälp av funktionen. Namnet på tabellen används i `tableName`, och anslutningssträngen i appinställningen som identifieras av `connection`.

Visa och redigera innehållet i *function.json* i Azure-portalen klickar du på den **Avancerad redigerare** alternativet på den **integrera** i din funktion.

> [!NOTE]
> Värdet för `connection` är namnet på en appinställning som innehåller anslutningssträngen inte anslutningssträngen själva. Bindningar använda anslutningen strängar som lagras i appen inställningar för att tillämpa bäst öva som *function.json* innehåller inte service hemligheter.

Här är C#-skriptkoden som fungerar med den här utlösaren och bindning. Observera att namnet på den parameter som innehåller meddelandeinnehållet kön är `order`; det här namnet är obligatoriskt eftersom den `name` egenskapsvärdet i *function.json* är `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
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

I en klassbiblioteket, samma utlösare och bindningsinformation &mdash; kön och tabellen namn, storage-konton kan fungera parametrar för indata och utdata &mdash; tillhandahålls av attribut i stället för en function.json-fil. Här är ett exempel:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
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

## <a name="supported-bindings"></a>Stöds bindningar

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Information om vilka bindningar finns i förhandsversion eller är godkända för användning i produktion finns i [språk som stöds](supported-languages.md).

## <a name="register-binding-extensions"></a>Registrera tillägg av bindning

I vissa utvecklingsmiljöer, måste du uttryckligen *registrera* en bindning som du vill använda. Tillägg av bindning har angetts i NuGet-paket och för att registrera ett tillägg som du installerar ett paket. I följande tabell anger när och hur du registrerar tillägg av bindning.

|Utvecklingsmiljö |Registrering<br/> i funktioner 1.x  |Registrering<br/> i funktioner 2.x  |
|---------|---------|---------|
|Azure Portal|Automatisk|[Automatisk med Kommandotolken](#azure-portal-development)|
|Lokalt med hjälp av Azure Functions Core Tools|Automatisk|[Använda Core Tools CLI-kommandon](#local-development-azure-functions-core-tools)|
|C#-klassbibliotek har med hjälp av Visual Studio 2017|[Använd NuGet-verktyg](#c-class-library-with-visual-studio-2017)|[Använd NuGet-verktyg](#c-class-library-with-visual-studio-2017)|
|C#-klassbibliotek har med hjälp av Visual Studio Code|Gäller inte|[Använda .NET Core CLI](#c-class-library-with-visual-studio-code)|

Följande bindningstyper av är undantag som inte kräver registrering av explicita eftersom de registreras automatiskt i alla versioner och miljöer: HTTP och timer.

### <a name="azure-portal-development"></a>Azure portal-utveckling

Det här avsnittet gäller enbart för Functions 2.x. Tillägg av bindning inte har uttryckligen registreras i Functions 1.x.

När du skapar en funktion eller lägga till en bindning, uppmanas du när tillägget för lagringsutlösare eller bindning kräver registrering. Besvarar frågan genom att klicka på **installera** att registrera tillägget. Installationen kan ta upp till 10 minuter i en förbrukningsplan.

Du behöver bara installera varje tillägg en gång för en viss funktionsapp. För stöds bindningar som inte är tillgängliga i portalen eller för att uppdatera den ett installerade tillägg, du kan också [manuellt installera eller uppdatera Azure Functions bindande tillägg från portalen](install-update-binding-extensions-manual.md).  

### <a name="local-development-azure-functions-core-tools"></a>Lokal utveckling Azure Functions Core Tools

Det här avsnittet gäller enbart för Functions 2.x. Tillägg av bindning inte har uttryckligen registreras i Functions 1.x.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
### <a name="c-class-library-with-visual-studio-2017"></a>C#-klassbibliotek med Visual Studio 2017

I **Visual Studio 2017**, du kan installera paket från Package Manager-konsolen med hjälp av den [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) kommandot, som visas i följande exempel:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Namnet på paketet som ska användas för en viss bindning har angetts i referensartikeln för bindningen. Ett exempel finns i den [paket i Service Bus-bindning referensartikeln](functions-bindings-service-bus.md#packages---functions-1x).

Ersätt `<target_version>` i det här exemplet med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org). Huvudversioner som motsvarar funktionskörningen 1.x och 2.x har angetts i referensartikeln för bindningen.

### <a name="c-class-library-with-visual-studio-code"></a>C#-klassbibliotek med Visual Studio Code

I **Visual Studio Code**, du kan installera paket från en kommandotolk med hjälp av den [dotnet lägga till paketet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) kommando i CLI för .NET Core, som visas i följande exempel:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

.NET Core CLI kan endast användas för Azure Functions 2.x-utveckling.

Namnet på paketet som ska användas för en viss bindning har angetts i referensartikeln för bindningen. Ett exempel finns i den [paket i Service Bus-bindning referensartikeln](functions-bindings-service-bus.md#packages---functions-1x).

Ersätt `<target_version>` i det här exemplet med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org). Huvudversioner som motsvarar funktionskörningen 1.x och 2.x har angetts i referensartikeln för bindningen.

## <a name="binding-direction"></a>Riktning för bindning

Alla utlösare och bindningar har en `direction` -egenskapen i den *function.json* fil:

- För utlösare är riktningen alltid `in`
- Bindningar för indata- och använda `in` och `out`
- Vissa bindningar stöd för en särskild riktning `inout`. Om du använder `inout`, endast den **Avancerad redigerare** är tillgänglig i den **integrera** fliken.

När du använder [attribut i en klassbiblioteket](functions-dotnet-class-library.md) för att konfigurera utlösare och bindningar, riktning har angetts i en attributkonstruktör eller härleds från parametertypen.

## <a name="using-the-function-return-value"></a>Med hjälp av returvärde för funktion

Du kan binda en utdatabindning till det returnera värdet på språk som har ett returvärde:

* I C# klassbiblioteket, gäller attributet utdata-bindning för returvärdet för metoden.
* I andra språk, ställer du in den `name` -egenskapen i *function.json* till `$return`.

Om det finns flera utdatabindningar kan du använda det returnera värdet för endast en av dem.

I C# och C#-skript, olika sätt att skicka data till en utdatabindning är `out` parametrar och [insamlaren objekt](functions-reference-csharp.md#writing-multiple-output-values).

Se språkspecifika exempel som visar användning av det returnera värdet:

* [C#](#c-example)
* [C#-skript (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>C#-exempel

Här är C#-kod som använder det returnera värdet för en utdatabindning, följt av ett async-exempel:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Exempel på C#-skript

Här är utdata-bindning i den *function.json* fil:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är den C#-skriptkoden, följt av ett async-exempel:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>F#exempel

Här är utdata-bindning i den *function.json* fil:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är den F# kod:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>JavaScript-exempel

Här är utdata-bindning i den *function.json* fil:

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

### <a name="python-example"></a>Python-exempel

Här är utdata-bindning i den *function.json* fil:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Här är Python-kod:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="binding-datatype-property"></a>DataType-egenskapen för bindning

I .NET, använder du parametertypen för att definiera datatypen för indata. Till exempel använda `string` att binda till texten i en kö-utlösare, en bytematris att läsa som binary och en anpassad typ att deserialisera till en POCO-objekt.

Språk som har skrivits dynamiskt, till exempel JavaScript, använda den `dataType` -egenskapen i den *function.json* fil. Exempelvis om du vill läsa innehållet i en HTTP-förfrågan i binärt format, ställer du in `dataType` till `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Andra alternativ för `dataType` är `stream` och `string`.

## <a name="binding-expressions-and-patterns"></a>Uttryck för bindning och mönster

En av de mest kraftfulla funktionerna i utlösare och bindningar är *bindning uttryck*. I den *function.json* fil och du kan använda uttryck som matchas till värden från olika källor i funktionsparametrar och kod.

De flesta uttryck identifieras genom att omsluta dem anger mellan klammerparenteser. Till exempel i en kö i utlösningsfunktion `{queueTrigger}` motsvarar meddelandetext för kön. Om den `path` -egenskapen för en blob-utdata bindningen är `container/{queueTrigger}` och funktionen som utlöses av ett kömeddelande `HelloWorld`, en blob med namnet `HelloWorld` skapas.

Typer av uttryck för bindning

* [Appinställningar](#binding-expressions---app-settings)
* [Filnamn för utlösare](#binding-expressions---trigger-file-name)
* [Utlösaren metadata](#binding-expressions---trigger-metadata)
* [JSON-nyttolaster](#binding-expressions---json-payloads)
* [Nytt GUID](#binding-expressions---create-guids)
* [Aktuellt datum och tid](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Uttryck för bindning - appinställningar

Som bästa praxis, ska hemligheter och anslutningssträngar hanteras med appinställningar i stället för konfigurationsfiler. Detta begränsar åtkomsten till dessa hemligheter och gör det säkert att lagra filer som *function.json* i offentliga källa källkontrollsdatabaser.

Appinställningar är också användbara när du vill ändra konfigurationen som baseras på vilken miljö. Till exempel i en testmiljö kan du övervaka en annan kö eller blob storage-behållare.

App inställningen bindning uttryck identifieras på olika sätt från andra uttryck för bindning: de placeras i procenttecken snarare än av klammerparenteser. Till exempel om bindningssökväg för blob-utdata är `%Environment%/newblob.txt` och `Environment` app Inställningsvärdet är `Development`, en blob skapas i den `Development` behållare.

När en funktion körs lokalt, app inställningen värdena kommer från den *local.settings.json* fil.

Observera att den `connection` egenskap av utlösare och bindningar är ett specialfall och löser automatiskt värden som appinställningar utan procenttecken. 

I följande exempel är en Azure Queue Storage-utlösare som använder en appinställning `%input-queue-name%` att definiera kön ska utlösas på.

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
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

### <a name="binding-expressions---trigger-file-name"></a>Uttryck för bindning - utlösaren filnamn

Den `path` för en Blob utlösare kan vara ett mönster som kan du referera till namnet på den utlösande bloben i andra bindningar och funktionen kod. Mönstret kan även inkludera filtreringskriterier som anger vilka blobar kan utlösa ett funktionsanrop.

I den följande Blob-utlösare som binder, till exempel den `path` mönstret är `sample-images/{filename}`, vilket skapar en bindning uttryck med namnet `filename`:

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

Uttrycket `filename` kan sedan användas i en utdatabindning för att ange namnet på bloben håller på att skapas:

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

Function-koden har åtkomst till den här samma värde med hjälp av `filename` som ett parameternamn:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Samma möjlighet att använda bindningen uttryck och mönster gäller attribut i klassbibliotek. I följande exempel attributet konstruktor parametrarna är samma `path` värden som föregående *function.json* exempel: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Du kan också skapa uttryck för delar av filnamnet, till exempel tillägget. Läs mer om hur du använder uttryck och mönster i Blob-sökvägen den [referens för Storage blob-bindning](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Uttryck för bindning - utlösaren metadata

Ange ytterligare metadatavärden förutom datanyttolasten som tillhandahålls av en utlösare (t.ex innehållet i kömeddelandet som en funktion som utlöses av), många utlösare. Dessa värden kan användas som indataparametrar i C# och F# eller egenskaper på den `context.bindings` objekt i JavaScript. 

Till exempel stöder en utlösare för Azure Queue storage följande egenskaper:

* QueueTrigger - utlösa meddelandeinnehåll om en giltig sträng
* DequeueCount
* expirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Dessa metadatavärdena är tillgängliga i *function.json* filegenskaper. Anta exempelvis att du använder en kö-utlösare och kömeddelandet innehåller namnet på en blob som du vill läsa. I den *function.json* -fil som du kan använda `queueTrigger` metadata-egenskap i blob `path` egenskapen, som visas i följande exempel:

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

Metadata-egenskaperna för varje utlösare beskrivs i referensartikeln i fråga. Ett exempel finns i [kö utlösaren metadata](functions-bindings-storage-queue.md#trigger---message-metadata). Dokumentation är också tillgängligt i den **integrera** fliken i portalen, i den **dokumentation** avsnittet nedan konfigurationsområde bindning.  

### <a name="binding-expressions---json-payloads"></a>Uttryck för bindning - JSON-nyttolaster

När en utlösare nyttolast är JSON, kan du referera till dess egenskaper i konfigurationen för andar bindningar i samma funktion och i funktionskoden.

I följande exempel visas den *function.json* -filen för en webhook-funktion som tar emot ett blobnamn i JSON: `{"BlobName":"HelloWorld.txt"}`. En Blob-indatabindning läser blob och HTTP utdata bindning returnerar blob-innehållet i HTTP-svaret. Observera att Blob-indatabindning hämtar blobnamnet genom att referera direkt till den `BlobName` egenskapen (`"path": "strings/{BlobName}"`)

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

För detta ska fungera C# och F#, behöver du en klass som definierar de fält som ska avserialiseras, som i följande exempel:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

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

Om vissa av egenskaperna i JSON-nyttolast finns objekt med egenskaper kan du referera till dem direkt med hjälp av punktnotation. Anta exempelvis att din JSON som ser ut så här:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Du kan gå direkt till `FileName` som `BlobName.FileName`. Med den här JSON-format du här är vad den `path` -egenskapen i föregående exempel skulle se ut:

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

### <a name="binding-expressions---create-guids"></a>Uttryck för bindning - skapa GUID

Den `{rand-guid}` bindning uttrycket skapar ett GUID. Följande blob sökväg i en `function.json` skapar en blob med ett namn som liknar *50710cb5-84b9 - 4d 87 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Uttryck för bindning – aktuell tid

Uttryck för bindning `DateTime` motsvarar `DateTime.UtcNow`. Följande blob sökväg i en `function.json` skapar en blob med ett namn som liknar *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>Bindningen vid körning

I C# och andra .NET-språk, kan du använda ett viktigt bindning mönster, till skillnad från de deklarativa bindningarna i *function.json* och attribut. Tvingande bindning är användbar när bindande parametrar behöver beräknas vid körning i stället för design tidpunkt. Mer information finns i den [C#-utvecklarreferens](functions-dotnet-class-library.md#binding-at-runtime) eller [utvecklarreferens för C#-skript](functions-reference-csharp.md#binding-at-runtime).

## <a name="functionjson-file-schema"></a>Function.JSON filschemat

Den *function.json* filschemat finns på [ http://json.schemastore.org/function ](http://json.schemastore.org/function).

## <a name="testing-bindings"></a>Testa bindningar

När du utvecklar funktioner lokalt, kan du testa dina bindningar som använder Visual Studio 2017 eller Visual Studio Code. Mer information finns i [strategier för att testa din kod i Azure Functions](functions-test-a-function.md). Du kan även anropa icke-HTTP-bindningar med hjälp av REST API: er. Mer information finns i [manuellt köra en icke HTTP-utlöst funktion](functions-manually-run-non-http.md).

## <a name="handling-binding-errors"></a>Hantering av Bindningsfel

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Länkar till alla relevanta fel ämnen för de olika tjänsterna som stöds av Functions finns i den [bindning felkoder](functions-bindings-error-pages.md#binding-error-codes) delen av den [felhantering för Azure Functions](functions-bindings-error-pages.md) översiktsavsnittet.  

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
