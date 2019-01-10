---
title: Övervaka servermått för Azure Analysis Services | Microsoft Docs
description: Lär dig mer om att övervaka servermått för Analysis Services i Azure-portalen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6f686ff40805050b6da05a1971aeabb367da96d7
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190860"
---
# <a name="monitor-server-metrics"></a>Övervaka servermått

Analysis Services innehåller mått för att övervaka prestanda och hälsa för dina servrar. Till exempel övervaka minne och CPU-användning, antalet klientanslutningar och fråga resursförbrukning. Analysis Services använder samma övervakning framework som de flesta andra Azure-tjänster. Mer information finns i [mått i Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Om du vill utföra mer djupgående diagnostik, spåra prestanda, och identifiera trender över flera service-resurser i en resursgrupp eller prenumeration kan använda [Azure Monitor](https://azure.microsoft.com/services/monitor/). Azure Monitor (tjänst) kan resultera i en fakturerbar tjänst.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Övervaka mått för en Analysis Services-server

1. I Azure-portalen väljer du **mått**.

    ![Övervaka i Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. I **tillgängliga mått**, Välj de mått som ska ingå i diagrammet. 

    ![Övervaka diagram](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Servermått

Använd den här tabellen för att avgöra vilka mått som är bäst för ditt övervakning scenario. Endast mått med samma enhet kan visas på samma diagram.

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Kommandot Pool Jobbkölängd|Antal|Medel|Antal jobb i kön för kommandotrådspoolen.|
|CurrentConnections|Anslutning: Aktuella anslutningar|Antal|Medel|Aktuellt antal etablerade klientanslutningar.|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medel|Aktuellt antal etablerade användarsessioner.|
|mashup_engine_memory_metric|M-Motorminne|Byte|Medel|Minnesanvändning efter motorprocesser|
|mashup_engine_qpu_metric|M-motor QPU|Antal|Medel|QPU-användning efter motorprocesser|
|memory_metric|Minne|Byte|Medel|Minne. Intervall 0 – 25 GB för S1, 0 – 50 GB för S2 och 0-100 GB för S4|
|memory_thrashing_metric|Minnesförslöing|Procent|Medel|Genomsnittlig minnesförslöing.|
|CleanerCurrentPrice|Minne: Aktuellt pris för rensaren|Antal|Medel|Aktuellt pris för minne, $/ byte/tid, normaliserat till 1000.|
|CleanerMemoryNonshrinkable|Minne: Rensningsminne krympbart|Byte|Medel|Mängden minne i byte, inte tas bort av bakgrundsrensaren.|
|CleanerMemoryShrinkable|Minne: Rensningsminne Krympbart|Byte|Medel|Mängden minne i byte, tas bort av bakgrundsrensaren.|
|MemoryLimitHard|Minne: Minnesgräns, hård|Byte|Medel|Hård minnesgräns, från konfigurationsfilen.|
|MemoryLimitHigh|Minne: Minnesgräns hög|Byte|Medel|Hög minnesgräns, från konfigurationsfilen.|
|MemoryLimitLow|Minne: Minnesgräns låg|Byte|Medel|Låg minnesgräns, från konfigurationsfilen.|
|MemoryLimitVertiPaq|Minne: Minnesgräns VertiPaq|Byte|Medel|Minnesintern gräns, från konfigurationsfilen.|
|MemoryUsage|Minne: Minnesanvändning|Byte|Medel|Minnesanvändning för serverprocessen som används för att beräkna tydligare minnespris. Lika med räknare Process\PrivateBytes plus storleken på minnesmappade data och ignorerar minne, som har mappats eller allokerats av den minnesintern Analysmotorn (VertiPaq) utöver motorns minnesgräns.|
|Kvot|Minne: Kvot|Byte|Medel|Aktuella minneskvoten, i byte. Minneskvoten är även känd som en minnesreservation för bevilja eller minne.|
|QuotaBlocked|Minne: Kvot blockerad|Antal|Medel|Aktuella antalet kvotbegäranden som blockeras tills andra minneskvoter.|
|VertiPaqNonpaged|Minne: VertiPaq oväxlat|Byte|Medel|Byte av minne låst i arbetsminnet för användning av InMemory-motorn.|
|VertiPaqPaged|Minne: VertiPaq växlat|Byte|Medel|Bytes med växlingsbart minne som används för data i minnet.|
|ProcessingPoolJobQueueLength|Bearbetning av poolen Jobbkölängd|Antal|Medel|Antal icke-I/O-jobb i kön för bearbetningstrådpoolen.|
|RowsConvertedPerSec|Bearbetar: Rader konverterade per sekund|CountPerSecond|Medel|Frekvensen som rader konverteras under bearbetning.|
|RowsReadPerSec|Bearbetar: Rader lästa per sekund|CountPerSecond|Medel|Frekvensen som rader läses från alla relationella databaser.|
|RowsWrittenPerSec|Bearbetar: Rader skrivna per sekund|CountPerSecond|Medel|Frekvensen som rader skrivs vid bearbetning.|
|qpu_metric|QPU|Antal|Medel|QPU. Intervall 0-100 för S1, 0-200 för S2 och 0-400 för S4|
|QueryPoolBusyThreads|Upptagna trådar för Frågepoolen|Antal|Medel|Antal upptagna trådar i frågetrådspoolen.|
|SuccessfullConnectionsPerSec|Lyckade anslutningar Per sekund|CountPerSecond|Medel|Hastighet för lyckade anslutningsavslutningar.|
|CommandPoolBusyThreads|Trådar: Kommandot upptagna trådar i kommandopoolen|Antal|Medel|Antal upptagna trådar i kommandotrådspoolen.|
|CommandPoolIdleThreads|Trådar: Kommandot frågepoolen inaktiva trådar|Antal|Medel|Antal inaktiva trådar i kommandotrådspoolen.|
|LongParsingBusyThreads|Trådar: Lång parsning upptagna trådar|Antal|Medel|Antal upptagna trådar i den lång parsningstrådspoolen.|
|LongParsingIdleThreads|Trådar: Lång parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i den lång parsningstrådspoolen.|
|LongParsingJobQueueLength|Trådar: Lång parsning jobbkölängd|Antal|Medel|Antal jobb i kön för den lång parsningstrådspoolen.|
|ProcessingPoolIOJobQueueLength|Trådar: Bearbetningspoolen i/o-jobbkölängd|Antal|Medel|Antal i/o-jobb i kön för bearbetningstrådpoolen.|
|ProcessingPoolBusyIOJobThreads|Trådar: Bearbetningspoolen upptagen i/o-jobbtrådar|Antal|Medel|Antal trådar som kör i/o-jobb i bearbetningstrådpoolen.|
|ProcessingPoolBusyNonIOThreads|Trådar: Bearbetning av poolen upptagen icke-I/O-trådar|Antal|Medel|Antal trådar som kör icke-I/O-jobb i bearbetningstrådpoolen.|
|ProcessingPoolIdleIOJobThreads|Trådar: Bearbetningspoolen inaktiva i/o-jobbtrådar|Antal|Medel|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|
|ProcessingPoolIdleNonIOThreads|Trådar: Bearbetning av poolen inaktiva icke-I/O-trådar|Antal|Medel|Antal inaktiva trådar i bearbetningstrådpoolen som är dedikerade till icke-I/O-jobb.|
|QueryPoolIdleThreads|Trådar: Frågepoolen inaktiva trådar|Antal|Medel|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|
|QueryPoolJobQueueLength|Trådar: Fråga efter pool jobbkölängd|Antal|Medel|Antal jobb i kön för frågetrådspoolen.|
|ShortParsingBusyThreads|Trådar: Kort parsning upptagna trådar|Antal|Medel|Antal upptagna trådar i den korta parsningstrådpoolen.|
|ShortParsingIdleThreads|Trådar: Kort parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i den korta parsningstrådpoolen.|
|ShortParsingJobQueueLength|Trådar: Kort parsning jobbkölängd|Antal|Medel|Antal jobb i kön för den korta parsningstrådpoolen.|
|TotalConnectionFailures|Totalt antal misslyckade anslutningar|Antal|Medel|Antalet misslyckade anslutningsförsök.|
|TotalConnectionRequests|Total antal anslutningsbegäranden|Antal|Medel|Total antal anslutningsbegäranden. |

## <a name="next-steps"></a>Nästa steg
[Övervaka i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Mått i Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Mått i Azure Monitor REST-API](https://msdn.microsoft.com/library/azure/dn931930.aspx)