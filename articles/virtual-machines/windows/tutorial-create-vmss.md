---
title: Självstudier – Skapa en VM-skalningsuppsättning för Windows i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du använder Azure PowerShell för att skapa och distribuera ett program med hög tillgänglighet på virtuella Windows-datorer med hjälp av en skalningsuppsättning för virtuella datorer
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 49754fd4409b1fbc6b15577d37e216290582ef2b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Självstudiekurs: Skapa en VM-skalningsuppsättning och distribuera en app med hög tillgänglighet i Windows med Azure PowerShell
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. I självstudien distribuerar du en VM-skalningsuppsättning i Azure. Lär dig att:

> [!div class="checklist"]
> * Använda tillägget för anpassat skript för att definiera en IIS-webbplats för att skala
> * Skapa en belastningsutjämnare för din skalningsuppsättning
> * Skapa en VM-skalningsuppsättning
> * Öka eller minska antalet instanser i en skalningsuppsättning
> * Skapa regler för automatisk skalning

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.


## <a name="scale-set-overview"></a>Översikt över skalningsuppsättning
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Virtuella datorer i en skalningsuppsättning är distribuerade över logiska fel- och uppdateringsdomäner i en eller flera *placeringsgrupper*. Detta är grupper med virtuella datorer som har en liknande konfiguration, vilket liknar [tillgänglighetsuppsättningar](tutorial-availability-sets.md).

Virtuella datorer skapas efter behov i en skalningsuppsättning. Du kan definiera regler för automatisk skalning om du vill styra hur och när virtuella datorer läggs till eller tas bort från skalningsuppsättningen. De här reglerna kan utlösas baserat på mått som CPU-belastning, minnesanvändning eller nätverkstrafik.

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
Om du vill testa skaluppsättningen hämtar du den offentliga IP-adressen för belastningsutjämnaren med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIP* som skapas som en del av skalningsuppsättningen:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Ange den offentliga IP-adressen i en webbläsare. Webbappen visas med värddatornamnet för den virtuella dator som belastningsutjämnaren distribuerade trafik till:

![Köra IIS-webbplats](./media/tutorial-create-vmss/running-iis-site.png)

Om du vill se när skalningsuppsättningen används kan du framtvinga en uppdatering av webbläsaren. Då visas hur belastningsutjämnaren distribuerar trafik över alla virtuella datorer som kör programmet.


## <a name="management-tasks"></a>Administrativa uppgifter
Du kan behöva köra en eller flera administrativa uppgifter i hela livscykeln för skalningsuppsättningen. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. Med Azure PowerShell kan du snabbt utföra dessa uppgifter. Här följer några vanliga uppgifter.

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
Om du vill se antalet instanser som du för närvarande har i en skalningsuppsättning använder du [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) och frågar efter *sku.capacity*:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

Du kan sedan manuellt öka eller minska antalet virtuella datorer i skalningsuppsättningen med [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). I följande exempel anges antalet virtuella datorer i din skalningsuppsättning till *3*:

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

Det tar några minuter att uppdatera det angivna antalet instanser i skalningsuppsättningen.


### <a name="configure-autoscale-rules"></a>Konfigurera regler för automatisk skalning
Du kan definiera regler för automatisk skalning i stället för att skala antalet instanser manuellt i skalningsuppsättningen. De här reglerna övervakar instanserna i skalningsuppsättningen och svarar därefter baserat på de mått och tröskelvärden som du definierar. I följande exempel skalas antalet instanser ut med en när den genomsnittliga CPU-belastningen är större än 60 % under en 5-minutersperiod. Om den genomsnittliga CPU-belastningen sedan sjunker under 30 % under en 5-minutersperiod, skalas instanserna in med en instans:

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
> * Skapa en belastningsutjämnare för din skalningsuppsättning
> * Skapa en VM-skalningsuppsättning
> * Öka eller minska antalet instanser i en skalningsuppsättning
> * Skapa regler för automatisk skalning

Gå vidare till nästa självstudie för att lära dig mer om belastningsutjämning för virtuella datorer.

> [!div class="nextstepaction"]
> [Balansera belastningen mellan virtuella datorer](tutorial-load-balancer.md)
