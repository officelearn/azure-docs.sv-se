---
title: Översikt över Istio
description: Få en översikt över Istio
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593908"
---
# <a name="istio"></a>Istio (på)

## <a name="overview"></a>Översikt

[Istio][istio] är ett komplett, anpassningsbart och utbyggbart servicenät.

## <a name="architecture"></a>Arkitektur

Istio tillhandahåller ett dataplan som består av [Envoy-baserade][envoy-proxy]sidovagnar. Dessa intelligenta proxyservrar styr all nätverkstrafik in och ut ur dina mesh-appar och arbetsbelastningar.

Kontrollplanet hanterar konfigurationen, principen och telemetrin via följande [komponenter:][what-is-istio]

- **Mixer** - Tillämpar principer för åtkomstkontroll och användning. Samlar telemetri från fullmakter som skjuts in [prometheus][prometheus].

- **Pilot** - Tillhandahåller princip/konfiguration för tjänstidentifiering och trafikhantering för proxyservrarna.

- **Citadel** - Tillhandahåller identitets- och säkerhetsfunktioner som möjliggör mTLS mellan tjänster.

- **Pentry** - Abstracts och ger konfiguration till komponenter.

Följande arkitekturdiagram visar hur de olika komponenterna i dataplanet och kontrollplanet interagerar.


![Översikt över Istio-komponenter och arkitektur.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Urvalskriterier

Det är viktigt att förstå och tänka på följande områden när du utvärderar Istio för dina arbetsbelastningar:

- [Designmål](#design-goals)
- [Funktioner](#capabilities)
- [Scenarier](#scenarios)


### <a name="design-goals"></a>Designmål

Följande designmål [styr][design-goals] Istio-projektet:

- **Maximera genomskinlighet** - Tillåt införande med minsta möjliga arbete för att få verkligt värde från systemet.

- **Utökningsbarhet** - Måste kunna växa och anpassa sig med förändrade behov.

- **Bärbarhet** - Kör enkelt i olika typer av miljöer - moln, lokalt.

- **Politisk enhetlighet** - Konsekvens i utformningen av politiken över olika resurser.


### <a name="capabilities"></a>Funktioner

Istio tillhandahåller följande uppsättning funktioner:

- **Mesh** – gateways (multi-cluster), virtuella datorer (meshexpansion)

- **Trafikledning** – routning, delning, timeout, brytare, återförsök, ingående, utgående

- **Princip** – åtkomstkontroll, hastighetsgräns, kvot, anpassade principkort

- **Säkerhet** – autentisering (jwt), auktorisering, kryptering (mTLS), extern certifikatutfärdarcertifikatutfärdar (HashiCorp Vault)

- **Observerbarhet** – gyllene mätvärden, spegel, spårning, anpassade adaptrar, prometheus, grafana

### <a name="scenarios"></a>Scenarier

Istio är väl lämpad för och föreslås för följande scenarier:

- Kräv utökningsbarhet och omfattande uppsättning funktioner

- Mesh-expansion till att omfatta VM-baserade arbetsbelastningar

- Tjänstnät för flera kluster

## <a name="next-steps"></a>Nästa steg

I följande dokumentation beskrivs hur du kan installera Istio på Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Installera Istio i Azure Kubernetes Service (AKS)][istio-install]

Du kan också utforska Istio-koncept och ytterligare distributionsmodeller ytterligare:

- [Istio-begrepp][what-is-istio]
- [Istio-distributionsmodeller][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
