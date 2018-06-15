---
title: Växlingen OS-disk för en virtuell Azure-dator med PowerShell | Microsoft Docs
description: Ändra operativsystemets disk som används av en virtuell Azure-dator med hjälp av PowerShell.
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
ms.openlocfilehash: caa8fe2088995e3d30c9b808f639b9280e3a74be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196198"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Ändra OS-disk som används av en virtuell Azure-dator med hjälp av PowerShell

Om du har en befintlig virtuell dator, men du vill byta disken för en säkerhetskopiering disk eller en annan OS-disk, kan du använda Azure PowerShell byta OS. Du behöver ta bort och återskapa den virtuella datorn. Du kan även använda en hanterade diskar i en annan resursgrupp, så länge det inte redan används.

Den virtuella datorn måste vara stopped\deallocated och resurs-ID för hanterade diskar kan ersättas med resurs-ID för en annan hanterad disk.

Kontrollera att typen VM storlek och lagring är kompatibla med den disk som du vill bifoga. Till exempel om disken som du vill använda i Premium-lagring, måste sedan den virtuella datorn kunna Premium-lagring (t.ex. en DS-storlek). 

Hämta en lista över diskar i en resurs med [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk)

```azurepowershell-interactive
Get-AzureRmDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
När du har namnet på den disk som du vill använda, anger du som som OS-disk för den virtuella datorn. Det här exemplet stop\deallocates den virtuella datorn med namnet *myVM* och tilldelar disk med namnet *newDisk* som den nya OS-disken. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Nästa steg**

För att skapa en kopia av en disk, se [ögonblicksbild en disk](snapshot-copy-managed-disk.md).