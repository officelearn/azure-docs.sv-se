---
title: Hur du tagga en Windows VM-resurs i Azure | Microsoft Docs
description: Lär dig mer om en Windows-dator som skapats i Azure med hjälp av Resource Manager-distributionsmodellen-märkning
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
ms.openlocfilehash: 5044bda855b6ac88eb5784f257686bf8a1838222
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Hur du tagga en virtuell Windows-dator i Azure
Den här artikeln beskrivs olika sätt att märka en virtuell Windows-dator i Azure via Resource Manager-distributionsmodellen. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resursgrupp. Azure stöder för närvarande upp till 15 taggar per resurs och resursgruppen. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller lägga till en befintlig resurs. Observera att taggar stöds för resurser som skapats via den Resource Manager distributionsmodellen. Om du vill att märka en Linux-dator, se [så att märka en Linux-dator i Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Tagga med PowerShell
För att skapa, lägga till och ta bort taggar via PowerShell kan du första behovet av att ställa in din [PowerShell-miljö med Azure Resource Manager][PowerShell environment with Azure Resource Manager]. När du har slutfört installationen kan placera du taggar på beräkning, nätverk och lagring resurser vid skapandet eller när resursen har skapats via PowerShell. Den här artikeln kommer att inriktas på Visa/redigera taggar som placeras på virtuella datorer.

Först gå till en virtuell dator via den `Get-AzureRmVM` cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Om den virtuella datorn redan innehåller taggar kan sedan visas alla taggar på resursen:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Om du vill lägga till taggar via PowerShell kan du använda den `Set-AzureRmResource` kommando. Observera vid uppdatering av taggarna via PowerShell taggar uppdateras som helhet. Så om du lägger till en tagg till en resurs som redan har taggar måste att inkludera alla taggar som du vill ska placeras på resursen. Nedan visas ett exempel på hur du lägger till ytterligare taggar till en resurs via PowerShell-Cmdlets.

Den här första cmdlet anger alla taggar placerade på *MyTestVM* till den *$tags* variabel, med hjälp av den `Get-AzureRmResource` och `Tags` egenskapen.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Det andra kommandot visar etiketter för den angivna variabeln.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

Tredje kommandot lägger till en ytterligare så att den *$tags* variabeln. Observera användningen av den **+=** ska läggas till på nytt nyckel/värde-par till den *$tags* lista.

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

Kommandot fjärde anger alla taggar som definierats i den *$tags* variabeln till den angivna resursen. I det här fallet är det MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Det femte kommandot visar alla taggar på resursen. Som du kan se *plats* nu har definierats som en tagg med *MyLocation* som värde.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

Om du vill veta mer om märkning via PowerShell kan ta en titt på [Cmdlets för Azure-resurs][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Nästa steg
* Läs mer om resurserna i Azure-märkning i [översikt över Azure Resource Manager] [ Azure Resource Manager Overview] och [med taggar för att organisera dina Azure-resurser][Using Tags to organize your Azure Resources].
* Om du vill se hur taggar kan hjälpa dig att hantera din användning av Azure-resurser, se [förstå fakturan Azure] [ Understanding your Azure Bill] och [få insikter om dina Microsoft Azure-resursförbrukning][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
