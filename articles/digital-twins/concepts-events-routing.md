---
title: Dirigera händelser och meddelanden – Azure Digitals, dubblare | Microsoft Docs
description: Översikt över routning av händelser och meddelanden till tjänst slut punkter med Azure Digitals
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: f2479d9f3e278d23d62275b667f78d1fd70dd151
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889693"
---
# <a name="routing-events-and-messages"></a>Routningshändelser och meddelanden

IoT-lösningar enhets ofta flera kraftfulla tjänster som omfattar lagring, analys och mycket annat. Den här artikeln beskriver hur du ansluter Azure Digitals dubbla appar till Azure Analytics-, AI-och lagrings tjänster för att ge dem djupare insikter och funktioner.

## <a name="route-types"></a>Flödes typer  

Azure Digital-dubbla finns på två sätt för att ansluta IoT-händelser med andra Azure-tjänster eller affärs program:

* **Routning av Azure Digitals dubbla nätverks händelser**: ett objekt i den spatialdata som ändrar, telemetridata som tas emot eller en användardefinierad funktion som skapar ett meddelande baserat på fördefinierade villkor kan utlösa Azure Digitals dubbla händelser. Användare kan skicka dessa händelser till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure Service Bus ämnen](https://azure.microsoft.com/services/service-bus/)eller [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) för ytterligare bearbetning.

* **Telemetri för routning av enhet**: förutom att dirigera händelser kan även Azure Digitals-enheter dirigera meddelanden om telemetri för enheter till Event Hubs för ytterligare insikter och analyser. Dessa typer av meddelanden bearbetas inte av digitala Azure-dubbla. Och de vidarebefordras endast till händelsehubben.

Användare kan ange en eller flera utgående slut punkter för att skicka händelser eller vidarebefordra meddelanden. Händelser och meddelanden kommer att skickas till slut punkterna enligt dessa fördefinierade inställningar för routning. Med andra ord kan användarna ange en viss slut punkt för att ta emot diagram åtgärds händelser, en annan för att ta emot telemetri-händelser och så vidare.

[![routning av Azure Digitals sammanflätade händelser](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Routning till Event Hubs upprätthåller i vilken ordning telemetri-meddelanden ska skickas. De kommer då till slut punkten i samma ordning som de ursprungligen togs emot. 

Event Grid och Service Bus garanterar inte att slut punkterna tar emot händelser i samma ordning som de har inträffat. Händelse schemat innehåller dock en tidsstämpel som kan användas för att identifiera ordningen när händelserna kommer till slut punkten.

## <a name="route-implementation"></a>Väg implementering

Azure Digitals dubbla tjänster har för närvarande stöd för följande **EndpointTypes**:

* **EventHub** är slut punkten för Event Hubs anslutnings strängen.
* **Service Bus** är slut punkten för den Service Bus anslutnings strängen.
* **EventGrid** är slut punkten för den Event Grid anslutnings strängen.

Azure Digital-dubbla är för närvarande stöd för följande **EventTypes** som skickas till den valda slut punkten:

* **DeviceMessages** är telemetri meddelanden som skickas från användarnas enheter och vidarebefordras av systemet.
* **TopologyOperation** är en åtgärd som ändrar grafen eller metadata i grafen. Ett exempel är att lägga till eller ta bort en entitet, till exempel ett blank steg.
* **SpaceChange** är en ändring i ett utrymmes beräknade värde som resulterar från ett meddelande om enhets telemetri.
* **SensorChange** är en förändring i en sensors beräknade värde som resulterar från ett meddelande om enhets telemetri.
* **UdfCustom** är ett anpassat meddelande från en användardefinierad funktion.

> [!IMPORTANT]  
> Alla **EndpointTypes** stöder inte alla **EventTypes**.
> Se följande tabell för de **EventTypes** som är tillåtna för varje **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Mer information om hur du skapar slut punkter och exempel på schema för händelser finns i [utgående och slut punkter](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om för hands versioner av Azure Digitals för hands versioner finns i [begränsningar för allmän för hands versions tjänst](concepts-service-limits.md).

- Om du vill prova ett digitalt Azure-exempel kan du läsa [snabb starten för att hitta tillgängliga rum](quickstart-view-occupancy-dotnet.md).