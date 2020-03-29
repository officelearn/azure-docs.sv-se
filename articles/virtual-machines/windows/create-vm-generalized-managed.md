---
title: Skapa virtuell dator från en hanterad avbildning i Azure
description: Skapa en virtuell Windows-dator från en generaliserad hanterad avbildning med Azure PowerShell eller Azure-portalen i Resurshanterarens distributionsmodell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: de59edc2e2c702993efd6187a590264d9aac16a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74841939"
---
# <a name="create-a-vm-from-a-managed-image"></a>Skapa en virtuell dator från en hanterad avbildning

Du kan skapa flera virtuella datorer (VMs) från en Azure-hanterad VM-avbildning med Azure-portalen eller PowerShell. En hanterad VM-avbildning innehåller den information som krävs för att skapa en virtuell dator, inklusive operativsystem och datadiskar. De virtuella hårddiskar (VHDs) som utgör avbildningen, inklusive både OS-diskar och eventuella datadiskar, lagras som hanterade diskar. 

Innan du skapar en ny virtuell dator måste du [skapa en hanterad VM-avbildning](capture-image-resource.md) som ska användas som källavbildning och ge läsbehörighet på avbildningen till alla användare som ska ha åtkomst till avbildningen. 


## <a name="use-the-portal"></a>Använda portalen

1. Gå till [Azure-portalen](https://portal.azure.com) för att hitta en hanterad avbildning. Sök efter och välj **Bilder**.
3. Markera den bild som du vill använda i listan. Sidan Översikt **för** bilden öppnas.
4. Välj **Skapa virtuell dator** på menyn.
5. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet som anges här kommer att användas för att logga in på den virtuella datorn. När du är klar väljer du **OK**. Du kan skapa den nya virtuella datorn i en befintlig resursgrupp eller välja **Skapa ny** för att skapa en ny resursgrupp för att lagra den virtuella datorn.
6. Välj en storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. 
7. Under **Inställningar**gör du nödvändiga ändringar och väljer **OK**. 
8. På sammanfattningssidan bör du se bildnamnet som en **privat bild**. Välj **Ok** om du vill starta distributionen av den virtuella datorn.


## <a name="use-powershell"></a>Använd PowerShell

Du kan använda PowerShell för att skapa en virtuell dator från en avbildning med hjälp av den förenklade parameteruppsättningen för [cmdleten New-AzVm.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Avbildningen måste finnas i samma resursgrupp där du ska skapa den virtuella datorn.

 

Den förenklade parameteruppsättningen för [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) kräver bara att du anger ett namn, en resursgrupp och ett avbildningsnamn för att skapa en virtuell dator från en avbildning. New-AzVm kommer att använda värdet för **parametern -Name** som namnet på alla resurser som skapas automatiskt. I det här exemplet ger vi mer detaljerade namn för var och en av resurserna, men låt cmdleten skapa dem automatiskt. Du kan också skapa resurser i förväg, till exempel det virtuella nätverket, och skicka resursnamnet till cmdleten. New-AzVm kommer att använda de befintliga resurserna om den kan hitta dem vid deras namn.

I följande exempel skapas en virtuell dator med namnet *myVMFromImage*, i resursgruppen *myResourceGroup,* från avbildningen *myImage*. 


```azurepowershell-interactive
New-AzVm `
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
[Skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

