---
title: 'Azure-nätverk VPN gateway SKU: er för äldre | Microsoft Docs'
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
ms.openlocfilehash: 5a9e3f63a484069bf8cd39f8a545d7c37f05c63c
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417320"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Arbeta med virtuella nätverkets gateway SKU: er (äldre SKU: er)

Den här artikeln innehåller information om den äldre (gamla) virtuella nätverksgatewayen SKU: er. Äldre SKU: er fortfarande fungerar i båda distributionsmodellerna för VPN-gatewayer som redan har skapats. Klassiska VPN-gatewayer fortsätta att använda de äldre SKU: er, både för befintliga gatewayer och för nya gateways. När du skapar nya Resource Manager VPN gateways kan du använda den nya gatewayen SKU: er. Information om de nya SKU: er finns i [om VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Beräknat aggregerat dataflöde av SKU: N

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Konfigurationer som stöds av SKU och VPN-typ

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Ändra storlek på en gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan ändra storlek på din gateway till en gateway-SKU inom samma SKU-familjen. Till exempel om du har en Standard-SKU, du kan ändra storlek till en HighPerformance-SKU. Dock kan du ändra storlek på din VPN-gateway mellan gamla SKU: er och nya SKU-familjer. Du kan inte exempelvis gå från en Standard-SKU till en VpnGw2 SKU eller en grundläggande SKU till VpnGw1.

Om du vill ändra storlek på en gateway för den klassiska distributionsmodellen, använder du följande kommando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Om du vill ändra storlek på en gateway för Resource Manager-distributionsmodellen med hjälp av PowerShell, använder du följande kommando:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Du kan också ändra storlek på en gateway i Azure-portalen.

## <a name="change"></a>Ändra till den nya gatewayen SKU: er

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om den nya Gateway-SKU: er finns i [Gateway SKU: er](vpn-gateway-about-vpngateways.md#gwsku).

Mer information om konfigurationsinställningar finns i [om VPN Gateway-konfigurationsinställningar](vpn-gateway-about-vpn-gateway-settings.md).
