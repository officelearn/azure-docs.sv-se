---
title: Lär dig mer om Orchestration-lägen för skalnings uppsättningar för virtuella datorer i Azure
description: Lär dig mer om Orchestration-lägen för skalnings uppsättningar för virtuella datorer i Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 10/23/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: eb7d4d8a6f1c1ee55601cdd839e330147e60bcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011081"
---
# <a name="orchestration-modes-preview"></a>Orchestration-lägen (förhands granskning)

> [!CAUTION]
> Tack för alla som deltog i den här offentliga för hands versionen. Vi kunde samla in värdefull feedback från vår community. Den här förhands granskningen är nu **avslutad** för nya deltagare, så att du kan integrera feedback. Vi kommer att uppdatera det här utrymmet med ny information.

Skalnings uppsättningar för virtuella datorer ger en logisk gruppering av plattforms hanterade virtuella datorer. Med skalnings uppsättningar skapar du en konfigurations modell för virtuella datorer, lägger automatiskt till eller tar bort ytterligare instanser baserat på CPU eller minnes belastning och uppgraderar automatiskt till den senaste versionen av operativ systemet. Som traditionellt kan du använda skalnings uppsättningar för att skapa virtuella datorer med en konfigurations modell för virtuella datorer som tillhandahålls när skalnings uppsättningen skapas, och skalnings uppsättningen kan bara hantera virtuella datorer som är implicit skapade baserat på konfigurations modellen.

Med skalnings uppsättningens Orchestration-läge (för hands version) kan du nu välja om skalnings uppsättningen ska dirigera virtuella datorer som uttryckligen skapas utanför en konfigurations modell för skalnings uppsättningar eller om virtuella dator instanser har skapats implicit baserat på konfigurations modellen. I Orchestration-läget för skalnings uppsättningar kan du också utforma din infrastruktur för virtuella datorer för hög tillgänglighet genom att distribuera de virtuella datorerna i fel domäner och Tillgänglighetszoner.


Skalnings uppsättningar för virtuella datorer har stöd för 2 distinkta Orchestration lägen:

- ScaleSetVM – virtuella dator instanser som läggs till i skalnings uppsättningen baseras på konfigurations modellen för skalnings uppsättningen. Livs cykeln för virtuella dator instanser – skapa, uppdatera och ta bort – hanteras av skalnings uppsättningen.
- Virtuell dator (Virtual Machines) – virtuella datorer som skapats utanför skalnings uppsättningen kan läggas till explicit i scaleset. 
 

> [!IMPORTANT]
> Orchestration-läget definieras när du skapar skalnings uppsättningen och kan inte ändras eller uppdateras senare. 
> 
> Den här funktionen för skalnings uppsättningar för virtuella datorer är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Orkestreringslägen

| Funktion                     | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Konfigurations modell för virtuell dator      | Inget                                       | Krävs |
| Lägger till ny virtuell dator i skalnings uppsättningen  | Virtuella datorer läggs explicit till i skalnings uppsättningen när den virtuella datorn skapas. | Virtuella datorer skapas implicit och läggs till i skalnings uppsättningen baserat på den virtuella datorns konfigurations modell, instans antal och regler för automatisk skalning | |
| Ta bort virtuell dator                   | Virtuella datorer måste tas bort individuellt, skalnings uppsättningen tas inte bort om den har några virtuella datorer i den. | Virtuella datorer kan tas bort individuellt. om du tar bort skalnings uppsättningen raderas alla VM-instanser.  |
| Ansluta/koppla från virtuella datorer           | Stöds inte                              | Stöds inte |
| Instans livs cykel (skapande genom borttagning) | Virtuella datorer och deras artefakter (t. ex. diskar och nätverkskort) kan hanteras separat. | Instanser och deras artefakter (t. ex. diskar och nätverkskort) är implicita till de skalnings uppsättnings instanser som skapar dem. De kan inte kopplas från eller hanteras separat utanför skalnings uppsättningen |
| Feldomäner               | Kan definiera fel domäner. 2 eller 3 baserat på regional support och 5 för tillgänglighets zon. | Kan definiera fel domäner från 1 till 5 |
| Uppdateringsdomäner              | Uppdaterings domäner mappas automatiskt till fel domäner | Uppdaterings domäner mappas automatiskt till fel domäner |
| Tillgänglighetszoner          | Stöder regional distribution eller virtuella datorer i en tillgänglighets zon | Stöder regional distribution eller flera Tillgänglighetszoner; Kan definiera strategi för zon utjämning |
| Automatisk skalning                   | Stöds inte                              | Stöds |
| Uppgradering av operativ system                  | Stöds inte                              | Stöds |
| Modell uppdateringar               | Stöds inte                              | Stöds |
| Instans kontroll            | Fullständig VM-kontroll. Virtuella datorer har fullständigt kvalificerade URI: er som stöder alla funktioner för hantering av virtuella Azure-datorer (t. ex. Azure Policy, Azure Backup och Azure Site Recovery) | Virtuella datorer är beroende resurser i skalnings uppsättningen. Instanser kan endast nås för hantering via skalnings uppsättningen. |
| Instans modell              | Modell definition för Microsoft. Compute/VirtualMachines. | Modell definition för Microsoft. Compute/VirtualMachineScaleSets/VirtualMachines. |
| Kapacitet                    | Det går inte att skapa en tom skalnings uppsättning. upp till 200 virtuella datorer kan läggas till i skalnings uppsättningen | Skalnings uppsättningar kan definieras med ett instans antal 0-1000 |
| Flytta                        | Stöds                                  | Stöds |
| Enskild placerings grupp = = falskt | Stöds inte                          | Stöds |


## <a name="next-steps"></a>Nästa steg

Mer information finns i [Översikt över tillgänglighets alternativ](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machine-scale-sets%2ftoc.json).
