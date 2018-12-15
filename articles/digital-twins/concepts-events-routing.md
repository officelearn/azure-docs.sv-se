---
title: Routning händelser och meddelanden med Azure Digital Twins | Microsoft Docs
description: Översikt över routning händelser och meddelanden till Tjänsteslutpunkter med Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: e16d27314a159b124e35560ffb8cd9685fc5c7a0
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438021"
---
# <a name="routing-events-and-messages"></a>Routningshändelser och meddelanden

IoT-lösningar har ofta förenats flera kraftfulla tjänster som omfattar lagring och analys. Den här artikeln beskriver hur du ansluter Azure Digital Twins appar till Azure analys, AI och storage-tjänster att ge dem djupare insikter och funktioner.

## <a name="route-types"></a>Väg typer  

Azure Digital Twins finns två sätt att integrera IoT-händelser i andra Azure-tjänster eller program:

* **Routning Azure Digital Twins händelser**: Ett objekt i den spatial graph som ändringar, telemetridata som tas emot, eller en användardefinierad funktion som skapar en avisering baserat på fördefinierade villkor kan utlösa Azure Digital Twins händelser. Användarna kan skicka dessa händelser till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure Service Bus-ämnen](https://azure.microsoft.com/services/service-bus/), eller [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) för vidare bearbetning.

* **Routning enhetstelemetri**: Förutom routning händelser, kan Azure Digital Twins också dirigera raw-enhet telemetrimeddelanden till Event Hubs för ytterligare insikter och analyser. Dessa typer av meddelanden bearbetas inte av Azure Digital Twins. Och de är bara vidarebefordras till händelsehubben.

Användare kan ange en eller flera utgående slutpunkter att skicka händelser eller för att vidarebefordra meddelanden. Händelser och meddelanden skickas till slutpunkter enligt inställningarna för fördefinierade routning. Med andra ord kan användarna ange en viss slutpunkt för att ta emot händelser för graph-åtgärden, en annan för att ta emot telemetri enhetshändelser och så vidare.

![Azure Digital Twins händelser Routning][1]

Routning till Event Hubs underhåller den ordning i vilken telemetrimeddelanden skickas. Så kommer de till slutpunkten i samma ordning som de ursprungligen togs emot. Event Grid och Service Bus garanterar inte att slutpunkter visas händelser i samma ordning som de inträffade. Händelseschemat innehåller emellertid en tidsstämpel som kan användas för att identifiera ordningen efter händelserna tas emot på slutpunkten.

## <a name="route-implementation"></a>Dirigera implementering

Tjänsten Azure Digital Twins stöder för närvarande följande **EndpointTypes**:

* **EventHub** är Händelsehubbar sträng anslutningsslutpunkten.
* **ServiceBus** är Service Bus-anslutning sträng-slutpunkten.
* **EventGrid** är sträng anslutningsslutpunkten Event Grid.

Azure Digital Twins stöder för närvarande följande **EventTypes** som skickas till den valda slutpunkten:

* **DeviceMessages** telemetrimeddelanden skickas från användarnas enheter och vidarebefordras av systemet.
* **TopologyOperation** är en åtgärd som ändrar graph eller metadata för diagrammet. Ett exempel är att lägga till eller tar bort en enhet, till exempel ett blanksteg.
* **SpaceChange** görs en ändring i ett utrymme beräknade värde som är ett resultat från en enhet telemetri-meddelande.
* **SensorChange** görs en ändring i en sensor beräknade värde som är ett resultat från en enhet telemetri-meddelande.
* **UdfCustom** är ett anpassat meddelande från en användardefinierad funktion.

> [!IMPORTANT]  
> Inte alla **EndpointTypes** har stöd för alla **EventTypes**.
> Se tabellen nedan för den **EventTypes** som är tillåtna för varje **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Läs mer om hur du skapar slutpunkter och exempel på ' händelseschemat [slutpunkter och utgående](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Digital Twins gränser preview, i [förhandsversion tjänstbegränsningar](concepts-service-limits.md).
- Om du vill prova ett exempel på Azure Digital Twins se den [Snabbstart för att hitta tillgängliga rum](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
