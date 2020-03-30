---
title: Instansskydd för Azure-skalningsuppsättningsinstanser för virtuella datorer
description: Lär dig hur du skyddar Azure-skalningsuppsättningsinstanser från skalnings- och skalningsåtgärder.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254123"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Instansskydd för Azure-skalningsuppsättningsinstanser för virtuella datorer

Azure-skalningsuppsättningar för virtuella datorer möjliggör bättre elasticitet för dina arbetsbelastningar via [Automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md), så att du kan konfigurera när infrastrukturen skalas ut och när den skalas in. Med skalningsuppsättningar kan du också hantera, konfigurera och uppdatera ett stort antal virtuella datorer centralt via olika [inställningar för uppgraderingsprinciper.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) Du kan konfigurera en uppdatering på skalningsuppsättningsmodellen och den nya konfigurationen tillämpas automatiskt på varje skalningsuppsättningsinstans om du har angett uppgraderingsprincipen till Automatisk eller Rullande.

När ditt program bearbetar trafik kan det finnas situationer där du vill att specifika instanser ska behandlas på ett annat sätt än resten av skalningsuppsättningsinstansen. Vissa instanser i skalningsuppsättningen kan till exempel utföra tidskrävande åtgärder och du vill inte att dessa instanser ska skalas in förrän åtgärderna har slutförts. Du kan också ha specialiserat några instanser i skalningsuppsättningen för att utföra ytterligare eller andra uppgifter än de andra medlemmarna i skalningsuppsättningen. Du kräver att dessa "speciella" virtuella datorer inte ändras med de andra instanserna i skalningsuppsättningen. Instansskydd innehåller ytterligare kontroller för att aktivera dessa och andra scenarier för ditt program.

I den här artikeln beskrivs hur du kan använda och använda de olika instansskyddsfunktionerna med skalningsuppsättningsinstanser.

## <a name="types-of-instance-protection"></a>Typer av instansskydd
Skalningsuppsättningar ger två typer av instansskyddsfunktioner:

-   **Skydda mot inskalning**
    - Aktiverad via **egenskapen ProtectFromScaleIn** på skalningsuppsättningsinstansen
    - Skyddar instansen från automatisk skalning initierad skalning
    - Användarinitierade instansåtgärder (inklusive instansborttagning) **blockeras inte**
    - Åtgärder som initierats på skalningsuppsättningen (uppgradering, ombildning, deallocate, etc.) **blockeras inte**

-   **Skydda mot skalningsuppsättningsåtgärder**
    - Aktiverad via **egenskapen ProtectFromScaleSetActions** på skalningsuppsättningsinstansen
    - Skyddar instansen från automatisk skalning initierad skalning
    - Skyddar instans från åtgärder som initierats i skalningsuppsättningen (t.ex. uppgradering, ombildning, deallocate osv.)
    - Användarinitierade instansåtgärder (inklusive instansborttagning) **blockeras inte**
    - Borttagning av fullskalig uppsättning är **inte blockerad**

## <a name="protect-from-scale-in"></a>Skydda mot inskalning
Instansskydd kan användas för att skala uppsättning instanser när instanserna har skapats. Skydd tillämpas och ändras endast på [instansmodellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) och inte på [skalningsuppsättningsmodellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Det finns flera sätt att tillämpa skalningsskydd på dina skalningsuppsättningsinstanser som beskrivs i exemplen nedan.

### <a name="azure-portal"></a>Azure Portal

Du kan använda skalningsskydd via Azure-portalen på en instans i skalningsuppsättningen. Du kan inte justera mer än en instans i taget. Upprepa stegen för varje instans som du vill skydda.
 
1. Gå till en befintlig skalningsuppsättning för virtuella datorer.
1. Välj **Instanser** på menyn till vänster under **Inställningar**.
1. Markera namnet på den instans som du vill skydda.
1. Välj fliken **Skyddsprincip.**
1. I bladet **Skyddsprincip** väljer du alternativet **Skydda mot skalning.**
1. Välj **Spara**. 

### <a name="rest-api"></a>REST API

I följande exempel tillämpas skalningsskydd på en instans i skalningsuppsättningen.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Instansskydd stöds endast med API-version 2019-03-01 och senare

### <a name="azure-powershell"></a>Azure PowerShell

Använd [cmdleten Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) för att tillämpa skalningsskydd på din skalningsuppsättningsinstans.

I följande exempel tillämpas skalningsskydd på en instans i skalningsuppsättningen med instans-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [az vmss-uppdatering](/cli/azure/vmss#az-vmss-update) för att tillämpa skalningsskydd på din skalningsuppsättningsinstans.

I följande exempel tillämpas skalningsskydd på en instans i skalningsuppsättningen med instans-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Skydda mot skalningsuppsättningsåtgärder
Instansskydd kan användas för att skala uppsättning instanser när instanserna har skapats. Skydd tillämpas och ändras endast på [instansmodellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) och inte på [skalningsuppsättningsmodellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Om du skyddar en instans från skalningsuppsättningsåtgärder skyddas även instansen från initierad skalning av automatisk skalning.

Det finns flera sätt att tillämpa skalningsuppsättningsåtgärder skydd på dina skalningsuppsättningsinstanser som beskrivs i exemplen nedan.

### <a name="azure-portal"></a>Azure Portal

Du kan använda skydd mot skalningsuppsättningsåtgärder via Azure-portalen på en instans i skalningsuppsättningen. Du kan inte justera mer än en instans i taget. Upprepa stegen för varje instans som du vill skydda.
 
1. Gå till en befintlig skalningsuppsättning för virtuella datorer.
1. Välj **Instanser** på menyn till vänster under **Inställningar**.
1. Markera namnet på den instans som du vill skydda.
1. Välj fliken **Skyddsprincip.**
1. I bladet **Skyddsprincip** väljer du alternativet **Skydda mot skaluppsättningsåtgärder.**
1. Välj **Spara**. 

### <a name="rest-api"></a>REST API

I följande exempel gäller skydd mot skalningsuppsättningsåtgärder för en instans i skalningsuppsättningen.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Instansskydd stöds endast med API-version 2019-03-01 och högre.</br>
Om du skyddar en instans från skalningsuppsättningsåtgärder skyddas även instansen från initierad skalning av automatisk skalning. Du kan inte ange "protectFromScaleIn": false when setting "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Använd [cmdleten Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) för att tillämpa skydd mot skalningsuppsättningsåtgärder på skalningsuppsättningsinstansen.

I följande exempel gäller skydd mot skalningsuppsättningsåtgärder till en instans i skalningsuppsättningen med instans-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [az vmss-uppdatering](/cli/azure/vmss#az-vmss-update) för att tillämpa skydd mot skalningsuppsättningsåtgärder på din skalningsuppsättningsinstans.

I följande exempel gäller skydd mot skalningsuppsättningsåtgärder till en instans i skalningsuppsättningen med instans-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Felsöka
### <a name="no-protectionpolicy-on-scale-set-model"></a>Inget skyddPolicy på skalningsuppsättningsmodell
Instansskydd är endast tillämpligt på skalningsuppsättningsinstanser och inte på skalningsuppsättningsmodellen.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Inget skyddPolicy för skalningsuppsättningsinstansmodell
Som standard tillämpas inte skyddsprincipen på en instans när den skapas.

Du kan använda instansskydd för skalningsuppsättningsinstanser när instanserna har skapats.

### <a name="not-able-to-apply-instance-protection"></a>Det går inte att tillämpa instansskydd
Instansskydd stöds endast med API-version 2019-03-01 och högre. Kontrollera API-versionen som används och uppdatera efter behov. Du kan också behöva uppdatera din PowerShell eller CLI till den senaste versionen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på skalningsuppsättningar för virtuella datorer.
