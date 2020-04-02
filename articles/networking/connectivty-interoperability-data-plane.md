---
title: 'Interoperabilitet i Azure : Dataplananalys'
description: Den här artikeln innehåller dataplananalys av testinställningarna som du kan använda för att analysera interoperabilitet mellan ExpressRoute, en VPN från plats till plats och virtuell nätverks peering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: fe7b74b0d4d065d4f222fefbbdc4a1d434d1163b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518253"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperabilitet i Azure : Dataplananalys

I den här artikeln beskrivs dataplananalysen av [testinställningen][Setup]. Du kan också granska [testinställningskonfigurationen][Configuration] och [kontrollplansanalysen][Control-Analysis] av testkonfigurationen.

Dataplananalys undersöker sökvägen som tas av paket som passerar från ett lokalt nätverk (LAN eller virtuellt nätverk) till ett annat inom en topologi. Datasökvägen mellan två lokala nätverk är inte nödvändigtvis symmetrisk. Därför analyserar vi i den här artikeln en vidarebefordringsväg från ett lokalt nätverk till ett annat nätverk som är separat från den omvända sökvägen.

## <a name="data-path-from-the-hub-vnet"></a>Datasökväg från navet VNet

### <a name="path-to-the-spoke-vnet"></a>Sökväg till det ekra virtuella nätverket

VNet-peering (Virtual Network) emulerar nätverksbrygga mellan de två virtuella nätverk som är peer-relaterade. Traceroute-utdata från ett nav-virtuellt nätverk till en virtuell dator i det eker-virtuella nätverket visas här:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Följande bild visar den grafiska anslutningsvyn för navet VNet och eker-VNet ur Azure Network Watchers perspektiv:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Sökväg till grenens virtuella nätverk

Traceroute-utdata från ett nav-virtuellt nätverk till en virtuell dator i grenens virtuella nätverk visas här:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

I den här traceroute är det första hoppet VPN-gatewayen i Azure VPN Gateway för hub vnet. Det andra hoppet är VPN-gatewayen för grenenS VNet. IP-adressen för VPN-gatewayen för grenens virtuella nätverk annonseras inte i hub vnet. Det tredje hoppet är den virtuella datorn på grenens virtuella nätverk.

Följande bild visar den grafiska anslutningsvyn för navet VNet och grenen VIRTUELLA nätverk ur Network Watcher:

![2][2]

För samma anslutning visar följande bild rutnätsvyn i Network Watcher:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Sökväg till lokal plats 1

Traceroute-utdata från ett nav-virtuellt nätverk till en virtuell dator på lokal plats 1 visas här:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

I den här traceroute är det första hoppet slutpunkten för Azure ExpressRoute gateway-tunnel till en Microsoft Enterprise Edge Router (MSEE). Det andra och tredje hoppen är routern för kundkant (CE) och de lokala plats 1-IP-adresserna. Dessa IP-adresser annonseras inte i hubb-nätverket. Det fjärde hoppet är den virtuella datorn på den lokala plats 1.


### <a name="path-to-on-premises-location-2"></a>Sökväg till lokal plats 2

Traceroute-utdata från ett nav-virtuellt nätverk till en virtuell dator på lokal plats 2 visas här:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

I den här traceroute är det första hoppet ExpressRoute gateway tunnel slutpunkten till en MSEE. Det andra och tredje hoppen är CE-routern och de lokala plats 2 LAN-IP-adresser. Dessa IP-adresser annonseras inte i hubb-nätverket. Det fjärde hoppet är den virtuella datorn på den lokala plats 2.

### <a name="path-to-the-remote-vnet"></a>Sökväg till fjärr-VNet

Traceroute-utdata från ett nav-virtuellt nätverk till en virtuell dator i fjärr-virtuella nätverk visas här:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

I den här traceroute är det första hoppet ExpressRoute gateway tunnel slutpunkten till en MSEE. Det andra hoppet är fjärr-VNets gateway-IP. Det andra HOP IP-intervallet annonseras inte i hubb-nätverket. Det tredje hoppet är den virtuella datorn på fjärr-VNet.

## <a name="data-path-from-the-spoke-vnet"></a>Datasökväg från det eker-virtuella nätverket

Eker-VNet delar nätverksvyn för navet VNet. Genom VNet-peering använder ekrarna VNet fjärrgatewayanslutningen för navet VNet som om det är direkt anslutet till det ekriga virtuella nätverket.

### <a name="path-to-the-hub-vnet"></a>Sökväg till navet VNet

Traceroute-utdata från ekrarnavnet till en virtuell dator i navet VNet visas här:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Sökväg till grenens virtuella nätverk

Traceroute-utdata från ekrarnavnet till en virtuell dator i grenens virtuella nätverk visas här:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

I den här traceroute är det första hoppet VPN-gatewayen för navet VNet. Det andra hoppet är VPN-gatewayen för grenenS VNet. IP-adressen för VPN-gatewayen för grenens virtuella nätverk annonseras inte inom hub/spoke-nätverket. Det tredje hoppet är den virtuella datorn på grenens virtuella nätverk.

### <a name="path-to-on-premises-location-1"></a>Sökväg till lokal plats 1

Traceroute-utdata från ekrarvnätet till en virtuell dator i lokal plats 1 visas här:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

I den här traceroute är det första hoppet navet VNets ExpressRoute gateway tunnel slutpunkt till en MSEE. Det andra och tredje hoppen är CE-routern och de lokala plats 1 LAN-IP-adresser. Dessa IP-adresser annonseras inte i hubb/eker-nätverket. Det fjärde hoppet är den virtuella datorn på den lokala plats 1.

### <a name="path-to-on-premises-location-2"></a>Sökväg till lokal plats 2

Traceroute-utdata från ekrarvnätet till en virtuell dator på lokal plats plats 2 visas här:


    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    76 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

I den här traceroute är det första hoppet navet VNets ExpressRoute gateway tunnel slutpunkt till en MSEE. Det andra och tredje hoppen är CE-routern och de lokala plats 2 LAN-IP-adresser. Dessa IP-adresser annonseras inte i hubben/eker-virtuella nätverk. Det fjärde hoppet är den virtuella datorn på den lokala plats 2.

### <a name="path-to-the-remote-vnet"></a>Sökväg till fjärr-VNet

Traceroute-utdata från ekrarnavnet till en virtuell dator i fjärr-virtuella nätverk visas här:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

I den här traceroute är det första hoppet navet VNets ExpressRoute gateway tunnel slutpunkt till en MSEE. Det andra hoppet är fjärr-VNets gateway-IP. Det andra HOP IP-intervallet annonseras inte i hubb/eker-VNet. Det tredje hoppet är den virtuella datorn på fjärr-VNet.

## <a name="data-path-from-the-branch-vnet"></a>Datasökväg från grenens virtuella nätverk

### <a name="path-to-the-hub-vnet"></a>Sökväg till navet VNet

Traceroute-utdata från grenenS virtuella nätverk till en virtuell dator i navet VNet visas här:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

I den här traceroute är det första hoppet VPN-gatewayen för grenen VNet. Det andra hoppet är VPN-gatewayen för hubnet VNet. IP-adressen för VPN-gatewayen för hubnet VNet annonseras inte i fjärr-virtuella nätverk. Det tredje hoppet är den virtuella datorn på hubb-nätverket.

### <a name="path-to-the-spoke-vnet"></a>Sökväg till det ekra virtuella nätverket

Traceroute-utdata från grenenS virtuella nätverk till en virtuell dator i det eker-virtuella nätverket visas här:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

I den här traceroute är det första hoppet VPN-gatewayen för grenen VNet. Det andra hoppet är VPN-gatewayen för hubnet VNet. IP-adressen för VPN-gatewayen för hubnet VNet annonseras inte i fjärr-virtuella nätverk. Det tredje hoppet är den virtuella datorn på det eker-virtuella nätverket.

### <a name="path-to-on-premises-location-1"></a>Sökväg till lokal plats 1

Traceroute-utdata från grenenS virtuella nätverk till en virtuell dator på lokal plats 1 visas här:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

I den här traceroute är det första hoppet VPN-gatewayen för grenen VNet. Det andra hoppet är VPN-gatewayen för hubnet VNet. IP-adressen för VPN-gatewayen för hubnet VNet annonseras inte i fjärr-virtuella nätverk. Det tredje hoppet är VPN-tunnelns avslutningspunkt på den primära CE-routern. Det fjärde hoppet är en intern IP-adress för lokal plats 1. Den här LAN-IP-adressen annonseras inte utanför CE-routern. Det femte hoppet är måldatorn på den lokala plats 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Sökväg till lokal plats 2 och fjärr-VNet

Som vi diskuterade i kontrollplansanalysen har grenens virtuella nätverk ingen synlighet vare sig på lokal plats 2 eller till fjärr-VNet per nätverkskonfigurationen. Följande ping resultat bekräftar: 

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

## <a name="data-path-from-on-premises-location-1"></a>Datasökväg från lokal plats 1

### <a name="path-to-the-hub-vnet"></a>Sökväg till navet VNet

Traceroute-utdata från lokal plats 1 till en virtuell dator i navet VNet visas här:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

I detta spårförbud är de två första hoppen en del av det lokala nätverket. Det tredje hoppet är det primära MSEE-gränssnittet som är vänd mot CE-routern. Det fjärde hoppet är ExpressRoute-gatewayen för hubnet VNet. IP-intervallet för ExpressRoute-gatewayen för hub VNet annonseras inte till det lokala nätverket. Det femte hoppet är måldass.

Network Watcher tillhandahåller endast en Azure-centrerad vy. För ett lokalt perspektiv använder vi Azure Network Performance Monitor. Network Performance Monitor tillhandahåller agenter som du kan installera på servrar i nätverk utanför Azure för datasökvägsanalys.

Följande bild visar topologivyn för den lokala VM-anslutningen för plats 1 till den virtuella datorn på navet VNet via ExpressRoute:

![4][4]

Som diskuterats tidigare använder testkonfigurationen en plats-till-plats-VPN som säkerhetskopieringsanslutning för ExpressRoute mellan den lokala plats 1 och hubbenS VNet. Om du vill testa sökvägen till säkerhetskopieringsdata ska vi inducera ett ExpressRoute-länkfel mellan den lokala plats 1-primära CE-routern och motsvarande MSEE. Om du vill inducera ett ExpressRoute-länkfel stänger du av CE-gränssnittet som är vänd mot MSEE:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Följande bild visar topologivyn för den lokala VM-anslutningen för plats 1 till den virtuella datorn på navet VNet via VPN-anslutning från plats till plats när ExpressRoute-anslutningen är nere:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Sökväg till det ekra virtuella nätverket

Traceroute-utdata från lokal plats 1 till en virtuell dator i det ekerbaserade virtuella nätverket visas här:

Låt oss få tillbaka Den primära ExpressRoute-anslutningen för att göra datasökvägsanalysen mot det eker-virtuella nätverket:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Ta fram den primära ExpressRoute 1-anslutningen för resten av datasökvägsanalysen.

### <a name="path-to-the-branch-vnet"></a>Sökväg till grenens virtuella nätverk

Traceroute-utdata från lokal plats 1 till en virtuell dator i grenens virtuella nätverk visas här:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Sökväg till lokal plats 2

Som vi diskuterar i [kontrollplansanalysen][Control-Analysis]har den lokala plats 1 ingen synlighet för lokal plats 2 per nätverkskonfigurationen. Följande ping resultat bekräftar: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Sökväg till fjärr-VNet

Traceroute-utdata från lokal plats 1 till en virtuell dator i fjärr-virtuella nätverk visas här:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Datasökväg från lokal plats 2

### <a name="path-to-the-hub-vnet"></a>Sökväg till navet VNet

Traceroute-utdata från lokal plats 2 till en virtuell dator i navet VNet visas här:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Sökväg till det ekra virtuella nätverket

Traceroute-utdata från lokal plats 2 till en virtuell dator i det ekerbaserade virtuella nätverket visas här:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Sökväg till filialens virtuella nätverk, lokal plats 1 och det fjärr-virtuella nätverket

Som vi diskuterar i [kontrollplansanalysen][Control-Analysis]har den lokala plats 1 ingen synlighet för grenenS virtuella nätverk, till lokal plats 1 eller till fjärr-virtuella nätverk per nätverkskonfiguration. 

## <a name="data-path-from-the-remote-vnet"></a>Datasökväg från fjärr-VNet

### <a name="path-to-the-hub-vnet"></a>Sökväg till navet VNet

Traceroute-utdata från fjärr-virtuella nätverk till en virtuell dator i navet VNet visas här:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Sökväg till det ekra virtuella nätverket

Traceroute-utdata från fjärr-virtuella nätverk till en virtuell dator i det ekriga virtuella nätverket visas här:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Sökväg till filialens virtuella nätverk och lokal plats 2

Som vi diskuterar i [kontrollplansanalysen][Control-Analysis]har fjärr-VNet ingen synlighet för grenenS virtuella nätverk eller till lokal plats 2 per nätverkskonfigurationen. 

### <a name="path-to-on-premises-location-1"></a>Sökväg till lokal plats 1

Traceroute-utdata från fjärr-virtuella nätverk till en virtuell dator på lokal plats 1 visas här:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


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

Se [Vanliga frågor och svar om ExpressRoute][ExR-FAQ] för att:
-   Läs om hur många ExpressRoute-kretsar du kan ansluta till en ExpressRoute-gateway.
-   Läs om hur många ExpressRoute-gateways du kan ansluta till en ExpressRoute-krets.
-   Läs mer om andra skalbegränsningar för ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Nätverksbevakningsvy över anslutning från ett nav-VNet till ett eker-VNet"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Nätverksbevakningsvy över anslutning från ett nav-VNet till ett filial-VNet"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Nätverksbevakningsnätsvy för anslutning från ett nav-VNet till ett filial-VNet"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Nätverksprestandaövervakaren av anslutning från den virtuella datorn plats 1 till navet VNet via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Nätverksprestandaövervakaren av anslutning från den virtuella datorn plats 1 till navet VNet via en VPN-plats från plats till plats"

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


