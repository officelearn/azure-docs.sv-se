---
title: Vanliga frågor om övervakning av Azure Site Recovery
description: Få svar på vanliga frågor om övervakning av Azure Site Recovery, med inbyggd övervakning och Azure Monitor (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68718267"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Vanliga frågor om övervakning av webbplatsåterställning

Den här artikeln besvarar vanliga frågor om hur du övervakar Azure [Site Recovery](site-recovery-overview.md), med inbyggd övervakning av webbplatsåterställning och Azure Monitor (Log Analytics).

## <a name="general"></a>Allmänt

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Hur loggas RPO-värdet från den senaste tillgängliga återställningspunkten?

Site Recovery använder en process i flera steg, asynkron för att replikera datorer till Azure.

- I det näst sista steget i replikering kopieras de senaste ändringarna på datorn, tillsammans med metadata, till ett lagringskonto för logg/cache.
- Dessa ändringar, tillsammans med taggen som identifierar en återställningsbar punkt, skrivs till lagringskontot/den hanterade disken i målregionen.
- Site Recovery kan nu generera en återställningsbar punkt för maskinen.
- Vid denna punkt har RPO uppfyllts för de ändringar som överförts till lagringskontot hittills. Med andra ord är maskinens RPO vid denna punkt lika med den tid som förflutit från tidsstämpeln som motsvarar återställningspunkten.
- Nu väljer Site Recovery de överförda data från lagringskontot och tillämpar dem på de replikdiskar som skapats för datorn.
- Site Recovery genererar sedan en återställningspunkt och gör denna punkt tillgänglig för återställning vid redundans.
- Den senaste tillgängliga återställningspunkten anger därför den tidsstämpel som motsvarar den senaste återställningspunkten som redan har bearbetats och tillämpas på replikdiskarna.


En felaktig systemtid på den replikerande källdatorn eller på lokala infrastrukturservrar kommer att skeva det beräknade RPO-värdet. För korrekt RPO-rapportering, se till att systemklockan är korrekt på alla servrar och maskiner.



## <a name="inbuilt-site-recovery-logging"></a>Inbyggd loggning av webbplatsåterställning


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Varför skiljer sig antalet virtuella datorer i vyn valvinfrastruktur från det totala antalet som visas i replikerade objekt?

Vyn arkivinfrastruktur begränsas av replikeringsscenarier. Endast datorer i det valda replikeringsscenariot ingår i antalet för vyn. Dessutom räknar vi bara virtuella datorer som är konfigurerade för att replikera till Azure. Det gick inte att överföra datorer eller datorer som replikerar tillbaka till en lokal plats räknas inte i vyn.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Varför skiljer sig antalet replikerade objekt i Essentials från det totala antalet replikerade objekt på instrumentpanelen?

Endast datorer för vilka den första replikeringen har slutförts ingår i antalet som visas i Essentials. Summan av replikerade objekt omfattar alla datorer i valvet, inklusive de för vilka den första replikeringen pågår.

## <a name="azure-monitor-logging"></a>Azure Monitor-loggning


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Hur ofta skickar Site Recovery diagnostikloggar till Azure Monitor Log? 

- AzureSiteRecoveryReplicationStats och AzureSiteRecoveryRecoveryPoints skickas var 15:e minut.  
- AzureSiteRecoveryReplicationDataUploadRate och AzureSiteRecoveryProtectedDiskDataChurn skickas var femte minut. 
- AzureSiteRecoveryJobs skickas vid utlösaren och slutförande av ett jobb.
- AzureSiteRecoveryEvents skickas när en händelse genereras. 
- AzureSiteRecoveryReplicatedItems skickas när det sker någon miljöändring. Vanligtvis är datauppdateringstiden 15 minuter efter en ändring. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Hur länge sparas data i Azure Monitor-loggar? 

Som standard är kvarhållningen i 31 dagar. Du kan öka perioden i avsnittet **Användning och uppskattad kostnad** på arbetsytan Logganalys. Klicka på **Datalagring**och välj intervall.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>Hur stor är diagnostikloggarna? 

Vanligtvis är storleken på en logg 15-20 KB. 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du övervakar med [inbyggd övervakning av webbplatsåterställning](site-recovery-monitor-and-troubleshoot.md)eller [Azure Monitor](monitor-log-analytics.md).


