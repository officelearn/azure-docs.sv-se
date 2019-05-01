---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744628"
---
För att snabbt skapa ett virtuellt nätverk, kan du klicka på ”prova” i den här artikeln för att öppna en PowerShell-konsol. Justera värdena och kopiera och klistra sedan in kommandona i konsolfönstret. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az). Installationsanvisningar Az-modulen, finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

Se till att adressutrymmet för det virtuella nätverket du skapar inte överlappar adressintervallen för andra virtuella nätverk du vill ansluta till, eller dina lokala nätverksadresser.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du inte redan har en resursgrupp som du vill använda, skapa en ny. Justera PowerShell-kommandon för att återspegla resursgruppens namn du vill använda, och sedan kör du följande cmdlet:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Justera PowerShell-kommandon för att skapa ett virtuellt nätverk som är kompatibel för din miljö.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```