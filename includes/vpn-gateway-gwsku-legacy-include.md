De äldre SKU:erna (gamla) för VPN-gatewayer är:

* Basic
* Standard
* HighPerformance

VPN-gateway använder inte SKU för UltraPerformance-gateway. Information om UltraPerformance SKU finns i [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md)-dokumentationen.

Tänk på följande när du arbetar med de äldre SKU:erna:

* Om du vill använda en PolicyBased VPN-typ måste du använda Basic SKU:n. PolicyBased VPN (tidigare kallade statisk routning) stöds inte på andra SKU:er.
* BGP stöds inte på Basic-SKU:n.
* ExpressRoute VPN-Gateway samexisterande konfigurationer stöds inte på Basic-SKU:n.
* S2S VPN-gateway-anslutningar av typen aktiv-aktiv kan enbart konfigureras på HighPerformance SKU.