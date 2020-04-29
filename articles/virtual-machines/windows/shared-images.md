---
title: Skapa delade VM-avbildningar med Azure PowerShell
description: Lär dig hur du använder Azure PowerShell för att skapa en delad avbildning av virtuella datorer i Azure
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.openlocfilehash: 9da3a531766e74398f4b54f1c777f9a5c65892e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100693"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Skapa ett galleri för delad avbildning med Azure PowerShell 

Ett [delat bild galleri](shared-image-galleries.md) fören klar en anpassad bild delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för att starta distributions åtgärder som till exempel för att läsa in program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner, inom en AAD-klient. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar logiskt. 

Galleriet är en resurs på den översta nivån som ger fullständig rollbaserad åtkomst kontroll (RBAC). Avbildningar kan vara versioner och du kan välja att replikera varje avbildnings version till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder.

Funktionen för delad bild galleri har flera resurs typer. Vi kommer att använda eller skapa dessa i den här artikeln:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad avbildning** | Detta är en grundläggande bild som kan användas separat eller användas för att skapa en **avbildnings version** i ett bild galleri. Hanterade avbildningar skapas från generaliserade virtuella datorer. En hanterad avbildning är en särskild typ av virtuell hård disk som kan användas för att skapa flera virtuella datorer och kan nu användas för att skapa delade avbildnings versioner. |
| **Bild galleri** | Precis som Azure Marketplace är ett **avbildnings Galleri** en lagrings plats för att hantera och dela bilder, men du styr vem som har åtkomst. |
| **Bild definition** | Avbildningar definieras i ett galleri och bär information om avbildningen och kraven för att använda den internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och minimi-och högsta minnes krav. Det är en definition av en typ av bild. |
| **Avbildnings version** | En **avbildnings version** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Som en hanterad avbildning används avbildnings versionen för att skapa nya diskar för den virtuella datorn när du använder en **avbildnings version** för att skapa en virtuell dator. Avbildnings versioner kan användas flera gånger. |

För varje 20 virtuella datorer som du skapar samtidigt rekommenderar vi att du behåller en replik. Om du till exempel skapar 120 virtuella datorer samtidigt med samma avbildning i en region, rekommenderar vi att du behåller minst 6 repliker av avbildningen. Mer information finns i [skalning](/azure/virtual-machines/windows/shared-image-galleries#scaling).

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en befintlig hanterad avbildning för att kunna slutföra exemplet i den här artikeln. Du kan följa [Självstudier: skapa en anpassad avbildning av en virtuell Azure-dator med Azure PowerShell](tutorial-custom-images.md) för att skapa en om det behövs. Om den hanterade avbildningen innehåller en datadisk får data disk storleken inte vara större än 1 TB.

När du arbetar med den här artikeln ersätter du resurs gruppen och VM-namnen där det behövs.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Skapa virtuella datorer från en avbildning

När avbildnings versionen är klar kan du skapa en eller flera nya virtuella datorer. Använda cmdleten [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) . 

I det här exemplet skapas en virtuell dator med namnet *myVMfromImage*i *myResourceGroup* i *USA, södra centrala* data centret.


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
[Azure Image Builder (för hands version)](image-builder-overview.md) kan hjälpa dig att automatisera avbildnings versionen, du kan även använda den för att uppdatera och [skapa en ny avbildnings version från en befintlig avbildnings version](image-builder-gallery-update-image-version.md). 

Du kan också skapa en delad resurs för avbildnings galleriet med hjälp av mallar. Det finns flera tillgängliga Azure snabb starts mallar: 

- [Skapa ett galleri för delad avbildning](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildnings definition i ett galleri för delade avbildningar](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildnings version i ett galleri för delad avbildning](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildnings version](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade avbildnings gallerier finns i [översikten](shared-image-galleries.md). Om du stöter på problem, se [Felsöka delade avbildnings gallerier](troubleshooting-shared-images.md).

