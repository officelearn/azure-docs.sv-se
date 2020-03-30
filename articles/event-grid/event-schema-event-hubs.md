---
title: Händelseschema för Azure Event Grid-händelsehubbar
description: Beskriver de egenskaper som tillhandahålls för händelsehubbar händelser med Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561836"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure Event Grid-händelseschema för händelsehubbar

Den här artikeln innehåller egenskaper och schema för händelsehubbar händelser.En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md).

En lista över exempelskript och självstudier finns i [händelsekällan Event Hubs](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Tillgängliga händelsetyper

Event Hubs avger händelsetypen **Microsoft.EventHub.CaptureFileCreated** när en hämtningsfil skapas.

## <a name="example-event"></a>Exempel händelse

Den här exempelhändelsen visar schemat för en händelsehubbar som utlöses när hämtningsfunktionen lagrar en fil: 

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

## <a name="event-properties"></a>Händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsehubbhändelsedata. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| fileUrl (filUrl) | sträng | Sökvägen till hämtningsfilen. |
| Filtyp | sträng | Filtypen för hämtningsfilen. |
| Partitionid | sträng | Fragment-ID: et. |
| sizeInBytes | heltal | Filstorleken. |
| eventCount | heltal | Antalet händelser i filen. |
| firstSequenceNumber | heltal | Det minsta sekvensnumret från kön. |
| lastSequenceNumber | heltal | Det sista sekvensnumret från kön. |
| firstEnqueueTime | sträng | Första gången från kön. |
| lastEnqueueTime | sträng | Sista gången från kön. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
* Information om hur du hanterar händelser i händelsehubbar finns [i Strömma stordata till ett informationslager](event-grid-event-hubs-integration.md).