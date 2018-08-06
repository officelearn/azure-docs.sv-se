---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4cbbb64489acf23c1248e35269e1441dd2a6878e
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513762"
---
|**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br>-anslutningar** | **Prestandamått för<br>aggregerat datagenomflöde** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Max. 30*                         | Max. 128\*\*             | 650 Mbit/s                    |
|**VpnGw2**| Max. 30*                         | Max. 128\*\*             | 1 Gbit/s                      |
|**VpnGw3**| Max. 30*                         | Max. 128\*\*             | 1,25 Gbit/s                   |
|**Basic** | Max. 10                         | Max. 128               | 100 Mbit/s                    | 

* (*) Använd [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) om du behöver fler än 30 S2S VPN-tunnlar.

* (\*\*) Kontakta supporten om du behöver ytterligare anslutningar. Detta gäller endast IKEv2. Antalet anslutningar för SSTP kan inte utökas.

* Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Det är inte garanterad genomströmning på grund av villkor för Internet-trafik och dina program.

* Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1 VpnGw2 och VpnGw3 stöds endast för VPN-gatewayer som använder Resource Manager-distributionsmodellen.