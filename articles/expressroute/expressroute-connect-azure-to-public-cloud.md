---
title: Ansluta Azure till offentliga moln | Microsoft-dokument
description: Beskriv olika sätt att ansluta Azure till andra offentliga moln
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889631"
---
# <a name="connecting-azure-with-public-clouds"></a>Ansluta Azure med offentliga moln

Många företag följer en strategi med flera moln på grund av affärsmål och tekniska mål. Dessa inkluderar kostnad, flexibilitet, funktionstillgänglighet, redundans, datasuveränitet etc. Denna strategi hjälper dem att utnyttja det bästa av båda molnen. 

Detta tillvägagångssätt innebär också utmaningar för företaget när det gäller nätverks- och programarkitektur. Några av dessa utmaningar är latens och datadataflöde. För att ta itu med dessa utmaningar kunder vill ansluta till flera moln direkt. Vissa tjänsteleverantörer tillhandahåller en lösning för att ansluta flera molnleverantörer för kunderna. I andra fall kan kunden distribuera sin egen router för att ansluta flera offentliga moln.
## <a name="connectivity-via-expressroute"></a>Anslutning via ExpressRoute
ExpressRoute låter kunderna utöka sina lokala nätverk till Microsoft-molnet via en privat anslutning som underlättas av en anslutningsleverantör. Med ExpressRoute kan kunder upprätta anslutningar till Microsofts molntjänster.

Det finns tre sätt att ansluta via ExpressRoute.

1. Layer3-leverantör
2. Layer2-leverantör
3. Direkt anslutning

### <a name="layer3-provider"></a>Layer3-provider

Layer3-leverantörer är allmänt kända som IP VPN- eller MPLS VPN-leverantörer. Kunder utnyttjar dessa leverantörer för multipointanslutning mellan sina datacenter, grenar och molnet. Kunder ansluter till L3-leverantören via BGP eller via statisk standardväg. Tjänsteleverantören annonserar vägar mellan kundwebbplatser, datacenter och offentligt moln. 
 
När du ansluter via Layer3-leverantören annonserar Microsoft vnet-rutter för kunder till tjänsteleverantören via BGP. Leverantören kan ha två olika implementeringar.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Leverantören kan landa varje molnleverantör i en separat VRF, om trafiken från alla molnleverantörer kommer att nå på kundens router. Om kunden kör BGP med tjänsteleverantör kommer dessa vägar att annonseras om till andra molnleverantörer som standard. 

Om tjänsteleverantören landar alla molnleverantörer i samma VRF annonseras rutter till andra molnleverantörer från tjänsteleverantören direkt. Detta förutsätter standard BGP operation där eBGP rutter annonseras till andra eBGP grannar som standard.

Varje offentligt moln har olika prefix gräns så samtidigt som distribution av vägar tjänsteleverantören bör vara försiktig med att distribuera rutter.

### <a name="layer2-provider-and-direct-connection"></a>Layer2-provider och direktanslutning

Även om fysisk anslutning i båda modellerna är olika, men vid layer3 BGP upprättas direkt mellan MSEE och kunden routern. För ExpressRoute Direct-kund ansluter till MSEE direkt. När det gäller Layer2 utökar tjänsteleverantören VLAN från kundlokal till molnet. Kunder kör BGP ovanpå layer2-nätverket för att ansluta sina DC till molnet.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
I båda fallen har kunden punkt-till-punkt-anslutningar till vart och ett av de offentliga molnen. Kunden upprättar separat BGP-anslutning till varje offentligt moln. Rutter som tas emot av en molnleverantör annonseras till andra molnleverantörer som standard. Varje molnleverantör har olika prefix gräns så medan annonsera de rutter kunden bör ta hand om dessa gränser. Kunden kan använda vanliga BGP-rattar med Microsoft medan annonsering vägar från andra offentliga moln.

## <a name="direct-connection-with-expressroute"></a>Direkt anslutning med ExpressRoute

Kunder kan välja att ansluta ExpressRoute direkt till molnleverantörens direktanslutningserbjudande. Två molnleverantörer kommer att anslutas tillbaka till rygg och BGP kommer att upprättas direkt mellan sina routrar. Denna typ av anslutning är tillgänglig med Oracle idag.

## <a name="site-to-site-vpn"></a>Plats-till-plats-VPN

Kunder kan utnyttja Internet för att ansluta sina instanser i Azure med andra offentliga moln. Nästan alla molnleverantörer erbjuder VPN-funktioner från plats till plats. Det kan dock finnas inkompatibiliteter på grund av brist på vissa varianter. Vissa molnleverantörer stöder till exempel bara IKEv1 så det krävs en slutpunkt för VPN-avslutning i molnet. För de molnleverantörer som stöder IKEv2 kan en direkt tunnel upprättas mellan VPN-gateways hos båda molnleverantörerna.

Site-to-site VPN anses inte vara en lösning med högt dataflöde och låg latens. Den kan dock användas som en säkerhetskopia till fysisk anslutning.

## <a name="next-steps"></a>Nästa steg
Se Vanliga frågor och svar om [ExpressRoute][ER-FAQ] för ytterligare frågor om ExpressRoute och virtuell nätverksanslutning.

Se [Konfigurera direktanslutning mellan Azure och Oracle Cloud][ER-OCI] för anslutning mellan Azure och Oracle

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



