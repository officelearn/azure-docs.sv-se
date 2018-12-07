---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 555a8e3e92dc1d12cb7c6d6e06d2511f15a2c862
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52973127"
---
|**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br> SSTP-anslutningar** | **P2S<br> IKEv2-anslutningar** | **Prestandamått för<br>aggregerat datagenomflöde** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**Basic** | Max. 10    | Max. 128  | Stöds inte  | 100 Mbit/s  | Stöds inte|
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbit/s  | Stöds |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbit/s    | Stöds |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbit/s | Stöds |


(*) Använd [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) om du behöver fler än 30 S2S VPN-tunnlar.

* Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Prestandamåttet för aggregerat dataflöde för VPN Gateway är S2S + P2S kombinerat. **Om du har många P2S-anslutningar kan det påverka en S2S-anslutning negativt på grund av dataflödesbegränsningar.** Prestandamåttet för aggregerat datagenomflöde inte garanterat genomströmning på grund av villkor för Internet-trafik och dina program.

* Dessa anslutningsgränser är separata. Du kan exempelvis ha 128 SSTP-anslutningar och även 250 IKEv2-anslutningar på en VpnGw1-SKU.

* Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1 VpnGw2 och VpnGw3 stöds endast för VPN-gatewayer som använder Resource Manager-distributionsmodellen.

* Basic SKU:n betraktas som en äldre SKU. Basic SKU:n har vissa funktionsbegränsningar. Du kan inte ändra storlek på en gateway som använder en Basic SKU till en av de nya gateway-SKU:erna. Du måste istället ändra till en ny SKU, vilket innebär att ta bort och återskapa din VPN-gateway. Kontrollera att funktionen du behöver stöds innan du använder Basic SKU:n.
