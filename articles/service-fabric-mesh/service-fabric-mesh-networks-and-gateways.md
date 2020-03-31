---
title: Introduktion till Azure Service Fabric-nätverk
description: Lär dig mer om nätverk, gateways och intelligent trafikroutning i Service Fabric Mesh.
author: dkkapur
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: dc793e2991783cc9b7b46d92fcc8e0267feb529b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459133"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Introduktion till nätverk i Service Fabric Mesh-program
I den här artikeln beskrivs olika typer av belastningsutjämnare, hur gateways ansluter nätverket till dina program till andra nätverk och hur trafiken dirigeras mellan tjänsterna i dina program.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Lager 4 vs lager 7 belastningsutjämnare
Belastningsutjämning kan utföras vid olika lager i [OSI-modellen](https://en.wikipedia.org/wiki/OSI_model) för nätverk, ofta i lager 4 (L4) och lager 7 (L7).  Vanligtvis finns det två typer av belastningsutjämnare:

- En L4 belastningsutjämnare arbetar på nätverkstransportskiktet, som handlar om leverans av paket utan hänsyn till innehållet i paketen. Endast pakethuvuden kontrolleras av belastningsutjämnaren, så balanseringskriterierna är begränsade till IP-adresser och portar. En klient upprättar till exempel en TCP-anslutning till belastningsutjämnaren. Belastningsutjämnaren avslutar anslutningen (genom att svara direkt på SYN), väljer en serverning och gör en ny TCP-anslutning till serverda (skickar ett nytt SYN). En L4-belastningsutjämnare fungerar vanligtvis endast på nivån för L4 TCP/UDP-anslutningen eller-sessionen. Således lastbalansen omdirigerar byte runt och ser till att byte från samma session hamnar på samma serverdel. L4 belastningsutjämnaren är omedveten om några programdetaljer om de byte som den rör sig. Bytena kan vara vilket programprotokoll som helst.

- En L7 belastningsutjämnare fungerar på programlagret, som behandlar innehållet i varje paket. Paketets innehåll kontrolleras eftersom det förstår protokoll som HTTP, HTTPS eller WebSockets. Detta ger belastningsutjämnaren möjlighet att utföra avancerad routning. En klient gör till exempel en enda HTTP/2 TCP-anslutning till belastningsutjämnaren. Belastningsutjämnaren fortsätter sedan att göra två backend-anslutningar. När klienten skickar två HTTP/2-strömmar till belastningsutjämnaren skickas en ström till serverda ett och ström två skickas till serverda två. Således kommer även multiplexering klienter som har mycket olika begäran laster balanseras effektivt över backends. 

## <a name="networks-and-gateways"></a>Nätverk och gatewayer
I [service fabric-resursmodellen](service-fabric-mesh-service-fabric-resources.md)är en nätverksresurs en individuellt distributionsbar resurs, oberoende av en program- eller tjänstresurs som kan referera till den som sitt beroende. Det används för att skapa ett nätverk för dina program som är öppna för Internet. Flera tjänster från olika program kan vara en del av samma nätverk. Det här privata nätverket skapas och hanteras av Service Fabric och är inte ett virtuellt Azure-nätverk (VNET). Program kan läggas till dynamiskt och tas bort från nätverksresursen för att aktivera och inaktivera VNET-anslutning. 

En gateway används för att överbrygga två nätverk. Gateway-resursen distribuerar en [Envoy-proxy](https://www.envoyproxy.io/) som tillhandahåller L4-routning för alla protokoll- och L7-routning för avancerad HTTP(S)-programdirigering. Gatewayen dirigerar trafik till nätverket från ett externt nätverk och avgör vilken tjänst som trafik ska dirigeras till.  Det externa nätverket kan vara ett öppet nätverk (i huvudsak det offentliga internet) eller ett virtuellt Azure-nätverk, så att du kan ansluta till dina andra Azure-program och resurser. 

![Nätverk och gateway][Image1]

När nätverksresursen skapas med `ingressConfig`tilldelas en offentlig IP-resurs till nätverksresursen. Den offentliga IP-adressen kommer att vara knuten till nätverksresursens livstid.

När ett Mesh-program skapas bör det referera till en befintlig nätverksresurs. Nya offentliga portar kan läggas till eller befintliga portar kan tas bort från ingresskonfigurationen. En borttagning för en nätverksresurs misslyckas om en programresurs refererar till den. När programmet tas bort tas nätverksresursen bort.

## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh finns i översikten:
- [Service Fabric Mesh-översikt](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png