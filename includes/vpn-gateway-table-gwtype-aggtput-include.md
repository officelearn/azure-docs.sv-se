Azure erbjuder följande SKU: er för VPN-gatewayen:

|**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br>-anslutningar** | **Prestandamått för<br>aggregerat datagenomflöde** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Max. 30                         | Max. 128               | 650 Mbit/s                    |
|**VpnGw2**| Max. 30                         | Max. 128               | 1 Gbit/s                      |
|**VpnGw3**| Max. 30                         | Max. 128               | 1,25 Gbit/s                   |
|**Basic** | Max. 10                         | Max. 128               | 100 Mbit/s                    | 
|          |                                 |                        |                             | 

- Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Det är inte garanterad genomströmning på grund av villkor för Internet-trafik och dina program.

- Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

- Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).