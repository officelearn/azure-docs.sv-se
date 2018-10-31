---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 03af5efcd4a37203a82db503f8bc602b33de734d
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227043"
---
|**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br> SSTP-anslutningar** | **P2S<br> IKEv2-anslutningar** | **Prestandamått för<br>aggregerat datagenomflöde** |
|---       | ---        | ---       | ---            | ---       |
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbit/s  |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbit/s    |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbit/s |
|**Basic** | Max. 10    | Max. 128  | Stöds inte  | 100 Mbit/s  | 

(*) Använd [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) om du behöver fler än 30 S2S VPN-tunnlar.

* Prestandamåttet för aggregerat dataflöde för VPN Gateway är S2S + P2S kombinerat. **Om du har många P2S-anslutningar kan det påverka en S2S-anslutning negativt på grund av dataflödesbegränsningar.** Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Det är inte garanterad genomströmning på grund av villkor för Internet-trafik och dina program.

* Dessa anslutningsgränser är separata. Du kan exempelvis ha 128 SSTP-anslutningar och även 250 IKEv2-anslutningar på en VpnGw1-SKU.

* Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1 VpnGw2 och VpnGw3 stöds endast för VPN-gatewayer som använder Resource Manager-distributionsmodellen.
