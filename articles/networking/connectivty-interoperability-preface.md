---
title: 'Samverkan i Azure backend-anslutningsfunktionerna: Testa installationen | Microsoft Docs'
description: Den här artikeln beskrivs en test-konfiguration som du kan använda för att analysera samverkan mellan ExpressRoute, en plats-till-plats-VPN och virtuell nätverkspeering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: b090633ee33582a235a92ab7100741e0be48cb27
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200164"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Samverkan i Azure backend-anslutningsfunktionerna: Inställningar för

Den här artikeln beskrivs en test-konfiguration som du kan använda för att analysera hur Azure nätverkstjänster interagera på den plan kontrollnivå och data plan nivå. Låt oss titta kort på Azure nätverkskomponenter:

-   **Azure ExpressRoute**: Använda privata peering i Azure ExpressRoute för att ansluta direkt privata IP-adressutrymmen i ditt lokala nätverk till Azure Virtual Network-distributioner. Med hjälp av ExpressRoute kan du uppnå högre bandbredd och en privat anslutning. Många ExpressRoute miljövänligaste partner erbjuder ExpressRoute-anslutning med serviceavtal. Mer information om ExpressRoute och för att lära dig hur du konfigurerar ExpressRoute finns i [introduktion till ExpressRoute][ExpressRoute].
-   **Plats-till-plats VPN**: Du kan använda Azure VPN-Gateway som en plats-till-plats-VPN för att på ett säkert sätt ansluta ett lokalt nätverk till Azure via internet eller genom att använda ExpressRoute. Läs hur du konfigurerar en plats-till-plats VPN-anslutning för att ansluta till Azure i [konfigurera VPN-Gateway][VPN].
-   **VNet-peering**: Använd peering av virtuella nätverk (VNet) för att upprätta en anslutning mellan virtuella nätverk i Azure-nätverk. Läs mer om VNet-peering i den [självstudiekurs som VNet-peering][VNet].

## <a name="test-setup"></a>Inställningar för

Följande bild visar test-installationen:

[![1]][1]

Mittpunkten av test-installationen är det virtuella hubbnätverket i Azure-Region 1. Det virtuella hubbnätverket är ansluten till olika nätverk på följande sätt:

-   Det virtuella hubbnätverket är ansluten till virtuellt ekernätverk med hjälp av VNet-peering. Virtuellt ekernätverk har fjärråtkomst till båda gatewayerna i det virtuella hubbnätverket.
-   Det virtuella hubbnätverket är ansluten till grenen virtuellt nätverk med hjälp av plats-till-plats-VPN. Anslutningen använder eBGP för att utbyta vägar.
-   Det virtuella hubbnätverket är ansluten till en lokal plats 1 nätverk med hjälp av ExpressRoute privat peering som den primära sökvägen. Plats-till-plats VPN-anslutning används som den säkerhetskopiera sökvägen. I resten av den här artikeln använder refererar vi till den här ExpressRoute-krets som ExpressRoute 1. Som standard anger ExpressRoute-kretsar redundant anslutning för hög tillgänglighet. Sekundär (CE) gränsrouters undergränssnittet som riktas mot den sekundära Microsoft Enterprise Edge Router (msee: N) är inaktiverat på ExpressRoute-1. En röd linje över dubbel pilen i bilden ovan representerar inaktiverad CE router undergränssnittet.
-   Det virtuella hubbnätverket är ansluten till en lokal plats 2 nätverk med hjälp av en annan ExpressRoute privat peering. I resten av den här artikeln använder refererar vi till den här andra ExpressRoute-krets som ExpressRoute 2.
-   ExpressRoute 1 ansluter också både det virtuella hubbnätverket och en lokal plats 1 nätverket till en fjärransluten virtuellt nätverk i Azure-Region 2.

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

Lär dig mer om [konfigurationsinformation] [ Configuration] för test-topologi.

Lär dig mer om [styra plan analysis] [ Control-Analysis] test-installationen och vyer för olika virtuella nätverk eller VLAN i topologin.

Lär dig mer om den [data analysis-dataplaner] [ Data-Analysis] av inställningar och Azure-nätverk övervakningsvyer för funktionen.

Se den [ExpressRoute vanliga frågor och svar] [ ExR-FAQ] till:
-   Lär dig hur många ExpressRoute-kretsar som du kan ansluta till en ExpressRoute-gateway.
-   Lär dig hur många ExpressRoute-gatewayer som du kan ansluta till en ExpressRoute-krets.
-   Läs mer om andra gränser för skalning av ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "diagram över topologi för testning"

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


