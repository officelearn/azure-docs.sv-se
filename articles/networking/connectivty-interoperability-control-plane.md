---
title: 'Samverkan i Azure backend-anslutningsfunktionerna: Kontrollera plan analys | Microsoft Docs'
description: Den här artikeln ger kontroll plan analys av de inställningar du kan använda för att analysera samverkan mellan ExpressRoute, en plats-till-plats-VPN och virtuell nätverkspeering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 37f5399426bebd375200bbc18dae7ed83f4fde3f
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614692"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Samverkan i Azure backend-anslutningsfunktionerna: Kontrollera plan analys

Den här artikeln beskriver kontrollen plan analys av den [testa installationen][Setup]. Du kan också granska den [test installationskonfiguration] [ Configuration] och [data analysis-dataplaner] [ Data-Analysis] av test-installationen.

Kontrollen plan analysis undersöker i stort sett vägar som utbyts mellan nätverk i en topologi. Kontrollen plan analys kan hjälpa dig att förstå hur olika nätverk visa topologi.

## <a name="hub-and-spoke-vnet-perspective"></a>NAV och ekrar VNet-perspektiv

Följande bild visar nätverket ur en hubbens virtuella nätverk (VNet) och en eker virtuellt nätverk (markerat i blått). Figuren visar också autonoma systemnummer (ASN) olika nätverk och vägar som utbyts mellan olika nätverk: 

[![1]][1]

ASN-NUMRET för det virtuella nätverkets Azure ExpressRoute-gateway skiljer sig från den ASN för Microsoft Enterprise-gränsroutrar (msee). En ExpressRoute-gateway använder ett privat ASN-nummer (värdet **65515**) och msee använder offentliga ASN (värdet **12076**) globalt. När du konfigurerar ExpressRoute-peering, eftersom msee: N är peer-datorn kan du använda **12076** som peer-ASN. På Azure-sidan upprättar msee: N eBGP-peering med ExpressRoute-gateway. Den dubbla eBGP-peering som den msee: N fastställer för varje ExpressRoute-peering är transparent på nivån kontroll plan. När du visar en ExpressRoute-routningstabell, se därför det virtuella nätverkets ExpressRoute-gateway ASN-NUMRET för det virtuella nätverkets prefix. 

Följande bild visar en routningstabell för exemplet ExpressRoute: 

[![5]][5]

I Azure är ASN betydande bara ur en peering. ASN för både ExpressRoute-gatewayen och VPN-gatewayen i Azure VPN Gateway är som standard **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>En lokal plats 1 och det fjärranslutna VNet perspektivet via ExpressRoute 1

Både en lokal plats 1 och virtuella fjärrnätverket är anslutna till det virtuella hubbnätverket via ExpressRoute 1. De delar samma perspektiv topologi, enligt följande diagram:

[![2]][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Lokal plats 1 och grenen VNet perspektiv via ett plats-till-plats-VPN

Både en lokal plats 1 och grenen virtuellt nätverk är anslutna till en Hubbnätverk VPN-gatewayen via en plats-till-plats VPN-anslutning. De delar samma perspektiv topologi, enligt följande diagram:

[![3]][3]

## <a name="on-premises-location-2-perspective"></a>En lokal plats 2 perspektiv

Lokal plats 2 är ansluten till en hubbnätverket via privat peering av ExpressRoute-2: 

[![4]][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute och plats-till-plats VPN-anslutning tillsammans

###  <a name="site-to-site-vpn-over-expressroute"></a>Plats-till-plats-VPN över ExpressRoute

Du kan konfigurera en plats-till-plats VPN med hjälp av ExpressRoute-Microsoft-peering för att privat utbyta data mellan ditt lokala nätverk och ditt virtuella Azure-nätverk. Du kan utbyta data med sekretess, äkthetsbeviset och integritet med den här konfigurationen. Datautbytet är också ett repetitionsattacker. Mer information om hur du konfigurerar en plats-till-plats IPsec VPN i tunnelläge med hjälp av ExpressRoute Microsoft-peering finns i [plats-till-plats-VPN över ExpressRoute Microsoft-peering][S2S-Over-ExR]. 

Den huvudsakliga begränsningen med att konfigurera en plats-till-plats VPN-anslutning som använder Microsoft-peering är genomströmning. Genomströmning via IPsec-tunneln begränsas av VPN gateway-kapaciteten. VPN gateway-genomströmning är lägre än ExpressRoute dataflöde. I det här scenariot använder IPsec-tunneln för mycket säker trafik och använder privata peering för all annan trafik hjälper till att optimera bandbreddsanvändningen för ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Plats-till-plats VPN som en säker redundanssökväg för ExpressRoute

ExpressRoute fungerar som ett redundant krets-par för att säkerställa hög tillgänglighet. Du kan konfigurera geo-redundant ExpressRoute-anslutningen i olika Azure-regioner. Som visas i vår test-installationsprogrammet, inom en Azure-region kan du också använda en plats-till-plats VPN-anslutning för att skapa en redundanssökväg för ExpressRoute-anslutning. När samma prefix har annonserats via både ExpressRoute och en plats-till-plats-VPN, prioriterar ExpressRoute i Azure. Om du vill undvika asymmetrisk routning mellan ExpressRoute och plats-till-plats-VPN, lokala nätverkskonfigurationen bör också att öka exponeringen med hjälp av ExpressRoute-anslutningen innan den använder plats-till-plats VPN-anslutning.

Mer information om hur du konfigurerar du samexisterande anslutningar för ExpressRoute och en plats-till-plats-VPN finns i [ExpressRoute och plats-till-plats samexistens][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Utöka backend-anslutning till virtuella ekernätverk och olika kontor

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Ekeranslutning virtuellt nätverk med hjälp av VNet-peering

NAV och ekrar VNet arkitektur används ofta. Hubben är ett virtuellt nätverk i Azure som fungerar som en central plats för anslutning mellan din virtuella ekernätverken och till ditt lokala nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben, och som du kan använda för att isolera arbetsbelastningar. Trafiken flödar mellan det lokala datacentret och hubben via en ExpressRoute eller VPN-anslutning. Mer information om arkitekturen finns i [implementerar en hub-spoke för nätverk i Azure][Hub-n-Spoke].

I VNet-peering inom en region, kan virtuella ekernätverk använda hub VNet-gatewayer (både VPN och ExpressRoute-gatewayer) att kommunicera med fjärrnätverk.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Gren VNet-anslutning via plats-till-plats-VPN

Du kanske vill gren virtuella nätverk som finns i olika regioner och lokala nätverk för att kommunicera med varandra via en hubbnätverket. Intern Azure-lösning för den här cofiguration är plats-till-plats VPN-anslutning med hjälp av en VPN-anslutning. Ett alternativ är att använda en virtuell nätverksinstallation (NVA) för routning i hubben.

Mer information finns i [vad är VPN-Gateway?] [ VPN] och [distribuera en högtillgänglig NVA][Deploy-NVA].

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [data analysis-dataplaner] [ Data-Analysis] av inställningar och Azure-nätverk övervakningsvyer för funktionen.

Se den [ExpressRoute vanliga frågor och svar] [ ExR-FAQ] till:
-   Lär dig hur många ExpressRoute-kretsar som du kan ansluta till en ExpressRoute-gateway.
-   Lär dig hur många ExpressRoute-gatewayer som du kan ansluta till en ExpressRoute-krets.
-   Läs mer om andra gränser för skalning av ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "NAV och ekrar VNet perspektiv topologin"
[2]: ./media/backend-interoperability/Loc1ExRView.png "plats 1 och den fjärranslutna VNet perspektiv topologins via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "plats 1 och grenen VNet perspektiv topologin via ett plats-till-plats-VPN"
[4]: ./media/backend-interoperability/Loc2View.png "plats 2 perspektiv topologin"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 routningstabell"

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


