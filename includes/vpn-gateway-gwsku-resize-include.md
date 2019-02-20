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
ms.openlocfilehash: 081352a23e6a0d8f9e2daa77eca1f8ac85172ff6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418236"
---
För de aktuella SKU: er (VpnGw1, VpnGw2 och VPNGW3) som du vill ändra storlek på din gateway SKU att uppgradera till en kraftfullare, du kan använda den `Resize-AzVirtualNetworkGateway` PowerShell-cmdlet. Du kan också nedgradera gateway SKU-storlek som använder denna cmdlet. Om du använder Basic-gateway SKU, [Följ dessa instruktioner i stället](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) att ändra storlek på din gateway.

Följande PowerShell-exempel visar en gateway-SKU som ändras till VpnGw2.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Du kan också ändra storlek på en gateway i Azure portal genom att gå till den **Configuration** för din virtuella nätverksgateway och väljer en annan SKU i listrutan.