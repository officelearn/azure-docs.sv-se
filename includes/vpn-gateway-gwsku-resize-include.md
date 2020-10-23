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
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424364"
---
Du kan använda `Resize-AzVirtualNetworkGateway` PowerShell-cmdleten för att uppgradera eller nedgradera en Generation1-eller Generation2-SKU (alla VpnGw SKU: er kan ändra storlek förutom grundläggande SKU: er). Om du använder den grundläggande Gateway-SKU: n [använder du de här anvisningarna i stället](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) för att ändra storlek på din gateway.

I följande PowerShell-exempel visas en gateway-SKU som ändrar storlek till VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```