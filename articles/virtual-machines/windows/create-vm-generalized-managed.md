---
title: Skapa en virtuell dator från en hanterad avbildning i Azure
description: Skapa en virtuell Windows-dator från en generaliserad hanterad avbildning med Azure PowerShell eller portalen.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: b0c6689b66037067a4c5174738945b7c6fabd5b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086325"
---
# <a name="create-a-vm-from-a-managed-image"></a>Skapa en virtuell dator från en hanterad avbildning

Du kan skapa flera virtuella datorer från en Azure-hanterad VM-avbildning med hjälp av Azure Portal eller PowerShell. En hanterad virtuell dator avbildning innehåller den information som krävs för att skapa en virtuell dator, inklusive operativ system och data diskar. De virtuella hård diskar (VHD: er) som utgör avbildningen, inklusive både OS-diskar och data diskar, lagras som hanterade diskar. 

Innan du skapar en ny virtuell dator måste du [skapa en hanterad virtuell dator avbildning](capture-image-resource.md) som ska användas som käll avbildning och ge Läs behörighet på avbildningen till alla användare som ska ha åtkomst till avbildningen. 


## <a name="use-the-portal"></a>Använda portalen

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta en hanterad avbildning. Sök efter och välj **bilder**.
3. Välj den avbildning som du vill använda från listan. Sidan **Översikt över** avbildning öppnas.
4. Välj **Skapa virtuell dator** på menyn.
5. Ange informationen för den virtuella datorn. Användar namnet och lösen ordet som anges här kommer att användas för att logga in på den virtuella datorn. När du är klar väljer du **OK**. Du kan skapa den nya virtuella datorn i en befintlig resurs grupp eller välja **Skapa ny** för att skapa en ny resurs grupp för att lagra den virtuella datorn.
6. Välj en storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. 
7. Under **Inställningar**, gör du nödvändiga ändringar och väljer **OK**. 
8. På sidan Sammanfattning bör du se avbildnings namnet som en **privat bild**. Välj **OK** för att starta distributionen av virtuella datorer.


## <a name="use-powershell"></a>Använd PowerShell

Du kan använda PowerShell för att skapa en virtuell dator från en avbildning med hjälp av den förenklade parameter uppsättningen för cmdleten [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) . Avbildningen måste finnas i samma resurs grupp där du skapar den virtuella datorn.

 

Den förenklade parametern som angetts för [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) kräver bara att du anger ett namn, en resurs grupp och ett avbildnings namn för att skapa en virtuell dator från en avbildning. New-AzVm använder värdet för parametern **-Name** som namnet på alla resurser som skapas automatiskt. I det här exemplet ger vi mer detaljerade namn för var och en av resurserna, men låt cmdleten skapa dem automatiskt. Du kan också skapa resurser i förväg, till exempel det virtuella nätverket och skicka resurs namnet till cmdleten. New-AzVm kommer att använda de befintliga resurserna om de kan hitta dem med deras namn.

I följande exempel skapas en virtuell dator med namnet *myVMFromImage*i resurs gruppen *myResourceGroup* från avbildningen med namnet *image*. 


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
[Skapa och hantera virtuella Windows-datorer med modulen Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

