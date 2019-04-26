---
title: 'Samverkan i Azure backend-anslutningsfunktionerna: Information om | Microsoft Docs'
description: Den här artikeln beskriver konfigurationsdetaljer för de inställningar du kan använda för att analysera samverkan mellan ExpressRoute, en plats-till-plats-VPN och virtuell nätverkspeering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 2ceb4aeac55bd555a41c29bd41b00c771490e5f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60425799"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Samverkan i Azure backend-anslutningsfunktionerna: Testa konfigurationsinformation

Den här artikeln beskriver konfigurationsinformationen för de [testa installationen][Setup]. Testa installationen hjälper dig att analysera hur Azure nätverkstjänster interagera på den plan kontrollnivå och data plan nivå.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Ekeranslutning virtuellt nätverk med hjälp av VNet-peering

Följande bild visar Azure Virtual Network peering-information för ett virtuellt eker-nätverk (VNet). Läs hur du konfigurerar peering mellan två virtuella nätverk i [hantera VNet-peering][VNet-Config]. Om du vill att eker VNet att använda gateways som är anslutna till det virtuella hubbnätverket, Välj **Använd fjärrgateway**.

[![1]][1]

Följande bild visar VNet-peering-information för det virtuella hubbnätverket. Om du vill eker VNet att använda VNet-gateways hub väljer **Använd fjärrgateway**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Gren VNet-anslutning med hjälp av en plats-till-plats-VPN

Konfigurera plats-till-plats VPN-anslutningar mellan hub och grenen virtuella nätverk med hjälp av VPN-gatewayer i Azure VPN Gateway. Som standard VPN gateway och Azure ExpressRoute-gateway använder privata autonoma system tal (ASN) värdet **65515**. Du kan ändra ASN-värdet i VPN-Gateway. I test-installationsprogrammet ASN-värdet på förgrening VNet VPN-gateway har ändrats till **65516** som stöd för eBGP routning mellan hub och grenen virtuella nätverk.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Lokal plats 1 anslutning med hjälp av ExpressRoute och en plats-till-plats-VPN

### <a name="expressroute-1-configuration-details"></a>Information om konfiguration av ExpressRoute 1

Följande bild visar Azure-Region 1 ExpressRoute-krets konfigurationen mot en lokal plats 1 (CE) gränsroutrar:

[![4]][4]

Följande bild visar konfigurationen mellan 1 för ExpressRoute-kretsen och det virtuella hubbnätverket:

[![5]][5]

I följande lista visas den primära CE router-konfigurationen för ExpressRoute privat peering-anslutningen. (Cisco-routrar ASR1000 används som CE routrar i installationsprogrammet för test). När plats-till-plats VPN och ExpressRoute-kretsar har konfigurerats parallellt för att ansluta ett lokalt nätverk till Azure, prioriterar Azure ExpressRoute-krets som standard. För att undvika asymmetrisk routning kan ska det lokala nätverket även gälla i ExpressRoute-anslutningen via plats-till-plats VPN-anslutning. Följande konfiguration upprättar prioritering med hjälp av BGP **till lokala inställningar** attribut:

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

### <a name="site-to-site-vpn-configuration-details"></a>Information om plats-till-plats VPN-konfiguration

I följande lista visas den primära CE router-konfigurationen för plats-till-plats VPN-anslutning:

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

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Lokal plats 2-anslutning med ExpressRoute

En andra ExpressRoute-kretsen i en lokal plats 2 finns närmare ansluter en lokal plats 2 till det virtuella hubbnätverket. Följande bild visar den andra ExpressRoute-konfigurationen:

[![6]][6]

Följande bild visar konfigurationen mellan andra ExpressRoute-kretsen och det virtuella hubbnätverket:

[![7]][7]

ExpressRoute 1 ansluter både virtuella hubbnätverket och en lokal plats 1 till en fjärransluten virtuellt nätverk i en annan Azure-region:

[![8]][8]

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

Du kanske vill gren virtuella nätverk som finns i olika regioner och lokala nätverk för att kommunicera med varandra via en hubbnätverket. Intern Azure-lösning för den här konfigurationen är plats-till-plats VPN-anslutning med hjälp av en VPN-anslutning. Ett alternativ är att använda en virtuell nätverksinstallation (NVA) för routning i hubben.

Mer information finns i [vad är VPN-Gateway?] [ VPN] och [distribuera en högtillgänglig NVA][Deploy-NVA].

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [styra plan analysis] [ Control-Analysis] test-installationen och vyer för olika virtuella nätverk eller VLAN i topologin.

Lär dig mer om [data analysis-dataplaner] [ Data-Analysis] av inställningar och Azure-nätverk övervakningsvyer för funktionen.

Se den [ExpressRoute vanliga frågor och svar] [ ExR-FAQ] till:
-   Lär dig hur många ExpressRoute-kretsar som du kan ansluta till en ExpressRoute-gateway.
-   Lär dig hur många ExpressRoute-gatewayer som du kan ansluta till en ExpressRoute-krets.
-   Läs mer om andra gränser för skalning av ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "ekrar VNet VNet-peering"
[2]: ./media/backend-interoperability/HubVNet-peering.png "hub VNet VNet-peering"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN Gateway-konfiguration av en gren virtuellt nätverk"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute-1-konfiguration"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "anslutningskonfiguration ExpressRoute 1 till ett nav VNet ExR gateway"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute-2-konfiguration"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "anslutningskonfiguration ExpressRoute 2 till en hubb VNet ExR gateway"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "anslutningskonfiguration ExpressRoute 2 till en fjärransluten VNet ExR-gateway"

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


