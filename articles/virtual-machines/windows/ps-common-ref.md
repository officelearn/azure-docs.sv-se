---
title: Vanliga PowerShell-kommandon för Azure Virtual Machines | Microsoft Docs
description: Vanliga PowerShell-kommandon för att komma igång med att skapa och hantera dina virtuella Windows-datorer i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: f84473e73a32da43cc6cc80b21deb49ab4f3ceb9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Vanliga PowerShell-kommandon för att skapa och hantera virtuella datorer i Azure

Den här artikeln beskrivs några av de Azure PowerShell-kommandon som du kan använda för att skapa och hantera virtuella datorer i din Azure-prenumeration.  Mer detaljerad hjälp med specifika kommandoradsväxlar och alternativ, kan du använda **Get-Help** *kommandot*.

Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

Dessa variabler kan vara praktiskt om körs mer än något av kommandona i den här artikeln:

- $location - platsen för den virtuella datorn. Du kan använda [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) att hitta en [geografisk region](https://azure.microsoft.com/regions/) som passar dig.
- $myResourceGroup - namnet på resursgruppen som innehåller den virtuella datorn.
- $myVM - namnet på den virtuella datorn.

## <a name="create-a-vm"></a>Skapa en virtuell dator

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa en VM-konfiguration |$vm = [ny AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) - VMName $myVM - VMSize ”Standard_D1_v1”<BR></BR><BR></BR>VM-konfiguration används för att definiera eller uppdatera inställningarna för den virtuella datorn. Konfigurationen har initierats med namnet på den virtuella datorn och dess [storlek](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Lägg till konfigurationsinställningar |$vm = [set AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-autentiseringsuppgifter $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Inställningar för operativsystem inklusive [autentiseringsuppgifter](https://technet.microsoft.com/library/hh849815.aspx) läggs till i konfigurationsobjektet som du skapat med hjälp av ny AzureRmVMConfig. |
| Lägg till ett nätverksgränssnitt |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>En virtuell dator måste ha en [nätverksgränssnittet](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kommunicera på ett virtuellt nätverk. Du kan också använda [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) att hämta ett befintligt nätverk gränssnittet objekt. |
| Ange en plattformsavbildning |$vm = [set AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - PublisherName ”publisher_name”-erbjudande ”publisher_offer” - SKU: er ”product_sku”-”senaste” Version<BR></BR><BR></BR>[Bild information](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) har lagts till i konfigurationsobjektet som du skapat med hjälp av ny AzureRmVMConfig. Objektet som returnerades från det här kommandot används bara när du ställer in OS-disken att använda en plattformsavbildning. |
| Ange OS-disken att använda en plattformsavbildning |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>Namnet på disken för operativsystemet och dess plats i [lagring](../../storage/common/storage-powershell-guide-full.md) har lagts till i konfigurationsobjektet som du skapade tidigare. |
| Ange OS-disken att använda en generaliserad avbildning |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>Namnet på operativsystemets disk, platsen för källbilden och diskplatsen i [lagring](../../storage/common/storage-powershell-guide-full.md) har lagts till i konfigurationsobjektet. |
| Ange OS-disken att använda en specialiserad avbildning |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Skapa en virtuell dator |[Nya AzureRmVM]() - ResourceGroupName $myResourceGroup-plats $location - VM $vm<BR></BR><BR></BR>Alla resurser skapas i en [resursgruppen](../../azure-resource-manager/powershell-azure-resource-manager.md). Innan du kör det här kommandot Kör New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Lägg till AzureRmVMNetworkInterface och Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Hämta information om virtuella datorer

| Aktivitet | Kommando |
| ---- | ------- |
| Lista över virtuella datorer i en prenumeration |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Lista över virtuella datorer i en resursgrupp |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Om du vill hämta en lista över resursgrupper i din prenumeration, Använd [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Hämta information om en virtuell dator |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Hantera virtuella datorer
| Aktivitet | Kommando |
| --- | --- |
| Starta en virtuell dator |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) - ResourceGroupName $myResourceGroup-Name $myVM |
| Stoppa en virtuell dator |[Stoppa AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) - ResourceGroupName $myResourceGroup-Name $myVM |
| Starta om en aktiv virtuell dator |[Starta om AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) - ResourceGroupName $myResourceGroup-Name $myVM |
| Ta bort en virtuell dator |[Ta bort AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) - ResourceGroupName $myResourceGroup-Name $myVM |
| Generalisera en virtuell dator |[Ange AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) - ResourceGroupName $myResourceGroup-Name $myVM-generaliserad<BR></BR><BR></BR>Kör kommandot innan du kör spara AzureRmVMImage. |
| Avbilda en virtuell dator |[Spara AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) - ResourceGroupName $myResourceGroup - VMName $myVM - DestinationContainerName ”myImageContainer” - VHDNamePrefix ”myImagePrefix”-sökvägen ”C:\filepath\filename.json”<BR></BR><BR></BR>En virtuell dator måste vara [förberedd, Stäng och generaliserad](prepare-for-upload-vhd-image.md) som används för att skapa en avbildning. Innan du kör det här kommandot Kör Set-AzureRmVm. |
| Uppdatera en virtuell dator |[Uppdatera AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) - ResourceGroupName $myResourceGroup - VM $vm<BR></BR><BR></BR>Hämta den aktuella VM-konfigurationen med hjälp av Get-AzureRmVM, ändra konfigurationsinställningarna på det Virtuella datorobjektet och kör det här kommandot. |
| Lägg till en datadisk i en virtuell dator |[Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) -VM $vm -Name "myDataDisk" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Använd Get-AzureRmVM för att hämta det Virtuella datorobjektet. Ange LUN-nummer och storleken på disken. Kör Update-AzureRmVM för att tillämpa ändringar i konfigurationen på den virtuella datorn. Disken som du lägger till har inte initierats. |
| Ta bort en datadisk från en virtuell dator |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Använd Get-AzureRmVM för att hämta det Virtuella datorobjektet. Kör Update-AzureRmVM för att tillämpa ändringar i konfigurationen på den virtuella datorn. |
| Lägga till ett tillägg till en virtuell dator |[Ange AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) - ResourceGroupName $myResourceGroup-plats $location - VMName $myVM-Name ”Tilläggsnamn”-Publisher ”publisherName”-typ ”extensionType” - TypeHandlerVersion ”#. #”-inställningar $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Kör det här kommandot med rätt [konfigurationsinformation](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) för det tillägg som du vill installera. |
| Ta bort ett virtuellt datortillägg |[Ta bort AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) - ResourceGroupName $myResourceGroup-Name ”Tilläggsnamn” - VMName $myVM |

## <a name="next-steps"></a>Nästa steg
* Se stegen för att skapa en virtuell dator i [skapa en virtuell Windows-dator med Resource Manager och PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

