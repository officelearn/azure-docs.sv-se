---
title: 'PowerShell: Använd närhetsplaceringsgrupper'
description: Lär dig mer om hur du skapar och använder närhetsplaceringsgrupper med Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208533"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Distribuera virtuella datorer till närhetsplaceringsgrupper med PowerShell


Om du vill få virtuella datorer så nära som möjligt och uppnå lägsta möjliga svarstid bör du distribuera dem inom en [närhetsplaceringsgrupp](co-location.md#proximity-placement-groups).

En närhetsplaceringsgrupp är en logisk gruppering som används för att se till att Azure-beräkningsresurser är fysiskt placerade nära varandra. Närhetsplaceringsgrupper är användbara för arbetsbelastningar där låg svarstid är ett krav.


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


## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator i `-ProximityPlacementGroup $ppg.Id` proximity placement-gruppen med hjälp av att referera till närhetsplaceringsgrupp-ID när du använder [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) för att skapa den virtuella datorn.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Du kan se den virtuella datorn i placeringsgruppen med [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Flytta en befintlig virtuell dator till en närhetsplaceringsgrupp

Du kan också lägga till en befintlig virtuell dator i en närhetsplaceringsgrupp. Du måste stoppa\deallocate den virtuella datorn först, sedan uppdatera den virtuella datorn och starta om.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Flytta en befintlig virtuell dator från en närhetsplaceringsgrupp

Om du vill ta bort en virtuell dator från en webbplats för närhet måste du först stoppa\deallocate den virtuella datorn och sedan uppdatera den virtuella datorn och starta om.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
Du kan också skapa en tillgänglighetsuppsättning i din närhetsplaceringsgrupp. Använd samma `-ProximityPlacementGroup` parameter med [cmdleten New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) för att skapa en tillgänglighetsuppsättning och alla virtuella datorer som skapats i tillgänglighetsuppsättningen skapas också i samma närhetsplaceringsgrupp.

Om du vill lägga till eller ta bort en befintlig tillgänglighetsuppsättning till en närhetsplaceringsgrupp måste du först stoppa alla virtuella datorer i tillgänglighetsuppsättningen. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Flytta en befintlig tillgänglighetsuppsättning till en närhetsplaceringsgrupp

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Flytta en befintlig tillgänglighetsuppsättning från en närhetsplaceringsgrupp

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Skalningsuppsättningar

Du kan också skapa en skalningsuppsättning i din närhetsplaceringsgrupp. Använd samma `-ProximityPlacementGroup` parameter med [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) för att skapa en skalningsuppsättning och alla instanser skapas i samma närhetsplaceringsgrupp.


Om du vill lägga till eller ta bort en befintlig skalningsuppsättning i en närhetsplaceringsgrupp måste du först stoppa skalningsuppsättningen. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Flytta en befintlig skalningsuppsättning till en närhetsplaceringsgrupp

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Flytta en befintlig skala från en närhetsplaceringsgrupp

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

Du kan också använda [Azure CLI](../linux/proximity-placement-groups.md) för att skapa närhetsplaceringsgrupper.
