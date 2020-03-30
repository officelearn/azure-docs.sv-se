---
title: 'Interoperabilitet i Azure-backend-anslutningsfunktioner: Konfigurationsinformation | Microsoft-dokument'
description: I den här artikeln beskrivs konfigurationsinformation för testkonfigurationen som du kan använda för att analysera interoperabilitet mellan ExpressRoute, en VPN från plats till plats och virtuell nätverks peering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 9c4a57111566248d3537cab0d9d85c0c3be874a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68335939"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilitet i Azure-backend-anslutningsfunktioner: Testkonfigurationsinformation

I den här artikeln beskrivs konfigurationsinformationen för [testinställningen][Setup]. Testkonfigurationen hjälper dig att analysera hur Azure-nätverkstjänster samverkar på kontrollplansnivå och dataplansnivå.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Eker-VNet-anslutning med hjälp av VNet-peering

Följande bild visar azure Virtual Network peering detaljer för en eker virtuellt nätverk (VNet). Mer information om hur du konfigurerar peering mellan två virtuella nätverk finns i [Hantera VNet-peering][VNet-Config]. Om du vill att ekrarnas virtuella nätverk ska använda de gateways som är anslutna till hubv-nätverket väljer du **Använd fjärrgateways**.

[![1]][1]

Följande bild visar VNet-peering-informationen om navet VNet. Om du vill att hubb-nätverket ska tillåta att det eker-virtuella nätverket använder hubbens gateways väljer du **Tillåt gatewaytransit .**

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Gren VNet-anslutning med hjälp av en plats-till-plats-VPN

Konfigurera VPN-anslutning mellan navet och grenens virtuella nätverk med hjälp av VPN-gateways i Azure VPN Gateway. Som standard använder VPN-gateways och Azure ExpressRoute-gateways ett asn-värde (Private Autonomous System Number) på **65515**. Du kan ändra ASN-värdet i VPN Gateway. I testkonfigurationen ändras ASN-värdet för grenens VPN VPN-gateway till **65516** för att stödja eBGP-routning mellan nav- och gren-virtuella nätverk.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Lokal plats 1-anslutning med hjälp av ExpressRoute och en plats-till-plats-VPN

### <a name="expressroute-1-configuration-details"></a>Konfigurationsinformation för ExpressRoute 1

Följande bild visar Azure Region 1 ExpressRoute-kretskonfigurationen mot lokala eS-routrar (Location 1).The following figure shows the Azure Region 1 ExpressRoute circuit configuration towards on-premises Location 1 customer edge (CE) routers:

[![4]][4]

Följande bild visar anslutningskonfigurationen mellan ExpressRoute 1-kretsen och navet VNet:

[![5]][5]

Följande lista visar den primära CE-routerkonfigurationen för ExpressRoute-privat peering-anslutning. (Cisco ASR1000-routrar används som CE-routrar i testkonfigurationen.) När VPN- och ExpressRoute-kretsar från plats till plats konfigureras parallellt för att ansluta ett lokalt nätverk till Azure prioriterar Azure ExpressRoute-kretsen som standard. För att undvika asymmetrisk routning bör det lokala nätverket också prioritera ExpressRoute-anslutning via vpn-anslutning från plats till plats. Följande konfiguration fastställer prioritering med hjälp av attributet BGP **local-preference:**

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

### <a name="site-to-site-vpn-configuration-details"></a>Konfigurationsinformation för plats till plats

Följande lista visar den primära CE-routerkonfigurationen för VPN-anslutning mellan plats och plats:

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

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Lokal anslutning till plats 2 med hjälp av ExpressRoute

En andra ExpressRoute-krets, i närheten av lokal plats 2, ansluter lokal plats 2 till navet VNet. Följande bild visar den andra ExpressRoute-konfigurationen:

[![6]][6]

Följande bild visar anslutningskonfigurationen mellan den andra ExpressRoute-kretsen och navet VNet:

[![7]][7]

ExpressRoute 1 ansluter både hubb-VNet och lokal plats 1 till ett fjärr-VNet i en annan Azure-region:

[![8]][8]

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

Lär dig mer om [kontrollplansanalys][Control-Analysis] av testinställningarna och vyerna för olika virtuella nätverk eller VLAN i topologin.

Lär dig mer om analys av [testplan][Data-Analysis] av testkonfigurationen och Azure-nätverksövervakningsfunktionsvyer.

Se [Vanliga frågor och svar om ExpressRoute][ExR-FAQ] för att:
-   Läs om hur många ExpressRoute-kretsar du kan ansluta till en ExpressRoute-gateway.
-   Läs om hur många ExpressRoute-gateways du kan ansluta till en ExpressRoute-krets.
-   Läs mer om andra skalbegränsningar för ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Talade VNet's VNet peering"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Hub VNets VNet-peering"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN Gateway-konfiguration av ett filial-virtuella nätverk"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1 konfiguration"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Anslutningskonfiguration för ExpressRoute 1 till en hubBNet ExR-gateway"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2 konfiguration"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Anslutningskonfiguration för ExpressRoute 2 till en hubBNet ExR-gateway"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Anslutningskonfiguration för ExpressRoute 2 till en fjärr-VNet ExR-gateway"

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


