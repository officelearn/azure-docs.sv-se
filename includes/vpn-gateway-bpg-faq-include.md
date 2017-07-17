### Stöds BGP på alla Azure VPN-gateway-SKU:er?
<a id="is-bgp-supported-on-all-azure-vpn-gateway-skus" class="xliff"></a>
Nej, BGP stöds på Azure **Standard** och **HighPerformance** VPN-gatewayer. **Basic** SKU stöds inte.

### Kan jag använda BGP med Azure principbaserade VPN-gatewayer?
<a id="can-i-use-bgp-with-azure-policy-based-vpn-gateways" class="xliff"></a>
Nej, BGP stöds bara på route-baserade VPN-gatewayer.

### Kan jag använda privata ASN:er (Autonomous System Numbers)?
<a id="can-i-use-private-asns-autonomous-system-numbers" class="xliff"></a>
Ja, du kan använda dina egna offentliga ASN:er eller privata ASN:er för både dina lokala nätverk och virtuella Azure-nätverk.

### Finns det ASN:er reserverade av Azure?
<a id="are-there-asns-reserved-by-azure" class="xliff"></a>
Ja, följande ASN:er är reserverade av Azure för både interna och externa peerings:

* Offentliga ASN:er: 8075, 8076, 12076
* Privata ASN:er: 65515, 65517, 65518, 65519, 65520

Du kan inte ange dessa ASN:er för dina lokala VPN-enheter när du ansluter till Azure VPN-gatewayer.

### Kan jag använda samma ASN för både lokala VPN-nätverk och virtuella Azure-nätverk?
<a id="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets" class="xliff"></a>
Nej, måste du tilldela olika ASN:er mellan dina lokala nätverk och dina virtuella Azure-nätverk om du ska ansluta dem till varandra med BGP. Azure-VPN-gatewayer är tilldelade en standard-ASN som är 65515, oavsett om BGP är aktiverat eller inte för dina korsanslutningar. Du kan åsidosätta det här standardvärdet genom att tilldela en annan ASN när du skapar din VPN-gateway eller genom att ändra ASN efter att din gateway har skapats. Du måste tilldela dina lokala ASN:er till motsvarande Azure-lokala nätverksgatewayer.

### Vilka adressprefix kommer Azure VPN-gatewayer att meddela mig?
<a id="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me" class="xliff"></a>
Azure VPN-gateway kommer att meddela följande rutter till dina lokala BGP-enheter:

* Dina VNet-adressprefixer
* Adressprefixer för varje lokal nätverksgateway som är ansluten till Azure VPN-gateway
* Rutter som den lärt sig från andra BGP-peeringsessioner anslutna till Azure VPN-gatewayen, **förutom standardrutten eller -rutterna eller rutter som överlappar med ett VNet-prefix**.

### Kan jag annonsera standardväg (0.0.0.0/0) till Azure VPN-gatewayer?
<a id="can-i-advertise-default-route-00000-to-azure-vpn-gateways" class="xliff"></a>
Ja.

Observera att detta gör att all utgående VNet-trafik skickas till din lokala plats och förhindrar att de virtuella VNet-datorerna accepterar offentlig kommunikation direkt från internet, som RDP eller SSH från internet till de virtuella datorerna.

### Kan jag annonsera exakta prefix som mina virtuella nätverksprefix?
<a id="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes" class="xliff"></a>

Nej, annonsering av samma prefix som något av dina virtuella nätverks adressprefix kommer att blockeras eller filtreras av Azure-plattformen. Du kan dock annonsera ett prefix som är en överordnad uppsättning av det du har i det virtuella nätverket. 

Om ditt virtuella nätverk till exempel använder adressutrymmet 10.0.0.0/16, kan du annonsera 10.0.0.0/8. Men du kan inte annonsera 10.0.0.0/16 eller 10.0.0.0/24.

### Kan jag använda BGP med mina VNet-till-VNet-anslutningar?
<a id="can-i-use-bgp-with-my-vnet-to-vnet-connections" class="xliff"></a>
Ja, du kan använda BGP för både anslutningar mellan platser och VNet-till-VNet-anslutningar.

### Kan jag blanda BGP- med icke-BGP-anslutningar för mina Azure VPN- gatewayer?
<a id="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways" class="xliff"></a>
Ja, du kan blanda både BGP- och icke-BGP-anslutningar för samma Azure VPN-gateway.

### Stöder Azure VPN-gateway BGP-överföringsrutter?
<a id="does-azure-vpn-gateway-support-bgp-transit-routing" class="xliff"></a>
Ja, BGP-överföringsrutter stöds, med undantaget att Azure VPN-gatewayer **INTE** annonserar ut standardrutter till andra BGP-peers. För att aktivera överföringsrutter över flera Azure VPN-gatewayer, behöver du aktivera BGP på alla mellanliggande VNet-till-VNet-anslutningar.

### Kan jag ha fler än en tunnel mellan en Azure VPN-gateway och mitt lokala nätverk?
<a id="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network" class="xliff"></a>
Ja, du kan skapa fler än en S2S-VPN-tunnel mellan en Azure VPN-gateway och ditt lokala nätverk. Observera att alla dessa tunnlar räknas mot det totala antalet tunnlar för dina Azure VPN-gateways och att du måste aktivera BGP för båda tunnlarna.

Om du exempelvis har två redundanta tunnlar mellan din Azure VPN-gateway och ett av dina lokala nätverk, kommer de förbruka 2 tunnlar av din totala kvot för din Azure VPN-gateway (10 för standard och 30 för HighPerformance).

### Kan jag ha flera tunnlar mellan två Azure VNets med BGP?
<a id="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp" class="xliff"></a>
Ja, men minst en av dina virtuella nätverksgateways måste ha en aktiv-aktiv-konfiguration.

### Kan jag använda BGP för S2S VPN i en ExpressRoute/S2S-VPN samexistent konfiguration?
<a id="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration" class="xliff"></a>
Ja. 

### Vilken adress använder Azure VPN-gateway för BGP-peer-IP?
<a id="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip" class="xliff"></a>
Azure VPN-gatewayen allokerar en enda IP-adress från GatewayUndernäts-intervallet som definierats för det virtuella nätverket. Som standard är det den näst sista adressen i intervallet. Om ditt GatewaySubnet exempelvis är 10.12.255.0/27, med intervallet 10.12.255.0 till 10.12.255.31, kommer BGP-peer-IP-adressen på Azure VPN-gatewayen vara 10.12.255.30. Du hittar den här informationen när du listar Azure VPN-gatewayinformationen.

### Vad ställer BGP-peer-IP-adresserna för krav på min VPN-enhet?
<a id="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device" class="xliff"></a>
Din lokala BGP-peer-adress **FÅR INTE** vara densamma som den offentliga IP-adressen för din VPN-enhet. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP-adress. Det kan vara en adress som tilldelats till loopback-gränssnittet på enheten. Ange den adressen i den motsvarande lokala nätverksgateway som representerar platsen.

### Vad ska jag ange som mina adressprefix för den lokala nätverksgatewayen när jag använder BGP?
<a id="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp" class="xliff"></a>
Azure-lokal nätverksgateway anger de första adressprefixen för det lokala nätverket. Med BGP, måste du allokera värdprefixet (/ 32 prefix) från din BGP-peer-IP-adress som adressutrymmet för det lokala nätverket. Om din BGP-peer-IP är 10.52.255.254, ska du ange "10.52.255.254/32" som localNetworkAddressSpace för den lokala nätverksgateway som representerar det här lokala nätverket. Det är för att se till att Azure VPN-gatewayen upprättar BGP-sessionen via S2S VPN-tunneln.

### Vad bör jag lägga till på min lokala VPN-enhet för BGP-peeringsessionen?
<a id="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session" class="xliff"></a>
Du bör lägga till en värdrutt för Azure BGP-peer-IP-adressen på din VPN-enhet som pekar på IPsec S2S VPN-tunneln. Om Azure VPN-peer-IP-adressen är "10.12.255.30", bör du till exempel lägga till en värdrutt för "10.12.255.30" med ett nexthop-gränssnitt för det matchande IPSec-tunnelgränssnittet på din VPN-enhet.

