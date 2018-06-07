---
title: Hantera Skalningsuppsättningar i virtuella datorer med Azure PowerShell | Microsoft Docs
description: Vanliga Azure PowerShell-cmdletar för att hantera Skalningsuppsättningar i virtuella datorer, till exempel att starta och stoppa en instans eller ändra skalan Ange kapacitet.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: iainfou
ms.openlocfilehash: 39cd7fa2232329716ba16abf92ba4a5f2cc15487
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652788"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Hantera en virtuell dator-skala med Azure PowerShell
Under livscykeln för en VM-skalningsuppsättning kan du behöva köra en eller flera hanteringsuppgifter. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. Den här artikeln beskrivs några vanliga Azure PowerShell-cmdlets som gör att du kan utföra dessa uppgifter.

För att slutföra dessa hanteringsaktiviteter, behöver du den senaste Azure PowerShell-modulen. Mer information finns i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps). Om du behöver skapa en skaluppsättning för virtuell dator kan du [skapa en skala med Azure PowerShell](quick-create-powershell.md).


## <a name="view-information-about-a-scale-set"></a>Visa information om en skaluppsättning
Om du vill visa allmän information om en skalningsuppsättning [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). I följande exempel hämtar information om skaluppsättningen namngivna *myScaleSet* i den *myResourceGroup* resursgruppen. Ange egna namn på följande sätt:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Du kan visa en lista över VM-instans i en skaluppsättning [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). I följande exempel visar alla VM-instanser i skaluppsättningen namngivna *myScaleSet* och i den *myResourceGroup* resursgruppen. Ange egna värden för dessa namn:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Om du vill visa mer information om en specifik VM-instans, lägger du till den `-InstanceId` parameter till [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) och ange en instans för att visa. I följande exempel visar information om VM-instans *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna namn på följande sätt:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skalningsuppsättning
Föregående kommandona visade information om din skaluppsättning och VM-instanser. Du kan ändra kapacitet för att öka eller minska antalet instanser i skaluppsättning. Skala automatiskt skapar eller tar bort antalet virtuella datorer som krävs och sedan konfigurerar de virtuella datorerna för att ta emot trafik för programmet.

Skapa först ett skalningsuppsättningsobjekt med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) och ange sedan ett nytt värde för `sku.capacity`. Om du vill tillämpa kapacitetsändringen, använder du [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Följande exempel uppdateringar *myScaleSet* i den *myResourceGroup* resursgrupp till en kapacitet på *5* instanser. Ange egna värden på följande sätt:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Det tar några minuter att uppdatera kapaciteten för din skalningsuppsättning. Om du minskar kapaciteten för en skala ange de virtuella datorerna med högsta instans-ID tas bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skaluppsättning
Om du vill stoppa en eller flera virtuella datorer i en skalningsuppsättning, använder du [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skalningsuppsättningen. Avgränsa varje instans-ID om du vill stoppa flera virtuella datorer med ett kommatecken.

Följande exempel stoppar instans *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna värden på följande sätt:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Som standard frigörs stoppade virtuella datorer och uppbär inga beräkningskostnader. Om du vill att den virtuella datorn är kvar i etablerat tillstånd när den stoppats, lägger du till parametern `-StayProvisioned` till det föregående kommandot. Stoppade virtuella datorer som fortsätter att vara etablerade, kostar vanliga beräkningsavgifter.


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skaluppsättning
Om du vill starta en eller flera virtuella datorer i en skalningsuppsättning, använder du [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen. Avgränsa varje instans-ID för att starta flera virtuella datorer med ett kommatecken.

Följande exempel startar instans *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna värden på följande sätt:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skaluppsättning
Om du vill starta om en eller flera virtuella datorer i en skalningsuppsättning, använder du [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att starta om. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen om. Avgränsa varje instans-ID för att starta om flera virtuella datorer med ett kommatecken.

I följande exempel startar om instansen *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna värden på följande sätt:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skaluppsättning
Ta bort en eller flera virtuella datorer i en skaluppsättning med [ta bort AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). Den `-InstanceId` parametern kan du ange en eller flera virtuella datorer ska tas bort. Om du inte anger ett instans-ID för tas alla virtuella datorer i skaluppsättning bort. Avgränsa varje instans-ID för att ta bort flera virtuella datorer med ett kommatecken.

I följande exempel tar bort instansen *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna värden på följande sätt:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga uppgifter för skalningsuppsättningar inkluderar så [distribuera ett program](virtual-machine-scale-sets-deploy-app.md), och [uppgradera VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure PowerShell för att [konfigurera regler för automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md).
