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
ms.openlocfilehash: fad9b990b6ff1021efdaf8aadeb1e19d8a55871d
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39138055"
---
|**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br>-anslutningar** | **Prestandamått för<br>aggregerat datagenomflöde** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Max. 30*                         | Max. 128**             | 650 Mbit/s                    |
|**VpnGw2**| Max. 30*                         | Max. 128**             | 1 Gbit/s                      |
|**VpnGw3**| Max. 30*                         | Max. 128**             | 1,25 Gbit/s                   |
|**Basic** | Max. 10                         | Max. 128               | 100 Mbit/s                    | 

* (*) Använd [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) om du behöver fler än 30 S2S VPN-tunnlar.

* (**) Kontakta supporten om du behöver ytterligare anslutningar

* Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Det är inte garanterad genomströmning på grund av villkor för Internet-trafik och dina program.

* Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1 VpnGw2 och VpnGw3 stöds endast för VPN-gatewayer som använder Resource Manager-distributionsmodellen.