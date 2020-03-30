---
title: Avsluta meddelande för Azure-skalningsuppsättningsinstanser för virtuella datorer
description: Lär dig hur du aktiverar uppsägningsmeddelanden för Azure-skalningsuppsättningsinstanser för virtuella datorer
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250756"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Avsluta meddelande för Azure-skalningsuppsättningsinstanser för virtuella datorer
Skala uppsättningsinstanser kan välja att ta emot instansavslutningsmeddelanden och ange en fördefinierad fördröjningstimeout till avsluta-åtgärden. Uppsägningsmeddelandet skickas via Azure Metadata Service – [Schemalagda händelser](../virtual-machines/windows/scheduled-events.md), som tillhandahåller meddelanden för och fördröjning av effektfulla åtgärder som omstarter och omfördelning. Lösningen lägger till en annan händelse – Avsluta – i listan över schemalagda händelser och den associerade fördröjningen för avsluta-händelsen beror på fördröjningsgränsen som anges av användare i deras skalningsuppsättningsmodellkonfigurationer.

När du har registrerat dig i funktionen behöver skalningsuppsättningsinstanser inte vänta på att den angivna tidsgränsen upphör att gälla innan instansen tas bort. När du har fått ett avsluta meddelande kan instansen välja att tas bort när som helst innan tidsgränsen för avslutad upphör att gälla.

## <a name="enable-terminate-notifications"></a>Aktivera avsluta meddelanden
Det finns flera sätt att aktivera uppsägningsmeddelanden på dina skalningsuppsättningsinstanser, som beskrivs i exemplen nedan.

### <a name="azure-portal"></a>Azure Portal

Följande steg aktiverar avsluta meddelande när du skapar en ny skalningsuppsättning. 

1. Gå till **skalningsuppsättningar för virtuella datorer**.
1. Välj **+ Lägg till** om du vill skapa en ny skaluppsättning.
1. Gå till fliken **Hantering.** 
1. Leta reda på avsnittet **Instansavslutning.**
1. Välj **Instance termination notification** **På**.
1. För **fördröjning (minuter)** anger du önskad standardtidsutgång.
1. När du är klar med att skapa den nya skalningsuppsättningen väljer du **knappen Granska + skapa.** 

> [!NOTE]
> Det går inte att ange avsluta meddelanden på befintliga skalningsuppsättningar i Azure-portalen

### <a name="rest-api"></a>REST API

I följande exempel kan du avsluta meddelanden på skalningsuppsättningsmodellen.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

Ovanstående block anger en timeout-fördröjning på 5 minuter (som indikeras av *PT5M)* för alla avsluta åtgärder för alla instanser i din skalningsuppsättning. Fältet *inteFörutgång* kan ta valfritt värde mellan 5 och 15 minuter i ISO 8601-format. Du kan ändra standardtidsgränsen för avslutad åtgärd genom att ändra egenskapen *notBeforeTimeout* under *terminateNotificationProfile* som beskrivs ovan.

När du har aktiverat *scheduledEventsProfile* på skalningsuppsättningsmodellen och ställt in *notBeforeTimeout*uppdaterar du de enskilda instanserna till den [senaste modellen](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) för att återspegla ändringarna.

> [!NOTE]
>Avsluta meddelanden på skalningsuppsättningsinstanser kan endast aktiveras med API version 2019-03-01 och högre

### <a name="azure-powershell"></a>Azure PowerShell
När du skapar en ny skalningsuppsättning kan du aktivera avslutningsmeddelanden på skalan som angetts med hjälp av cmdleten [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

Det här exempelskriptet går igenom skapandet av en skalningsuppsättning och associerade resurser med hjälp av konfigurationsfilen: [Skapa en fullständig skalningsuppsättning för virtuella datorer](./scripts/powershell-sample-create-complete-scale-set.md). Du kan tillhandahålla konfigurera avsluta meddelande genom att lägga till parametrarna *TerminateScheduledEvents* och *TerminateScheduledEventNotBeforeTimeoutInMinutes* till konfigurationsobjektet för att skapa skalningsuppsättning. I följande exempel aktiveras funktionen med en fördröjningstidsutgång på 10 minuter.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Använd [cmdleten Update-AzVmss](/powershell/module/az.compute/update-azvmss) för att aktivera avslutningsmeddelanden i en befintlig skalningsuppsättning.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
I exemplet ovan kan du avsluta meddelanden på en befintlig skalningsuppsättning och ange en 15-minuters timeout för avsluta-händelsen.

När du har aktiverat schemalagda händelser på skalningsuppsättningsmodellen och ställt in timeouten uppdaterar du de enskilda instanserna till den [senaste modellen](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) för att återspegla ändringarna.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Följande exempel är för att aktivera uppsägningsmeddelande när du skapar en ny skalningsuppsättning.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

Exemplet ovan skapar först en resursgrupp och skapar sedan en ny skalningsuppsättning med avsluta-meddelanden aktiverade för en 10-minuters standardtidsutgång.

Följande exempel är för att aktivera uppsägningsmeddelanden i en befintlig skalningsuppsättning.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Få avsluta meddelanden

Avsluta meddelanden levereras via [schemalagda händelser](../virtual-machines/windows/scheduled-events.md), som är en Azure Metadata Service. Azure Metadata-tjänsten visar information om hur du kör virtuella datorer med hjälp av en REST-slutpunkt som är tillgänglig inifrån den virtuella datorn. Informationen är tillgänglig via en icke-dirigerbar IP så att den inte exponeras utanför den virtuella datorn.

Schemalagda händelser är aktiverade för din skalningsuppsättning första gången du gör en begäran om händelser. Du kan förvänta dig ett försenat svar i ditt första samtal på upp till två minuter. Fråga slutpunkten regelbundet för att identifiera kommande underhållshändelser och status för pågående underhållsaktiviteter.

Schemalagda händelser är inaktiverat för skalningsuppsättningen om skalningsuppsättningsinstanserna inte gör en begäran på 24 timmar.

### <a name="endpoint-discovery"></a>Identifiering av slutpunkt
För virtuella datorer medaktiverad VNET är metadatatjänsten tillgänglig från en statisk icke-dirigerbar IP, 169.254.169.254.

Den fullständiga slutpunkten för den senaste versionen av schemalagda händelser är:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Frågesvar
Ett svar innehåller en matris med schemalagda händelser. En tom matris innebär att det för närvarande inte finns några schemalagda händelser.

Om det finns schemalagda händelser innehåller svaret en matris med händelser. För en "Avsluta"-händelse ser svaret ut på följande sätt:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*DocumentInkarnationen* är en ETag och är ett enkelt sätt att kontrollera om nyttolasten Händelser har ändrats sedan den senaste frågan.

Mer information om vart och ett av fälten ovan finns i dokumentationen schemalagda händelser för [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) och [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Svara på händelser
När du har lärt dig om en kommande händelse och slutfört din logik för graciös avstängning kan du godkänna den utestående händelsen genom att ringa ett POST-anrop till metadatatjänsten med EventId. POST-anropet anger för Azure att det kan fortsätta med borttagning av virtuella datorer.

Nedan är json förväntas i POST begäran kroppen. Begäran bör innehålla en lista över StartRequests. Varje StartRequest innehåller EventId för den händelse som du vill påskynda:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Se till att alla virtuella datorer i skalningsuppsättningen endast godkänner det EventID som endast är relevant för den virtuella datorn. En virtuell dator kan få sitt eget VM-namn [via instansmetadata](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Det här namnet har formen {scale-set-name}_{instance-id}och visas i avsnittet Resurser i frågesvaret som beskrivs ovan.

Du kan också referera till exempelskript för att fråga och svara på händelser med [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) och [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Tips och regelverk
-   Avsluta meddelanden endast för "ta bort" åtgärder – Alla borttagningsåtgärder (manuell borttagning eller Automatisk skalningsinitierad skalning) genererar Avsluta händelser om skalningsuppsättningen har *aktiverat SchemalagdEventsProfile.* Andra åtgärder som omstart, ominmatning, omfördelning och stopp/deallocate genererar inte Avsluta händelser. Avsluta meddelanden kan inte aktiveras för virtuella datorer med låg prioritet.
-   Ingen obligatorisk väntetid för timeout – Du kan starta avsluta åtgärden när som helst efter att händelsen har tagits emot och innan händelsen inte *är innan* tiden går ut.
-   Obligatorisk borttagning vid timeout – Det finns ingen möjlighet att utöka timeout-värdet när en händelse har genererats. När tidsgränsen går ut bearbetas den väntande avslutningshändelsen och den virtuella datorn tas bort.
-   Ändringsbart timeout-värde – Du kan ändra timeout-värdet när som helst innan en instans tas bort, genom att ändra egenskapen *notBeforeTimeout* på skalningsuppsättningsmodellen och uppdatera VM-instanserna till den senaste modellen.
-   Godkänn alla väntande borttagningar – Om det finns en väntande borttagning på VM_1 som inte har godkänts och du har godkänt en annan avslutad händelse på VM_2 tas VM_2 inte bort förrän avslutningshändelsen för VM_1 har godkänts eller dess timeout har förflutit. När du har godkänt avsluta händelsen för VM_1 tas både VM_1 och VM_2 bort.
-   Godkänn alla samtidiga borttagningar – Utöka exemplet ovan, om VM_1 och VM_2 har samma *NotBefore-tid,* måste båda avsluta händelser godkännas eller ingen av virtuella datorn tas bort innan tidsgränsen upphör att gälla.

## <a name="troubleshoot"></a>Felsöka
### <a name="failure-to-enable-scheduledeventsprofile"></a>Det gick inte att aktivera schemalagdaEventsProfile
Om du får felmeddelandet "BadRequest" med ett felmeddelande med texten "Det gick inte att hitta medlemmen 'scheduledEventsProfile' på objekt av typen 'VirtualMachineProfile'", kontrollerar du API-versionen som används för skalningsuppsättningsåtgärderna. Beräkning API version **2019-03-01** eller högre krävs. 

### <a name="failure-to-get-terminate-events"></a>Det gick inte att hämta avsluta händelser
Om du inte får några **avsluta** händelser via schemalagda händelser kontrollerar du den API-version som används för att hämta händelserna. Api-version **för metadatatjänsten 2019-01-01** eller senare krävs för avsluta händelser.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Få avsluta händelse med felaktig NotBefore-tid  
När du har aktiverat *scheduledEventsProfile* på skalningsuppsättningsmodellen och ställt in *notBeforeTimeout*uppdaterar du de enskilda instanserna till den [senaste modellen](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) för att återspegla ändringarna.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på skalningsuppsättningar för virtuella datorer.
