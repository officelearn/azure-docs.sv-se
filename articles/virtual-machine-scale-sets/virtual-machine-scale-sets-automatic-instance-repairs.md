---
title: Automatisk instansreparationer med Azure-skalningsuppsättningar för virtuella datorer
description: Lär dig hur du konfigurerar automatisk reparationsprincip för VM-instanser i en skalningsuppsättning
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274819"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Förhandsversion: Automatiska instansreparationer för Azure-skalningsuppsättningar för virtuella datorer

Aktivera automatiska instansreparationer för Azure-skalningsuppsättningar för virtuella datorer bidrar till att uppnå hög tillgänglighet för program genom att underhålla en uppsättning felfria instanser. Om en instans i skalningsuppsättningen visar sig vara felaktig som rapporterats av [Programhälsotillägg](./virtual-machine-scale-sets-health-extension.md) eller [belastningsutjämnad hälsoavsökningar,](../load-balancer/load-balancer-custom-probe-overview.md)utför den här funktionen automatiskt instansreparation genom att ta bort den felaktiga instansen och skapa en ny som ersätter den.

> [!NOTE]
> Den här förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Krav för att använda automatiska instansreparationer

**Välj för automatiska instansreparationer förhandsvisning**

Använd antingen REST API eller Azure PowerShell för att välja den automatiska instansen reparationer förhandsvisning. De här stegen registrerar din prenumeration för förhandsgranskningsfunktionen. Observera att detta bara är en engångsinställning som krävs för att använda den här funktionen. Om din prenumeration redan är registrerad för automatisk instans reparationer förhandsvisning, då behöver du inte registrera dig igen. 

Använda REST-API:et 

1. Registrera dig för funktionen med [Funktioner - Registrera dig](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. Vänta några minuter innan *staten* har *ändrats*till Registrerad . Du kan använda följande API för att bekräfta detta.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. När *staten* har ändrats till *Registrerad*kör du följande.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Använda Azure PowerShell

1. Registrera dig för funktionen med cmdlet [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) följt av [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Vänta några minuter innan *RegistrationState* har ändrats till *Registrerad*. Du kan använda följande cmdlet för att bekräfta detta.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 Svaret bör vara följande.

| Funktionsnamn                           | ProviderName            | Registreringsstat       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesFörvisa      | Microsoft.Compute       | Registrerad              |

3. När *RegistrationState* att ändra till *Registrerad*, kör sedan följande cmdlet.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Aktivera hälsoövervakning av program för skalningsuppsättning**

Skalningsuppsättningen bör ha programhälsoövervakning för instanser aktiverade. Detta kan göras med hjälp av antingen [Program Health extension](./virtual-machine-scale-sets-health-extension.md) eller Load [Balancer hälsa sonder](../load-balancer/load-balancer-custom-probe-overview.md). Endast en av dessa kan aktiveras åt gången. Programhälsotillägget eller belastningsutjämnaren avsöker den programslutpunkt som konfigurerats på instanser för virtuella datorer för att fastställa programmets hälsostatus. Den här hälsostatusen används av skalningsuppsättningen orchestrator för att övervaka instanshälsa och utföra reparationer när det behövs.

**Konfigurera slutpunkt för att ange hälsostatus**

Innan du aktiverar principen för automatisk instansreparationer bör du se till att skalningsuppsättningsinstanserna har programslutpunkten konfigurerad för att avge programmets hälsostatus. När en instans returnerar status 200 (OK) på den här programslutpunkten markeras instansen som "Felfri". I alla andra fall är instansen märkt "Felfritt", inklusive följande scenarier:

- När det inte finns någon programslutpunkt konfigurerad i instanserna för den virtuella datorn för att ge programmets hälsostatus
- När programslutpunkten är felaktigt konfigurerad
- När programslutpunkten inte kan nås

Till exempel markerade som "Ohälsosamt", utlöses automatiska reparationer av skalningsuppsättningen. Kontrollera att programslutpunkten är korrekt konfigurerad innan du aktiverar principen för automatiska reparationer för att undvika oavsiktliga instansreparationer, medan slutpunkten konfigureras.

**Aktivera enstaka placeringsgrupp**

Den här förhandsgranskningen är för närvarande endast tillgänglig för skalningsuppsättningar som distribueras som en enda placeringsgrupp. Egenskapen *singlePlacementGroup* ska vara true *för* att skalningsuppsättningen ska använda funktionen för automatiska instansreparationer. Läs mer om [placeringsgrupper](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**API-version**

Principen för automatiska reparationer stöds för beräknings-API-version 2018-10-01 eller senare.

**Begränsningar för resurs- eller prenumerationsflyttningar**

Som en del av den här förhandsversionen stöds inte resurs- eller prenumerationsflyttningar för skalningsuppsättningar när principen för automatiska reparationer är aktiverad.

**Begränsning för serviceinfrastrukturskalauppsättningar**

Den här förhandsgranskningsfunktionen stöds för närvarande inte för skalningsuppsättningar för serviceinfrastruktur.

## <a name="how-do-automatic-instance-repairs-work"></a>Hur fungerar automatiska instansreparationer?

Automatisk instansreparationsfunktion är beroende av hälsoövervakning av enskilda instanser i en skalningsuppsättning. VM-instanser i en skalningsuppsättning kan konfigureras för att avge programhälsostatus med hjälp av antingen [programhälsotillägget](./virtual-machine-scale-sets-health-extension.md) eller [belastningsutjämnadvägsavsökningarna](../load-balancer/load-balancer-custom-probe-overview.md). Om en instans visar sig vara fel, utför skalningsuppsättningen reparationsåtgärden genom att ta bort den felaktiga instansen och skapa en ny som ersätter den. Den här funktionen kan aktiveras i modellen för skalningsuppsättning för virtuella datorer med hjälp av det *automatiskaRepairsPolicy-objektet.*

### <a name="batching"></a>Batchbearbetning

De automatiska instansreparationsåtgärderna utförs i batchar. Vid varje given tidpunkt repareras inte mer än 5 % av instanserna i skalningsuppsättningen via principen för automatiska reparationer. Detta hjälper till att undvika samtidig borttagning och återskapande av ett stort antal instanser om det samtidigt befinns fel.

### <a name="grace-period"></a>Respitperiod

När en instans går igenom en tillståndsändringsåtgärd på grund av en PUT-, PATCH- eller POST-åtgärd som utförs på skalningsuppsättningen (till exempel omfördelning, omfördelning, uppdatering osv.), utförs alla reparationsåtgärder på den instansen först efter att ha väntat på respitperioden. Respitperiod är den tid som instansen kan återgå till felfritt tillstånd. Respitperioden börjar när tillståndsändringen har slutförts. Detta hjälper till att undvika för tidig eller oavsiktlig reparation. Respitperioden gäller för alla nyskapade instanser i skalningsuppsättningen (inklusive den som skapats som ett resultat av reparationsåtgärd). Respitperioden anges i minuter i ISO 8601-format och kan ställas in med hjälp av egenskapen *automaticRepairsPolicy.gracePeriod*. Respitperioden kan variera mellan 30 minuter och 90 minuter och har ett standardvärde på 30 minuter.

Den automatiska instansreparationsprocessen fungerar på följande sätt:

1. [Program Health extension](./virtual-machine-scale-sets-health-extension.md) eller [Load balancer health probes](../load-balancer/load-balancer-custom-probe-overview.md) ping application endpoint inside each virtual machine in the scale set to get application health status for each instance.
2. Om slutpunkten svarar med status 200 (OK) markeras instansen som "Felfri". I alla andra fall (inklusive om slutpunkten inte kan nås) är instansen märkt "Felfritt".
3. När en instans visar sig vara fel, utlöser skalningsuppsättningen en reparationsåtgärd genom att ta bort den felaktiga instansen och skapa en ny som ersätter den.
4. Instansreparationer utförs i batchar. Vid varje given tidpunkt repareras inte mer än 5 % av de totala instanserna i skalningsuppsättningen. Om en skalningsuppsättning har färre än 20 instanser görs reparationerna för en felinstans i taget.
5. Ovanstående process fortsätter tills alla felaktiga instanser i skalningsuppsättningen repareras.

## <a name="instance-protection-and-automatic-repairs"></a>Instansskydd och automatiska reparationer

Om en instans i en skalningsuppsättning skyddas genom att tillämpa *[skyddsprincipen Skydda mot skalningsuppsättningsåtgärder](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* utförs inte automatiska reparationer på den instansen.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Aktivera princip för automatiska reparationer när du skapar en ny skalningsuppsättning

För att aktivera principen för automatiska reparationer samtidigt som du skapar en ny skalningsuppsättning, se till att alla [krav](#requirements-for-using-automatic-instance-repairs) för att välja den här funktionen uppfylls. Programslutpunkten bör konfigureras korrekt för skalningsuppsättningsinstanser för att undvika att utlösa oavsiktliga reparationer medan slutpunkten konfigureras. För nyligen skapade skalningsuppsättningar utförs alla instansreparationer endast efter att ha väntat på respitperioden. Om du vill aktivera automatisk instansreparation i en skalningsuppsättning använder du *automatisktRepairsPolicy-objekt* i skaluppsättningsmodellen för den virtuella datorn.

### <a name="rest-api"></a>REST API

I följande exempel visas hur du aktiverar automatisk instansreparation i en skalningsuppsättningsmodell. Använd API-version 2018-10-01 eller senare.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Den automatiska instansreparationsfunktionen kan aktiveras när du skapar en ny skalningsuppsättning med hjälp av [cmdleten New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Det här exempelskriptet går igenom skapandet av en skalningsuppsättning och associerade resurser med hjälp av konfigurationsfilen: [Skapa en fullständig skalningsuppsättning för virtuella datorer](./scripts/powershell-sample-create-complete-scale-set.md). Du kan konfigurera principen för automatiska instansreparationer genom att lägga till parametrarna *EnableAutomaticRepair* och *AutomaticRepairGracePeriod* i konfigurationsobjektet för att skapa skalningsuppsättningen. I följande exempel aktiveras funktionen med en respitperiod på 30 minuter.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Aktivera princip för automatiska reparationer när du uppdaterar en befintlig skalningsuppsättning

Innan du aktiverar principen för automatiska reparationer i en befintlig skalningsuppsättning ska du se till att alla [krav](#requirements-for-using-automatic-instance-repairs) för att anmäla sig till den här funktionen uppfylls. Programslutpunkten bör konfigureras korrekt för skalningsuppsättningsinstanser för att undvika att utlösa oavsiktliga reparationer medan slutpunkten konfigureras. Om du vill aktivera automatisk instansreparation i en skalningsuppsättning använder du *automatisktRepairsPolicy-objekt* i skaluppsättningsmodellen för den virtuella datorn.

### <a name="rest-api"></a>REST API

I följande exempel aktiveras principen med respitperiod på 40 minuter. Använd API-version 2018-10-01 eller senare.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Använd [cmdleten Update-AzVmss](/powershell/module/az.compute/update-azvmss) för att ändra konfigurationen av automatisk instansreparationsfunktion i en befintlig skalningsuppsättning. I följande exempel uppdateras respitperioden till 40 minuter.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Felsöka

**Det gick inte att aktivera principen för automatiska reparationer**

Om du får felmeddelandet "BadRequest" med ett meddelande med texten "Det gick inte att hitta medlemmen 'automaticRepairsPolicy' på objekt av typen egenskaper'" kontrollerar du API-versionen som används för skalningsuppsättning för virtuella datorer. API-version 2018-10-01 eller senare krävs för den här funktionen.

**Instansen repareras inte ens när principen är aktiverad**

Instansen kan vara i respitperiod. Detta är den tid att vänta efter eventuella tillståndsändringar på instansen innan du utför reparationer. Detta för att undvika för tidig eller oavsiktlig reparation. Reparationsåtgärden ska utföras när respitperioden har slutförts för instansen.

**Visa programhälsostatus för skalningsuppsättningsinstanser**

Du kan använda [API:et](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) hämta instansvy för instanser i en skala för virtuell dator för att visa programmets hälsostatus. Med Azure PowerShell kan du använda cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) med flaggan *-InstanceView.* Programmets hälsostatus anges under egenskapen *vmHealth*.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar [programhälsotillägg](./virtual-machine-scale-sets-health-extension.md) eller [belastningsutjämnare hälsoavsökningar](../load-balancer/load-balancer-custom-probe-overview.md) för dina skalningsuppsättningar.
