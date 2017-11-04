---
title: "Hantera Skalningsuppsättningar i virtuella datorer med Azure PowerShell | Microsoft Docs"
description: "Vanliga Azure PowerShell-cmdletar för att hantera Skalningsuppsättningar i virtuella datorer, till exempel att starta och stoppa en instans eller ändra skalan Ange kapacitet."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 39836b207a84911d4749da8a084779d93949846b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Hantera en virtuell dator-skala med Azure PowerShell
Du kan behöva köra en eller flera administrativa uppgifter i hela livscykeln för en skaluppsättning för virtuell dator. Dessutom kanske du vill skapa skript som automatiserar olika livscykel-uppgifter. Den här artikeln beskrivs några vanliga Azure PowerShell-cmdlets som gör att du kan utföra dessa uppgifter.

För att slutföra dessa hanteringsaktiviteter, behöver du den senaste Azure PowerShell-modulen. Mer information om hur du installerar och använder den senaste versionen finns [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps). Om du behöver skapa en skaluppsättning för virtuell dator kan du [skapa en skala som angetts i Azure portal](virtual-machine-scale-sets-portal-create.md).


## <a name="view-information-about-a-scale-set"></a>Visa information om en skaluppsättning
Om du vill visa allmän information om en skalningsuppsättning [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). I följande exempel hämtar information om skaluppsättningen namngivna *myScaleSet* i den *myResourceGroup* resursgruppen. Ange egna namn på följande sätt:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skaluppsättning
Du kan visa en lista över VM-instans i en skaluppsättning [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). I följande exempel visa en lista med alla VM-instanser i skaluppsättningen namngivna *myScaleSet* och i den *myResourceGroup* resursgruppen. Ange egna värden för dessa namn:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Om du vill visa mer information om en specifik VM-instans, lägger du till den `-InstanceId` parameter till [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) och ange en instans för att visa. I följande exempel visar information om VM-instans *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange egna namn på följande sätt:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skaluppsättning
Föregående kommandona visade information om din skaluppsättning och VM-instanser. Du kan ändra kapacitet för att öka eller minska antalet instanser i skaluppsättning. Skala automatiskt skapar eller tar bort antalet virtuella datorer som krävs och sedan konfigurerar de virtuella datorerna för att ta emot trafik för programmet.

Skapa först ett scale set-objekt med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), ange ett nytt värde för `sku.capacity`. Om du vill tillämpa ändringen kapacitet, Använd [uppdatering AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Följande exempel uppdateringar *myScaleSet* i den *myResourceGroup* resursgrupp till en kapacitet på *5* instanser. Ange egna värden på följande sätt:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Om tar några minuter att uppdatera kapaciteten för din skala har angetts. Om du minskar kapaciteten för en skala ange de virtuella datorerna med högsta instans-ID tas bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skaluppsättning
Om du vill stoppa en eller flera virtuella datorer i en skaluppsättning använder [stoppa AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). Den `-InstanceId` parametern kan du ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skaluppsättning. Avgränsa varje instans-ID om du vill stoppa flera virtuella datorer med ett kommatecken.

Följande exempel stoppar instans *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange värdena på följande sätt:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Som standard stoppade virtuella datorer har frigjorts och till inte datorkostnader. Om du vill att den virtuella datorn kvar i ett etablerat tillstånd när stoppats, lägga till den `-StayProvisioned` parametern till det föregående kommandot. Stoppade virtuella datorer som finns kvar etablerade avgifter reguljära beräkning.


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skaluppsättning
Starta en eller flera virtuella datorer i en skaluppsättning med [Start AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). Den `-InstanceId` parametern kan du ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID för startas alla virtuella datorer i skaluppsättning. Avgränsa varje instans-ID för att starta flera virtuella datorer med ett kommatecken.

Följande exempel startar instans *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange värdena på följande sätt:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skaluppsättning
Om du vill starta om en eller flera virtuella datorer i en skaluppsättning använder [Retart AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). Den `-InstanceId` parametern kan du ange en eller flera virtuella datorer startas om. Om du inte anger ett instans-ID för alla virtuella datorer i skaluppsättning har startats om. Avgränsa varje instans-ID för att starta om flera virtuella datorer med ett kommatecken.

I följande exempel startar om instansen *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange värdena på följande sätt:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skaluppsättning
Ta bort en eller flera virtuella datorer i en skaluppsättning med [ta bort AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). Den `-InstanceId` parametern kan du ange en eller flera virtuella datorer ska tas bort. Om du inte anger ett instans-ID för tas alla virtuella datorer i skaluppsättning bort. Avgränsa varje instans-ID för att ta bort flera virtuella datorer med ett kommatecken.

I följande exempel tar bort instansen *0* i skaluppsättningen namngivna *myScaleSet* och *myResourceGroup* resursgruppen. Ange värdena på följande sätt:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga uppgifter för skalningsuppsättningar inkluderar så [distribuera ett program](virtual-machine-scale-sets-deploy-app.md), och [uppgradera VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure PowerShell för att [konfigurera regler för automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md).