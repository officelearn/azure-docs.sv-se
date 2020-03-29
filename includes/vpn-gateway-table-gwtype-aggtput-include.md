---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74828812"
---
|**Generering<br>av VPN-gateway<br>** |**Sku**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br> SSTP-anslutningar** | **P2S<br> IKEv2/OpenVPN-anslutningar** | **Prestandamått för<br>aggregerat datagenomflöde** | **BGP** | **Zon-redundant** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Basic**   | Max. 10    | Max. 128  | Stöds inte  | 100 Mbps  | Stöds inte| Inga |
|**Generation1**|**VpnGw1**  | Max. 30*   | Max. 128  | Max. 250       | 650 Mbit/s  | Stöds | Inga |
|**Generation1**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1 Gbit/s    | Stöds | Inga |
|**Generation1**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbit/s | Stöds | Inga |
|**Generation1**|**VpnGw1AZ (På andra sätt)**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbit/s  | Stöds | Ja |
|**Generation1**|**VpnGw2AZ (På andra sätt)**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbit/s    | Stöds | Ja |
|**Generation1**|**VpnGw3AZ (På andra sätt)**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbit/s | Stöds | Ja |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1,25 Gbit/s | Stöds | Inga |
|**Generation2**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 2,5 Gbit/s  | Stöds | Inga |
|**Generation2**|**VpnGw4 (På andra)**  | Max. 30*   | Max. 128  | Max. 5000      | 5 Gbit/s    | Stöds | Inga |
|**Generation2**|**VpnGw5**  | Max. 30*   | Max. 128  | Max. 10000      | 10 Gbit/s   | Stöds | Inga |
|**Generation2**|**VpnGw2AZ (På andra sätt)**| Max. 30*   | Max. 128  | Max. 500       | 1,25 Gbit/s | Stöds | Ja |
|**Generation2**|**VpnGw3AZ (På andra sätt)**| Max. 30*   | Max. 128  | Max. 1000      | 2,5 Gbit/s  | Stöds | Ja |
|**Generation2**|**VpnGw4AZ (På andra sätt)**| Max. 30*   | Max. 128  | Max. 5000      | 5 Gbit/s    | Stöds | Ja |
|**Generation2**|**VpnGw5AZ**| Max. 30*   | Max. 128  | Max. 10000      | 10 Gbit/s   | Stöds | Ja |

(*) Använd [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) om du behöver fler än 30 S2S VPN-tunnlar.

* Storleken på VPNGw SKU:er tillåts inom samma generation, förutom storleksändring av basic SKU. Basic SKU är en äldre SKU och har funktionsbegränsningar. För att kunna flytta från Basic till en annan VpnGw SKU måste du ta bort den grundläggande SKU VPN-gatewayen och skapa en ny gateway med önskad kombination av generering och SKU-storlek.

* Dessa anslutningsgränser är separata. Du kan exempelvis ha 128 SSTP-anslutningar och även 250 IKEv2-anslutningar på en VpnGw1-SKU.

* Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* På en enda tunnel kan högst 1 Gbps dataflöde uppnås. Aggregate Throughput Benchmark i tabellen ovan baseras på mätningar av flera tunnlar som aggregeras via en enda gateway. Prestandamåttet för aggregerat dataflöde för VPN Gateway är S2S + P2S kombinerat. **Om du har många P2S-anslutningar kan det påverka en S2S-anslutning negativt på grund av dataflödesbegränsningar.** Prestandamåttet för aggregerat datagenomflöde inte garanterat genomströmning på grund av villkor för Internet-trafik och dina program.

För att hjälpa våra kunder att förstå sku:ernas relativa prestanda med hjälp av olika algoritmer använde vi allmänt tillgängliga iPerf- och CTSTraffic-verktyg för att mäta prestanda. Tabellen nedan visar resultaten av prestandatester för Generation 1, VpnGw SKU: er. Som ni kan se erhålls bästa prestanda när vi använde GCMAES256-algoritm för både IPsec-kryptering och integritet. Vi fick genomsnittliga prestanda när vi använder AES256 för IPsec-kryptering och SHA256 för integritet. När vi använde DES3 för IPsec Encryption och SHA256 for Integrity fick vi lägsta prestanda.

|**Generation**|**Sku**   | **Algoritmer<br>som används** | **Dataflöde<br>observerat** | **Observerade paket<br>per sekund** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbit/s<br>500 Mbps<br>120 Mbit/s   | 58,000<br>50 000<br>50 000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbit/s<br>500 Mbps<br>120 Mbit/s | 90 000<br>80 000<br>55,000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbit/s<br>550 Mbit/s<br>120 Mbit/s | 105,000<br>90 000<br>60 000|
|**Generation1**|**VpnGw1AZ (På andra sätt)**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbit/s<br>500 Mbps<br>120 Mbit/s   | 58,000<br>50 000<br>50 000|
|**Generation1**|**VpnGw2AZ (På andra sätt)**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbit/s<br>500 Mbps<br>120 Mbit/s | 90 000<br>80 000<br>55,000|
|**Generation1**|**VpnGw3AZ (På andra sätt)**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbit/s<br>550 Mbit/s<br>120 Mbit/s | 105,000<br>90 000<br>60 000|
