---
title: 'Driftskompatibilitet för ExpressRoute, plats-till-plats-VPN och VNet-Peering - Data Analysis-dataplaner: Azure Serverdelsanslutning funktioner samverkan | Microsoft Docs'
description: Den här sidan innehåller plan dataanalys av test-installationen som har skapats för att analysera samverkan med funktioner för ExpressRoute, plats-till-plats VPN- och VNet-Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947343"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>Driftskompatibilitet för ExpressRoute, plats-till-plats VPN- och VNet-Peering - plan för dataanalys

I den här artikeln ska vi gå igenom plan dataanalys av test-installationen. Test-installation finns den [inställningar][Setup]. Konfigurationsdetaljer för inställningar finns i [Test installationskonfiguration][Configuration]. Kontrollen plan analys av test-installationen finns i [kontroll plan Analysis][Control-Analysis].

Plan för dataanalys undersöker sökvägen som paket som passerar från ett lokalt nätverk (LAN/VNet) till ett annat inom en topologi. Datasökväg mellan två lokala nätverk inte nödvändigtvis symmetriska. Därför i den här artikeln ska vi analysera sökvägen för vidarebefordran från ett lokalt nätverk till en annan separat från omvänd sökvägen.

##<a name="data-path-from-hub-vnet"></a>Datasökväg från Hubbnätverk

###<a name="path-to-spoke-vnet"></a>Sökvägen till ekrar virtuellt nätverk

VNet-peering emulerar nätverksbrygga funktioner mellan de två virtuella nätverken är peer-kopplade. Traceroute utdata från en Hubbnätverk till en virtuell dator i virtuellt ekernätverk som listas nedan:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Följande skärm klippet är vyn grafiska anslutning för VNet NAV och ekrar VNet som presenteras av Azure Network Watcher:


[![1]][1]

###<a name="path-to-branch-vnet"></a>Sökvägen till grenen virtuellt nätverk

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

I ovanstående traceroute är det första hoppet VPN GW för Hub VNet. Det andra hoppet är VPN-GW av gren virtuellt nätverk, vars IP-adresser inte annonseras inom den Hubbnätverk. Det tredje hoppet är den virtuella datorn på grenen VNet.

Följande skärm klippet är vyn grafiska anslutning av den Hubbnätverk och det virtuella nätverket gren som presenteras av Azure Network Watcher:

[![2]][2]

För samma anslutning är följande skärm clip rutnätsvyn som presenteras av Azure Network Watcher:

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>Sökväg till en lokal plats-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

I ovanstående traceroute är det första hoppet ExpressRoute GW tunnelslutpunkt för MSEE. Andra och tredje hopp är respektive CE routern och en lokal plats 1 LAN IP-adresser, IP-adresserna inte annonseras inom VNet Hub. Det fjärde hoppet är den virtuella datorn på en lokal plats-1.


###<a name="path-to-on-premises-location-2"></a>Sökväg till en lokal plats-2

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

I ovanstående traceroute är det första hoppet ExpressRoute GW tunnelslutpunkt för MSEE. Andra och tredje hopp är respektive CE routern och en lokal plats 2 LAN IP-adresser, IP-adresserna inte annonseras inom VNet Hub. Det fjärde hoppet är den virtuella datorn på en lokal plats-2.

###<a name="path-to-remote-vnet"></a>Sökväg till virtuella fjärrnätverket

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

I ovanstående traceroute är det första hoppet ExpressRoute GW tunnelslutpunkt för MSEE. Det andra hoppet är fjärransluten VNet GW IP-adress. Det andra hopp-IP-adressintervallet annonseras inte inom den Hubbnätverk. Det tredje hoppet är den virtuella datorn på det virtuella fjärrnätverket.

##<a name="data-path-from-spoke-vnet"></a>Datasökväg från ekrar virtuellt nätverk

Återkallande att VNet ekrar dela vyn nätverk för den Hubbnätverk. Via VNet-peering, använder virtuellt ekernätverk fjärr-gateway-anslutningen för det virtuella hubbnätverket som om de är direkt anslutna till virtuellt ekernätverk.

###<a name="path-to-hub-vnet"></a>Sökvägen till Hubbnätverk

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>Sökvägen till grenen virtuellt nätverk

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

I ovanstående traceroute är det första hoppet VPN GW för Hub VNet. Det andra hoppet är VPN-GW av gren virtuellt nätverk, vars IP-adresser inte annonseras inom det nav/eker virtuella nätverket. Det tredje hoppet är den virtuella datorn på grenen VNet.

###<a name="path-to-on-premises-location-1"></a>Sökväg till en lokal plats-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

I ovanstående traceroute är det första hoppet i Hubbnätverk ExpressRoute GW tunnelslutpunkt för MSEE. Andra och tredje hopp är respektive CE routern och en lokal plats 1 LAN IP-adresser, IP-adresserna inte annonseras inom nav/eker-VNet. Det fjärde hoppet är den virtuella datorn på en lokal plats-1.

###<a name="path-to-on-premises-location-2"></a>Sökväg till en lokal plats-2

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

I ovanstående traceroute är det första hoppet i Hubbnätverk ExpressRoute GW tunnelslutpunkt för MSEE. Andra och tredje hopp är respektive CE routern och en lokal plats 2 LAN IP-adresser, IP-adresserna inte annonseras i nav/eker-VNets. Det fjärde hoppet är den virtuella datorn på en lokal plats-2.

###<a name="path-to-remote-vnet"></a>Sökväg till virtuella fjärrnätverket

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

I ovanstående traceroute är det första hoppet i Hubbnätverk ExpressRoute GW tunnelslutpunkt för MSEE. Det andra hoppet är fjärransluten VNet GW IP-adress. Det andra hopp-IP-adressintervallet annonseras inte inom det nav/eker virtuella nätverket. Det tredje hoppet är den virtuella datorn på det virtuella fjärrnätverket.

##<a name="data-path-from-branch-vnet"></a>Datasökväg från gren virtuellt nätverk

###<a name="path-to-hub-vnet"></a>Sökvägen till Hubbnätverk

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

I ovanstående traceroute är det första hoppet VPN GW för grenen VNet. Det andra hoppet är VPN-GW av Hubbnätverk, vars IP-adresser inte annonseras inom det virtuella fjärrnätverket. Det tredje hoppet är den virtuella datorn på den Hubbnätverk.

###<a name="path-to-spoke-vnet"></a>Sökvägen till ekrar virtuellt nätverk

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

I ovanstående traceroute är det första hoppet VPN GW för grenen VNet. Det andra hoppet är VPN-GW av Hubbnätverk, vars IP-adresser inte annonseras inom det virtuella fjärrnätverket, och det tredje hoppet är den virtuella datorn på VNet ekrar.

###<a name="path-to-on-premises-location-1"></a>Sökväg till en lokal plats-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

I ovanstående traceroute är det första hoppet VPN GW för grenen VNet. Det andra hoppet är VPN-GW av Hubbnätverk, vars IP-adresser inte annonseras inom det virtuella fjärrnätverket. Det tredje hoppet är den VPN-tunnel avslutning punkten på den primära CE-routern. Det fjärde hoppet är en intern IP-adressen för en lokal plats 1 LAN IP-adress som inte annonseras ut CE routern. Det femte hoppet är målet virtuell dator på en lokal plats-1.

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>Sökväg till en lokal plats 2 och fjärranslutna virtuellt nätverk

Som vi diskuterade före i kontrollen plan analys har grenen VNet inga synlighet till en lokal plats-2 eller till virtuella fjärrnätverket per nätverkskonfigurationen. Följande ping-resultat bekräfta faktumet. 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

##<a name="data-path-from-on-premises-location-1"></a>Sökvägen för från en lokal plats-1

###<a name="path-to-hub-vnet"></a>Sökvägen till Hubbnätverk

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

I ovanstående traceroute är först två hopp en del av det lokala nätverket. Det tredje hoppet är det primära msee: N gränssnittet mot CE-router. Det fjärde hoppet är ExpressRoute G/W för det virtuella hubbnätverket, vars IP-adressintervall inte annonseras till det lokala nätverket. Det femte hoppet är målet VM.

Azure Network Watcher innehåller endast Azure-inriktad vy. Därför har vi använt Azure Network prestanda Övervakare (NPM) för lokala centrerad vy. NPM innehåller agenter som kan vara installerade servrar i nätverk utanför Azure och analyserar data-sökväg.

Följande skärm klippet är topologi vy av en lokal plats 1 VM-anslutning till den virtuella datorn på det virtuella hubbnätverket via ExpressRoute.

[![4]][4]

Återkallar, test-installationen använder plats-till-plats VPN som säkerhetskopiering anslutning för ExpressRoute mellan en lokal plats-1 och Hubbnätverk. Om du vill testa backend datapath vi ge upphov till en ExpressRoute-länkfel mellan en lokal plats 1 primär CE router och den motsvarande msee: N genom att stänga av gränssnittet CE mot den msee: N.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Följande skärm klippet är topologi-bild av en lokal plats 1 VM-anslutning till den virtuella datorn på det virtuella hubbnätverket via plats-till-plats-VPN-anslutning när ExpressRoute-anslutningar ligger nere.

[![5]][5]

###<a name="path-to-spoke-vnet"></a>Sökvägen till ekrar virtuellt nätverk

Låt oss sätta tillbaka den primära ExpressRoute-anslutningen att göra datapath analys mot ekrar VNet.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Låt oss ta fram primära ExpressRoute-1-anslutning för resten av datapath analysen.

###<a name="path-to-branch-vnet"></a>Sökvägen till grenen virtuellt nätverk

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>Sökväg till en lokal plats-2

Som vi diskuterade före i kontrollen plan analysen har en lokal plats-1 inga synlighet till en lokal plats-2 per nätverkskonfigurationen. Följande ping-resultat bekräfta faktumet. 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>Sökväg till virtuella fjärrnätverket

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>Sökvägen för från en lokal plats-2

###<a name="path-to-hub-vnet"></a>Sökvägen till Hubbnätverk

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Sökvägen till ekrar virtuellt nätverk

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>Sökvägen till grenen virtuellt nätverk, en lokal plats 1 och fjärranslutna VNet

Som vi beskrivs före i kontrollen plan analysen kan en lokal plats-1 har inga insyn i gren VNet, lokala plats-1 och virtuella fjärrnätverket per nätverkskonfigurationen. 

##<a name="data-path-from-remote-vnet"></a>Datasökväg från virtuella fjärrnätverket

###<a name="path-to-hub-vnet"></a>Sökvägen till Hubbnätverk

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Sökvägen till ekrar virtuellt nätverk

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>Sökvägen till grenen virtuellt nätverk och en lokal plats-2

Som vi diskuterade före i kontrollen plan analysen har virtuella fjärrnätverket inga insyn gren virtuellt nätverk och en lokal plats – 2 per nätverkskonfigurationen. 


### <a name="path-to-on-premises-location-1"></a>Sökväg till en lokal plats-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>Ytterligare läsning

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Med ExpressRoute och plats-till-plats VPN-anslutning tillsammans

####<a name="site-to-site-vpn-over-expressroute"></a>Plats-till-plats-VPN över ExpressRoute

Plats-till-plats-VPN kan konfigureras via ExpressRoute Microsoft-peering för att privat utbyta data mellan ditt lokala nätverk och ditt virtuella Azure-nätverk med konfidentialitet, anti repetitionsattacker, äkthetsbeviset och integritet. Mer information om hur du konfigurerar plats-till-plats-IPSec VPN i tunnelläge via ExpressRoute Microsoft-peering finns i [plats-till-plats-VPN över ExpressRoute Microsoft-peering][S2S-Over-ExR]. 

Den viktigaste begränsningen med att konfigurera S2S VPN över Microsoft-peering är dataflödet. Genomströmning via IPSec-tunneln begränsas av VPN-GW-kapacitet. VPN-GW dataflödet är mindre jämfört med ExpressRoute dataflöde. I sådana scenarier skulle med hjälp av IPSec-tunneln för hög belastning på säker och privat peering för alla andra trafik att optimera bandbreddsanvändningen för ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Plats-till-plats VPN som en säker redundanssökväg för ExpressRoute
ExpressRoute erbjuds som redundant krets par att säkerställa hög tillgänglighet. Du kan konfigurera geo-redundant ExpressRoute-anslutningen i olika Azure-regioner. Också som görs i vår test-installationsprogrammet, inom en viss Azure-region kan om du vill ha en redundanssökväg för ExpressRoute-anslutningen du göra det med hjälp av plats-till-plats VPN. När samma prefix har annonserats via både ExpressRoute och S2S VPN, föredrar Azure ExpressRoute via S2S VPN. Om du vill undvika asymmetrisk routning mellan ExpressRoute och S2S VPN, lokala nätverkskonfigurationen bör även öka exponeringen föredra ExpressRoute via S2S VPN-anslutning.

Läs mer om hur du konfigurerar ExpressRoute och plats-till-plats VPN-anslutningar för samexistens mellan [ExpressRoute och plats-till-plats samexistens][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Utöka Serverdelsanslutning till ekrar virtuella nätverk och grenen platser

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Ekrar VNet-anslutning med VNet-peering

Arkitektur för NAV-och-eker-virtuellt nätverk används ofta. Hubben är ett virtuellt nätverk (VNet) i Azure som fungerar som en central plats för anslutning mellan din virtuella ekernätverken och till ditt lokala nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben och kan användas för att isolera arbetsbelastningar. Trafiken flödar mellan det lokala datacentret och hubben via en ExpressRoute eller VPN-anslutning. Mer information om arkitekturen finns [NAV och eker-arkitektur][Hub-n-Spoke]

VNet-peering inom en region kan virtuella använda hub VNet-gateway (både VPN och ExpressRoute-gatewayer) att kommunicera med fjärrnätverk ekernätverken.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Branch-VNet-anslutning med VPN för plats-till-plats

Om du vill gren virtuella nätverk (i olika regioner) och lokala nätverk som kommunicerar med varandra via en hubbnätverk, är den interna Azure-lösningen med hjälp av VPN för plats-till-plats VPN-anslutning. Ett alternativ är att använda en NVA för routning i hubben.

Konfigurera VPN-gatewayer finns i [konfigurera VPN-Gateway][VPN]. Distribuera högtillgängliga NVA finns i [distribuera högtillgängliga NVA][Deploy-NVA].

## <a name="next-steps"></a>Nästa steg

Lär dig hur många ExpressRoute-kretsar som du kan ansluta till en ExpressRoute-Gateway, eller hur många ExpressRoute-gatewayer kan du ansluta till en ExpressRoute-krets, eller Läs andra gränser för skalning av ExpressRoute, i avsnittet [ExpressRoute vanliga frågor och svar][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "network Watcher vy över anslutningen från Hubbnätverk till ekrar virtuellt nätverk"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "network Watcher vy över anslutningen från Hubbnätverk till grenen virtuellt nätverk"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "network Watcher rutnätsvyn för anslutningen från Hubbnätverk till grenen virtuellt nätverk"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Övervakare av nätverksprestanda vy över anslutning från plats 1 virtuell dator till Hubbnätverk via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Övervakare av nätverksprestanda vy över anslutning från plats 1 virtuell dator till Hubbnätverk via S2S VPN"

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




