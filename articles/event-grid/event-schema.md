---
title: Schema för Azure Event Grid-händelse
description: Beskriver de egenskaper som har angetts för händelser med Azure Event Grid
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 07/06/2018
ms.author: babanisa
ms.openlocfilehash: 266ddced5f1949fa72508d914f76953101a7aac6
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902239"
---
# <a name="azure-event-grid-event-schema"></a>Schema för Azure Event Grid-händelse

Den här artikeln beskriver egenskaper och scheman som är tillgängliga för alla händelser. Händelser som består av en uppsättning med fem obligatoriska strängegenskaper och ett nödvändiga data-objekt. Egenskaperna är vanliga i alla händelser från valfri utgivare. Dataobjektet har egenskaper som är specifika för varje utgivare. De här egenskaperna är specifika för resursleverantör, till exempel Azure Storage eller Azure Event Hubs för system-ämnen.

Händelsekällor används för att skicka händelser till Azure Event Grid i en matris som kan ha flera händelseobjekt. När skicka händelser till en event grid-ämne kan matrisen ha en total storlek på upp till 1 MB. Varje händelse i matrisen är begränsat till 64 KB. Om en händelse eller matrisen är större än storleksgränserna, får du svaret **413 nyttolasten är för stor**.

Event Grid skickar händelser till prenumeranter i en matris som har en enda händelse. Det här beteendet kan ändras i framtiden.

Du kan hitta JSON-schemat för Event Grid-händelse och varje Azure utgivare datanyttolasten i den [Händelseschema store](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

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

Schemat som publicerats för en Azure Blob storage-händelse är till exempel:

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

Alla händelser har samma följande översta data:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet är inte skrivbar. Event Grid ger det här värdet. |
| ämne | sträng | Publisher-definierade sökvägen till ämne för händelsen. |
| Händelsetyp | sträng | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata är specifika för resursprovidern. |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Mer information om egenskaperna i dataobjektet, se händelsekällan:

* [Azure-prenumerationer (åtgärder)](event-schema-subscriptions.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Resursgrupper (åtgärder)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)

För anpassade ämnen anger händelseutfärdaren dataobjektet. Översta data bör ha samma fält som standard resurs-definierade händelser.

När du publicerar händelser till anpassade ämnen, skapa ämnen för händelser som gör det enkelt för prenumeranter att veta om de är intresserad av händelsen. Prenumeranter använda ämne på filtret och dirigera händelser. Ställ in sökvägen för där händelsen har inträffat, så prenumeranter kan filtrera efter segment i sökvägen. Sökvägen kan prenumeranter att snävare eller brett Filtrera händelser. Exempel: Om du anger en sökväg för tre segment som `/A/B/C` på ämnesraden prenumeranter kan filtrera efter det första segmentet `/A` att hämta en rad olika händelser. Dessa prenumeranter får händelser med ämnen som `/A/B/C` eller `/A/D/E`. Andra prenumeranter kan filtrera efter `/A/B` att hämta en smalare uppsättning händelser.

Ibland behöver dina mer information om vad som hände. Till exempel den **Lagringskonton** publisher innehåller ämnesraden `/blobServices/default/containers/<container-name>/blobs/<file>` när en fil läggs till i en behållare. En prenumerant kan filtrera efter sökvägen `/blobServices/default/containers/testcontainer` att hämta alla händelser för den behållaren men inte andra behållare i lagringskontot. En prenumerant kan också filtrera eller dirigera efter suffixet `.txt` till fungerar bara med textfiler.

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
