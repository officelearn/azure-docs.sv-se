---
title: Använd Azure Event Grid med händelser i CloudEvents-schema
description: Beskriver hur du ställer in CloudEvents-schema för händelser i Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: babanisa
ms.openlocfilehash: 4f1f0e95ae74ef41ed91be55f4c964671e8f723b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044557"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Använd CloudEvents-schema med Event Grid

Utöver dess [standard Händelseschema](event-schema.md), Azure Event Grid har inbyggt stöd för händelser i den [CloudEvents JSON-schema](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) är en [öppna standard-specifikationen](https://github.com/cloudevents/spec/blob/master/spec.md) för att beskriva händelsedata i ett vanligt sätt.

CloudEvents förenklar samverkan genom att tillhandahålla ett gemensamt Händelseschema för publicering och använder molnet baserat på händelser. Det här schemat kan enhetliga verktyg, standard sätt för Routning och hantera händelser och universal sätt att deserialisering av yttre händelseschemat. Du kan enkelt integrera arbete på plattformar med ett gemensamt schema.

CloudEvents håller på att bygga av flera [medarbetare](https://github.com/cloudevents/spec/blob/master/community/contributors.md), inklusive Microsoft, via den [Cloud interna Compute Foundation](https://www.cncf.io/). Det finns för närvarande som version 0.1.

Den här artikeln beskriver hur du använder CloudEvents-schema med Event Grid.

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
| händelse-ID            | Sträng   | ”1234-1234-1234”                 | ID för händelsen                                                    | ID
| eventTime          | Tidsstämpel| ”2018-04-05T17:31:00Z”           | Tidsstämpel för när händelsen som inträffade (valfritt)                    | eventTime
| schemaURL          | URI      | "https://myschema.com"           | En länk till det schema som dataattributet följer (valfritt) | *används inte*
| contentType        | Sträng   | ”application/json”               | Beskriv data Kodningsformatet (valfritt)                       | *används inte*
| Tillägg         | Karta      | {”Extra”: ”vA”, ”extB”, ”vB”}  | Alla ytterligare metadata (valfritt)                                 | *används inte*
| data               | Objekt   | {”objA”: ”vA”, ”objB”, ”vB”}  | Händelsenyttolast (valfritt)                                       | data

Mer information finns i den [CloudEvents-specifikationen](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

Värdena för händelser som levereras i CloudEvents-schema och Event Grid-schemat är likadana, med undantag för `content-type`. Det huvud-värdet är för CloudEvents-schema `"content-type":"application/cloudevents+json; charset=utf-8"`. Det huvud-värdet är för Event Grid-schema, `"content-type":"application/json; charset=utf-8"`.

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

Om du vill ställa in inmatningsschemat på ett anpassat ämne att CloudEvents, använder du följande parameter i Azure CLI när du skapar ditt anpassade ämne `--input-schema cloudeventv01schema`. Det anpassade ämnet förväntar nu inkommande händelser i CloudEvents v0.1 format.

Om du vill skapa en event grid-ämne, använder du:

```azurecli
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

Den aktuella versionen av CloudEvents stöder inte batchbearbetning av händelser. Publicera varje händelse individuellt för att publicera händelser med CloudEvent schemat till ett ämne.

### <a name="output-schema"></a>Utdata-schemat

Om du vill ställa in utdata-schemat på en händelseprenumeration på CloudEvents, använder du följande parameter i Azure CLI när du skapar din händelseprenumeration `--event-delivery-schema cloudeventv01schema`. Händelser för den här händelseprenumerationen levereras nu CloudEvents v0.1 format.

Om du vill skapa en händelseprenumeration, använder du:

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Den aktuella versionen av CloudEvents stöder inte batchbearbetning av händelser. En händelseprenumeration som är konfigurerad för CloudEvent schema får varje händelse individuellt. För närvarande kan använda du inte en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i CloudEvents-schema. Du måste använda en HTTP-utlösare. Exempel för att implementera en HTTP-utlösare som tar emot händelser i CloudEvents-schema finns [använder en HTTP-utlösare som en Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelse leveranser finns i [övervaka Event Grid meddelandeleverans](monitor-event-delivery.md).
* Vi rekommenderar att du ska testa, kommentar, och [bidra](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) till CloudEvents.
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
