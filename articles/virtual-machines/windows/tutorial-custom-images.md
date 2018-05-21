---
title: Självstudier – Skapa anpassade VM-avbildningar med Azure PowerShell | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du skapar en anpassad avbildning av en virtuell dator i Azure med hjälp av Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a449c1f9781ffc86de4786eaab3cb83999b86a72
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Självstudier: Skapa en anpassad avbildning av en virtuell dator i Azure med hjälp av Azure PowerShell

Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. I den här självstudien skapar du en egen anpassad avbildning av en virtuell Azure-dator. Lär dig att:

> [!div class="checklist"]
> * Förbereda systemet med Sysprep och generalisera virtuella datorer
> * Skapa en anpassad avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Göra en lista med alla avbildningar i din prenumeration
> * Ta bort en avbildning

## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan visar hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan använda för att skapa nya VM-instanser.

Du måste ha en befintlig virtuell dator för att kunna utföra exemplet i självstudien. Om det behövs kan du skapa en med detta [skriptexempel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md). När du använder självstudien ersätter du namn på resursgrupp och VM där det behövs.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt krävs AzureRM-modulen version 5.7.0 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="prepare-vm"></a>Förbereda den virtuella datorn

För att skapa en avbildning av en virtuell dator måste du förbereda den genom att generalisera den virtuella datorn, frigöra och sedan markera den virtuella källdatorn som generaliserad i Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalisera den virtuella Windows-datorn med hjälp av Sysprep

Sysprep tar bland annat bort all din personliga kontoinformation och förbereder datorn så att den kan användas som en avbildning. Mer information om Sysprep finns i [How to Use Sysprep: An Introduction](http://technet.microsoft.com/library/bb457073.aspx) (Använda Sysprep: En introduktion).


1. Ansluta till den virtuella datorn.
2. Öppna Kommandotolken som administratör. Ändra katalogen till *%windir%\system32\sysprep* och kör sedan *sysprep.exe*.
3. Välj *Starta OOBE för systemet (Out-of-Box Experience)* i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan *Generalisera* är markerad.
4. Välj *Avstängning* i **Avstängningsalternativ** och klicka på **OK**.
5. När Sysprep har slutförts stängs den virtuella datorn av. **Starta inte om den virtuella datorn**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Frigöra och markera den virtuella datorn som generaliserad

När du skapar en avbildning måste den virtuella datorn frigöras och markeras som generaliserad i Azure.

Frigör den virtuella datorn med hjälp av [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Ange statusen för den virtuella datorn till `-Generalized` med hjälp av [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Skapa avbildningen

Nu kan du skapa en avbildning av den virtuella datorn med hjälp av [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) och [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). I följande exempel skapas en avbildning med namnet *myImage* från en virtuell dator med namnet *myVM*.

Hämta den virtuella datorn. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Skapa avbildningskonfigurationen.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Skapa avbildningen.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Skapa virtuella datorer från avbildningen

Nu när du har en avbildning kan du skapa en eller flera nya virtuella datorer från avbildningen. Att skapa en virtuell dator från en anpassad avbildning påminner om att skapa en virtuell dator med hjälp av en Marketplace-avbildning. När du använder en Marketplace-avbildning måste du ange information om avbildningen, avbildningsprovidern, produkten, SKU och version. Med den förenklade parameteruppsättningen för cmdleten [New-AzureRMVM]() behöver du bara ange namnet på den anpassade avbildningen, förutsatt att den finns i samma resursgrupp. 

I det här exemplet skapas den virtuella datorn *myVMfromImage* från *myImage* i *myResourceGroup*.


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

Här följer några exempel på vanliga hanteringsuppgifter för avbildningar, samt anvisningar som beskriver hur du utför dem med hjälp av PowerShell.

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
> * Förbereda systemet med Sysprep och generalisera virtuella datorer
> * Skapa en anpassad avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Göra en lista med alla avbildningar i din prenumeration
> * Ta bort en avbildning

Gå vidare till nästa självstudie om du vill lära dig mer om virtuella datorer med hög tillgänglighet.

> [!div class="nextstepaction"]
> [Skapa virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md)



