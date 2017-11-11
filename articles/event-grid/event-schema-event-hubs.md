---
title: "Azure händelse rutnätet hubbar händelse Händelseschema"
description: "Beskriver de egenskaper som har angetts för event hubs händelser med Azure händelse rutnätet"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: tomfitz
ms.openlocfilehash: 80959ee589a1cfcf317a98c3bafd7f92c796fc2d
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure händelse rutnätet Händelseschema för händelsehubbar

Den här artikeln innehåller egenskaperna och schemat för event hubs händelser. En introduktion till händelse scheman, se [Azure händelse rutnätet Händelseschema](event-schema.md).

### <a name="available-event-types"></a>Tillgängliga händelsetyper

Händelsehubbar genererar den **Microsoft.EventHub.CaptureFileCreated** händelsetyp när en avbildning fil skapas.

## <a name="example-event"></a>Exempel-händelse

Den här händelsen i exemplet visar schemat för en händelse hubbar händelse som aktiveras när en fil lagras i funktionen avbildning: 

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
        }
    }
]
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
| Data | Objektet | Event hub händelsedata. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| fileUrl | Sträng | Sökvägen till filen avbildning. |
| Filtyp | Sträng | Avbilda filen filtyp. |
| partitions-ID | Sträng | Fragment-ID: t. |
| sizeInBytes | heltal | Filstorleken. |
| eventCount | heltal | Antalet händelser i filen. |
| firstSequenceNumber | heltal | Det minsta sekvensnumret från kön. |
| lastSequenceNumber | heltal | Det sista sekvensnumret från kön. |
| firstEnqueueTime | Sträng | Första gången från kön. |
| lastEnqueueTime | Sträng | Senast från kön. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)
* Mer information om hur du skapar en prenumeration på Azure händelse rutnätet finns [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).
* Information om hur du hanterar event hubs händelser finns [strömma stordata i ett data warehouse](event-grid-event-hubs-integration.md).