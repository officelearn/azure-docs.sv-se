Azure erbjuder följande SKU: er för VPN-gatewayen:

|**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br>-anslutningar** | **Sammanställt<br>genomflöde** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Max. 30                         | Max. 128               | 500 Mbit/s                    |
|**VpnGw2**| Max. 30                         | Max. 128               | 1 Gbit/s                      |
|**VpnGw3**| Max. 30                         | Max. 128               | 1,25 Gbit/s                   |
|**Basic** | Max. 10                         | Max. 128               | 100 Mbit/s                    | 
|          |                                 |                        |                             | 

- Genomströmning baseras på mätningar av flera tunnlar som går genom en enda gateway. Det är inte garanterad genomströmning på grund av villkor för Internet-trafik och dina program.

- Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

- Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/en-us/support/legal/sla/vpn-gateway/).