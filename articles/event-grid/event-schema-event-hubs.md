---
title: Azure Event Hubs som event grid-källa
description: Beskriver de egenskaper som tillhandahålls för händelsehubbar händelser med Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: fd65c20f07a091fa1fc8a6cbf003986e1096ebe3
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393343"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Azure Event Hubs som en event grid-källa

Den här artikeln innehåller egenskaper och schema för händelsehubbar händelser.En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md).

## <a name="event-grid-event-schema"></a>Händelseschema för händelserutnät

### <a name="available-event-types"></a>Tillgängliga händelsetyper

Event Hubs avger händelsetypen **Microsoft.EventHub.CaptureFileCreated** när en hämtningsfil skapas.

### <a name="example-event"></a>Exempel händelse

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

### <a name="event-properties"></a>Händelseegenskaper

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

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudiekurs: strömma stordata till ett informationslager](event-grid-event-hubs-integration.md) | När Event Hubs skapar en capture-fil skickar Event Grid en händelse till en funktionsapp. Appen hämtar capture-filen och migrerar data till ett informationslager. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
* Information om hur du hanterar händelser i händelsehubbar finns [i Strömma stordata till ett informationslager](event-grid-event-hubs-integration.md).