---
title: Koppla från en datadisk från en Windows-VM - Azure | Microsoft Docs
description: Lär dig att koppla från en datadisk från en virtuell dator i Azure med hjälp av Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: e56e9ce22cc9e2bad75c944c20bff812d8720d18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913509"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Hur du koppla från en datadisk från en Windows-dator
När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Detta tar bort disken från den virtuella datorn, men ta bort inte den från lagring.

> [!WARNING]
> Om du koppla från en disk som den inte tas bort automatiskt. Om du har prenumererat på Premium-lagring, fortsätter du lagring avgifter för disken. Mer information finns i [priser och fakturering när du använder Premiumlagring](premium-storage.md#pricing-and-billing).
>
>

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.

## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen

1. Välj i den vänstra menyn **virtuella datorer**.
2. Välj den virtuella dator som har datadisk som du vill koppla från och klickar på **stoppa** att frigöra den virtuella datorn.
3. Välj i rutan virtuella **diskar**.
4. Längst upp i den **diskar** väljer **redigera**.
5. I den **diskar** rutan längst till höger på disken för data som du vill koppla från, klicka på den ![Detach bilden](./media/detach-disk/detach.png) frånkoppling knappen.
5. När disken har tagits bort, klickar du på **spara** överst i fönstret.
6. Klicka i den virtuella dator i rutan **översikt** och klicka sedan på den **starta** längst upp för att starta om den virtuella datorn.



Disken finns kvar i lagringsutrymmet men är inte längre kopplad till en virtuell dator.

## <a name="detach-a-data-disk-using-powershell"></a>Koppla från en datadisk med hjälp av PowerShell
I det här exemplet är det första kommandot hämtar den virtuella datorn med namnet **MyVM07** i den **RG11** resurs med det [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet och lagrar den i **$VirtualMachine** variabeln.

Den andra raden tar bort datadisken med namnet DataDisk3 från den virtuella datorn med hjälp av [ta bort AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk) cmdlet.

Den tredje raden uppdaterar tillståndet för den virtuella datorn med hjälp av den [uppdatering AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) för att slutföra processen att ta bort datadisk.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

Mer information finns i [ta bort AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Nästa steg
Om du vill återanvända datadisk du just [koppla den till en annan virtuell dator](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

