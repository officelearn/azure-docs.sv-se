|    | **VPN Gateway-genomströmning (1)** | **VPN Gateway, max. IPsec-tunnlar (2)** | **ExpressRoute-gateway, genomflöde** | **VPN Gateway och ExpressRoute samexisterar**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **Grundläggande SKU**              |  100 Mbps | 10                         |  500 Mbps                           | Nej   |
| **Standard-SKU**           |  100 Mbps | 10                         | 1000 Mbps                           | Ja  |
| **Högpresterande SKU**   | 200 Mbps  | 30                         | 2000 Mbps                           | Ja  |

- (1) VPN-genomströmning är en grov uppskattning baserat på mätningar mellan VNet i samma Azure-region. Det är ingen garanti på vad du kan uppnå med anslutningar mellan platser över Internet, men bör användas som ett högsta möjliga mått.
- (2) Antalet tunnlar innebär ruttbaserad VPN nedan. En principbaserad VPN stöder bara en plats-till-plats-VPN tunnel


<!--HONumber=Jun16_HO2-->


