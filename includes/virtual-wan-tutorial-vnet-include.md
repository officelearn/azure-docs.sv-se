---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552989"
---
Om du inte redan har ett virtuellt nätverk kan du snabbt skapa ett med PowerShell. Du kan också skapa ett virtuellt nätverk via Azure-portalen.

* Se till att adressutrymmet för det virtuella nätverket du skapar inte överlappar adressintervallen för andra virtuella nätverk du vill ansluta till, eller dina lokala nätverksadresser. 
* Om du redan har ett virtuellt nätverk kontrollerar du att det uppfyller kraven och inte har någon gateway för virtuellt nätverk.

I den här artikeln kan du klicka och enkelt prova att skapa ett virtuellt nätverk. Du öppnar då en PowerShell-konsol. Justera värdena och kopiera och klistra sedan in kommandona i konsolfönstret.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Justera PowerShell-kommandona och skapa sedan en resursgrupp.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Justera PowerShell-kommandona och skapa ett virtuellt nätverk som är kompatibelt med din miljö.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```