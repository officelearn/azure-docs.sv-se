---
title: Ändra storlek på en Windows-dator i Azure med hjälp av PowerShell | Microsoft Docs
description: Ändra storlek på en Windows-dator som skapats i Resource Manager-distributionsmodellen, med hjälp av Azure Powershell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 8854f694ce067aaa80a159430a9f48440bcdd95a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701878"
---
# <a name="resize-a-windows-vm"></a>Ändra storlek på en Windows VM

Den här artikeln visar hur du flyttar en virtuell dator till en annan [VM-storlek](sizes.md) med hjälp av Azure Powershell.

När du har skapat en virtuell dator (VM) kan du skala VM uppåt eller nedåt genom att ändra VM-storlek. I vissa fall måste du frigöra den virtuella datorn först. Detta kan inträffa om den nya storleken inte är tillgänglig på klustret maskinvara som för närvarande är värd för den virtuella datorn.

Om den virtuella datorn använder Premium-lagring, se till att du väljer en **s** version storlek du kan få support för Premium-lagring. Till exempel välja Standard_E4**s**_v3 i stället för Standard_E4_v3.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Ändra storlek på en Windows-VM inte i en tillgänglighetsuppsättning

Ange några variabler. Ersätt värdena med din egen information.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista över storlek på Virtuella datorer som är tillgängliga på maskinvara klustret där den virtuella datorn finns. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om du vill visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om önskad storlek inte visas kan du gå vidare till steg 3.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```

Om du vill inte visas, kör följande kommandon för att frigöra den virtuella datorn, ändra storlek på den och starta om den virtuella datorn. Replease **<newVMsize>** med önskad storlek.
   
```powershell
Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Det har frigjorts VM Frigör alla dynamiska IP-adresser som tilldelats den virtuella datorn. Operativsystemet och datadiskarna påverkas inte. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Ändra storlek på en virtuell Windows-dator i en tillgänglighetsuppsättning

Om den nya storleken för en virtuell dator i en tillgänglighetsuppsättning inte är tillgänglig på maskinvara klustret för närvarande är värd för den virtuella datorn, måste alla virtuella datorer i tillgänglighetsuppsättningen att frigöras om du vill ändra storlek på den virtuella datorn. Du kan behöva uppdatera storleken på andra virtuella datorer i tillgänglighetsuppsättningen när en virtuell dator har storleksändrats. Utför följande steg om du vill ändra en virtuell dator i en tillgänglighetsuppsättning.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista över storlek på Virtuella datorer som är tillgängliga på maskinvara klustret där den virtuella datorn finns. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om önskad storlek visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om det inte finns med i listan går du till nästa avsnitt.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Om du vill inte visas, fortsätter du med följande steg för att frigöra alla virtuella datorer i tillgänglighetsuppsättningen, ändra storlek på virtuella datorer och starta om dem.

Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
   
```powershell
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Ändra storlek och starta om de virtuella datorerna i tillgänglighetsuppsättningen.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Nästa steg

För ytterligare utbyggbarhet köra flera VM-instanser och skala ut. Mer information finns i [skala automatiskt Windows-datorer i en virtuell dator Skaluppsättning](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

