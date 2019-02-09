---
title: Tagga en Windows VM-resurs i Azure | Microsoft Docs
description: Lär dig mer om att tagga en Windows-dator som skapats i Azure med hjälp av Resource Manager-distributionsmodellen
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: ca389814b35a666a48959a50de58a231df6728c5
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981136"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Tagga en virtuell Windows-dator i Azure
Den här artikeln beskrivs olika sätt att tagga en virtuell Windows-dator i Azure via Resource Manager-distributionsmodellen. Taggar är en användardefinierad nyckel/värde-par som kan placeras direkt på en resurs eller resursgrupp. Azure stöder för närvarande upp till 15 taggar per resurs och resursgruppen. Taggar kan placeras på en resurs när den skapas eller lagts till i en befintlig resurs. Observera att taggar stöds för resurser som har skapats via Resource Manager-distributionsmodellen endast. Om du vill tagga en virtuell Linux-dator, se [tagga en virtuell Linux-dator i Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Tagga med PowerShell
För att skapa, lägga till och ta bort taggar via PowerShell kan du första behovet av att ställa in din [PowerShell-miljö med Azure Resource Manager][PowerShell environment with Azure Resource Manager]. När du har slutfört installationen, kan du placera taggar på beräkning, nätverk och lagring resurser vid skapandet eller när resursen har skapats via PowerShell. Den här artikeln kommer att inriktas på Visa/redigera taggar som placeras på virtuella datorer.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Navigera först till en virtuell dator via den `Get-AzVM` cmdlet.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Om den virtuella datorn innehåller redan taggar, därefter visas alla taggar på resursen:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Om du vill lägga till taggar via PowerShell kan du använda den `Set-AzResource` kommando. Observera när du uppdaterar taggar via PowerShell, taggar uppdateras som helhet. Så om du lägger till en tagg till en resurs som redan har taggar måste att inkludera alla taggar som du vill ska kunna placeras på resursen. Nedan visas ett exempel på hur du lägger till ytterligare taggar till en resurs via PowerShell-Cmdlets.

Den här första cmdlet: en anger alla taggar som placeras på *MyTestVM* till den *$tags* variabeln, med hjälp av den `Get-AzResource` och `Tags` egenskapen.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Det andra kommandot visar taggar för den angivna variabeln.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

Tredje kommandot lägger till en ytterligare tagg till den *$tags* variabeln. Observera användningen av den **+=** att lägga till nytt nyckel/värde-par till den *$tags* lista.

        PS C:\> $tags += @{Location="MyLocation"}

Det fjärde kommandot anger alla taggar som definierats i den *$tags* variabeln till den angivna resursen. I det här fallet är det MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Det femte kommandot visar alla taggar för resursen. Som du kan se *plats* nu har definierats som en tagg med *MyLocation* som värde.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Om du vill veta mer om taggning via PowerShell kan ta en titt på [Cmdlets för Azure Resource][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Nästa steg
* Läs mer om tagga dina Azure-resurser i [översikt över Azure Resource Manager] [ Azure Resource Manager Overview] och [med taggar för att organisera dina Azure-resurser] [ Using Tags to organize your Azure Resources].
* Om du vill se hur taggar kan hjälpa dig att hantera din användning av Azure-resurser, se [förstå din Azure-faktura] [ Understanding your Azure Bill] och [insyn i din Microsoft Azure-resursförbrukning] [Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
