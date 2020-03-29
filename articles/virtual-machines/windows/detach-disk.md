---
title: Koppla bort en datadisk från en Virtuell Windows- Azure
description: Koppla från en datadisk från en virtuell dator i Azure med hjälp av Distributionsmodellen för Resource Manager.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 301f3abd26f702f3f29c8833c835ba7d0e41bcaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834611"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Så här kopplar du bort en datadisk från en virtuell Windows-dator

När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Detta tar bort disken från den virtuella datorn, men tar inte bort den från lagring.

> [!WARNING]
> Om du kopplar från en disk tas den inte bort automatiskt. Om du prenumererar på Premium-lagring fortsätter du att debiteras lagringsavgifter för disken. Mer information finns i [Priser och fakturering när du använder Premium Storage](disks-types.md#billing).

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.

 

## <a name="detach-a-data-disk-using-powershell"></a>Koppla bort en datadisk med PowerShell

Du kan *ta* bort en datadisk med PowerShell, men se till att inget aktivt använder disken innan du kopplar från den från den virtuella datorn.

I det här exemplet tar vi bort disken med namnet **myDisk** från VM **myVM** i resursgruppen **myResourceGroup.** Först tar du bort disken med cmdleten [Remove-AzVMDataDisk.](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) Sedan uppdaterar du tillståndet för den virtuella datorn, med hjälp av [cmdleten Update-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) för att slutföra processen med att ta bort datadisken.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

Disken lagras men är inte längre ansluten till en virtuell dator.

## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen

Du kan ta bort en datadisk för *att* ta bort en datadisk, men se till att inget aktivt använder disken innan du kopplar från den från den virtuella datorn.

1. Välj **Virtuella datorer**på den vänstra menyn .
1. Välj den virtuella dator som har den datadisk som du vill koppla från.
1. Välj **Diskar** under **Inställningar**.
1. Välj **Redigera**högst upp i fönstret **Diskar** .
1. Välj Koppla från till höger på den datadisk som du vill koppla från längst till höger på den datadisk som du vill koppla från i **fönstret** **Diskar**.
1. Välj **Spara** högst upp på sidan för att spara ändringarna.

Disken lagras men är inte längre ansluten till en virtuell dator.

## <a name="next-steps"></a>Nästa steg

Om du vill återanvända datadisken kan du bara [koppla den till en annan virtuell dator](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
