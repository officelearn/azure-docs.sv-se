---
title: Ändra storlek på en Windows-virtuell dator i Azure
description: Ändra den virtuella datorns storlek som används för en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 6718804d4635edb2628b53017ab9d377928afad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941723"
---
# <a name="resize-a-windows-vm"></a>Ändra storlek på en virtuell Windows-dator

Den här artikeln visar hur du flyttar en virtuell dator till en annan [vm-storlek](sizes.md).

När du har skapat en virtuell dator (VM) kan du skala den virtuella datorn uppåt eller nedåt genom att ändra storleken på den virtuella datorn. I vissa fall måste du frigöra den virtuella datorn först. Detta kan inträffa om den nya storleken inte är tillgänglig i maskinvaruklustret som för närvarande är värd för den virtuella datorn.

Om din virtuella dator använder Premium Storage kontrollerar du att du väljer en **version** av storleken för att få support för Premium Storage. Välj till exempel Standard_E4**s**_v3 i stället för Standard_E4_v3.

## <a name="use-the-portal"></a>Använda portalen

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Öppna sidan för den virtuella datorn.
1. Välj **Storlek**i den vänstra menyn .
1. Välj en ny storlek i listan över tillgängliga storlekar och välj sedan **Ändra storlek**.


Om den virtuella datorn körs kommer om du ändrar dess storlek att den startas om. Om du stoppar den virtuella datorn kan det finnas ytterligare storlekar.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Använda PowerShell för att ändra storlek på en virtuell dator som inte finns i en tillgänglighetsuppsättning

Ställ in några variabler. Ersätt värdena med din egen information.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista de vm-storlekar som är tillgängliga i maskinvaruklustret där den virtuella datorn är värd. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om storleken du vill använda visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om önskad storlek inte anges går du vidare till steg 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Om storleken du vill använda inte visas kör du följande kommandon för att frigöra den virtuella datorn, ändra storlek på den och starta om den virtuella datorn. Ersätt ** \<newVMsize>** med den storlek du vill ha.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Deallocating den virtuella datorn släpper alla dynamiska IP-adresser som tilldelats den virtuella datorn. Operativsystemet och datadiskarna påverkas inte. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Använda PowerShell för att ändra storlek på en virtuell dator i en tillgänglighetsuppsättning

Om den nya storleken för en virtuell dator i en tillgänglighetsuppsättning inte är tillgänglig i maskinvaruklustret som för närvarande är värd för den virtuella datorn, måste alla virtuella datorer i tillgänglighetsuppsättningen frigöras för att ändra storlek på den virtuella datorn. Du kan också behöva uppdatera storleken på andra virtuella datorer i tillgänglighetsuppsättningen när en virtuell dator har ändrats. Om du vill ändra storlek på en virtuell dator i en tillgänglighetsuppsättning utför du följande steg.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista de vm-storlekar som är tillgängliga i maskinvaruklustret där den virtuella datorn är värd. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om önskad storlek visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om den inte finns med i listan går du till nästa avsnitt.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Om storleken du vill använda inte visas fortsätter du med följande steg för att frigöra alla virtuella datorer i tillgänglighetsuppsättningen, ändrar storlek på virtuella datorer och startar om dem.

Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Ändra storlek på och starta om de virtuella datorerna i tillgänglighetsuppsättningen.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Nästa steg

Om du vill ha ytterligare skalbarhet kör du flera VM-instanser och skalar ut. Mer information finns [i Skala Windows-datorer automatiskt i en skalningsuppsättning för virtuella datorer](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

