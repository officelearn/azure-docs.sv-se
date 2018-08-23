---
title: Vanliga PowerShell-kommandon för Azure Virtual Machines | Microsoft Docs
description: Vanliga PowerShell-kommandon för att komma igång med att skapa och hantera dina virtuella Windows-datorer i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: cb9f03ab87079ba33135840e3e7599d2e8cc95e9
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "42055376"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Vanliga PowerShell-kommandon för att skapa och hantera Azure Virtual Machines

Den här artikeln beskriver några av de Azure PowerShell-kommandon som du kan använda för att skapa och hantera virtuella datorer i din Azure-prenumeration.  Mer detaljerad hjälp med specifika kommandoradsväxlar och alternativ du kan använda den **Get-Help** *kommandot*.

Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

Dessa variabler kan vara användbar för dig om körs mer än något av kommandona i den här artikeln:

- $location - platsen för den virtuella datorn. Du kan använda [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) att hitta en [geografisk region](https://azure.microsoft.com/regions/) som passar dig.
- $myResourceGroup - namnet på resursgruppen som innehåller den virtuella datorn.
- $myVM - namnet på den virtuella datorn.

## <a name="create-a-vm---simplified"></a>Skapa en virtuell dator – förenklad

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa en enkel virtuell dator | [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -namnge $myVM <BR></BR><BR></BR> New-AzureRMVM har en uppsättning *förenklad* parametrar, där allt som krävs är ett unikt namn. Värdet för - namnet används som namn på alla resurser som krävs för att skapa en ny virtuell dator. Du kan ange mer, men det är allt som krävs.|
| Skapa en virtuell dator från en anpassad avbildning | New-AzureRmVm - ResourceGroupName $myResourceGroup-namnge $myVM ImageName ”myImage”-plats $location  <BR></BR><BR></BR>Du måste redan har skapat dina egna [hanterad avbildning](capture-image-resource.md). Du kan använda en avbildning för att göra flera identiska virtuella datorer. |



## <a name="create-a-vm-configuration"></a>Skapa en VM-konfiguration

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa en VM-konfiguration |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) - VMName $myVM - VMSize ”Standard_D1_v1”<BR></BR><BR></BR>VM-konfigurationen används för att definiera eller uppdatera inställningarna för den virtuella datorn. Konfigurationen har initierats med namnet på den virtuella datorn och dess [storlek](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Lägg till konfigurationsinställningar |$vm = [set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-autentiseringsuppgift $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Inställningar för operativsystem, inklusive [autentiseringsuppgifter](https://technet.microsoft.com/library/hh849815.aspx) läggs till i konfigurationsobjektet som du skapade tidigare med hjälp av New-AzureRmVMConfig. |
| Lägg till ett nätverksgränssnitt |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/Add-AzureRmVMNetworkInterface) - VM $vm-Id $nic ID<BR></BR><BR></BR>En virtuell dator måste ha en [nätverksgränssnittet](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kommunicera på ett virtuellt nätverk. Du kan också använda [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) att hämta en befintlig objektet för nätverksgränssnittet. |
| Ange en plattformsavbildning |$vm = [set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - PublisherName ”publisher_name”-erbjudande ”publisher_offer” - SKU: er ”product_sku”-Version ”senaste”<BR></BR><BR></BR>[Bild information](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) läggs till i konfigurationsobjektet som du skapade tidigare med hjälp av New-AzureRmVMConfig. Det objekt som returneras från det här kommandot används endast när du ställer in OS-disken du använder en plattformsavbildning. |
| Skapa en virtuell dator |[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) - ResourceGroupName $myResourceGroup-plats $location - VM $vm<BR></BR><BR></BR>Alla resurser som skapas i en [resursgrupp](../../azure-resource-manager/powershell-azure-resource-manager.md). Innan du kör det här kommandot Kör New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface och Set-AzureRmVMOSDisk. |
| Uppdatera en virtuell dator |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) - ResourceGroupName $myResourceGroup - VM $vm<BR></BR><BR></BR>Hämta den aktuella konfigurationen för virtuell dator med hjälp av Get-AzureRmVM, ändra konfigurationsinställningarna för VM-objektet och kör sedan det här kommandot. |

## <a name="get-information-about-vms"></a>Få information om virtuella datorer

| Aktivitet | Kommando |
| ---- | ------- |
| Lista över virtuella datorer i en prenumeration |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Lista över virtuella datorer i en resursgrupp |Get-AzureRmVM - ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Hämta en lista över resursgrupper i din prenumeration med [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Hämta information om en virtuell dator |Get-AzureRmVM - ResourceGroupName $myResourceGroup-namnge $myVM |

## <a name="manage-vms"></a>Hantera virtuella datorer
| Aktivitet | Kommando |
| --- | --- |
| Starta en virtuell dator |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) - ResourceGroupName $myResourceGroup-namnge $myVM |
| Stoppa en virtuell dator |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) - ResourceGroupName $myResourceGroup-namnge $myVM |
| Starta om en aktiv virtuell dator |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) - ResourceGroupName $myResourceGroup-namnge $myVM |
| Ta bort en virtuell dator |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) - ResourceGroupName $myResourceGroup-namnge $myVM |


## <a name="next-steps"></a>Nästa steg
* Se de grundläggande stegen för att skapa en virtuell dator i [skapa en Windows virtuell dator med Resource Manager och PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

