---
title: "Översikt över meddelandebearbetningsarkitekturen i Azure Service Bus | Microsoft Docs"
description: "Här beskrivs arkitekturen för meddelandebehandling i Azure Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: b810618b485b631e1d72b24c2a9587017d635cc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-architecture"></a>Service Bus-arkitektur
I den här artikeln beskrivs arkitekturen för meddelandebehandling i Azure Service Bus.

## <a name="service-bus-scale-units"></a>Service Bus-skalningsenheter
Service Bus är organiserat i *skalningsenheter*. En skalningsenhet är en distributionsenhet och innehåller alla komponenter som krävs för att köra tjänsten. Varje region distribuerar en eller flera Service Bus-skalningsenheter.

Ett Service Bus-namnområde är mappat till en skalningsenhet. Skalningsenheten hanterar alla typer av Service Bus-entiteter (köer, ämnen, prenumerationer). En Service Bus-skalningsenhet består av följande komponenter:

* **En uppsättning gateway-noder.** Gateway-noder autentiserar inkommande begäranden. Varje gateway-nod har en offentlig IP-adress.
* **En uppsättning asynkrona meddelandenoder.** Asynkrona meddelandenoder bearbetar begäranden om meddelandeentiteter.
* **Ett gateway-arkiv.** Gateway-arkivet innehåller data för varje entitet som definieras i den här skalningsenheten. Gateway-arkivet implementeras ovanpå en SQL Azure-databas.
* **Flera meddelandearkiv.** I meddelandearkiven lagras meddelanden från alla köer, ämnen och prenumerationer som definieras i den här skalningsenheten. De innehåller även alla data för prenumerationen. Om [partitionera enheter för meddelanden](service-bus-partitioning.md) inte är aktiverat kommer en kö eller ett ämne att lagras på en meddelandelagring. Prenumerationer lagras i samma meddelandearkiv som det överordnade ämnet. Förutom för Service Bus [Premium Messaging](service-bus-premium-messaging.md) implementeras meddelandearkiven ovanpå SQL Azure-databaser.

## <a name="containers"></a>Behållare
Varje meddelandeentitet tilldelas en specifik behållare. En behållare är en logisk konstruktion som använder ett meddelandearkiv för att lagra alla relevanta data för den behållaren. Varje behållare tilldelas en asynkron meddelandenod. Det finns normalt sett fler behållare än asynkrona meddelandenoder. Därför läser varje asynkron meddelandenod in flera behållare. Distributionen av behållare till en asynkron meddelandenod ordnas så att alla asynkrona meddelandenoder läses in lika. Om inläsningsmönstret ändras (till exempel om en av behållarna  belastas hårt) eller om en asynkron meddelandenod blir tillfälligt otillgänglig, omfördelas behållarna bland de asynkrona meddelandenoderna.

## <a name="processing-of-incoming-messaging-requests"></a>Bearbetning av inkommande meddelandebegäranden
När en klient skickar en begäran till Service Bus skickar Azure-belastningsutjämnaren den vidare till någon av gateway-noderna. Gateway-noden godkänner begäran. Om begäran gäller en meddelandeentitet (kö, ämne, prenumeration), letar gateway-noden upp entiteten i gateway-arkivet och avgör i vilket meddelandearkiv entiteten finns. Den söker sedan upp den asynkrona meddelandenod som för närvarande underhåller behållaren och skickar en begäran till den asynkrona meddelandenoden. Den asynkrona meddelandenoden behandlar begäran och uppdaterar entitetens tillstånd i behållarens arkiv. Den asynkrona meddelandenoden skickar sedan svaret tillbaka till gateway-noden, som skickar ett lämpligt svar tillbaka till klienten som utfärdade den ursprungliga begäran.

![Bearbetning av inkommande meddelandebegäranden](./media/service-bus-architecture/ic690644.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en översikt över Service Bus-arkitekturen hittar du mer information på följande länkar:

* [Översikt över Service Bus-meddelandetjänster](service-bus-messaging-overview.md)
* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [En lösning för köade meddelanden som använder Service Bus-köer](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)


