---
title: 'Äldre virtuella Azure-nätverket VPN gateway-SKU: er | Microsoft Docs'
description: 'Hur du arbetar med den gamla virtuella nätverksgatewayen SKU: er; Basic, Standard och HighPerformance.'
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 4feecb9c1e91e1bc6c66a610c092e7bf894886e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Arbeta med virtuell nätverksgateway SKU: er (äldre SKU: er)

Den här artikeln innehåller information om den äldre (gamla) virtuell nätverksgatewayen SKU: er. Äldre SKU: er fortfarande fungerar i båda distributionsmodellerna för VPN-gatewayer som redan har skapats. Klassiska VPN-gatewayer fortsätta att använda äldre SKU: er, både för befintliga gateways och för nya gatewayer. När du skapar nya Resource Manager VPN gateway ska använda den nya gatewayen SKU: er. Information om nya SKU: er finns [om VPN-Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Beräknad sammanställda genomflöde av SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Konfigurationer som stöds av SKU- och VPN-typ

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Ändra storlek på en gateway

Du kan ändra storlek på din gateway till en gateway-SKU inom samma SKU-familjen. Till exempel om du har en Standard-SKU, du kan ändra storlek till en HighPerformance SKU. Men kan inte du ändra din VPN-gateway mellan de gamla SKU: er och nya SKU-familjer. Du kan inte exempelvis gå från en Standard-SKU till en VpnGw2 SKU eller en grundläggande SKU till VpnGw1.

Om du vill ändra storlek på en gateway för den klassiska distributionsmodellen, använder du följande kommando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Om du vill ändra storlek på en gateway för Resource Manager-distributionsmodellen med hjälp av PowerShell, använder du följande kommando:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Du kan också ändra storlek på en gateway i Azure-portalen.

## <a name="change"></a>Ändra till den nya gatewayen SKU: er

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om den nya Gateway-SKU: er finns [Gateway-SKU: er](vpn-gateway-about-vpngateways.md#gwsku).

Mer information om konfigurationsinställningar finns [konfigurationsinställningar för VPN-Gateway](vpn-gateway-about-vpn-gateway-settings.md).