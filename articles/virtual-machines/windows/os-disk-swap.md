---
title: Växla OS-disk för en virtuell Azure-dator med PowerShell | Microsoft Docs
description: Ändra operativsystemets disk som används av virtuella Azure-datorer med hjälp av PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 73aab0750d97981d6684d04415683435bbd28797
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980422"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Ändra OS-disken som används av en Azure-dator med hjälp av PowerShell

Om du har en befintlig virtuell dator, men du vill växla disken för en säkerhetskopiering disk eller en annan OS-disk, kan du använda Azure PowerShell för att växla OS-diskar. Du behöver ta bort och återskapa den virtuella datorn. Du kan även använda en hanterad disk i en annan resursgrupp, förutsatt att den inte redan används.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Den virtuella datorn måste vara stopped\deallocated och resurs-ID för hanterad disk kan ersättas med resurs-ID för en annan hanterad disk.

Kontrollera att typ av virtuell dator och är kompatibla med den disk du vill bifoga. Till exempel om det är den disk du vill använda i Premium-lagring, måste den virtuella datorn kunna Premium Storage (t.ex. en storlek i DS-serien). 

Hämta en lista över diskar i en resurs med [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
När du har namnet på den disk som du vill använda, anger du den som OS-disken för den virtuella datorn. Det här exemplet stop\deallocates den virtuella datorn med namnet *myVM* och tilldelar disk med namnet *newDisk* som den nya OS-disken. 
 
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

För att skapa en kopia av en disk, se [ögonblicksbild av en disk](snapshot-copy-managed-disk.md).