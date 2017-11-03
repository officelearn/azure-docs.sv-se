---
title: "Övervaka Azure Analysis Services-server | Microsoft Docs"
description: "Lär dig hur du övervakar Analysis Services-serverstatistik en i Azure-portalen."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/14/2017
ms.author: owend
ms.openlocfilehash: f9b32029f0a7065fff73ddb6417fc5c1c7e658a5
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="monitor-server-metrics"></a>Övervakaren serverstatistik

Analysis Services innehåller mått för att övervaka prestanda och hälsotillståndet för dina servrar. Övervaka till exempel minne och CPU-användning, antalet klientanslutningar och resursanvändningen för frågan. Analysis Services använder samma övervakning framework som de flesta andra Azure-tjänster. Läs mer i [mått i Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Om du vill utföra mer detaljerad diagnostik spåra prestanda och identifiera trender över flera serviceresurser i en resursgrupp eller prenumeration kan använda [Azure-Monitor](https://azure.microsoft.com/services/monitor/). Azure-Monitor (service) kan resultera i en fakturerbar tjänst.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Övervaka mätvärden för en Analysis Services-server

1. Välj i Azure-portalen **mått**.

    ![Övervaka i Azure-portalen](./media/analysis-services-monitor/aas-monitor-portal.png)

2. I **tillgängliga mått**, välja mått ska ingå i diagrammet. 

    ![Övervakare för diagram](./media/analysis-services-monitor/aas-monitor-chart.png)

## <a name="server-metrics"></a>Serverstatistik
Använd den här tabellen för att avgöra vilka mått som är bäst för ditt scenario för övervakning. Endast mätvärden för samma enhet kan visas på samma schema.

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Kölängd för kommandot poolen jobb|Antal|Genomsnittlig|Antal jobb i kö i trådpoolen för kommandot.|
|CurrentConnections|Anslutning: Aktuella anslutningar|Antal|Genomsnittlig|Aktuellt antal klientanslutningar.|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Genomsnittlig|Aktuellt antal användarsessioner som har upprättats.|
|mashup_engine_memory_metric|M-motorn minne|Byte|Genomsnittlig|Minnesanvändning för mashup-motorn processer|
|mashup_engine_qpu_metric|M-motorn QPU|Antal|Genomsnittlig|QPU användning av mashup-motorn processer|
|memory_metric|Minne|Byte|Genomsnittlig|Minne. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|
|memory_thrashing_metric|Minne knattrar|Procent|Genomsnittlig|Genomsnittlig minne knattrar.|
|CleanerCurrentPrice|Minne: Tydligare aktuella pris|Antal|Genomsnittlig|Aktuella priset minne, $ byte/tidsvärdet, normaliserad till 1000.|
|CleanerMemoryNonshrinkable|Minne: Tydligare minne nonshrinkable|Byte|Genomsnittlig|Mängden minne i byte som inte omfattas av rensning av bakgrunden ett rengöringsband.|
|CleanerMemoryShrinkable|Minne: Tydligare minne krympning|Byte|Genomsnittlig|Mängden minne i byte, föremål rensning av bakgrunden ett rengöringsband.|
|MemoryLimitHard|Minne: Minnesgränsen hårddisken|Byte|Genomsnittlig|Hårda minnesgränsen, från konfigurationsfilen.|
|MemoryLimitHigh|Minne: Minne begränsa hög|Byte|Genomsnittlig|Hög minnesgränsen, från konfigurationsfilen.|
|MemoryLimitLow|Minne: Minne gränsen låg|Byte|Genomsnittlig|Minnesbrist gräns från konfigurationsfilen.|
|MemoryLimitVertiPaq|Minne: Minne gränsen VertiPaq|Byte|Genomsnittlig|I minnet gräns från konfigurationsfilen.|
|MemoryUsage|Minne: Minnesanvändning|Byte|Genomsnittlig|Mängden minne på server-processen som används för att beräkna tydligare minnespris. Lika med prestandaräknaren Process\PrivateBytes plus storleken på minnesmappade data, ignorerar något minne som har mappats eller allokerats av motorn i minnet analytics (VertiPaq) överskrider minnesgränsen motorn.|
|Kvot|Minne: kvot|Byte|Genomsnittlig|Aktuella minneskvoten, i byte. Minneskvoten kallas även en reservation för minne grant eller minne.|
|QuotaBlocked|: Minneskvoten blockeras|Antal|Genomsnittlig|Aktuellt antal begäranden om kvoten som blockeras tills andra minneskvoter frigörs.|
|VertiPaqNonpaged|Minne: VertiPaq växlingsbart systemminne|Byte|Genomsnittlig|Byte av minne låst i arbetsminnet för användning av motorn i minnet.|
|VertiPaqPaged|Minne: VertiPaq-växlat minne|Byte|Genomsnittlig|Antal byte växlingsbart minne som används för data i minnet.|
|ProcessingPoolJobQueueLength|Kölängd för bearbetning poolen jobb|Antal|Genomsnittlig|Antal icke-I/O-jobb i kö för bearbetningstrådpoolen.|
|RowsConvertedPerSec|Bearbetning: Rader konverteras per sek|CountPerSecond|Genomsnittlig|Antal rader konverteras under bearbetning.|
|RowsReadPerSec|Bearbetning: Läsa rader per sek|CountPerSecond|Genomsnittlig|Antal rader att läsa från alla relationsdatabaser.|
|RowsWrittenPerSec|Bearbetning: Rader som skrivs per sekund|CountPerSecond|Genomsnittlig|Antal rader skrivna under bearbetning.|
|qpu_metric|QPU|Antal|Genomsnittlig|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|
|QueryPoolBusyThreads|Frågan poolen upptagen trådar|Antal|Genomsnittlig|Antal upptagen trådar i trådpoolen för frågan.|
|SuccessfullConnectionsPerSec|Lyckade anslutningar Per sekund|CountPerSecond|Genomsnittlig|Hastighet för lyckade anslutningen slutföras.|
|CommandPoolBusyThreads|Trådar: Kommandot poolen upptagen trådar|Antal|Genomsnittlig|Antal upptagen trådar i trådpoolen för kommandot.|
|CommandPoolIdleThreads|Trådar: Kommandot poolen inaktiva trådar|Antal|Genomsnittlig|Antalet inaktiva trådar i trådpoolen för kommandot.|
|LongParsingBusyThreads|Trådar: Long parsning upptagen trådar|Antal|Genomsnittlig|Antal upptagen trådar i länge tolkning trådpoolen.|
|LongParsingIdleThreads|Trådar: Long parsning inaktiva trådar|Antal|Genomsnittlig|Antalet inaktiva trådar i länge tolkning trådpoolen.|
|LongParsingJobQueueLength|Trådar: Parsning länge jobbet Kölängd|Antal|Genomsnittlig|Antal jobb i kö för länge tolkning trådpoolen.|
|ProcessingPoolIOJobQueueLength|Trådar: Bearbetning av poolen Kölängd för i/o-jobb|Antal|Genomsnittlig|Antal i/o-jobb i kö för bearbetningstrådpoolen.|
|ProcessingPoolBusyIOJobThreads|Trådar: Bearbetning av poolen upptagen i/o-jobbet trådar|Antal|Genomsnittlig|Antal trådar som körs i/o-jobb i bearbetningstrådpoolen.|
|ProcessingPoolBusyNonIOThreads|Trådar: Bearbetning av poolen upptagen icke-I/O-trådar|Antal|Genomsnittlig|Antal trådar som icke-I/O-jobb som körs i bearbetningstrådpoolen.|
|ProcessingPoolIdleIOJobThreads|Trådar: Bearbetning av poolen inaktiv i/o-jobbet trådar|Antal|Genomsnittlig|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|
|ProcessingPoolIdleNonIOThreads|Trådar: Bearbetning av poolen inaktiv icke-I/O-trådar|Antal|Genomsnittlig|Antalet inaktiva trådar i bearbetningstrådpoolen som är dedikerad till icke-I/O-jobb.|
|QueryPoolIdleThreads|Trådar: Frågan poolen inaktiva trådar|Antal|Genomsnittlig|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|
|QueryPoolJobQueueLength|Trådar: Kölängd frågan poolen jobb|Antal|Genomsnittlig|Antal jobb i kö i trådpoolen för frågan.|
|ShortParsingBusyThreads|Trådar: Kort parsning upptagen trådar|Antal|Genomsnittlig|Antal upptagen trådar i kort tolkning trådpoolen.|
|ShortParsingIdleThreads|Trådar: Kort parsning inaktiva trådar|Antal|Genomsnittlig|Antalet inaktiva trådar i kort tolkning trådpoolen.|
|ShortParsingJobQueueLength|Trådar: Kort parsning jobbet Kölängd|Antal|Genomsnittlig|Antal jobb i kö för kort tolkning trådpoolen.|
|TotalConnectionFailures|Totalt antal anslutningsfel|Antal|Genomsnittlig|Totalt antal misslyckade anslutningsförsök.|
|TotalConnectionRequests|Totalt antal begäranden|Antal|Genomsnittlig|Totalt antal förfrågningar. |

## <a name="next-steps"></a>Nästa steg
[Övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Mått i Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Mått i Azure-Monitor REST-API](https://msdn.microsoft.com/library/azure/dn931930.aspx)