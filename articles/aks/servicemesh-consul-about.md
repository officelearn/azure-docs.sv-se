---
title: Översikt över konsuln
description: Få en översikt över konsuln
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594217"
---
# <a name="consul"></a>Konsul

## <a name="overview"></a>Översikt

[Consul][consul] är en multi datacentermedveten servicenätverkslösning för att ansluta och säkra tjänster mellan runtime-plattformar. [Connect][consul-features] är den komponent som tillhandahåller servicenätfunktioner.

## <a name="architecture"></a>Arkitektur

Konsuln tillhandahåller ett dataplan som består av [Envoy-baserade][envoy-proxy] [sidovagnar][consul-sidecar] som standard. Konsuln har en proxyarkitektur som kan anslutas. Dessa intelligenta proxyservrar styr all nätverkstrafik in och ut ur dina mesh-appar och arbetsbelastningar.

Kontrollplanet hanterar konfigurationen och principen via följande [komponenter:][consul-architecture]

- **Server** - En konsulagent som körs i serverläge och som upprätthåller consul-klustertillstånd.

- **Klient** - En konsulagent som körs i lätt klientläge. Varje beräkningsnod måste ha en klientagent igång. Den här konfigurationen och principen för klientmäklare mellan arbetsbelastningarna och konsulkonfigurationen. 

Följande arkitekturdiagram visar hur de olika komponenterna i dataplanet och kontrollplanet interagerar.

![Översikt över consulkomponenter och arkitektur.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Urvalskriterier

Det är viktigt att förstå och tänka på följande områden när du utvärderar konsuln för dina arbetsbelastningar:

- [Consul Principer](#consul-principles)
- [Funktioner](#capabilities)
- [Scenarier](#scenarios)


### <a name="consul-principles"></a>Konsulprinciper

Följande principer [är vägledande för][consul-principles] konsulprojektet:

- **API-Driven** - Kodifiera all konfiguration och princip.

- **Kör och anslut var som helst** – Anslut arbetsbelastningar över körningsplattformar (Kubernetes, virtuella datorer, serverlösa).

- **Utöka och integrera** – Anslut arbetsbelastningar på ett säkert sätt mellan infrastrukturen.


### <a name="capabilities"></a>Funktioner

Konsuln innehåller följande uppsättning funktioner:

- **Mesh** – gateway (multi datacenter), virtuella datorer (av klusternoder), synkronisering av tjänster, inbyggt felsökningsalternativ

- **Proxyservrar** – Sändebud, inbyggd proxy, plugable, l4-proxy tillgänglig för Windows-arbetsbelastningar

- **Trafikledning** – routning, delning, upplösning

- **Politik** – avsikter, ACL:er

- **Säkerhet** – auktorisering, autentisering, kryptering, SPIFFE-baserade identiteter, extern certifikatutfärdare (Arkiv), certifikathantering och rotation

- **Observerbarhet** – mätvärden, ui instrumentbräda, prometheus, grafana


### <a name="scenarios"></a>Scenarier

Konsuln är väl lämpad och föreslåd för följande scenarier:

- Utöka befintliga arbetsbelastningar för konsulkopplade arbetsbelastningar

- Efterlevnadskrav kring certifikathantering

- Tjänstnät för flera kluster

- VM-baserade arbetsbelastningar som ska ingå i tjänstnätet



## <a name="next-steps"></a>Nästa steg

I följande dokumentation beskrivs hur du kan installera konsul på Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Installera konsul i Azure Kubernetes Service (AKS)][consul-install]

Du kan också utforska consul-funktioner och arkitektur ytterligare:

- [Konsulfunktioner][consul-features]
- [Konsularkitektur][consul-architecture]
- [Consul - Hur Connect fungerar][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
