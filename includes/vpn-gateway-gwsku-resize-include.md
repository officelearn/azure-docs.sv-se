---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73495739"
---
Du kan använda `Resize-AzVirtualNetworkGateway` PowerShell-cmdleten för att uppgradera eller nedgradera en Generation1-eller Generation2-SKU (alla VpnGw SKU: er kan ändra storlek förutom grundläggande SKU: er). Om du använder den grundläggande Gateway-SKU: n [använder du de här anvisningarna i stället](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) för att ändra storlek på din gateway.

I följande PowerShell-exempel visas en gateway-SKU som ändrar storlek till VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Du kan också ändra storlek på en gateway i Azure Portal genom att gå till **konfigurations** sidan för den virtuella Nätverksgatewayen och välja en annan SKU i list rutan.
