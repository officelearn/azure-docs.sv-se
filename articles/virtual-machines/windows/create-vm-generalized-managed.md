---
title: Skapa virtuell dator från en hanterad avbildning i Azure | Microsoft Docs
description: Skapa en virtuell Windows-dator från en generaliserad hanterad avbildning med Azure PowerShell eller Azure-portalen i Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 6baf784068b1fba0c35d2848b8d2dda4f1064a2d
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867988"
---
# <a name="create-a-vm-from-a-managed-image"></a>Skapa en virtuell dator från en hanterad avbildning

Du kan skapa flera virtuella datorer från en hanterad avbildning med hjälp av PowerShell eller Azure-portalen. En hanterad datoravbildning av virtuell innehåller informationen som krävs för att skapa en virtuell dator, inklusive operativsystem och datadiskar. De virtuella hårddiskar som bilden, både för OS-diskar och eventuella datadiskar, lagras som hanterade diskar. 

Du måste redan ha [skapas en hanterad avbildning](capture-image-resource.md) du använder för att skapa den nya virtuella datorn. 

## <a name="use-the-portal"></a>Använda portalen

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser** i menyn till vänster. Du kan sortera resurser efter **typ** att enkelt hitta dina avbildningar.
3. Välj den avbildning som du vill använda i listan. Avbildningen **översikt** öppnas.
4. Klicka på **+ skapa VM** på menyn.
5. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. När du är klar klickar du på **OK**. Du kan skapa den nya virtuella datorn i en befintlig resursgrupp eller välj **Skapa nytt** att skapa en ny resursgrupp för att lagra den virtuella datorn.
6. Välj en storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. 
7. Under **inställningar**, gör önskade ändringar och klicka på **OK**. 
8. På sidan Sammanfattning du bör se din Avbildningsnamnet för **privat avbildning**. Klicka på **Ok** att starta distributionen av virtuella datorn.


## <a name="use-powershell"></a>Använd PowerShell

Du kan använda PowerShell för att skapa en virtuell dator från en avbildning med hjälp av förenklad parameteruppsättning för den [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) cmdlet. Avbildningen måste finnas i samma resursgrupp där du vill skapa den virtuella datorn.

Det här exemplet kräver AzureRM-Modulversion 5.6.0 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Förenklad parameteruppsättning för New-AzureRmVm endast kräver att du anger ett namn och en grupp och avbildningen namn att skapa en virtuell dator från en avbildning, men den kommer att använda värdet för den **-namnet** parametern som namnet på alla resurser som den skapar automatiskt. I det här exemplet vi ger mer detaljerad namn för varje resurs, men låta cmdleten skapa dem automatiskt. Du kan också skapa resurser, t.ex. det virtuella nätverket, förbereds i förväg och skicka namnet i cmdleten. Använder befintliga resurser om det kan hitta dem efter deras namn.

I följande exempel skapas en virtuell dator med namnet *myVMFromImage*i den *myResourceGroup* resursgrupp från avbildningen med namnet *myImage*. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>Nästa steg
För att hantera din nya virtuella dator med Azure PowerShell, se [skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

