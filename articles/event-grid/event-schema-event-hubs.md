---
title: Azure Event Hubs som Event Grid källa
description: Beskriver de egenskaper som anges för Event Hub-händelser med Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: fd65c20f07a091fa1fc8a6cbf003986e1096ebe3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393343"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Azure Event Hubs som en Event Grid källa

Den här artikeln innehåller egenskaper och schema för Event Hub-händelser.En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md).

## <a name="event-grid-event-schema"></a>Event Grid-händelseschema

### <a name="available-event-types"></a>Tillgängliga händelse typer

Event Hubs skapar händelse typen **Microsoft. EventHub. CaptureFileCreated** när en infångstfil skapas.

### <a name="example-event"></a>Exempel händelse

Den här exempel händelsen visar schemat för en Event Hub-händelse som aktive ras när insamlings funktionen lagrar en fil: 

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

### <a name="event-properties"></a>Händelse egenskaper

En händelse har följande data på översta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| motiv | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| id | sträng | Unikt ID för händelsen. |
| data | objekt | Händelse data för Event Hub. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| fileUrl | sträng | Sökvägen till insamlings filen. |
| Typen | sträng | Filtypen för insamlings filen. |
| Partition | sträng | Shard-ID. |
| sizeInBytes | heltal | Fil storleken. |
| eventCount | heltal | Antalet händelser i filen. |
| firstSequenceNumber | heltal | Det minsta ordnings numret från kön. |
| lastSequenceNumber | heltal | Det sista sekvensnumret från kön. |
| firstEnqueueTime | sträng | Första gången från kön. |
| lastEnqueueTime | sträng | Sista gången från kön. |

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudie: strömma Big data till ett informations lager](event-grid-event-hubs-integration.md) | När Event Hubs skapar en Infångnings fil, Event Grid skickar en händelse till en Function-app. Appen hämtar insamlings filen och migrerar data till ett data lager. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
* Information om hur du hanterar Event Hub-händelser finns i [strömma Big data till ett data lager](event-grid-event-hubs-integration.md).