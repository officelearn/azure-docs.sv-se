---
title: Azure Monitor för virtuella datorer (förhandsversion) kända problem | Microsoft Docs
description: Den här artikeln beskriver kända problem med Azure Monitor för virtuella datorer, en lösning i Azure som kombinerar hälsotillstånd, beroende programidentifiering och prestandaövervakning av Azure VM-operativsystem.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: magoedte
ms.openlocfilehash: 5fdee4edce0301a5a4524725879f599b0a2e3532
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386386"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Kända problem med Azure Monitor för virtuella datorer (förhandsversion)

Den här artikeln beskriver kända problem med Azure Monitor för virtuella datorer, en lösning i Azure som kombinerar hälsotillstånd, identifiering av programkomponenter och övervakning av programprestanda för Azure VM-operativsystem. 

## <a name="health"></a>Hälsa 
Följande är kända problem med den aktuella versionen av funktionen hälsotillstånd:

- Panelen VM-egenskapen visar 2019 för Windows Server-operativsystem som Windows Server 2016. Detta kommer att åtgärdas i en kommande version.
- Om en Azure-dator har tagits bort eller tagits bort, visas den i VM-listvyn under en viss tid. Även om du klickar på tillståndet för en virtuell dator har tagits bort eller borttagna öppnas den **hälsotillstånd diagnostik** visa och sedan startar en inläsning av slinga. Väljer namnet på den borttagna virtuella datorn öppnas ett fönster med ett meddelande om att den virtuella datorn har tagits bort.
- Konfigurationsändringar, till exempel att uppdatera ett tröskelvärde ta upp till 30 minuter, även om portalen eller arbetsbelastning övervakaren API kan uppdatera dem omedelbart. 
- Diagnostik för hälsa får uppdateringar snabbare än andra vyer. Informationen kan fördröjas när du växlar mellan dem. 
- För virtuella Linux-datorer har titeln på den sida där hälsotillstånd villkoren för en enda VM-vy hela domännamnet för den virtuella datorn i stället för en användardefinierad VM-namn. 
- När du inaktiverar övervakning för en virtuell dator med en av metoderna som stöds och du försöker distribuera den igen, bör du distribuera den på samma arbetsyta. Om du väljer en annan arbetsyta och försök att visa hälsotillståndet för den virtuella datorn kan visas det inkonsekvent beteende.
- Total processoranvändning hälsotillstånd kriterium för Windows visas ett tröskelvärde för *inte lika med* **4**, betydelse CPU-belastningen är större än 95% och kölängden för systemet är större än 15. Det här kriteriet för hälsotillstånd kan inte konfigureras i den här förhandsversionen.  
- När du tar bort komponenter från din arbetsyta som du kan fortsätta att visa hälsotillståndet från dina virtuella datorer i Azure; mer specifikt kartan data om prestanda och när du navigerar till någon av vyerna i portalen. Data kommer så småningom inte att visas i vyn prestanda och kartan efter en stund; men hälsovyn fortsätter att visa hälsostatus för dina virtuella datorer. Den **Prova nu** alternativet kommer att kunna re publicera från prestanda och kartan vyer.

## <a name="next-steps"></a>Nästa steg
För att förstå de krav och metoder för att aktivera övervakning av dina virtuella datorer, granska [distribuera Azure Monitor för virtuella datorer](vminsights-onboard.md).
