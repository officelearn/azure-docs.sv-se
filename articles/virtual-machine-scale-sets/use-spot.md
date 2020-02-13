---
title: Skapa en skalnings uppsättning som använder virtuella Azure-datorer (för hands version)
description: Lär dig hur du skapar skalnings uppsättningar för virtuella Azure-datorer som använder virtuella datorer för att spara pengar.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 37e914fe6bafe9587be525faf3e01c897cdd8230
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162692"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>För hands version: virtuella Azure-datorer för skalnings uppsättningar för virtuella datorer 

Med hjälp av Azure-punkt på skalnings uppsättningar kan du dra nytta av vår outnyttjade kapacitet till betydande kostnads besparingar. Vid alla tidpunkter då Azure behöver kapaciteten återkommer Azure-infrastrukturen att ta bort plats instanser. Därför är det bra för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Mängden tillgänglig kapacitet kan variera beroende på storlek, region, tid och dag. När du distribuerar plats instanser på skalnings uppsättningar, kommer Azure att allokera instansen endast om det finns tillgänglig kapacitet, men det finns inget service avtal för dessa instanser. En dekor Skale uppsättning distribueras i en enskild feldomän och ger inga garantier för hög tillgänglighet.

> [!IMPORTANT]
> Punkt instanser finns för närvarande i offentlig för hands version.
> Den här för hands versionen rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="pricing"></a>Priser

Priser för punkt instanser är varierande, baserat på region och SKU. Mer information finns i avsnittet om priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Med varierande priser har du möjlighet att ange ett högsta pris i USD (USD) med upp till 5 decimaler. Värdet `0.98765`skulle till exempel vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset som ska `-1`avlägsnas instansen utifrån pris. Priset för instansen är det aktuella priset för dekor eller priset för en standard instans, vilket någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.

## <a name="eviction-policy"></a>Borttagnings princip

När du skapar dekor skalnings uppsättningar kan du ställa in principen för att *frigöra* (standard) eller *ta bort*. 

Principen *frigör* flyttar de inaktuella instanserna till läget Stoppad-frigjord, så att du kan distribuera om avlägsnade instanser. Det finns dock ingen garanti för att allokeringen ska lyckas. De friallokerade virtuella datorerna räknas över till kvoten för din skalnings uppsättning och du debiteras för de underliggande diskarna. 

Om du vill att dina instanser i din dekor Skale uppsättning ska tas bort när de avlägsnas, kan du ange vilken borttagnings princip som ska *tas bort*. När du har angett en princip för borttagning kan du skapa nya virtuella datorer genom att öka antalet skalnings uppsättnings instanser. De avlägsnade virtuella datorerna tas bort tillsammans med deras underliggande diskar och därför debiteras du inte för lagringen. Du kan också använda funktionen för automatisk skalning i skalnings uppsättningar för att automatiskt försöka och kompensera för avlägsnade virtuella datorer, men det finns ingen garanti för att allokeringen kommer att lyckas. Vi rekommenderar att du bara använder funktionen för automatisk skalning på dekor skalnings uppsättningar när du anger att borttagnings principen ska tas bort för att undvika kostnaden för diskarna och vid kvot gränser. 

Användare kan välja att ta emot meddelanden i den virtuella datorn via [Azure schemalagda händelser](../virtual-machines/linux/scheduled-events.md). Detta meddelar dig om dina virtuella datorer avlägsnas och du har 30 sekunder på dig att slutföra jobben och utföra avstängnings uppgifter innan avlägsnandet. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Distribuera virtuella datorer i skalnings uppsättningar

Om du vill distribuera virtuella datorer på skalnings uppsättningar kan du ställa in den nya *prioritets* flaggan på *plats*. Alla virtuella datorer i din skalnings uppsättning anges till dekor. Använd någon av följande metoder för att skapa en skalnings uppsättning med virtuella datorer:
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager mallar](#resource-manager-templates)

## <a name="portal"></a>Portal

Processen för att skapa en skalnings uppsättning som använder virtuella datorer är samma som i [artikeln komma igång](quick-create-portal.md). När du distribuerar en skalnings uppsättning kan du välja att ange flaggan för punkt och avpolicyn: ![skapa en skalnings uppsättning med virtuella datorer](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Processen att skapa en skalnings uppsättning med virtuella datorer är samma som beskrivs i [artikeln komma igång](quick-create-cli.md). Lägg bara till "--prioritets plats" och Lägg till `--max-price`. I det här exemplet använder vi `-1` för `--max-price` så att instansen inte avlägsnas baserat på priset.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Processen att skapa en skalnings uppsättning med virtuella datorer är samma som beskrivs i [artikeln komma igång](quick-create-powershell.md).
Lägg bara till "-prioritets plats" och ange en `-max-price` till [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

Den process som används för att skapa en skalnings uppsättning som använder virtuella datorer är samma som beskrivs i komma igång-artikeln för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). 

Använd`"apiVersion": "2019-03-01"` eller senare för distribution av dekor mallar. Lägg till egenskaperna `priority`, `evictionPolicy` och `billingProfile` i avsnittet `"virtualMachineProfile":` i mallen: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Om du vill ta bort instansen när den har avlägsnats ändrar du `evictionPolicy` parametern till `Delete`.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F:** När det har skapats är en punkt instans samma som standard instans?

**A:** Ja, förutom att det inte finns något service avtal för virtuella datorer på plats och de kan avlägsnas när som helst.


**F:** Vad ska jag göra när du har avlägsnat, men behöver fortfarande kapacitet?

**A:** Vi rekommenderar att du använder virtuella standard datorer i stället för virtuella datorer för virtuella datorer om du behöver kapacitet direkt.


**F:** Hur hanteras kvoten för dekor?

**A:** Punkt instanser och standard instanser kommer att ha separata kvotmallar. Kvoten för kvoten kommer att delas mellan virtuella datorer och skalnings uppsättnings instanser. Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**F:** Kan jag begära ytterligare kvot för platsen?

**A:** Ja, du kommer att kunna skicka begäran om att öka din kvot för virtuella datorer med hjälp av [standard kvot processen](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**F:** Kan jag konvertera befintliga skalnings uppsättningar till dekor skalnings uppsättningar?

**A:** Nej, det går bara att ställa in `Spot`-flaggan när den skapas.


**F:** Måste jag börja använda `Spot` i stället för att använda `low` för låg prioritets skalnings uppsättningar?

**A:** För tillfället kommer både `low` och `Spot` fungera, men du bör börja överföra via `Spot`.


**F:** Kan jag skapa en skalnings uppsättning med både vanliga virtuella datorer och virtuella datorer?

**A:** Nej, en skalnings uppsättning kan inte stödja fler än en prioritets typ.


**F:**  Kan jag använda autoskalning med dekor skalnings uppsättningar?

**A:** Ja, du kan ange regler för automatisk skalning på din plats skalnings uppsättning. Om dina virtuella datorer avlägsnas kan autoskalning försöka skapa nya virtuella dator datorer. Kom ihåg att du inte garanterar den här kapaciteten. 


**F:**  Kan autoskalning användas med både borttagnings principer (frigör och ta bort)?

**A:** Vi rekommenderar att du ställer in en princip för borttagning när du använder autoskalning. Detta beror på att friallokerade instanser räknas mot ditt kapacitets antal i skalnings uppsättningen. När du använder autoskalning når du förmodligen antalet mål instanser snabbt på grund av de frikopplade, avlägsnade instanserna. 


**F:** Vilka kanaler stöder virtuella datorer?

**A:** Se tabellen nedan för tillgänglighet för dekor datorer.

<a name="channel"></a>

| Azure-kanaler               | Tillgänglighet för Azure-VM-VM       |
|------------------------------|-----------------------------------|
| Enterprise-avtal         | Ja                               |
| Betala per användning                | Ja                               |
| Cloud Service Provider (CSP) | [Kontakta din partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Fördelar                     | Inte tillgängligt                     |
| Sponsrat                    | Inte tillgängligt                     |
| Kostnads fri utvärdering                   | Inte tillgängligt                     |


**F:** Var kan jag skicka frågor?

**A:** Du kan skicka och tagga din fråga med `azure-spot` på [f & A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat en skalnings uppsättning med virtuella datorer kan du prova att distribuera vår [mall för automatisk skalning med hjälp av dekor](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Se [prissättnings sidan för den virtuella datorns skalnings uppsättning](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) för pris information.
