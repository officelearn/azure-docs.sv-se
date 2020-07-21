---
title: Koppla från en datadisk från en virtuell Windows-dator – Azure
description: Koppla från en datadisk från en virtuell dator i Azure med hjälp av distributions modellen Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: 02192850dd9103b6c1783baf42c184ee16fe299f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500692"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Så här kopplar du bort en datadisk från en virtuell Windows-dator

När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Disken tas bort från den virtuella datorn, men den tas inte bort från lagrings platsen.

> [!WARNING]
> Om du kopplar från en disk tas den inte bort automatiskt. Om du prenumererar på Premium Storage kommer du att fortsätta att betala lagrings avgifter för disken. Mer information finns i [priser och fakturering när du använder Premium Storage](disks-types.md#billing).

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.

 

## <a name="detach-a-data-disk-using-powershell"></a>Koppla från en datadisk med PowerShell

Du kan *ta* bort en datadisk med PowerShell, men se till att inget aktivt använder disken innan du kopplar bort den från den virtuella datorn.

I det här exemplet tar vi bort disken med namnet min **disk** från VM- **myVM** i resurs gruppen **myResourceGroup** . Först tar du bort disken med cmdleten [Remove-AzVMDataDisk](/powershell/module/az.compute/remove-azvmdatadisk) . Sedan uppdaterar du statusen för den virtuella datorn med hjälp av cmdleten [Update-AzVM](/powershell/module/az.compute/update-azvm) för att slutföra processen att ta bort data disken.

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

Disken finns kvar i lagrings utrymmet men är inte längre kopplad till en virtuell dator.

## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen

Du kan ta bort en datadisk med *snabb* borttagning, men se till att inget aktivt använder disken innan du kopplar bort den från den virtuella datorn.

1. På den vänstra menyn väljer du **Virtual Machines**.
1. Välj den virtuella dator som innehåller den datadisk som du vill koppla från.
1. Välj **Diskar** under **Inställningar**.
1. Överst i fönstret **diskar** väljer du **Redigera**.
1. I rutan **diskar** längst till höger på den datadisk som du vill koppla från väljer du **Koppla från**.
1. Spara ändringarna genom att välja **Spara** längst upp på sidan.

Disken finns kvar i lagrings utrymmet men är inte längre kopplad till en virtuell dator.

## <a name="next-steps"></a>Nästa steg

Om du vill återanvända data disken kan du bara [ansluta den till en annan virtuell dator](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
