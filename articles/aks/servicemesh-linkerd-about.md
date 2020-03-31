---
title: Översikt över Linkerd
description: Få en översikt över Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593775"
---
# <a name="linkerd"></a>Linkerd (länk)

## <a name="overview"></a>Översikt

[Linkerd][linkerd] är ett lättanvänt och lätt servicenät.

## <a name="architecture"></a>Arkitektur

Linkerd tillhandahåller ett dataplan som består av ultralätta [Linkerd-specialiserade][linkerd-proxy] proxy-sidovagnar. Dessa intelligenta proxyservrar styr all nätverkstrafik in och ut ur dina mesh-appar och arbetsbelastningar. Proxyservrarna exponerar också mätvärden via [Prometheus-måttslutpunkter.][prometheus]

Kontrollplanet hanterar konfigurationen och aggregerad telemetri via följande [komponenter:][linkerd-architecture]

- **Controller** - Tillhandahåller api som driver Linkerd CLI och Dashboard. Ger konfiguration för proxyservrar.

- **Tryck på** - Upprätta klockor i realtid på förfrågningar och svar.

- **Identitet** - Tillhandahåller identitets- och säkerhetsfunktioner som möjliggör mTLS mellan tjänster.

- **Webb** - Tillhandahåller instrumentpanelen i Linkerd.


Följande arkitekturdiagram visar hur de olika komponenterna i dataplanet och kontrollplanet interagerar.


![Översikt över Linkerd-komponenter och arkitektur.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Urvalskriterier

Det är viktigt att förstå och tänka på följande områden när du utvärderar Linkerd för dina arbetsbelastningar:

- [Designprinciper](#design-principles)
- [Funktioner](#capabilities)
- [Scenarier](#scenarios)


### <a name="design-principles"></a>Designprinciper

Följande designprinciper [vägleder][design-principles] Linkerd-projektet:

- **Keep it Simple** - Måste vara lätt att använda och förstå.

- **Minimera resurskrav** - Införa minimal prestanda och resurskostnad.

- **Just Work** - Bryt inte befintliga program och kräver inte komplex konfiguration.


### <a name="capabilities"></a>Funktioner

Linkerd innehåller följande uppsättning funktioner:

- **Mesh** – inbyggt felsökningsalternativ

- **Trafikledning** – delning, timeout, återförsök, inträngning

- **Säkerhet** – kryptering (mTLS), certifikat som roteras automatiskt var 24:e timme

- **Observerbarhet** – gyllene mått, kran, spårning, serviceprofiler och per ruttmått, webbinstrumentpanel med topologidiagram, prometheus, grafana


### <a name="scenarios"></a>Scenarier

Linkerd är väl lämpad för och föreslås för följande scenarier:

- Enkel att använda med bara den väsentliga uppsättningen kapacitetskrav

- Låg latens, låg overhead, med fokus på observerbarhet och enkel trafikledning


## <a name="next-steps"></a>Nästa steg

I följande dokumentation beskrivs hur du kan installera Linkerd på Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Installera Linkerd i Azure Kubernetes Service (AKS)][linkerd-install]

Du kan också utforska Linkerds funktioner och arkitektur ytterligare:

- [Länkningsfunktioner][linkerd-features]
- [Länkningsarkitektur][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md