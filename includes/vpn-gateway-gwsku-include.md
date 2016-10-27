När du skapar en virtuell nätverksgateway måste du ange vilken gateway-SKU som du vill använda. När du väljer en högre gateway-SKU allokeras fler processorer och mer nätverksbandbredd till gatewayen så att gatewayen kan hantera ett större nätverksgenomflöde till det virtuella nätverket.

VPN-gateway kan använda följande SKU:er:

- Basic
- Standard
- HighPerformance

När du väljer en SKU, bör du överväga följande begränsningar:

- Om du vill använda en PolicyBased VPN-typ måste du använda Basic gateway-SKU:n. PolicyBased VPN (tidigare kallade statisk routning) stöds inte på andra SKU:er.
- BGP stöds inte på Basic-SKU:n.
- ExpressRoute VPN-Gateway samexisterande konfigurationer stöds inte på Basic-SKU:n.


<!--HONumber=Oct16_HO3-->


