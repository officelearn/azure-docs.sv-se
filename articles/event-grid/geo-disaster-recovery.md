---
title: Geo katastrofåterställning i Azure Event Grid | Microsoft-dokument
description: Beskriver hur Azure Event Grid stöder geokatastrofåterställning (GeoDR) automatiskt.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307324"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Geo-haveriberedskap på serversidan i Azure Event Grid
Event Grid har nu en automatisk geo katastrofåterställning (GeoDR) av metadata inte bara för nya, men alla befintliga domäner, ämnen och händelseprenumerationer. Om en hel Azure-region går ned har Event Grid redan alla dina händelserelaterade infrastrukturmetadata synkroniserade med en parkopplad region. Dina nya händelser kommer att börja flöda igen utan ingripande av dig. 

Haveriberedskap mäts med två mått:

- [Mål för återställningspunkt :de](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)minuter eller timmar av data som kan gå förlorade.
- [Mål för återställningstid (RTO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)de timmar som tjänsten kan vara nere.

Event Grid automatisk redundans har olika felpunkter och FPO:er för dina metadata (händelseprenumerationer etc.) och data (händelser). Om du behöver en annan specifikation än följande kan du fortfarande implementera din egen [klientsidan misslyckas över med hjälp av ämnet hälsa apis](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Mål för återställningspunkt (RPO)
- **Metadata RPO:** noll minuter. När som helst en resurs skapas i Event Grid, replikeras den omedelbart över regioner. När en redundans inträffar går inga metadata förlorade.
- **Data RPO**: Om ditt system är felfritt och fångas upp på befintlig trafik vid tidpunkten för regionala redundans, rpo för händelser är ca 5 minuter.

## <a name="recovery-time-objective-rto"></a>Mål för återställningstid (RTO)
- **Metadata RTO:** Även om det i allmänhet händer mycket snabbare, inom 60 minuter, kommer Event Grid börja acceptera skapa / uppdatera / ta bort samtal för ämnen och prenumerationer.
- **Data RTO:** Liksom metadata, det händer i allmänhet mycket snabbare, men inom 60 minuter, kommer Event Grid börja acceptera ny trafik efter en regional redundans.

> [!NOTE]
> Kostnaden för metadata GeoDR på Event Grid är: $0.


## <a name="next-steps"></a>Nästa steg
Om du vill implementera din egen redundanslogik på klientsidan läser du [# Skapa din egen haveriberedskap för anpassade ämnen i Händelserutnät](custom-disaster-recovery.md)
