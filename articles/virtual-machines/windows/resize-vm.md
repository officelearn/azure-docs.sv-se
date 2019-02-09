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
ms.openlocfilehash: 0c942056e95812dfbbe6e3b1e8963799088273fb
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981204"
---
# <a name="resize-a-windows-vm"></a>Ändra storlek på en Windows VM

Den här artikeln visar hur du flyttar en virtuell dator till en annan [VM-storlek](sizes.md) med Azure Powershell.

När du har skapat en virtuell dator (VM) kan du skala den virtuella datorn upp eller ned genom att ändra virtuella datorstorlek. I vissa fall måste du frigöra den virtuella datorn först. Detta kan inträffa om den nya storleken inte är tillgänglig på klustret maskinvara som för närvarande är värd för den virtuella datorn.

Om den virtuella datorn använder Premium Storage kan du se till att du väljer en **s** version av storlek kan få support för Premium Storage. Till exempel välja Standard_E4**s**_v3 i stället för Standard_E4_v3.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Ändra storlek på en virtuell Windows-dator inte i en tillgänglighetsuppsättning

Ange några variabler. Ersätt värdena med din egen information.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista de storlekar som är tillgängliga på maskinvara klustret där den virtuella datorn finns. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om önskad storlek visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om önskad storlek inte visas kan du gå vidare till steg 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Om önskad storlek inte visas, kör följande kommandon för att frigöra den virtuella datorn, ändra storlek på den och starta om den virtuella datorn. Ersätt **<newVMsize>** med önskad storlek.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Avallokera den virtuella datorn Frigör några dynamiska IP-adresser som tilldelas den virtuella datorn. Operativsystemet och datadiskarna påverkas inte. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Ändra storlek på en virtuell Windows-dator i en tillgänglighetsuppsättning

Om den nya storleken för en virtuell dator i en tillgänglighetsuppsättning inte är tillgänglig på klustret för maskinvara som för närvarande som är värd för den virtuella datorn, måste alla virtuella datorer i tillgänglighetsuppsättningen frigöras för att ändra storlek på den virtuella datorn. Du kan behöva uppdatera storleken på andra virtuella datorer i tillgänglighetsuppsättningen när en virtuell dator har ändrats. Om du vill ändra storlek på en virtuell dator i en tillgänglighetsuppsättning, utför du följande steg.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista de storlekar som är tillgängliga på maskinvara klustret där den virtuella datorn finns. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om önskad storlek visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om det inte visas, gå till nästa avsnitt.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Om önskad storlek inte visas, fortsätter du med följande steg för att frigöra alla virtuella datorer i tillgänglighetsuppsättningen, ändra storlek på virtuella datorer och starta sedan om dem.

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

För extra skalbarhet, kör flera VM-instanser och skala ut. Mer information finns i [skala automatiskt Windows-datorer i Virtual Machine Scale Sets](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

