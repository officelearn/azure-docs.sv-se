---
title: 'Samverkan i Azure backend-anslutningsfunktionerna: Data analysis-dataplaner | Microsoft Docs'
description: Den här artikeln ger data plan analys av de inställningar du kan använda för att analysera samverkan mellan ExpressRoute, en plats-till-plats-VPN och virtuell nätverkspeering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 75569d552baba8ce72b5e322d6ef6f81fcaa4bc5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761267"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Samverkan i Azure backend-anslutningsfunktionerna: Plan för dataanalys

Den här artikeln beskriver plan dataanalys av den [testa installationen][Setup]. Du kan också granska den [test installationskonfiguration] [ Configuration] och [styra plan analysis] [ Control-Analysis] av test-installationen.

Plan för dataanalys undersöker sökvägen som paket som passerar från ett lokalt nätverk (LAN eller virtuellt nätverk) till ett annat inom en topologi. Datasökväg mellan två lokala nätverk är inte nödvändigtvis symmetriska. Därför i den här artikeln analyserar vi en vidarebefordran sökväg från ett lokalt nätverk till ett annat nätverk som är separat från omvänd sökvägen.

## <a name="data-path-from-the-hub-vnet"></a>Datasökväg från det virtuella hubbnätverket

### <a name="path-to-the-spoke-vnet"></a>Sökvägen till virtuellt ekernätverk

Peering av virtuella nätverk (VNet) emulerar nätverksfunktioner brygga mellan de två virtuella nätverken är peer-kopplade. Traceroute utdata från hubbens virtuella nätverk till en virtuell dator i eker VNet visas här:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Följande bild visar vyn grafiska anslutning för det virtuella hubbnätverket och virtuellt ekernätverk ur Azure Network Watcher:


[![1]][1]

### <a name="path-to-the-branch-vnet"></a>Sökvägen till grenen virtuellt nätverk

Traceroute utdata från hubbens virtuella nätverk till en virtuell dator i grenen VNet visas här:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

I den här traceroute är det första hoppet VPN-gatewayen i Azure VPN Gateway för det virtuella hubbnätverket. Det andra hoppet är den VPN-gatewayen på förgrening VNet. IP-adressen för VPN-gatewayen på förgrening VNet annonseras inte i det virtuella hubbnätverket. Det tredje hoppet är den virtuella datorn på grenen VNet.

Följande bild visar vyn grafiska anslutning för det virtuella hubbnätverket och gren VNet Network Watcher ur:

[![2]][2]

Följande bild visar rutnätsvyn i Network Watcher för samma anslutning:

[![3]][3]

### <a name="path-to-on-premises-location-1"></a>Sökväg till en lokal plats 1

Traceroute utdata från hubbens virtuella nätverk till en virtuell dator i en lokal plats 1 visas här:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

I den här traceroute är det första hoppet Azure ExpressRoute-gateway-tunnelslutpunkten till en Microsoft Enterprise Edge Router (MSEE). Andra och tredje hopp är gränsrouter (CE) och en lokal plats 1 LAN IP-adresser. Dessa IP-adresser annonseras inte i det virtuella hubbnätverket. Det fjärde hoppet är den virtuella datorn i en lokal plats 1.


### <a name="path-to-on-premises-location-2"></a>Sökväg till en lokal plats 2

Traceroute utdata från hubbens virtuella nätverk till en virtuell dator i en lokal plats 2 visas här:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

I den här traceroute är det första hoppet ExpressRoute-gateway-tunnelslutpunkten för en MSEE. Andra och tredje hopp är CE-router och den lokala platsen 2 LAN IP-adresser. Dessa IP-adresser annonseras inte i det virtuella hubbnätverket. Det fjärde hoppet är den virtuella datorn på en lokal plats 2.

### <a name="path-to-the-remote-vnet"></a>Sökvägen till virtuella fjärrnätverket

Traceroute utdata från hubbens virtuella nätverk till en virtuell dator i virtuella fjärrnätverket visas här:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

I den här traceroute är det första hoppet ExpressRoute-gateway-tunnelslutpunkten för en MSEE. Det andra hoppet är fjärransluten VNet-gateway IP-adress. Det andra hopp-IP-adressintervallet är inte annonseras i det virtuella hubbnätverket. Det tredje hoppet är den virtuella datorn på virtuella fjärrnätverket.

## <a name="data-path-from-the-spoke-vnet"></a>Datasökväg från virtuellt ekernätverk

Virtuellt ekernätverk delar vyn nätverk för det virtuella hubbnätverket. Via VNet-peering, använder virtuellt ekernätverk fjärr-gateway-anslutningen för det virtuella hubbnätverket som om den är direkt ansluten till virtuellt ekernätverk.

### <a name="path-to-the-hub-vnet"></a>Sökvägen till det virtuella hubbnätverket

Traceroute utdata från virtuellt ekernätverk till en virtuell dator i hubben VNet visas här:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Sökvägen till grenen virtuellt nätverk

Traceroute utdata från virtuellt ekernätverk till en virtuell dator i grenen VNet visas här:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

I den här traceroute är det första hoppet VPN-gateway för det virtuella hubbnätverket. Det andra hoppet är den VPN-gatewayen på förgrening VNet. IP-adressen för VPN-gatewayen på förgrening VNet annonseras inte inom nav/eker VNet. Det tredje hoppet är den virtuella datorn på grenen VNet.

### <a name="path-to-on-premises-location-1"></a>Sökväg till en lokal plats 1

Traceroute utdata från eker VNet till en virtuell dator i en lokal plats 1 visas här:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

I den här traceroute är det första hoppet i Hubbnätverk ExpressRoute-gateway tunnelslutpunkt för en MSEE. Andra och tredje hopp är CE-router och den lokala platsen 1 LAN IP-adresser. Dessa IP-adresser annonseras inte i hubben/eker VNet. Det fjärde hoppet är den virtuella datorn i en lokal plats 1.

### <a name="path-to-on-premises-location-2"></a>Sökväg till en lokal plats 2

Traceroute utdata från eker VNet till en virtuell dator i en lokal plats 2 visas här:


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

I den här traceroute är det första hoppet i Hubbnätverk ExpressRoute-gateway tunnelslutpunkt för en MSEE. Andra och tredje hopp är CE-router och den lokala platsen 2 LAN IP-adresser. Dessa IP-adresser annonseras inte i de hub/virtuella ekernätverken. Det fjärde hoppet är den virtuella datorn i en lokal plats 2.

### <a name="path-to-the-remote-vnet"></a>Sökvägen till virtuella fjärrnätverket

Traceroute utdata från eker VNet till en virtuell dator i virtuella fjärrnätverket visas här:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

I den här traceroute är det första hoppet i Hubbnätverk ExpressRoute-gateway tunnelslutpunkt för en MSEE. Det andra hoppet är fjärransluten VNet-gateway IP-adress. Det andra hopp-IP-adressintervallet är inte annonseras i hubben/eker VNet. Det tredje hoppet är den virtuella datorn på virtuella fjärrnätverket.

## <a name="data-path-from-the-branch-vnet"></a>Datasökväg från grenen virtuellt nätverk

### <a name="path-to-the-hub-vnet"></a>Sökvägen till det virtuella hubbnätverket

Traceroute utdata från grenen VNet till en virtuell dator i hubben VNet visas här:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

I den här traceroute är det första hoppet VPN-gatewayen på förgrening VNet. Det andra hoppet är VPN-gateway för det virtuella hubbnätverket. IP-adressen för VPN-gateway för det virtuella hubbnätverket annonseras inte i virtuella fjärrnätverket. Det tredje hoppet är den virtuella datorn på det virtuella hubbnätverket.

### <a name="path-to-the-spoke-vnet"></a>Sökvägen till virtuellt ekernätverk

Traceroute utdata från grenen VNet till en virtuell dator i eker VNet visas här:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

I den här traceroute är det första hoppet VPN-gatewayen på förgrening VNet. Det andra hoppet är VPN-gateway för det virtuella hubbnätverket. IP-adressen för VPN-gateway för det virtuella hubbnätverket annonseras inte i virtuella fjärrnätverket. Det tredje hoppet är den virtuella datorn på virtuellt ekernätverk.

### <a name="path-to-on-premises-location-1"></a>Sökväg till en lokal plats 1

Traceroute utdata från grenen VNet till en virtuell dator i en lokal plats 1 visas här:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

I den här traceroute är det första hoppet VPN-gatewayen på förgrening VNet. Det andra hoppet är VPN-gateway för det virtuella hubbnätverket. IP-adressen för VPN-gateway för det virtuella hubbnätverket annonseras inte i virtuella fjärrnätverket. Det tredje hoppet är den VPN-tunnel avslutning punkten på den primära CE-routern. Det fjärde hoppet är en intern IP-adressen för en lokal plats 1. Den här LAN IP-adressen är inte annonseras utanför CE-router. Det femte hoppet är målet virtuell dator i en lokal plats 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Sökvägen till en lokal plats 2 och virtuella fjärrnätverket

Enligt beskrivningen i kontrollen plan analysen, har grenen VNet inga synlighet till en lokal plats 2 eller till virtuella fjärrnätverket per nätverkskonfigurationen. Bekräfta att följande ping-resultat: 

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

## <a name="data-path-from-on-premises-location-1"></a>Sökvägen för från en lokal plats 1

### <a name="path-to-the-hub-vnet"></a>Sökvägen till det virtuella hubbnätverket

Traceroute utdata från en lokal plats 1 till en virtuell dator i hubben VNet visas här:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

I den här traceroute är först två hopp en del av det lokala nätverket. Det tredje hoppet är det primära msee: N-gränssnittet som riktas mot CE-router. Det fjärde hoppet är ExpressRoute-gatewayen för det virtuella hubbnätverket. ExpressRoute-gatewayen för det virtuella hubbnätverket IP-adressintervall är inte annonseras till det lokala nätverket. Det femte hoppet är målet VM.

Network Watcher får endast en Azure-inriktad vy. För en lokal perspektiv kan vi använda Azure Network Performance Monitor. Övervakare av nätverksprestanda innehåller agenter som du kan installera på servrar i nätverk utanför Azure för dataanalys i sökvägen.

Följande bild visar topologin vy av en lokal plats 1 VM-anslutning till den virtuella datorn på det virtuella hubbnätverket via ExpressRoute:

[![4]][4]

Som tidigare diskuterats, använder test-installationen en plats-till-plats-VPN som säkerhetskopiering anslutning för ExpressRoute mellan en lokal plats 1 och det virtuella hubbnätverket. Om du vill testa sökvägen för säkerhetskopierade data, vi ge upphov till en ExpressRoute-länkfel mellan en lokal plats 1 primär CE router och den motsvarande msee: N. Stäng av gränssnittet CE som riktas mot den msee: N för att framkalla en ExpressRoute-länkfel:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Följande bild visar topologin vy av en lokal plats 1 VM-anslutning till den virtuella datorn på det virtuella hubbnätverket via plats-till-plats VPN-anslutning när ExpressRoute-anslutningar ligger nere:

[![5]][5]

### <a name="path-to-the-spoke-vnet"></a>Sökvägen till virtuellt ekernätverk

Traceroute utdata från en lokal plats 1 till en virtuell dator i eker VNet visas här:

Låt oss ta tillbaka den primära ExpressRoute-anslutningen kan utföra analyser på sökvägen data mot virtuellt ekernätverk:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Ta fram primära ExpressRoute 1-anslutningen för resten av sökvägen dataanalys.

### <a name="path-to-the-branch-vnet"></a>Sökvägen till grenen virtuellt nätverk

Traceroute utdata från en lokal plats 1 till en virtuell dator i grenen VNet visas här:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Sökväg till en lokal plats 2

Som beskrivs i den [styra plan analysis][Control-Analysis], en lokal plats 1 har inga synlighet till en lokal plats 2 per nätverkskonfigurationen. Bekräfta att följande ping-resultat: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Sökvägen till virtuella fjärrnätverket

Traceroute utdata från en lokal plats 1 till en virtuell dator i virtuella fjärrnätverket visas här:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Sökvägen för från en lokal plats 2

### <a name="path-to-the-hub-vnet"></a>Sökvägen till det virtuella hubbnätverket

Traceroute utdata från en lokal plats 2 till en virtuell dator i hubben VNet visas här:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Sökvägen till virtuellt ekernätverk

Traceroute utdata från en lokal plats 2 till en virtuell dator i eker VNet visas här:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Sökvägen till grenen virtuellt nätverk, en lokal plats 1 och virtuella fjärrnätverket

Som beskrivs i den [styra plan analysis][Control-Analysis], en lokal plats 1 har inga synlighet till grenen virtuellt nätverk, till en lokal plats 1 eller till virtuella fjärrnätverket per nätverkskonfigurationen. 

## <a name="data-path-from-the-remote-vnet"></a>Datasökväg från virtuella fjärrnätverket

### <a name="path-to-the-hub-vnet"></a>Sökvägen till det virtuella hubbnätverket

Traceroute utdata från virtuella fjärrnätverket till en virtuell dator i hubben VNet visas här:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Sökvägen till virtuellt ekernätverk

Traceroute utdata från virtuella fjärrnätverket till en virtuell dator i eker VNet visas här:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Sökvägen till grenen virtuellt nätverk och en lokal plats 2

Som beskrivs i den [styra plan analysis][Control-Analysis], virtuella fjärrnätverket har inga synlighet till grenen virtuellt nätverk eller till en lokal plats 2 per nätverkskonfigurationen. 

### <a name="path-to-on-premises-location-1"></a>Sökväg till en lokal plats 1

Traceroute utdata från virtuella fjärrnätverket till en virtuell dator i en lokal plats 1 visas här:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


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

Se den [ExpressRoute vanliga frågor och svar] [ ExR-FAQ] till:
-   Lär dig hur många ExpressRoute-kretsar som du kan ansluta till en ExpressRoute-gateway.
-   Lär dig hur många ExpressRoute-gatewayer som du kan ansluta till en ExpressRoute-krets.
-   Läs mer om andra gränser för skalning av ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "network Watcher vy över anslutningen från hubbens virtuella nätverk till ett virtuellt ekernätverk"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "network Watcher vy över anslutningen från hubbens virtuella nätverk till en gren virtuellt nätverk"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "network Watcher rutnätsvyn för anslutningen mellan en hubbnätverket och en gren virtuellt nätverk"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Övervakare av nätverksprestanda vy över anslutning från plats 1 virtuell dator till det virtuella hubbnätverket via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Övervakare av nätverksprestanda vy över anslutning från plats 1 virtuell dator till det virtuella hubbnätverket via ett plats-till-plats-VPN"

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


