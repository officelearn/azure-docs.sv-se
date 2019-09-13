---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919747"
---
|**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br> SSTP-anslutningar** | **P2s<br> IKEv2/OpenVPN-anslutningar** | **Prestandamått för<br>aggregerat datagenomflöde** | **BGP** | **Zone-redundant** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Basic** | Max. 10    | Max. 128  | Stöds inte  | 100 Mbit/s  | Stöds inte| Nej |
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbit/s  | Stöds | Nej |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbit/s    | Stöds | Nej |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbit/s | Stöds | Nej |
|**VpnGw1AZ**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbit/s  | Stöds | Ja |
|**VpnGw2AZ**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbit/s    | Stöds | Ja |
|**VpnGw3AZ**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbit/s | Stöds | Ja |

(*) Använd [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) om du behöver fler än 30 S2S VPN-tunnlar.

* Dessa anslutningsgränser är separata. Du kan exempelvis ha 128 SSTP-anslutningar och även 250 IKEv2-anslutningar på en VpnGw1-SKU.

* Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2, VpnGw3, VpnGw1AZ, VpnGw2AZ och VpnGw3AZ stöds endast för VPN-gatewayer med distributions modellen Resource Manager.

* Basic SKU:n betraktas som en äldre SKU. Basic SKU:n har vissa funktionsbegränsningar. Du kan inte ändra storlek på en gateway som använder en Basic SKU till en av de nya gateway-SKU:erna. Du måste istället ändra till en ny SKU, vilket innebär att ta bort och återskapa din VPN-gateway. Kontrollera att funktionen du behöver stöds innan du använder Basic SKU:n.

* Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Prestandamåttet för aggregerat dataflöde för VPN Gateway är S2S + P2S kombinerat. **Om du har många P2S-anslutningar kan det påverka en S2S-anslutning negativt på grund av dataflödesbegränsningar.** Prestandamåttet för aggregerat datagenomflöde inte garanterat genomströmning på grund av villkor för Internet-trafik och dina program.

* För att hjälpa våra kunder att förstå den relativa prestandan hos VpnGw SKU: er använde vi offentligt tillgängliga iPerf-och CTSTraffic-verktyg för att mäta prestanda. I tabellen nedan visas resultatet av prestandatester som använder olika algoritmer. Som du kan se erhålls bästa prestanda när vi använde GCMAES256-algoritm för kryptering och integritet i både IPsec och integritet. Vi fick genomsnitts prestanda när du använder AES256 för IPsec-kryptering och SHA256 för integritet. Vi fick lägsta prestanda när vi använde DES3 för IPsec-kryptering och SHA256 för integritet.

|**SKU**   | **Använda<br>algoritmer** | **Data<br>flöde som observerats** | **Paket per sekund<br>som observerats** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbit/s<br>500 Mbit/s<br>120 Mbit/s   | 58 000<br>50,000<br>50,000|
|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbit/s<br>500 Mbit/s<br>120 Mbit/s | 90,000<br>80,000<br>55 000|
|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbit/s<br>550 Mbit/s<br>120 Mbit/s | 105 000<br>90,000<br>60,000|
|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbit/s<br>500 Mbit/s<br>120 Mbit/s   | 58 000<br>50,000<br>50,000|
|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbit/s<br>500 Mbit/s<br>120 Mbit/s | 90,000<br>80,000<br>55 000|
|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbit/s<br>550 Mbit/s<br>120 Mbit/s | 105 000<br>90,000<br>60,000|
