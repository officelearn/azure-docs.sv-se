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
ms.openlocfilehash: c9457e51858d4a073d8baffdd435c8100d95d566
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
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

- VpnGw1, VpnGw2 och VpnGw3 stöds för VPN-gatewayer med den Resource Manager distributionsmodellen.