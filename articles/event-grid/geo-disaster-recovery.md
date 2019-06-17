---
title: GEO-haveriberedskap i Azure Event Grid | Microsoft Docs
description: Här beskrivs hur Azure Event Grid stöder geo-haveriberedskap (GeoDR) automatiskt.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66307324"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Serversidan geo-haveriberedskap i Azure Event Grid
Event Grid har nu en automatisk geo-haveriberedskap (GeoDR) av metadata inte bara för nya, men alla befintliga domäner, ämnen och prenumerationer på händelser. Om en hel Azure-region slutar fungera kan redan Event Grid alla dina event-relaterade infrastruktur metadata synkroniseras till en länkad region. Din nya händelser börjar flöda igen utan någon åtgärd från dig. 

Haveriberedskap mäts med två mått:

- [Mål för återställningspunkt (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): minuter eller timmar av data som kan gå förlorade.
- [Recovery mål för återställningstid (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): minuter timmar som tjänsten kan vara ned.

Event Grid automatisk redundans har olika återställningspunkter och mål för återställningstid för dina metadata (händelseprenumerationer etc.) och data (händelser). Om du behöver olika specifikationen bland de följande kan du fortfarande implementera dina egna [klientsidan växla över med hjälp av avsnittet hälsotillstånd API: er](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Mål för återställningspunkt (RPO)
- **Metadata RPO**: noll minuter. Varje gång en resurs har skapats i Event Grid, replikeras det direkt i olika regioner. Inga metadata går förlorad när en redundansväxling.
- **Data RPO**: Om systemet är felfritt och i fas med befintlig trafik vid tidpunkten för den regionala växlingen vid fel, är RPO för händelser cirka 5 minuter.

## <a name="recovery-time-objective-rto"></a>Mål för återställningstid (RTO)
- **Metadata RTO**: Även om Allmänt det händer mycket snabbare, inom 60 minuter, börjar Event Grid att acceptera inbjudan att skapa/uppdatera/ta bort ämnen och prenumerationer.
- **Data RTO**: Som metadata sker det vanligtvis mycket snabbare, men inom 60 minuter, Event Grid börjar acceptera nya trafik efter en regional redundans.

> [!NOTE]
> Kostnaden för metadata GeoDR på Event Grid är: $0.


## <a name="next-steps"></a>Nästa steg
Om du vill implementera du egna klientsidan redundanslogiken [# skapa dina egna haveriberedskap för anpassade ämnen i Event Grid](custom-disaster-recovery.md)
