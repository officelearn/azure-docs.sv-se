---
title: "Skapa en Skalningsuppsättningar i virtuella datorer för Windows i Azure | Microsoft Docs"
description: "Skapa och distribuera ett program som har hög tillgänglighet på virtuella Windows-datorer med hjälp av en skaluppsättning för virtuell dator"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d190d046f7572c51df0c5c9e14e14a41d93e3248
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>Skapa en Virtual Machine Scale Set och distribuera en app som har hög tillgänglighet i Windows
En skaluppsättning för virtuell dator kan du distribuera och hantera en uppsättning identiska, automatisk skalning virtuella datorer. Du kan skala antalet virtuella datorer i skaluppsättning manuellt eller definiera regler för att kunna Autoskala baserat på Resursanvändning t.ex CPU, minne begäran eller nätverkstrafik. I kursen får distribuera du en virtuell dator skala i Azure. Lär dig att:

> [!div class="checklist"]
> * Använda tillägget för anpassat skript för att definiera en IIS-webbplats för att skala
> * Skapa en belastningsutjämnare för din skaluppsättning
> * Skapa en skaluppsättning för virtuell dator
> * Öka eller minska antalet instanser i en skaluppsättning
> * Skapa automatiska regler

Den här kursen kräver Azure PowerShell Modulversion 5.1.1 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).


## <a name="scale-set-overview"></a>Skala Set-översikt
En skaluppsättning för virtuell dator kan du distribuera och hantera en uppsättning identiska, automatisk skalning virtuella datorer. Virtuella datorer i en skaluppsättning är fördelade på logiken fel- och update-domäner i en eller flera *placering grupper*. Dessa är grupper med liknande konfigurerad virtuella datorer, liknar [tillgänglighetsuppsättningar](tutorial-availability-sets.md).

Virtuella datorer skapas efter behov i en skaluppsättning. Du kan definiera automatiska regler för att styra hur och när virtuella datorer läggs till eller tas bort från skaluppsättning. De här reglerna kan utlösa baserat på mått som CPU-belastning, minnesanvändning eller nätverkstrafik.

Skala anger stöd för upp till 1 000 virtuella datorer när du använder en avbildning i Azure-plattformen. För arbetsbelastningar med betydande installation eller VM anpassning krav, kan du [skapa en anpassad VM-avbildning](tutorial-custom-images.md). Du kan skapa upp till 300 virtuella datorer i en skala som anges när du använder en anpassad avbildning.


## <a name="create-an-app-to-scale"></a>Skapa en app att skala
Innan du kan skapa en skalningsuppsättning, skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroupAutomate* i den *EastUS* plats:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupScaleSet -Location EastUS
```

I en tidigare kursen får du lärt dig hur du [automatisera VM-konfiguration](tutorial-automate-vm-deployment.md) med tillägget för anpassat skript. Skapa en scale set-konfiguration och sedan tillämpa en tillägget för anpassat skript för att installera och konfigurera IIS:

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location EastUS `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings
```

## <a name="create-scale-load-balancer"></a>Skapa skala belastningsutjämnare
En Azure belastningsutjämnare är en belastningsutjämnare för Layer-4 (TCP, UDP) som ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfri virtuella datorer. En belastningsutjämnaren, hälsoavsökningen övervakar en viss port på varje virtuell dator och distribuerar endast trafik till en virtuell dator i drift. Mer information finns i nästa kurs på [hur du läser in balansera virtuella Windows-datorer](tutorial-load-balancer.md).

Skapa en belastningsutjämnare som har en offentlig IP-adress och distribuerar Internet-trafik på port 80:

```powershell
# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupScaleSet `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="create-a-scale-set"></a>Skapa en skaluppsättning
Nu skapa en virtuell dator-skala med [ny AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en uppsättning med namnet skala *myScaleSet*:

```powershell
# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword P@ssword! `
  -ComputerNamePrefix myVM

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myScaleSet `
  -VirtualMachineScaleSet $vmssConfig
```

Det tar några minuter att skapa och konfigurera alla skala uppsättning resurser och virtuella datorer.


## <a name="test-your-app"></a>Testa din app
Om du vill se din IIS-webbplats i åtgärden hämta offentlig IP-adressen för din belastningsutjämnare med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtar IP-adressen för *myPublicIP* skapas som en del av skaluppsättning:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupScaleSet -Name myPublicIP | select IpAddress
```

Ange den offentliga IP-adressen i en webbläsare. Appen visas, inklusive värdnamnet för den virtuella datorn som belastningsutjämnaren distribuerade trafik till:

![Kör IIS-webbplats](./media/tutorial-create-vmss/running-iis-site.png)

Om du vill se skaluppsättningen i praktiken du kan framtvinga-uppdatera webbläsaren om du vill se belastningsutjämnaren distribuerar trafik över alla de virtuella datorerna kör appen.


## <a name="management-tasks"></a>Administrativa uppgifter
Du kan behöva köra en eller flera administrativa uppgifter i hela livscykeln för skaluppsättning. Dessutom kanske du vill skapa skript som automatiserar olika livscykel-uppgifter. Azure PowerShell kan du snabbt att utföra dessa uppgifter. Här följer några vanliga uppgifter.

### <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skaluppsättning
Du kan visa en lista över virtuella datorer som körs i en skaluppsättning [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) på följande sätt:

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Loop through the instanaces in your scale set
for ($i=1; $i -le ($scaleset.Sku.Capacity - 1); $i++) {
    Get-AzureRmVmssVM -ResourceGroupName myResourceGroupScaleSet `
      -VMScaleSetName myScaleSet `
      -InstanceId $i
}
```


### <a name="increase-or-decrease-vm-instances"></a>Öka eller minska VM-instanser
Om du vill se antalet instanser som du har för närvarande i en skaluppsättning [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) och fråga på *sku.capacity*:

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -VMScaleSetName myScaleSet | `
    Select -ExpandProperty Sku
```

Du kan manuellt öka eller minska antalet virtuella datorer i skaluppsättningen med [uppdatering AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). I följande exempel anger hur många virtuella datorer i din skaluppsättningen *3*:

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -Name myScaleSet `
    -VirtualMachineScaleSet $scaleset
```

Om värdet tar några minuter att uppdatera det angivna antalet instanser i nivå.


### <a name="configure-autoscale-rules"></a>Konfigurera automatiska regler
I stället för att skala antalet instanser manuellt i din skala har angetts kan du definiera automatiska regler. De här reglerna övervaka instanser i en skaluppsättning och svara därefter baserat på mått och tröskelvärden som du definierar. I följande exempel skalas ut antalet instanser av en när Genomsnittlig CPU-belastningen är större än 60% under en period på 5 minuter. Om Genomsnittlig CPU-belastningen sedan sjunker under 30% under en period på 5 minuter, skalas instanser i en instans:

```powershell
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```

Design av användningen av Autoskala, Läs mer [Autoskala metodtips](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Nästa steg
Du har skapat en skaluppsättning för virtuell dator i den här självstudiekursen. Du har lärt dig att:

> [!div class="checklist"]
> * Använda tillägget för anpassat skript för att definiera en IIS-webbplats för att skala
> * Skapa en belastningsutjämnare för din skaluppsättning
> * Skapa en skaluppsättning för virtuell dator
> * Öka eller minska antalet instanser i en skaluppsättning
> * Skapa automatiska regler

Gå vidare till nästa kurs att lära dig mer om koncept för virtuella datorer för belastningsutjämning.

> [!div class="nextstepaction"]
> [Belastningsutjämna virtuella datorer](tutorial-load-balancer.md)
