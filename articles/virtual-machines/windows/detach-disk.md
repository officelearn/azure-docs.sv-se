---
title: "Koppla från en datadisk från en Windows-VM - Azure | Microsoft Docs"
description: "Lär dig att koppla från en datadisk från en virtuell dator i Azure med hjälp av Resource Manager-distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: bbbd31313db44d32a829e9e4c6c9b5fd9c0e533e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Hur du koppla från en datadisk från en Windows-dator
När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Detta tar bort disken från den virtuella datorn, men ta bort inte den från lagring.

> [!WARNING]
> Om du koppla från en disk som den inte tas bort automatiskt. Om du har prenumererat på Premium-lagring, fortsätter du lagring avgifter för disken. Mer information finns i [priser och fakturering när du använder Premiumlagring](premium-storage.md#pricing-and-billing).
>
>

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.

## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen
1. I portalen hubben, väljer **virtuella datorer**.
2. Välj den virtuella dator som har datadisk som du vill koppla från och klickar på **stoppa** att frigöra den virtuella datorn.
3. I bladet för virtuella datorer väljer du **diskar**.
4. Längst upp i den **diskar** bladet väljer **redigera**.
5. I den **diskar** bladet längst till höger på disken för data som du vill koppla från, klicka på den ![Detach bilden](./media/detach-disk/detach.png) frånkoppling knappen.
5. När disken har tagits bort, klicka på Spara överst på bladet.
6. I bladet för virtuella datorer, klickar du på **översikt** och klicka sedan på den **starta** längst upp på bladet för att starta om den virtuella datorn.



Disken finns kvar i lagringsutrymmet men är inte längre kopplad till en virtuell dator.

## <a name="detach-a-data-disk-using-powershell"></a>Koppla från en datadisk med hjälp av PowerShell
I det här exemplet är det första kommandot hämtar den virtuella datorn med namnet **MyVM07** i den **RG11** resursgrupp med hjälp av cmdleten Get-AzureRmVM. Kommandot lagrar den virtuella datorn i den **$VirtualMachine** variabeln.

Det andra kommandot tar bort datadisken med namnet DataDisk3 från den virtuella datorn.

Kommandot uppdaterar tillståndet för den virtuella datorn för att slutföra processen för att ta bort datadisken.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```

Mer information finns i [ta bort AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Nästa steg
Om du vill återanvända datadisk du just [koppla den till en annan virtuell dator](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

