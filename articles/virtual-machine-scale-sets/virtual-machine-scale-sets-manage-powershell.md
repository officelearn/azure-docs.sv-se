---
title: Hantera skalningsuppsättningar för virtuella datorer med Azure PowerShell
description: Vanliga Azure PowerShell-cmdletar för att hantera skalningsuppsättningar för virtuella datorer, till exempel hur du startar och stoppar en instans, eller ändrar skalningsuppsättningskapaciteten.
author: ju-shim
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: 5794fb40b49a575c12f6855292c41014fabf3b8c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011607"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Hantera en skalningsuppsättning för virtuella datorer med Azure PowerShell

Under livscykeln för en VM-skalningsuppsättning kan du behöva köra en eller flera hanteringsuppgifter. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. I den här artikeln beskrivs några av de vanliga Azure PowerShell-cmdletar som låter dig utföra dessa uppgifter.

Om du behöver skapa en skalningsuppsättning för virtuella datorer kan du [skapa en skalningsuppsättning med Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Visa information om en skalningsuppsättning
Om du vill visa den övergripande informationen om en skalningsuppsättning använder du [Get-AzVmss](/powershell/module/az.compute/get-azvmss). I följande exempel får du information om skalningsuppsättningen *myScaleSet* i resursgruppen *myResourceGroup.* Ange dina egna namn på följande sätt:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Om du vill visa en lista över VM-instans i en skalningsuppsättning använder du [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). I följande exempel visas alla VM-instanser i skalningsuppsättningen *myScaleSet* och i resursgruppen *myResourceGroup.* Ange dina egna värden för dessa namn:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Om du vill visa ytterligare information `-InstanceId` om en viss VM-instans lägger du till parametern [i Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) och anger en instans att visa. I följande exempel visar information om VM-instans *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup-resursgruppen.* Ange dina egna namn på följande sätt:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skalningsuppsättning
Föregående kommandon visade information om din skalningsuppsättning och VM-instanser. Om du vill öka eller minska antalet instanser i skalningsuppsättningen kan du ändra kapaciteten. Skalningsuppsättningen skapar eller tar automatiskt bort det antal virtuella datorer som krävs och konfigurerar sedan de virtuella datorerna så att de tar emot programtrafik.

Skapa först ett skalningsuppsättningsobjekt med [Get-AzVmss](/powershell/module/az.compute/get-azvmss) och ange sedan ett nytt värde för `sku.capacity`. Om du vill tillämpa kapacitetsändringen, använder du [Update-AzVmss](/powershell/module/az.compute/update-azvmss). I följande exempel *uppdateras myScaleSet* i resursgruppen *myResourceGroup* till en kapacitet på *5* instanser. Ange dina egna värderingar enligt följande:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Det tar några minuter att uppdatera kapaciteten för din skalningsuppsättning. Om du minskar kapaciteten för en skalningsuppsättning tas de virtuella datorerna med de högsta instans-ID:na bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skalningsuppsättning
Om du vill stoppa en eller flera virtuella datorer i en skalningsuppsättning, använder du [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skalningsuppsättningen. Om du vill stoppa flera virtuella datorer separerar du varje instans-ID med ett kommatecken.

I följande exempel stoppas instans *0* i skalningsuppsättningen *myScaleSet* och resursgruppen *myResourceGroup.* Ange dina egna värderingar enligt följande:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Som standard frigörs stoppade virtuella datorer och uppbär inga beräkningskostnader. Om du vill att den virtuella datorn är kvar i etablerat tillstånd när den stoppats, lägger du till parametern `-StayProvisioned` till det föregående kommandot. Stoppade virtuella datorer som fortsätter att vara etablerade, kostar vanliga beräkningsavgifter.


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skalningsuppsättning
Om du vill starta en eller flera virtuella datorer i en skalningsuppsättning, använder du [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen. Om du vill starta flera virtuella datorer separerar du varje instans-ID med ett kommatecken.

I följande exempel startar instans *0* i skalningsuppsättningen *myScaleSet* och resursgruppen *myResourceGroup.* Ange dina egna värderingar enligt följande:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skalningsuppsättning
Om du vill starta om en eller flera virtuella datorer i en skalningsuppsättning använder du [Starta om azvmss](/powershell/module/az.compute/restart-azvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att starta om. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen om. Om du vill starta om flera virtuella datorer separerar du varje instans-ID med ett kommatecken.

I följande exempel startas instans *0* i skalningsuppsättningen *myScaleSet* och resursgruppen *myResourceGroup.* Ange dina egna värderingar enligt följande:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skaluppsättning
Om du vill ta bort en eller flera virtuella datorer i en skalningsuppsättning använder du [Ta bort AzVmss](/powershell/module/az.compute/remove-azvmss). Med `-InstanceId` parametern kan du ange en eller flera virtuella datorer som ska tas bort. Om du inte anger ett instans-ID tas alla virtuella datorer i skalningsuppsättningen bort. Om du vill ta bort flera virtuella datorer separerar du varje instans-ID med ett kommatecken.

I följande exempel tas instans *0* bort i skalningsuppsättningen *myScaleSet* och resursgruppen *myResourceGroup.* Ange dina egna värderingar enligt följande:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga uppgifter för skalningsuppsättningar är hur [du distribuerar ett program](virtual-machine-scale-sets-deploy-app.md)och [uppgraderar VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure PowerShell för att [konfigurera regler för automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md).
