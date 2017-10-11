Följande tabell visar gateway-typerna och beräknat aggregerat dataflöde efter gateway-SKU. Den här tabellen gäller för Resource Manager och klassiska distributionsmodeller. 

Prissättningen skiljer sig åt mellan gateway-SKU:er. Mer information finns i [Prissättning för VPN-gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).

Observera att UltraPerformance gateway-SKU inte visas i den här tabellen. Information om UltraPerformance SKU finns i [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md)-dokumentationen.

|  | **VPN Gateway-genomflöde (1)** | **VPN Gateway, max. IPsec-tunnlar (2)** | **ExpressRoute-gateway, genomflöde** | **VPN-gateway och ExpressRoute samexisterar** |
| --- | --- | --- | --- | --- |
| **Basic SKU (3)(5)(6)** |100 Mbit/s |10 |500 Mbit/s (6) |Nej |
| **Standard SKU (4)(5)** |100 Mbit/s |10 |1000 Mbps |Ja |
| **High Performance SKU (4)** |200 Mbit/s |30 |2000 Mbps |Ja |


(1) VPN-genomströmning är en grov uppskattning baserat på mätningar mellan VNet i samma Azure-region. Det här är ingen garanterad genomströmning för anslutningar mellan olika platser via Internet. Värdet utgör dock högsta möjliga genomflöde.

(2) Antalet tunnlar refererar till ruttbaserad VPN. En principbaserad VPN stöder bara en VPN-tunnel av typen plats-till-plats.

(3) BGP stöds inte för Basic-SKU:n.

(4) Principbaserade VPN:er stöds inte för den här SKU:n. De stöds bara för Basic-SKU:n.

(5) S2S VPN-Gateway-anslutningar av typen aktiv-aktiv stöds inte för denna SKU. Aktiv-aktiv stöds enbart på HighPerformance SKU.

(6) grundläggande SKU är föråldrad för användning med ExpressRoute.
