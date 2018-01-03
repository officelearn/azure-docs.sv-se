---
title: "Arbeta med utlösare och bindningar i Azure Functions"
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
ms.openlocfilehash: ab5550ee0c057c9abc4b706929d780a495aaff65
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2017
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions-utlösare och bindningar begrepp
Azure Functions kan du skriva kod som svar på händelser i Azure och andra tjänster via *utlösare* och *bindningar*. Den här artikeln innehåller en översikt av utlösare och bindningar för alla programmeringsspråk som stöds. Här beskrivs funktioner som är gemensamma för alla bindningar.

## <a name="overview"></a>Översikt

Utlösare och bindningar är en deklarativ metod för att definiera hur en funktion anropas och hur det fungerar med data. En *utlösaren* definierar hur en funktion har anropats. En funktion måste ha exakt en utlösare. Utlösare har associerade data, vilket är vanligtvis nyttolasten som utlöste funktionen.

Indata och utdata *bindningar* tillhandahåller en deklarativ metod för att ansluta till data från i din kod. Precis som utlösare kan du ange anslutningssträngar och andra egenskaper i konfigurationen av funktionen. Bindningar är valfria och en funktion kan ha flera indata och utdata bindningar. 

Med hjälp av utlösare och bindningar, du kan skriva kod som är mer generisk och har inte hårdkoda information om tjänsterna med den samverkar. Data som kommer från tjänster som bara blir indatavärden för din funktionskoden. Använd returvärdet för metoden för att spara data till en annan tjänst (till exempel skapa en ny rad i Azure Table Storage). Eller Använd en hjälpobjektet om du behöver utdata flera värden. Utlösare och bindningar har en **namn** -egenskap som är en identifierare som du använder i din kod till bindningen.

Du kan konfigurera utlösare och bindningar i den **integrera** i Azure Functions-portalen. Under försättsbladen, Användargränssnittet ändrar en fil med namnet *function.json* fil i katalogen funktion. Du kan redigera den här filen genom att ändra till den **redigeraren**.

## <a name="supported-bindings"></a>Stöds bindningar

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Information om vilka bindningar finns i förhandsgranskningen eller godkänns för produktion finns [språk som stöds](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Exempel: kön utlösare och tabellen utdatabindning

Anta att du vill skriva en ny rad till Azure Table Storage när ett nytt meddelande visas i Azure Queue Storage. Det här scenariot kan implementeras med hjälp av en Azure-kö utlösare och Azure-tabellagring utdatabindning. 

En Azure Queue Storage-utlösare kräver följande information i den **integrera** fliken:

* Namnet på appinställningen som innehåller anslutningssträngen för Azure Storage-konto för Azure Queue Storage
* Könamnet
* Identifieraren i koden för att läsa innehållet i meddelandet kön som `order`.

Använd en output-bindning för att skriva till Azure Table Storage med följande information:

* Namnet på appinställningen som innehåller anslutningssträngen för Azure Storage-konto för Azure-tabellagring
* Tabellens namn
* Identifieraren i koden för att skapa utdata objekt eller returvärdet från funktionen.

Använd anslutningssträngar för bindningar värden som lagras i appinställningar för att tillämpa bäst rutin som *function.json* inte innehåller tjänsten hemligheter och istället bara innehåller namnen på app-inställningar.

Använd sedan de identifierare som du angav för att integrera med Azure Storage i koden.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
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

Här är den *function.json* som motsvarar föregående kod. Observera att samma konfiguration kan användas, oavsett vilket språk i funktionen implementering.

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
Visa och redigera innehållet i *function.json* i Azure-portalen klickar du på den **redigeraren** alternativet på den **integrera** för din funktion.

Mer kodexempel och information om att integrera med Azure Storage finns [Azure Functions-utlösare och bindningar för Azure Storage](functions-bindings-storage.md).

## <a name="binding-direction"></a>Bindningen riktning

Alla utlösare och bindningar har en `direction` egenskap i den *function.json* fil:

- För utlösare är riktningen alltid`in`
- Inkommande och utgående bindningar använda `in` och`out`
- Vissa bindningar stöd för en särskild riktning `inout`. Om du använder `inout`, endast den **redigeraren** är tillgängliga i den **integrera** fliken.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Använda Returtypen för funktionen för att returnera ett enda utflöde

Föregående exempel visar hur du använder funktionen returvärdet ge utdata till en bindning som uppnås med hjälp av parametern särskilda namnet `$return`. (Detta stöds endast på språk som har ett returvärde, till exempel C#, JavaScript och F #.) Om en funktion har flera bindningar för utdata använder `$return` för endast en av bindningarna som utdata. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Exemplen nedan visar tillbaka hur typer som används med utdata bindningar i C#, JavaScript och F #.

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

Använda typer i .NET, definiera datatypen för indata. Till exempel använda `string` att binda till texten i en kö-utlösare, en bytematris läsa som binary och en anpassad typ att deserialisera ett POCO-objekt.

Språk som skrivs dynamiskt, till exempel JavaScript, använda den `dataType` egenskapen i Bindningsdefinitionen. Till exempel använda typen om du vill läsa innehållet i en HTTP-begäran i binärformat `binary`:

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

## <a name="trigger-metadata-properties"></a>Utlösaren metadataegenskaper

Ange ytterligare metadatavärden förutom datanyttolasten som tillhandahålls av en utlösare (till exempel kön meddelandet som utlöste en funktion) många utlösare. Dessa värden kan användas som indataparametrar i C# och F # eller egenskaper på den `context.bindings` objekt i JavaScript. 

Till exempel stöder en kö för Azure Storage-utlösare följande egenskaper:

* QueueTrigger - utlösa meddelandeinnehåll om en giltig sträng
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Information om metadataegenskaper för varje utlösare beskrivs i motsvarande referensavsnittet. Dokumentation är också tillgänglig i den **integrera** för portalen, i den **dokumentationen** avsnittet nedan konfigurationsområde bindning.  

Eftersom blob-utlösare har vissa fördröjningar kan du använda en utlösare för kön för att köra funktionen (se [Blob Storage utlösaren](functions-bindings-storage-blob.md#trigger)). Kön meddelandet innehåller blobfilnamn som utlöser på. Med hjälp av den `queueTrigger` metadataegenskapen, du kan ange det här beteendet i konfigurationen, i stället för din kod.

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

Metadataegenskaper från en utlösare kan också användas i en *bindande uttryck* för en annan bindning som beskrivs i följande avsnitt.

## <a name="binding-expressions-and-patterns"></a>Bindande uttryck och mönster

En av de viktigaste funktionerna i utlösare och bindningar är *bindningsuttryck*. Du kan definiera mönster för uttryck som kan användas i andra bindningar eller din kod i din bindning. Utlösaren metadata kan också användas i bindande uttryck, som visas i exemplet i föregående avsnitt.

Anta att du vill ändra storlek på bilder i viss blob storage-behållare, liknar den **storleksändring av** mallen i den **nya funktionen** sidan. Gå till **nya funktionen** -> språk **C#** -> scenariot **exempel** -> **ImageResizer CSharp**. 

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

Observera att den `filename` parameter används i både blob utlösardefinition samt blob-utdatabindning. Den här parametern kan också användas i funktionskod.

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


### <a name="random-guids"></a>Slumpmässig GUID
Azure Functions erbjuder en bekvämlighet syntax för att skapa GUID i dina bindningar via den `{rand-guid}` bindande uttryck. I följande exempel använder detta för att generera ett unikt blob-namn: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Aktuell tid

Du kan använda Bindningsuttrycket `DateTime`, vilket motsvarar `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>Binda till anpassade inkommande egenskaper i ett uttryck för bindning

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

I C# och andra .NET-språk, kan du använda en tvingande bindning mönster, till skillnad från deklarativ bindningar i *function.json*. Tvingande bindning är användbar när bindande parametrar måste beräknas vid körning i stället för design tidpunkt. Läs mer i [bindning under körning via tvingande bindningar](functions-reference-csharp.md#imperative-bindings) i C#-utvecklare.

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
- [Azure Cosmos DB](functions-bindings-documentdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Extern fil](functions-bindings-external-file.md)
