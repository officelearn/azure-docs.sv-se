---
title: Byt OS-disk mot en virtuell Azure-dator med PowerShell '
description: Ändra operativsystemdisken som används av en virtuell Azure-dator med PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 566347414ffe707b1d68a61b00ba21d19ff2b1eb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869386"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Ändra os-disken som används av en virtuell Azure-dator med PowerShell

Om du har en befintlig virtuell dator, men vill byta disk mot en reservdisk eller en annan OS-disk, kan du använda Azure PowerShell för att byta os-diskarna. Du behöver inte ta bort och återskapa den virtuella datorn. Du kan till och med använda en hanterad disk i en annan resursgrupp, så länge den inte redan används.

 

Den virtuella datorn måste stoppas\deallocated, då resurs-ID för den hanterade disken kan ersättas med resurs-ID för en annan hanterad disk.

Kontrollera att den virtuella datorns storlek och lagringstyp är kompatibla med den disk som du vill koppla. Om disken du till exempel vill använda finns i Premium Storage måste den virtuella datorn kunna hantera Premium Storage (som en storlek i DS-serien). Båda diskarna måste också ha samma storlek.

Hämta en lista över diskar i en resursgrupp med [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
När du har namnet på den disk som du vill använda anger du den som OS-disk för den virtuella datorn. Det här exemplet stop\deallocates den virtuella datorn med namnet *myVM* och tilldelar disken som heter *newDisk* som den nya OS-disken. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Nästa steg**

Information om hur du skapar en kopia av en disk finns i [Ögonblicksbild en disk](snapshot-copy-managed-disk.md).
