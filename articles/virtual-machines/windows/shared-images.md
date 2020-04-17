---
title: Skapa delade VM-avbildningar med Azure PowerShell
description: Lär dig hur du använder Azure PowerShell för att skapa en avbildning med delad virtuell dator i Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: cfaf545ad2388688666a04076559ff82b7a5d120
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458070"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Skapa ett delat avbildningsgalleri med Azure PowerShell 

Ett [delat bildgalleri](shared-image-galleries.md) förenklar anpassad bilddelning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för att bootstrap distributionsuppgifter som förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med det delade bildgalleriet kan du dela dina anpassade VM-avbildningar med andra i organisationen, inom eller mellan regioner, inom en AAD-klientorganisation. Välj vilka bilder du vill dela, vilka regioner du vill göra dem tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du logiskt kan gruppera delade bilder. 

Galleriet är en resurs på den högsta nivån som ger fullständig rollbaserad åtkomstkontroll (RBAC). Avbildningar kan versionsas och du kan välja att replikera varje avbildningsversion till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder.

Funktionen Delat bildgalleri har flera resurstyper. Vi kommer att använda eller bygga dessa i den här artikeln:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad bild** | Det här är en grundläggande bild som kan användas ensamt eller användas för att skapa en **bildversion** i ett bildgalleri. Hanterade avbildningar skapas från generaliserade virtuella datorer. En hanterad avbildning är en speciell typ av virtuell hårddisk som kan användas för att skapa flera virtuella datorer och som nu kan användas för att skapa delade avbildningsversioner. |
| **Bildgalleri** | Precis som Azure Marketplace är ett **avbildningsgalleri** en databas för hantering och delning av avbildningar, men du styr vem som har åtkomst. |
| **Bilddefinition** | Bilder definieras i ett galleri och innehåller information om bilden och krav för att använda den internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och lägsta och högsta minneskrav. Det är en definition av en typ av bild. |
| **Bildversion** | En **bildversion** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Precis som en hanterad avbildning används avbildningsversionen när du använder en **avbildningsversion** för att skapa en virtuell dator för att skapa nya diskar för den virtuella datorn. Bildversioner kan användas flera gånger. |

För varje 20 virtuella datorer som du skapar samtidigt rekommenderar vi att du behåller en replik. Om du till exempel skapar 120 virtuella datorer samtidigt med samma bild i en region föreslår vi att du behåller minst 6 repliker av bilden. Mer information finns i [Skala](/azure/virtual-machines/windows/shared-image-galleries#scaling).

## <a name="before-you-begin"></a>Innan du börjar

Om du vill slutföra exemplet i den här artikeln måste du ha en befintlig hanterad avbildning. Du kan följa [självstudiekurs: Skapa en anpassad avbildning av en Virtuell Azure-dator med Azure PowerShell](tutorial-custom-images.md) för att skapa en om det behövs. Om den hanterade avbildningen innehåller en datadisk kan datadiskstorleken inte vara mer än 1 TB.

När du arbetar med den här artikeln ersätter du resursgruppen och VM-namnen där det behövs.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Skapa virtuella datorer från en bild

När avbildningsversionen är klar kan du skapa en eller flera nya virtuella datorer. Använda [Cmdlet new-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 

I det här exemplet skapas en virtuell dator med namnet *myVMfromImage*i *myResourceGroup* i det *södra centrala amerikanska* datacentret.


```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Nästa steg
[Azure Image Builder (förhandsgranskning)](image-builder-overview.md) kan hjälpa till att automatisera skapandet av avbildningsversion, du kan till och med använda den för att uppdatera och [skapa en ny avbildningsversion från en befintlig avbildningsversion](image-builder-gallery-update-image-version.md). 

Du kan också skapa resurs för delat bildgalleri med hjälp av mallar. Det finns flera Azure Quickstart-mallar tillgängliga: 

- [Skapa ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en bilddefinition i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en bildversion i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade bildgallerier finns i [översikten](shared-image-galleries.md). Om du stöter på problem läser du [Felsöka delade bildgallerier](troubleshooting-shared-images.md).

