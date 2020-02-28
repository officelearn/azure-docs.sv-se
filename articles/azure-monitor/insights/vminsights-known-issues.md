---
title: Azure Monitor för virtuella datorer (förhandsversion) kända problem | Microsoft Docs
description: Den här artikeln beskriver kända problem med Azure Monitor for VMs, en lösning i Azure som kombinerar hälsa, identifiering av program beroenden och prestanda övervakning av operativ systemet för virtuella Azure-datorer.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: 711b3707d536c4858578817589670edf0f467b64
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670737"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Kända problem med Azure Monitor för virtuella datorer (förhandsversion)

Den här artikeln beskriver kända problem med Azure Monitor for VMs, en lösning i Azure som kombinerar hälsa, identifiering av program komponenter och prestanda övervakning av operativ systemet för virtuella Azure-datorer. 

## <a name="health"></a>Hälsa 
Följande är kända problem med den aktuella versionen av hälso funktionen:

- Om en virtuell Azure-dator tas bort eller tas bort visas den i vyn VM-lista för en stund. Genom att klicka på tillståndet för en borttagen eller borttagen virtuell dator öppnar du dessutom vyn **hälso diagnos** och initierar sedan en inläsnings slinga. Väljer namnet på den borttagna virtuella datorn öppnas ett fönster med ett meddelande om att den virtuella datorn har tagits bort.
- Konfigurationsändringar, till exempel att uppdatera ett tröskelvärde ta upp till 30 minuter, även om portalen eller arbetsbelastning övervakaren API kan uppdatera dem omedelbart. 
- Hälso Diagnoss upplevelsen uppdateras snabbare än andra vyer. Informationen kan vara försenad när du växlar mellan dem. 
- För virtuella Linux-datorer har titeln på sidan som visar hälso villkoren för en enskild VM-vy hela domän namnet för den virtuella datorn i stället för det användardefinierade virtuella dator namnet. 
- När du har inaktiverat övervakning för en virtuell dator med någon av de metoder som stöds och du försöker distribuera den igen, bör du distribuera den på samma arbets yta. Om du väljer en annan arbets yta och försöker visa hälso tillståndet för den virtuella datorn kan det Visa inkonsekventa beteenden.
- När du har tagit bort lösnings komponenterna från din arbets yta kan du fortsätta att se hälso tillståndet från dina virtuella Azure-datorer. specifikt prestanda och mappa data när du navigerar till någon av vyerna i portalen. Data kommer slutligen att sluta visas från vyn prestanda och karta efter en stund. Hälso läget kommer dock fortfarande att Visa hälso status för dina virtuella datorer. Alternativet **prova nu** är bara tillgängligt för att återställas från prestanda-och kart visning.

## <a name="next-steps"></a>Nästa steg
Om du vill förstå kraven och metoderna för att aktivera övervakning av dina virtuella datorer kan du läsa [aktivera Azure Monitor for VMS](vminsights-enable-overview.md).
