---
title: Översikt för Azure Event Grid
description: Beskriver Azure Event Grid och dess begrepp.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 06/01/2018
ms.author: babanisa
ms.openlocfilehash: 6d0f769d65bc8ed4f41469b96edf4f0595d994de
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725249"
---
# <a name="an-introduction-to-azure-event-grid"></a>En introduktion till Azure Event Grid

Med Azure Event Grid kan du enkelt skapa program med händelsebaserade arkitekturer. Du väljer Azure-resursen som du vill prenumerera på och ger händelsehanteraren eller WebHook den slutpunkt som händelsen ska skickas till. Event Grid har inbyggt stöd för händelser som kommer från Azure-tjänster, som lagringsblobar och resursgrupper. Event Grid har också anpassat stöd för program och händelser från tredje part, med hjälp av anpassade ämnen och anpassade webhooks. 

Du kan använda filter för att dirigera specifika händelser till olika slutpunkter, använda multicast till flera slutpunkter och kontrollera att din händelser levereras på ett tillförlitligt sätt. Event Grid har också inbyggt stöd för anpassade händelser och händelser från tredje part.

Event Grid stöder för närvarande följande regioner:

* Sydostasien
* Asien, östra
* Östra Australien
* Sydöstra Australien
* Centrala USA
*   Östra USA
*   Östra USA 2
* Västeuropa
* Nordeuropa
* Östra Japan
* Västra Japan
*   Västra centrala USA
*   Västra USA
*   Västra USA 2

Den här artikeln innehåller en översikt för Azure Event Grid. Om du vill komma igång med Event Grid kan du läsa [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md). Följande bild visar hur Event Grid ansluter källor och hanterare, men den är inte någon heltäckande lista över vilka alternativ som stöds.

![Funktionell modell för Event Grid](./media/overview/functional-model.png)

## <a name="event-sources"></a>Händelsekällor

För närvarande stöder följande Azure-tjänster att skicka händelser till Event Grid:

* Azure-prenumerationer (hanteringsåtgärder)
* Anpassade ämnen
* Event Hubs
* IoT Hub
* Media Services
* Resursgrupper (hanteringsåtgärder)
* Service Bus
* Storage Blob
* Allmän lagring v2 (GPv2)

Länkar till artiklar som visar hur du använder varje händelsekälla finns i [Händelsekällor i Azure Event Grid](event-sources.md).

## <a name="event-handlers"></a>Händelsehanterare

För närvarande stöder följande Azure-tjänster hantering av händelser från Event Grid: 

* Azure Automation
* Azure Functions
* Event Hubs
* Hybridanslutningar
* Logic Apps
* Microsoft Flow
* Queue Storage
* WebHooks

Länkar till artiklar som visar hur du använder varje händelsehanterare finns i [Händelsehanterare i Azure Event Grid](event-handlers.md).

## <a name="concepts"></a>Begrepp

Det finns fem begrepp i Azure Event Grid som hjälper dig igång:

* **Händelser** – Vad hände?
* **Händelsekällor** – där händelsen ägde rum.
* **Ämnen** – Slutpunkten som utgivarna skickar händelser till.
* **Händelseprenumerationer** – Den slutpunkt eller inbyggda mekanism som dirigerar händelser, ibland till flera hanterare. Prenumerationer används också av hanterarna för att filtrera inkommande händelser på ett intelligent sätt.
* **Händelsehanterare** – Den app eller tjänst som reagerar på händelsen.

Mer information om de här begreppen finns i [Begrepp i Azure Event Grid](concepts.md).

## <a name="capabilities"></a>Funktioner

Här följer några viktiga funktioner i Azure Event Grid:

* **Enkelhet** – Peka och klicka för att dirigera händelser från Azure-resursen till valfri händelsehanterare eller slutpunkt.
* **Avancerad filtrering** – Filtrera på händelsetyp eller händelsens publiceringssökväg för att säkerställa att händelsehanterarna endast får relevanta händelser.
* **Förgrena** – Prenumerera på flera slutpunkter till samma händelse för att kunna skicka kopior av händelsen till så många platser som behövs.
* **Tillförlitlighet** – Använd 24-timmars återförsök med exponentiell backoff för att säkerställa att händelser levereras.
* **Betala per händelse** – Betala endast för hur mycket du använder Event Grid.
* **Högt dataflöde** – Skapa arbetsbelastningar med hög volym i Event Grid med stöd för miljontals händelser per sekund.
* **Inbyggda händelser** – Kom igång snabbt med resursdefinierade inbyggda händelser.
* **Anpassade händelser** – Använd Event Grid-dirigering, filtrera och leverera anpassade händelser i din app på ett tillförlitligt sätt.

En jämförelse av Event Grid, Event Hubs och Service Bus finns i [Välj mellan Azure-tjänster som levererar meddelanden](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Vad kan jag göra med Event Grid?

Azure Event Grid innehåller flera funktioner som avsevärt förbättrar åtgärdsautomatisering och integreringsarbete utan server: 

### <a name="serverless-application-architectures"></a>Arkitekturer för program utan server

![Program utan server](./media/overview/serverless_web_app.png)

Event Grid kopplar samman datakällor och händelsehanterare. Använd exempelvis Event Grid för att direkt utlösa att en funktion utan server kör bildanalys varje gång ett nytt foto läggs till i en bloblagringsbehållare. 

### <a name="ops-automation"></a>Automatisering av åtgärder

![Automatisering av åtgärder](./media/overview/Ops_automation.png)

Event Grid ger snabbare automatisering och enklare principtillämpning. Event Grid kan exempelvis meddela Azure Automation när en virtuell dator skapas eller en SQL-databas utökas. De här händelserna kan användas för att automatiskt kontrollera att tjänstkonfigurationer följer standard, placera metadata i åtgärdsverktyg, tagga virtuella datorer eller arkivera arbetsobjekt.

### <a name="application-integration"></a>Integrering av program

![Integrering av program](./media/overview/app_integration.png)

Event Grid ansluter din app till andra tjänster. Skapa till exempel ett anpassat ämne för att skicka appens händelsedata till Event Grid och dra nytta av dess tillförlitliga leverans, avancerade dirigering och direkta integrering med Azure. Du kan även använda Event Grid med Logic Apps för att bearbeta data var som helst, utan att skriva någon kod. 

## <a name="how-much-does-event-grid-cost"></a>Hur mycket kostar Event Grid?

Azure Event Grid använder en prismodell med betalning per händelse, så du betalar bara för det du använder. De första 100 000 åtgärderna per månad är gratis. Åtgärder definieras som händelsens ingång, leveransförsök av prenumeration, hanteringsanrop och filtrering av ämnessuffix. Se [sidan med våra priser](https://azure.microsoft.com/pricing/details/event-grid/) för mer information.

## <a name="next-steps"></a>Nästa steg

* [Dirigera Storage Blob-händelser](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Svara på Storage Blob-händelser med hjälp av Event Grid.
* [Skapa och prenumerera på anpassade händelser](custom-event-quickstart.md)  
  Kom igång direkt och börja skicka dina egna anpassade händelser till valfri slutpunkt med hjälp av snabbstarten för Azure Event Grid.
* [Använda Logic Apps som händelsehanterare](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  En självstudie om hur du skapar en app med hjälp av Logic Apps, för att kunna reagera på händelser som skickas av Event Grid.
* [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)  
  En självstudie som använder Azure Functions till att strömma data från Event Hubs till SQL Data Warehouse.
* [REST API-referens för Event Grid](/rest/api/eventgrid)  
  Ger mer teknisk information om Azure Event Grid och en referens för att hantera prenumerationer på händelser, routning och filtrering.