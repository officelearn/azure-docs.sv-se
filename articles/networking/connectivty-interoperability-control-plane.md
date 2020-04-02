---
title: 'Interoperabilitet i Azure : Kontrollplananalys'
description: Den här artikeln innehåller kontrollplansanalys av testkonfigurationen som du kan använda för att analysera interoperabilitet mellan ExpressRoute, en VPN från plats till plats och virtuell nätverks peering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 5e41bc86533815c394077bf5276d930fe958cd19
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518278"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabilitet i Azure : Kontrollplananalys

I den här artikeln beskrivs kontrollplansanalysen av [testinställningen][Setup]. Du kan också granska [testinställningskonfigurationen][Configuration] och [dataplananalysen][Data-Analysis] av testkonfigurationen.

Kontrollplan analys undersöker i huvudsak rutter som utbyts mellan nätverk inom en topologi. Kontrollplansanalys kan hjälpa dig att förstå hur olika nätverk visar topologin.

## <a name="hub-and-spoke-vnet-perspective"></a>Hub och eker VNet perspektiv

Följande bild illustrerar nätverket ur ett virtuellt navnätverk (VNet) och ett eker-virtuellt nätverk (markerat i blått). Figuren visar också det autonoma systemnumret (ASN) för olika nät och rutter som utbyts mellan olika nät: 

![1][1]

ASN för VNet:s Azure ExpressRoute-gateway skiljer sig från ASN för Microsoft Enterprise Edge-routrar (MSEEs). En ExpressRoute-gateway använder ett privat ASN (ett värde på **65515**) och MSEE använder offentliga ASN (ett värde på **12076)** globalt. När du konfigurerar ExpressRoute-peering, eftersom MSEE är peer, använder du **12076** som peer ASN. På Azure-sidan upprättar MSEE eBGP-peering med ExpressRoute-gatewayen. Den dubbla eBGP-peering som MSEE upprättar för varje ExpressRoute-peering är transparent på kontrollplansnivå. När du visar en ExpressRoute-vägtabell visas därför VNets ExpressRoute gateway ASN för VNet:s prefix. 

Följande bild visar en exempeltabell för ExpressRoute-rutt: 

![5][5]

Inom Azure är ASN endast betydande ur ett peering-perspektiv. Som standard är ASN för både ExpressRoute-gatewayen och VPN-gatewayen i Azure VPN Gateway **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Lokalt plats 1 och det avlägsna virtuella nätverkets perspektiv via ExpressRoute 1

Både lokal plats 1 och fjärr-VNet är anslutna till navet VNet via ExpressRoute 1. De delar samma perspektiv på topologin, som visas i följande diagram:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Lokalt plats 1 och grenens virtuella nätverksperspektiv via en plats-till-plats-VPN

Både lokal plats 1 och grenen virtuella nätverk är anslutna till ett nav VNet VPN-gateway via en plats-till-plats VPN-anslutning. De delar samma perspektiv på topologin, som visas i följande diagram:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Lokalt läge 2 perspektiv

Lokal plats 2 är ansluten till ett nav-VNet via privat peering av ExpressRoute 2: 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute och plats-till-plats VPN-anslutning i tandem

###  <a name="site-to-site-vpn-over-expressroute"></a>Plats-till-plats VPN via ExpressRoute

Du kan konfigurera en plats-till-plats-VPN genom att använda ExpressRoute Microsoft-peering för att privat utbyta data mellan ditt lokala nätverk och dina Virtuella Azure-nätverk. Med den här konfigurationen kan du utbyta data med sekretess, äkthet och integritet. Datautbytet är också anti-repris. Mer information om hur du konfigurerar en IPsec-VPN från plats till plats i tunnelläge med Hjälp av ExpressRoute Microsoft-peering finns i [Plats-till-plats VPN via ExpressRoute Microsoft-peering][S2S-Over-ExR]. 

Den primära begränsningen av att konfigurera ett PLATS-till-plats-VPN som använder Microsoft-peering är dataflöde. Dataflöde över IPsec-tunneln begränsas av VPN-gatewaykapaciteten. DATAFLÖDET FÖR VPN-gateway är lägre än ExpressRoute-dataflödet. I det här fallet, med hjälp av IPsec tunneln för mycket säker trafik och använda privat peering för all annan trafik hjälper till att optimera ExpressRoute bandbredd utnyttjande.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Plats-till-plats VPN som en säker redundanssökväg för ExpressRoute

ExpressRoute fungerar som ett redundant kretspar för att säkerställa hög tillgänglighet. Du kan konfigurera geo-redundant ExpressRoute-anslutning i olika Azure-regioner. Dessutom, som visas i vår testkonfiguration, inom en Azure-region, kan du använda en plats-till-plats-VPN för att skapa en redundanssökväg för din ExpressRoute-anslutning. När samma prefix annonseras via både ExpressRoute och ett VPN från plats till plats prioriterar Azure ExpressRoute. För att undvika asymmetrisk routning mellan ExpressRoute och plats-till-plats-VPN bör lokal nätverkskonfiguration också återgäldas med hjälp av ExpressRoute-anslutning innan den använder VPN-anslutning från plats till plats.

Mer information om hur du konfigurerar samtidiga anslutningar för ExpressRoute och en plats-till-plats-VPN finns i [ExpressRoute och samtidig samexistens][ExR-S2S-CoEx]mellan plats och plats till plats .

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Utöka backend-anslutning till eker-virtuella nätverk och filialplatser

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Eker-VNet-anslutning med hjälp av VNet-peering

Hub och eker VNet arkitektur används i stor utsträckning. Navet är ett virtuella nätverk i Azure som fungerar som en central anslutningspunkt mellan dina eker-virtuella nätverk och till ditt lokala nätverk. Ekrarna är virtuella nätverk som peer med hubben och som du kan använda för att isolera arbetsbelastningar. Trafiken flödar mellan det lokala datacentret och navet via en ExpressRoute- eller VPN-anslutning. Mer information om arkitekturen finns i [Implementera en hub-spoke-nätverkstopologi i Azure][Hub-n-Spoke].

I VNet-peering inom en region kan eker-virtuella nätverk använda hubVNet-gateways (både VPN- och ExpressRoute-gateways) för att kommunicera med fjärrnätverk.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Gren VNet-anslutning med hjälp av plats-till-plats-VPN

Du kanske vill att virtuella filialer, som finns i olika regioner, och lokala nätverk ska kommunicera med varandra via ett nav-virtuella nätverk. Den inbyggda Azure-lösningen för den här konfigurationen är VPN-anslutning från plats till plats med hjälp av en VPN. Ett alternativ är att använda en virtuell nätverksinstallation (NVA) för routning i navet.

Mer information finns i Vad är [Deploy a highly available NVA][Deploy-NVA] [VPN Gateway?][VPN]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om analys av [testplan][Data-Analysis] av testkonfigurationen och Azure-nätverksövervakningsfunktionsvyer.

Se [Vanliga frågor och svar om ExpressRoute][ExR-FAQ] för att:
-   Läs om hur många ExpressRoute-kretsar du kan ansluta till en ExpressRoute-gateway.
-   Läs om hur många ExpressRoute-gateways du kan ansluta till en ExpressRoute-krets.
-   Läs mer om andra skalbegränsningar för ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Hub och talade VNet perspektiv av topologin"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Plats 1 och fjärr-VNet perspektiv av topologin via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Plats 1 och gren VNet perspektiv av topologin via en plats-till-plats VPN"
[4]: ./media/backend-interoperability/Loc2View.png "Plats 2 perspektiv av topologin"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 rutttabell"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


