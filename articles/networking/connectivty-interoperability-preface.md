---
title: 'Driftskompatibilitet för ExpressRoute, plats-till-plats-VPN och VNet-Peering - testa installationen: Azure Serverdelsanslutning funktioner samverkan | Microsoft Docs'
description: Den här sidan innehåller en test-konfiguration som används för att analysera samverkan med funktioner för ExpressRoute, plats-till-plats VPN- och VNet-Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947354"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>Driftskompatibilitet för ExpressRoute, plats-till-plats VPN- och VNet-peering - inställningar
I den här artikeln ska vi identifiera en test-konfiguration som vi kan använda för att analysera hur olika funktioner mellan platser fungerar med varandra både på-kontrollplanet och dataplanet. Före diskutera test-setup vi kort titta på vad dessa olika Azure nätverksfunktioner betyder.

ExpressRoute: Med hjälp av ExpressRoute kan privat peering du ansluta direkt privata IP-adressutrymmena för ditt lokala nätverk till Azure VNet-distributioner.  Du kan använda ExpressRoute för att uppnå högre bandbredd och privat anslutning. Det finns många miljövänligaste partner för ExpressRoute, som erbjuder ExpressRoute-anslutning med serviceavtal. Läs mer om ExpressRoute och hur du konfigurerar det i [introduktion till ExpressRoute][ExpressRoute]

Plats-till-plats-VPN: Om du vill ansluta ett lokalt nätverk till Azure på ett säkert sätt via Internet eller via ExpressRoute, plats-till-plats (S2S) VPN-alternativet som är tillgängligt. Läs om hur du konfigurerar S2S VPN för att ansluta till Azure i [konfigurera VPN-Gateway][VPN]

VNet-peering: VNet-peering är tillgänglig för att upprätta en anslutning mellan virtuella nätverk (Vnet). Läs mer om VNet-peering i [självstudiekurs om VNet-Peering][VNet].

##<a name="test-setup"></a>Inställningar för

Diagrammet nedan illustrerar test-installationen.

[![1]][1]

Center typ av test-installationen är Hubbnätverk i Azure-Region 1. Den Hubbnätverk är ansluten till olika nätverk på följande sätt:

1.  Att ekrar Vnet via Vnet-peering. Virtuellt ekernätverk har fjärråtkomst till båda gatewayerna i hubben virtuella nätverk.
2.  Till grenen virtuellt nätverk via plats-till-plats-VPN. Anslutningen använder eBGP för att utbyta vägar.
3.  För plats-1 lokala nätverk via ExpressRoute privat peering som den primära sökvägen och plats-till-plats-VPN-anslutning som sökväg för säkerhetskopiering. I resten av det här dokumentet använder vi kan kalla den här ExpressRoute-krets som ExpressRoute1. Som standard ger redundant anslutning i ExpressRoute-kretsar för hög tillgänglighet. På ExpressRoute1 är sekundära CE routerns undergränssnittet mot den sekundära msee: N inaktiverad. Detta anges med en röd-linje över pilen dubbel linje i diagrammet ovan.
4.  För plats 2 lokala nätverk via en annan ExpressRoute privat peering. I resten av det här dokumentet använder vi kan kalla den här andra ExpressRoute-krets som ExpressRoute2.
5.  ExpressRoute1 ansluter också både Hubbnätverk och plats 1 lokalt till en fjärransluten virtuellt nätverk i Azure-Region 2.

## <a name="further-reading"></a>Ytterligare läsning

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Med ExpressRoute och plats-till-plats VPN-anslutning tillsammans

#### <a name="site-to-site-vpn-over-expressroute"></a>Plats-till-plats-VPN över ExpressRoute 

Plats-till-plats-VPN kan konfigureras via ExpressRoute Microsoft-peering för att privat utbyta data mellan ditt lokala nätverk och ditt virtuella Azure-nätverk med konfidentialitet, anti repetitionsattacker, äkthetsbeviset och integritet. Mer information om hur du konfigurerar plats-till-plats-IPSec VPN i tunnelläge via ExpressRoute Microsoft-peering finns i [plats-till-plats-VPN över ExpressRoute Microsoft-peering][S2S-Over-ExR]. 

Den viktigaste begränsningen med att konfigurera S2S VPN över Microsoft-peering är dataflödet. Genomströmning via IPSec-tunneln begränsas av VPN-GW-kapacitet. VPN-GW dataflödet är mindre jämfört med ExpressRoute dataflöde. I sådana scenarier skulle med hjälp av IPSec-tunneln för hög belastning på säker och privat peering för alla andra trafik att optimera bandbreddsanvändningen för ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Plats-till-plats VPN som en säker redundanssökväg för ExpressRoute
ExpressRoute erbjuds som redundant krets par att säkerställa hög tillgänglighet. Du kan konfigurera geo-redundant ExpressRoute-anslutningen i olika Azure-regioner. Också som görs i vår test-installationsprogrammet, inom en viss Azure-region kan om du vill ha en redundanssökväg för ExpressRoute-anslutningen du göra det med hjälp av plats-till-plats VPN. När samma prefix har annonserats via både ExpressRoute och S2S VPN, föredrar Azure ExpressRoute via S2S VPN. Om du vill undvika asymmetrisk routning mellan ExpressRoute och S2S VPN, lokala nätverkskonfigurationen bör även öka exponeringen föredra ExpressRoute via S2S VPN-anslutning.

Läs mer om hur du konfigurerar ExpressRoute och plats-till-plats VPN-anslutningar för samexistens mellan [ExpressRoute och plats-till-plats samexistens][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Utöka serverdelen anslutningar till virtuella ekernätverk och olika kontor

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Ekrar VNet-anslutning med VNet-peering

Arkitektur för NAV-och-eker-virtuellt nätverk används ofta. Hubben är ett virtuellt nätverk (VNet) i Azure som fungerar som en central plats för anslutning mellan din virtuella ekernätverken och till ditt lokala nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben och kan användas för att isolera arbetsbelastningar. Trafiken flödar mellan det lokala datacentret och hubben via en ExpressRoute eller VPN-anslutning. Mer information om arkitekturen finns i [NAV och eker-arkitektur][Hub-n-Spoke]

VNet-peering inom en region kan virtuella använda hub VNet-gateway (både VPN och ExpressRoute-gatewayer) att kommunicera med fjärrnätverk ekernätverken.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Branch-VNet-anslutning med VPN för plats-till-plats

Om du vill gren virtuella nätverk (i olika regioner) och lokala nätverk som kommunicerar med varandra via en hubbnätverk, är den interna Azure-lösningen med hjälp av VPN för plats-till-plats VPN-anslutning. Ett alternativ är att använda en NVA för routning i hubben.

Konfigurera VPN-gatewayer finns i [konfigurera VPN-Gateway][VPN]. Distribuera högtillgängliga NVA finns i [distribuera högtillgängliga NVA][Deploy-NVA].

## <a name="next-steps"></a>Nästa steg

Konfigurationsinformationen för test-topologi, se [konfigurationsinformation][Configuration].

Kontrollen plan analys av test-installationen och för att förstå vyer för olika virtuella nätverk och VLAN topologins, se [kontrollplanet Analysis][Control-Analysis].

Plan för dataanalys av test-installationen och Azure-nätverk övervakningsvyer funktioner finns i avsnittet [Dataplanet Analysis][Data-Analysis].

Lär dig hur många ExpressRoute-kretsar som du kan ansluta till en ExpressRoute-Gateway, eller hur många ExpressRoute-gatewayer kan du ansluta till en ExpressRoute-krets, eller Läs andra gränser för skalning av ExpressRoute, i avsnittet [ExpressRoute vanliga frågor och svar][ExR-FAQ]



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Test-topologi"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha




