---
title: Containersäkerhet
titleSuffix: Azure Cognitive Services
description: Läs om hur du skyddar din behållare
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fd2a6cdad01302501e30ec60a4d3ccf6efd9c266
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876848"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services-behållare

Säkerhet bör vara ett primärt fokus när du utvecklar program. Vikten av säkerhet är ett mått för framgång. När du skapar en programvarulösning som innehåller Cognitive Services-behållare är det viktigt att förstå de begränsningar och funktioner som är tillgängliga för dig. Mer information om nätverkssäkerhet finns i [Konfigurera virtuella Azure Cognitive Services-nätverk][az-security].

> [!IMPORTANT]
> Som standard finns det *ingen säkerhet* på Cognitive Services-behållar-API:et. Anledningen till detta är att behållaren oftast kommer att köras som en del av en pod som skyddas från utsidan av en nätverksbrygga. Det är dock möjligt att aktivera autentisering som fungerar identiskt med den autentisering som används vid åtkomst till [molnbaserade Cognitive Services][request-authentication].

Diagrammet nedan illustrerar standardmetoden och **den osäkra** metoden:

![Containersäkerhet](../media/container-security.svg)

Som ett alternativt och *säkert* tillvägagångssätt kan konsumenter av Cognitive Services-behållare utöka en behållare med en framåtriktad komponent och hålla behållarslutpunkten privat. Låt oss överväga ett scenario där vi använder [Istio][istio] som en ingress gateway. Istio stöder HTTPS/TLS och klientcertifikatautentisering. I det här fallet exponerar Istio-klientdelen behållaråtkomsten och presenterar klientcertifikatet som är vitlistat i förväg med Istio.

[Nginx][nginx] är ett annat populärt val i samma kategori. Både Istio och Nginx fungerar som ett servicenät och erbjuder ytterligare funktioner, inklusive saker som belastningsutjämning, routning och hastighetskontroll.

### <a name="container-networking"></a>Nätverk för containrar

Cognitive Services-behållarna måste skicka information om mätning för faktureringsändamål. Det enda undantaget är *offlinebehållare* eftersom de följer en annan faktureringsmetod. Det gick inte att tillåta en lista över olika nätverkskanaler som Cognitive Services-behållarna förlitar sig på förhindrar att behållaren fungerar.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Tillåt lista Cognitive Services-domäner och portar

Värden bör tillåta **listport 443** och följande domäner:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Inaktivera djup paketinspektion

> [DPI (Deep Packet Inspection)](https://en.wikipedia.org/wiki/Deep_packet_inspection) är en typ av databehandling som i detalj granskar de data som skickas via ett datornätverk och vanligtvis vidtar åtgärder genom att blockera, dirigera om eller logga dem därefter.

Inaktivera DPI på de säkra kanaler som Cognitive Services-behållarna skapar för Microsoft-servrar. Om du inte gör det förhindrar du att behållaren fungerar korrekt.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
