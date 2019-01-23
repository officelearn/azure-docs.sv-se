---
title: Azure Event Grid event hubs Händelseschema
description: Beskriver de egenskaper som har angetts för event hubs händelser med Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475415"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure Event Grid-Händelseschema för event hubs

Den här artikeln innehåller egenskaperna och schemat för event hubs-händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](event-schema.md).

En lista över exempel på skript och självstudier finns i [Händelsehubbar händelsekälla](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Tillgängliga händelsetyper

Händelsehubbar genererar den **Microsoft.EventHub.CaptureFileCreated** händelsetyp när en avbildning fil har skapats.

## <a name="example-event"></a>Exempel-händelse

Den här exempelhändelse visar schemat för en aktiveras när en fil lagras i avbildningsfunktionen i event hubs händelse: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet är skrivskyddat. Event Grid ger det här värdet. |
| ämne | sträng | Publisher-definierade sökvägen till ämne för händelsen. |
| Händelsetyp | sträng | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för Event hub. |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| fileUrl | sträng | Sökvägen till filen avbildning. |
| fileType | sträng | Filtypen för filen avbildning. |
| Partitions-ID | sträng | Shard-ID. |
| sizeInBytes | heltal | Filstorlek. |
| eventCount | heltal | Antal händelser i filen. |
| firstSequenceNumber | heltal | Det minsta sekvensnumret från kön. |
| lastSequenceNumber | heltal | Det sista sekvensnumret från kön. |
| firstEnqueueTime | sträng | Första gången från kön. |
| lastEnqueueTime | sträng | Den senaste gången från kön. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
* Information om att hantera event hubs-händelser finns i [Stream stordata till ett datalager](event-grid-event-hubs-integration.md).