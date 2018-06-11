---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc42697f756ec75d9a8f2c20c99b28b2f7886ca1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670283"
---
|**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br>-anslutningar** | **Prestandamått för<br>aggregerat datagenomflöde** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Max. 30                         | Max. 128*              | 650 Mbit/s                    |
|**VpnGw2**| Max. 30                         | Max. 128*              | 1 Gbit/s                      |
|**VpnGw3**| Max. 30                         | Max. 128*              | 1,25 Gbit/s                   |
|**Basic** | Max. 10                         | Max. 128               | 100 Mbit/s                    | 

*Kontakta supporten om du behöver ytterligare anslutningar
- Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Det är inte garanterad genomströmning på grund av villkor för Internet-trafik och dina program.

- Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

- Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

- VpnGw1 VpnGw2 och VpnGw3 stöds endast för VPN-gatewayer som använder Resource Manager-distributionsmodellen.