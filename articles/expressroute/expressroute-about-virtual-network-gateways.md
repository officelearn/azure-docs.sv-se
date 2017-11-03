---
title: "Om ExpressRoute-gateway för virtuellt nätverk | Microsoft Docs"
description: "Läs mer om virtuella nätverks-gateway för ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: cherylmc
ms.openlocfilehash: a6363fa380d0bab05d7500141cc6019d1d3f68b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Om virtuella nätverksgatewayer för ExpressRoute
En virtuell nätverksgateway används för att skicka trafik mellan virtuella Azure-nätverk och lokala platser. När du konfigurerar en ExpressRoute-anslutning, måste du skapa och konfigurera en virtuell nätverksgateway och gateway för virtuell nätverksanslutning.

När du skapar en virtuell nätverksgateway anger du flera inställningar. En av de nödvändiga inställningarna anger om gatewayen som ska användas för ExpressRoute eller plats-till-plats VPN-trafik. I Resource Manager-distributionsmodellen inställningen är '-GatewayType'.

När nätverkstrafiken som skickas på en privat anslutning, använder du gateway-typ 'ExpressRoute'. Det kallas även för en ExpressRoute-gateway. När nätverkstrafik skickas krypterade över det offentliga Internet, använder du gateway-typ 'Vpn'. Detta kallas för en VPN-gateway. Plats-till-plats-, punkt-till-plats- och VNet-VNet-anslutningar använder allihop en VPN-gateway.

Varje virtuellt nätverk kan bara ha en VNet-gateway per gateway-typ. Du kan exempelvis ha en VNet-gateway som använder -GatewayType Vpn och en som använder -GatewayType ExpressRoute. Den här artikeln fokuserar på den virtuella nätverksgatewayen för ExpressRoute.

## <a name="gwsku"></a>Gateway-SKU:er
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Om du vill uppgradera din gateway till en kraftigare gateway SKU kan du använda 'Storleksändring AzureRmVirtualNetworkGateway' PowerShell-cmdleten i de flesta fall. Den här metoden fungerar för uppgradering till Standard- och HighPerformance SKU: er. Men om du vill uppgradera till UltraPerformance SKU, behöver du skapa gatewayen på nytt.

### <a name="aggthroughput"></a>Beräknad sammanställda genomflöde av gateway-SKU
Följande tabell visar gateway-typerna och beräknat aggregerat dataflöde. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Dataflöde för programmet är beroende av flera faktorer, till exempel slutpunkt till slutpunkt-svarstid och antal trafikflöden programmet öppnas. Siffrorna i tabellen representerar den övre gränsen som programmet kan theorectically uppnå i en perfekt miljö. 
> 
>

## <a name="resources"></a>REST API: er och PowerShell-cmdlets
Ytterligare tekniska resurser och särskild syntax krav för användning av REST API: er och PowerShell-cmdletar för gateway-konfigurationer för virtuella nätverk finns på följande sidor:

| **Klassisk** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [REST-API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Nästa steg
Se [ExpressRoute översikt](expressroute-introduction.md) för mer information om tillgänglig anslutning konfigurationer. 

