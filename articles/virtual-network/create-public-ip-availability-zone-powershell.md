---
title: Skapa en zoned offentlig IP-adress med PowerShell | Microsoft Docs
description: "Skapa en offentlig IP-adress i en zon för tillgänglighet med PowerShell."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: bfeba57036338b4e325d2f122443f2cde0373eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>Skapa en offentlig IP-adress i en zon för tillgänglighet med PowerShell

Du kan distribuera en offentlig IP-adress i en zon för Azure tillgänglighet (förhandsversion). En zon för tillgänglighet är ett fysiskt separat zon i en Azure-region. Lär dig att:

> * Skapa en offentlig IP-adress i en zon för tillgänglighet
> * Identifiera relaterade resurser som skapats i zonen tillgänglighet
  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här artikeln kräver att du har version 4.4.0 eller högre för modulen AzureRM installerad. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera, installera den senaste versionen av AzureRM-modul från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureRM).

> [!NOTE]
> Tillgänglighet zoner i förhandsversionen och är redo för din utveckla och testa scenarier. Support är tillgänglig för väljer Azure-resurser och regioner och familjer för VM-storlek. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och Virtuella storlek familjer kan du försöka tillgänglighet zoner med finns [översikt av tillgänglighet zoner](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I det här exemplet en resursgrupp med namnet *myResourceGroup* skapas i den *westeurope* region. *westeurope* är en Azure-regioner som har stöd för tillgänglighet zoner i förhandsgranskningen.

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>Skapa en zonal offentliga IP-adress

Skapa en offentlig IP-adress i en zon för tillgänglighet med hjälp av följande kommando:

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.

## <a name="get-zone-information-about-a-public-ip-address"></a>Hämta zonen information om en offentlig IP-adress

Hämta zoninformation av en offentlig IP-adress med följande kommando:

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [tillgänglighet zoner](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
- Lär dig mer om [offentliga IP-adresser](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 