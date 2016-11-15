När du skapar en virtuell nätverksgateway måste du ange vilken gateway-SKU som du vill använda. När du väljer en högre gateway-SKU allokeras fler processorer och mer nätverksbandbredd till gatewayen så att gatewayen kan hantera ett större nätverksgenomflöde till det virtuella nätverket.

VPN-gateway kan använda följande SKU:er:

* Basic
* Standard
* HighPerformance

VPN-gateway använder inte SKU för UltraPerformance-gateway. Information om UltraPerformance SKU finns i [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md)-dokumentationen.

När du väljer en SKU bör du överväga följande:

* Om du vill använda en PolicyBased VPN-typ måste du använda Basic SKU:n. PolicyBased VPN (tidigare kallade statisk routning) stöds inte på andra SKU:er.
* BGP stöds inte på Basic-SKU:n.
* ExpressRoute VPN-Gateway samexisterande konfigurationer stöds inte på Basic-SKU:n.
* S2S VPN-gateway-anslutningar av typen aktiv-aktiv kan enbart konfigureras på HighPerformance SKU.



<!--HONumber=Nov16_HO2-->


