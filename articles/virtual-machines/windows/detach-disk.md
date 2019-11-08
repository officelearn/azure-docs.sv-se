---
title: Koppla från en datadisk från en virtuell Windows-dator – Azure | Microsoft Docs
description: Koppla från en datadisk från en virtuell dator i Azure med hjälp av distributions modellen Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: de33ed80055c26870a29b4c4393a9072ec8dda5e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749484"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Så här kopplar du från en datadisk från en virtuell Windows-dator

När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Disken tas bort från den virtuella datorn, men den tas inte bort från lagrings platsen.

> [!WARNING]
> Om du kopplar från en disk tas den inte bort automatiskt. Om du prenumererar på Premium Storage kommer du att fortsätta att betala lagrings avgifter för disken. Mer information finns i [priser och fakturering när du använder Premium Storage](disks-types.md#billing).

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.

 

## <a name="detach-a-data-disk-using-powershell"></a>Koppla från en datadisk med PowerShell

Du kan *ta* bort en datadisk med PowerShell, men se till att inget aktivt använder disken innan du kopplar bort den från den virtuella datorn.

I det här exemplet tar vi bort disken med namnet min **disk** från VM- **myVM** i resurs gruppen **myResourceGroup** . Först tar du bort disken med cmdleten [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) . Sedan uppdaterar du statusen för den virtuella datorn med hjälp av cmdleten [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) för att slutföra processen att ta bort data disken.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Disken finns kvar i lagrings utrymmet men är inte längre kopplad till en virtuell dator.

## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen

1. På den vänstra menyn väljer du **Virtual Machines**.
2. Välj den virtuella dator som innehåller den datadisk som du vill koppla från och klicka på **stoppa** för att ta bort den virtuella datorn.
3. I fönstret virtuell dator väljer du **diskar**.
4. Överst i fönstret **diskar** väljer du **Redigera**.
5. I rutan **diskar** längst till höger på den datadisk som du vill koppla från, klickar du på knappen ![koppla bort knapp bild](./media/detach-disk/detach.png) koppla från.
5. När disken har tagits bort klickar du på **Spara** längst upp i fönstret.
6. I fönstret virtuell dator klickar du på **Översikt** och sedan på **Start** -knappen längst upp i fönstret för att starta om den virtuella datorn.

Disken finns kvar i lagrings utrymmet men är inte längre kopplad till en virtuell dator.

## <a name="next-steps"></a>Nästa steg

Om du vill återanvända data disken kan du bara [ansluta den till en annan virtuell dator](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)