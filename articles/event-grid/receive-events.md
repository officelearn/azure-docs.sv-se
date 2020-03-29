---
title: Ta emot händelser från Azure Event Grid till en HTTP-slutpunkt
description: Beskriver hur du validerar en HTTP-slutpunkt och sedan får och avserialiserar händelser från Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: cb38fd17c0c1bfbe3e5957d8f432f0a43b285c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60803792"
---
# <a name="receive-events-to-an-http-endpoint"></a>Ta emot händelser till en HTTP-slutpunkt

I den här artikeln beskrivs hur du [validerar en HTTP-slutpunkt](security-authentication.md#webhook-event-delivery) för att ta emot händelser från en händelseprenumeration och sedan ta emot och avserialisera händelser. Den här artikeln använder en Azure-funktion för demonstrationsändamål, men samma begrepp gäller oavsett var programmet finns.

> [!NOTE]
> Vi rekommenderar **starkt** att du använder en [utlösare av händelserutnät](../azure-functions/functions-bindings-event-grid.md) när du utlöser en Azure-funktion med händelserutnät. Användningen av en generisk WebHook utlösare här är demonstrativ.

## <a name="prerequisites"></a>Krav

Du behöver en funktionsapp med en HTTP-utlöst funktion.

## <a name="add-dependencies"></a>Lägga till beroenden

Om du utvecklar i .NET [lägger du till](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) `Microsoft.Azure.EventGrid` ett beroende i din funktion för [Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Exemplen i den här artikeln kräver version 1.4.0 eller senare.

SDK:er för andra språk är tillgängliga via referensen [Publicera SDK:er.](./sdk-overview.md#data-plane-sdks) Dessa paket har modeller för inbyggda `EventGridEvent` `StorageBlobCreatedEventData`händelsetyper `EventHubCaptureFileCreatedEventData`som , och .

Klicka på länken "Visa filer" i din Azure-funktion (till höger i fönstret Azure-funktioner) och skapa en fil som heter project.json. Lägg till följande `project.json` innehåll i filen och spara det:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![Tillagt NuGet-paket](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Validering av slutpunkt

Det första du vill göra `Microsoft.EventGrid.SubscriptionValidationEvent` är att hantera händelser. Varje gång någon prenumererar på en händelse skickar Event Grid `validationCode` en valideringshändelse till slutpunkten med en i datanyttolasten. Slutpunkten krävs för att upprepa detta tillbaka i svarsorganet för att bevisa att [slutpunkten är giltig och ägs av dig](security-authentication.md#webhook-event-delivery). Om du använder en utlösare för [händelserutnät](../azure-functions/functions-bindings-event-grid.md) i stället för en WebHook-utlöst funktion hanteras slutpunktsverifiering åt dig. Om du använder en API-tjänst från tredje part (som [Zapier](https://zapier.com) eller [IFTTT)](https://ifttt.com/)kanske du inte kan upprepa valideringskoden programmässigt. För dessa tjänster kan du manuellt validera prenumerationen med hjälp av en validerings-URL som skickas i prenumerationsverifieringshändelsen. Kopiera webbadressen `validationUrl` i egenskapen och skicka en GET-begäran antingen via en REST-klient eller din webbläsare.

I C#, `DeserializeEventGridEvents()` deserialiserar funktionen händelserna Event Grid. Händelsedata deserialiserar händelsedata till lämplig typ, till exempel StorageBlobCreatedEventData. Använd `Microsoft.Azure.EventGrid.EventTypes` klassen för att hämta händelsetyper och namn som stöds.

Om du vill upprepa valideringskoden programmässigt använder du följande kod. Du hittar relaterade exempel i [exemplet Event Grid Consumer](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

```csharp
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
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

### <a name="test-validation-response"></a>Svar på testvalidering

Testa valideringssvarsfunktionen genom att klistra in exempelhändelsen i testfältet för funktionen:

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

När du klickar på Kör ska utdata `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` vara 200 OK och i brödtexten:

![valideringssvar](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Hantera blob-lagringshändelser

Nu ska vi utöka funktionen `Microsoft.Storage.BlobCreated`för att hantera:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
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

### <a name="test-blob-created-event-handling"></a>Test Blob Skapad händelsehantering

Testa funktionens nya funktioner genom att placera en [Blob-lagringshändelse](./event-schema-blob-storage.md#example-event) i testfältet och köra:

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

Du bör se blob URL-utdata i funktionsloggen:

![Utdatalogg](./media/receive-events/blob-event-response.png)

Du kan också testa genom att skapa ett Blob storage-konto eller Ett GPv2-konto (General Purpose V2), [lägga till och händelseprenumeration](../storage/blobs/storage-blob-event-quickstart.md)och ange slutpunkten i funktions-URL:en:

![Funktionswebbadress](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Hantera anpassade händelser

Slutligen kan utöka funktionen en gång till så att den också kan hantera anpassade händelser. 

I C#stöder SDK att mappa ett händelsetypsnamn till händelsedatatypen. Använd `AddOrUpdateCustomEventMapping()` funktionen för att mappa den anpassade händelsen.

Lägg till en `Contoso.Items.ItemReceived`check för ditt evenemang . Din slutliga kod ska se ut så här:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

class ContosoItemReceivedEventData
{
    [JsonProperty(PropertyName = "itemSku")]
    public string ItemSku { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
    eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
        else if (eventGridEvent.Data is ContosoItemReceivedEventData)
        {
            var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
            log.Info($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

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
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Testa anpassad händelsehantering

Testa slutligen att din funktion nu kan hantera din anpassade händelsetyp:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Du kan också testa den här funktionen live genom [att skicka en anpassad händelse med CURL från portalen](./custom-event-quickstart-portal.md) eller genom att publicera ett anpassat [ämne](./post-to-custom-topic.md) med någon tjänst eller ett program som kan publicera till en slutpunkt som [Postman](https://www.getpostman.com/). Skapa ett anpassat ämne och en händelseprenumeration med slutpunktsuppsättningen som funktions-URL.

## <a name="next-steps"></a>Nästa steg

* Utforska [Azure Event Grid Management och publicera SDK:er](./sdk-overview.md)
* Läs om hur du [gör inlägg i ett anpassat ämne](./post-to-custom-topic.md)
* Prova en av de djupgående händelserutnät och funktioner tutorials som ändra storlek på bilder som [laddats upp till Blob-lagring](resize-images-on-storage-blob-upload-event.md)
