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
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74085279"
---
När du skapar en virtuell nätverksgateway måste du ange vilken gateway-SKU som du vill använda. Välj den SKU som uppfyller dina krav baserat på typerna av arbetsbelastning, dataflöden, funktioner och serviceavtal. För SKU:er för virtuell nätverksgateway i Azure-tillgänglighetszoner finns i [Azure Availability Zones gateway SKU: er](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Gateway-SKU:er efter tunnel, anslutning och dataflöde

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKU:er (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 och VpnGw5AZ) stöds endast för resurshanterarens distributionsmodell. Klassiska virtuella nätverk bör fortsätta att använda de gamla (äldre) SKU:erna.
>  * Information om hur du arbetar med de äldre SKU:erna för gateway (Basic, Standard och HighPerformance) finns i [Arbeta med SKU:er (äldre SKU:er för VPN-gateway)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * För ExpressRoute gateway SKU: [er, se Virtuella nätverksgateways för ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>Gateway SKU:er efter funktionsuppsättning

De nya SKU:erna för VPN-gateway effektiviserar de funktionsuppsättningar som erbjuds på gateways:

| **Sku**| **Funktioner**|
| ---    | ---         |
|**Grundläggande** (**)   | **Ruttbaserad VPN:** 10 tunnlar för S2S/anslutningar; Ingen RADIUS-autentisering för P2S; ingen IKEv2 för P2S<br>**Principbaserad VPN**: (IKEv1): 1 S2S/anslutningstunnel; ingen P2S|
| **Alla Generation1 och Generation2 SKU utom Basic** | **Ruttbaserad VPN:** upp till 30 tunnlar (*), P2S, BGP, aktiv,aktiv, anpassad IPsec/IKE-policy, ExpressRoute/VPN-samexistens |
|        |             |

(*) Du kan konfigurera "PolicyBasedTrafficSelectors" för att ansluta en ruttbaserad VPN-gateway till flera lokala principbaserade brandväggsenheter. Referera till [Ansluta VPN-gatewayer till flera lokala principbaserad VPN-enheter med hjälp av PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) för mer information.

(\*\*) Basic SKU anses vara en äldre SKU. Basic SKU:n har vissa funktionsbegränsningar. Du kan inte ändra storlek på en gateway som använder en Basic SKU till en av de nya gateway-SKU:erna. Du måste istället ändra till en ny SKU, vilket innebär att ta bort och återskapa din VPN-gateway.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Gateway SKU : Produktion jämfört med arbetsbelastningar för utvecklingstest

På grund av skillnader i serviceavtal och funktioner, rekommenderar vi följande SKU: er för produktion jämfört med utv-test:

| **Arbetsbelastning**                       | **SKU: er**               |
| ---                                | ---                    |
| **Produktion, kritiska arbetsbelastningar** | Alla Generation1 och Generation2 SKU utom Basic |
| **Utv-test eller konceptbevis**   | Grundläggande (**)                 |
|                                    |                        |

(\*\*) Basic SKU anses vara en äldre SKU och har funktionsbegränsningar. Kontrollera att funktionen du behöver stöds innan du använder Basic SKU:n.

Om du använder de gamla SKU:erna (äldre) är produktions-SKU-rekommendationerna Standard och HighPerformance. Information och instruktioner för gamla SKU:er finns i [Gateway SKU:er (äldre)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
