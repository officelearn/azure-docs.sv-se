---
title: 'Interoperabilitet i Azure : Testinställning | Microsoft-dokument'
description: I den här artikeln beskrivs en testkonfiguration som du kan använda för att analysera interoperabilitet mellan ExpressRoute, en VPN från plats till plats och virtuell nätverks peering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 3aec41a145d2c94a45a453393831902069b9c41b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518195"
---
# <a name="interoperability-in-azure--test-setup"></a>Interoperabilitet i Azure : Testinstallation

I den här artikeln beskrivs en testkonfiguration som du kan använda för att analysera hur Azure-nätverkstjänster samverkar på kontrollplansnivå och dataplansnivå. Låt oss titta kort på Azure-nätverkskomponenter:

-   **Azure ExpressRoute**: Använd privat peering i Azure ExpressRoute för att direkt ansluta privata IP-utrymmen i ditt lokala nätverk till dina Azure Virtual Network-distributioner. ExpressRoute kan hjälpa dig att uppnå högre bandbredd och en privat anslutning. Många ExpressRoute eco-partners erbjuder ExpressRoute-anslutning med SLA.Many ExpressRoute eco partners offer ExpressRoute connectivity with SLAs. Mer information om ExpressRoute och hur du konfigurerar ExpressRoute finns i [Introduktion till ExpressRoute][ExpressRoute].
-   **Plats-till-plats-VPN:** Du kan använda Azure VPN Gateway som en plats-till-plats-VPN för att säkert ansluta ett lokalt nätverk till Azure via Internet eller med ExpressRoute. Mer information om hur du konfigurerar en plats-till-plats-VPN för att ansluta till Azure finns i [Konfigurera VPN Gateway][VPN].
-   **VNet-peering:** Använd virtuell nätverks-peering (Virtual Network) för att upprätta anslutning mellan virtuella nätverk i Azure Virtual Network. Mer information om VNet-peering finns i [självstudien om VNet-peering][VNet].

## <a name="test-setup"></a>Testinställning

Följande bild illustrerar testinställningarna:

![1][1]

Mittpunkten för testkonfigurationen är navet VNet i Azure Region 1. Navet VNet är anslutet till olika nätverk på följande sätt:

-   Hub-nätverket är anslutet till det ekrarna VNet med hjälp av VNet-peering. Det eker-virtuella nätverket har fjärråtkomst till båda gatewayerna i navet VNet.
-   Hub-nätverket är anslutet till grenens virtuella nätverk med hjälp av plats-till-plats-VPN. Anslutningen använder eBGP för att utbyta vägar.
-   HubVNet är anslutet till det lokala plats 1-nätverket med hjälp av ExpressRoute-privat peering som primär sökväg. Den använder plats-till-plats VPN-anslutning som backup sökväg. I resten av den här artikeln hänvisar vi till denna ExpressRoute-krets som ExpressRoute 1. Som standard ger ExpressRoute-kretsar redundant anslutning för hög tillgänglighet. På ExpressRoute 1 inaktiveras den sekundära e-gränsen (CE) routerns undergränssnitt som är vänt mot den sekundära MSEE-routern (Microsoft Enterprise Edge Router). En röd linje över dubbellinjepilen i föregående bild representerar det inaktiverade CE-routerns undergränssnitt.
-   Hub-nätverket är anslutet till det lokala plats 2-nätverket med hjälp av en annan ExpressRoute-privat peering. I resten av den här artikeln hänvisar vi till denna andra ExpressRoute-krets som ExpressRoute 2.
-   ExpressRoute 1 ansluter också både hubvnet och det lokala plats 1-nätverket till ett fjärr-virtuella nätverk i Azure Region 2.

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

Läs mer om [konfigurationsinformation][Configuration] för testtopologin.

Lär dig mer om [kontrollplansanalys][Control-Analysis] av testinställningarna och vyerna för olika virtuella nätverk eller VLAN i topologin.

Lär dig mer om [dataplananalys][Data-Analysis] av testkonfigurationen och Azure-nätverksövervakningsfunktionsvyer.

Se [Vanliga frågor och svar om ExpressRoute][ExR-FAQ] för att:
-   Läs om hur många ExpressRoute-kretsar du kan ansluta till en ExpressRoute-gateway.
-   Läs om hur många ExpressRoute-gateways du kan ansluta till en ExpressRoute-krets.
-   Läs mer om andra skalbegränsningar för ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagram över testtopologin"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


