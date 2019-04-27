---
title: Introduktion till Azure Service Fabric-nätverk | Microsoft Docs
description: Läs mer om nätverk, gatewayer och intelligent trafikdirigering i Service Fabric-nät.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b0e1047c5bbd7d8caaf2afd8b002be1c46837852
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811021"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Introduktion till nätverk i Service Fabric-nät program
Den här artikeln beskrivs olika typer av belastningsutjämnare, hur gateways ansluter nätverket med dina program till andra nätverk och hur trafik dirigeras mellan tjänster i dina program.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Layer 4 jämfört med layer 7-belastningsutjämnare
Utjämning av nätverksbelastning kan utföras på olika nivåer i den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model) för nätverk, ofta i olika lager 4 (L4) och layer 7 (L7).  Det finns vanligtvis två typer av belastningsutjämnare:

- En belastningsutjämnare för L4 fungerar på transportlagret nätverk, som hanterar leveransen av paket med ingen hänsyn till innehållet i paketen. Endast paketrubriker kontrolleras av belastningsutjämnaren, så belastningsutjämning kriterierna är begränsad till IP-adresser och portar. Exempelvis kan gör en klient en TCP-anslutning till belastningsutjämnaren. Belastningsutjämnaren avslutar anslutningen (genom att svara direkt på SYN), väljer en serverdel och gör en ny TCP-anslutning till serverdel (skickar en ny SYN). En belastningsutjämnare för L4 fungerar vanligtvis endast på nivån för L4 TCP/UDP-anslutning eller session. Därför belastningsutjämningen omdirigerar byte runt och ser till att byte från samma session avveckla på samma serverdel. L4-belastningsutjämnare är medveten om eventuella programinformation rader med byte som den flyttar. Antal byte som kan vara alla protokoll.

- En belastningsutjämnare för L7 fungerar på programnivå som behandlar innehållet i varje paket. Den kontrollerar paketinnehållet eftersom den förstår protokoll som HTTP, HTTPS och WebSockets. Detta ger belastningsutjämnaren möjligheten att utföra avancerade routning. Exempelvis kan gör en klient en enkel HTTP/2-TCP-anslutning till belastningsutjämnaren. Belastningsutjämnaren fortsätter sedan att skapa två backend-anslutningar. När klienten skickar två HTTP/2-dataströmmar till belastningsutjämnaren, stream något skickas till serverdelen något och stream två skickas till serverdelen två. Därför kommer även multiplexering klienter som har avsevärt olika begäran belastning att balanseras effektivt mellan serverdelen. 

## <a name="networks-and-gateways"></a>Nätverk och gatewayer
I den [Service Fabric-Resursmodell](service-fabric-mesh-service-fabric-resources.md), en nätverksresurs är en separat distribuerbar resurs, oberoende av en resurs för programmet eller tjänsten som kan referera till den som deras beroende. Den används för att skapa ett nätverk för dina program som är öppen för internet. Flera tjänster från olika program kan ingå i samma nätverk. Den här privata nätverk skapas och hanteras av Service Fabric och är inte ett Azure-nätverk (VNET). Program kan dynamiskt har lagts till och tas bort från nätverksresursen som du aktiverar och inaktiverar VNET-anslutning. 

En gateway används till överbrygga två nätverk. Gatewayresursen distribuerar en [Envoy proxy](https://www.envoyproxy.io/) som ger L4 routning för alla protokoll och L7 routning för avancerade HTTP (S)-program och routning. Gatewayen dirigerar trafik i ditt nätverk från ett externt nätverk och avgör vilken tjänst för att dirigera trafik till.  Det externa nätverket kan vara ett öppet nätverk (vad som sker är det offentliga internet) eller ett Azure-nätverk, vilket gör att du kan ansluta till andra Azure-program och resurser. 

![Nätverk och gateway][Image1]

När nätverksresursen skapas med `ingressConfig`, en offentlig IP-adress tilldelas till nätverksresursen. Den offentliga IP-Adressen kommer att vara bundna till livslängden för nätverksresursen.

När ett nät program skapas, bör det refererar till en befintlig nätverksresurs. Du kan lägga till nya offentliga portar eller befintliga portar kan tas bort från den ingående-konfigurationen. En borttagning av en nätverksresurs misslyckas om en resurs för en refererar till den. När programmet tas bort, tas bort nätverksresursen.

## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh finns i översikten:
- [Service Fabric Mesh-översikt](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png