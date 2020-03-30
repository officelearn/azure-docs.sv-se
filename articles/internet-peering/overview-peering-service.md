---
title: Internet-peering kontra peering-tjänst
titleSuffix: Azure
description: Internet-peering kontra peering-tjänst
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6c1205fcacd3d7228f1aecf1e603b66d9e1fcee5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75775707"
---
# <a name="internet-peering-vs-peering-service"></a>Internet-peering kontra peering-tjänst

Internet-peering avser all samtrafik mellan Microsofts globala nätverk (AS8075) och Carriers eller Service Providers nätverk. En tjänsteleverantör kan bli peering-tjänstpartner genom att implementera partnerskapkraven för peering-tjänsten som beskrivs nedan för att tillhandahålla tillförlitlig och högpresterande offentlig anslutning med optimal routning från kunden till Microsoft-nätverket.

## <a name="about-peering-service"></a>Om Peering Service
Peering Service är ett partnerskapsprogram med viktiga tjänsteleverantörer för att ge förstklassig offentlig Internet-anslutning till sina företagsanvändare. Partner som ingår i programmet kommer att ha direkta, mycket tillgängliga, geo-redundanta anslutningar och optimerad routning till Microsoft. Peering Service är ett tillägg till Microsofts anslutningsportfölj:
*   ExpressRoute för privat anslutning till IaaS- eller PaaS-resurser (stöd för privat IP-utrymme)
    *   Partnerbaserad anslutning
    *   Direkt 100G-anslutning till Microsoft
*   IPSEC över Internet för VPN-anslutning till molnet
*   SD-WAN-anslutning till Azure via VirtuellT WAN

Målsegmentet för Peering Service är SaaS-anslutning, SD-WAN-kunder som är villiga att göra internetutbrytning på filialen och alla kunder som har en dubbel strategi MPLS och internet i företagsklass.

Det primära målet vid anslutning till Microsoft Cloud bör vara att minimera svarstiden genom att minska rundresastiden (RTT) från en användarwebbplats till Microsofts globala nätverk, som är Microsofts stamnät för det offentliga nätverket som kopplar samman alla Microsofts datacenter och flera startpunkter för molnprogram. Se [Få bästa anslutning och prestanda i Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Bild av distribuerad åtkomst](./media/distributed-access.png)

I figuren ovanför varje filialkontor i ett globalt företag ansluter till närmaste möjliga Microsoft-kantplats via partnerns nätverk.

**Peering Service kundförmåner:**
* Bästa offentliga routing via internet till Microsoft Cloud Services för optimal prestanda och tillförlitlighet.
* Möjlighet att välja önskad SP för att ansluta till Microsoft Cloud.
* Trafikinsikter som svarstidsrapportering och prefixövervakning.
* Optimala nätverkshopp (AS Hops) från Microsoft Cloud.
* Flödesanalys och statistik - Händelser[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)för BGP-vägavvikelser (läck-/kapningsavdirigering) och underoptimal routning.

## <a name="peering-service-partnership-requirements"></a>Peering Service-partnerskapskrav
* Anslutning till Microsoft Cloud på en plats närmast kunden. En partnertjänstleverantör dirigerar användartrafik till Microsofts fördel närmast användaren. På samma sätt dirigerar Microsoft trafik (med BGP-taggen) till den kantplats som ligger närmast användaren och SP levererar trafiken till användaren.
* Partnern kommer att upprätthålla hög tillgänglig, hög dataflöde och geouppsagbar anslutning med Microsoft Global Network.
* Partner kan använda sin befintliga peering för att stödja peering-tjänsten om den uppfyller kravet

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
Vanliga frågor och svar finns i [Peering Service - FAQ](service-faqs.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om kundförmåner med [Peering Service](https://docs.microsoft.com/azure/peering-service/).
* Lär dig mer om steg för att aktivera en direkt peering för peering-tjänst i [peering-tjänst-partnergenomgång.](walkthrough-peering-service-all.md)
