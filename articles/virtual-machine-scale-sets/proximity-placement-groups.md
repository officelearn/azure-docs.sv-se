---
title: Förhandsversion av närhetsplaceringsgrupper för skalningsuppsättningar för virtuella datorer
description: Lär dig mer om hur du skapar och använder närhetsplaceringsgrupper för Windows-skalningsuppsättningar för virtuella datorer i Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273626"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Förhandsgranskning: Skapa och använda närhetsplaceringsgrupper med PowerShell

Om du vill få virtuella datorer så nära som möjligt och uppnå lägsta möjliga svarstid bör du distribuera skalningsuppsättningen inom en [närhetsplaceringsgrupp](co-location.md#preview-proximity-placement-groups).

En närhetsplaceringsgrupp är en logisk gruppering som används för att se till att Azure-beräkningsresurser är fysiskt placerade nära varandra. Närhetsplaceringsgrupper är användbara för arbetsbelastningar där låg svarstid är ett krav.

> [!IMPORTANT]
> Proximity Placement Groups är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Närhetsplaceringsgrupper är inte tillgängliga i dessa regioner under förhandsversionen: **Japan East,** **Australia East** och India **Central**.


## <a name="create-a-proximity-placement-group"></a>Skapa en närhetsplaceringsgrupp
Skapa en närhetsplaceringsgrupp med cmdleten [New-AzProximityPlacementGroup.](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Lista närhetsplaceringsgrupper

Du kan lista alla närhetsplaceringsgrupper med cmdleten [Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Skapa en skala i gruppen `-ProximityPlacementGroup $ppg.Id` proximity placement med att referera till närhetsplaceringsgrupp-ID när du använder [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) för att skapa skalningsuppsättningen.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Du kan se förekomsten i placeringsgruppen med [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Nästa steg

Du kan också använda [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) för att skapa närhetsplaceringsgrupper.
