---
title: Skapa anpassade VM-avbildningar med Azure PowerShell | Microsoft Docs
description: "Självstudiekurs – skapa en anpassad VM-avbildning med hjälp av Azure PowerShell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/07/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7001e5df235d65c531b9102f879bde9693c4f853
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av PowerShell

Anpassade avbildningar liknar marketplace-bilder, men du skapa dem själv. Anpassade avbildningar kan användas för bootstrap konfigurationer, till exempel förinstalleras program, Programinställningar och andra OS-konfigurationer. I den här självstudiekursen skapar du en egen anpassad avbildning av en virtuell Azure-dator. Lär dig att:

> [!div class="checklist"]
> * Sysprep och generalisera virtuella datorer
> * Skapa en egen avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Alla avbildningar i din prenumeration
> * Ta bort en bild

Den här självstudien kräver Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan innehåller information om hur du tar en befintlig virtuell dator och omvandla det till en återanvändbara anpassad avbildning som du kan använda för att skapa nya VM-instanser.

Du måste ha en befintlig virtuell dator för att slutföra exemplet i den här självstudiekursen. Om det behövs, detta [skriptexempel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) kan skapa en åt dig. När gå igenom kursen, ersätter namn resursgrupp och VM där det behövs.

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

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Ange status för den virtuella datorn till `-Generalized` med [Set AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Skapa avbildningen

Nu kan du skapa en avbildning av den virtuella datorn med hjälp av [ny AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) och [ny AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). I följande exempel skapas en bild med namnet *myImage* från en virtuell dator med namnet *myVM*.

Hämta den virtuella datorn. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Skapa image-konfigurationen.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Skapa avbildningen.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Skapa virtuella datorer från avbildningen

Nu när du har skapat en avbildning kan du skapa en eller flera nya virtuella datorer från avbildningen. Skapa en virtuell dator från en anpassad avbildning påminner mycket om att skapa en virtuell dator med hjälp av en Marketplace-avbildning. När du använder en Marketplace-avbildning, måste du ange information om avbildning, image-providern, erbjudande, SKU och version. Med en anpassad avbildning behöver du bara ange ID för den anpassade bild resursen. 

I skriptet nedan skapar vi en variabel *$image* att lagra information om en anpassad avbildning med hjälp av [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) och sedan använder vi [Set AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) och ange ID med hjälp av den *$image* variabeln vi just skapade. 

Skriptet skapar en virtuell dator med namnet *myVMfromImage* från våra anpassad avbildning i en ny resursgrupp med namnet *myResourceGroupFromImage* i den *västra USA* plats.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="image-management"></a>Avbildningshantering 

Här följer några exempel på vanliga hanteringsuppgifter för avbildning och hur du utför dem med hjälp av PowerShell.

Visa alla avbildningar efter namn.

```powershell
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Ta bort en bild. Det här exemplet tar bort det bild med namnet *myOldImage* från den *myResourceGroup*.

```powershell
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I kursen får skapat du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Sysprep och generalisera virtuella datorer
> * Skapa en egen avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Alla avbildningar i din prenumeration
> * Ta bort en bild

Gå vidare till nästa kurs att lära dig hur högtillgängliga virtuella datorer.

> [!div class="nextstepaction"]
> [Skapa virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md)



