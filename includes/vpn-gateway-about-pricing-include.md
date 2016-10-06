Du betalar för två saker: beräkningskostnaderna per timme för den virtuella nätverksgatewayen och för utgående dataöverföring från den virtuella nätverksgatewayen. Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

**Beräkningskostnader för virtuell nätverksgateway**<br>Varje virtuell nätverksgateway har ett timpris för beräkningar. Priset grundas på den gateway-SKU du anger när du skapar en virtuell nätverksgateway. Kostnaden är för själva gatewayen och läggs till utöver den dataöverföring som går via gatewayen.

**Kostnader för överföring av data**<br>Kostnaderna för överföring av data beräknas baserat på utgående trafik från den virtuella nätverksgatewayen (källan).

- Om du skickar trafik till din lokala VPN-enhet debiteras du priset för utgående dataöverföring via Internet.
- Om du skickar trafik mellan virtuella nätverk i olika regioner baseras priset på region.
- Om du skickar trafik endast mellan virtuella nätverk i samma region tillkommer inga kostnader för data. Trafik mellan VNets i samma region är kostnadsfri.

<!--HONumber=Sep16_HO3-->


