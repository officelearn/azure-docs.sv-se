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
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
ms.locfileid: "30196823"
---
Du vill ändra storlek på din gateway-SKU att uppgradera till en kraftigare för de aktuella SKU: er (VpnGw1 VpnGw2 och VPNGW3) kan du använda den `Resize-AzureRmVirtualNetworkGateway` PowerShell-cmdlet. Du kan också nedgradera gateway SKU-storleken som använder denna cmdlet. Om du använder grundläggande gatewayen SKU, [Följ dessa instruktioner i stället](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) att ändra storlek på din gateway.

Följande PowerShell-exempel visas en gateway-SKU storleksändras till VpnGw2.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Du kan också ändra storlek på en gateway i Azure portal genom att gå till den **Configuration** för din virtuella nätverksgateway och välja en annan SKU i listrutan.