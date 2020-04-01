---
title: Reservera offentliga IPv6-adresser och adressintervall i ett virtuellt Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig hur du reserverar offentliga IPv6-adresser och adressintervall i ett virtuellt Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a0dd56842174d89688c862397c373326ef50d1f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420548"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Reservera offentligt IPv6-adressprefix
Med IPv6 för Azure Virtual Network (VNet) kan du vara värd för program i Azure med IPv6- och IPv4-anslutning både i ett virtuellt nätverk och till och från Internet. Förutom att reservera enskilda IPv6-adresser kan du reservera sammanhängande intervall för Azure IPv6-adresser (kallas IP-prefix) för din användning. I de här artiklarna beskrivs hur du skapar offentliga IPv6-ADRESSER och adressintervall med Azure PowerShell och CLI.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Skapa en enda reserverad IPv6-offentlig IP

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Du kan skapa en enda reserverad (statisk) IPv6 Public IP-adress med Azure PowerShell med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) enligt följande:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Använda Azure CLI

 Du kan skapa en enda reserverad (statisk) IPv6 Public IP-adress Azure CLI med [az network public-ip skapa](/cli/azure/network/public-ip) enligt följande:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Skapa ett reserverat IPv6-prefix (intervall)

Om du vill reservera ett IPv6-prefix lägger du till IP-adressfamiljen i IPv6 i samma kommando som används för att skapa IPv4-prefix. Följande kommandon skapar ett prefix med storleken /125 ( 8 IPv6-adresser).  

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Du kan skapa en offentlig IPv6-adress med Azure CLI med [az network public-ip create](/powershell/module/az.network/new-azpublicipprefix) enligt följande:
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Använda Azure CLI

Du kan skapa en offentlig IPv6-adress med Azure CLI enligt följande:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Allokera en offentlig IP-adress från ett reserverat IPv6-prefix

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

 Du skapar en statisk IPv6 Public IP från `-PublicIpPrefix` ett reserverat prefix genom att lägga till argumentet när du skapar den offentliga IP-adressen med Azure PowerShell. I följande exempel förutsätts att ett prefix har skapats och lagrats i en PowerShell-variabel med namnet: *$myOwnIPv6Prefix*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Använda Azure CLI
 
I följande exempel förutsätts att ett prefix har skapats och lagrats i en CLI-variabel med namnet: *IPv6PrefixWestUS*.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [IPv6-adressprefix](ipv6-public-ip-address-prefix.md).
- Läs mer om [IPv6-adresser](ipv6-overview.md).
