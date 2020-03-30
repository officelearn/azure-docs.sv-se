---
title: Händelsedrivna arkitekturer på kanten – Azure Event Grid på IoT Edge
description: Använd Azure Event Grid som en modul på IoT Edge för framåthändelser mellan moduler, kantenheter och molnet.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: feac5891734731e6f7377750127958a40a815036
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844672"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Vad är Azure Event Grid på Azure IoT Edge?
Event Grid på IoT Edge ger kraften och flexibiliteten i Azure Event Grid till kanten. Skapa ämnen, publicera händelser och prenumerera på flera mål oavsett om det är moduler på samma enhet, andra kantenheter eller tjänster i molnet.

Precis som i molnet hanterar eventrutnätet på IoT Edge-modulen routning, filtrering och tillförlitlig leverans av händelser i stor skala. Filtrera händelser för att säkerställa att endast relevanta händelser skickas till olika händelsehanterare med hjälp av avancerade sträng-, numeriska och booleska filter. Logiken För återförsök säkerställer att händelsen når målmålet även om den inte är tillgänglig vid publiceringstillfället. Det gör att du kan använda Event Grid på IoT Edge som en kraftfull butik och framåt mekanism.

Event Grid på IoT Edge stöder både CloudEvents v1.0 och anpassade händelsescheman. Den stöder också samma Pub / Sub semantik som Event Grid i molnet för enkel interoperabilitet.

Den här artikeln innehåller en översikt över Azure Event Grid på IoT Edge. Stegvisa instruktioner för att använda den här modulen på kanten finns i [Publicera, prenumerera på händelser lokalt](pub-sub-events-webhook-local.md). 

![Event Grid på IoT Edge-modell av källor och hanterare](../media/edge-overview/functional-model.png)

Den här bilden visar några av de sätt du kan använda Händelserutnät på IoT Edge och är inte en omfattande lista över funktioner som stöds.

## <a name="when-to-use-event-grid-on-iot-edge"></a>När ska händelserutnät användas på IoT Edge

Event Grid på IoT Edge ger en lättanvänd, tillförlitlig eventing-modell för mellan kanten och molnet.

Event Grid på IoT Edge är byggt med en symmetrisk körning yta till Azure molntjänst, så att du kan använda samma händelser och API-anrop var du än behöver. Oavsett om du gör pub/sub i molnet, på kanten eller mellan de två kan Event Grid på IoT Edge nu vara din enda go-to-lösning.

Använd Händelserutnät på IoT Edge för att utlösa enkla arbetsflöden mellan moduler. Skapa till exempel ett ämne och publicera "storage blob created" händelser från din lagringsmodul till ämnet. Du kan nu prenumerera på en eller flera funktioner eller anpassade moduler till dessa ämnen.

Utöka funktionaliteten mellan kantenheter. Om du publicerar blob-modulhändelser och vill använda beräkningskraften för flera enheter i närheten skapar du prenumerationer över flera enheter.

Slutligen ansluter du till molnet. Om dina blobmodulhändelser ska synkroniseras regelbundet till molnet, använda den större beräkning som är tillgänglig i molnet eller skicka bearbetade data uppåt, skapa ytterligare molntjänstprenumerationer.

Event Grid på IoT Edge ger en flexibel och tillförlitlig frikopplad eventing arkitektur.

## <a name="event-sources"></a>Händelsekällor

Precis som i molnet möjliggör Event Grid på IoT Edge direkt integrering mellan moduler för att skapa händelsedrivna arkitekturer. För närvarande kan händelserna skickas till Event Grid på IoT Edge från:

* Azure Blob Storage på IoT Edge
* CloudEvents källor
* Anpassade moduler & behållare via HTTP POST

## <a name="event-handlers"></a>Händelsehanterare

Event Grid på IoT Edge är byggd för att skicka händelser till var du vill. För närvarande stöds följande destinationer:

* Andra moduler, inklusive IoT Hub, funktioner och anpassade moduler
* Andra kantenheter
* WebHooks
* Molntjänst för Azure Event Grid
* Händelsehubbar
* Service Bus-köer
* Avsnitt om Service Bus
* Lagringsköer

## <a name="supported-environments"></a>Miljöer som stöds
För närvarande stöds Windows 64-bitars, Linux 64-bitars och ARM 32-bitarsmiljöer.

## <a name="concepts"></a>Begrepp

Det finns fem begrepp i Azure Event Grid som gör att du kan komma igång:

* **Händelser** — Vad hände.
* **Händelsekällor** – Där händelsen ägde rum.
* **Ämnen** – Slutpunkten där utgivare skickar händelser.
* **Händelseprenumerationer** – Slutpunkten eller den inbyggda mekanismen för att dirigera händelser, ibland till mer än en hanterare. Prenumerationer används också av hanterarna för att filtrera inkommande händelser på ett intelligent sätt.
* **Händelsehanterare** – Appen eller tjänsten som reagerar på händelsen.

## <a name="cost"></a>Kostnad

Event Grid på IoT Edge är gratis under offentlig förhandsversion.

## <a name="issues"></a>Problem
Rapportera eventuella problem med att använda [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)Händelserutnät på IoT Edge på .

## <a name="next-steps"></a>Nästa steg

* [Publicera, prenumerera på evenemang lokalt](pub-sub-events-webhook-local.md)
* [Publicera, prenumerera på händelser i molnet](pub-sub-events-webhook-cloud.md)
* [Vidarebefordra händelser till moln för Event Grid](forward-events-event-grid-cloud.md)
* [Vidarebefordra händelser till IoTHub](forward-events-iothub.md)
* [Agera på Blob Storage-händelser lokalt](react-blob-storage-events-locally.md)