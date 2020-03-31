---
title: Övervaka servermått för Azure Analysis Services | Microsoft-dokument
description: Lär dig hur Analysis Services använder Azure Metrics Explorer, ett kostnadsfritt verktyg i portalen, som hjälper dig att övervaka prestanda och hälsa för dina servrar.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aaa3a6d128fe7dd466f6f60ab515f05fa38ba63b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252810"
---
# <a name="monitor-server-metrics"></a>Övervaka servermått

Analysis Services tillhandahåller mått i Azure Metrics Explorer, ett kostnadsfritt verktyg i portalen, som hjälper dig att övervaka prestanda och hälsa för dina servrar. Övervaka till exempel minnes- och CPU-användning, antal klientanslutningar och frågeresursförbrukning. Analysis Services använder samma övervakningsramverk som de flesta andra Azure-tjänster. Mer information finns i [Komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md).

Om du vill utföra mer djupgående diagnostik, spåra prestanda och identifiera trender för flera tjänstresurser i en resursgrupp eller prenumeration använder du [Azure Monitor](../azure-monitor/overview.md). Azure Monitor (tjänst) kan resultera i en fakturerbar tjänst.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Så här övervakar du mått för en Analysis Services-server

1. I Azure-portalen väljer du **Mått**.

    ![Övervaka i Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Välj de mått som ska inkluderas i diagrammet i **Mått.** 

    ![Övervaka diagram](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Servermått

Använd den här tabellen för att avgöra vilka mått som är bäst för ditt övervakningsscenario. Endast mått för samma enhet kan visas i samma diagram.

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|
|---|---|---|---|---|
|KommandoPoolJobQueueLength|Längd på jobbköen för kommandopool|Antal|Medel|Antal jobb i kön till kommandotrådspoolen.|
|CurrentConnections|Anslutning: Aktuella anslutningar|Antal|Medel|Aktuellt antal etablerade klientanslutningar.|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medel|Aktuellt antal användarsessioner har upprättats.|
|mashup_engine_memory_metric|M Motorminne|Byte|Medel|Minnesanvändning genom mashup motor processer|
|mashup_engine_qpu_metric|M Motor QPU|Antal|Medel|QPU användning av mashup motor processer|
|memory_metric|Minne|Byte|Medel|Memory. Räckvidd 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|
|memory_thrashing_metric|Minnesförslöing|Procent|Medel|Genomsnittligt minne stryk.|
|CleanerCurrentPrice|Minne: Renare nuvarande pris|Antal|Medel|Nuvarande pris på minne, $/byte/tid, normaliserat till 1000.|
|CleanerMemoryNonshrinkable|Minne: Renare minne nonshrinkable|Byte|Medel|Mängden minne, i byte, som inte kan rensas av bakgrundstvätten.|
|CleanerMemoryShrinkable|Minne: Renare minne krympbar|Byte|Medel|Mängd minne, i byte, föremål för rensning av bakgrundstvätten.|
|MemoryLimitHard|Minne: Minnesgräns hårt|Byte|Medel|Gräns för hårt minne från konfigurationsfilen.|
|MinneLimitHög|Minne: Minnesgränsen hög|Byte|Medel|Hög minnesgräns, från konfigurationsfilen.|
|MemoryLimitLow|Minne: Minnesgränsen låg|Byte|Medel|Låg minnesgräns från konfigurationsfilen.|
|MinneLimitVertiPaq|Minne: Minnesgräns VertiPaq|Byte|Medel|In-memory limit, från konfigurationsfilen.|
|Minnesupptring|Minne: Minnesanvändning|Byte|Medel|Minnesanvändning av serverprocessen som används för att beräkna renare minnespris. Lika med att motverka Process\PrivateBytes plus storleken på minnesmappade data, ignorera allt minne, som mappades eller allokerades av in-memory analytics engine (VertiPaq) som överstiger motorn Minnesgränsen.|
|private_bytes_metric|Privata byte |Byte|Medel|Den totala mängden minne som Analysis Services-motorprocessen och Mashup-behållarprocesserna har allokerat, exklusive minne som delas med andra processer.|
|virtual_bytes_metric|Virtuella byte |Byte|Medel|Den aktuella storleken på det virtuella adressutrymmet som Analysis Services-motorprocessen och Mashup-behållarprocesserna använder.|
|mashup_engine_private_bytes_metric|M Motor privata byte |Byte|Medel|Den totala mängden minne Mashup-behållarprocesser har allokerats, exklusive minne som delas med andra processer.|
|mashup_engine_virtual_bytes_metric|Virtuella byte för M-motor |Byte|Medel|Den aktuella storleken på den virtuella adressutrymme Mashup behållaren processer använder.|
|Kvot|Minne: Kvot|Byte|Medel|Aktuell minneskvot, i byte. Minneskvot kallas också minnesbidrag eller minnesreservation.|
|Kvotblockerad|Minne: Kvot blockerad|Antal|Medel|Aktuellt antal kvotbegäranden som blockeras tills andra minneskvoter frigörs.|
|VertiPaqNonpaged|Minne: VertiPaq Ej sidiga|Byte|Medel|Byte av minne låst i arbetsminnet för användning av minnesmotorn.|
|VertiPaqSidad|Minne: VertiPaq Sökte|Byte|Medel|Byte av sökt minne som används för minnesdata.|
|BearbetningPoolJobQueueLength|Köns längd för bearbetning av pooljobb|Antal|Medel|Antal icke-I/O-jobb i kön till bearbetningstrådspoolen.|
|RaderKonverteradPerSec|Bearbetning: Rader konverterade per sekund|CountPerSecond|Medel|Radhastighet som konverterats under bearbetningen.|
|RadernaReadPerSec|Bearbetning: Rader som läses per sekund|CountPerSecond|Medel|Radhastigheten som läses från alla relationsdatabaser.|
|RaderSkrivnaPerSec|Bearbetning: Rader skrivna per sekund|CountPerSecond|Medel|Radhastighet som skrivs under bearbetningen.|
|qpu_metric|QPU (på andra sätt)|Antal|Medel|QPU. Räckvidd 0-100 för S1, 0-200 för S2 och 0-400 för S4|
|QueryPoolBusyTråds|Upptagna trådar för frågepool|Antal|Medel|Antal upptagna trådar i frågetrådspoolen.|
|Lyckade anslutningarPerSec|Lyckade anslutningar per sekund|CountPerSecond|Medel|Hastighet för slutförda anslutningar.|
|CommandPoolBusyTråds|Trådar: Kommandopool upptagen trådar|Antal|Medel|Antal upptagna trådar i kommandotrådspoolen.|
|CommandPoolIdleThreads|Trådar: Kommandopool inaktiva trådar|Antal|Medel|Antal inaktiva trådar i kommandotrådspoolen.|
|LongParsingBusyTråds|Trådar: Lång tolkning upptagen trådar|Antal|Medel|Antal upptagna trådar i den långa tolkningstrådspoolen.|
|LongParsingIdleThreads|Trådar: Lång parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i den långa tolkningstrådspoolen.|
|LångParsingJobQueueLength|Trådar: Lång parsning av jobbkölängd|Antal|Medel|Antal jobb i kön för den långa tolkningstrådspoolen.|
|BearbetningPoolIOJobQueueLength|Trådar: Bearbetning av i/o-jobbkölängd för pool|Antal|Medel|Antal I/O-jobb i kön till bearbetningstrådspoolen.|
|ProcessingPoolBusyIOJobTrådar|Trådar: Bearbetning pool upptagen I / O-jobb trådar|Antal|Medel|Antal trådar som kör I/O-jobb i bearbetningstrådspoolen.|
|ProcessingPoolBusyNonIOTråds|Trådar: Bearbetning pool upptagen icke-I /O trådar|Antal|Medel|Antal trådar som kör icke-I/O-jobb i bearbetningstrådspoolen.|
|ProcessingPoolIdleIOJobTrådar|Trådar: Bearbetning av tomgångstrådar för i/o-jobb för poolen|Antal|Medel|Antal inaktiva trådar för I/O-jobb i bearbetningstrådspoolen.|
|ProcessingPoolIdleNonIOTrådar|Trådar: Bearbetning av pool inaktiv icke-I/O-trådar|Antal|Medel|Antal inaktiva trådar i bearbetningstrådspoolen som är dedikerad till icke-I/O-jobb.|
|QueryPoolIdleTrådar|Trådar: Frågepool inaktiva trådar|Antal|Medel|Antal inaktiva trådar för I/O-jobb i bearbetningstrådspoolen.|
|FrågaPoolJobQueueLength|Trådar: Kölängd för frågepooljobb|Antal|Medel|Antal jobb i kön till frågetrådspoolen.|
|ShortParsingBusyTråds|Trådar: Kort tolkning upptagen trådar|Antal|Medel|Antal upptagna trådar i den korta tolkningstrådspoolen.|
|ShortParsingIdleThreads|Trådar: Korta parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i den korta tolkningstrådspoolen.|
|KortParsingJobQueueLength|Trådar: Kort parsning av jobbkölängd|Antal|Medel|Antal jobb i kön till den korta tolkningstrådspoolen.|
|TotalAnslutningsylar|Totalt antal anslutningsfel|Antal|Medel|Totalt antal misslyckade anslutningsförsök.|
|Totalt Antal anslutningsanknlöningar|Totalt antal anslutningsbegäranden|Antal|Medel|Totalt antal anslutningsbegäranden. |

## <a name="next-steps"></a>Nästa steg
[Översikt över Azure Monitor](../azure-monitor/overview.md)      
[Komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md)      
[Mått i REST-API FÖR Azure Monitor](/rest/api/monitor/metrics)
