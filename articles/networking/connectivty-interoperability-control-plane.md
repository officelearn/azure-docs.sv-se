---
title: 'Driftskompatibilitet för ExpressRoute, plats-till-plats-VPN och VNet-Peering - styra plan Analysis: Azure Serverdelsanslutning funktioner samverkan | Microsoft Docs'
description: Den här sidan ger kontroll plan analys av test-inställningarna som har angetts för att analysera samverkan med funktioner för ExpressRoute, plats-till-plats VPN- och VNet-Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947360"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>Driftskompatibilitet för ExpressRoute, plats-till-plats VPN- och VNet-Peering - kontroll plan analys

I den här artikeln ska vi gå igenom kontroll plan analysen av test-installationen. Om du vill granska Test-installationen finns i den [inställningar][Setup]. Konfigurationsdetaljer för inställningar finns i [Test installationskonfiguration][Configuration].

Kontrollen plan analysis undersöker i stort sett vägar som utbyts mellan nätverk i en topologi. Kontrollera plan analysis hjälp hur olika nätverk visa topologin.

##<a name="hub-and-spoke-vnet-perspective"></a>NAV och ekrar VNet perspektiv

Följande diagram illustrerar nätverket från Hubbnätverk och ekrar virtuellt nätverk (markerat i blått) perspektiv. Diagrammet visar också autonoma System tal (ASN) för olika nätverk och vägar som utbyts mellan olika nätverk. 

[![1]][1]

Observera att det ASN för virtuella nätverkets ExpressRoute-gateway skiljer sig från den ASN för Microsoft Enterprise-gränsroutrar (msee). ExpressRoute-gateway använder ett privat ASN-nummer (65515) och msee använder offentliga ASN (12076) globalt. När du konfigurerar ExpressRoute-peering, eftersom msee: N är peer-datorn kan använda du 12076 som peer-ASN. På Azure-sidan upprättar msee: N eBGP-peering med ExpressRoute GW. Den dubbla eBGP-peering som den msee: N fastställer för varje ExpressRoute-peering är transparent på kontrollplanet nivå. Därför när en ExpressRoute-routningstabellen visas kan se du det virtuella nätverket ExpressRoute GW ASN för det virtuella nätverkets prefix. En exempel ExpressRoute väg tabell skärmbild visas nedan: 

[![5]][5]

I Azure är endast ASN betydande från en peering perspektiv. Som standard är ASN för både GW för ExpressRoute och VPN-GW 65515.

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>En lokal plats-1 och virtuella fjärrnätverket perspektiv via ExpressRoute-1

En lokal plats-1 och virtuella fjärrnätverket är anslutna till Hubbnätverk via ExpressRoute 1 och därför de delar samma perspektiv topologi, enligt den nedanstående diagram.

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>En lokal plats-1 och grenen VNet perspektiv via plats-till-plats-VPN

En lokal plats-1 och grenen virtuellt nätverk är anslutna till hubben VNet VPN GW via plats-till-plats-VPN-anslutningar och därför de delar samma perspektiv topologi, enligt den nedanstående diagram.

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>En lokal plats-2-perspektiv

En lokal plats-2 är ansluten till Hubbnätverk via privat peering av ExpressRoute-2. 

[![4]][4]

## <a name="further-reading"></a>Ytterligare läsning

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Med ExpressRoute och plats-till-plats VPN-anslutning tillsammans

####  <a name="site-to-site-vpn-over-expressroute"></a>Plats-till-plats-VPN över ExpressRoute

Plats-till-plats-VPN kan konfigureras via ExpressRoute Microsoft-peering för att privat utbyta data mellan ditt lokala nätverk och ditt virtuella Azure-nätverk med konfidentialitet, anti repetitionsattacker, äkthetsbeviset och integritet. Mer information om hur du konfigurerar plats-till-plats-IPSec VPN i tunnelläge via ExpressRoute Microsoft-peering finns i [plats-till-plats-VPN över ExpressRoute Microsoft-peering][S2S-Over-ExR]. 

Den viktigaste begränsningen med att konfigurera S2S VPN över Microsoft-peering är dataflödet. Genomströmning via IPSec-tunneln begränsas av VPN-GW-kapacitet. VPN-GW dataflödet är mindre jämfört med ExpressRoute dataflöde. I sådana scenarier skulle med hjälp av IPSec-tunneln för hög belastning på säker och privat peering för alla andra trafik att optimera bandbreddsanvändningen för ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Plats-till-plats VPN som en säker redundanssökväg för ExpressRoute
ExpressRoute erbjuds som redundant krets par att säkerställa hög tillgänglighet. Du kan konfigurera geo-redundant ExpressRoute-anslutningen i olika Azure-regioner. Också som görs i vår test-installationsprogrammet, inom en viss Azure-region kan om du vill ha en redundanssökväg för ExpressRoute-anslutningen du göra det med hjälp av plats-till-plats VPN. När samma prefix har annonserats via både ExpressRoute och S2S VPN, föredrar Azure ExpressRoute via S2S VPN. Om du vill undvika asymmetrisk routning mellan ExpressRoute och S2S VPN, lokala nätverkskonfigurationen bör även öka exponeringen föredra ExpressRoute via S2S VPN-anslutning.

Läs mer om hur du konfigurerar ExpressRoute och plats-till-plats VPN-anslutningar för samexistens mellan [ExpressRoute och plats-till-plats samexistens][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Utöka serverdelen anslutningar till virtuella ekernätverk och olika kontor

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Ekrar VNet-anslutning med VNet-peering

Arkitektur för NAV-och-eker-virtuellt nätverk används ofta. Hubben är ett virtuellt nätverk (VNet) i Azure som fungerar som en central plats för anslutning mellan din virtuella ekernätverken och till ditt lokala nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben och kan användas för att isolera arbetsbelastningar. Trafiken flödar mellan det lokala datacentret och hubben via en ExpressRoute eller VPN-anslutning. Mer information om arkitekturen finns [NAV och eker-arkitektur][Hub-n-Spoke]

VNet-peering inom en region kan virtuella använda hub VNet-gateway (både VPN och ExpressRoute-gatewayer) att kommunicera med fjärrnätverk ekernätverken.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Branch-VNet-anslutning med VPN för plats-till-plats

Om du vill gren virtuella nätverk (i olika regioner) och lokala nätverk som kommunicerar med varandra via en hubbnätverk, är den interna Azure-lösningen med hjälp av VPN för plats-till-plats VPN-anslutning. Ett alternativ är att använda en NVA för routning i hubben.

Konfigurera VPN-gatewayer finns i [konfigurera VPN-Gateway][VPN]. Distribuera högtillgängliga NVA finns i [distribuera högtillgängliga NVA][Deploy-NVA].

## <a name="next-steps"></a>Nästa steg

Plan för dataanalys av test-installationen och Azure-nätverk övervakningsvyer funktioner finns i avsnittet [Dataplanet Analysis][Data-Analysis].

Lär dig hur många ExpressRoute-kretsar som du kan ansluta till en ExpressRoute-Gateway, eller hur många ExpressRoute-gatewayer kan du ansluta till en ExpressRoute-krets, eller Läs andra gränser för skalning av ExpressRoute, i avsnittet [ExpressRoute vanliga frågor och svar][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "NAV och ekrar VNet perspektiv topologin"
[2]: ./media/backend-interoperability/Loc1ExRView.png "plats 1 och den fjärranslutna VNet perspektiv via ExpressRoute 1 topologins"
[3]: ./media/backend-interoperability/Loc1VPNView.png "plats 1 och grenen VNet perspektiv via S2S VPN i topologin"
[4]: ./media/backend-interoperability/Loc2View.png "plats 2 perspektiv topologin"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 RouteTable"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




