---
title: Routningshändelser och -meddelanden – Azure Digital Twins | Microsoft-dokument
description: Översikt över routningshändelser och meddelanden till tjänstslutpunkter med Azure Digital Twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 65b760eaf28d907fab3654ed92f960be7556b0d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862364"
---
# <a name="routing-iot-events-and-messages"></a>Routning av IoT-händelser och meddelanden

Internet of Things-lösningar förenar ofta flera kraftfulla tjänster som inkluderar lagring, analys med mera. I den här artikeln beskrivs hur du ansluter Azure Digital Twins-appar till Azure Analytics-, AI- och lagringstjänster för att ge dem djupare insikter och funktioner.

## <a name="route-types"></a>Rutttyper  

Azure Digital Twins erbjuder två sätt att ansluta IoT-händelser till andra Azure-tjänster eller affärsprogram:

* **Routning av Azure Digital Twins-händelser:** Ett objekt i det rumsliga diagrammet som ändras, telemetridata som tas emot eller en användardefinierad funktion som skapar ett meddelande baserat på fördefinierade villkor kan utlösa Azure Digital Twins-händelser. Användare kan skicka dessa händelser till [Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/) [Azure Service Bus ämnen](https://azure.microsoft.com/services/service-bus/)eller Azure Event [Grid](https://azure.microsoft.com/services/event-grid/) för vidare bearbetning.

* **Telemetri för routningsenhet:** Förutom routningshändelser kan Azure Digital Twins även dirigera meddelanden om rå enhetstelemetri till eventhubbar för ytterligare insikter och analyser. Dessa typer av meddelanden bearbetas inte av Azure Digital Twins. Och de vidarebefordras bara till eventhubben.

Användare kan ange en eller flera slutpunkter för utgående start för att skicka ut händelser eller vidarebefordra meddelanden. Händelser och meddelanden skickas till slutpunkterna enligt dessa fördefinierade routningsinställningar. Med andra ord kan användare ange en viss slutpunkt för att ta emot diagramåtgärdshändelser, en annan för att ta emot enhetstelemetrihändelser och så vidare.

[![Routning av Azure Digital Twins-händelser](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Routning till händelsehubbar upprätthåller den ordning i vilken telemetrimeddelanden skickas. Så de anländer till slutpunkten i samma sekvens som de ursprungligen togs emot. 

Event Grid och Service Bus garanterar inte att slutpunkter kommer att ta emot händelser i samma ordning som de inträffade. Händelseschemat innehåller dock en tidsstämpel som kan användas för att identifiera ordern när händelserna anländer till slutpunkten.

## <a name="route-implementation"></a>Ruttimplementering

Azure Digital Twins-tjänsten stöder för närvarande följande **EndpointTypes:**

* **EventHub** är slutpunkten för anslutningssträngen Event Hubs.
* **ServiceBus** är slutpunkten för anslutningssträngen Service Bus.
* **EventGrid** är slutpunkten för anslutningssträngen För händelserutnät.

Azure Digital Twins stöder för närvarande följande **EventTypes** som ska skickas till den valda slutpunkten:

* **DeviceMessages** är telemetrimeddelanden som skickas från användarnas enheter och vidarebefordras av systemet.
* **TopologiOperation** är en åtgärd som ändrar diagrammet eller metadata i diagrammet. Ett exempel är att lägga till eller ta bort en entitet, till exempel ett blanksteg.
* **SpaceChange** är en förändring i ett blankstegs beräknade värde som är resultatet av ett enhetstelemetrimeddelande.
* **SensorChange** är en förändring i en sensors beräknade värde som är resultatet av ett enhetstelemetrimeddelande.
* **UdfCustom** är ett anpassat meddelande från en användardefinierad funktion.

> [!IMPORTANT]  
> Alla **EndpointTypes** stöder inte alla **EventTypes**.
> Granska följande tabell för de **EventTypes** som tillåts för varje **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange (spacechange) | SensorÄnd av | UdfCustom (UdfCustom) |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid (På ett sätt som är fallet)|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Mer information om hur du skapar slutpunkter och exempel på händelsers schema finns i [Egress och slutpunkter](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om azure digital twins preview gränser, läsa [Public preview service limits](concepts-service-limits.md).

- Om du vill prova ett Azure Digital Twins-exempel läser du [snabbstarten för att hitta tillgängliga rum](quickstart-view-occupancy-dotnet.md).