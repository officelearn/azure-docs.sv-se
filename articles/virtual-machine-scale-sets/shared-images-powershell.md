---
title: Använd delade VM-avbildningar för att skapa en skalningsuppsättning i Azure | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell för att skapa delade VM-avbildning som ska användas för att distribuera VM-skalningsuppsättningar i Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 055242c3118ce9d972d55cdc6a21bf623679a0c1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242053"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Skapa och använda delade avbildningar för VM-skalningsuppsättningar med Azure PowerShell

När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. Tjänsten delad bildgalleriet förenklar anpassad avbildning delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Delad galleriet kan du dela din anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner inom en AAD-klient. Välj vilka bilder som du vill dela, vilka regioner som du vill göra dem tillgängliga i och som du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar. 

Galleriet är en resurs på toppnivå som ger fullständig rollbaserad åtkomstkontroll (RBAC). Bilder kan versionshanteras och du kan välja att replikera varje versionsnumret för avbildningen till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade avbildningar. 

Delad bildgalleriet-funktionen har flera resurstyper. Vi ska använda eller att skapa dessa i den här artikeln:

| Resource | Beskrivning|
|----------|------------|
| **Hanterad avbildning** | Det här är en grundläggande avbildning som kan användas fristående eller används för att skapa en **Avbildningsversion** i ett galleri med avbildningar. Hanterade avbildningarna skapas från generaliserad virtuella datorer. En hanterad avbildning är en särskild typ av virtuell Hårddisk som kan användas för att göra flera virtuella datorer och kan nu användas för att skapa delade bild versioner. |
| **Bildgalleri** | Som Azure Marketplace, en **bildgalleriet** är en lagringsplats för att hantera och dela bilder, men du bestämmer vem som har åtkomst. |
| **Avbildningsdefinitionen** | Bilder har definierats i ett galleri och utföra information om avbildningen och krav för att använda det internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och lägsta och högsta minneskrav. Det är en definition av en typ av bild. |
| **Avbildningsversion** | En **Avbildningsversion** är det du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning som behövs för din miljö. Som en hanterad avbildning när du använder en **Avbildningsversion** för att skapa en virtuell dator, versionsnumret för avbildningen som används för att skapa nya diskar för den virtuella datorn. Bild-versioner kan användas flera gånger. |

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan visar hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan skapa nya VM-instanser med.

Du måste ha en befintlig hanterad avbildning för att utföra exemplet i den här artikeln. Du kan följa [självstudien: Skapa och använda en anpassad avbildning för VM-skalningsuppsättningar med Azure PowerShell](tutorial-use-custom-image-powershell.md) att skapa en om det behövs. Om hanterad avbildning innehåller en datadisk, får inte datadiskstorleken vara mer än 1 TB.

När du börjar med artikeln, Ersätt namnges resursgrupp och virtuell dator där det behövs.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Skapa en skalningsuppsättning från den delade avbildningsversionen

Skapa en VM-skalningsuppsättning med [New-AzVmss](/powershell/module/az.compute/new-azvmss). I följande exempel skapas en skalningsuppsättning från den nya avbildningsversionen i den *södra centrala USA* datacenter. När du uppmanas, ange din egen administratörsautentiseringsuppgifter för VM-instanser i skalningsuppsättningen:


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
  -Id $imageVersion.Id

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

Du kan också skapa delade bildgalleriet resursen med hjälp av mallar. Det finns flera Azure-Snabbstartsmallar: 

- [Skapa en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en Definition för bilden i en delad Avbildningsgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en Bildversion i en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från Avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade Image Galleries finns i den [översikt](shared-image-galleries.md). Om du stöter på problem, se [felsökning delade bildgallerier](troubleshooting-shared-images.md).
