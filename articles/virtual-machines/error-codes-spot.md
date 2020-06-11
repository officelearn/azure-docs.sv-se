---
title: Felkoder för Azure-virtuella datorer och skalnings uppsättnings instanser
description: Lär dig mer om felkoder som kan uppstå när du använder virtuella datorer och skalnings uppsättnings instanser.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 4625a0fa75f992ac18eeb91629af373a426aa955
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84676581"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Fel meddelanden för virtuella datorer och skalnings uppsättningar

Här följer några möjliga fel koder som du kan få när du använder virtuella datorer och skalnings uppsättningar.


| Nyckel | Meddelande | Beskrivning |
|-----|---------|-------------|
| SkuNotAvailable | Den begärda nivån för resursen \<resource\> är för närvarande inte tillgänglig på platsen \<location\> för prenumerationen \<subscriptionID\> . Försök med en annan nivå eller distribuera till en annan plats. | Det finns inte tillräckligt med Azure-dekorfärg på den här platsen för att skapa en VM-eller skalnings uppsättnings instans. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Borttagnings principen kan bara ställas in på Azure-Virtual Machines. | Den här virtuella datorn är inte en VM-VM, så du kan inte ange principen för borttagning. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  En virtuell Azure-dator stöds inte i tillgänglighets uppsättningen. | Du måste välja att antingen använda en virtuell dator eller använda en virtuell dator i en tillgänglighets uppsättning. |
| AzureSpotFeatureNotEnabledForSubscription  |  Prenumerationen är inte aktive rad med funktionen Azure-dekor. | Använd en prenumeration som har stöd för virtuella datorer för virtuella datorer. |
| VMPriorityCannotBeApplied  |  Det angivna prioritet svärdet {0} kan inte tillämpas på den virtuella datorn {1} eftersom ingen prioritet angavs när den virtuella datorn skapades. | Ange prioritet när den virtuella datorn skapas. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Det går inte att utföra åtgärden {0} eftersom det angivna Max priset {1} USD är lägre än det aktuella dekor priset {2} USD för Azure VM-storleken för virtuella datorer {3} . | Välj ett högre pris. Mer information finns i pris information för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) eller [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Ogiltigt max pris värde. De enda värden som stöds för högsta pris är-1 eller en decimal som är större än noll. Max pris värde på-1 anger att den virtuella Azure-datorn inte tas bort av pris skäl. | Ange ett giltigt max pris. Mer information finns i avsnittet om priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) eller [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Maximal pris ändring är inte tillåten när den virtuella datorn {0} har allokerats för tillfället. Frigör och försök igen. | Stop\Deallocate den virtuella datorn så att du kan ändra det högsta priset. |
| MaxPriceChangeNotAllowed | Max pris ändring är inte tillåten. | Du kan inte ändra det högsta priset för den här virtuella datorn. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure-platsen stöds inte för den här API-versionen. | API-versionen måste vara 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Azure dekor stöds inte för den här virtuella dator storleken {0} . | Välj en annan VM-storlek. Mer information finns på [plats Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Högsta pris stöds bara för Azure-Virtual Machines. | Mer information finns på [plats Virtual Machines](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Begäran om att flytta resurser innehåller en virtuell Azure-dator. Detta stöds inte för närvarande. Kontrol lera fel informationen för virtuella dator-ID: n. | Det går inte att flytta virtuella plats datorer. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Begäran om att flytta resurser innehåller en skalnings uppsättning för en virtuell Azure-dator. Detta stöds inte för närvarande. Kontrol lera fel informationen för skalnings uppsättnings-ID: n för virtuella datorer. | Det går inte att flytta instanser för punkt skalnings uppsättning. |
| EphemeralOSDisksNotSupportedForSpotVMs | Tillfälliga OS-diskar stöds inte för virtuella datorer på platsen. | Använd en vanlig OS-disk för den virtuella datorn. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Virtuell dator i Azure-platsen stöds inte i skalnings uppsättningen för virtuella datorer med den virtuella datorns Orchestration-läge. | Ange Orchestration-läget till skalnings uppsättningen för den virtuella datorn för att kunna använda punkt instanser. |


**Nästa steg** Mer information finns på [plats Virtual Machines](./linux/spot-vms.md).