---
title: Reagera på Azure SignalR-tjänsthändelser
description: Använd Azure Event Grid för att prenumerera på Azure SignalR-tjänsthändelser. Andra underordnade tjänster kan utlösas av dessa händelser.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158188"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagera på händelser i Azure SignalR Service

Azure SignalR Service-händelser gör det möjligt för program att reagera på klientanslutningar som är anslutna eller frånkopplade med hjälp av moderna serverlösa arkitekturer. Det gör den utan behov av komplicerad kod eller dyra och ineffektiva valtjänster.  I stället överförs händelser via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som Azure [Functions,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med till din egen anpassade http-lyssnare, och du betalar bara för det du använder.

Azure SignalR-tjänsthändelser skickas på ett tillförlitligt sätt till Event Grid-tjänsten som tillhandahåller tillförlitliga leveranstjänster till dina program via avancerade principer för återförsök och leverans av obeställbara brev. Mer information finns i [Leverans av meddelande från Event Grid och försök igen](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Modell för händelserutnät](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Serverlöst tillstånd
Azure SignalR-tjänsthändelser är bara aktiva när klientanslutningar är i serverlöst tillstånd. Generellt sett, om en klient inte väg till en hubbserver, går den in i serverlöst tillstånd. Klassiskt läge fungerar bara när navet, som klientanslutningar ansluter till, inte har en navserver. Serverlöst läge rekommenderas dock för att undvika vissa problem. Mer information om serviceläge finns i [Så här väljer du Serviceläge](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Tillgängliga Azure SignalR-tjänsthändelser
Event grid använder [händelseprenumerationer](../event-grid/concepts.md#event-subscriptions) för att dirigera händelsemeddelanden till prenumeranter. Azure SignalR-tjänsthändelseprenumerationer stöder två typer av händelser:  

|Händelsenamn|Beskrivning|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Utlöses när en klientanslutning är ansluten.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Utlöses när en klientanslutning kopplas från.|

## <a name="event-schema"></a>Händelseschema
Azure SignalR-tjänsthändelser innehåller all information du behöver för att svara på ändringarna i dina data. Du kan identifiera en Azure SignalR-tjänsthändelse med eventType-egenskapen som börjar med "Microsoft.SignalRService". Ytterligare information om användningen av händelserutnätshändelseegenskaper dokumenteras i [händelseschemat för Händelserutnät](../event-grid/event-schema.md).  

Här är ett exempel på en klientanslutningsansluten händelse:
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

Mer information finns i [schema för SignalR-tjänsthändelser](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure SignalR-tjänsthändelser ett försök:

> [!div class="nextstepaction"]
> [Prova ett exempel på integrering av händelserutnät med Azure SignalR-tjänst](./signalr-howto-event-grid-integration.md)
> [om händelserutnät](../event-grid/overview.md)
