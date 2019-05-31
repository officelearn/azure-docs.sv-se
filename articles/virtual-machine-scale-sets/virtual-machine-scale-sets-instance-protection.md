---
title: Skydd av instans för Azure VM-skalningsuppsättning skalningsuppsättningsinstanser | Microsoft Docs
description: Lär dig hur du skyddar Azure VM-skalningsuppsättningsinstanser från in- och skalningsuppsättning åtgärder.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 61430f5a43a04fa0e5b2f0c79ff03419c73aaf28
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416550"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>Instans-skydd för Azure-datorer har angetts instances (förhandsversion)
Azure VM scale sets kan du bättre flexibilitet när det gäller för dina arbetsbelastningar via [Autoskala](virtual-machine-scale-sets-autoscale-overview.md), så att du kan konfigurera när din infrastruktur måste skalas upp och när det skalas in. Skalningsuppsättningar kan du centralt hantera, konfigurera och uppdatera ett stort antal virtuella datorer via olika [versionsuppgraderingsprincipen](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) inställningar. Du kan konfigurera en uppdatering på skalningsuppsättningsmodell och den nya konfigurationen tillämpas automatiskt på varje scale set-instans om du har ställt in uppgraderingsprincipen för automatisk eller rullande.

När ditt program bearbetar trafik, det kan finnas situationer där du vill att specifika instanser som ska behandlas annorlunda än resten av skalan skalningsuppsättningsinstans. Exempelvis kan vissa instanser i skalningsuppsättningen kan utföra långvariga åtgärder och du vill inte att dessa instanser att skalas in tills åtgärderna har slutförts. Du kan också ha särskild några instanser i skaluppsättningen för att utföra ytterligare eller andra aktiviteter än andra medlemmar i skalningsuppsättningen. Du behöver dessa 'särskilda ”virtuella datorer inte ska ändras med de andra instanserna i skalningsuppsättningen. Skydd av instans innehåller ytterligare kontroller för att aktivera dessa och andra scenarier för ditt program.

Den här artikeln beskrivs hur du kan tillämpa och använda annan instans-skyddsfunktioner med skalningsuppsättningsinstanser.

> [!NOTE]
>Skydd av instans är för närvarande i offentlig förhandsversion. Inga delta i proceduren behövs för att använda funktionen förhandsversion som beskrivs nedan. Förhandsversionen av instans protection stöds bara med API-versionen 2019-03-01 och på skalningsuppsättningar med hanterade diskar.

## <a name="types-of-instance-protection"></a>Typer av skydd för instansen
Skalningsuppsättningar ger två typer av instans funktioner:

-   **Skydda mot skala in**
    - Aktiveras via **protectFromScaleIn** på skala egenskapsuppsättning instans
    - Skyddar instans från automatisk skalning initierade skala in
    - Användarinitierad instans åtgärder (inklusive ta bort instansen) är **inte har blockerats**
    - Åtgärder på skalningsuppsättningen (uppgradera, Återställ avbildning, frigöra osv.) är **inte har blockerats**

-   **Skydda mot scale set-åtgärder**
    - Aktiveras via **protectFromScaleSetActions** på skala egenskapsuppsättning instans
    - Skyddar instans från automatisk skalning initierade skala in
    - Skyddar instans från skalningsuppsättningen-åtgärder (till exempel uppgraderingen, Återställ avbildning, frigöra, osv.)
    - Användarinitierad instans åtgärder (inklusive ta bort instansen) är **inte har blockerats**
    - Borttagning av fullständig skalningsuppsättningen är **inte har blockerats**

## <a name="protect-from-scale-in"></a>Skydda mot skala in
Instans-skydd kan tillämpas till skalningsuppsättningsinstanser när instanser har skapats. Skydd som tillämpas och ändras bara på den [instansmodell](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) och inte på den [scale Sets modellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Det finns flera sätt att skala in skydd på dina skalningsuppsättningsinstanser som beskrivs i exemplen nedan.

### <a name="rest-api"></a>REST-API

I följande exempel gäller skala in skydd för en instans i skalningsuppsättningen.

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
>Skydd av instans stöds endast med API-versionen 2019-03-01 och senare

### <a name="azure-powershell"></a>Azure PowerShell

Använd den [uppdatering AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet för att tillämpa skala in skydd på din skalningsuppsättning skalningsuppsättningsinstans.

I följande exempel gäller skala in skydd för en instans i skalningsuppsättningen med instans-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [az vmss uppdatera](/cli/azure/vmss#az-vmss-update) att tillämpa skala in skydd på din scale set-instans.

I följande exempel gäller skala in skydd för en instans i skalningsuppsättningen med instans-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Skydda mot scale set-åtgärder
Instans-skydd kan tillämpas till skalningsuppsättningsinstanser när instanser har skapats. Skydd som tillämpas och ändras bara på den [instansmodell](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) och inte på den [scale Sets modellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Skydda en instans inte set skalningsåtgärder skyddar även instansen från skala in automatisk skalning initieras.

Det finns flera olika sätt att tillämpa skala ställa in åtgärder skydd på din skalningsuppsättning instanser som beskrivs i exemplen nedan.

### <a name="rest-api"></a>REST-API

I följande exempel gäller skydd mot åtgärder för skalning till en instans i skalningsuppsättningen.

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
>Skydd av instans stöds endast med API-versionen 2019-03-01 och senare.</br>
Skydda en instans inte set skalningsåtgärder skyddar även instansen från skala in automatisk skalning initieras. Du kan inte ange ”protectFromScaleIn”: false när du anger ”protectFromScaleSetActions”: true

### <a name="azure-powershell"></a>Azure PowerShell

Använd den [uppdatering AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet för att tillämpa skydd från skala ange åtgärder till din scale set-instans.

I följande exempel gäller skydd mot åtgärder för skalning till en instans i skalningsuppsättningen med instans-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [az vmss uppdatera](/cli/azure/vmss#az-vmss-update) att tillämpa skydd mot åtgärder för skalning på din scale set-instans.

I följande exempel gäller skydd mot åtgärder för skalning till en instans i skalningsuppsättningen med instans-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Felsöka
### <a name="no-protectionpolicy-on-scale-set-model"></a>Inga protectionPolicy på skalningsuppsättningen
Skydd av instans kan bara användas på skalningsuppsättningsinstanser och inte i skalningsuppsättningen.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Inga protectionPolicy på instansen av skalningsuppsättningen
Som standard tillämpas inte protection-principen till en instans när den skapas.

Du kan tillämpa skydd-instans till skalningsuppsättningsinstanser när instanser har skapats.

### <a name="not-able-to-apply-instance-protection"></a>Det går inte att tillämpa skydd-instans
Skydd av instans stöds endast med API-versionen 2019-03-01 och senare. Kontrollera den API-versionen som används och uppdatera efter behov. Du kan också behöva uppdatera PowerShell eller CLI till den senaste versionen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuera ditt program](virtual-machine-scale-sets-deploy-app.md) på virtuella datorer du anger.
