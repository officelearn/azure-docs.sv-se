---
title: Växla OS-disk för en virtuell Azure-dator med PowerShell
description: Ändra den operativ system disk som används av en virtuell Azure-dator med PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: ec66892804f3c2d1f831168a2955f2498462cbf3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033017"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Ändra den OS-disk som används av en virtuell Azure-dator med PowerShell

Om du har en befintlig virtuell dator, men vill byta disk för en säkerhets kopierings disk eller en annan OS-disk, kan du använda Azure PowerShell för att växla OS-diskarna. Du behöver inte ta bort och återskapa den virtuella datorn. Du kan till och med använda en hanterad disk i en annan resurs grupp, så länge den inte redan används.

 

Den virtuella datorn måste vara stopped\deallocated. resurs-ID: t för den hanterade disken kan ersättas med resurs-ID: t för en annan hanterad disk.

Kontrol lera att storleken på den virtuella datorn och lagrings typen är kompatibla med den disk som du vill ansluta. Om den disk som du vill använda till exempel är i Premium Storage måste den virtuella datorn kunna Premium Storage (som en storlek för en DS-serie). Båda diskarna måste också ha samma storlek.

Hämta en lista över diskar i en resurs grupp med [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
När du har namnet på den disk som du vill använda anger du som OS-disk för den virtuella datorn. Det här exemplet stop\deallocates den virtuella datorn med namnet *myVM* och tilldelar disken namnet *newDisk* som den nya OS-disken. 
 
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

Information om hur du skapar en kopia av en disk finns i [ögonblicks bilder av en disk](snapshot-copy-managed-disk.md).
