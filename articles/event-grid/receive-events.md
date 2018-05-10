---
title: Ta emot händelser från Azure Event rutnät till en HTTP-slutpunkt
description: Beskriver hur du validera en HTTP-slutpunkt, och sedan ta emot och deserialisera händelser från Azure Event rutnätet
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: article
ms.date: 04/26/2018
ms.author: babanisa
ms.openlocfilehash: db79629c5f806fe50d22200574c29052a485dd06
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="receive-events-to-an-http-endpoint"></a>Ta emot händelser till en HTTP-slutpunkt

Den här artikeln beskriver hur du [validera en HTTP-slutpunkt](security-authentication.md#webhook-event-delivery) att ta emot händelser från en prenumeration för händelsen ta emot och deserialisera händelser. Den här artikeln använder en Azure-funktion i exempelsyfte, men samma koncept gäller oavsett där programmet körs.

> [!NOTE]
> Det är **starkt** bör du använda en [rutnätet händelseutlösare](../azure-functions/functions-bindings-event-grid.md) när utlösa en Azure-funktion med händelsen rutnät. En allmän WebHook-utlösare här används demonstrativt.

## <a name="prerequisites"></a>Förutsättningar

* Du behöver en funktionsapp med en [HTTP aktiveras funktionen](../azure-functions/functions-create-generic-webhook-triggered-function.md)

## <a name="add-dependencies"></a>Lägga till beroenden

Om du utvecklar i .NET, [lägger till ett beroende](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) till funktionen för den `Microsoft.Azure.EventGrid` [Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). SDK: er för andra språk som är tillgängliga via den [publicera SDK](./sdk-overview.md#data-plane-sdks) referens. Dessa paket som innehåller modeller för interna händelsetyper `EventGridEvent`, `StorageBlobCreatedEventData`, och `EventHubCaptureFileCreatedEventData`.

Klicka på länken ”Visa filer” i din Azure-funktion (de flesta höger i Azure functions-portalen) och skapa en fil med namnet project.json. Lägg till följande innehåll till den `project.json` filen och spara den:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "1.1.0-preview"
      }
    }
   }
}
```

![Tillagda NuGet-paketet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Validering av slutpunkten

Det första som du vill göra är att hantera `Microsoft.EventGrid.SubscriptionValidationEvent` händelser. Varje gång någon prenumererar på en händelse händelse rutnätet skickar en verifiering-händelse till slutpunkten med en `validationCode` i datanyttolasten. Slutpunkten som krävs för att echo tillbaka detta i svarstexten till [bevisa slutpunkten är giltig och ägs av du](security-authentication.md#webhook-event-delivery). Om du använder en [rutnätet händelseutlösare](../azure-functions/functions-bindings-event-grid.md) snarare än en WebHook utlöses funktion, hanteras verifieringen av slutpunkten för dig. Om du använder en tredjeparts-API-tjänsten (t.ex. [Zapier](https://zapier.com) eller [IFTTT](https://ifttt.com/)), kan du inte kunna programmässigt echo verifieringskoden. Du kan manuellt verifiera prenumerationen med hjälp av en verifiering URL som skickas i händelsen prenumeration verifiering för dessa tjänster. Kopiera den URL i den `validationUrl` egenskapen och skickar en GET-begäran via ett REST-klient eller webbläsaren.

För att programmatiskt echo verifieringskoden, använder du följande kod:

```csharp
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{

    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Testa verifieringen svar

Testa funktionen för validering svar genom att klistra in händelsen exemplet i testfältet för funktionen:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

När du klickar på Kör utdata ska vara 200 OK och `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` i brödtext:

![verifieringen svar](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Hantera Blob storage-händelser

Nu ska vi utöka funktionen för att hantera `Microsoft.Storage.BlobCreated`:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Testa Blob Skapad händelsehantering

Testa de nya funktionerna för funktionen genom att lägga till en [Blob storage händelse](./event-schema-blob-storage.md#example-event) i fältet test och körs:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Du bör se utdata för blob-URL i loggen för funktionen:

![Logg för utdata](./media/receive-events/blob-event-response.png)

Du kan också testa genom att skapa ett Blob storage-konto eller allmänna syften V2 (GPv2) lagringskonto [prenumeration för att lägga till och händelsen](../storage/blobs/storage-blob-event-quickstart.md), och ange slutpunkten funktions-URL:

![Funktionswebbadress](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Hantera anpassade händelser

Slutligen kan du utöka funktionen igen så att den kan också hantera anpassade händelser. Lägg till en kontroll för din händelse `Contoso.Items.ItemReceived`. Sista koden bör se ut som:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

class ContosoItemReceivedEventData
{
    public string id { get; set; }
    public string message { get; set; }
    public string time { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>();
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};

```

### <a name="test-custom-event-handling"></a>Testa anpassade händelsehantering

Slutligen testa att din EA funktion nu kan hantera dina anpassade Händelsetyp:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Du kan också testa den här funktionen live av [skicka en anpassad händelse med CURL från portalen](./custom-event-quickstart-portal.md) eller [bokföring till ett eget avsnitt](./post-to-custom-topic.md) med hjälp av någon tjänst eller program som kan skicka till en slutpunkt som [Postman](https://www.getpostman.com/). Skapa ett anpassat ämne och en händelseprenumeration med slutpunkten som funktions-URL.

## <a name="next-steps"></a>Nästa steg

* Utforska den [Azure händelse rutnätet Mangement och publicera SDK](./sdk-overview.md)
* Lär dig hur du [efter till ett eget avsnitt](./post-to-custom-topic.md)
* Försök med något av djupgående självstudier för händelsen rutnätet och funktioner som [storleksändring bilder har överförts till Blob storage](resize-images-on-storage-blob-upload-event.md)
