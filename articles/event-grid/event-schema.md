---
title: Azure händelse rutnätet Händelseschema
description: Beskriver de egenskaper som har angetts för händelser med Azure händelse rutnätet
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 04/17/2018
ms.author: babanisa
ms.openlocfilehash: 3e0b7fd825b8e985cea2c32301986b3a7f8bb619
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="azure-event-grid-event-schema"></a>Azure händelse rutnätet Händelseschema

Den här artikeln beskriver egenskaper och scheman som är tillgängliga för alla händelser. Händelser som består av en uppsättning med fem krävs strängegenskaper och ett objekt för data som krävs. Egenskaper är gemensamma för alla händelser från alla utgivare. Dataobjekt som innehåller egenskaper som är specifika för varje utgivare. De här egenskaperna är specifika för resursleverantör, till exempel Azure Storage eller Azure Event Hubs för system-avsnitt.

Händelsekällan skicka händelser till Azure händelse rutnät i en matris som kan innehålla flera händelseobjekt. Matrisen kan skicka händelser till en händelse rutnätet ämne ha en total storlek på upp till 1 MB. Varje händelse i matrisen är begränsat till 64 KB. Om en händelse eller matrisen överskrider storleksgränser, visas svaret **413 nyttolasten är för stor**.

Händelsen rutnätet skickar händelser till prenumeranter i en matris som innehåller en enstaka händelse. Detta kan ändras i framtiden.

Du kan hitta JSON-schema för händelsen rutnätet händelsen och varje Azure utgivare datanyttolasten i den [Händelseschema store](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Händelseschema

I följande exempel visas de egenskaper som används av alla utgivare:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Schemat för en Azure Blob storage-händelse är till exempel:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
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
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Egenskaper för händelse

Alla händelser som innehåller samma följande översta data:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Avsnittet | sträng | Fullständigt labbresurs sökvägen till händelsekällan. Det här fältet är skrivskyddat. Händelsen rutnätet innehåller det här värdet. |
| Ämne | sträng | Publisher-definierade sökvägen till ämnet för händelsen. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekälla. |
| EventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata är specifika för resursprovidern. |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversionen. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Händelsen rutnätet definierar schemat för egenskaperna på den översta nivån. Händelsen rutnätet innehåller det här värdet. |

Läs om egenskaperna i dataobjektet i händelsekällan:

* [Azure-prenumerationer (hanteringsåtgärder)](event-schema-subscriptions.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [Service Bus](event-schema-service-bus.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Resursgrupper (hanteringsåtgärder)](event-schema-resource-groups.md)

För anpassade avsnitt avgörs händelse dataobjektet. Informationen på den översta nivån ska innehålla samma fält som standard resurs användardefinierade händelser.

När du publicerar händelser till anpassade avsnitt, skapa ämnen för händelserna som gör det enkelt för prenumeranter att veta om de är intresserad av händelsen. Prenumeranter använda ämnet att filtrera och väg händelser. Överväg att ange sökvägen för vilket händelsen inträffade, så prenumeranter kan filtrera efter segment i sökvägen. Sökvägen kan prenumeranter att snävare eller brett Filtrera händelser. Om du anger en sökväg för tre segment som till exempel `/A/B/C` prenumeranter kan filtrera efter det första segmentet i ämnet, `/A` att hämta en bred uppsättning händelser. Dessa prenumeranter får händelser med ämnen som `/A/B/C` eller `/A/D/E`. Andra prenumeranter kan filtrera efter `/A/B` att hämta en mindre uppsättning händelser.

Ibland måste ämnet mer information om vad som hänt. Till exempel den **Lagringskonton** publisher innehåller ämnet `/blobServices/default/containers/<container-name>/blobs/<file>` när en fil har lagts till i en behållare. En prenumerant kan filtrera efter sökvägen `/blobServices/default/containers/testcontainer` att hämta alla händelser för behållaren men inte andra behållare i storage-konto. En prenumerant kan även filtrera eller flödet av suffixet `.txt` fungerar bara med textfiler.

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)
* Mer information om hur du skapar en prenumeration på Azure händelse rutnätet finns [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).
