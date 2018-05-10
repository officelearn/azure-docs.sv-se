---
title: Använda Azure händelse rutnät med händelser i CloudEvents schemat
description: Beskriver hur du anger CloudEvents schemat för händelser i Azure händelse rutnät.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/09/2018
ms.author: babanisa
ms.openlocfilehash: 84581de2819ec062a537d4a302f242085a4b3c2c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Använd CloudEvents schema med händelsen rutnätet

Förutom dess [standardschema händelse](event-schema.md), Azure händelse rutnätet har inbyggt stöd för händelser i den [CloudEvents JSON-schema](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) är en [öppna standard-specifikationen](https://github.com/cloudevents/spec/blob/master/spec.md) för att beskriva händelsedata i ett vanligt sätt.

CloudEvents förenklar samverkan genom att tillhandahålla ett gemensamt Händelseschema för publicering och använder molnet baserat på händelser. Det här schemat tillåter uniform verktygsuppsättning, standard sätt routning & Hantera händelser och universal sätt avserialisering av yttre Händelseschema. Du kan enkelt integrera arbete plattformar med ett gemensamt schema.

CloudEvents används build av flera [medarbetare](https://github.com/cloudevents/spec/blob/master/community/contributors.md), inklusive Microsoft, via den [moln interna Compute Foundation](https://www.cncf.io/). Det är tillgängligt som version 0.1.

Den här artikeln beskriver hur du använder CloudEvents schemat med händelsen rutnätet.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent schema

Här är ett exempel på en Azure Blob Storage-händelse i CloudEvents format:

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

| CloudEvents        | Typ     | Exempel JSON-värde             | Beskrivning                                                        | Händelsen rutnätet mappning
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| Händelsetyp          | Sträng   | ”com.example.someevent”          | Typ av händelse som inträffade                                   | Händelsetyp
| eventTypeVersion   | Sträng   | ”1.0”                            | Versionen av eventType (valfritt)                            | dataVersion
| cloudEventsVersion | Sträng   | ”0,1”                            | Versionen av specifikationen CloudEvents händelsen använder        | *passerat*
| källa             | URI      | ”/ mycontext”                     | Beskriver producenten händelse                                       | avsnittet #subject
| Händelse-ID            | Sträng   | ”1234-1234-1234”                 | ID för händelsen                                                    | id
| EventTime          | Tidsstämpel| ”2018-04-05T17:31:00Z”           | Tidsstämpel när händelsen inträffade (valfritt)                    | EventTime
| schemaURL          | URI      | "https://myschema.com"           | En länk till det schema som dataattributet följer (valfritt) | *används inte*
| ContentType        | Sträng   | ”application/json”               | Beskriv data Kodningsformatet (valfritt)                       | *används inte*
| Tillägg         | Karta      | {”extA”: ”vA”, ”extB”, ”vB”}  | Eventuella ytterligare metadata (valfritt)                                 | *används inte*
| data               | Objekt   | {”objA”: ”vA”, ”objB”, ”vB”}  | Händelsenyttolast (valfritt)                                       | data

Mer information finns i [CloudEvents spec](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurera händelse rutnät för CloudEvents

För närvarande Azure händelse rutnätet har preview stöder för CloudEvents JSON-formatet indata och utdata i **Väst centrala oss**, **centrala USA**, och **Nordeuropa**.

Du kan använda händelsen rutnät för både inkommande och utgående händelser i CloudEvents schema. Du kan använda CloudEvents för systemhändelser som Blob Storage-händelser och händelser för IoT-hubb och anpassade händelser. Det kan också transformera dessa händelser på kabeln fram och tillbaka.


| Ingående schema       | Utdataschemat
|--------------------|---------------------
| CloudEvents format | CloudEvents format
| Händelsen rutnätsformat  | CloudEvents format
| CloudEvents format | Händelsen rutnätsformat
| Händelsen rutnätsformat  | Händelsen rutnätsformat

För alla händelse scheman kräver händelse rutnätet verifiering vid publicering till en händelse rutnätet avsnittet och när du skapar en händelseprenumeration. Mer information finns i [händelse rutnätet säkerhets- och autentiseringstjänster](security-authentication.md).

### <a name="input-schema"></a>Ingående schema

Om du vill ange det inkommande schemat på en anpassad avsnittet CloudEvents, använder du följande parameter i Azure CLI när du skapade ditt ämne `--input-schema cloudeventv01schema`. Anpassade avsnittet förväntar nu inkommande händelser i CloudEvents v0.1 format.

Använd för att skapa en händelse rutnätet avsnittet:

```azurecli
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

Den aktuella versionen av CloudEvents stöder inte massbearbetning av händelser. Publicera varje händelse individuellt för att publicera händelser med CloudEvent schemat till ett ämne.

### <a name="output-schema"></a>Utdataschemat

Om du vill ange utdataschemat på en händelseprenumeration till CloudEvents, använder du följande parameter i Azure CLI när du skapar prenumerationen händelsen `--event-delivery-schema cloudeventv01schema`. Händelser för den här händelseprenumerationen levereras nu CloudEvents v0.1 format.

Använd för att skapa en händelseprenumeration:

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \  
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Den aktuella versionen av CloudEvents stöder inte massbearbetning av händelser. En händelseprenumeration som har konfigurerats för CloudEvent schemat får varje händelse individuellt.

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelse leveranser finns [övervakaren händelse rutnätet meddelandeleverans](monitor-event-delivery.md).
* Vi rekommenderar att du ska testa, kommentar, och [bidra](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) till CloudEvents.
* Mer information om hur du skapar en prenumeration på Azure händelse rutnätet finns [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).
