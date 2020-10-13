---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 32e4658af48a0ae3bde08de18cf1d8204878d671
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91024924"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Stöds BGP på alla Azure VPN Gateway-SKU:er?
BGP stöds på alla Azure VPN Gateway SKU: er förutom Basic SKU.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Kan jag använda BGP med Azure principbaserade VPN-gatewayer?
Nej, BGP stöds bara på route-baserade VPN-gatewayer.

### <a name="what-asn-autonomous-system-numbers-can-i-use"></a>Vilka ASN (autonoma system nummer) kan jag använda?
Du kan använda din egen offentliga ASN: er eller privata ASN: er för både dina lokala nätverk och virtuella Azure-nätverk **förutom** de intervall som reserver ATS av Azure eller IANA:

> [!IMPORTANT]
>
> Följande ASN: er är reserverade för Azure eller IANA:
> * ASN: er reserverade av Azure:
>    * Offentliga ASN:er: 8074, 8075, 12076
>    * Privata ASN:er: 65515, 65517, 65518, 65519, 65520
> * ASN: er [reserverad av IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
>    * 23456, 64496-64511, 65535-65551 och 429496729
>
> Du kan inte ange dessa ASN: er för dina lokala VPN-enheter när du ansluter till Azure VPN-gatewayer.
>

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>Kan jag använda 32-bitars (4-byte) ASN: er (autonoma system nummer)?
Ja, Azure VPN-gatewayer stöder nu 32-bitars (4 byte) ASN: er. Använd PowerShell/CLI/SDK för att konfigurera med hjälp av ASN i decimal format.

### <a name="what-private-asns-can-i-use"></a>Vilken privat ASN: er kan jag använda?
Det användbara intervallet av privata ASN: er som kan användas är:

* 64512-65514, 65521-65534

Dessa ASN: er är inte reserverade för IANA eller Azure för användning och kan därför användas för att tilldela Azure-VPN Gateway.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Vilken adress använder Azure VPN-gateway för BGP-peer-IP?

* Som standard allokerar Azure VPN-gatewayen en enskild IP-adress från GatewaySubnet-intervallet för VPN-gatewayer med aktiva vänte läge eller två IP-adresser för aktiva, aktiva VPN-gatewayer. De här adresserna tilldelas automatiskt när du skapar VPN-gatewayen. Du kan få de faktiska BGP-IP-adresserna som allokeras genom att använda PowerShell (Get-AzVirtualNetworkGateway, leta efter egenskapen "bgpPeeringAddress") eller i Azure Portal (under egenskapen "Konfigurera BGP ASN" på sidan gateway-konfiguration).

* Om dina lokala VPN-routrar använder **APIPA** IP-adresser (169.254. x. x) som BGP-IP-adresser, måste du ange ytterligare en **Azure APIPA BGP-IP-adress** på din Azure VPN-gateway. Azure VPN-gatewayen väljer den APIPA-adress som ska användas med den lokala APIPA BGP-peer som anges i den lokala Nätverksgatewayen eller privat IP-adress för icke-APIPA lokal BGP-peer. Mer information finns i [Configure BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Vad ställer BGP-peer-IP-adresserna för krav på min VPN-enhet?
Din lokala BGP-peer-adress **får inte** vara samma som den offentliga IP-adressen för din VPN-enhet eller från det virtuella nätverkets adress utrymme för VPN gateway. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP-adress. Det kan vara en adress som tilldelats till loopback-gränssnittet på enheten, antingen en vanlig IP-adress eller en APIPA-adress. Om din enhet använder APIPA-adressen för BGP måste du ange en APIPA BGP-IP-adress på din Azure VPN-gateway enligt beskrivningen i [Konfigurera BGP](../articles/vpn-gateway/bgp-howto.md). Ange den adressen i den motsvarande lokala nätverksgateway som representerar platsen.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Vad ska jag ange som mina adressprefix för den lokala nätverksgatewayen när jag använder BGP?

> [!IMPORTANT]
>
>Detta är en ändring från det tidigare dokumenterade kravet. Om du använder BGP för en anslutning bör du lämna fältet **adress utrymme** ***tomt*** för motsvarande resurs för en lokal nätverksgateway. Azure VPN-gateway kommer internt att lägga till en värdväg till den lokala BGP-peer-IP-adressen via IPsec-tunneln. Lägg **inte** till vägen/32 i fältet adress utrymme. Det är redundant och om du använder en APIPA-adress som den lokala VPN-enhetens BGP IP kan den inte läggas till i det här fältet. Om du lägger till andra prefix i fältet adress utrymme läggs de till som **statiska vägar** på Azure VPN-gatewayen, förutom de vägar som har lärts via BGP.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Kan jag använda samma ASN för både lokala VPN-nätverk och virtuella Azure-nätverk?
Nej, måste du tilldela olika ASN:er mellan dina lokala nätverk och dina virtuella Azure-nätverk om du ska ansluta dem till varandra med BGP. Azure VPN Gateway är tilldelad en standard-ASN som är 65515, oavsett om BGP är aktiverat eller inte för dina korsanslutningar. Du kan åsidosätta det här standardvärdet genom att tilldela en annan ASN när du skapar din VPN-gateway eller genom att ändra ASN efter att din gateway har skapats. Du måste tilldela dina lokala ASN:er till motsvarande Azure-lokala nätverksgatewayer.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Vilka adressprefix kommer Azure VPN-gatewayer att meddela mig?
Azure VPN-gateway kommer att meddela följande rutter till dina lokala BGP-enheter:

* Dina VNet-adressprefixer
* Adressprefixer för varje lokal nätverksgateway som är ansluten till Azure VPN-gateway
* Rutter som den lärt sig från andra BGP-peeringsessioner anslutna till Azure VPN-gatewayen, **förutom standardrutten eller -rutterna eller rutter som överlappar med ett VNet-prefix**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Hur många prefix kan jag annonsera till Azure VPN-gatewayen?
Vi har stöd för upp till 4000 prefix. BGP-sessionen kommer att tas bort om antalet prefix överskrider gränsen.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Kan jag annonsera standardväg (0.0.0.0/0) till Azure VPN-gatewayer?
Ja.

Observera att detta framtvingar all utgående trafik i VNet mot din lokala plats och hindrar de virtuella VNet-datorerna från att acceptera offentlig kommunikation från Internet direkt, sådan RDP eller SSH från Internet till de virtuella datorerna.

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
Ja, du kan skapa fler än en S2S-VPN-tunnel mellan en Azure VPN-gateway och ditt lokala nätverk. Observera att alla dessa tunnlar räknas mot det totala antalet tunnlar för dina Azure VPN-gatewayer och du måste aktivera BGP i båda tunnlarna.

Om du till exempel har två redundanta tunnlar mellan din Azure VPN-gateway och ett av dina lokala nätverk, kommer de att förbruka 2 tunnlar av den totala kvoten för din Azure VPN-gateway.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Kan jag har flera tunnlar mellan två Azure VNets med BGP?
Ja, men minst en av dina virtuella nätverksgateways måste ha en aktiv-aktiv-konfiguration.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-coexistence-configuration"></a>Kan jag använda BGP för S2S VPN i en konfiguration för ExpressRoute/S2S VPN-samexistens?
Ja. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Vad bör jag lägga till på min lokala VPN-enhet för BGP-peeringsessionen?
Du bör lägga till en värdrutt för Azure BGP-peer-IP-adressen på din VPN-enhet som pekar på IPsec S2S VPN-tunneln. Om Azure VPN-peer-IP-adressen är "10.12.255.30", bör du till exempel lägga till en värdrutt för "10.12.255.30" med ett nexthop-gränssnitt för det matchande IPSec-tunnelgränssnittet på din VPN-enheten.
