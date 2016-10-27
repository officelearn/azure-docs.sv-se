|    | **VPN Gateway-genomströmning (1)** | **VPN Gateway, max. IPsec-tunnlar (2)** | **ExpressRoute-gateway, genomflöde** | **VPN Gateway och ExpressRoute samexisterar**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **Basic SKU (3)**              |  100 Mbit/s | 10                         |  500 Mbps                           | Nej   |
| **Standard-SKU (4)**           |  100 Mbit/s | 10                         | 1000 Mbps                           | Ja  |
| **Högpresterande SKU (4)**   | 200 Mbit/s  | 30                         | 2000 Mbps                           | Ja  |

- (1) VPN-genomströmning är en grov uppskattning baserat på mätningar mellan VNet i samma Azure-region. Det här är ingen garanterad genomströmning för anslutningar mellan olika platser via Internet. Värdet utgör dock högsta möjliga genomflöde.
- (2) Antalet tunnlar refererar till ruttbaserad VPN. En principbaserad VPN stöder bara en VPN-tunnel av typen plats-till-plats.
- (3) BGP stöds inte för Basic-SKU:n.
- (4) Principbaserade VPN:er stöds inte för den här SKU:n. De stöds bara för Basic-SKU:n.

<!--HONumber=Oct16_HO3-->


