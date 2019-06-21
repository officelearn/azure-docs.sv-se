---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6fd4039614dbd7c1a2b2c6ba8403502a6420fe3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187226"
---
För de aktuella SKU: er (VpnGw1, VpnGw2 och VPNGW3) som du vill ändra storlek på din gateway SKU att uppgradera till en kraftfullare, du kan använda den `Resize-AzVirtualNetworkGateway` PowerShell-cmdlet. Du kan också nedgradera gateway SKU-storlek som använder denna cmdlet. Om du använder Basic-gateway SKU, [Följ dessa instruktioner i stället](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) att ändra storlek på din gateway.

Följande PowerShell-exempel visar en gateway-SKU som ändras till VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Du kan också ändra storlek på en gateway i Azure portal genom att gå till den **Configuration** för din virtuella nätverksgateway och väljer en annan SKU i listrutan.