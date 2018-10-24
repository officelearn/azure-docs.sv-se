---
title: 'Driftskompatibilitet för ExpressRoute, plats-till-plats-VPN och VNet-Peering - information om konfiguration: Azure Serverdelsanslutning funktioner samverkan | Microsoft Docs'
description: Den här sidan innehåller konfigurationsinformationen för de inställningar som används för att analysera samverkan med funktioner för ExpressRoute, plats-till-plats VPN- och VNet-Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947342"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>Driftskompatibilitet för ExpressRoute, plats-till-plats VPN- och VNet-Peering - information om konfiguration av Test

I den här artikeln ska vi gå igenom konfigurationsinformationen för test-installationen. Test-installation finns den [inställningar][Setup]. 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>Ekrar VNet-anslutning med VNet-peering

I följande skärmbild för Azure portal visas VNet-peering-information för virtuellt ekernätverk. Stegvisa anvisningar om hur du konfigurerar VNet-peering mellan två virtuella nätverk finns i [hantera VNet-Peering][VNet-Config]. Om du vill att ekrar VNet att använda gatewayer som är anslutna till hubben VNet, måste du kontrollera *Använd fjärrgateway*.

[![1]][1]

I följande skärmbild för Azure portal visas VNet-peering-information för det virtuella hubbnätverket. Om du vill att Hubbnätverk så att använda dess gateways ekrar VNet, måste du kontrollera *Använd fjärrgateway*.

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Branch-VNet-anslutning med VPN för plats-till-plats

Plats-till-plats VPN-anslutning mellan Hub och grenen virtuella nätverk har konfigurerats med hjälp av VPN-gatewayer. Som standard konfigureras VPN och ExpressRoute-gatewayer med värdet för privata ASN på 65515. VPN-gateway kan du ändra ASN-värdet. I test-installationsprogrammet, som visas i Azure portal skärmbilden ändras ASN-värdet för grenen VNet VPN-gateway till 65516 att aktivera eBGP routning mellan Hub och grenen virtuella nätverk.


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>Plats 1 lokal anslutning med ExpressRoute och VPN för plats-till-plats

###<a name="expressroute1-configuration-details"></a>Information om konfiguration av ExpressRoute1

Portalen skärmbilden nedan visar Azure-Region 1 ExpressRoute-krets konfigurationen för plats-1 lokala CE routrar.

[![4]][4]

Portalen skärmbilden nedan visar konfigurationen mellan ExpressRoute1 kretsen och Hub VNet.

[![5]][5]

Följande konfiguration är över den primära CE-routern (Cisco ASR1000 routrar som används som CE routrar i installationsprogrammet för test) konfiguration som är relaterade till ExpressRoute privat peering-anslutningen. När både plats-till-plats VPN och ExpressRoute-krets har konfigurerats parallellt för att ansluta ett lokalt nätverk till Azure, Azure föredrar ExpressRoute-krets som standard. För att undvika asymmetrisk routning kan ska lokalt nätverk också föredrar ExpressRoute via plats-till-plats-VPN för de vägar som tas emot både via ExpressRoute och VPN för plats-till-plats. Detta uppnås i följande konfiguration med hjälp av BGP-attribut för lokala inställningar. 

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

###<a name="site-to-site-vpn-configuration-details"></a>Information om plats-till-plats VPN-konfiguration

Följande är listor över de primära CE routerkonfiguration relaterade till plats-till-plats-VPN-anslutning:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>Plats 2 lokal anslutning med ExpressRoute

En andra ExpressRoute-kretsen i plats 2 lokalt, finns närmare ansluter plats 2 lokala platser till det virtuella hubbnätverket. Portalen skärmbilden nedan visar den andra ExpressRoute-konfigurationen.

[![6]][6]

Portalen skärmbilden nedan visar konfigurationen mellan andra ExpressRoute-kretsen och Hub VNet.

[![7]][7]

ExpressRoute1 ansluter både Hubbnätverk och plats 1 lokalt till en fjärransluten virtuellt nätverk i en annan Azure-region.

[![8]][8]

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

Kontrollen plan analys av test-installationen och för att förstå vyer för olika virtuella nätverk och VLAN topologins, se [kontrollplanet Analysis][Control-Analysis].

Plan för dataanalys av test-installationen och Azure-nätverk övervakningsvyer funktioner finns i avsnittet [Dataplanet Analysis][Data-Analysis].

Lär dig hur många ExpressRoute-kretsar som du kan ansluta till en ExpressRoute-Gateway, eller hur många ExpressRoute-gatewayer kan du ansluta till en ExpressRoute-krets, eller Läs andra gränser för skalning av ExpressRoute, i avsnittet [ExpressRoute vanliga frågor och svar][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "ekrar VNet VNet-peering"
[2]: ./media/backend-interoperability/HubVNet-peering.png "hub VNet VNet-peering"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN GW-konfiguration för grenen VNet"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute1 konfiguration"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "anslutningskonfiguration av ExpressRoute1 till hubben VNet ExR GW"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute2 konfiguration"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "anslutningskonfiguration av ExpressRoute2 till hubben VNet ExR GW"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "anslutningskonfiguration av ExpressRoute2 till fjärranslutna virtuella nätverket ExR GW"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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




