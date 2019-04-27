---
title: Azure Event Grid blob storage Händelseschema
description: Beskriver de egenskaper som har angetts för blob storage-händelser med Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 401eb660d7e5ddc68bc7422ef9f2e600295d2aea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614893"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid-Händelseschema för Blob storage

Den här artikeln innehåller egenskaperna och schemat för blob storage-händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](event-schema.md).

En lista över exempel på skript och självstudier finns i [Storage händelsekälla](event-sources.md#storage).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

BLOB-lagring genererar följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Utlöses när en blob skapas. |
| Microsoft.Storage.BlobDeleted | Utlöses när en blob tas bort. |

## <a name="example-event"></a>Exempel-händelse

I följande exempel visar schemat för en blob som skapats av händelse: 

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

Schemat för en blob tas bort händelsen liknar: 

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
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | string | Fullständig resurssökväg till händelsekällan. Det här fältet är skrivskyddat. Event Grid ger det här värdet. |
| ämne | string | Publisher-definierade sökvägen till ämne för händelsen. |
| Händelsetyp | string | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | string | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | string | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för BLOB storage. |
| dataVersion | string | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | string | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| api | string | Åtgärden som utlöste händelsen. |
| clientRequestId | string | Ett klientgenererade, täckande värde med en teckengräns på 1 KB. När du har aktiverat lagringsanalys, loggning, registreras den i analytics-loggarna. |
| requestId | string | Den unika identifieraren för begäran. Använd det för felsökning av begäran. |
| eTag | string | Det värde som du kan använda för att utföra åtgärder villkorligt. |
| contentType | string | Innehållstyp som angetts för blobben. |
| contentLength | heltal | Storleken på blobben i byte. |
| blobType | string | Typ av blob. Giltiga värden är ”BlockBlob” eller ”PageBlob”. |
| url | string | Sökvägen till blobben. |
| sekvenseraren | string | Ett användarstyrd värde som du kan använda för att spåra begäranden. |
| storageDiagnostics | objekt | Information om storage-diagnostik. |
 
## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
* En introduktion till arbeta med blob storage-händelser finns i [dirigera Blob storage-händelser – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
