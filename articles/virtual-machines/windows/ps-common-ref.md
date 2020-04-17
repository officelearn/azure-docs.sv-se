---
title: Vanliga PowerShell-kommandon för virtuella Azure-datorer
description: Vanliga PowerShell-kommandon för att komma igång med att skapa och hantera dina virtuella Windows-datorer i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3d08693b6b07b5a2f1fb40d0c4758ab43729bd76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456370"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Vanliga PowerShell-kommandon för att skapa och hantera Azure Virtual Machines

Den här artikeln innehåller några av Azure PowerShell-kommandon som du kan använda för att skapa och hantera virtuella datorer i din Azure-prenumeration.  Om du vill ha mer detaljerad hjälp med specifika kommandoradsväxlar och alternativ kan du använda *kommandot* **Hämta hjälp** .

 

Dessa variabler kan vara användbara för dig om du kör mer än ett av kommandona i den här artikeln:

- $location - Platsen för den virtuella datorn. Du kan använda [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) för att hitta en [geografisk region](https://azure.microsoft.com/regions/) som fungerar för dig.
- $myResourceGroup - Namnet på resursgruppen som innehåller den virtuella datorn.
- $myVM - Namnet på den virtuella datorn.

## <a name="create-a-vm---simplified"></a>Skapa en virtuell dator – förenklad

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa en enkel virtuell dator | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Namn $myVM <BR></BR><BR></BR> New-AzVM har en uppsättning *förenklade* parametrar, där allt som krävs är ett enda namn. Värdet för -Namn används som namn för alla resurser som krävs för att skapa en ny virtuell dator. Du kan ange mer, men det här är allt som krävs.|
| Skapa en virtuell dator från en anpassad avbildning | New-AzVm -ResourceGroupName $myResourceGroup -Namn $myVM ImageName "myImage" -Plats $location  <BR></BR><BR></BR>Du måste redan ha skapat en egen [hanterad avbildning](capture-image-resource.md). Du kan använda en bild för att skapa flera, identiska virtuella datorer. |



## <a name="create-a-vm-configuration"></a>Skapa en VM-konfiguration

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa en VM-konfiguration |$vm = [Ny-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>Vm-konfigurationen används för att definiera eller uppdatera inställningar för den virtuella datorn. Konfigurationen initieras med namnet på den virtuella datorn och dess [storlek](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Lägga till konfigurationsinställningar |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Autentiseringsuppgifter $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Operativsystemets inställningar inklusive [autentiseringsuppgifter](https://technet.microsoft.com/library/hh849815.aspx) läggs till i konfigurationsobjektet som du tidigare skapade med New-AzVMConfig. |
| Lägga till ett nätverksgränssnitt |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic. Id<BR></BR><BR></BR>En virtuell dator måste ha ett [nätverksgränssnitt](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att kommunicera i ett virtuellt nätverk. Du kan också använda [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) för att hämta ett befintligt nätverksgränssnittsobjekt. |
| Ange en plattformsavbildning |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Erbjudandet "publisher_offer" -Skus "product_sku" -Version "senaste"<BR></BR><BR></BR>[Bildinformation](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) läggs till i konfigurationsobjektet som du tidigare skapade med New-AzVMConfig. Objektet som returneras från det här kommandot används bara när du ställer in OS-disken så att en plattformsavbildning ska användas. |
| Skapa en virtuell dator |[Ny-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Plats $location -VM-$vm<BR></BR><BR></BR>Alla resurser skapas i en [resursgrupp](../../azure-resource-manager/management/manage-resource-groups-powershell.md). Innan du kör det här kommandot kör du New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface och Set-AzVMOSDisk. |
| Uppdatera en virtuell dator |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Hämta den aktuella VM-konfigurationen med Get-AzVM, ändra konfigurationsinställningarna för VM-objektet och kör sedan det här kommandot. |

## <a name="get-information-about-vms"></a>Få information om virtuella datorer

| Aktivitet | Kommando |
| ---- | ------- |
| Lista virtuella datorer i en prenumeration |[Få-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Lista virtuella datorer i en resursgrupp |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Om du vill hämta en lista över resursgrupper i din prenumeration använder du [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Hämta information om en virtuell dator |Hämta-AzVM -ResourceGroupName $myResourceGroup -Namn $myVM |

## <a name="manage-vms"></a>Hantera virtuella datorer
| Aktivitet | Kommando |
| --- | --- |
| Starta en virtuell dator |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Namn $myVM |
| Stoppa en virtuell dator |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Namn $myVM |
| Starta om en virtuell dator som körs |[Starta om-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Namn $myVM |
| Ta bort en virtuell dator |[Ta bort-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Namn $myVM |


## <a name="next-steps"></a>Nästa steg
* Se de grundläggande stegen för att skapa en virtuell dator i [Skapa en Virtuell Windows-dator med Hjälp av Resource Manager och PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

