---
title: Ändra storlek på en Windows-dator i Azure med hjälp av PowerShell | Microsoft Docs
description: Ändra storlek på en Windows-dator som skapats i Resource Manager-distributionsmodellen, med hjälp av Azure Powershell.
services: virtual-machines-windows
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 09a20892770ada7c67c43c20f3c4f36b5a76f09d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="resize-a-windows-vm"></a>Ändra storlek på en Windows VM
Den här artikeln visar hur du ändrar storlek på en Windows-VM som skapats i Resource Manager-distributionsmodellen med hjälp av Azure Powershell.

När du har skapat en virtuell dator (VM) kan du skala VM uppåt eller nedåt genom att ändra VM-storlek. I vissa fall måste du frigöra den virtuella datorn först. Detta kan inträffa om den nya storleken inte är tillgänglig på klustret maskinvara som för närvarande är värd för den virtuella datorn.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Ändra storlek på en Windows-VM inte i en tillgänglighetsuppsättning
1. Lista över storlek på Virtuella datorer som är tillgängliga på maskinvara klustret där den virtuella datorn finns. 
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```
2. Om önskad storlek visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om önskad storlek inte visas kan du gå vidare till steg 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Om önskad storlek inte visas, kör följande kommandon för att frigöra den virtuella datorn, ändra storlek på den och starta om den virtuella datorn.
   
    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -Name $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [!WARNING]
> Det har frigjorts VM Frigör alla dynamiska IP-adresser som tilldelats den virtuella datorn. Operativsystemet och datadiskarna påverkas inte. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Ändra storlek på en virtuell Windows-dator i en tillgänglighetsuppsättning
Om den nya storleken för en virtuell dator i en tillgänglighetsuppsättning inte är tillgänglig på maskinvara klustret för närvarande är värd för den virtuella datorn, måste alla virtuella datorer i tillgänglighetsuppsättningen att frigöras om du vill ändra storlek på den virtuella datorn. Du kan behöva uppdatera storleken på andra virtuella datorer i tillgänglighetsuppsättningen när en virtuell dator har storleksändrats. Utför följande steg om du vill ändra en virtuell dator i en tillgänglighetsuppsättning.

1. Lista över storlek på Virtuella datorer som är tillgängliga på maskinvara klustret där den virtuella datorn finns.
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```
2. Om önskad storlek visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om det inte finns med i listan går du till steg 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Om önskad storlek inte visas, fortsätter du med följande steg för att frigöra alla virtuella datorer i tillgänglighetsuppsättningen, ändra storlek på virtuella datorer och starta om dem.
4. Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
   } 
   ```
5. Ändra storlek och starta om de virtuella datorerna i tillgänglighetsuppsättningen.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $newSize = "<newVmSize>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
     $vm.HardwareProfile.VmSize = $newSize
     Update-AzureRmVM -ResourceGroupName $rg -VM $vm
     Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
   }
   ```

## <a name="next-steps"></a>Nästa steg
* För ytterligare utbyggbarhet köra flera VM-instanser och skala ut. Mer information finns i [skala automatiskt Windows-datorer i en virtuell dator Skaluppsättning](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

