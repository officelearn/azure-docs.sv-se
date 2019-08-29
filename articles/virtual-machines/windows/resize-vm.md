---
title: Använd PowerShell för att ändra storlek på en virtuell Windows-dator i Azure | Microsoft Docs
description: Ändra storlek på en virtuell Windows-dator som skapats i Resource Manager-distributions modellen med hjälp av Azure PowerShell.
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
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 9537744787df7fc6c470bc1ee6862ad3f2991ae9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088731"
---
# <a name="resize-a-windows-vm"></a>Ändra storlek på en virtuell Windows-dator

Den här artikeln visar hur du flyttar en virtuell dator till en annan [storlek](sizes.md) med hjälp av Azure PowerShell.

När du har skapat en virtuell dator (VM) kan du skala upp eller ned den virtuella datorn genom att ändra storleken på den virtuella datorn. I vissa fall måste du frigöra den virtuella datorn först. Detta kan inträffa om den nya storleken inte är tillgänglig i det maskin varu kluster som för närvarande är värd för den virtuella datorn.

Om din virtuella dator använder Premium Storage, se till att du väljer en **s** -version av storleken för att få Premium Storage support. Välj till exempel Standard_E4**s**_v3 i stället för Standard_E4_v3.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Ändra storlek på en virtuell Windows-dator som inte finns i en tillgänglighets uppsättning

Ange några variabler. Ersätt värdena med din egen information.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista de VM-storlekar som är tillgängliga på det maskin varu kluster där den virtuella datorn finns. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om den storlek som du vill ha visas i listan kör du följande kommandon för att ändra storlek på den virtuella datorn. Om önskad storlek inte visas i listan går du vidare till steg 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Om den storlek som du vill ha inte visas i listan kör du följande kommandon för att frigöra den virtuella datorn, ändra storlek på den och starta om den virtuella datorn. **Ersätt\<newVMsize >** med den storlek som du vill ha.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Om du avallokerar VM frigör det dynamiska IP-adresser som tilldelats den virtuella datorn. Operativ system-och data diskar påverkas inte. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Ändra storlek på en virtuell Windows-dator i en tillgänglighets uppsättning

Om den nya storleken för en virtuell dator i en tillgänglighets uppsättning inte är tillgänglig på det maskin varu kluster som är värd för den virtuella datorn, måste alla virtuella datorer i tillgänglighets uppsättningen frigöras för att ändra storlek på den virtuella datorn. Du kan också behöva uppdatera storleken på andra virtuella datorer i tillgänglighets uppsättningen när storleken på en virtuell dator har ändrats. Utför följande steg för att ändra storlek på en virtuell dator i en tillgänglighets uppsättning.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista de VM-storlekar som är tillgängliga på det maskin varu kluster där den virtuella datorn finns. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om önskad storlek visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om den inte visas går du till nästa avsnitt.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Om den storlek som du vill ha inte visas i listan fortsätter du med följande steg för att frigöra alla virtuella datorer i tillgänglighets uppsättningen, ändrar storlek på virtuella datorer och startar om dem.

Stoppa alla virtuella datorer i tillgänglighets uppsättningen.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Ändra storlek och starta om de virtuella datorerna i tillgänglighets uppsättningen.
   
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

Om du vill ha ytterligare skalbarhet kan du köra flera virtuella dator instanser och skala ut. Mer information finns i skala [Windows-datorer automatiskt i en skalnings uppsättning för virtuella datorer](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

