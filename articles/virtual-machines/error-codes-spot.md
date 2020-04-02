---
title: Felkoder för virtuella Azure-plats-datorer och skalningsuppsättningar
description: Lär dig mer om felkoder som du kan se när du använder Spot-virtuella datorer och skalningsuppsättningsinstanser.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547822"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Felmeddelanden för spot-datorer och skalningsuppsättningar

Här är några möjliga felkoder som du kan få när du använder Spot-virtuella datorer och skalningsuppsättningar.


| Nyckel | Meddelande | Beskrivning |
|-----|---------|-------------|
| SkuNotAvailable | Den begärda nivån\<\>för resurs ' resurs\<'\>är för\<närvarande\>inte tillgänglig på plats ' plats ' för prenumeration ' subscriptionID '. Försök med en annan nivå eller distribuera till en annan plats. | Det finns inte tillräckligt med Azure Spot-kapacitet på den här platsen för att skapa din virtuella dator eller skalningsuppsättningsinstans. |
| VräkningPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Vräkningsprincipen kan endast anges på virtuella Azure Spot-datorer. | Den här virtuella datorn är inte en plats-VM, så du kan inte ange vräkningsprincipen. |
| AzureSpotVMIntörsintillgänglighetsuppsättning  |  Virtuell azure spot-dator stöds inte i tillgänglighetsuppsättningen. | Du måste välja att antingen använda en spot-vm eller använda en virtuell dator i en tillgänglighetsuppsättning, du kan inte välja båda. |
| AzureSpotFeatureNEnabledFörBeställelse  |  Prenumerationen är inte aktiverad med Azure Spot-funktionen. | Använd en prenumeration som stöder spot-virtuella datorer. |
| VMPriorityCannotBeApplied VMPriority  |  Det angivna prioritetsvärdet '{0}' kan inte{1}tillämpas på den virtuella datorn ' eftersom ingen prioritet angavs när den virtuella datorn skapades. | Ange prioritet när den virtuella datorn skapas. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Det går inte{0}att utföra åtgärden '{1} ' eftersom det angivna maxpriset ' USD är lägre än det aktuella spotpriset USD{2} ' för Azure Spot VM-storlek '{3}'. | Välj ett högre maxpris. Mer information finns i prisinformation för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) eller [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Ogiltigt maxprisvärde. De enda värden som stöds för maxpris är -1 eller en decimal som är större än noll. Maxprisvärde på -1 anger att den virtuella Azure Spot-datorn inte kommer att vräkas av prisskäl. | Ange ett giltigt maxpris. Mer information finns i priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) eller [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Maxprisändring tillåts inte när{0}den virtuella datorn ' för närvarande allokeras. Vänligen deallocate och försök igen. | Stop\Deallocate den virtuella datorn så att du kan ändra maxpriset. |
| MaxPriceChangeNotAllowed | Max prisändring är inte tillåten. | Du kan inte ändra maxpriset för den här virtuella datorn. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure Spot stöds inte för den här API-versionen. | API-versionen måste vara 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Azure Spot stöds inte för {0}den här vm-storleken . | Välj en annan vm-storlek. Mer information finns i [Spot virtuella datorer](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Maxpris stöds endast för virtuella Azure Spot-datorer. | Mer information finns i [Spot virtuella datorer](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Begäran om flytta resurser innehåller en virtuell Azure Spot-dator. Detta stöds för närvarande inte. Kontrollera felinformationen för virtuella dator-ID: n. | Du kan inte flytta start-och retur-datorer. |
| MoveResourcesWithAzureSpotVmssNotSupported Flytta  |  Begäran om flytta resurser innehåller en azure spot-skalningsuppsättning för virtuella datorer. Detta stöds för närvarande inte. Kontrollera felinformationen för skaluppsättnings-ID:n för virtuella datorer. | Du kan inte flytta spotskalauppsättningsförekomster. |
| EfemäraOSDisksNotSupportedForSpotVM | Efemära OS-diskar stöds inte för start-och returdiskar. | Använd en vanlig OS-disk för den virtuella platsen. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Virtuell azure spot-dator stöds inte i skaluppsättningen för virtuella datorer med VM-orkestreringsläge. | Ställ in orchestration-läget på skala för virtuella datorer för att använda Spot-instanser. |


**Nästa steg** Mer information finns i [plats virtuella datorer](./linux/spot-vms.md).