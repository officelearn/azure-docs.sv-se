---
title: 'Äldre Azure virtuella nätverk VPN gateway SKU: er'
description: Så här arbetar du med de gamla SKU:erna för virtuell nätverksgateway. Grundläggande, standard och highperformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279395"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Arbeta med virtuella nätverksgatewaySass (äldre SKU:er)

Den här artikeln innehåller information om de äldre (gamla) virtuella nätverksgateway-SKU:erna. De äldre SKU:erna fungerar fortfarande i båda distributionsmodellerna för VPN-gateways som redan har skapats. Klassiska VPN-gateways fortsätter att använda de äldre SKU:erna, både för befintliga gateways och för nya gateways. När du skapar nya VPN-gatewayer för Resource Manager använder du de nya gateway-SKU:erna. Information om de nya SKU:erna finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Du kan visa äldre gateway-priser i avsnittet **Virtuella nätverksgateways,** som finns på [sidan ExpressRoute-priser](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Beräknat aggregerat dataflöde av SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Konfigurationer som stöds av SKU- och VPN-typ

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Ändra storlek på en gateway

Du kan ändra storlek på din gateway till en gateway SKU inom samma SKU-familj. Om du till exempel har en standard-SKU kan du ändra storlek på en SKU med hög resultat. Du kan dock inte ändra storlek på din VPN-gateway mellan de gamla SKU:erna och de nya SKU-familjerna. Du kan till exempel inte gå från en standard-SKU till en VpnGw2 SKU eller en grundläggande SKU till VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Om du vill ändra storlek på en gateway för Resurshanterarens distributionsmodell med PowerShell använder du följande kommando:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Du kan också ändra storlek på en gateway i Azure-portalen.

### <a name="classic"></a><a name="classicresize"></a>Klassisk

Om du vill ändra storlek på en gateway för den klassiska distributionsmodellen måste du använda PowerShell-cmdlets för tjänsthantering. Ange följande kommando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Ändra till de nya gateway-SKU:erna

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om de nya SKU:erna för gateway finns i [SKU:er för gateway](vpn-gateway-about-vpngateways.md#gwsku).

Mer information om konfigurationsinställningar finns i [Om konfigurationsinställningar för VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).
