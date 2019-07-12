---
title: Schema för Azure Event Grid Azure SignalR-händelse
description: Beskriver de egenskaper som har angetts för Azure SignalR händelser med Azure Event Grid
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789077"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Azure Event Grid-Händelseschema för SignalR Service

Den här artikeln innehåller egenskaperna och schemat för SignalR Service-händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](event-schema.md).


## <a name="available-event-types"></a>Tillgängliga händelsetyper

SignalR Service genererar följande händelsetyper:

| eventType | Beskrivning |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | Utlöses när en klientanslutning ansluten. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Utlöses när en klientanslutning kopplas från. |

## <a name="example-event"></a>Exempel-händelse

I följande exempel visar schemat för en klient anslutning anslutna händelse: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat för en anslutning frånkopplade klienthändelse liknar: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap | Type | Beskrivning |
| -------- | ---- | ----------- |
| subject | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet är skrivskyddat. Event Grid ger det här värdet. |
| subject | sträng | Publisher-definierade sökvägen till ämne för händelsen. |
| eventType | sträng | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | object | SignalR Service händelsedata. |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Type | Beskrivning |
| -------- | ---- | ----------- |
| timestamp | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| hubName | sträng | Hubben som klientanslutningen tillhör. |
| connectionId | sträng | Den unika identifieraren för klientanslutningen. |
| userId | sträng | Användaridentifierare som definierats i anspråk. |
| errorMessage | sträng | Felet som orsakar anslutningen kopplas från. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
