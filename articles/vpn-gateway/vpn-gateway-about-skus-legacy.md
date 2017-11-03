---
title: "Äldre Azure virtuell nätverksgateway SKU: er | Microsoft Docs"
description: "Gamla virtuella nätverks-gateway-SKU: er."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2017
ms.author: cherylmc
ms.openlocfilehash: d5127c7fa512bad49817fa4c8edf3a16ca2f7d60
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Arbeta med virtuell nätverksgateway SKU: er (äldre SKU: er)

Den här artikeln innehåller information om den äldre (gamla) virtuell nätverksgatewayen SKU: er. Äldre SKU: er fortfarande fungerar i båda distributionsmodellerna för VPN-gatewayer som redan har skapats. Klassiska VPN-gatewayer fortsätta att använda äldre SKU: er, både för befintliga gateways och för nya gatewayer. När du skapar nya Resource Manager VPN gateway ska använda den nya gatewayen SKU: er. Information om nya SKU: er finns [om VPN-Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Beräknad sammanställda genomflöde av SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Konfigurationer som stöds av SKU- och VPN-typ

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Ändra storlek på en gateway (ändra en gateway-SKU)

Du kan ändra storlek på en gateway-SKU inom samma SKU-familjen. Till exempel om du har en Standard-SKU, du kan ändra storlek till en HighPerformance SKU. Du kan inte ändra storlek på VPN-gatewayer mellan de gamla SKU: er och nya SKU-familjer. Du kan exempelvis gå från en Standard-SKU till en VpnGw2 SKU.

>[!IMPORTANT]
>När du ändrar storlek på en gateway har du 20 – 30 minuter stillestånd för denna gateway när det storleksändras.
>
>

Om du vill ändra storlek på en gateway-SKU för den klassiska distributionsmodellen, använder du följande kommando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Om du vill ändra storlek på en gateway-SKU för Resource Manager-distributionsmodellen, använder du följande kommando:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="migrate"></a>Migrera till ny gateway SKU: er

Om du arbetar med Resource Manager-distributionsmodellen kan du migrera till ny gateway SKU: er. Om du arbetar med den klassiska distributionsmodellen kan inte migreras till de nya SKU: er och i stället måste fortsätta att använda de äldre SKU: er.

[!INCLUDE [Migrate SKU](../../includes/vpn-gateway-migrate-legacy-sku-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om den nya Gateway-SKU: er finns [Gateway-SKU: er](vpn-gateway-about-vpngateways.md#gwsku).

Mer information om konfigurationsinställningar finns [konfigurationsinställningar för VPN-Gateway](vpn-gateway-about-vpn-gateway-settings.md).
