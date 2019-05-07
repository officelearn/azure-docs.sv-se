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
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 57fbab4194f6cd232e1462ecea9a07d104c6cb51
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205739"
---
# <a name="create-a-vm-from-a-managed-image"></a>Skapa en virtuell dator från en hanterad avbildning

Du kan skapa flera virtuella datorer (VM) från en Azure-hanterade VM bild med hjälp av Azure portal eller PowerShell. En hanterad datoravbildning av virtuell innehåller informationen som krävs för att skapa en virtuell dator, inklusive operativsystem och datadiskar. De virtuella hårddiskarna (VHD) som utgör bilden, inklusive både OS-diskar och eventuella datadiskar lagras som hanterade diskar. 

Innan du skapar en ny virtuell dator, måste du [skapa en hanterad virtuell datoravbildning](capture-image-resource.md) ska användas som käll-avbildning och bevilja läsåtkomst till avbildningen till alla användare som ska ha åtkomst till avbildningen. 


## <a name="use-the-portal"></a>Använda portalen

1. Öppna [Azure-portalen](https://portal.azure.com).
2. På menyn till vänster väljer **alla resurser**. Du kan sortera resurser efter **typ** att enkelt hitta dina avbildningar.
3. Välj den avbildning som du vill använda i listan. Avbildningen **översikt** öppnas.
4. Välj **Create VM** på menyn.
5. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. När du är klar väljer **OK**. Du kan skapa den nya virtuella datorn i en befintlig resursgrupp eller välj **Skapa nytt** att skapa en ny resursgrupp för att lagra den virtuella datorn.
6. Välj en storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. 
7. Under **inställningar**, gör nödvändiga ändringar och välj **OK**. 
8. På sidan Sammanfattning du bör se din avbildningsnamn som listas som en **privat avbildning**. Välj **Ok** att starta distributionen av virtuella datorn.


## <a name="use-powershell"></a>Använd PowerShell

Du kan använda PowerShell för att skapa en virtuell dator från en avbildning med hjälp av förenklad parameteruppsättning för den [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet. Avbildningen måste finnas i samma resursgrupp där du skapar den virtuella datorn.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

En förenklad parameteruppsättning för [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) endast kräver att du anger namn, resursgrupp och Avbildningsnamnet för att skapa en virtuell dator från en avbildning. Nya AzVm kommer att använda värdet för den **-namnet** parametern som namnet på alla resurser som skapas automatiskt. I det här exemplet vi ger mer detaljerad namnen för varje resurs men låta cmdleten skapa dem automatiskt. Du kan också skapa resurser i förväg, till exempel det virtuella nätverket, och skicka resursnamnet i cmdleten. Ny AzVm använder befintliga resurser om det hittar dem efter deras namn.

I följande exempel skapas en virtuell dator med namnet *myVMFromImage*i den *myResourceGroup* resursgrupp från avbildningen med namnet *myImage*. 


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

