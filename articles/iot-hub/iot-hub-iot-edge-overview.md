---
title: "Översikt över Azure IoT-Edge | Microsoft Docs"
description: "Beskriver arkitekturen nyckelbegreppen i Azure IoT kant som gateways, moduler och mäklare."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2017
ms.author: dobett
ms.openlocfilehash: 11f6375c319c8945b0278003ee08215715f7ac42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-edge-architectural-concepts"></a>Azure IoT kant arkitektur begrepp

Innan du granska alla exempelkod eller skapa egna fältet gateway med IoT kant, bör du förstå viktiga begrepp som ligger till grund för arkitekturen för IoT kant.

## <a name="iot-edge-modules"></a>IoT-Edge-moduler

Du skapar en gateway med Azure IoT kant genom att skapa och montera *IoT kant moduler*. Moduler använder *meddelanden* för att utbyta data med varandra. En modul får ett meddelande, utför en åtgärd på det, omvandlar det eventuellt till ett nytt meddelande och publicerar det sedan för andra moduler att bearbeta. Vissa moduler kan bara generera nya meddelanden och bearbetar aldrig inkommande meddelanden. En kedja av moduler skapar en databearbetningspipeline med varje modul som utför en omvandling av data vid en punkt i denna pipeline.

![En kedja med moduler i en gateway som skapats med Azure IoT Egde][1]

IoT-Edge innehåller följande komponenter:

* Färdig moduler som utför vanliga gateway-funktioner.
* Gränssnitt som utvecklare kan använda för att skriva anpassade moduler.
* Den infrastruktur som krävs för att distribuera och köra en uppsättning moduler.

SDK innehåller ett Abstraktionslager som låter dig skapa gatewayer som ska köras på olika operativsystem och plattformar.

![Azure IoT Edge-abstraktionslager][2]

## <a name="messages"></a>Meddelanden

Tänk på att även om moduler som skickar meddelanden till varandra är ett bekvämt sätt att få en överblick över hur en gateway fungerar, så återspeglar det inte korrekt vad som händer. IoT kant moduler använda förhandlare för att kommunicera med varandra. Moduler publicera meddelanden i Service broker (med meddelandemönster, till exempel bus eller publicera/prenumerera) och därefter låta Service broker vidarebefordra meddelandet till de moduler som är anslutna till den.

En modul använder funktionen **Broker_Publish** för att publicera ett meddelande till meddelandekön. Den asynkrona meddelandekön levererar meddelanden till en modul genom att anropa en återanropsfunktion. Ett meddelande består av en uppsättning nyckel-/värdeegenskaper och innehåll som skickas som ett block med minne.

![Agentens roll i Azure IoT Edge][3]

## <a name="message-routing-and-filtering"></a>Meddelandedirigering och filtrering

Det finns två sätt att dirigera meddelanden till rätt IoT kant-modulerna:

* Du kan skicka en uppsättning länkar till Service broker så att Service broker vet källa och mottagare för varje modul.
* En modul kan filtrera i egenskaperna för meddelandet.

En modul ska bara agera på ett meddelande om meddelandet är avsett för modulen. Länkar och meddelandet filtrering effektivt skapar du en message-pipeline.

## <a name="next-steps"></a>Nästa steg

Dessa begrepp som används i ett exempel kan du köra finns [utforska Azure IoT kant arkitektur][lnk-hello-world].

<!-- Images -->
[1]: media/iot-hub-iot-edge-overview/modules.png
[2]: media/iot-hub-iot-edge-overview/modules_2.png
[3]: media/iot-hub-iot-edge-overview/messages_1.png

<!-- Links -->
[lnk-hello-world]: ./iot-hub-linux-iot-edge-get-started.md
