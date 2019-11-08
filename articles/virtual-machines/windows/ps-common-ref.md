---
title: Vanliga PowerShell-kommandon för Azure Virtual Machines | Microsoft Docs
description: Vanliga PowerShell-kommandon för att komma igång med att skapa och hantera virtuella Windows-datorer i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 24cb9db9dff670ece75db24df873d24c08919722
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749256"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Vanliga PowerShell-kommandon för att skapa och hantera Azure-Virtual Machines

Den här artikeln beskriver några av de Azure PowerShell kommandon som du kan använda för att skapa och hantera virtuella datorer i din Azure-prenumeration.  Om du vill ha mer detaljerad hjälp med specifika kommando rads växlar och alternativ kan du använda *kommandot* **Get-Help** .

 

Dessa variabler kan vara användbara för dig om du kör fler än ett av kommandona i den här artikeln:

- $location-platsen för den virtuella datorn. Du kan använda [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) för att hitta en [geografisk region](https://azure.microsoft.com/regions/) som passar dig.
- $myResourceGroup-namnet på den resurs grupp som innehåller den virtuella datorn.
- $myVM-namnet på den virtuella datorn.

## <a name="create-a-vm---simplified"></a>Skapa en virtuell dator – förenklad

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa en enkel virtuell dator | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> New-AzVM har en uppsättning *förenklade* parametrar, där allt som krävs är ett enda namn. Värdet för-Name används som namn på alla resurser som krävs för att skapa en ny virtuell dator. Du kan ange fler, men det är allt som krävs.|
| Skapa en virtuell dator från en anpassad avbildning | New-AzVm-ResourceGroupName $myResourceGroup-Name $myVM ImageName "image" – location $location  <BR></BR><BR></BR>Du måste redan ha skapat din egen [hanterade avbildning](capture-image-resource.md). Du kan använda en avbildning för att göra flera identiska virtuella datorer. |



## <a name="create-a-vm-configuration"></a>Skapa en VM-konfiguration

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa en VM-konfiguration |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $MyVM-VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfigurationen av den virtuella datorn används för att definiera eller uppdatera inställningarna för den virtuella datorn. Konfigurationen initieras med namnet på den virtuella datorn och dess [storlek](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Lägg till konfigurations inställningar |$vm = [set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $VM-Windows-ComputerName $MyVM-Credential $cred-ProvisionVMAgent-EnableAutoUpdate<BR></BR><BR></BR>Operativ systemets inställningar, inklusive [autentiseringsuppgifter](https://technet.microsoft.com/library/hh849815.aspx) , läggs till i konfigurationsobjektet som du skapade tidigare med New-AzVMConfig. |
| Lägg till ett nätverks gränssnitt |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $VM-ID $NIC. Identitet<BR></BR><BR></BR>En virtuell dator måste ha ett [nätverks gränssnitt](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att kunna kommunicera i ett virtuellt nätverk. Du kan också använda [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) för att hämta ett befintligt nätverks gränssnitts objekt. |
| Ange en plattforms avbildning |$vm = [set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $VM-PublisherName "publisher_name"-erbjudande "publisher_offer"-SKU: er "product_sku"-version "senaste"<BR></BR><BR></BR>[Avbildnings information](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) läggs till i konfigurationsobjektet som du skapade tidigare med New-AzVMConfig. Objektet som returneras från det här kommandot används bara när du anger att operativ system disken ska använda en plattforms avbildning. |
| Skapa en virtuell dator |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $MyResourceGroup-location $location-VM $VM<BR></BR><BR></BR>Alla resurser skapas i en [resurs grupp](../../azure-resource-manager/manage-resource-groups-powershell.md). Innan du kör det här kommandot kör du New-AzVMConfig, set-AzVMOperatingSystem, set-AzVMSourceImage, Add-AzVMNetworkInterface och set-AzVMOSDisk. |
| Uppdatera en virtuell dator |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $MYRESOURCEGROUP-VM $VM<BR></BR><BR></BR>Hämta den aktuella VM-konfigurationen med Get-AzVM, ändra konfigurations inställningarna för VM-objektet och kör sedan det här kommandot. |

## <a name="get-information-about-vms"></a>Hämta information om virtuella datorer

| Aktivitet | Kommando |
| ---- | ------- |
| Lista virtuella datorer i en prenumeration |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Lista virtuella datorer i en resurs grupp |Get-AzVM-ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Använd [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup)om du vill hämta en lista över resurs grupper i din prenumeration. |
| Hämta information om en virtuell dator |Get-AzVM-ResourceGroupName $myResourceGroup-Name $myVM |

## <a name="manage-vms"></a>Hantera virtuella datorer
| Aktivitet | Kommando |
| --- | --- |
| Starta en virtuell dator |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Stoppa en virtuell dator |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) – ResourceGroupName $MyResourceGroup-Name $myVM |
| Starta om en virtuell dator som körs |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Ta bort en virtuell dator |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |


## <a name="next-steps"></a>Nästa steg
* Se de grundläggande stegen för att skapa en virtuell dator i [skapa en virtuell Windows-dator med Resource Manager och PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

