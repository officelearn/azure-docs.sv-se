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
ms.openlocfilehash: 8e73ef75b3313656b45d29270d9996c3ad17c630
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538077"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatiska instansreparationer för Azure-skalningsuppsättningar för virtuella datorer

Aktivera automatiska instansreparationer för Azure-skalningsuppsättningar för virtuella datorer bidrar till att uppnå hög tillgänglighet för program genom att underhålla en uppsättning felfria instanser. Om en instans i skalningsuppsättningen visar sig vara felaktig som rapporterats av [Programhälsotillägg](./virtual-machine-scale-sets-health-extension.md) eller [belastningsutjämnad hälsoavsökningar,](../load-balancer/load-balancer-custom-probe-overview.md)utför den här funktionen automatiskt instansreparation genom att ta bort den felaktiga instansen och skapa en ny som ersätter den.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Krav för att använda automatiska instansreparationer

**Aktivera hälsoövervakning av program för skalningsuppsättning**

Skalningsuppsättningen bör ha programhälsoövervakning för instanser aktiverade. Detta kan göras med hjälp av antingen [Program Health extension](./virtual-machine-scale-sets-health-extension.md) eller Load [Balancer hälsa sonder](../load-balancer/load-balancer-custom-probe-overview.md). Endast en av dessa kan aktiveras åt gången. Programhälsotillägget eller belastningsutjämnaren avsöker den programslutpunkt som konfigurerats på instanser för virtuella datorer för att fastställa programmets hälsostatus. Den här hälsostatusen används av skalningsuppsättningen orchestrator för att övervaka instanshälsa och utföra reparationer när det behövs.

**Konfigurera slutpunkt för att ange hälsostatus**

Innan du aktiverar principen för automatisk instansreparationer bör du se till att skalningsuppsättningsinstanserna har programslutpunkten konfigurerad för att avge programmets hälsostatus. När en instans returnerar status 200 (OK) på den här programslutpunkten markeras instansen som "Felfri". I alla andra fall är instansen märkt "Felfritt", inklusive följande scenarier:

- När det inte finns någon programslutpunkt konfigurerad i instanserna för den virtuella datorn för att ge programmets hälsostatus
- När programslutpunkten är felaktigt konfigurerad
- När programslutpunkten inte kan nås

Till exempel markerade som "Ohälsosamt", utlöses automatiska reparationer av skalningsuppsättningen. Kontrollera att programslutpunkten är korrekt konfigurerad innan du aktiverar principen för automatiska reparationer för att undvika oavsiktliga instansreparationer, medan slutpunkten konfigureras.

**Aktivera enstaka placeringsgrupp**

Den här funktionen är för närvarande endast tillgänglig för skalningsuppsättningar som distribueras som en enda placeringsgrupp. Egenskapen *singlePlacementGroup* ska vara true *för* att skalningsuppsättningen ska använda funktionen för automatiska instansreparationer. Läs mer om [placeringsgrupper](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**API-version**

Principen för automatiska reparationer stöds för beräknings-API-version 2018-10-01 eller senare.

**Begränsningar för resurs- eller prenumerationsflyttningar**

Resurs- eller prenumerationsflyttningar stöds för närvarande inte för skalningsuppsättningar när funktionen för automatiska reparationer är aktiverad.

**Begränsning för serviceinfrastrukturskalauppsättningar**

Den här funktionen stöds för närvarande inte för serviceinfrastrukturskalningsuppsättningar.

## <a name="how-do-automatic-instance-repairs-work"></a>Hur fungerar automatiska instansreparationer?

Automatisk instansreparationsfunktion är beroende av hälsoövervakning av enskilda instanser i en skalningsuppsättning. VM-instanser i en skalningsuppsättning kan konfigureras för att avge programhälsostatus med hjälp av antingen [programhälsotillägget](./virtual-machine-scale-sets-health-extension.md) eller [belastningsutjämnadvägsavsökningarna](../load-balancer/load-balancer-custom-probe-overview.md). Om en instans visar sig vara fel, utför skalningsuppsättningen reparationsåtgärden genom att ta bort den felaktiga instansen och skapa en ny som ersätter den. Den senaste skaluppsättningen för virtuella datorer används för att skapa den nya instansen. Den här funktionen kan aktiveras i modellen för skalningsuppsättning för virtuella datorer med hjälp av det *automatiskaRepairsPolicy-objektet.*

### <a name="batching"></a>Batchbearbetning

De automatiska instansreparationsåtgärderna utförs i batchar. Vid varje given tidpunkt repareras inte mer än 5 % av instanserna i skalningsuppsättningen via principen för automatiska reparationer. Detta hjälper till att undvika samtidig borttagning och återskapande av ett stort antal instanser om det samtidigt befinns fel.

### <a name="grace-period"></a>Respitperiod

När en instans går igenom en tillståndsändringsåtgärd på grund av en PUT-, PATCH- eller POST-åtgärd som utförs på skalningsuppsättningen (till exempel omfördelning, omfördelning, uppdatering osv.), utförs alla reparationsåtgärder på den instansen först efter att ha väntat på respitperioden. Respitperiod är den tid som instansen kan återgå till felfritt tillstånd. Respitperioden börjar när tillståndsändringen har slutförts. Detta hjälper till att undvika för tidig eller oavsiktlig reparation. Respitperioden gäller för alla nyskapade instanser i skalningsuppsättningen (inklusive den som skapats som ett resultat av reparationsåtgärd). Respitperioden anges i minuter i ISO 8601-format och kan ställas in med hjälp av egenskapen *automaticRepairsPolicy.gracePeriod*. Respitperioden kan variera mellan 30 minuter och 90 minuter och har ett standardvärde på 30 minuter.

### <a name="suspension-of-repairs"></a>Avstängning av reparationer 

Skalningsuppsättningar för virtuella datorer ger möjlighet att tillfälligt pausa automatiska instansreparationer om det behövs. *ServiceState* för automatiska reparationer under *egenskapsorkestreringServices* i instansvy av skaluppsättningen för virtuella datorer visar det aktuella tillståndet för de automatiska reparationerna. När en skalningsuppsättning har valts till automatiska reparationer anges värdet för *parameterserviceState* till *Kör*. När de automatiska reparationerna pausas för en skalningsuppsättning anges *parameterserviceState* till *Suspenderad*. Om *automatiskRepairsPolicy* definieras i en skalningsuppsättning men funktionen för automatiska reparationer inte är aktiverad, är *parameterserviceState* inställd på *Kör inte*.

Om nyligen skapade instanser för att ersätta de felaktiga i en skalningsuppsättning fortsätter att vara ohälsosamma även efter upprepade reparationsåtgärder, uppdaterar plattformen *serviceState* som en säkerhetsåtgärd för automatiska reparationer till *Pausad*. Du kan återuppta de automatiska reparationerna igen genom att ange värdet för *serviceState* för automatiska reparationer till *Löpning*. Detaljerade instruktioner finns i avsnittet om [visning och uppdatering av servicetillståndet](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) för principen för automatiska reparationer för din skalningsuppsättning. 

Den automatiska instansreparationsprocessen fungerar på följande sätt:

1. [Program Health extension](./virtual-machine-scale-sets-health-extension.md) eller [Load balancer health probes](../load-balancer/load-balancer-custom-probe-overview.md) ping application endpoint inside each virtual machine in the scale set to get application health status for each instance.
2. Om slutpunkten svarar med status 200 (OK) markeras instansen som "Felfri". I alla andra fall (inklusive om slutpunkten inte kan nås) är instansen märkt "Felfritt".
3. När en instans visar sig vara fel, utlöser skalningsuppsättningen en reparationsåtgärd genom att ta bort den felaktiga instansen och skapa en ny som ersätter den.
4. Instansreparationer utförs i batchar. Vid varje given tidpunkt repareras inte mer än 5 % av de totala instanserna i skalningsuppsättningen. Om en skalningsuppsättning har färre än 20 instanser görs reparationerna för en felinstans i taget.
5. Ovanstående process fortsätter tills alla felaktiga instanser i skalningsuppsättningen repareras.

## <a name="instance-protection-and-automatic-repairs"></a>Instansskydd och automatiska reparationer

Om en instans i en skalningsuppsättning skyddas genom att tillämpa en av [skyddsprinciperna](./virtual-machine-scale-sets-instance-protection.md)utförs inte automatiska reparationer på den instansen. Detta gäller både skyddsprinciper: *Skydda mot skalning* och *Skydda mot skalningsuppsättningsåtgärder.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Avsluta meddelande och automatiska reparationer

Om [funktionen för avsluta meddelande](./virtual-machine-scale-sets-terminate-notification.md) är aktiverad på en skalningsuppsättning följer borttagningen av en felaktig instans när en felaktig instans tas bort. Ett avsluta meddelande skickas via Azure metadata service – schemalagda händelser – och instansborttagning försenas under den tid då den konfigurerade fördröjningstidsgränsen. Skapandet av en ny instans för att ersätta den felaktiga väntar dock inte på att fördröjningstiden ska slutföras.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Aktivera princip för automatiska reparationer när du skapar en ny skalningsuppsättning

För att aktivera principen för automatiska reparationer samtidigt som du skapar en ny skalningsuppsättning, se till att alla [krav](#requirements-for-using-automatic-instance-repairs) för att välja den här funktionen uppfylls. Programslutpunkten bör konfigureras korrekt för skalningsuppsättningsinstanser för att undvika att utlösa oavsiktliga reparationer medan slutpunkten konfigureras. För nyligen skapade skalningsuppsättningar utförs alla instansreparationer endast efter att ha väntat på respitperioden. Om du vill aktivera automatisk instansreparation i en skalningsuppsättning använder du *automatisktRepairsPolicy-objekt* i skaluppsättningsmodellen för den virtuella datorn.

### <a name="azure-portal"></a>Azure Portal
 
Följande steg som aktiverar principen för automatiska reparationer när du skapar en ny skalningsuppsättning.
 
1. Gå till **skalningsuppsättningar för virtuella datorer**.
1. Välj **+ Lägg till** om du vill skapa en ny skaluppsättning.
1. Gå till fliken **Hälsa.** 
1. Leta reda på avsnittet **Hälsa.**
1. Aktivera **hälsoalternativet Övervaka programmets hälsa.**
1. Leta reda på avsnittet **Automatisk reparationsprincip.**
1. Aktivera **On** alternativet **Automatiska reparationer.**
1. I **Respitperiod (min)** anger du respitperioden i minuter, tillåtna värden är mellan 30 och 90 minuter. 
1. När du är klar med att skapa den nya skalningsuppsättningen väljer du **knappen Granska + skapa.**

### <a name="rest-api"></a>REST-API

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

I följande exempel kan principen för automatiska reparationer skapas när du skapar en ny skalningsuppsättning med *[az vmss create](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)*. Skapa först en resursgrupp och skapa sedan en ny skalningsuppsättning med automatiska reparationer princip respitperiod inställd på 30 minuter.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-period 30
```

I exemplet ovan används en befintlig belastningsutjämnare och hälsoavsökning för övervakning av programhälsostatus för instanser. Om du föredrar att använda ett programhälsotillägg för övervakning i stället kan du skapa en skalningsuppsättning, konfigurera programhälsotillägget och sedan aktivera principen för automatisk instansreparationer med hjälp av *uppdateringen av az vmss*, som förklaras i nästa avsnitt.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Aktivera princip för automatiska reparationer när du uppdaterar en befintlig skalningsuppsättning

Innan du aktiverar principen för automatiska reparationer i en befintlig skalningsuppsättning ska du se till att alla [krav](#requirements-for-using-automatic-instance-repairs) för att anmäla sig till den här funktionen uppfylls. Programslutpunkten bör konfigureras korrekt för skalningsuppsättningsinstanser för att undvika att utlösa oavsiktliga reparationer medan slutpunkten konfigureras. Om du vill aktivera automatisk instansreparation i en skalningsuppsättning använder du *automatisktRepairsPolicy-objekt* i skaluppsättningsmodellen för den virtuella datorn.

När du har uppdaterat modellen för en befintlig skalningsuppsättning kontrollerar du att den senaste modellen tillämpas på alla instanser av skalan. Se instruktionen om [hur du gör virtuella datorer uppdaterade med den senaste skalningsuppsättningsmodellen](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Azure Portal

Du kan ändra principen för automatiska reparationer för en befintlig skalningsuppsättning via Azure-portalen. 
 
1. Gå till en befintlig skalningsuppsättning för virtuella datorer.
1. Under **Inställningar** i menyn till vänster väljer du **Hälsa och reparation**.
1. Aktivera **hälsoalternativet Övervaka programmets hälsa.**
1. Leta reda på avsnittet **Automatisk reparationsprincip.**
1. Aktivera **On** alternativet **Automatiska reparationer.**
1. I **Respitperiod (min)** anger du respitperioden i minuter, tillåtna värden är mellan 30 och 90 minuter. 
1. När du är klar väljer du **Spara**. 

### <a name="rest-api"></a>REST-API

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

Följande är ett exempel på uppdatering av principen för automatisk instansreparationer för en befintlig skalningsuppsättning med hjälp av *[uppdatering av az vmss](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Visa och uppdatera servicetillståndet för principen för automatiska instansreparationer

### <a name="rest-api"></a>REST-API 

Använd [Hämta instansvy](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) med API-version 2019-12-01 eller senare för skala för virtuella datorer för att visa *serviceState* för automatiska reparationer under *egenskapsorkestreringServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Använd *setOrchestrationServiceState* API med API-version 2019-12-01 eller senare på en virtuell datorskala inställd på att ställa in tillståndet för automatiska reparationer. När skalningsuppsättningen har valts in i funktionen för automatiska reparationer kan du använda det här API:et för att pausa eller återuppta automatiska reparationer för skalningsuppsättningen. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

Använd [get-instance-view cmdlet](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) för att visa *serviceState* för automatiska instansreparationer. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Använd [cmdlet för set-orchestration-service-state](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) för att uppdatera *serviceState* för automatiska instansreparationer. När skalningsuppsättningen har valts in i den automatiska reparationsfunktionen kan du använda den här cmdleten för att pausa eller återuppta automatiska reparationer för din skalningsuppsättning. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Använd [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) cmdlet med *parametern InstanceView* för att visa *ServiceState* för automatiska instansreparationer.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Använd Set-AzVmssOrchestrationServiceState cmdlet för att uppdatera *serviceState* för automatiska instansreparationer. När skalningsuppsättningen har valts in i den automatiska reparationsfunktionen kan du använda den här cmdleten för att pausa eller återuppta automatiska reparationer för din skalningsuppsättning.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Felsöka

**Det gick inte att aktivera principen för automatiska reparationer**

Om du får felmeddelandet "BadRequest" med ett meddelande med texten "Det gick inte att hitta medlemmen 'automaticRepairsPolicy' på objekt av typen egenskaper'" kontrollerar du API-versionen som används för skalningsuppsättning för virtuella datorer. API-version 2018-10-01 eller senare krävs för den här funktionen.

**Instansen repareras inte ens när principen är aktiverad**

Instansen kan vara i respitperiod. Detta är den tid att vänta efter eventuella tillståndsändringar på instansen innan du utför reparationer. Detta för att undvika för tidig eller oavsiktlig reparation. Reparationsåtgärden ska utföras när respitperioden har slutförts för instansen.

**Visa programhälsostatus för skalningsuppsättningsinstanser**

Du kan använda [API:et](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) hämta instansvy för instanser i en skala för virtuell dator för att visa programmets hälsostatus. Med Azure PowerShell kan du använda cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) med flaggan *-InstanceView.* Programmets hälsostatus anges under egenskapen *vmHealth*.

I Azure-portalen kan du också se hälsostatus. Gå till en befintlig skalningsuppsättning, välj **Instanser** från menyn till vänster och titta i kolumnen **Hälsotillstånd** för hälsostatus för varje skalningsuppsättningsinstans. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar [programhälsotillägg](./virtual-machine-scale-sets-health-extension.md) eller [belastningsutjämnare hälsoavsökningar](../load-balancer/load-balancer-custom-probe-overview.md) för dina skalningsuppsättningar.
