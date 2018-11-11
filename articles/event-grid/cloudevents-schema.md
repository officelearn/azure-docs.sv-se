---
title: Använd Azure Event Grid med händelser i CloudEvents-schema
description: Beskriver hur du ställer in CloudEvents-schema för händelser i Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 3865a94192a65a2cb8a761cc1da30317f605548b
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287208"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Använd CloudEvents-schema med Event Grid

Utöver dess [standard Händelseschema](event-schema.md), Azure Event Grid har inbyggt stöd för händelser i den [CloudEvents JSON-schema](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) är en [öppna specifikationen](https://github.com/cloudevents/spec/blob/master/spec.md) för att beskriva händelsedata.

CloudEvents förenklar samverkan genom att tillhandahålla ett gemensamt Händelseschema för publicering och använder molnet baserat på händelser. Det här schemat kan enhetliga verktyg, standard sätt för Routning och hantera händelser och universal sätt att deserialisering av yttre händelseschemat. Du kan enkelt integrera arbete på plattformar med ett gemensamt schema.

CloudEvents håller på att bygga av flera [medarbetare](https://github.com/cloudevents/spec/blob/master/community/contributors.md), inklusive Microsoft, via den [Cloud interna Compute Foundation](https://www.cncf.io/). Det finns för närvarande som version 0.1.

Den här artikeln beskriver hur du använder CloudEvents-schema med Event Grid.

## <a name="install-preview-feature"></a>Installera funktionen för förhandsgranskning

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent schema

Här är ett exempel på en Azure Blob Storage-händelse i CloudEvents-format:

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1 har följande egenskaper som är tillgängliga:

| CloudEvents        | Typ     | Värdet för exempel-JSON             | Beskrivning                                                        | Event Grid-mappning
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| Händelsetyp          | Sträng   | ”com.example.someevent”          | Typ av händelse som inträffade                                   | Händelsetyp
| eventTypeVersion   | Sträng   | ”1.0”                            | Versionen av händelsetyp (valfritt)                            | dataVersion
| cloudEventsVersion | Sträng   | ”0.1”                            | Versionen av CloudEvents-specifikationen händelsen använder        | *skickas via*
| källa             | URI      | ”/ mycontext”                     | Beskriver producenten händelse                                       | avsnittet #subject
| händelse-ID            | Sträng   | ”1234-1234-1234”                 | ID för händelsen                                                    | id
| eventTime          | Tidsstämpel| ”2018-04-05T17:31:00Z”           | Tidsstämpel för när händelsen som inträffade (valfritt)                    | eventTime
| schemaURL          | URI      | "https://myschema.com"           | En länk till det schema som dataattributet följer (valfritt) | *används inte*
| contentType        | Sträng   | ”application/json”               | Beskriv data Kodningsformatet (valfritt)                       | *används inte*
| Tillägg         | Karta      | {”Extra”: ”vA”, ”extB”, ”vB”}  | Alla ytterligare metadata (valfritt)                                 | *används inte*
| data               | Objekt   | {”objA”: ”vA”, ”objB”, ”vB”}  | Händelsenyttolast (valfritt)                                       | data

Mer information finns i den [CloudEvents-specifikationen](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

Värdena för händelser som levereras i CloudEvents-schema och Event Grid-schemat är samma undantag för `content-type`. Det huvud-värdet är för CloudEvents-schema `"content-type":"application/cloudevents+json; charset=utf-8"`. Det huvud-värdet är för Event Grid-schema, `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurera Event Grid för CloudEvents

Du kan använda Event Grid för både indata och utdata av händelser i CloudEvents-schema. Du kan använda CloudEvents för systemhändelser som Blob Storage-händelser och IoT Hub-händelser och anpassade händelser. Det kan också omvandla dessa händelser på kabeln fram och tillbaka.


| Inmatningsschemat       | Utdata-schemat
|--------------------|---------------------
| CloudEvents-format | CloudEvents-format
| Event Grid-format  | CloudEvents-format
| CloudEvents-format | Event Grid-format
| Event Grid-format  | Event Grid-format

För alla Händelsescheman kräver Event Grid-verifiering vid publicering till en event grid-ämne och när du skapar en händelseprenumeration. Mer information finns i [Event Grid säkerhet och autentisering](security-authentication.md).

### <a name="input-schema"></a>Inmatningsschemat

Du kan ange inmatningsschemat för ett anpassat ämne när du skapar det anpassade ämnet.

Om du använder Azure CLI använder du:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
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
  -InputSchema CloudEventV01Schema
```

Den aktuella versionen av CloudEvents stöder inte batchbearbetning av händelser. Publicera varje händelse individuellt för att publicera händelser med CloudEvent schemat till ett ämne.

### <a name="output-schema"></a>Utdata-schemat

Du kan ange utdata-schemat för när du skapar händelseprenumerationen.

Om du använder Azure CLI använder du:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Om du använder PowerShell använder du:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

Den aktuella versionen av CloudEvents stöder inte batchbearbetning av händelser. En händelseprenumeration som är konfigurerad för CloudEvent schema får varje händelse individuellt. För närvarande kan använda du inte en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i CloudEvents-schema. Använd en HTTP-utlösare. Exempel för att implementera en HTTP-utlösare som tar emot händelser i CloudEvents-schema finns [använder en HTTP-utlösare som en Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelse leveranser finns i [övervaka Event Grid meddelandeleverans](monitor-event-delivery.md).
* Vi rekommenderar att du ska testa, kommentar, och [bidra](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) till CloudEvents.
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
