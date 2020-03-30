---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73495739"
---
Du kan `Resize-AzVirtualNetworkGateway` använda PowerShell-cmdleten för att uppgradera eller nedgradera en Generation1 eller Generation2 SKU (alla VPNGw SKU kan ändra storlek utom grundläggande SKU: er). Om du använder SKU basic-gatewayen [använder du dessa instruktioner i stället](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) för att ändra storlek på gatewayen.

Följande PowerShell-exempel visar en gateway SKU som ãgs storlek till VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Du kan också ändra storlek på en gateway i Azure-portalen genom att gå till **konfigurationssidan** för din virtuella nätverksgateway och välja en annan SKU från listrutan.
