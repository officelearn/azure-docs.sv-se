---
title: Azure händelse rutnätet scheman för Media Services-händelser
description: Beskriver de egenskaper som har angetts för Media Services händelser med Azure händelse rutnätet
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure händelse rutnätet scheman för Media Services-händelser

Den här artikeln innehåller scheman och egenskaper för Media Services-händelser.

## <a name="media-services-job-state-change-event"></a>Media Services jobbet statusändringshändelsen

Det här avsnittet innehåller egenskaperna och schemat för Media Services jobbets status ändra händelse.  

### <a name="available-event-types"></a>Tillgängliga händelsetyper

Ett Media Services **jobbet** genererar följande händelse:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Utlöses när ett tillstånd för jobb ändringarna. |

### <a name="example-event"></a>Exempel-händelse

I följande exempel visas schemat för en klar händelsen för jobbstatus: 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Avsnittet | sträng | Händelsen rutnätet innehåller det här värdet. |
| Ämne | sträng | Resursens sökväg under resurs för Media Services-konto. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekälla. Till exempel ”Microsoft.Media.JobStateChange”. |
| EventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Media Services händelsedata. |
| dataVersion | sträng | Schemaversion av dataobjektets primärnycklar. Utgivaren definierar schemaversionen. |
| metadataVersion | sträng | Schemaversion för metadata för händelsen. Händelsen rutnätet definierar schemat för egenskaperna på den översta nivån. Händelsen rutnätet innehåller det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| previousState | sträng | Status för jobbet före händelsen. |
| state | sträng | Ny status för jobbet att meddelas i den här händelsen. Till exempel ”i kö: jobbet väntar på resurser” eller ”schemalagda: jobbet är redo att börja”.|

Där jobbets status kan vara något av värdena: *i kö*, *schemalagda*, *bearbetning*, *avslutad*, *fel*, *Avbrutits*, *avbryter*

## <a name="next-steps"></a>Nästa steg

[Registrera dig för jobbet tillståndsändringshändelser](job-state-events-cli-how-to.md)
