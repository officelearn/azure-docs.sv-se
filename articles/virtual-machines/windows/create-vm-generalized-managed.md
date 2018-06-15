---
title: Skapa virtuell dator från en hanterad avbildning i Azure | Microsoft Docs
description: Skapa en virtuell Windows-dator från en generaliserad hanterad avbildning med hjälp av Azure PowerShell eller Azure-portalen i Resource Manager-distributionsmodellen.
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
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239063"
---
# <a name="create-a-vm-from-a-managed-image"></a>Skapa en virtuell dator från en hanterad avbildning

Du kan skapa flera virtuella datorer från en hanterad VM-avbildning med hjälp av PowerShell eller Azure-portalen. En hanterad VM-avbildning innehåller informationen som behövs för att skapa en virtuell dator, inklusive Operativsystemet och datadiskarna. De virtuella hårddiskar som bilden, inklusive både OS-diskar och eventuella hårddiskar, lagras som hanterade diskar. 

Du måste redan ha [skapas en hanterade VM-avbildning](capture-image-resource.md) ska användas för att skapa den nya virtuella datorn. 

## <a name="use-the-portal"></a>Använda portalen

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser** i menyn till vänster. Du kan sortera resurser efter **typen** att enkelt hitta bilderna.
3. Välj den avbildning du vill använda i listan. Bilden **översikt** öppnas.
4. Klicka på **+ skapa VM** på menyn.
5. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. När du är klar klickar du på **OK**. Du kan skapa den nya virtuella datorn i en befintlig grupp Resrouce eller välj **Skapa nytt** att skapa en ny resursgrupp för att lagra den virtuella datorn.
6. Välj en storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. 
7. Under **inställningar**, gör önskade ändringar och klicka på **OK**. 
8. På sidan Sammanfattning bör du se ditt namn visas för **privata avbildningen**. Klicka på **Ok** att starta distributionen av virtuella datorer.


## <a name="use-powershell"></a>Använd PowerShell

Du kan använda PowerShell för att skapa en virtuell dator från en avbildning med hjälp av den förenklade parameter har angetts för den [ny AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) cmdlet. Bilden måste vara i samma resursgrupp som du vill skapa den virtuella datorn.

Det här exemplet kräver AzureRM Modulversion 5.6.0 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Förenklad parameteruppsättningen för ny AzureRmVm endast kräver att du anger ett namn, grupp- och bildfiler resursnamnet att skapa en virtuell dator från en avbildning, men använder värdet för den **-namnet** parameter som namnet på alla resurser som den skapar automatiskt. I det här exemplet vi ger mer detaljerad namn för varje resursen, men låta cmdleten skapa dem automatiskt. Du kan också skapa resurser, t.ex. det virtuella nätverket i förväg och skicka namnet till cmdleten. Om den hittar dem efter deras använder den befintliga resurser.

I följande exempel skapas en virtuell dator med namnet *myVMFromImage*i den *myResourceGroup* resursgruppen från avbildningen med namnet *myImage*. 


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
För att hantera din nya virtuella datorn med Azure PowerShell, se [skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

