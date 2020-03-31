---
title: Så här taggar du en Windows VM-resurs i Azure
description: Lär dig mer om att tagga en virtuell Windows-dator som skapats i Azure med hjälp av distributionsmodellen för Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: b646b1a14d6cedcafa662192229daa570a0d2441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616433"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Så här taggar du en virtuell Windows-dator i Azure
I den här artikeln beskrivs olika sätt att tagga en virtuell Windows-dator i Azure via distributionsmodellen för Resource Manager. Taggar är användardefinierade nyckel-/värdepar som kan placeras direkt på en resurs eller en resursgrupp. Azure stöder för närvarande upp till 50 taggar per resurs och resursgrupp. Taggar kan placeras på en resurs när de skapas eller läggs till i en befintlig resurs. Observera att taggar stöds endast för resurser som skapats via resurshanterarens distributionsmodell. Om du vill tagga en virtuell Linux-dator läser du Så här taggar du [en virtuell Linux-dator i Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Taggning med PowerShell
Om du vill skapa, lägga till och ta bort taggar via PowerShell måste du först konfigurera [PowerShell-miljön med Azure Resource Manager][PowerShell environment with Azure Resource Manager]. När du har slutfört installationen kan du placera taggar på beräknings-, nätverks- och lagringsresurser när du skapar eller efter att resursen har skapats via PowerShell. Den här artikeln kommer att koncentrera sig på visning / redigering taggar placeras på virtuella datorer.

 

Navigera först till en virtuell `Get-AzVM` dator genom cmdleten.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Om den virtuella datorn redan innehåller taggar visas alla taggar på resursen:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Om du vill lägga till taggar via PowerShell kan du använda kommandot. `Set-AzResource` Observera att när taggar uppdateras via PowerShell uppdateras taggarna som en helhet. Så om du lägger till en tagg i en resurs som redan har taggar måste du inkludera alla taggar som du vill placeras på resursen. Nedan följer ett exempel på hur du lägger till ytterligare taggar till en resurs via PowerShell-cmdlets.

Den här första cmdleten ställer in alla taggar som placeras på `Get-AzResource` *MyTestVM* till *variabeln $tags* med hjälp av egenskapen och. `Tags`

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Det andra kommandot visar taggarna för den angivna variabeln.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

Det tredje kommandot lägger till ytterligare en tagg i *variabeln $tags.* Observera att den **+=** nya nyckeln/värdeparet används för att lägga till den nya nyckeln/värdet i *listan över $tags.*

        PS C:\> $tags += @{Location="MyLocation"}

Det fjärde kommandot anger alla taggar som definierats i *variabeln $tags* till den angivna resursen. I det här fallet är det MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Det femte kommandot visar alla taggar på resursen. Som du kan se definieras *platsen* nu som en tagg med *MyLocation* som värde.

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

Om du vill veta mer om hur du taggar via PowerShell finns i [Azure Resource Cmdlets][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du taggar dina Azure-resurser finns i [Översikt över Azure Resource Manager][Azure Resource Manager Overview] och Använda taggar för att ordna dina [Azure-resurser][Using Tags to organize your Azure Resources].
* Information om hur taggar kan hjälpa dig att hantera din användning av Azure-resurser finns i [Förstå din Azure-faktura][Understanding your Azure Bill] och [få insikter om din Microsoft Azure-resursförbrukning][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
