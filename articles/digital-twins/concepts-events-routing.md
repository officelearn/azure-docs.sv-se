---
title: Routning händelser och meddelanden med Azure Digital Twins | Microsoft Docs
description: Översikt över routning händelser och meddelanden till Tjänsteslutpunkter med Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324381"
---
# <a name="routing-events-and-messages"></a>Routning händelser och meddelanden

IoT-lösningar har ofta förenats flera kraftfulla tjänster, inklusive lagring, analys med mera. Den här artikeln beskriver hur du ansluter Azure Digital Twins appar till Azure analys, AI och storage-tjänster att utöka dem med djupare insikter och funktioner.

## <a name="route-types"></a>Väg typer

Digitala Twins finns två sätt att integrera IoT-händelser i andra Azure-tjänster eller program:

* **Routning digitala Twins händelser**: Azure Digital Twins händelserna kan utlösas när ett objekt i spatial graph-ändringarna när dessa data tas emot, eller när en användardefinierade funktionen skapar en avisering baserat på fördefinierade villkor. Användarna kan skicka dessa händelser till [Händelsehubbar](https://azure.microsoft.com/services/event-hubs/), [Service Bus-ämnen](https://azure.microsoft.com/services/service-bus/), eller [Event Grid](https://azure.microsoft.com/services/event-grid/) för vidare bearbetning.

* **Routning enhetstelemetri**: förutom routning händelser, Azure Digital Twins kan också dirigera raw-enhet telemetrimeddelanden till Event Hubs för ytterligare insikter och analyser. Dessa typer av meddelanden inte bearbetas av digitala Twins och de vidarebefordras till endast de **Event Hub**.

Användare kan ange en eller flera utgående slutpunkter att skicka händelser eller för att vidarebefordra meddelanden. Händelser och meddelanden skickas till slutpunkter enligt inställningarna för fördefinierade routning. Med andra ord ange användare vissa en slutpunkt för att ta emot händelser för graph-åtgärden, en annan för att ta emot telemetri enhetshändelser och så vidare.

![Digitala Twins händelser Routning][1]

Routning till **Händelsehubbar** underhåller den ordning i vilken telemetri skickas meddelanden, så att de tas emot i slutpunkten i samma ordning som de ursprungligen togs emot. **Event Grid** och **Service Bus** inte garanterar att slutpunkter får händelser i samma ordning som de inträffade. Händelseschemat innehåller en tidsstämpel som kan användas för att identifiera ordningen efter händelserna tas emot på slutpunkten.

## <a name="route-implementation"></a>Dirigera implementering

Tjänsten Azure Digital Twins stöder för närvarande följande **EndpointTypes**:

* **EventHub**: är sträng anslutningsslutpunkten Event Hub.
* **ServiceBus**: är Service Bus-anslutning sträng-slutpunkten.
* **EventGrid**: är sträng anslutningsslutpunkten Event Grid.

Azure Digital Twins stöder för närvarande följande **EventTypes** som skickas till den valda slutpunkten:

* **DeviceMessages**: telemetrimeddelanden skickas från användarnas enheter och vidarebefordras av systemet.
* **TopologyOperation**: är åtgärder som ändrar graph eller metadata för diagrammet. Till exempel att lägga till eller ta bort en enhet, till exempel ett blanksteg.
* **SpaceChange**: är ändringar i ett utrymme beräknade värde till följd av en enhet telemetri-meddelande.
* **SensorChange**: är ändringar i en sensor beräknade värde till följd av en enhet telemetri-meddelande.
* **UdfCustom**: är anpassade meddelanden från en användardefinierad funktion.

> [!IMPORTANT]
> Inte alla **EndpointTypes** har stöd för alla **EventTypes**.
> Se tabellen nedan för den **EventTypes** som är tillåtna för varje **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **EventHub**|     X          |         X         |     X       |      X       |   X       |
| **serviceBus**|              |         X         |     X       |      X       |   X       |
| **EventGrid**|               |         X         |     X       |      X       |   X       |

>[!NOTE]
>Mer information om hur du skapar slutpunkter och exempel på händelser schemat finns i [slutpunkter och utgående](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Nästa steg

Mer information om offentlig förhandsversion begränsningar, läsa [Azure Digital Twins Förhandsgranska gränser](concepts-service-limits.md).

Om du vill prova ett exempel på Azure Digital Twins läsa [Snabbstart för att hitta tillgängliga rum](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png