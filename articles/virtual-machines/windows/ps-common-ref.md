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
ms.openlocfilehash: 9d0e27c2427d53554b454e0c319ce9cf180f1633
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820798"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Vanliga PowerShell-kommandon för att skapa och hantera Azure Virtual Machines

Den här artikeln beskriver några av de Azure PowerShell-kommandon som du kan använda för att skapa och hantera virtuella datorer i din Azure-prenumeration.  Mer detaljerad hjälp med specifika kommandoradsväxlar och alternativ du kan använda den **Get-Help** *kommandot*.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Dessa variabler kan vara användbar för dig om körs mer än något av kommandona i den här artikeln:

- $location - platsen för den virtuella datorn. Du kan använda [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) att hitta en [geografisk region](https://azure.microsoft.com/regions/) som passar dig.
- $myResourceGroup - namnet på resursgruppen som innehåller den virtuella datorn.
- $myVM - namnet på den virtuella datorn.

## <a name="create-a-vm---simplified"></a>Skapa en virtuell dator – förenklad

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa en enkel virtuell dator | [Ny AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -namnge $myVM <BR></BR><BR></BR> Ny AzVM har en uppsättning *förenklad* parametrar, där allt som krävs är ett unikt namn. Värdet för - namnet används som namn på alla resurser som krävs för att skapa en ny virtuell dator. Du kan ange mer, men det är allt som krävs.|
| Skapa en virtuell dator från en anpassad avbildning | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>Du måste redan har skapat dina egna [hanterad avbildning](capture-image-resource.md). Du kan använda en avbildning för att göra flera identiska virtuella datorer. |



## <a name="create-a-vm-configuration"></a>Skapa en VM-konfiguration

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa en VM-konfiguration |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>VM-konfigurationen används för att definiera eller uppdatera inställningarna för den virtuella datorn. Konfigurationen har initierats med namnet på den virtuella datorn och dess [storlek](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Lägg till konfigurationsinställningar |$vm = [set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-autentiseringsuppgift $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Inställningar för operativsystem, inklusive [autentiseringsuppgifter](https://technet.microsoft.com/library/hh849815.aspx) läggs till i konfigurationsobjektet som du skapade tidigare med hjälp av New-AzVMConfig. |
| Lägg till ett nätverksgränssnitt |$vm = [Lägg till AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) - VM $vm-Id $nic ID<BR></BR><BR></BR>En virtuell dator måste ha en [nätverksgränssnittet](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kommunicera på ett virtuellt nätverk. Du kan också använda [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) att hämta en befintlig objektet för nätverksgränssnittet. |
| Ange en plattformsavbildning |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[Bild information](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) läggs till i konfigurationsobjektet som du skapade tidigare med hjälp av New-AzVMConfig. Det objekt som returneras från det här kommandot används endast när du ställer in OS-disken du använder en plattformsavbildning. |
| Skapa en virtuell dator |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Alla resurser som skapas i en [resursgrupp](../../azure-resource-manager/manage-resource-groups-powershell.md). Innan du kör det här kommandot Kör New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Lägg till AzVMNetworkInterface och Set-AzVMOSDisk. |
| Uppdatera en virtuell dator |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Hämta den aktuella konfigurationen för virtuell dator med hjälp av Get-AzVM, ändra konfigurationsinställningarna för VM-objektet och kör sedan det här kommandot. |

## <a name="get-information-about-vms"></a>Få information om virtuella datorer

| Aktivitet | Kommando |
| ---- | ------- |
| Lista över virtuella datorer i en prenumeration |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Lista över virtuella datorer i en resursgrupp |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Hämta en lista över resursgrupper i din prenumeration med [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Hämta information om en virtuell dator |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Hantera virtuella datorer
| Aktivitet | Kommando |
| --- | --- |
| Starta en virtuell dator |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Stoppa en virtuell dator |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Starta om en aktiv virtuell dator |[Omstart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) - ResourceGroupName $myResourceGroup-namnge $myVM |
| Ta bort en virtuell dator |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Nästa steg
* Se de grundläggande stegen för att skapa en virtuell dator i [skapa en Windows virtuell dator med Resource Manager och PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

