---
title: Övervaka Azure Analysis Services server mått | Microsoft Docs
description: Lär dig hur Analysis Services använder Azure Metrics Explorer, ett kostnads fritt verktyg i portalen, för att hjälpa dig att övervaka serverns prestanda och hälsa.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aaa3a6d128fe7dd466f6f60ab515f05fa38ba63b
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84690384"
---
# <a name="monitor-server-metrics"></a>Övervaka servermått

Analysis Services tillhandahåller mått i Azure Metrics Explorer, ett kostnads fritt verktyg i portalen, som hjälper dig att övervaka servrarnas prestanda och hälsa. Du kan till exempel övervaka minne och CPU-användning, antal klient anslutningar och fråga resursförbrukning. Analysis Services använder samma övervaknings ramverk som de flesta andra Azure-tjänster. Mer information finns i [komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md).

Använd [Azure Monitor](../azure-monitor/overview.md)för att utföra mer djupgående diagnostik, spåra prestanda och identifiera trender för flera tjänst resurser i en resurs grupp eller prenumeration. Azure Monitor (tjänst) kan resultera i en fakturerings bar tjänst.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Övervaka mått för en Analysis Services server

1. I Azure Portal väljer du **mått**.

    ![Övervaka i Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. I **mått**väljer du de mått som ska ingå i diagrammet. 

    ![Övervaka diagram](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Server mått

Använd den här tabellen för att avgöra vilka mått som passar bäst för ditt övervaknings scenario. Endast mått för samma enhet kan visas i samma diagram.

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Kölängd för kommando bassäng|Antal|Medel|Antal jobb i kön för kommando tråds poolen.|
|CurrentConnections|Anslutning: aktuella anslutningar|Antal|Medel|Aktuellt antal upprättade klient anslutningar.|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medel|Aktuellt antal användarsessioner som har upprättats.|
|mashup_engine_memory_metric|M motor minne|Byte|Medel|Minnes användning per kombinations motor processer|
|mashup_engine_qpu_metric|M-motor QPU|Antal|Medel|QPU användning av kombinations motor processer|
|memory_metric|Minne|Byte|Medel|Memory. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|
|memory_thrashing_metric|Minnesförslöing|Procent|Medel|Genomsnittligt minne nedskräpning.|
|CleanerCurrentPrice|Minne: aktuellt pris för renare|Antal|Medel|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|
|CleanerMemoryNonshrinkable|Minne: det går inte att krympa rengörings minnet|Byte|Medel|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|
|CleanerMemoryShrinkable|Minne: krympnings utrymme för rensning|Byte|Medel|Mängden minne i byte som kan rensas av bakgrunds rensaren.|
|MemoryLimitHard|Minne: minnes gräns hårt|Byte|Medel|Hård minnes gräns, från konfigurations filen.|
|MemoryLimitHigh|Minne: minnes gräns hög|Byte|Medel|Hög minnes gräns, från konfigurations filen.|
|MemoryLimitLow|Minne: minnes gräns låg|Byte|Medel|Låg minnes gräns, från konfigurations filen.|
|MemoryLimitVertiPaq|Minne: minnes gräns VertiPaq|Byte|Medel|Minnes intern gräns, från konfigurations filen.|
|MemoryUsage|Minne: minnes användning|Byte|Medel|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar vilket minne som har mappats eller allokerats av en minnes intern analys motor (VertiPaq) utöver motorns minnes gräns.|
|private_bytes_metric|Privata byte |Byte|Medel|Den totala mängden minne som processerna för Analysis Services motor processen och kombinations programmet har allokerat, inklusive minne som delas med andra processer.|
|virtual_bytes_metric|Virtuella byte |Byte|Medel|Den aktuella storleken för det virtuella adress utrymmet som Analysis Services motor process-och kombinations programmets process processer använder.|
|mashup_engine_private_bytes_metric|M motor privata byte |Byte|Medel|Den totala mängden minnes kombinations programmets behållar processer har allokerats, inte inklusive minne som delas med andra processer.|
|mashup_engine_virtual_bytes_metric|Virtuella byte i M-motor |Byte|Medel|Den aktuella storleken för processerna för kombinations behållare för virtuella adress utrymmen använder.|
|Kvot|Minne: kvot|Byte|Medel|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|
|QuotaBlocked|Minne: kvot blockerad|Antal|Medel|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|
|VertiPaqNonpaged|Minne: VertiPaq som inte är växlat|Byte|Medel|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|
|VertiPaqPaged|Minne: VertiPaq växlat|Byte|Medel|Byte av växlings Bart minne som används för minnes intern data.|
|ProcessingPoolJobQueueLength|Bearbetar Kölängd för poolen|Antal|Medel|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|
|RowsConvertedPerSec|Bearbetar: rader konverterade per sekund|CountPerSecond|Medel|Antal rader som konverterats under bearbetning.|
|RowsReadPerSec|Bearbetar: rader lästa per sekund|CountPerSecond|Medel|Antalet rader som läses från alla Relations databaser.|
|RowsWrittenPerSec|Bearbetar: rader skrivna per sekund|CountPerSecond|Medel|Antal rader som skrivs under bearbetningen.|
|qpu_metric|QPU|Antal|Medel|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|
|QueryPoolBusyThreads|Upptagna trådar för frågeprocessorn|Antal|Medel|Antalet upptagna trådar i trådpoolen.|
|SuccessfullConnectionsPerSec|Lyckade anslutningar per sekund|CountPerSecond|Medel|Antal slutförda slut för ande av anslutningar.|
|CommandPoolBusyThreads|Trådar: pool med upptagna kommando trådar|Antal|Medel|Antalet upptagna trådar i kommando tråds-poolen.|
|CommandPoolIdleThreads|Trådar: inaktiva trådar för kommando pool|Antal|Medel|Antal inaktiva trådar i kommando tråds-poolen.|
|LongParsingBusyThreads|Trådar: lång parsning av upptagna trådar|Antal|Medel|Antalet upptagna trådar i den långa parsande trådpoolen.|
|LongParsingIdleThreads|Trådar: inaktiva trådar för lång parsning|Antal|Medel|Antalet inaktiva trådar i den långa parsande trådpoolen.|
|LongParsingJobQueueLength|Trådar: lång parsning av jobb Kölängd|Antal|Medel|Antal jobb i kön för den långa parsande trådpoolen.|
|ProcessingPoolIOJobQueueLength|Trådar: bearbeta pool I/O-jobb Kölängd|Antal|Medel|Antalet I/O-jobb i kön för bearbetning av trådpoolen.|
|ProcessingPoolBusyIOJobThreads|Trådar: bearbetning av pool upptagna I/O-jobb trådar|Antal|Medel|Antal trådar som kör i/O-jobb i bearbetnings trådens pool.|
|ProcessingPoolBusyNonIOThreads|Trådar: bearbetning av pool upptagna icke-I/O-trådar|Antal|Medel|Antal trådar som kör icke-I/O-jobb i bearbetningen av trådpoolen.|
|ProcessingPoolIdleIOJobThreads|Trådar: behandlar inaktiva I/O-jobb trådar för poolen|Antal|Medel|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|
|ProcessingPoolIdleNonIOThreads|Trådar: behandlar inaktiva icke-I/O-trådar som behandlar poolen|Antal|Medel|Antalet inaktiva trådar i bearbetnings trådens pool som är dedikerad till icke-I/O-jobb.|
|QueryPoolIdleThreads|Trådar: inaktiva trådar i lagringspoolen|Antal|Medel|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|
|QueryPoolJobQueueLength|Trådar: Kölängd för jobbkö|Antal|Medel|Antal jobb i kön för trådpoolen för Query.|
|ShortParsingBusyThreads|Trådar: kort parsning upptagna trådar|Antal|Medel|Antalet upptagna trådar i den kort parsar trådpoolen.|
|ShortParsingIdleThreads|Trådar: kort parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i kort parsar trådpoolen.|
|ShortParsingJobQueueLength|Trådar: kort parsning av jobb Kölängd|Antal|Medel|Antal jobb i kö för kort parsar trådpoolen.|
|TotalConnectionFailures|Totalt antal anslutnings problem|Antal|Medel|Totalt antal misslyckade anslutnings försök.|
|TotalConnectionRequests|Totalt antal anslutnings begär Anden|Antal|Medel|Totalt antal anslutnings begär Anden. |

## <a name="next-steps"></a>Nästa steg
[Översikt över Azure Monitor](../azure-monitor/overview.md)      
[Komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md)      
[Mått i Azure Monitor REST API](/rest/api/monitor/metrics)
