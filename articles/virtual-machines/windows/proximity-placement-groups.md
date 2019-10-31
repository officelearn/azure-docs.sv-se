---
title: Använd närhets placerings grupper för virtuella Windows-datorer
description: Lär dig mer om att skapa och använda närhets placerings grupper för virtuella Windows-datorer i Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d0c35737151b060dcffba8944f4a1361d36dc14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171215"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Distribuera virtuella datorer till närhets placerings grupper med hjälp av PowerShell


För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera dem i en [närhets placerings grupp](co-location.md#proximity-placement-groups).

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.


## <a name="create-a-proximity-placement-group"></a>Skapa en närhetsplaceringsgrupp
Skapa en närhets placerings grupp med cmdleten [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) . 

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

## <a name="list-proximity-placement-groups"></a>Lista närhets placerings grupper

Du kan visa alla närhets placerings grupper med hjälp av cmdleten [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator i närhets placerings gruppen med `-ProximityPlacementGroup $ppg.Id` för att referera till närhets placerings gruppens ID när du använder [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) för att skapa den virtuella datorn.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Du kan se den virtuella datorn i placerings gruppen med hjälp av [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
Du kan också skapa en tillgänglighets uppsättning i närheten av placerings gruppen. Använd samma `-ProximityPlacementGroup` parameter med cmdleten [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) för att skapa en tillgänglighets uppsättning och alla de virtuella datorer som skapats i tillgänglighets uppsättningen skapas också i samma närhets placerings grupp.

## <a name="scale-sets"></a>Skalningsuppsättningar

Du kan också skapa en skalnings uppsättning i närheten av placerings gruppen. Använd samma `-ProximityPlacementGroup`-parameter med [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) för att skapa en skalnings uppsättning och alla instanser kommer att skapas i samma närhets placerings grupp.

## <a name="next-steps"></a>Nästa steg

Du kan också använda [Azure CLI](../linux/proximity-placement-groups.md) för att skapa närhets grupper.
