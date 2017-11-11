---
title: "Azure händelse rutnätet blob storage Händelseschema"
description: "Beskriver de egenskaper som har angetts för blob storage-händelser med Azure händelse rutnätet"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: bdc64733b75fd809cf0245986aa96370343c1a34
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure händelse rutnätet Händelseschema för Blob storage

Den här artikeln innehåller egenskaperna och schemat för blob storage-händelser. En introduktion till händelse scheman, se [Azure händelse rutnätet Händelseschema](event-schema.md).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

BLOB storage genererar följande händelse:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Utlöses när en blob skapas. |
| Microsoft.Storage.BlobDeleted | Utlöses när en blob tas bort. |

## <a name="example-event"></a>Exempel-händelse

I följande exempel visas schemat för en blob som skapats i händelsen: 

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
  }
}]
```

Schemat för en blob bort händelse är ungefär: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  }
}]
```
 
## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Avsnittet | Sträng | Fullständigt labbresurs sökvägen till händelsekällan. Det här fältet är skrivskyddat. |
| Ämne | Sträng | Publisher-definierade sökvägen till ämnet för händelsen. |
| Händelsetyp | Sträng | En av de registrerade händelsetyperna för den här händelsekälla. |
| EventTime | Sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | Sträng | Unik identifierare för händelsen. |
| Data | Objektet | BLOB storage händelsedata. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| api | Sträng | Åtgärden som utlöste händelsen. |
| ClientRequestId | Sträng | Klienten genererade, täckande värde med en 1 KB tecken. När du har aktiverat storage analytics loggning registreras den i analytics-loggarna. |
| begärande-ID | Sträng | Den unika identifieraren för begäran. Använda det för felsökning av begäran. |
| ETag | Sträng | Det värde som du kan använda för att utföra åtgärder enligt villkor. |
| ContentType | Sträng | Innehållstyp som angetts för blob. |
| contentLength | heltal | Storleken på blobben i byte. |
| BlobType | Sträng | Typ av blob. |
| URL: en | Sträng | Sökvägen till blob. |
| sequencer | Sträng | Ett användar-kontrollerade värde som du kan använda för att spåra förfrågningar. |
| storageDiagnostics | Objektet | Information om lagring diagnostik. |
 
## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)
* Mer information om hur du skapar en prenumeration på Azure händelse rutnätet finns [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).
* En introduktion till blob storage-händelser finns [väg Blob storage-händelser - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
