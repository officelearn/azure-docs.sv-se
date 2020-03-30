---
title: Använda delade VM-avbildningar för att skapa en skalningsuppsättning i Azure
description: Lär dig hur du använder Azure PowerShell för att skapa delade VM-avbildningar som ska användas för distribution av skalningsuppsättningar för virtuella datorer i Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 5f4eca88614a98f0caf87d04847029328042edd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368729"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Skapa och använda delade avbildningar för skalningsuppsättningar för virtuella datorer med Azure PowerShell

När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. Tjänsten Delat bildgalleri förenklar i hög grad anpassad bilddelning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med det delade bildgalleriet kan du dela dina anpassade VM-avbildningar med andra i organisationen, inom eller mellan regioner, inom en AAD-klientorganisation. Välj vilka bilder du vill dela, vilka regioner du vill göra dem tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du logiskt kan gruppera delade bilder. 

Galleriet är en resurs på den högsta nivån som ger fullständig rollbaserad åtkomstkontroll (RBAC). Avbildningar kan versionsas och du kan välja att replikera varje avbildningsversion till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder. 

Funktionen Delat bildgalleri har flera resurstyper. Vi kommer att använda eller bygga dessa i den här artikeln:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad bild** | Det här är en grundläggande bild som kan användas ensamt eller användas för att skapa en **bildversion** i ett bildgalleri. Hanterade avbildningar skapas från generaliserade virtuella datorer. En hanterad avbildning är en speciell typ av virtuell hårddisk som kan användas för att skapa flera virtuella datorer och som nu kan användas för att skapa delade avbildningsversioner. |
| **Bildgalleri** | Precis som Azure Marketplace är ett **avbildningsgalleri** en databas för hantering och delning av avbildningar, men du styr vem som har åtkomst. |
| **Bilddefinition** | Bilder definieras i ett galleri och innehåller information om bilden och krav för att använda den internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och lägsta och högsta minneskrav. Det är en definition av en typ av bild. |
| **Bildversion** | En **bildversion** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Precis som en hanterad avbildning används avbildningsversionen när du använder en **avbildningsversion** för att skapa en virtuell dator för att skapa nya diskar för den virtuella datorn. Bildversioner kan användas flera gånger. |

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan visar hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan skapa nya VM-instanser med.

Om du vill slutföra exemplet i den här artikeln måste du ha en befintlig hanterad avbildning. Du kan följa [självstudiekurs: Skapa och använda en anpassad avbildning för skalbara uppsättningar med virtuella datorer med Azure PowerShell](tutorial-use-custom-image-powershell.md) för att skapa en om det behövs. Om den hanterade avbildningen innehåller en datadisk kan datadiskstorleken inte vara mer än 1 TB.

När du arbetar igenom artikeln ersätter du resursgruppen och VM-namnen där det behövs.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Skapa en skalningsuppsättning från den delade bildversionen

Skapa en VM-skalningsuppsättning med [New-AzVmss](/powershell/module/az.compute/new-azvmss). I följande exempel skapas en skalningsuppsättning från den nya avbildningsversionen i *det södra centrala amerikanska* datacentret. När du uppmanas till det anger du egna administratörsbehörighet för VM-instanserna i skalningsuppsättningen:


```azurepowershell-interactive
# Define variables
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"
$cred = Get-Credential

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a netowrking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageVersion.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]


## <a name="next-steps"></a>Nästa steg

Du kan också skapa resurs för delat bildgalleri med hjälp av mallar. Det finns flera Azure Quickstart-mallar tillgängliga: 

- [Skapa ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en bilddefinition i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en bildversion i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade bildgallerier finns i [översikten](shared-image-galleries.md). Om du stöter på problem läser du [Felsöka delade bildgallerier](troubleshooting-shared-images.md).
