---
title: Reagera på händelser för Azure SignalR Service
description: Använd Azure Event Grid för att prenumerera på händelser för Azure SignalR Service.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789194"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagera på händelser för Azure SignalR Service

Azure SignalR Service-evenemang tillåta program att reagera på klientanslutningar ansluten eller frånkopplad med moderna arkitekturer utan server. Detta sker utan behov av komplicerade kod eller dyrt och ineffektiv avsökningen tjänster.  I stället händelser skickas [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), eller till och med dina egna anpassade http-lyssnare och du bara betala för det du använder.

Azure SignalR Service-händelser skickas på ett tillförlitligt sätt till Event Grid-tjänsten som tillhandahåller pålitlig leveranstjänster till dina program via omfattande försök principer och förlorade leverans. Mer information finns i [Event Grid meddelandeleverans och försök igen](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Event Grid-modell](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Serverlös tillstånd
Azure SignalR Service-händelser är bara aktiva när klientanslutningar har utan server. Generellt sett om en klient inte vidarebefordrar till en hub-server, är den i tillståndet utan server. Klassiskt läge fungerar endast när hubben, som klientanslutningar ansluter till, inte har en navserver. Serverlös läge rekommenderas dock att undvika problem. Läs mer om tjänstläge i [välja tjänstläge](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Tillgängliga Azure SignalR Service-händelser
Händelserutnät använder [händelseprenumerationer](../event-grid/concepts.md#event-subscriptions) händelsen skicka meddelanden till prenumeranter. Händelseprenumerationer för Azure SignalR Service stöder två typer av händelser:  

|Händelsenamn|Beskrivning|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Utlöses när en klientanslutning är ansluten.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Utlöses när en klientanslutning kopplas bort.|

## <a name="event-schema"></a>Händelseschema
Azure SignalR Service-händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Azure SignalR Service händelsen med egenskapen händelsetyp börjar med ”Microsoft.SignalRService”. Mer information om användningen av egenskaper för Event Grid-händelse är dokumenterade i [Event Grid Händelseschema](../event-grid/event-schema.md).  

Här är ett exempel på en anslutning anslutna klienthändelse:
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

Mer information finns i [SignalR Service händelseschemat](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och testa Azure SignalR Service-händelser:

> [!div class="nextstepaction"]
> [Testa en exempel Event Grid-integration med Azure SignalR Service](./signalr-howto-event-grid-integration.md)
> [om Event Grid](../event-grid/overview.md)
