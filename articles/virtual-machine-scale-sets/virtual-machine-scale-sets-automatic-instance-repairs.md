---
title: Automatisk instans reparation med skalnings uppsättningar för virtuella Azure-datorer
description: Lär dig hur du konfigurerar princip för automatisk reparation för VM-instanser i en skalnings uppsättning
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274819"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Preview: automatiska instans reparationer för skalnings uppsättningar för virtuella Azure-datorer

Om du aktiverar automatiska instans reparationer för skalnings uppsättningar för virtuella Azure-datorer kan du få hög tillgänglighet för program genom att underhålla en uppsättning felfria instanser. Om en instans i skalnings uppsättningen inte är felfri som rapporteras av hälso tillstånds [tillägg för program](./virtual-machine-scale-sets-health-extension.md) eller hälso [avsökningar för belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md), utför den här funktionen automatiskt instans reparation genom att ta bort den felaktiga instansen och skapa en ny för att ersätta den.

> [!NOTE]
> Den här förhands gransknings funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Krav för att använda automatiska instans reparationer

**Välj för hands versionen av automatisk instans reparation**

Använd antingen REST API eller Azure PowerShell för att välja automatisk instans reparen Preview. De här stegen registrerar din prenumeration för för hands versions funktionen. OBS! detta är bara en engångs inställning som krävs för att använda den här funktionen. Om din prenumeration redan är registrerad för för hands versionen av automatisk instans reparation behöver du inte registrera igen. 

Använda REST-API:et 

1. Registrera dig för funktionen med hjälp av [funktioner-registrera](/rest/api/resources/features/register) 

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

2. Vänta några minuter tills *statusen* har ändrats till *registrerad*. Du kan använda följande API för att bekräfta detta.

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

3. När *statusen* har ändrats till *registrerad*kan du köra följande.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Använda Azure PowerShell

1. Registrera dig för funktionen med cmdlet [register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) följt av [register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Vänta några minuter tills *RegistrationState* har ändrats till *registrerad*. Du kan använda följande cmdlet för att bekräfta detta.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 Svaret bör vara följande:

| FeatureName                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Registrerad              |

3. När *RegistrationState* har ändrats till *registrerad*, kör du följande cmdlet.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Aktivera övervakning av program hälsa för skalnings uppsättning**

Skalnings uppsättningen ska ha program hälso övervakning för aktiverade instanser. Detta kan göras med hjälp av antingen [program hälso tillägg](./virtual-machine-scale-sets-health-extension.md) eller [hälso avsökningar för belastnings utjämning](../load-balancer/load-balancer-custom-probe-overview.md). Endast ett av dessa kan aktive ras åt gången. Program hälso tillägget eller belastningsutjämnaren avsöker pinga program slut punkten som kon figurer ATS på virtuella dator instanser för att fastställa programmets hälso status. Denna hälso status används av skalnings uppsättningens Orchestrator för att övervaka instans hälsa och utföra reparationer vid behov.

**Konfigurera slut punkten för att ange hälso status**

Innan du aktiverar principen för automatiska reparationer av instanser måste du kontrol lera att skalnings uppsättnings instanserna har program slut punkten konfigurerad för att generera programmets hälso status. När en instans returnerar status 200 (OK) för den här program slut punkten markeras instansen som "felfri". I alla andra fall markeras instansen som "skadad", inklusive följande scenarier:

- När ingen program slut punkt har kon figurer ATS i de virtuella dator instanserna för att tillhandahålla program hälso status
- När program slut punkten är felaktigt konfigurerad
- När det inte går att komma åt program slut punkten

För instanser som har marker ATS som "ohälsosam" utlöses automatiska reparationer av skalnings uppsättningen. Se till att program slut punkten är korrekt konfigurerad innan du aktiverar principen för automatisk reparation för att undvika oavsiktliga instans reparationer, medan slut punkten konfigureras.

**Aktivera enskild placerings grupp**

Den här för hands versionen är för närvarande endast tillgänglig för skalnings uppsättningar som distribueras som en enda placerings grupp. Egenskapen *singlePlacementGroup* ska anges till *Sant* för att din skalnings uppsättning ska kunna använda funktionen Automatiska reparations reparationer. Lär dig mer om [placerings grupper](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**API-version**

Princip för automatisk reparation stöds för Compute API version 2018-10-01 eller senare.

**Begränsningar för flytt av resurser eller prenumeration**

Som en del av den här förhands granskningen kan inte resurser eller prenumerationer flyttas för skalnings uppsättningar när principen för automatisk reparation är aktive rad.

**Begränsning för Service Fabric Scale Sets**

Den här förhands gransknings funktionen stöds för närvarande inte för Service Fabric Scale set.

## <a name="how-do-automatic-instance-repairs-work"></a>Hur fungerar automatisk reparation av instanser?

Funktionen reparera automatisk instans använder hälso övervakning av enskilda instanser i en skalnings uppsättning. Virtuella dator instanser i en skalnings uppsättning kan konfigureras för att generera program hälso status med antingen [program hälso tillägg](./virtual-machine-scale-sets-health-extension.md) eller [hälso avsökningar för belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md). Om en instans är ohälsosam, utför skalnings uppsättningen reparations åtgärden genom att ta bort den felaktiga instansen och skapa en ny för att ersätta den. Den här funktionen kan aktive ras i den virtuella datorns skalnings uppsättnings modell med hjälp av *automaticRepairsPolicy* -objektet.

### <a name="batching"></a>Batchbearbetning

Reparations åtgärder för automatisk instans utförs i batchar. Vid en angiven tidpunkt repare ras inte mer än 5% av instanserna i skalnings uppsättningen genom principen för automatisk reparation. Detta bidrar till att undvika Samtidig borttagning och att skapa ett stort antal instanser på nytt, om det upptäcks dåligt på samma gång.

### <a name="grace-period"></a>Respitperiod

När en instans går igenom en status ändrings åtgärd på grund av en skicka-, KORRIGERINGs-eller POST-åtgärd som utförs på skalnings uppsättningen (till exempel avbildning, omdistribution, uppdatering osv.), utförs eventuell reparations åtgärd på den instansen först efter att du har väntat på respitperioden. Respitperiod är hur lång tid som tillåts för instansen att återgå till felfritt tillstånd. Grace-perioden startar efter att tillstånds ändringen har slutförts. Detta bidrar till att undvika oförutsedda eller oavsiktliga reparations åtgärder. Respittiden gäller för alla nyskapade instanser i skalnings uppsättningen (inklusive den som skapats som ett resultat av reparations åtgärden). Grace-perioden anges i minuter i ISO 8601-format och kan anges med egenskapen *automaticRepairsPolicy. gracePeriod*. Grace-perioden kan vara mellan 30 minuter och 90 minuter och har ett standardvärde på 30 minuter.

Processen för automatisk instans reparation fungerar på följande sätt:

1. [Program hälso tillägg](./virtual-machine-scale-sets-health-extension.md) eller [hälso avsökningar för belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md) pingar program slut punkten i varje virtuell dator i skalnings uppsättningen för att få program hälso status för varje instans.
2. Om slut punkten svarar med statusen 200 (OK) markeras instansen som "felfri". I alla andra fall (inklusive om slut punkten inte kan komma åt) är instansen markerad som "ej felfri".
3. När en instans har visat sig vara ohälsosam, utlöser skalnings uppsättningen en reparations åtgärd genom att ta bort den felaktiga instansen och skapa en ny för att ersätta den.
4. Instans reparationer utförs i batchar. Vid en angiven tidpunkt repare ras inte fler än 5% av de totala instanserna i skalnings uppsättningen. Om en skalnings uppsättning har färre än 20 instanser görs reparationen för en skadad instans i taget.
5. Ovanstående process fortsätter tills all skadad instans i skalnings uppsättningen har reparerats.

## <a name="instance-protection-and-automatic-repairs"></a>Instans skydd och automatiska reparationer

Om en instans i en skalnings uppsättning skyddas genom att använda *[skydds principen skydda från skalnings uppsättning](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* , utförs inte automatiska reparationer på den instansen.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Aktivera automatisk reparations princip när du skapar en ny skalnings uppsättning

För att aktivera principen för automatisk reparation när du skapar en ny skalnings uppsättning, se till att alla [krav](#requirements-for-using-automatic-instance-repairs) för väljer i den här funktionen är uppfyllda. Program slut punkten måste vara korrekt konfigurerad för skalnings uppsättnings instanser för att undvika att oönskade reparationer utlöses när slut punkten konfigureras. Vid nyskapade skalnings uppsättningar utförs alla instans reparationer endast efter att ha väntat på giltighets perioden. Om du vill aktivera reparation av den automatiska instansen i en skalnings uppsättning använder du *automaticRepairsPolicy* -objekt i den virtuella datorns skal uppsättnings modell.

### <a name="rest-api"></a>REST-API

I följande exempel visas hur du aktiverar automatisk instans reparation i en skalnings uppsättnings modell. Använd API version 2018-10-01 eller senare.

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

Funktionen reparera automatisk instans kan aktive ras när du skapar en ny skalnings uppsättning med hjälp av cmdleten [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . Det här exempel skriptet går igenom skapandet av en skalnings uppsättning och associerade resurser med hjälp av konfigurations filen: [skapa en fullständig skalnings uppsättning för virtuella datorer](./scripts/powershell-sample-create-complete-scale-set.md). Du kan konfigurera principen för automatiska reparationer av instanser genom att lägga till parametrarna *EnableAutomaticRepair* och *AutomaticRepairGracePeriod* till konfigurationsobjektet för att skapa skalnings uppsättningen. I följande exempel aktive ras funktionen med en Grace-period på 30 minuter.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Aktivera automatisk reparations princip när du uppdaterar en befintlig skalnings uppsättning

Innan du aktiverar principen för automatisk reparation i en befintlig skalnings uppsättning måste du se till att alla [krav](#requirements-for-using-automatic-instance-repairs) för väljer i den här funktionen är uppfyllda. Program slut punkten måste vara korrekt konfigurerad för skalnings uppsättnings instanser för att undvika att oönskade reparationer utlöses när slut punkten konfigureras. Om du vill aktivera reparation av den automatiska instansen i en skalnings uppsättning använder du *automaticRepairsPolicy* -objekt i den virtuella datorns skal uppsättnings modell.

### <a name="rest-api"></a>REST-API

I följande exempel aktive ras principen med en Grace-period på 40 minuter. Använd API version 2018-10-01 eller senare.

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

Använd cmdleten [Update-AzVmss](/powershell/module/az.compute/update-azvmss) för att ändra konfigurationen av funktionen reparera automatiska instanser i en befintlig skalnings uppsättning. I följande exempel uppdateras Grace-perioden till 40 minuter.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Felsöka

**Det gick inte att aktivera principen för automatisk reparation**

Om du får ett "BadRequest"-fel med meddelandet "Det gick inte att hitta medlemmen" automaticRepairsPolicy "för objekt av typen" Properties "", kontrollerar du den API-version som används för skalnings uppsättningen för den virtuella datorn. API version 2018-10-01 eller senare krävs för den här funktionen.

**Instansen kan inte repare ras även när principen är aktive rad**

Instansen kan vara i Grace-period. Detta är vänte tiden efter en tillstånds ändring på instansen innan reparationen utförs. Detta är för att undvika oförutsedda eller oavsiktliga reparationer. Reparations åtgärden bör utföras när Grace-perioden har slutförts för instansen.

**Visa program hälso status för skalnings uppsättnings instanser**

Du kan använda [Hämta instans Visa API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) för instanser i en skalnings uppsättning för virtuella datorer för att Visa programmets hälso status. Med Azure PowerShell kan du använda cmdleten [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) med flaggan *-InstanceView* . Programmets hälso status anges under egenskapen *vmHealth*.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar [program hälso tillägg](./virtual-machine-scale-sets-health-extension.md) eller [hälso avsökningar för belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md) för dina skalnings uppsättningar.
