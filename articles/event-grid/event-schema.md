---
title: Evenemangsschema för Azure Event Grid
description: Beskriver de egenskaper och schema som finns för alla händelser.Händelser består av en uppsättning med fem obligatoriska strängegenskaper och ett obligatoriskt dataobjekt.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 35cea2e6df311d2f4071686c21c8e4c36477abc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244841"
---
# <a name="azure-event-grid-event-schema"></a>Evenemangsschema för Azure Event Grid

I den här artikeln beskrivs de egenskaper och schema som finns för alla händelser.Händelser består av en uppsättning med fem obligatoriska strängegenskaper och ett obligatoriskt dataobjekt. Egenskaperna är gemensamma för alla händelser från alla utgivare. Dataobjektet har egenskaper som är specifika för varje utgivare. För systemavsnitt är dessa egenskaper specifika för resursleverantören, till exempel Azure Storage eller Azure Event Hubs.

Händelsekällor skickar händelser till Azure Event Grid i en matris som kan ha flera händelseobjekt. När du bokför händelser i ett händelserutnätsavsnitt kan matrisen ha en total storlek på upp till 1 MB. Varje händelse i matrisen är begränsad till 64 KB (allmän tillgänglighet) eller 1 MB (förhandsversion). Om en händelse eller matrisen är större än storleksgränserna får du svaret **413 Nyttolast för stor**.

> [!NOTE]
> En händelse av storlek upp till 64 KB omfattas av Service Level Agreement (General Availability) Service Level Agreement (SLA). Stödet för en händelse med storlek upp till 1 MB är för närvarande i förhandsversion. Händelser över 64 KB debiteras i steg om 64 KB. 

Event Grid skickar händelserna till prenumeranter i en matris som har en enda händelse. Detta kan ändras i framtiden.

Du hittar JSON-schemat för Event Grid-händelsen och varje Azure-utgivares datanyttolast i [eventschemaarkivet](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Händelseschema

I följande exempel visas de egenskaper som används av alla händelseutgivare:

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

Schemat som publiceras för en Azure Blob-lagringshändelse är till exempel:

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

## <a name="event-properties"></a>Händelseegenskaper

Alla händelser har samma följande data på den högsta nivån:

| Egenskap | Typ | Krävs | Beskrivning |
| -------- | ---- | -------- | ----------- |
| ämne | sträng | Nej, men om det ingår, måste matcha avsnittet Event Grid Azure Resource Manager ID exakt. Om det inte ingår stämplar Event Grid händelsen. | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Ja | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | Ja | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Ja | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Ja | Unik identifierare för händelsen. |
| data | objekt | Inga | Händelsedata som är specifika för resursleverantören. |
| Dataversion | sträng | Nej, men kommer att stämplas med ett tomt värde. | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Krävs inte, men om det ingår, `metadataVersion` måste matcha `1`event grid-schemat exakt (för närvarande endast ). Om det inte ingår stämplar Event Grid händelsen. | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Mer information om egenskaperna i dataobjektet finns i händelsekällan:

* [Azure-prenumerationer (hanteringsåtgärder)](event-schema-subscriptions.md)
* [Behållare register](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Händelsehubbar](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Resursgrupper (hanteringsåtgärder)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

För anpassade ämnen bestämmer händelseutgivaren dataobjektet. Data på den översta nivån ska ha samma fält som standardresursdefinierade händelser.

När du publicerar händelser i anpassade ämnen skapar du ämnen för dina evenemang som gör det enkelt för prenumeranter att veta om de är intresserade av händelsen. Prenumeranter använder ämnet för filter- och rutthändelser. Överväg att ange sökvägen för var händelsen inträffade, så att prenumeranter kan filtrera efter segment av den sökvägen. Sökvägen gör det möjligt för prenumeranter att filtrera händelser snävt eller brett. Om du till exempel anger en `/A/B/C` sökväg med tre segment som `/A` i ämnet kan prenumeranter filtrera efter det första segmentet för att få en bred uppsättning händelser. Dessa abonnenter få evenemang `/A/B/C` `/A/D/E`med ämnen som eller . Andra prenumeranter kan filtrera efter `/A/B` att få en smalare uppsättning händelser.

Ibland behöver ditt ämne mer detaljerat om vad som hände. Utgivaren av **lagringskonton** anger `/blobServices/default/containers/<container-name>/blobs/<file>` till exempel ämnet när en fil läggs till i en behållare. En prenumerant kan `/blobServices/default/containers/testcontainer` filtrera efter sökvägen för att hämta alla händelser för den behållaren men inte andra behållare i lagringskontot. En prenumerant kan också filtrera `.txt` eller dirigera vid suffixet för att bara arbeta med textfiler.

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
