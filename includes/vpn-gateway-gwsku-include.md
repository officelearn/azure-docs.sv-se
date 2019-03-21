---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/20/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b1a9d93d9fccf02ba1517e429625150736e539e9
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305212"
---
När du skapar en virtuell nätverksgateway måste du ange vilken gateway-SKU som du vill använda. Välj den SKU som uppfyller dina krav baserat på typerna av arbetsbelastning, dataflöden, funktioner och serviceavtal. Vnet-gateway SKU: er i Azure Availability Zones, se [Azure Availability Zones Gateway SKU: er](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="benchmark"></a>Gateway-SKU:er efter tunnel, anslutning och dataflöde

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>Gateway-SKU: er av funktionsuppsättning

Den nya VPN-gatewayen SKU: er förenklar funktionsuppsättningarna gateway:

| **SKU**| **Funktioner**|
| ---    | ---         |
|**Grundläggande** (*)   | **Ruttbaserad VPN**: 10 tunnlar för S2S/anslutningar. Ingen RADIUS-autentisering för P2S; Ingen IKEv2 för P2S<br>**Principbaserad VPN**: (IKEv1): 1 S2S-anslutning tunneln. Ingen P2S|
| **VpnGw1, VpnGw2 och VpnGw3** | **Ruttbaserad VPN**: upp till 30 tunnlar (*), P2S, BGP, aktiv-aktiv, anpassade IPsec/IKE-principer, ExpressRoute/VPN samexistens |
|        |             |

( * ) Du kan konfigurera ”PolicyBasedTrafficSelectors” för att ansluta en ruttbaserad VPN-gateway (VpnGw1 VpnGw2, VpnGw3) till flera lokala principbaserade brandväggsenheter. Referera till [Ansluta VPN-gatewayer till flera lokala principbaserad VPN-enheter med hjälp av PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) för mer information.

(\*\*) Basic SKU anses vara en äldre SKU. Basic SKU:n har vissa funktionsbegränsningar. Du kan inte ändra storlek på en gateway som använder en Basic SKU till en av de nya gateway-SKU:erna. Du måste istället ändra till en ny SKU, vilket innebär att ta bort och återskapa din VPN-gateway.

###  <a name="workloads"></a>Gateway-SKU: er - produktion vs. Arbetsbelastningar för utvecklingstest

På grund av skillnader i serviceavtal och funktioner rekommenderar vi följande SKU: er för produktion och utveckling och testning:

| **Arbetsbelastning**                       | **SKU: er**               |
| ---                                | ---                    |
| **Produktion, kritiska arbetsbelastningar** | VpnGw1 VpnGw2, VpnGw3 |
| **Utv-test eller konceptbevis**   | Basic (*)                 |
|                                    |                        |

(\*\*) Basic SKU anses vara en äldre SKU och har funktionsbegränsningar. Kontrollera att funktionen du behöver stöds innan du använder Basic SKU:n.

Om du använder de gamla SKU: er (äldre) är rekommendationerna för produktion-SKU Standard och HighPerformance. Information och anvisningar om hur gamla SKU: er finns i [Gateway SKU: er (äldre)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
