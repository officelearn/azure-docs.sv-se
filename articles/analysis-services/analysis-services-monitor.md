---
title: Övervaka Azure Analysis Services server mått | Microsoft Docs
description: Lär dig hur du övervakar Analysis Services server mått i Azure Portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5431dd74629b9ed76a6a072d8ada286ce71a7633
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596078"
---
# <a name="monitor-server-metrics"></a>Övervaka servermått

Analysis Services tillhandahåller mått i Azure Metrics Explorer, ett kostnads fritt verktyg i portalen, som hjälper dig att övervaka servrarnas prestanda och hälsa. Du kan till exempel övervaka minne och CPU-användning, antal klient anslutningar och fråga resursförbrukning. Analysis Services använder samma övervaknings ramverk som de flesta andra Azure-tjänster. Mer information finns i [komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md).

Använd [Azure Monitor](../azure-monitor/overview.md)för att utföra mer djupgående diagnostik, spåra prestanda och identifiera trender för flera tjänst resurser i en resurs grupp eller prenumeration. Azure Monitor (tjänst) kan resultera i en fakturerings bar tjänst.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Övervaka mått för en Analysis Services server

1. I Azure Portal väljer du **mått**.

    ![Övervaka i Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. I **tillgängliga mått**väljer du de mått som ska ingå i diagrammet. 

    ![Övervaka diagram](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Server mått

Använd den här tabellen för att avgöra vilka mått som passar bäst för ditt övervaknings scenario. Endast mått för samma enhet kan visas i samma diagram.

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Kölängd för kommando bassäng|Count|Average|Antal jobb i kön för kommando tråds poolen.|
|CurrentConnections|Anslutning: Aktuella anslutningar|Count|Average|Aktuellt antal upprättade klient anslutningar.|
|CurrentUserSessions|Aktuella användarsessioner|Count|Average|Aktuellt antal användarsessioner som har upprättats.|
|mashup_engine_memory_metric|M motor minne|Byte|Average|Minnes användning per kombinations motor processer|
|mashup_engine_qpu_metric|M-motor QPU|Count|Average|QPU användning av kombinations motor processer|
|memory_metric|Minne|Byte|Average|Minnesoptimerade. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|
|memory_thrashing_metric|Nedskräpning för minne|Percent|Average|Genomsnittligt minne nedskräpning.|
|CleanerCurrentPrice|Minne: Renare aktuellt pris|Count|Average|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|
|CleanerMemoryNonshrinkable|Minne: Det går inte att krympa rengörings minnet|Byte|Average|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|
|CleanerMemoryShrinkable|Minne: Krympnings minne|Byte|Average|Mängden minne i byte som kan rensas av bakgrunds rensaren.|
|MemoryLimitHard|Minne: Minnes gräns hård|Byte|Average|Hård minnes gräns, från konfigurations filen.|
|MemoryLimitHigh|Minne: Hög minnes gräns|Byte|Average|Hög minnes gräns, från konfigurations filen.|
|MemoryLimitLow|Minne: Låg minnes gräns|Byte|Average|Låg minnes gräns, från konfigurations filen.|
|MemoryLimitVertiPaq|Minne: Minnes gräns VertiPaq|Byte|Average|Minnes intern gräns, från konfigurations filen.|
|MemoryUsage|Minne: Minnesanvändning|Byte|Average|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar vilket minne som har mappats eller allokerats av en minnes intern analys motor (VertiPaq) utöver motorns minnes gräns.|
|Kvot|Minne: Kvot|Byte|Average|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|
|QuotaBlocked|Minne: Blockerad kvot|Count|Average|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|
|VertiPaqNonpaged|Minne: VertiPaq är inte växlat|Byte|Average|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|
|VertiPaqPaged|Minne: VertiPaq-växlat|Byte|Average|Byte av växlings Bart minne som används för minnes intern data.|
|ProcessingPoolJobQueueLength|Bearbetar Kölängd för poolen|Count|Average|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|
|RowsConvertedPerSec|Bearbetar: Konverterade rader per sekund|CountPerSecond|Average|Antal rader som konverterats under bearbetning.|
|RowsReadPerSec|Bearbetar: Lästa rader per sekund|CountPerSecond|Average|Antalet rader som läses från alla Relations databaser.|
|RowsWrittenPerSec|Bearbetar: Skrivna rader per sekund|CountPerSecond|Average|Antal rader som skrivs under bearbetningen.|
|qpu_metric|QPU|Count|Average|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|
|QueryPoolBusyThreads|Upptagna trådar för frågeprocessorn|Count|Average|Antalet upptagna trådar i trådpoolen.|
|SuccessfullConnectionsPerSec|Lyckade anslutningar per sekund|CountPerSecond|Average|Antal slutförda slut för ande av anslutningar.|
|CommandPoolBusyThreads|Konversation Upptagna trådar för kommando pool|Count|Average|Antalet upptagna trådar i kommando tråds-poolen.|
|CommandPoolIdleThreads|Konversation Inaktiva trådar för kommando pool|Count|Average|Antal inaktiva trådar i kommando tråds-poolen.|
|LongParsingBusyThreads|Konversation Lång parsning av upptagna trådar|Count|Average|Antalet upptagna trådar i den långa parsande trådpoolen.|
|LongParsingIdleThreads|Konversation Långa parsar inaktiva trådar|Count|Average|Antalet inaktiva trådar i den långa parsande trådpoolen.|
|LongParsingJobQueueLength|Konversation Lång parsning av jobb Kölängd|Count|Average|Antal jobb i kön för den långa parsande trådpoolen.|
|ProcessingPoolIOJobQueueLength|Konversation Processor längd I/O-jobbkö|Count|Average|Antalet I/O-jobb i kön för bearbetning av trådpoolen.|
|ProcessingPoolBusyIOJobThreads|Konversation Processors upptagna I/O-jobb trådar|Count|Average|Antal trådar som kör i/O-jobb i bearbetnings trådens pool.|
|ProcessingPoolBusyNonIOThreads|Konversation Processor belastning är upptagen för icke-I/O-trådar|Count|Average|Antal trådar som kör icke-I/O-jobb i bearbetningen av trådpoolen.|
|ProcessingPoolIdleIOJobThreads|Konversation Bearbetar inaktiva I/O-jobb trådar|Count|Average|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|
|ProcessingPoolIdleNonIOThreads|Konversation Inaktiva icke-I/O-trådar som behandlar poolen|Count|Average|Antalet inaktiva trådar i bearbetnings trådens pool som är dedikerad till icke-I/O-jobb.|
|QueryPoolIdleThreads|Konversation Inaktiva trådar för frågenod|Count|Average|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|
|QueryPoolJobQueueLength|Konversation Kölängd för jobbkö|Count|Average|Antal jobb i kön för trådpoolen för Query.|
|ShortParsingBusyThreads|Konversation Kort parsning upptagna trådar|Count|Average|Antalet upptagna trådar i den kort parsar trådpoolen.|
|ShortParsingIdleThreads|Konversation Korta parsar inaktiva trådar|Count|Average|Antal inaktiva trådar i kort parsar trådpoolen.|
|ShortParsingJobQueueLength|Konversation Kort parsning av jobb Kölängd|Count|Average|Antal jobb i kö för kort parsar trådpoolen.|
|TotalConnectionFailures|Totalt antal anslutnings problem|Count|Average|Totalt antal misslyckade anslutnings försök.|
|TotalConnectionRequests|Totalt antal anslutnings begär Anden|Count|Average|Totalt antal anslutnings begär Anden. |

## <a name="next-steps"></a>Nästa steg
[Översikt över Azure Monitor](../azure-monitor/overview.md)      
[Komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md)      
[Mått i Azure Monitor REST API](/rest/api/monitor/metrics)
