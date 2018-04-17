---
title: Skapa en Skalningsuppsättningar i virtuella datorer för Windows i Azure | Microsoft Docs
description: Skapa och distribuera ett program som har hög tillgänglighet på virtuella Windows-datorer med hjälp av en skaluppsättning för virtuell dator
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a4a2d8d616a503a2b200627a52103b78e573f767
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>Skapa en Virtual Machine Scale Set och distribuera en app som har hög tillgänglighet i Windows
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. I självstudien distribuerar du en VM-skalningsuppsättning i Azure. Lär dig att:

> [!div class="checklist"]
> * Använda tillägget för anpassat skript för att definiera en IIS-webbplats för att skala
> * Skapa en belastningsutjämnare för din skaluppsättning
> * Skapa en VM-skalningsuppsättning
> * Öka eller minska antalet instanser i en skalningsuppsättning
> * Skapa regler för automatisk skalning

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt, kräver den här självstudiekursen Azure PowerShell Modulversion 5.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.


## <a name="scale-set-overview"></a>Översikt över skalningsuppsättning
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Virtuella datorer i en skalningsuppsättning är distribuerade över logiska fel- och uppdateringsdomäner i en eller flera *placeringsgrupper*. Detta är grupper med virtuella datorer som har en liknande konfiguration, vilket liknar [tillgänglighetsuppsättningar](tutorial-availability-sets.md).

Virtuella datorer skapas efter behov i en skalningsuppsättning. Du kan definiera regler för automatisk skalning om du vill styra hur och när virtuella datorer läggs till eller tas bort från skalningsuppsättningen. De här reglerna kan utlösa baserat på mått som CPU-belastning, minnesanvändning eller nätverkstrafik.

Skalningsuppsättningar har stöd för upp till 1 000 virtuella datorer när du använder en avbildning i Azure-plattformen. För arbetsbelastningar med större installations- eller VM-anpassningskrav kan du [skapa en anpassad VM-avbildning](tutorial-custom-images.md). Du kan skapa upp till 300 virtuella datorer i en skalningsuppsättning när du använder en anpassad avbildning.


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Skapa en VM-skalningsuppsättning med [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som använder *Windows Server 2016 Datacenter*-plattformsavbildningen. Azure-nätverksresurser för virtuellt nätverk, offentlig IP-adress och belastningsutjämnare skapas automatiskt. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="deploy-sample-application"></a>Distribuera exempelprogram
Testa din skalningsuppsättning genom att installera ett grundläggande webbprogram. Det anpassade skripttillägget i Azure används för att hämta och köra ett skript som installerar IIS på de virtuella datorinstanserna. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Mer information finns i [översikten över tillägget för anpassat skript](extensions-customscript.md).

Använd det anpassade skripttillägget för att installera en grundläggande IIS-webbserver. Tillämpa tillägget för anpassat skript som installerar IIS på följande sätt:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Se nivå som i åtgärd kan du få offentliga IP-adressen för din belastningsutjämnare med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtar IP-adressen för *myPublicIP* skapas som en del av skaluppsättning:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Ange den offentliga IP-adressen i en webbläsare. Webbprogrammet visas, inklusive värdnamnet för den virtuella datorn som belastningsutjämnaren distribuerade trafik till:

![Köra IIS-webbplats](./media/tutorial-create-vmss/running-iis-site.png)

Om du vill se när skalningsuppsättningen används kan du framtvinga en uppdatering av webbläsaren. Då visas hur belastningsutjämnaren distribuerar trafik över alla virtuella datorer som kör programmet.


## <a name="management-tasks"></a>Administrativa uppgifter
Du kan behöva köra en eller flera administrativa uppgifter i hela livscykeln för skalningsuppsättningen. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. Azure PowerShell kan du snabbt att utföra dessa uppgifter. Här följer några vanliga uppgifter.

### <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Om du vill visa en lista med virtuella datorinstanser i en skalningsuppsättning, använder du [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) på följande sätt:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

Följande exempelutdata visar två virtuella datorinstanser i skalningsuppsättningen:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Om du vill visa mer information om en specifik VM-instans, lägger du till parametern `-InstanceId` till [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Följande exempel visar information om den virtuella datorinstansen *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Öka eller minska VM-instanser
Om du vill se antalet instanser som du har för närvarande i en skaluppsättning [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) och fråga på *sku.capacity*:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

Du kan manuellt öka eller minska antalet virtuella datorer i skaluppsättningen med [uppdatering AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). I följande exempel anges antalet virtuella datorer i din skalningsuppsättning till *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Om värdet tar några minuter att uppdatera det angivna antalet instanser i nivå.


### <a name="configure-autoscale-rules"></a>Konfigurera regler för automatisk skalning
I stället för att skala antalet instanser manuellt i din skala har angetts kan du definiera automatiska regler. De här reglerna övervakar instanserna i skalningsuppsättningen och svarar därefter baserat på de mått och tröskelvärden som du definierar. I följande exempel skalas antalet instanser ut med en när den genomsnittliga CPU-belastningen är större än 60 % under en 5-minutersperiod. Om Genomsnittlig CPU-belastningen sedan sjunker under 30% under en period på 5 minuter, skalas instanser i en instans:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
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
  -MetricResourceId $myScaleSetId `
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
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfiles $myScaleProfile
```

Mer information om användningen av autoskalning finns i [Metodtips för autoskalning](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Nästa steg
I självstudien skapade du en VM-skalningsuppsättning. Du har lärt dig att:

> [!div class="checklist"]
> * Använda tillägget för anpassat skript för att definiera en IIS-webbplats för att skala
> * Skapa en belastningsutjämnare för din skaluppsättning
> * Skapa en VM-skalningsuppsättning
> * Öka eller minska antalet instanser i en skalningsuppsättning
> * Skapa regler för automatisk skalning

Gå vidare till nästa självstudie för att lära dig mer om belastningsutjämning för virtuella datorer.

> [!div class="nextstepaction"]
> [Balansera belastningen mellan virtuella datorer](tutorial-load-balancer.md)
