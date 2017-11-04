---
title: "Skapa en skaluppsättning för virtuell dator i Azure | Microsoft Docs"
description: Skapa och distribuera en Linux- eller Windows Azure virtuella skala med Azure CLI, PowerShell, en mall eller Visual Studio.
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.openlocfilehash: 32af01aa545c541688128a7ae6bbb82a0e046f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>Skapa och distribuera en skaluppsättning för virtuell dator
Skaluppsättningar för den virtuella datorn gör det enkelt att distribuera och hantera identiska virtuella datorer som en uppsättning. Skaluppsättningar ger en skalbar och anpassningsbara beräkningslager för storskaliga program och stöd för avbildningar för Windows-plattformen, Linux-plattformen bilder, anpassade avbildningar och tillägg. Läs mer om skaluppsättningar [skalningsuppsättningar i virtuella](virtual-machine-scale-sets-overview.md).

Den här kursen visar hur du skapar en virtuella datorns skaluppsättning **utan** med Azure-portalen. Information om hur du använder Azure portal finns [hur du skapar en virtuell dator skala anges med Azure-portalen](virtual-machine-scale-sets-portal-create.md).

>[!NOTE]
>Mer information om Azure Resource Manager-resurser finns [Azure Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du använder Azure CLI 2.0 eller Azure PowerShell för att skapa en skaluppsättning för, måste du först logga in till din prenumeration.

Mer information om hur du installerar, konfigurerar och logga in på Azure med Azure CLI eller PowerShell finns [komma igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) eller [Kom igång med Azure PowerShell-cmdlets](/powershell/azure/overview).

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du måste först skapa en resursgrupp som virtuella datorns skaluppsättning är kopplad till.

```azurecli
az group create --location westus2 --name MyResourceGroup1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name MyResourceGroup1
```

## <a name="create-from-azure-cli"></a>Skapa från Azure CLI

Med Azure CLI, kan du skapa en virtuell dator-skala med minsta möjliga ansträngning. Om du utelämnar standardvärden tillhandahålls de åt dig. Till exempel om du inte anger någon information om virtuellt nätverk skapas ett virtuellt nätverk för dig. Om du utelämnar följande delar skapas de automatiskt: 
- Belastningsutjämning
- Ett virtuellt nätverk
- En offentlig IP-adress

När du väljer den avbildning av virtuell dator som du vill använda på virtuella datorns skaluppsättning finns några alternativ:

- URN  
En resurs-ID:  
**Win2012R2Datacenter**

- URN alias  
Det egna namnet för en URN:  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**

- Anpassad resurs-id  
Sökväg till en Azure-resurs:  
**/subscriptions/Subscription-GUID/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**

- Webbresurs  
Sökväg till en HTTP-URI:  
**http://contoso.BLOB.Core.Windows.NET/vhds/osdiskimage.VHD**

>[!TIP]
>Du kan hämta en lista över tillgängliga avbildningar med `az vm image list`.

Om du vill skapa en skaluppsättning för virtuell dator, måste du ange följande:

- Resursgrupp 
- Namn
- Avbildning av operativsystemet
- Autentiseringsinformation 
 
I följande exempel skapar en grundläggande virtuella skaluppsättning (det här steget kan ta några minuter).

```azurecli
az vmss create --resource-group MyResourceGroup1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

När kommandot har slutförts har du nu ange skapade virtuella nivå. Du kan behöva hämta IP-adressen för den virtuella datorn så att du kan ansluta till den. Du kan hämta en mängd olika typer av information om den virtuella datorn (inklusive IP-adress) med följande kommando. 

```azurecli
az vmss list-instance-connection-info --resource-group MyResourceGroup1 --name MyScaleSet
```

## <a name="create-from-powershell"></a>Skapa från PowerShell

PowerShell är mer komplicerad att använda än Azure CLI. Medan Azure CLI tillhandahåller standardvärden för nätverksrelaterade resurser (till exempel belastningsutjämnare, IP-adresser och virtuella nätverk), PowerShell inte. Refererar till en bild med PowerShell är en något mer komplicerad för. Du kan hämta bilder med följande cmdlets:

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

Cmdlets arbetet kan skickas i rätt ordning. Här är ett exempel på hur du hämtar alla avbildningar för den **västra USA 2** region med en utgivare med namnet **microsoft** i den.

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

Arbetsflöde för att skapa en skaluppsättning för virtuell dator är följande:

1. Skapa ett konfigurationsobjekt som innehåller information om skaluppsättning.
2. Referera till den grundläggande OS-avbildningen.
3. Konfigurera inställningar för operativsystem: autentisering, VM-namnprefix och användaren/pass.
4. Konfigurera nätverk.
5. Skapa skaluppsättning.

Det här exemplet skapar en grundläggande två instans skala för en dator som har Windows Server 2016 installerad.

```powershell
# Resource group name from above
$rg = "MyResourceGroup1"
$location = "WestUS2"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location $location -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources

## Basics
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName $rg -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $subnet

## Load balancer
$publicIP = New-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName $rg -Location $location -AllocationMethod Static -DomainNameLabel "myuniquedomain"
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontend" -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
$probe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
$inboundNATRule1= New-AzureRmLoadBalancerRuleConfig -Name "webserver" -FrontendIpConfiguration $frontendIP -Protocol Tcp -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -Probe $probe -BackendAddressPool $backendPool
$inboundNATPool1 = New-AzureRmLoadBalancerInboundNatPoolConfig -Name "RDP" -FrontendIpConfigurationId $frontendIP.Id -Protocol TCP -FrontendPortRangeStart 53380 -FrontendPortRangeEnd 53390 -BackendPort 3389

New-AzureRmLoadBalancer -ResourceGroupName $rg -Name "LB1" -Location $location -FrontendIpConfiguration $frontendIP -LoadBalancingRule $inboundNATRule1 -InboundNatPool $inboundNATPool1 -BackendAddressPool $backendPool -Probe $probe

## IP address config
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ipaddress" -LoadBalancerBackendAddressPoolsId $backendPool.Id -SubnetId $vnet.Subnets[0].Id -LoadBalancerInboundNatPoolsId $inboundNATPool1.Id

# Attach the virtual network to the IP object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss -ResourceGroupName $rg -Name "MyScaleSet1" -VirtualMachineScaleSet $vmssConfig
```

### <a name="using-a-custom-virtual-machine-image"></a>Med hjälp av en virtuell datoravbildning
Om du skapar en skala från en egen anpassad avbildning i stället för att referera till en avbildning av virtuell dator från galleriet den _Set AzureRmVmssStorageProfile_ kommando skulle se ut så här:
```PowerShell
Set-AzureRmVmssStorageProfile -OsDiskCreateOption FromImage -ManagedDisk PremiumLRS -OsDiskCaching "None" -OsDiskOsType Linux -ImageReferenceId (Get-AzureRmImage -ImageName $VMImage -ResourceGroupName $rg).id
```

## <a name="create-from-a-template"></a>Skapa från en mall

Du kan distribuera en virtuell dator skala in med hjälp av en Azure Resource Manager-mall. Du kan skapa egna mallar eller använda en från den [mall databasen](https://azure.microsoft.com/resources/templates/?term=vmss). De här mallarna kan distribueras direkt till din Azure-prenumeration.

>[!NOTE]
>Om du vill skapa en egen mall kan du skapa en JSON-textfil. Allmän information om hur du skapar och anpassar en mall finns [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

En exempelmall finns [på GitHub](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set). Mer information om hur du skapar och använder det exemplet finns [minsta lönsam skaluppsättning](.\virtual-machine-scale-sets-mvss-start.md).

## <a name="create-from-visual-studio"></a>Skapa från Visual Studio

Du kan skapa ett Azure resursgruppsprojektet och lägga till en virtuell dator skala mall till den i Visual Studio. Du kan välja om du vill importera från GitHub eller Azure Web Application Gallery. En distribution PowerShell-skript skapas också för dig. Mer information finns i [hur du skapar en virtuell dator skala anges med Visual Studio](virtual-machine-scale-sets-vs-create.md).

## <a name="create-from-the-azure-portal"></a>Skapa från Azure-portalen

Azure-portalen är ett bekvämt sätt att snabbt skapa en skaluppsättning. Mer information finns i [hur du skapar en virtuell dator skala anges med Azure-portalen](virtual-machine-scale-sets-portal-create.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [datadiskar](virtual-machine-scale-sets-attached-disks.md).

Lär dig hur du [hanterar dina appar](virtual-machine-scale-sets-deploy-app.md).
