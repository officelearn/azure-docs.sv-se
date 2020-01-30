---
title: Händelse drivna arkitekturer på kant – Azure Event Grid på IoT Edge
description: Använd Azure Event Grid som en modul på IoT Edge för att vidarebefordra händelser mellan moduler, gräns enheter och molnet.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: feac5891734731e6f7377750127958a40a815036
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844672"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Vad är Azure Event Grid på Azure IoT Edge?
Event Grid på IoT Edge ger kraften och flexibiliteten i Azure Event Grid till gränsen. Skapa ämnen, publicera händelser och prenumerera på flera destinationer oavsett om de är moduler på samma enhet, andra gräns enheter eller tjänster i molnet.

Som i molnet hanterar Event Grid i IoT Edge-modulen Routning, filtrering och tillförlitlig leverans av händelser i stor skala. Filtrera händelser för att säkerställa att endast relevanta händelser skickas till olika händelse hanterare med avancerade sträng-, numeriska och booleska filter. Logik för omprövning ser till att händelsen når mål målet även om den inte är tillgänglig vid tidpunkten för publiceringen. Du kan använda Event Grid på IoT Edge som en kraftfull metod för lagring och vidarebefordran.

Event Grid på IoT Edge stöder både CloudEvents v 1.0 och anpassade händelse scheman. Det stöder också samma semantik/sub-semantik som Event Grid i molnet för enkel interoperabilitet.

Den här artikeln innehåller en översikt över Azure Event Grid på IoT Edge. Stegvisa instruktioner för att använda den här modulen på Edge finns i [publicera, prenumerera på händelser lokalt](pub-sub-events-webhook-local.md). 

![Event Grid på IoT Edge modell av källor och hanterare](../media/edge-overview/functional-model.png)

Den här bilden visar några av de olika sätt som du kan använda Event Grid på IoT Edge och inte en omfattande lista över funktioner som stöds.

## <a name="when-to-use-event-grid-on-iot-edge"></a>När du ska använda Event Grid på IoT Edge

Event Grid på IoT Edge är det enkelt att använda, tillförlitlig händelse modell för mellan kanten och molnet.

Event Grid på IoT Edge skapas med en symmetrisk runtime-yta i Azure-molnet, så att du kan använda samma händelser och API-samtal oavsett var du behöver. Oavsett om du använder pub/sub i molnet, på kanten eller mellan de två, kan Event Grid på IoT Edge nu vara en go-to-lösning.

Använd Event Grid på IoT Edge för att utlösa enkla arbets flöden mellan moduler. Du kan till exempel skapa ett ämne och publicera "lagrings BLOB skapad"-händelser från din Storage-modul till ämnet. Nu kan du prenumerera på en eller flera funktioner eller anpassade moduler i ämnena.

Utöka funktionerna mellan gräns enheter. Om du publicerar BLOB module-händelser och vill använda beräknings kraften hos flera nära enheter, skapar du kors enhets prenumerationer.

Anslut slutligen till molnet. Om dina BLOB module-händelser ska synkroniseras regelbundet till molnet, använder du den större beräkning som är tillgänglig i molnet eller skickar bearbetade data och skapar ytterligare prenumerationer för moln tjänster.

Event Grid på IoT Edge ger en flexibel och tillförlitlig frikopplad händelse arkitektur.

## <a name="event-sources"></a>Händelsekällor

På samma sätt som i molnet kan Event Grid på IoT Edge direkt integration mellan moduler för att bygga händelse drivna arkitekturer. För närvarande kan händelserna skickas till Event Grid på IoT Edge från:

* Azure Blob Storage på IoT Edge
* CloudEvents-källor
* Anpassade moduler & behållare via HTTP POST

## <a name="event-handlers"></a>Händelsehanterare

Event Grid på IoT Edge har skapats för att skicka händelser till önskad plats. För närvarande stöds följande destinationer:

* Andra moduler, inklusive IoT Hub, funktioner och anpassade moduler
* Andra gräns enheter
* WebHooks
* Azure Event Grid moln tjänst
* Händelsehubbar
* Service Bus-köer
* Service Bus-ämnen
* Lagrings köer

## <a name="supported-environments"></a>Miljöer som stöds
För närvarande stöds Windows 64-bitars-, Linux 64-bitars-och ARM 32-bitars miljöer.

## <a name="concepts"></a>Begrepp

Det finns fem koncept i Azure Event Grid som gör att du kan komma igång:

* **Händelser** – vad som hände.
* **Händelse källor** – där händelsen ägde rum.
* **Avsnitt** – slut punkten där utgivare skickar händelser.
* **Händelse prenumerationer** – slut punkten eller den inbyggda mekanismen för att dirigera händelser, ibland till fler än en hanterare. Prenumerationer används också av hanterarna för att filtrera inkommande händelser på ett intelligent sätt.
* **Händelse hanterare** – appen eller tjänsten som reagerar på händelsen.

## <a name="cost"></a>Kostnad

Event Grid på IoT Edge är gratis under den offentliga för hands versionen.

## <a name="issues"></a>Problem
Rapportera eventuella problem med att använda Event Grid på IoT Edge på [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues).

## <a name="next-steps"></a>Nästa steg

* [Publicera, prenumerera på händelser lokalt](pub-sub-events-webhook-local.md)
* [Publicera, prenumerera på händelser i molnet](pub-sub-events-webhook-cloud.md)
* [Vidarebefordra händelser till Event Grid Cloud](forward-events-event-grid-cloud.md)
* [Vidarebefordra händelser till IoTHub](forward-events-iothub.md)
* [Reagera på Blob Storage händelser lokalt](react-blob-storage-events-locally.md)