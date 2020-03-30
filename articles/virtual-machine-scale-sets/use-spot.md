---
title: Skapa en skalningsuppsättning som använder virtuella Azure Spot-datorer (förhandsversion)
description: Lär dig hur du skapar Azure-skalningsuppsättningar för virtuella datorer som använder spot-datorer för att spara på kostnader.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 37e914fe6bafe9587be525faf3e01c897cdd8230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162692"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>Förhandsversion: Virtuella Azure Spot-datorer för skalningsuppsättningar för virtuella datorer 

Med Hjälp av Azure Spot på skalningsuppsättningar kan du dra nytta av vår outnyttjade kapacitet till en betydande kostnadsbesparingar. När som helst när Azure behöver tillbaka kapaciteten kommer Azure-infrastrukturen att ta bort Spot-instanser. Därför är Spot-instanser bra för arbetsbelastningar som kan hantera avbrott som batchbearbetningsjobb, utvecklings-/testmiljöer, stora beräkningsarbetsbelastningar med mera.

Mängden tillgänglig kapacitet kan variera beroende på storlek, region, tid på dygnet och mycket mer. När du distribuerar Spot-instanser på skalningsuppsättningar allokerar Azure instansen endast om det finns kapacitet tillgänglig, men det finns inget serviceavtal för dessa instanser. En spotskalauppsättning distribueras i en enda feldomän och erbjuder inga höga tillgänglighetsgarantier.

> [!IMPORTANT]
> Spot-instanser är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="pricing"></a>Prissättning

Prissättningen för Spot-instanser varierar, baserat på region och SKU. Mer information finns i priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Med rörlig prissättning har du möjlighet att ange ett maxpris, i US-dollar (USD), med upp till 5 decimaler. Värdet `0.98765`skulle till exempel vara ett maxpris på 0,98765 USD per timme. Om du anger maxpriset `-1`så att det inte ska vräkas instansen baserat på priset. Priset för instansen kommer att vara det aktuella priset för Spot eller priset för en standardinstans, som någonsin är mindre, så länge det finns kapacitet och kvot tillgänglig.

## <a name="eviction-policy"></a>Policy för vräkning

När du skapar spotskalauppsättningar kan du ange att vräkningsprincipen ska *deallocate* (standard) eller *Ta bort*. 

*Deallocate-principen* flyttar dina vräkta instanser till det stoppade deallocated tillståndet så att du kan distribuera om bortreverade instanser. Det finns dock ingen garanti för att tilldelningen kommer att lyckas. De utdelade virtuella datorerna räknas mot din skalningsuppsättningsinstanskvot och du debiteras för de underliggande diskarna. 

Om du vill att dina instanser i spotskalauppsättningen ska tas bort när de vräkas kan du ställa in vräkningsprincipen för att *ta bort*. När vräkningsprincipen är inställd på att ta bort kan du skapa nya virtuella datorer genom att öka egenskapen skalningsuppsättningsantal. De vräkta virtuella datorerna tas bort tillsammans med deras underliggande diskar och därför debiteras du inte för lagringen. Du kan också använda funktionen för automatisk skalning av skalningsuppsättningar för att automatiskt försöka kompensera för vräkta virtuella datorer, men det finns ingen garanti för att allokeringen kommer att lyckas. Vi rekommenderar att du bara använder funktionen för automatisk skalning på spotskalauppsättningar när du ställer in vräkningsprincipen för att ta bort för att undvika kostnaden för dina diskar och slå kvotgränser. 

Användare kan välja att ta emot aviseringar via [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md). Detta meddelar dig om dina virtuella datorer håller på att vräkas och du kommer att ha 30 sekunder på dig att slutföra alla jobb och utföra avstängningsuppgifter före vräkningen. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Distribuera spot-virtuella datorer i skalningsuppsättningar

Om du vill distribuera spot-datorer i skalningsuppsättningar kan du ange den nya *prioritetsflaggan* till *Spot*. Alla virtuella datorer i skalningsuppsättningen ställs in på Spot. Om du vill skapa en skalningsuppsättning med punkt-virtuella datorer använder du någon av följande metoder:
- [Azure-portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-mallar](#resource-manager-templates)

## <a name="portal"></a>Portalen

Processen för att skapa en skalningsuppsättning som använder spot-datorer är samma som beskrivs i [artikeln komma igång](quick-create-portal.md). När du distribuerar en skalningsuppsättning kan du välja att ange platsflagga och vräkningsprincipen: ![Skapa en skalningsuppsättning med spot-datorer](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Processen för att skapa en skalningsuppsättning med start-datorer för plats är samma som beskrivs i [artikeln komma igång](quick-create-cli.md). Lägg bara till "--Prioritet Spot", och lägg till `--max-price`. I det här `-1` exemplet `--max-price` använder vi för så att instansen inte kommer att vräkas baserat på pris.

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

Processen för att skapa en skalningsuppsättning med start-datorer för plats är samma som beskrivs i [artikeln komma igång](quick-create-powershell.md).
Lägg bara till "-Prioriterad `-max-price` plats", och leverera en till [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

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

Processen för att skapa en skalningsuppsättning som använder spot-datorer är densamma som beskrivs i artikeln komma igång för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). 

Använd`"apiVersion": "2019-03-01"` eller senare för spotmalldistributioner. Lägg `priority`till `evictionPolicy` `billingProfile` , och `"virtualMachineProfile":` egenskaperna i avsnittet i mallen: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Om du vill ta bort instansen när `evictionPolicy` den `Delete`har vräkts ändrar du parametern till .

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F:** När du har skapat, är en Spot-instans samma som standardinstansen?

**A.** Ja, förutom att det inte finns något serviceavtal för start-och-start-datorer och de kan när som helst vräkas.


**F:** Vad ska man göra när du blir vräkt, men fortfarande behöver kapacitet?

**A.** Vi rekommenderar att du använder vanliga virtuella datorer i stället för Spot-virtuella datorer om du behöver kapacitet direkt.


**F:** Hur hanteras kvoten för Spot?

**A.** Spotinstanser och standardinstanser har separata kvotpooler. Spotkvoten delas mellan virtuella datorer och skalningsuppsättningsinstanser. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**F:** Kan jag begära ytterligare kvot för Spot?

**A.** Ja, du kommer att kunna skicka begäran om att öka din kvot för spot-datorer genom [standardprocessen för kvotbegäran.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**F:** Kan jag konvertera befintliga skaluppsättningar till Spot-skalningsuppsättningar?

**A.** Nej, inställningen `Spot` av flaggan stöds bara vid skapande.


**F:** Om jag `low` använde för skaluppsättningar med låg prioritet, måste jag börja använda `Spot` i stället?

**A.** För nu, `low` `Spot` både och kommer att fungera, `Spot`men du bör börja övergången till att använda .


**F:** Kan jag skapa en skalningsuppsättning med både vanliga virtuella datorer och spot-datorer?

**A.** Nej, en skalningsuppsättning kan inte stödja mer än en prioritetstyp.


**F:**  Kan jag använda automatisk skalning med dekorskalauppsättningar?

**A.** Ja, du kan ställa in regler för automatisk skalning på din Spot-skalningsuppsättning. Om dina virtuella datorer har vvrats ut kan automatisk skalning försöka skapa nya virtuella spot-datorer. Kom ihåg att du inte är garanterad denna kapacitet ändå. 


**F:**  Fungerar automatisk skalning med både vräkningsprinciper (deallocate och delete)?

**A.** Vi rekommenderar att du ställer in din vräkningsprincip så att den tas bort när du använder automatisk skalning. Detta beror på att deallocated instanser räknas mot din kapacitetsantal på skalningsuppsättningen. När du använder automatisk skalning kommer du sannolikt att nå ditt antal målinstanser snabbt på grund av de dislokaliserade, vräkta instanserna. 


**F:** Vilka kanaler har stöd för virtuella spotds?

**A.** Se tabellen nedan för Spot VM-tillgänglighet.

<a name="channel"></a>

| Azure-kanaler               | Azure Spot virtuella datorer tillgänglighet       |
|------------------------------|-----------------------------------|
| Enterprise-avtal         | Ja                               |
| Betala per användning                | Ja                               |
| Molntjänstleverantör (CSP) | [Kontakta din partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Fördelar                     | Inte tillgängligt                     |
| Sponsrade                    | Inte tillgängligt                     |
| Kostnadsfri utvärderingsversion                   | Inte tillgängligt                     |


**F:** Var kan jag ställa frågor?

**A.** Du kan lägga upp `azure-spot` och tagga din fråga med på [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat en skalningsuppsättning med spot-datorer kan du prova att distribuera vår [mall för automatisk skalning med Spot](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Läs prissidan för [den virtuella datorns skala för](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) prisinformation.
