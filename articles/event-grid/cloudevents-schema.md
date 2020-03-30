---
title: Använda Azure Event Grid med händelser i CloudEvents-schemat
description: Beskriver hur du använder CloudEvents-schemat för händelser i Azure Event Grid. Tjänsten stöder händelser i JSON-implementeringen av molnhändelser.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 0efccd2851885dad209d5548a76737c25777b891
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372455"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Använda CloudEvents v1.0-schema med Event Grid

Förutom [standardhändelseschemat](event-schema.md)stöder Azure Event Grid inbyggt händelser i [JSON-implementeringen av CloudEvents v1.0-](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) och [HTTP-protokollbindning](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) är en [öppen specifikation](https://github.com/cloudevents/spec/blob/v1.0/spec.md) för att beskriva händelsedata.

CloudEvents förenklar interoperabilitet genom att tillhandahålla ett gemensamt händelseschema för publicering och förbruka molnbaserade händelser. Det här schemat möjliggör enhetliga verktyg, standardsätt för routning & hantering av händelser och universella sätt att deserialisera det yttre händelseschemat. Med ett gemensamt schema kan du lättare integrera arbete på olika plattformar.

CloudEvents byggs av flera [medarbetare](https://github.com/cloudevents/spec/blob/master/community/contributors.md), inklusive Microsoft, via [Cloud Native Computing Foundation](https://www.cncf.io/). Det är för närvarande tillgänglig som version 1.0.

I den här artikeln beskrivs hur du använder CloudEvents-schemat med Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Installera förhandsversionsfunktionen

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent-schema

Här är ett exempel på en Azure Blob Storage-händelse i CloudEvents-format:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

En detaljerad beskrivning av tillgängliga fält, deras typer och definitioner i CloudEvents v1.0 [finns här](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Huvudenvärdena för händelser som levereras i CloudEvents-schemat och `content-type`eventrutnätet-schemat är desamma förutom . För CloudEvents-schema är `"content-type":"application/cloudevents+json; charset=utf-8"`det huvudvärdet . För event grid-schema är `"content-type":"application/json; charset=utf-8"`det huvudvärdet .

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurera händelserutnät för CloudEvents

Du kan använda Event Grid för både indata och utdata av händelser i CloudEvents-schemat. Du kan använda CloudEvents för systemhändelser, till exempel Blob Storage-händelser och IoT Hub-händelser och anpassade händelser. Det kan också omvandla dessa händelser på tråden fram och tillbaka.


| Indataschema       | Utdataschema
|--------------------|---------------------
| CloudEvents-format | CloudEvents-format
| Format för händelserutnät  | CloudEvents-format
| Format för händelserutnät  | Format för händelserutnät

För alla händelsescheman kräver Event Grid validering när du publicerar till ett händelserutnätsämne och när du skapar en händelseprenumeration. Mer information finns i [Säkerhet och autentisering av Händelserutnät](security-authentication.md).

### <a name="input-schema"></a>Indataschema

Du anger indataschemat för ett anpassat ämne när du skapar det anpassade avsnittet.

Om du använder Azure CLI använder du:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Utdataschema

Du anger utdataschemat när du skapar händelseprenumerationen.

Om du använder Azure CLI använder du:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Om du använder PowerShell använder du:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i CloudEvents-schemat. Använd en HTTP-utlösare. Exempel på hur du implementerar en HTTP-utlösare som tar emot händelser i CloudEvents-schemat finns [i Använda CloudEvents med Azure Functions](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Endpoint-validering med CloudEvents v1.0

Om du redan är bekant med Event Grid kan du vara medveten om Event Grids handslag för slutpunktsverifiering för att förhindra missbruk. CloudEvents v1.0 implementerar sin egen [semantik för missbruksskydd](security-authentication.md#webhook-event-delivery) med hjälp av HTTP OPTIONS-metoden. Du kan läsa mer om det [här](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). När du använder CloudEvents-schemat för utdata använder Event Grid med CloudEvents v1.0-missbruksskydd i stället för händelserutnätsverifieringshändelsemekanismen.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Använd med Azure-funktioner

[Azure Functions Event Grid-bindningen](../azure-functions/functions-bindings-event-grid.md) stöder inte CloudEvents internt, så HTTP-utlösta funktioner används för att läsa CloudEvents-meddelanden. När du använder en HTTP-utlösare för att läsa CloudEvents måste du skriva kod för vad utlösaren för händelserutnät gör automatiskt:

* Skickar ett valideringssvar till en [prenumerationsverifieringsbegäran](../event-grid/security-authentication.md#webhook-event-delivery).
* Anropar funktionen en gång per element i händelsematrisen som finns i begärandetexten.

Information om URL:en som ska användas för att anropa funktionen lokalt eller när den körs i Azure finns i referensdokumentationen för HTTP-utlösare som ska användas för att anropa funktionen lokalt eller när den körs i Azure finns i [referensdokumentationen för HTTP-utlösarebindning](../azure-functions/functions-bindings-http-webhook.md)

Följande exempel C#-kod för en HTTP-utlösare simulerar händelserutlösningsutlösarens beteende.  Använd det här exemplet för händelser som levereras i CloudEvents-schemat.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == "OPTIONS")
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Följande exempel på JavaScript-kod för en HTTP-utlösare simulerar händelserutlösningsutlösarens beteende. Använd det här exemplet för händelser som levereras i CloudEvents-schemat.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS) {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200, body: { "ValidationResponse": code } };
        context.res.headers.append('Webhook-Allowed-Origin', 'eventgrid.azure.net');
    }
    else
    {
        var message = req.body;
        
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelseleveranser finns i [Övervaka leverans av händelserutnätsmeddelanden](monitor-event-delivery.md).
* Vi uppmuntrar dig att testa, kommentera och [bidra](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) till CloudEvents.
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
