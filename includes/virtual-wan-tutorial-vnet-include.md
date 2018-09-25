---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fae3c3889242dfbf8f270d3762ea7434ceda6da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004193"
---
Om du inte redan har ett virtuellt nätverk kan du snabbt skapa ett med PowerShell. Du kan också skapa ett virtuellt nätverk via Azure-portalen.

* Se till att adressutrymmet för det virtuella nätverket du skapar inte överlappar adressintervallen för andra virtuella nätverk du vill ansluta till, eller dina lokala nätverksadresser. 
* Om du redan har ett virtuellt nätverk kontrollerar du att det uppfyller kraven och inte har någon gateway för virtuellt nätverk.

I den här artikeln kan du klicka och enkelt prova att skapa ett virtuellt nätverk. Du öppnar då en PowerShell-konsol. Justera värdena och kopiera och klistra sedan in kommandona i konsolfönstret.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Justera PowerShell-kommandona och skapa sedan en resursgrupp.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Justera PowerShell-kommandona och skapa ett virtuellt nätverk som är kompatibelt med din miljö.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```