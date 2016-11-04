|  | **Punkt-till-plats** | **Plats-till-plats** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure-tjänster som stöds** |Cloud Services och Virtual Machines |Cloud Services och Virtual Machines |[Tjänstlista](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Vanliga bandbredder** |Vanligtvis < 100 Mbps sammanlagt |Vanligtvis < 100 Mbps sammanlagt |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Protokoll som stöds** |SSTP (Secure Sockets Tunneling Protocol) |IPsec |Direktanslutning över VLAN, NSP:er VPN-teknologier (MPLS, VPLS,...) |
| **Routning** |Ruttbaserad (dynamisk) |Vi stöder principbaserad (statisk routning) och ruttbaserad (dynamisk routning VPN) |BGP |
| **Anslutningsåterhämtning** |aktiv-passiv |aktiv-passiv |aktiv-aktiv |
| **Vanligt användningsfall** |Prototyper, dev/test/testlabb-scenarier för molntjänster och virtuella datorer |Dev/test/testlabb-scenarier och småskaliga produktionsbelastningar för molntjänster och virtuella datorer |Åtkomst till alla Azure-tjänster (validerad lista), Enterprise-klass och verksamhetskritiska arbetsbelastningar, säkerhetskopiering, Big Data, Azure som en DR-plats |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Priser** |[Priser](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Priser](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Priser](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Teknisk dokumentation** |[VPN Gateway-dokumentation](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[VPN Gateway-dokumentation](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[ExpressRoute dokumentation](https://azure.microsoft.com/documentation/services/expressroute/) |
| **VANLIGA FRÅGOR OCH SVAR** |[Vanliga frågor och svar för VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar för VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar för ExpressRoute](../articles/expressroute/expressroute-faqs.md) |

<!--HONumber=Jun16_HO2-->


