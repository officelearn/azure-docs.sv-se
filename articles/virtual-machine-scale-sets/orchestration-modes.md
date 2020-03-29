---
title: Läs mer om orchestration-lägen för skalningsuppsättningar för virtuella datorer i Azure
description: Läs mer om orchestration-lägen för skalningsuppsättningar för virtuella datorer i Azure.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279067"
---
# <a name="orchestration-mode-preview"></a>Orkestreringsläge (förhandsgranskning)

Skalaruppsättningar för virtuella datorer ger en logisk gruppering av plattformshanterade virtuella datorer. Med skalningsuppsättningar skapar du en konfigurationsmodell för virtuella datorer, lägger automatiskt till eller tar bort ytterligare instanser baserat på CPU- eller minnesbelastning och uppgraderar automatiskt till den senaste OS-versionen. Traditionellt kan skalningsuppsättningar du skapa virtuella datorer med hjälp av en VM-konfigurationsmodell som tillhandahålls vid tidpunkten för skalningsuppsättningen skapas, och skalningsuppsättningen kan bara hantera virtuella datorer som implicit skapas baserat på konfigurationsmodellen.

Med skalningsuppsättningens orkestreringsläge (förhandsgranskning) kan du nu välja om skalningsuppsättningen ska dirigera virtuella datorer som skapas uttryckligen utanför en skalenlig konfigurationsmodell, eller instanser av virtuella datorer som skapas implicit baserat på konfigurationsmodell. Scale set orchestration mode hjälper dig också att utforma din VM-infrastruktur för hög tillgänglighet genom att distribuera dessa virtuella datorer i feldomäner och tillgänglighetszoner.


Skaluppsättningar för virtuella datorer stöder två olika orchestration-lägen:

- ScaleSetVM – Instanser av virtuella datorer som läggs till i skalningsuppsättningen baseras på konfigurationsmodellen för skalningsuppsättning. Livscykeln för instans för virtuell dator - skapande, uppdatering, borttagning - hanteras av skalningsuppsättningen.
- VM (virtuella datorer) – Virtuella datorer som skapats utanför skalningsuppsättningen kan uttryckligen läggas till i skalningsuppsättningen. 
 

> [!IMPORTANT]
> Orchestration-läget definieras när du skapar skalningsuppsättningen och kan inte ändras eller uppdateras senare. 
> 
> Den här funktionen i skalningsuppsättningar för virtuella datorer är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Orkestreringslägen

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Konfigurationsmodell för virtuell dator      | Inget                                       | Krävs |
| Lägga till ny virtuell dator i skalningsuppsättning  | Virtuella datorer läggs uttryckligen till i skalningsuppsättningen när den virtuella datorn skapas. | Virtuella datorer skapas implicit och läggs till i skalningsuppsättningen baserat på vm-konfigurationsmodellen, instansantalet och regler för automatisk skalning | |
| Ta bort virtuell dator                   | Virtuella datorer måste tas bort individuellt, skalningsuppsättningen tas inte bort om den har några virtuella datorer i den. | Virtuella datorer kan tas bort individuellt, om du tar bort skalningsuppsättningen tas alla VM-instanser bort.  |
| Bifoga/koppla bort virtuella datorer           | Stöds inte                              | Stöds inte |
| Instanslivscykeln (skapande genom borttagning) | Virtuella datorer och deras artefakter (som diskar och nätverkskort) kan hanteras oberoende av dem. | Instanser och deras artefakter (som diskar och nätverkskort) är implicita i de skalningsuppsättningsinstanser som skapar dem. De kan inte tas loss eller hanteras separat utanför skalan som |
| Feldomäner               | Kan definiera feldomäner. 2 eller 3 baserat på regionalt stöd och 5 för tillgänglighetszon. | Kan definiera feldomäner som går från 1 till 5 |
| Uppdateringsdomäner              | Uppdateringsdomäner mappas automatiskt till feldomäner | Uppdateringsdomäner mappas automatiskt till feldomäner |
| Tillgänglighetszoner          | Stöder regional distribution eller virtuella datorer i en tillgänglighetszon | Stöder regional distribution eller flera tillgänglighetszoner. Kan definiera zonbalanseringsstrategin |
| Automatisk skalning                   | Stöds inte                              | Stöds |
| Uppgradering av operativsystemet                  | Stöds inte                              | Stöds |
| Modelluppdateringar               | Stöds inte                              | Stöds |
| Instanskontroll            | Fullständig VM-kontroll. Virtuella datorer har fullt kvalificerad URI som stöder hela skalan av Azure VM-hanteringsfunktioner (som Azure Policy, Azure Backup och Azure Site Recovery) | Virtuella datorer är beroende resurser i skalningsuppsättningen. Instanser kan endast nås för hantering via skalningsuppsättningen. |
| Instansmodell              | Modelldefinition för Microsoft.Compute/VirtualMachines. | Modelldefinitionen för Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines. |
| Kapacitet                    | En tom skalningsuppsättning kan skapas. upp till 200 virtuella datorer kan läggas till i skalningsuppsättningen | Skalningsuppsättningar kan definieras med instansantalet 0 - 1000 |
| Flytta                        | Stöds                                  | Stöds |
| Enskild placeringsgrupp == falskt | Stöds inte                          | Stöds |


## <a name="next-steps"></a>Nästa steg

Mer information finns i [Översikt över tillgänglighetsalternativ](availability.md).
