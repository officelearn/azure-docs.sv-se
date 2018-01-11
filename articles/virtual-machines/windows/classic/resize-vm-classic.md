---
title: "Ändra storlek på en virtuell Windows-dator i den klassiska distributionsmodellen - Azure | Microsoft Docs"
description: "Ändra storlek på en Windows-dator som skapats i den klassiska distributionsmodellen med hjälp av Azure Powershell."
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 4277bc8394c7ba140291e9dc776162e87deab96b
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Ändra storlek på en Windows-VM som skapats i den klassiska distributionsmodellen
Den här artikeln visar hur du ändrar storlek på en Windows-VM som skapats i den klassiska distributionsmodellen med hjälp av Azure Powershell.

När du överväger möjligheten att ändra storlek på en virtuell dator, finns det två principer som styr de storlekar som finns tillgängliga för att ändra storlek på den virtuella datorn. Det första begreppet som är den region där den virtuella datorn distribueras. Listan över storlekar på VM tillgängligt i region är under fliken tjänster på webbsidan som Azure-regioner. Andra konceptet är den fysiska maskinvara som för närvarande är värd för den virtuella datorn. De fysiska servrarna som är värd för virtuella datorer grupperas i kluster vanliga fysisk maskinvara. Metoden för att ändra en VM-storlek skiljer sig åt beroende på om önskade nya VM-storlek som stöds av maskinvaran klustret för närvarande är värd för den virtuella datorn.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Du kan också [ändra storlek på en virtuell dator som skapats i Resource Manager-distributionsmodellen](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-your-account"></a>Lägg till ditt konto
Du måste konfigurera Azure PowerShell för att arbeta med klassiska Azure-resurser. Följ stegen nedan för att konfigurera Azure PowerShell för att hantera klassiska resurser.

1. I PowerShell-Kommandotolken skriver `Add-AzureAccount` och på **RETUR**. 
2. Skriv e-postadressen som är associerade med din Azure-prenumeration och klicka på **Fortsätt**. 
3. Skriv in lösenordet för ditt konto. 
4. Klicka på **logga in**. 

## <a name="resize-in-the-same-hardware-cluster"></a>Ändra storlek på i samma kluster för maskinvara
Om du vill ändra storlek på en virtuell dator till en storlek som är tillgängliga i klustret för maskinvara som värd för den virtuella datorn, utför du följande steg.

1. Kör följande PowerShell-kommando för att lista storlek på Virtuella datorer finns i maskinvara-kluster som värd för Molntjänsten som innehåller den virtuella datorn.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. Kör följande kommandon för att ändra storlek på den virtuella datorn.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Ändra storlek på ett nytt kluster för maskinvara
Ändra storlek på en virtuell dator till en storlek som är inte tillgänglig i klustret för maskinvara som värd för den virtuella datorn molnet tjänsten och alla virtuella datorer i Molntjänsten måste skapas på nytt. Varje tjänst i molnet finns på ett kluster med samma maskinvara så att alla virtuella datorer i Molntjänsten måste ha en storlek som stöds på ett kluster för maskinvara. Följande steg beskriver hur du ändrar storlek på en virtuell dator genom att ta bort och återskapa Molntjänsten.

1. Kör följande PowerShell-kommando för att lista storlek på Virtuella datorer tillgängligt i region. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. Anteckna alla konfigurationsinställningar för varje virtuell dator i den molntjänst som innehåller den virtuella datorn ska ändras. 
3. Ta bort alla virtuella datorer i Molntjänsten att välja alternativet att behålla diskarna för varje virtuell dator.
4. Skapa den virtuella datorn ska ändras med den önskade VM-storleken.
5. Återskapa alla andra virtuella datorer som fanns i Molntjänsten med hjälp av en VM-storlek som är tillgängliga i klustret maskinvara nu värd för Molntjänsten.

Ett exempelskript för att ta bort och återskapa en tjänst i molnet med hjälp av en ny VM-storlek kan hittas [här](https://github.com/Azure/azure-vm-scripts). 

## <a name="next-steps"></a>Nästa steg
* [Ändra storlek på en virtuell dator som skapats i Resource Manager-distributionsmodellen](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

