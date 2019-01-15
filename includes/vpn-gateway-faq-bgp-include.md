---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 894724b08876e96119bf72ce65e22c6ae884d8da
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306887"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Stöds BGP på alla Azure VPN Gateway-SKU:er?
Nej, BGP stöds på Azure VPN-gatewayerna **VpnGw1**, **VpnGw2**, **VpnGw3**, **Standard** och **HighPerformance**. **Basic** SKU stöds inte.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Kan jag använda BGP med Azure principbaserade VPN-gatewayer?
Nej, BGP stöds bara på route-baserade VPN-gatewayer.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Kan jag använda privata ASN:er (Autonomous System Numbers)?
Ja, du kan använda dina egna offentliga ASN:er eller privata ASN:er för både dina lokala nätverk och virtuella Azure-nätverk.

### <a name="can-i-use-32-bit-asns-autonomous-system-numbers"></a>Kan jag använda 32-bitars ASN:er (autonoma systemnummer)?
Nej, i dagens läge har Azure VPN Gateway stöd för 16-bitars ASN:er.

### <a name="are-there-asns-reserved-by-azure"></a>Finns det ASN:er reserverade av Azure?
Ja, följande ASN:er är reserverade av Azure för både interna och externa peerings:

* Offentliga ASN: er: 8074, 8075, 12076
* Privata ASN: er: 65515, 65517, 65518, 65519, 65520

Du kan inte ange dessa ASN:er för dina lokala VPN-enheter när du ansluter till Azure VPN-gatewayer.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>Finns det andra ASN-nummer som jag inte kan använda?
Ja, följande ASN-nummer är [reserverade av IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) och kan inte ställas in på din Azure VPN Gateway:

23456, 64496-64511, 65535-65551 och 429496729

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Kan jag använda samma ASN för både lokala VPN-nätverk och virtuella Azure-nätverk?
Nej, måste du tilldela olika ASN:er mellan dina lokala nätverk och dina virtuella Azure-nätverk om du ska ansluta dem till varandra med BGP. Azure VPN Gateway är tilldelad en standard-ASN som är 65515, oavsett om BGP är aktiverat eller inte för dina korsanslutningar. Du kan åsidosätta det här standardvärdet genom att tilldela en annan ASN när du skapar din VPN-gateway eller genom att ändra ASN efter att din gateway har skapats. Du måste tilldela dina lokala ASN:er till motsvarande Azure-lokala nätverksgatewayer.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Vilka adressprefix kommer Azure VPN-gatewayer att meddela mig?
Azure VPN-gateway kommer att meddela följande rutter till dina lokala BGP-enheter:

* Dina VNet-adressprefixer
* Adressprefixer för varje lokal nätverksgateway som är ansluten till Azure VPN-gateway
* Rutter som den lärt sig från andra BGP-peeringsessioner anslutna till Azure VPN-gatewayen, **förutom standardrutten eller -rutterna eller rutter som överlappar med ett VNet-prefix**.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Kan jag annonsera standardväg (0.0.0.0/0) till Azure VPN-gatewayer?
Ja.

Observera att detta gör att all utgående VNet-trafik skickas till din lokala plats och förhindrar att de virtuella VNet-datorerna accepterar offentlig kommunikation direkt från internet, som RDP eller SSH från internet till de virtuella datorerna.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Kan jag annonsera exakta prefix som mina virtuella nätverksprefix?

Nej, annonsering av samma prefix som något av dina virtuella nätverks adressprefix kommer att blockeras eller filtreras av Azure-plattformen. Du kan dock annonsera ett prefix som är en överordnad uppsättning av det du har i det virtuella nätverket. 

Om ditt virtuella nätverk till exempel använder adressutrymmet 10.0.0.0/16, kan du annonsera 10.0.0.0/8. Men du kan inte annonsera 10.0.0.0/16 eller 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Kan jag använda BGP med mina VNet-till-VNet-anslutningar?
Ja, du kan använda BGP för både anslutningar mellan platser och VNet-till-VNet-anslutningar.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Kan jag blanda BGP- med icke-BGP-anslutningar för mina Azure VPN- gatewayer?
Ja, du kan blanda både BGP- och icke-BGP-anslutningar för samma Azure VPN-gateway.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Stöder Azure VPN-gateway BGP-överföringsrutter?
Ja, BGP-överföringsrutter stöds, med undantaget att Azure VPN-gatewayer **INTE** annonserar ut standardrutter till andra BGP-peers. För att aktivera överföringsrutter över flera Azure VPN-gatewayer, behöver du aktivera BGP på alla mellanliggande VNet-till-VNet-anslutningar. Mer information finns i [om BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Kan jag har fler än en tunnel mellan en Azure VPN-gateway och mitt lokala nätverk?
Ja, du kan skapa fler än en S2S-VPN-tunnel mellan en Azure VPN-gateway och ditt lokala nätverk. Observera att alla dessa tunnlar räknas mot det totala antalet tunnlar för dina Azure VPN-gateways och att du måste aktivera BGP för båda tunnlarna.

Om du exempelvis har två redundanta tunnlar mellan din Azure VPN-gateway och ett av dina lokala nätverk, kommer de förbruka 2 tunnlar av din totala kvot för din Azure VPN-gateway (10 för standard och 30 för HighPerformance).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Kan jag har flera tunnlar mellan två Azure VNets med BGP?
Ja, men minst en av dina virtuella nätverksgateways måste ha en aktiv-aktiv-konfiguration.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Kan jag använda BGP för S2S VPN i en ExpressRoute/S2S-VPN samexistent konfiguration?
Ja. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Vilken adress använder Azure VPN-gateway för BGP-peer-IP?
Azure VPN-gatewayen allokerar en enda IP-adress från GatewayUndernäts-intervallet som definierats för det virtuella nätverket. Som standard är det den näst sista adressen i intervallet. Om ditt GatewaySubnet exempelvis är 10.12.255.0/27, med intervallet 10.12.255.0 till 10.12.255.31, kommer BGP-peer-IP-adressen på Azure VPN-gatewayen vara 10.12.255.30. Du hittar den här informationen när du listar Azure VPN-gatewayinformationen.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Vad ställer BGP-peer-IP-adresserna för krav på min VPN-enhet?
Din lokala BGP-peer-adress **FÅR INTE** vara densamma som den offentliga IP-adressen för din VPN-enhet. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP-adress. Det kan vara en adress som har tilldelats till loopback-gränssnittet på enheten, men observera att det inte kan vara en APIPA-adress (169.254.x.x). Ange den adressen i den motsvarande lokala nätverksgateway som representerar platsen.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Vad ska jag ange som mina adressprefix för den lokala nätverksgatewayen när jag använder BGP?
Azure-lokal nätverksgateway anger de första adressprefixen för det lokala nätverket. Med BGP, måste du allokera värdprefixet (/ 32 prefix) från din BGP-peer-IP-adress som adressutrymmet för det lokala nätverket. Om din BGP-peer-IP är 10.52.255.254, ska du ange "10.52.255.254/32" som localNetworkAddressSpace för den lokala nätverksgateway som representerar det här lokala nätverket. Det är för att se till att Azure VPN-gatewayen upprättar BGP-sessionen via S2S VPN-tunneln.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Vad bör jag lägga till på min lokala VPN-enhet för BGP-peeringsessionen?
Du bör lägga till en värdrutt för Azure BGP-peer-IP-adressen på din VPN-enhet som pekar på IPsec S2S VPN-tunneln. Om Azure VPN-peer-IP-adressen är "10.12.255.30", bör du till exempel lägga till en värdrutt för "10.12.255.30" med ett nexthop-gränssnitt för det matchande IPSec-tunnelgränssnittet på din VPN-enheten.
