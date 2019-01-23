---
title: Ta emot händelser från Azure Event Grid till en HTTP-slutpunkt
description: Beskriver hur du verifierar en HTTP-slutpunkt, och sedan ta emot och deserialisera händelser från Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: bb82ef542db09b3b7f864c4901107e1c5c0827f6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464794"
---
# <a name="receive-events-to-an-http-endpoint"></a>Ta emot händelser till en HTTP-slutpunkt

Den här artikeln beskrivs hur du [validera en HTTP-slutpunkt](security-authentication.md#webhook-event-delivery) att ta emot händelser från en händelseprenumeration och ta emot och deserialisera händelserna. Den här artikeln använder en Azure-funktion i demonstrationssyfte, men samma koncept gäller oavsett var programmet finns.

> [!NOTE]
> Det är **starkt** rekommenderar att du använder en [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md) när du utlöser en Azure-funktion med Event Grid. Användning av en allmän webhooksutlösare här är i demonstrativt syfte.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en funktionsapp med en HTTP-utlöst funktion.

## <a name="add-dependencies"></a>Lägga till beroenden

Om du håller på att utveckla i .NET, [lägger till ett beroende](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) till din funktion för den `Microsoft.Azure.EventGrid` [Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Exemplen i den här artikeln kräver version 1.4.0 eller senare.

SDK: er för andra språk är tillgängliga via den [publicera SDK: er](./sdk-overview.md#data-plane-sdks) referens. Dessa paket har modeller för interna händelsetyper som `EventGridEvent`, `StorageBlobCreatedEventData`, och `EventHubCaptureFileCreatedEventData`.

Klicka på länken ”Visa filer” i din Azure-funktion (de flesta till höger i Azure functions-portalen) och skapa en fil med namnet project.json. Lägg till följande innehåll till den `project.json` och spara den:

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

![Har lagts till NuGet-paketet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Slutpunktsvalideringen

Det första som du vill göra är att hantera `Microsoft.EventGrid.SubscriptionValidationEvent` händelser. Varje gång någon prenumererar på en händelse Event Grid skickar en verifiering-händelse till slutpunkten med en `validationCode` i datanyttolasten för. Slutpunkten som krävs för att echo tillbaka detta i svarstexten till [bevisa slutpunkten är giltig och ägs av dig](security-authentication.md#webhook-event-delivery). Om du använder en [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md) i stället för att en WebHook-funktion som utlöses, slutpunktsvalideringen hanteras åt dig. Om du använder en API-tjänst från tredje part (t.ex. [Zapier](https://zapier.com) eller [IFTTT](https://ifttt.com/)), kan du inte kunna programmässigt echo verifieringskoden. För dessa tjänster kan du manuellt verifiera prenumerationen med hjälp av en URL för verifiering som skickas i händelsen prenumeration verifiering. Kopiera URL: en i den `validationUrl` egenskapen och skicka en GET-begäran antingen via en REST-klient eller webbläsaren.

Manuell verifiering är i förhandsversion. Om du vill använda den måste du installera [Event Grid-tillägget](/cli/azure/azure-cli-extensions-list) för [Azure CLI](/cli/azure/install-azure-cli). Du kan installera det med `az extension add --name eventgrid`. Om du använder REST API, kontrollera att du använder `api-version=2018-05-01-preview`.

I C#, den `DeserializeEventGridEvents()` funktionen deserializes Event Grid-händelser. Den deserializes händelsedata till rätt typ, till exempel StorageBlobCreatedEventData. Använd den `Microsoft.Azure.EventGrid.EventTypes` klassen för att få stöds händelsetyper och namn.

Använd följande kod för att programmässigt echo verifieringskoden. Du hittar exempel på [Grid händelsekonsument exempel](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

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

### <a name="test-validation-response"></a>Testa verifieringssvaret

Testa funktionen för validering svar genom att klistra in exempelhändelse till testfältet för funktionen:

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

![verifieringssvaret](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Hantera Blob storage-händelser

Nu ska vi utöka funktion för att hantera `Microsoft.Storage.BlobCreated`:

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

### <a name="test-blob-created-event-handling"></a>Testa Blob Skapad händelsehantering

Prova de nya funktionerna för funktionen genom att placera en [Blob storage-händelse](./event-schema-blob-storage.md#example-event) i testfältet och körs:

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

Du bör se utdata för blob-URL: en i funktionsloggen för:

![Loggen](./media/receive-events/blob-event-response.png)

Du kan också testa genom att skapa ett Blob storage-konto eller General Purpose V2 (GPv2) Storage-konto, [att lägga till och händelseprenumerationen](../storage/blobs/storage-blob-event-quickstart.md), och ställa in slutpunkten funktions-URL:

![Funktionswebbadress](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Hantera anpassade händelser

Slutligen kan du utöka funktionen en gång till så att den kan också hantera anpassade händelser. 

I C# SDK stöder mappar ett namn på evenemang till datatypen händelse. Använd den `AddOrUpdateCustomEventMapping()` funktionen för att mappa den anpassade händelsen.

Lägg till en kontroll för evenemanget `Contoso.Items.ItemReceived`. Sista koden bör se ut:

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

Slutligen testa att din funktion kan nu hantera dina anpassade Händelsetyp:

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

Du kan också testa den här funktionen live av [skicka en anpassad händelse med CURL från portalen](./custom-event-quickstart-portal.md) eller [skicka information till ett anpassat ämne](./post-to-custom-topic.md) med hjälp av någon tjänst eller program som kan skicka till en slutpunkt som [Postman](https://www.getpostman.com/). Skapa ett anpassat ämne och en händelseprenumeration med slutpunkten som funktions-URL.

## <a name="next-steps"></a>Nästa steg

* Utforska den [Azure Event Grid Management och publicera SDK: er](./sdk-overview.md)
* Lär dig hur du [publicera till ett anpassat ämne](./post-to-custom-topic.md)
* Testa någon av fler djupgående självstudier för Event Grid och funktioner som [storleksändring av bilder har överförts till Blob storage](resize-images-on-storage-blob-upload-event.md)
