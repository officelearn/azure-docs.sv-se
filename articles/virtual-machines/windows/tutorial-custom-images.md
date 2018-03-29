---
title: Skapa anpassade VM-avbildningar med Azure PowerShell | Microsoft Docs
description: Självstudiekurs – skapa en anpassad VM-avbildning med hjälp av Azure PowerShell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 443f47b98ea063c6fe1f0b3517c00b6cf3692161
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av PowerShell

Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. I den här självstudien skapar du en egen anpassad avbildning av en virtuell Azure-dator. Lär dig att:

> [!div class="checklist"]
> * Sysprep och generalisera virtuella datorer
> * Skapa en anpassad avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Göra en lista med alla avbildningar i din prenumeration
> * Ta bort en avbildning


## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan visar hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan använda för att skapa nya VM-instanser.

Du måste ha en befintlig virtuell dator för att kunna utföra exemplet i självstudien. Om det behövs kan du skapa en med detta [skriptexempel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md). När du använder självstudien ersätter du namn på resursgrupp och VM där det behövs.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kursen krävs AzureRM Modulversion 5.6.0 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="prepare-vm"></a>Förbereda VM

Om du vill skapa en avbildning av en virtuell dator måste du förbereda den virtuella datorn genom att generalisera den virtuella datorn, det har frigjorts och markera källan VM som generaliserad i Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalisera Windows VM med hjälp av Sysprep

Sysprep tar bort alla dina personlig information, bland annat och förbereder datorn som ska användas som en bild. Mer information om Sysprep finns [så att använda Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx).


1. Ansluta till den virtuella datorn.
2. Öppna Kommandotolken som administratör. Ändra katalogen till *%windir%\system32\sysprep*, och kör sedan *sysprep.exe*.
3. I den **systemförberedelseverktyget** dialogrutan *ange System Out of Box Experience (OOBE)*, och se till att den *Generalize* är markerad.
4. I **avstängningsalternativ**väljer *avstängning* och klicka sedan på **OK**.
5. När Sysprep har slutförts stängs den virtuella datorn. **Starta inte om den virtuella datorn**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Frigöra och markera den virtuella datorn som generaliserad

Om du vill skapa en avbildning måste den virtuella datorn frigjorts och har markerats som generaliserad i Azure.

Frigöra den virtuella datorn med hjälp av [stoppa AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Ange status för den virtuella datorn till `-Generalized` med [Set AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Skapa avbildningen

Nu kan du skapa en avbildning av den virtuella datorn med hjälp av [ny AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) och [ny AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). I följande exempel skapas en avbildning med namnet *myImage* från en virtuell dator med namnet *myVM*.

Hämta den virtuella datorn. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Skapa image-konfigurationen.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Skapa avbildningen.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Skapa virtuella datorer från avbildningen

Nu när du har skapat en avbildning kan du skapa en eller flera nya virtuella datorer från avbildningen. Skapa en virtuell dator från en anpassad avbildning påminner mycket om att skapa en virtuell dator med hjälp av en Marketplace-avbildning. När du använder en Marketplace-avbildning, måste du ange information om avbildning, image-providern, erbjudande, SKU och version. Med parametern förenklad anges för den [ny AzureRMVM]() cmdlet, behöver du bara ange namnet på den anpassade avbildningen så länge som den är i samma resursgrupp. 

Det här exemplet skapar en virtuell dator med namnet *myVMfromImage* från den *myImage*i den *myResourceGroup*.


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

## <a name="image-management"></a>Avbildningshantering 

Här följer några exempel på vanliga hanteringsuppgifter för avbildning och hur du utför dem med hjälp av PowerShell.

Visa alla avbildningar efter namn.

```azurepowershell-interactive
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Ta bort en avbildning. I det här exemplet tar vi bort avbildningen med namnet *myOldImage* från *myResourceGroup*.

```azurepowershell-interactive
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I självstudien skapade du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Sysprep och generalisera virtuella datorer
> * Skapa en anpassad avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Göra en lista med alla avbildningar i din prenumeration
> * Ta bort en avbildning

Gå vidare till nästa kurs att lära dig hur högtillgängliga virtuella datorer.

> [!div class="nextstepaction"]
> [Skapa virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md)



