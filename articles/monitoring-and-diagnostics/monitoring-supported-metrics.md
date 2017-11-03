---
title: "Azure övervakaren mått - stöds mått per resurstypen | Microsoft Docs"
description: "Lista över tillgängliga för varje resurstyp av med Azure-Monitor mått."
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: ancav
ms.openlocfilehash: 05830547a5b8a24a59571edf6dd44d101b660189
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>Stöds mått med Azure-Monitor
Azure-Monitor finns flera sätt att interagera med statistik, inklusive diagram dem i portalen, komma åt dem via REST API eller fråga dem med PowerShell eller CLI. Nedan finns en fullständig lista över alla just nu med Azure-Monitor mått pipeline.

> [!NOTE]
> Andra mått kan finnas i portalen eller med äldre API: er. Listan innehåller endast tillgängliga med konsoliderade Azure-Monitor mått pipeline mått. Fråga efter och komma åt mått med dimensioner ska du använda den [2017-05-01-preview api-version](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|qpu_metric|QPU|Antal|Genomsnittlig|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|Inga dimensioner|
|memory_metric|Minne|Byte|Genomsnittlig|Minne. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|Inga dimensioner|
|TotalConnectionRequests|Totalt antal begäranden|Antal|Genomsnittlig|Totalt antal förfrågningar. Dessa är mottagna.|Inga dimensioner|
|SuccessfullConnectionsPerSec|Lyckade anslutningar Per sekund|CountPerSecond|Genomsnittlig|Hastighet för lyckade anslutningen slutföras.|Inga dimensioner|
|TotalConnectionFailures|Totalt antal anslutningsfel|Antal|Genomsnittlig|Totalt antal misslyckade anslutningsförsök.|Inga dimensioner|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Genomsnittlig|Aktuellt antal användarsessioner som har upprättats.|Inga dimensioner|
|QueryPoolBusyThreads|Frågan poolen upptagen trådar|Antal|Genomsnittlig|Antal upptagen trådar i trådpoolen för frågan.|Inga dimensioner|
|CommandPoolJobQueueLength|Kölängd för kommandot poolen jobb|Antal|Genomsnittlig|Antal jobb i kö i trådpoolen för kommandot.|Inga dimensioner|
|ProcessingPoolJobQueueLength|Kölängd för bearbetning poolen jobb|Antal|Genomsnittlig|Antal icke-I/O-jobb i kö för bearbetningstrådpoolen.|Inga dimensioner|
|CurrentConnections|Anslutning: Aktuella anslutningar|Antal|Genomsnittlig|Aktuellt antal klientanslutningar.|Inga dimensioner|
|CleanerCurrentPrice|Minne: Tydligare aktuella pris|Antal|Genomsnittlig|Aktuella priset minne, $ byte/tidsvärdet, normaliserad till 1000.|Inga dimensioner|
|CleanerMemoryShrinkable|Minne: Tydligare minne krympning|Byte|Genomsnittlig|Mängden minne i byte, föremål rensning av bakgrunden ett rengöringsband.|Inga dimensioner|
|CleanerMemoryNonshrinkable|Minne: Tydligare minne nonshrinkable|Byte|Genomsnittlig|Mängden minne i byte som inte omfattas av rensning av bakgrunden ett rengöringsband.|Inga dimensioner|
|MemoryUsage|Minne: Minnesanvändning|Byte|Genomsnittlig|Mängden minne på server-processen som används för att beräkna tydligare minnespris. Lika med prestandaräknaren Process\PrivateBytes plus storleken på minnesmappade data, ignoreras eventuella som mappats eller som har allokerats av xVelocity i minnet analytics motorn (VertiPaq) överskrider minnesgränsen för xVelocity-motorn.|Inga dimensioner|
|MemoryLimitHard|Minne: Minnesgränsen hårddisken|Byte|Genomsnittlig|Hårda minnesgränsen, från konfigurationsfilen.|Inga dimensioner|
|MemoryLimitHigh|Minne: Minne begränsa hög|Byte|Genomsnittlig|Hög minnesgränsen, från konfigurationsfilen.|Inga dimensioner|
|MemoryLimitLow|Minne: Minne gränsen låg|Byte|Genomsnittlig|Minnesbrist gräns från konfigurationsfilen.|Inga dimensioner|
|MemoryLimitVertiPaq|Minne: Minne gränsen VertiPaq|Byte|Genomsnittlig|I minnet gräns från konfigurationsfilen.|Inga dimensioner|
|Kvot|Minne: kvot|Byte|Genomsnittlig|Aktuella minneskvoten, i byte. Minneskvoten kallas även en reservation för minne grant eller minne.|Inga dimensioner|
|QuotaBlocked|: Minneskvoten blockeras|Antal|Genomsnittlig|Aktuellt antal begäranden om kvoten som blockeras tills andra minneskvoter frigörs.|Inga dimensioner|
|VertiPaqNonpaged|Minne: VertiPaq växlingsbart systemminne|Byte|Genomsnittlig|Byte av minne låst i arbetsminnet för användning av motorn i minnet.|Inga dimensioner|
|VertiPaqPaged|Minne: VertiPaq-växlat minne|Byte|Genomsnittlig|Antal byte växlingsbart minne som används för data i minnet.|Inga dimensioner|
|RowsReadPerSec|Bearbetning: Läsa rader per sek|CountPerSecond|Genomsnittlig|Antal rader att läsa från alla relationsdatabaser.|Inga dimensioner|
|RowsConvertedPerSec|Bearbetning: Rader konverteras per sek|CountPerSecond|Genomsnittlig|Antal rader konverteras under bearbetning.|Inga dimensioner|
|RowsWrittenPerSec|Bearbetning: Rader som skrivs per sekund|CountPerSecond|Genomsnittlig|Antal rader skrivna under bearbetning.|Inga dimensioner|
|CommandPoolBusyThreads|Trådar: Kommandot poolen upptagen trådar|Antal|Genomsnittlig|Antal upptagen trådar i trådpoolen för kommandot.|Inga dimensioner|
|CommandPoolIdleThreads|Trådar: Kommandot poolen inaktiva trådar|Antal|Genomsnittlig|Antalet inaktiva trådar i trådpoolen för kommandot.|Inga dimensioner|
|LongParsingBusyThreads|Trådar: Long parsning upptagen trådar|Antal|Genomsnittlig|Antal upptagen trådar i länge tolkning trådpoolen.|Inga dimensioner|
|LongParsingIdleThreads|Trådar: Long parsning inaktiva trådar|Antal|Genomsnittlig|Antalet inaktiva trådar i länge tolkning trådpoolen.|Inga dimensioner|
|LongParsingJobQueueLength|Trådar: Parsning länge jobbet Kölängd|Antal|Genomsnittlig|Antal jobb i kö för länge tolkning trådpoolen.|Inga dimensioner|
|ProcessingPoolBusyIOJobThreads|Trådar: Bearbetning av poolen upptagen i/o-jobbet trådar|Antal|Genomsnittlig|Antal trådar som körs i/o-jobb i bearbetningstrådpoolen.|Inga dimensioner|
|ProcessingPoolBusyNonIOThreads|Trådar: Bearbetning av poolen upptagen icke-I/O-trådar|Antal|Genomsnittlig|Antal trådar som icke-I/O-jobb som körs i bearbetningstrådpoolen.|Inga dimensioner|
|ProcessingPoolIOJobQueueLength|Trådar: Bearbetning av poolen Kölängd för i/o-jobb|Antal|Genomsnittlig|Antal i/o-jobb i kö för bearbetningstrådpoolen.|Inga dimensioner|
|ProcessingPoolIdleIOJobThreads|Trådar: Bearbetning av poolen inaktiv i/o-jobbet trådar|Antal|Genomsnittlig|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|Inga dimensioner|
|ProcessingPoolIdleNonIOThreads|Trådar: Bearbetning av poolen inaktiv icke-I/O-trådar|Antal|Genomsnittlig|Antalet inaktiva trådar i bearbetningstrådpoolen som är dedikerad till icke-I/O-jobb.|Inga dimensioner|
|QueryPoolIdleThreads|Trådar: Frågan poolen inaktiva trådar|Antal|Genomsnittlig|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|Inga dimensioner|
|QueryPoolJobQueueLength|Trådar: Frågan poolen jobbet kön lengt|Antal|Genomsnittlig|Antal jobb i kö i trådpoolen för frågan.|Inga dimensioner|
|ShortParsingBusyThreads|Trådar: Kort parsning upptagen trådar|Antal|Genomsnittlig|Antal upptagen trådar i kort tolkning trådpoolen.|Inga dimensioner|
|ShortParsingIdleThreads|Trådar: Kort parsning inaktiva trådar|Antal|Genomsnittlig|Antalet inaktiva trådar i kort tolkning trådpoolen.|Inga dimensioner|
|ShortParsingJobQueueLength|Trådar: Kort parsning jobbet Kölängd|Antal|Genomsnittlig|Antal jobb i kö för kort tolkning trådpoolen.|Inga dimensioner|
|memory_thrashing_metric|Minne knattrar|Procent|Genomsnittlig|Genomsnittlig minne knattrar.|Inga dimensioner|
|mashup_engine_qpu_metric|M-motorn QPU|Antal|Genomsnittlig|QPU användning av mashup-motorn processer|Inga dimensioner|
|mashup_engine_memory_metric|M-motorn minne|Byte|Genomsnittlig|Minnesanvändning för mashup-motorn processer|Inga dimensioner|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalRequests|Totalt antal Gateway-begäranden|Antal|Totalt|Antal begäranden för gateway|Plats, värdnamn|
|SuccessfulRequests|Rätt Gateway-begäranden|Antal|Totalt|Antalet begäranden som lyckats gateway|Plats, värdnamn|
|UnauthorizedRequests|Obehörig Gateway-begäranden|Antal|Totalt|Antal begäranden för obehörig gateway|Plats, värdnamn|
|FailedRequests|Gateway för misslyckade begäranden|Antal|Totalt|Antalet fel i gateway-begäranden|Plats, värdnamn|
|OtherRequests|Andra Gateway-begäranden|Antal|Totalt|Antal andra gateway-begäranden|Plats, värdnamn|
|Varaktighet|Varaktighet för Gateway-förfrågningar|millisekunder|Genomsnittlig|Övergripande varaktighet för Gateway-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Kapacitet (förhandsgranskning)|Procent|Maximalt|Användning mått för ApiManagement service|Plats|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Inga dimensioner|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CoreCount|Antal dedikerade kärnor|Antal|Totalt|Totalt antal dedikerade kärnor i batch-kontot|Inga dimensioner|
|TotalNodeCount|Antalet dedicerade noder|Antal|Totalt|Totalt antal dedikerade noder i batch-kontot|Inga dimensioner|
|LowPriorityCoreCount|Antal för LowPriority kärnor|Antal|Totalt|Totalt antal kärnor låg prioritet i batch-kontot|Inga dimensioner|
|TotalLowPriorityNodeCount|Antalet noder med låg prioritet|Antal|Totalt|Totalt antal noder låg prioritet i batch-kontot|Inga dimensioner|
|CreatingNodeCount|Skapa antalet noder|Antal|Totalt|Antalet noder som skapas|Inga dimensioner|
|StartingNodeCount|Starta antalet noder|Antal|Totalt|Antalet noder som startar|Inga dimensioner|
|WaitingForStartTaskNodeCount|Väntar på för starta uppgiften antalet noder|Antal|Totalt|Antalet noder som väntar på att starta aktiviteten för att slutföra|Inga dimensioner|
|StartTaskFailedNodeCount|Starta kunde inte antalet noder|Antal|Totalt|Antalet noder där aktiviteten starta misslyckades|Inga dimensioner|
|IdleNodeCount|Antalet inaktiva noder|Antal|Totalt|Antalet inaktiva noder|Inga dimensioner|
|OfflineNodeCount|Antalet offline noder|Antal|Totalt|Antalet noder som är offline|Inga dimensioner|
|RebootingNodeCount|Starta om antalet noder|Antal|Totalt|Antalet noder på att startas om|Inga dimensioner|
|ReimagingNodeCount|Återställning av avbildning antalet noder|Antal|Totalt|Antalet noder för återställning av avbildning|Inga dimensioner|
|RunningNodeCount|Kör antalet noder|Antal|Totalt|Antalet noder som körs|Inga dimensioner|
|LeavingPoolNodeCount|Om du lämnar antalet noder för poolen|Antal|Totalt|Antalet noder som lämnar poolen|Inga dimensioner|
|UnusableNodeCount|Antalet oanvändbar noder|Antal|Totalt|Antalet noder som inte kan användas|Inga dimensioner|
|PreemptedNodeCount|Antalet frånkopplas noder|Antal|Totalt|Antalet frånkopplas noder|Inga dimensioner|
|TaskStartEvent|Aktiviteten starta händelser|Antal|Totalt|Totalt antal aktiviteter som har startats|Inga dimensioner|
|TaskCompleteEvent|Uppgift slutförd händelser|Antal|Totalt|Totalt antal aktiviteter som har slutförts|Inga dimensioner|
|TaskFailEvent|Uppgiften misslyckas händelser|Antal|Totalt|Totalt antal aktiviteter som har slutförts i ett felaktigt tillstånd|Inga dimensioner|
|PoolCreateEvent|Poolen skapa händelser|Antal|Totalt|Totalt antal pooler som har skapats|Inga dimensioner|
|PoolResizeStartEvent|Poolen storleksändring Start händelser|Antal|Totalt|Totalt antal poolen storlek som har startat|Inga dimensioner|
|PoolResizeCompleteEvent|Poolen storleksändring slutförd händelser|Antal|Totalt|Totalt antal poolen storlek som har slutförts|Inga dimensioner|
|PoolDeleteStartEvent|Poolen ta bort Start händelser|Antal|Totalt|Totalt antal poolen borttagningar som har startat|Inga dimensioner|
|PoolDeleteCompleteEvent|Poolen ta bort klar händelser|Antal|Totalt|Totalt antal poolen borttagningar som har slutförts|Inga dimensioner|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedclients|Anslutna klienter|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed|Totalt antal åtgärder|Antal|Totalt||Inga dimensioner|
|cachehits|Träffar i cache|Antal|Totalt||Inga dimensioner|
|cachemisses|Cachemissar|Antal|Totalt||Inga dimensioner|
|getcommands|Hämtar|Antal|Totalt||Inga dimensioner|
|setcommands|Anger|Antal|Totalt||Inga dimensioner|
|evictedkeys|Avlägsnade nycklar|Antal|Totalt||Inga dimensioner|
|totalkeys|Totalt antal nycklar|Antal|Maximalt||Inga dimensioner|
|expiredkeys|Utgångna nycklar|Antal|Totalt||Inga dimensioner|
|usedmemory|Använt minne|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss|Använt minne RSS|Byte|Maximalt||Inga dimensioner|
|serverLoad|Belastningen på servern|Procent|Maximalt||Inga dimensioner|
|cacheWrite|Cache-skrivåtgärder|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead|Läs i cache|BytesPerSecond|Maximalt||Inga dimensioner|
|PercentProcessorTime|Processor|Procent|Maximalt||Inga dimensioner|
|connectedclients0|Anslutna klienter (Fragmentera 0)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed0|Totalt antal åtgärder (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|cachehits0|Cacheträffar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|cachemisses0|Cachemissar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|getcommands0|Hämtar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|setcommands0|Anger (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|evictedkeys0|Avlägsnade nycklar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|totalkeys0|Totalt antal nycklar (Fragmentera 0)|Antal|Maximalt||Inga dimensioner|
|expiredkeys0|Utgångna nycklar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|usedmemory0|Använt minne (Fragmentera 0)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss0|Använt minne RSS (Fragmentera 0)|Byte|Maximalt||Inga dimensioner|
|serverLoad0|Belastningen på servern (Fragmentera 0)|Procent|Maximalt||Inga dimensioner|
|cacheWrite0|Cache-skrivning (Fragmentera 0)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead0|Cache Läs (Fragmentera 0)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime0|CPU (Fragmentera 0)|Procent|Maximalt||Inga dimensioner|
|connectedclients1|Anslutna klienter (Fragmentera 1)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed1|Totalt antal åtgärder (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|cachehits1|Cacheträffar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|cachemisses1|Cachemissar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|getcommands1|Hämtar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|setcommands1|Anger (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|evictedkeys1|Avlägsnade nycklar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|totalkeys1|Totalt antal nycklar (Fragmentera 1)|Antal|Maximalt||Inga dimensioner|
|expiredkeys1|Utgångna nycklar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|usedmemory1|Använt minne (Fragmentera 1)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss1|Använt minne RSS (Fragmentera 1)|Byte|Maximalt||Inga dimensioner|
|serverLoad1|Belastningen på servern (Fragmentera 1)|Procent|Maximalt||Inga dimensioner|
|cacheWrite1|Cache-skrivning (Fragmentera 1)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead1|Cache Läs (Fragmentera 1)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime1|CPU (Fragmentera 1)|Procent|Maximalt||Inga dimensioner|
|connectedclients2|Anslutna klienter (Fragmentera 2)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed2|Totalt antal åtgärder (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|cachehits2|Cacheträffar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|cachemisses2|Cachemissar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|getcommands2|Hämtar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|setcommands2|Anger (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|evictedkeys2|Avlägsnade nycklar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|totalkeys2|Totalt antal nycklar (Fragmentera 2)|Antal|Maximalt||Inga dimensioner|
|expiredkeys2|Utgångna nycklar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|usedmemory2|Använt minne (Fragmentera 2)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss2|Använt minne RSS (Fragmentera 2)|Byte|Maximalt||Inga dimensioner|
|serverLoad2|Belastningen på servern (Fragmentera 2)|Procent|Maximalt||Inga dimensioner|
|cacheWrite2|Cache-skrivning (Fragmentera 2)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead2|Cache Läs (Fragmentera 2)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime2|CPU (Fragmentera 2)|Procent|Maximalt||Inga dimensioner|
|connectedclients3|Anslutna klienter (Fragmentera 3)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed3|Totalt antal åtgärder (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|cachehits3|Cacheträffar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|cachemisses3|Cachemissar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|getcommands3|Hämtar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|setcommands3|Anger (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|evictedkeys3|Avlägsnade nycklar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|totalkeys3|Totalt antal nycklar (Fragmentera 3)|Antal|Maximalt||Inga dimensioner|
|expiredkeys3|Utgångna nycklar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|usedmemory3|Använt minne (Fragmentera 3)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss3|Använt minne RSS (Fragmentera 3)|Byte|Maximalt||Inga dimensioner|
|serverLoad3|Belastningen på servern (Fragmentera 3)|Procent|Maximalt||Inga dimensioner|
|cacheWrite3|Cache-skrivning (Fragmentera 3)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead3|Cache Läs (Fragmentera 3)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime3|CPU (Fragmentera 3)|Procent|Maximalt||Inga dimensioner|
|connectedclients4|Anslutna klienter (Fragmentera 4)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed4|Totalt antal åtgärder (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|cachehits4|Cacheträffar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|cachemisses4|Cachemissar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|getcommands4|Hämtar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|setcommands4|Anger (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|evictedkeys4|Avlägsnade nycklar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|totalkeys4|Totalt antal nycklar (Fragmentera 4)|Antal|Maximalt||Inga dimensioner|
|expiredkeys4|Utgångna nycklar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|usedmemory4|Använt minne (Fragmentera 4)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss4|Använt minne RSS (Fragmentera 4)|Byte|Maximalt||Inga dimensioner|
|serverLoad4|Belastningen på servern (Fragmentera 4)|Procent|Maximalt||Inga dimensioner|
|cacheWrite4|Cache-skrivning (Fragmentera 4)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead4|Cache Läs (Fragmentera 4)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime4|CPU (Fragmentera 4)|Procent|Maximalt||Inga dimensioner|
|connectedclients5|Anslutna klienter (Fragmentera 5)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed5|Totalt antal åtgärder (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|cachehits5|Cacheträffar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|cachemisses5|Cachemissar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|getcommands5|Hämtar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|setcommands5|Anger (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|evictedkeys5|Avlägsnade nycklar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|totalkeys5|Totalt antal nycklar (Fragmentera 5)|Antal|Maximalt||Inga dimensioner|
|expiredkeys5|Utgångna nycklar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|usedmemory5|Använt minne (Fragmentera 5)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss5|Använt minne RSS (Fragmentera 5)|Byte|Maximalt||Inga dimensioner|
|serverLoad5|Belastningen på servern (Fragmentera 5)|Procent|Maximalt||Inga dimensioner|
|cacheWrite5|Cache-skrivning (Fragmentera 5)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead5|Cache Läs (Fragmentera 5)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime5|CPU (Fragmentera 5)|Procent|Maximalt||Inga dimensioner|
|connectedclients6|Anslutna klienter (Fragmentera 6)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed6|Totalt antal åtgärder (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|cachehits6|Cacheträffar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|cachemisses6|Cachemissar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|getcommands6|Hämtar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|setcommands6|Anger (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|evictedkeys6|Avlägsnade nycklar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|totalkeys6|Totalt antal nycklar (Fragmentera 6)|Antal|Maximalt||Inga dimensioner|
|expiredkeys6|Utgångna nycklar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|usedmemory6|Använt minne (Fragmentera 6)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss6|Använt minne RSS (Fragmentera 6)|Byte|Maximalt||Inga dimensioner|
|serverLoad6|Belastningen på servern (Fragmentera 6)|Procent|Maximalt||Inga dimensioner|
|cacheWrite6|Cache-skrivning (Fragmentera 6)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead6|Cache Läs (Fragmentera 6)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime6|CPU (Fragmentera 6)|Procent|Maximalt||Inga dimensioner|
|connectedclients7|Anslutna klienter (Fragmentera 7)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed7|Totalt antal åtgärder (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|cachehits7|Cacheträffar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|cachemisses7|Cachemissar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|getcommands7|Hämtar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|setcommands7|Anger (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|evictedkeys7|Avlägsnade nycklar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|totalkeys7|Totalt antal nycklar (Fragmentera 7)|Antal|Maximalt||Inga dimensioner|
|expiredkeys7|Utgångna nycklar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|usedmemory7|Använt minne (Fragmentera 7)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss7|Använt minne RSS (Fragmentera 7)|Byte|Maximalt||Inga dimensioner|
|serverLoad7|Belastningen på servern (Fragmentera 7)|Procent|Maximalt||Inga dimensioner|
|cacheWrite7|Cache-skrivning (Fragmentera 7)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead7|Cache Läs (Fragmentera 7)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime7|CPU (Fragmentera 7)|Procent|Maximalt||Inga dimensioner|
|connectedclients8|Anslutna klienter (Fragmentera 8)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed8|Totalt antal åtgärder (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|cachehits8|Cacheträffar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|cachemisses8|Cachemissar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|getcommands8|Hämtar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|setcommands8|Anger (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|evictedkeys8|Avlägsnade nycklar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|totalkeys8|Totalt antal nycklar (Fragmentera 8)|Antal|Maximalt||Inga dimensioner|
|expiredkeys8|Utgångna nycklar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|usedmemory8|Använt minne (Fragmentera 8)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss8|Använt minne RSS (Fragmentera 8)|Byte|Maximalt||Inga dimensioner|
|serverLoad8|Belastningen på servern (Fragmentera 8)|Procent|Maximalt||Inga dimensioner|
|cacheWrite8|Cache-skrivning (Fragmentera 8)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead8|Cache Läs (Fragmentera 8)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime8|CPU (Fragmentera 8)|Procent|Maximalt||Inga dimensioner|
|connectedclients9|Anslutna klienter (Fragmentera 9)|Antal|Maximalt||Inga dimensioner|
|totalcommandsprocessed9|Totalt antal åtgärder (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|cachehits9|Cacheträffar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|cachemisses9|Cachemissar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|getcommands9|Hämtar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|setcommands9|Anger (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|evictedkeys9|Avlägsnade nycklar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|totalkeys9|Totalt antal nycklar (Fragmentera 9)|Antal|Maximalt||Inga dimensioner|
|expiredkeys9|Utgångna nycklar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|usedmemory9|Använt minne (Fragmentera 9)|Byte|Maximalt||Inga dimensioner|
|usedmemoryRss9|Använt minne RSS (Fragmentera 9)|Byte|Maximalt||Inga dimensioner|
|serverLoad9|Belastningen på servern (Fragmentera 9)|Procent|Maximalt||Inga dimensioner|
|cacheWrite9|Cache-skrivning (Fragmentera 9)|BytesPerSecond|Maximalt||Inga dimensioner|
|cacheRead9|Cache Läs (Fragmentera 9)|BytesPerSecond|Maximalt||Inga dimensioner|
|percentProcessorTime9|CPU (Fragmentera 9)|Procent|Maximalt||Inga dimensioner|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procentandel CPU|Procentandel CPU|Procent|Genomsnittlig|Procentandelen allokerat beräknings-enheter som för närvarande används av minst en virtuell dator.|Inga dimensioner|
|Nätverk i|Nätverk i|Byte|Totalt|Antal byte som tagits emot på alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik).|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte ut på alla nätverksgränssnitt som de virtuella datorerna (utgående trafik).|Inga dimensioner|
|Disk-lästa byte/sek|Diskläsning|BytesPerSecond|Genomsnittlig|Genomsnittlig byte som har lästs från disken under perioden.|Inga dimensioner|
|Disk-skrivna byte/s|Skrivning till disk|BytesPerSecond|Genomsnittlig|Genomsnittligt antal byte som skrivs till disk under perioden.|Inga dimensioner|
|Disk-lästa åtgärder/sek|Disk-lästa åtgärder/sek|CountPerSecond|Genomsnittlig|Diskläsning IOPS.|Inga dimensioner|
|Disk skrivåtgärder per sekund|Disk skrivåtgärder per sekund|CountPerSecond|Genomsnittlig|Diskskrivning IOPS.|Inga dimensioner|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalCalls|Totalt antal anrop|Antal|Totalt|Totalt antal anrop.|Inga dimensioner|
|SuccessfulCalls|Antal samtal|Antal|Totalt|Antal lyckade anrop.|Inga dimensioner|
|TotalErrors|Totalt antal fel|Antal|Totalt|Totalt antal anrop med felsvar (http-svar kod 4xx eller 5xx).|Inga dimensioner|
|BlockedCalls|Blockerade anrop|Antal|Totalt|Antal anrop som överskred hastighet eller kvotgränsen.|Inga dimensioner|
|ServerErrors|Serverfel|Antal|Totalt|Antal anrop med internt tjänstfel (http-svar kod 5xx).|Inga dimensioner|
|ClientErrors|Klientfel|Antal|Totalt|Antal anrop med klientfel sida (http-svar kod 4xx).|Inga dimensioner|
|DataIn|Data i|Byte|Totalt|Storleken på inkommande data i byte.|Inga dimensioner|
|DataOut|Ut data|Byte|Totalt|Storleken på utgående data i byte.|Inga dimensioner|
|Svarstid|Svarstid|Millisekunder|Genomsnittlig|Fördröjning i millisekunder.|Inga dimensioner|
|CharactersTranslated|Tecken som översätts|Antal|Totalt|Totalt antal tecken i inkommande begäran om text.|Inga dimensioner|
|SpeechSessionDuration|Tal varaktighet för sessionen|Sekunder|Totalt|Total varaktighet för tal-session i sekunder.|Inga dimensioner|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procentandel CPU|Procentandel CPU|Procent|Genomsnittlig|Procentandelen allokerat beräknings-enheter som för närvarande används av minst en virtuell dator|Inga dimensioner|
|Nätverk i|Nätverk i|Byte|Totalt|Antalet byte som tagits emot på alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antalet byte ut på alla nätverksgränssnitt som de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Disk lästa byte|Disk lästa byte|Byte|Totalt|Totalt antal byte som har lästs från disken under perioden|Inga dimensioner|
|Disk byte för skrivning|Disk byte för skrivning|Byte|Totalt|Totalt antal byte som skrivs till disk under perioden|Inga dimensioner|
|Disk-lästa åtgärder/sek|Disk-lästa åtgärder/sek|CountPerSecond|Genomsnittlig|Diskläsning IOPS|Inga dimensioner|
|Disk skrivåtgärder per sekund|Disk skrivåtgärder per sekund|CountPerSecond|Genomsnittlig|Diskskrivning IOPS|Inga dimensioner|
|CPU-kredit kvar|CPU-kredit kvar|Antal|Genomsnittlig|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|CPU-krediter som används|CPU-krediter som används|Antal|Genomsnittlig|Totalt antal krediter som används av den virtuella datorn|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procentandel CPU|Procentandel CPU|Procent|Genomsnittlig|Procentandelen allokerat beräknings-enheter som för närvarande används av minst en virtuell dator|Inga dimensioner|
|Nätverk i|Nätverk i|Byte|Totalt|Antalet byte som tagits emot på alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antalet byte ut på alla nätverksgränssnitt som de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Disk lästa byte|Disk lästa byte|Byte|Totalt|Totalt antal byte som har lästs från disken under perioden|Inga dimensioner|
|Disk byte för skrivning|Disk byte för skrivning|Byte|Totalt|Totalt antal byte som skrivs till disk under perioden|Inga dimensioner|
|Disk-lästa åtgärder/sek|Disk-lästa åtgärder/sek|CountPerSecond|Genomsnittlig|Diskläsning IOPS|Inga dimensioner|
|Disk skrivåtgärder per sekund|Disk skrivåtgärder per sekund|CountPerSecond|Genomsnittlig|Diskskrivning IOPS|Inga dimensioner|
|CPU-kredit kvar|CPU-kredit kvar|Antal|Genomsnittlig|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|CPU-krediter som används|CPU-krediter som används|Antal|Genomsnittlig|Totalt antal krediter som används av den virtuella datorn|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procentandel CPU|Procentandel CPU|Procent|Genomsnittlig|Procentandelen allokerat beräknings-enheter som för närvarande används av minst en virtuell dator|Inga dimensioner|
|Nätverk i|Nätverk i|Byte|Totalt|Antalet byte som tagits emot på alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antalet byte ut på alla nätverksgränssnitt som de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Disk lästa byte|Disk lästa byte|Byte|Totalt|Totalt antal byte som har lästs från disken under perioden|Inga dimensioner|
|Disk byte för skrivning|Disk byte för skrivning|Byte|Totalt|Totalt antal byte som skrivs till disk under perioden|Inga dimensioner|
|Disk-lästa åtgärder/sek|Disk-lästa åtgärder/sek|CountPerSecond|Genomsnittlig|Diskläsning IOPS|Inga dimensioner|
|Disk skrivåtgärder per sekund|Disk skrivåtgärder per sekund|CountPerSecond|Genomsnittlig|Diskskrivning IOPS|Inga dimensioner|
|CPU-kredit kvar|CPU-kredit kvar|Antal|Genomsnittlig|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|CPU-krediter som används|CPU-krediter som används|Antal|Genomsnittlig|Totalt antal krediter som används av den virtuella datorn|Inga dimensioner|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DCIApiCalls|Kunden Insights API-anrop|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationSuccessfulLines|Mappning importera åtgärden lyckas rader|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationFailedLines|Det gick inte att mappa importen rader|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationTotalLines|Totalt antal rader för mappning importera åtgärden|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationRuntimeInSeconds|Mappning importera åtgärden körning i sekunder|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundProfileExportSucceeded|Utgående profil exporten har slutförts|Antal|Totalt||Inga dimensioner|
|DCIOutboundProfileExportFailed|Utgående profil exporten misslyckades|Antal|Totalt||Inga dimensioner|
|DCIOutboundProfileExportDuration|Utgående profil Export varaktighet|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiExportSucceeded|Lyckad utgående Kpi-Export|Antal|Totalt||Inga dimensioner|
|DCIOutboundKpiExportFailed|Utgående Kpi-exporten misslyckades|Antal|Totalt||Inga dimensioner|
|DCIOutboundKpiExportDuration|Utgående Kpi Export varaktighet|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiExportStarted|Utgående Kpi Export igång|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiRecordCount|Utgående Kpi postantal|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundProfileExportCount|Antalet för Export av utgående profiler|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialProfileExportFailed|Utgående första profil exporten misslyckades|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialProfileExportSucceeded|Utgående första profil exporten har slutförts|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialKpiExportFailed|Utgående inledande Kpi-exporten misslyckades|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialKpiExportSucceeded|Lyckad utgående inledande Kpi-Export|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialProfileExportDurationInSeconds|Utgående första profil Export varaktighet i sekunder|Sekunder|Totalt||Inga dimensioner|
|AdlaJobForStandardKpiFailed|Adla jobb för Standard-Kpi misslyckades i sekunder|Sekunder|Totalt||Inga dimensioner|
|AdlaJobForStandardKpiTimeOut|Adla jobb för Standard Kpi tidsgräns i sekunder|Sekunder|Totalt||Inga dimensioner|
|AdlaJobForStandardKpiCompleted|Adla jobb för Standard-Kpi slutförts i sekunder|Sekunder|Totalt||Inga dimensioner|
|ImportASAValuesFailed|Importera ASA värden kunde inte räkna|Antal|Totalt||Inga dimensioner|
|ImportASAValuesSucceeded|Importera ASA värden lyckades antal|Antal|Totalt||Inga dimensioner|
|DCIProfilesCount|Antal instanser för profil|Antal|senaste||Inga dimensioner|
|DCIInteractionsPerMonthCount|Interaktioner antal per månad|Antal|senaste||Inga dimensioner|
|DCIKpisCount|KPI-antal|Antal|senaste||Inga dimensioner|
|DCISegmentsCount|Antal segment|Antal|senaste||Inga dimensioner|
|DCIPredictiveMatchPoliciesCount|Förutsägbara Matchningsantal|Antal|senaste||Inga dimensioner|
|DCIPredictionsCount|Förutsägelse antal|Antal|senaste||Inga dimensioner|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobEndedSuccess|Slutförda jobb|Antal|Totalt|Antal slutförda jobb.|Inga dimensioner|
|JobEndedFailure|Misslyckade jobb|Antal|Totalt|Antal misslyckade jobb.|Inga dimensioner|
|JobEndedCancelled|Avbrutna jobb|Antal|Totalt|Antal avbrutna jobb.|Inga dimensioner|
|JobAUEndedSuccess|Lyckad Australien tid|Sekunder|Totalt|Totalt antal Australien tid för slutförda jobb.|Inga dimensioner|
|JobAUEndedFailure|Misslyckade Australien tid|Sekunder|Totalt|Totalt antal Australien tid för misslyckade jobb.|Inga dimensioner|
|JobAUEndedCancelled|Annullerad Australien tid|Sekunder|Totalt|Total tid Australien för avbrutna jobb.|Inga dimensioner|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Byte|Maximalt|Total mängd data som lagras på kontot.|Inga dimensioner|
|DataWritten|Data som skrivs|Byte|Totalt|Total mängd data som skrivs till kontot.|Inga dimensioner|
|DataRead|Läs data|Byte|Totalt|Total mängd data har lästs från kontot.|Inga dimensioner|
|WriteRequests|Skrivåtgärder|Antal|Totalt|Antal data skrivåtgärder till kontot.|Inga dimensioner|
|ReadRequests|Läs begäranden|Antal|Totalt|Antal data läsbegäranden till kontot.|Inga dimensioner|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Genomsnittlig|CPU-procent|Inga dimensioner|
|compute_limit|Compute-gränsen för enhet|Antal|Genomsnittlig|Compute-gränsen för enhet|Inga dimensioner|
|compute_consumption_percent|Beräkna procentandelen enhet|Procent|Genomsnittlig|Beräkna procentandelen enhet|Inga dimensioner|
|memory_percent|Minne|Procent|Genomsnittlig|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Procent|Genomsnittlig|IO-procent|Inga dimensioner|
|storage_percent|Lagringsprocent|Procent|Genomsnittlig|Lagringsprocent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Genomsnittlig|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagringsgränsen|Byte|Genomsnittlig|Lagringsgränsen|Inga dimensioner|
|active_connections|Totalt antal aktiva anslutningar|Antal|Genomsnittlig|Totalt antal aktiva anslutningar|Inga dimensioner|
|connections_failed|Totalt antal misslyckade anslutningar|Antal|Genomsnittlig|Totalt antal misslyckade anslutningar|Inga dimensioner|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Genomsnittlig|CPU-procent|Inga dimensioner|
|compute_limit|Compute-gränsen för enhet|Antal|Genomsnittlig|Compute-gränsen för enhet|Inga dimensioner|
|compute_consumption_percent|Beräkna procentandelen enhet|Procent|Genomsnittlig|Beräkna procentandelen enhet|Inga dimensioner|
|memory_percent|Minne|Procent|Genomsnittlig|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Procent|Genomsnittlig|IO-procent|Inga dimensioner|
|storage_percent|Lagringsprocent|Procent|Genomsnittlig|Lagringsprocent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Genomsnittlig|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagringsgränsen|Byte|Genomsnittlig|Lagringsgränsen|Inga dimensioner|
|active_connections|Totalt antal aktiva anslutningar|Antal|Genomsnittlig|Totalt antal aktiva anslutningar|Inga dimensioner|
|connections_failed|Totalt antal misslyckade anslutningar|Antal|Genomsnittlig|Totalt antal misslyckade anslutningar|Inga dimensioner|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.Ingress.allProtocol|Telemetri meddelande Skicka försök|Antal|Totalt|Antal meddelanden enhet till moln telemetri försökte skickas till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.Ingress.Success|Telemetri meddelanden som skickas|Antal|Totalt|Antal enhet till moln telemetri meddelanden som skickats till din IoT-hubb|Inga dimensioner|
|c2d.commands.egress.Complete.Success|Kommandon som slutförd|Antal|Totalt|Antal moln till enhet kommandon har slutförts av enheten|Inga dimensioner|
|c2d.commands.egress.Abandon.Success|Avbrutna kommandon|Antal|Totalt|Antal moln till enhet kommandon avbrutna av enheten|Inga dimensioner|
|c2d.commands.egress.Reject.Success|Kommandon som avvisat|Antal|Totalt|Antal moln till enhet kommandon som avvisats av enheten|Inga dimensioner|
|devices.totalDevices|Totalt antal enheter|Antal|Totalt|Antalet enheter som registrerats för din IoT-hubb|Inga dimensioner|
|devices.connectedDevices.allProtocol|Anslutna enheter|Antal|Totalt|Antalet enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.egress.Success|Telemetri meddelanden|Antal|Totalt|Antal gånger som meddelanden som skrivits till slutpunkter (totalt)|Inga dimensioner|
|d2c.telemetry.egress.dropped|Ignorerade meddelanden|Antal|Totalt|Antal meddelanden som utelämnats eftersom leverans slutpunkten har döda|Inga dimensioner|
|d2c.telemetry.egress.orphaned|Överblivna meddelanden|Antal|Totalt|Antal meddelanden som inte matchar några vägar inklusive återställningsplats flöde|Inga dimensioner|
|d2c.telemetry.egress.Invalid|Ogiltig meddelanden|Antal|Totalt|Antalet meddelanden som inte levereras på grund av inkompatibilitet med slutpunkten|Inga dimensioner|
|d2c.telemetry.egress.fallback|Meddelanden som matchar villkoret återställningsplats|Antal|Totalt|Antal meddelanden som skrivs till fallback-slutpunkten|Inga dimensioner|
|d2c.endpoints.egress.eventHubs|Skicka meddelanden till Event Hub-slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till Event Hub-slutpunkter|Inga dimensioner|
|d2c.endpoints.latency.eventHubs|Meddelandet svarstid för Event Hub-slutpunkter|millisekunder|Genomsnittlig|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång i Event Hub slutpunkt, i millisekunder|Inga dimensioner|
|d2c.endpoints.egress.serviceBusQueues|Skicka meddelanden till Service Bus-kö slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till Service Bus-kö slutpunkter|Inga dimensioner|
|d2c.endpoints.latency.serviceBusQueues|Meddelandet svarstid för Service Bus-kö-slutpunkter|millisekunder|Genomsnittlig|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång till en slutpunkt för Service Bus-kö, i millisekunder|Inga dimensioner|
|d2c.endpoints.egress.serviceBusTopics|Skicka meddelanden till Service Bus-ämne slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till Service Bus-ämne slutpunkter|Inga dimensioner|
|d2c.endpoints.latency.serviceBusTopics|Meddelandet svarstiden för Service Bus-ämne slutpunkter|millisekunder|Genomsnittlig|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång till en slutpunkt för Service Bus-ämne, i millisekunder|Inga dimensioner|
|d2c.endpoints.egress.builtIn.events|Skicka meddelanden till inbyggd slutpunkt (meddelanden/händelser)|Antal|Totalt|Antal gånger som meddelanden som skrivits till inbyggd slutpunkt (meddelanden/händelser)|Inga dimensioner|
|d2c.endpoints.latency.builtIn.events|Meddelandet svarstid för inbyggd slutpunkt (meddelanden/händelser)|millisekunder|Genomsnittlig|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång i inbyggd slutpunkt (meddelanden/händelser), i millisekunder |Inga dimensioner|
|d2c.endpoints.egress.Storage|Meddelanden som har levererats till lagring slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till lagring slutpunkter|Inga dimensioner|
|d2c.endpoints.latency.Storage|Meddelandet svarstid för lagring-slutpunkter|millisekunder|Genomsnittlig|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång till en slutpunkt för lagring, i millisekunder|Inga dimensioner|
|d2c.endpoints.egress.Storage.bytes|Data skrivs till lagring|Byte|Totalt|Mängden data i byte som skrivits till lagring slutpunkter|Inga dimensioner|
|d2c.endpoints.egress.Storage.BLOBs|BLOB skrivs till lagring|Antal|Totalt|Antal blobbar som skrivs till lagring slutpunkter|Inga dimensioner|
|d2c.Twin.Read.Success|Lyckad dubbla läser från enheter|Antal|Totalt|Antal alla lyckade enhet initieras dubbla läser.|Inga dimensioner|
|d2c.Twin.Read.failure|Det gick inte dubbla läsningar av enheter|Antal|Totalt|Antalet alla misslyckades enhet initieras dubbla läsningar.|Inga dimensioner|
|d2c.Twin.Read.size|Svarsstorlek dubbla läsningar av enheter|Byte|Genomsnittlig|Medel, min och max för alla lyckade enhet initieras dubbla läsningar.|Inga dimensioner|
|d2c.Twin.Update.Success|Lyckad dubbla uppdateringar från enheter|Antal|Totalt|Antal uppdateringar för alla lyckade enhet initieras dubbla.|Inga dimensioner|
|d2c.Twin.Update.failure|Det gick inte dubbla uppdateringar från enheter|Antal|Totalt|Antalet alla misslyckades enhet initieras dubbla uppdateringar.|Inga dimensioner|
|d2c.Twin.Update.size|Storleken på dubbla uppdateringar från enheter|Byte|Genomsnittlig|Medel, min och max storlek för alla lyckade enhet initieras dubbla uppdateringar.|Inga dimensioner|
|c2d.methods.Success|Lyckad direkta metoden anrop|Antal|Totalt|Antal alla lyckade direkt metodanrop.|Inga dimensioner|
|c2d.methods.failure|Det gick inte direkt metod anrop|Antal|Totalt|Det gick inte att direkt metodanrop antalet alla.|Inga dimensioner|
|c2d.methods.requestSize|Storleken på direkta metoden anrop för begäran|Byte|Genomsnittlig|Medel, min och max för alla lyckade direkta metodbegäranden.|Inga dimensioner|
|c2d.methods.responseSize|Storlek för direkta metoden anrop|Byte|Genomsnittlig|Medel, min och max för alla lyckade direkta metoden svar.|Inga dimensioner|
|c2d.Twin.Read.Success|Lyckad dubbla läser från serverdelen|Antal|Totalt|Antal alla lyckade tillbaka-end-initierad dubbla läser.|Inga dimensioner|
|c2d.Twin.Read.failure|Misslyckade dubbla läser från serverdelen|Antal|Totalt|Antalet alla misslyckades tillbaka-end-initierad dubbla läsningar.|Inga dimensioner|
|c2d.Twin.Read.size|Storlek för dubbla läser från serverdelen|Byte|Genomsnittlig|Medel, min och max för alla lyckade tillbaka-end-initierade dubbla läsningar.|Inga dimensioner|
|c2d.Twin.Update.Success|Lyckad dubbla uppdateringar från serverdelen|Antal|Totalt|Antal uppdateringar för alla lyckade tillbaka-end-initierad dubbla.|Inga dimensioner|
|c2d.Twin.Update.failure|Misslyckade dubbla uppdateringar från serverdelen|Antal|Totalt|Antalet alla misslyckades tillbaka-end-initierad dubbla uppdateringar.|Inga dimensioner|
|c2d.Twin.Update.size|Storleken på dubbla uppdateringar från serverdelen|Byte|Genomsnittlig|Medel, min och max storlek för alla lyckade tillbaka-end-initierad dubbla uppdateringar.|Inga dimensioner|
|twinQueries.success|Lyckad dubbla frågor|Antal|Totalt|Antal alla lyckade dubbla frågor.|Inga dimensioner|
|twinQueries.failure|Misslyckade dubbla frågor|Antal|Totalt|Antal alla misslyckade dubbla frågor.|Inga dimensioner|
|twinQueries.resultSize|Dubbla frågor storlek|Byte|Genomsnittlig|Medel, min och max resultatet av alla lyckade dubbla frågor storlek.|Inga dimensioner|
|jobs.createTwinUpdateJob.success|Lyckad skapande av dubbla uppdatera jobb|Antal|Totalt|Antal alla lyckade skapandet av dubbla uppdateringsjobb.|Inga dimensioner|
|jobs.createTwinUpdateJob.failure|Misslyckade skapande av dubbla uppdatera jobb|Antal|Totalt|Antal alla misslyckad generering av dubbla uppdateringsjobb.|Inga dimensioner|
|jobs.createDirectMethodJob.success|Lyckad skapande av metoden anrops-jobb|Antal|Totalt|Antal alla har skapats på direkta metoden anrop jobb.|Inga dimensioner|
|jobs.createDirectMethodJob.failure|Misslyckade skapande av metoden anrops-jobb|Antal|Totalt|Antal alla misslyckad generering av direkta metoden anrop jobb.|Inga dimensioner|
|jobs.listJobs.success|Antal samtal till listan jobb|Antal|Totalt|Antal alla lyckade anrop till listan jobb.|Inga dimensioner|
|jobs.listJobs.failure|Det gick inte anrop till listan jobb|Antal|Totalt|Antal alla misslyckade anrop till listan jobb.|Inga dimensioner|
|jobs.cancelJob.success|Lyckad jobbavbrott|Antal|Totalt|Antal lyckade anrop avbryta ett jobb.|Inga dimensioner|
|jobs.cancelJob.failure|Avbryta misslyckade jobb|Antal|Totalt|Antal misslyckade anrop avbryta ett jobb.|Inga dimensioner|
|jobs.queryJobs.success|Jobbfrågor|Antal|Totalt|Antal alla lyckade anrop till frågan jobb.|Inga dimensioner|
|jobs.queryJobs.failure|Misslyckade jobbfrågor|Antal|Totalt|Antal alla misslyckade anrop till frågan jobb.|Inga dimensioner|
|jobs.Completed|Slutförda jobb|Antal|Totalt|Antal alla slutförda jobb.|Inga dimensioner|
|jobs.Failed|Misslyckade jobb|Antal|Totalt|Antal alla misslyckade jobb.|Inga dimensioner|
|d2c.telemetry.Ingress.sendThrottle|Antalet fel som bandbreddsbegränsning|Antal|Totalt|Begränsar antalet bandbreddsbegränsning felen på grund av enheten genomflöde|Inga dimensioner|
|dailyMessageQuotaUsed|Sammanlagt antal meddelanden som används|Antal|Genomsnittlig|Antal Totalt antal meddelanden som används idag|Inga dimensioner|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Registrering försök|Antal|Totalt|Antal enheter registreringar försökte|ProvisioningServiceName IotHubName, Status|
|DeviceAssignments|Enheter som är tilldelade|Antal|Totalt|Antalet enheter som har tilldelats en IoT-hubb|ProvisioningServiceName IotHubName|
|AttestationAttempts|Attesteringen försök|Antal|Totalt|Antalet enheter intyg försökte|ProvisioningServiceName, Status, protokollet|

## <a name="microsoftdocumentdbdatabaseaccounts-cosmosdb"></a>Microsoft.DocumentDB/databaseAccounts (CosmosDB)
|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalRequests|Totalt antal begäranden|Antal|Antal|Antal begäranden|DatabaseAccount, samlingsnamn, DatabaseName, Region, StatusCode|
|MongoRequests|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden|DatabaseAccount, samlingsnamn, DatabaseName, Region, ErrorCode, CommandName|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|INREQS|Skicka förfrågningar|Antal|Totalt|Totalt antal inkommande skickar begäranden för ett namnområde|Inga dimensioner|
|SUCCREQ|Lyckade begäranden|Antal|Totalt|Totalt antal lyckade begäranden för ett namnområde|Inga dimensioner|
|FAILREQ|Misslyckade begäranden|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde|Inga dimensioner|
|SVRBSY|Upptagen serverfel|Antal|Totalt|Totalt antal upptagen serverfel för ett namnområde|Inga dimensioner|
|INTERR|Internt serverfel|Antal|Totalt|Totalt antal internt serverfel för ett namnområde|Inga dimensioner|
|MISCERR|Andra fel|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde|Inga dimensioner|
|INMSGS|Inkommande meddelanden (föråldrad)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde. Det här måttet är föråldrad. Använd mått för inkommande meddelanden i stället|Inga dimensioner|
|EHINMSGS|Inkommande meddelanden|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde|Inga dimensioner|
|OUTMSGS|Utgående meddelanden (föråldrad)|Antal|Totalt|Total utgående meddelanden för ett namnområde. Det här måttet är föråldrad. Använd i stället utgående meddelanden mått|Inga dimensioner|
|EHOUTMSGS|Utgående meddelanden|Antal|Totalt|Total utgående meddelanden för ett namnområde|Inga dimensioner|
|EHINMBS|Inkommande byte (inaktuell)|Byte|Totalt|Event Hub inkommande genomströmningen för ett namnområde. Det här måttet är föråldrad. Använd i stället inkommande byte mått|Inga dimensioner|
|EHINBYTES|Inkommande byte|Byte|Totalt|Event Hub inkommande genomströmningen för ett namnområde|Inga dimensioner|
|EHOUTMBS|Utgående byte (inaktuell)|Byte|Totalt|Hubb utgående meddelande händelsegenomflöde för ett namnområde. Det här måttet är föråldrad. Använd i stället utgående byte mått|Inga dimensioner|
|EHOUTBYTES|Utgående byte|Byte|Totalt|Hubb utgående meddelande händelsegenomflöde för ett namnområde|Inga dimensioner|
|EHABL|Arkivera eftersläpning meddelanden|Antal|Totalt|Händelsemeddelanden hubb Arkiv eftersläpande för ett namnområde|Inga dimensioner|
|EHAMSGS|Arkivera meddelanden|Antal|Totalt|Event Hub arkiverade meddelanden i ett namnområde|Inga dimensioner|
|EHAMBS|Arkivera genomströmningen|Byte|Totalt|Event Hub arkiveras genomströmningen i ett namnområde|Inga dimensioner|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerade värde|Antal|Genomsnittlig|Värdet som beräknats med Autoskala när den körs|MetricTriggerSource|
|MetricThreshold|Mått tröskelvärde|Antal|Genomsnittlig|Konfigurerade Autoskala tröskelvärdet när Autoskala kördes.|MetricTriggerRule|
|ObservedCapacity|Observerade kapacitet|Antal|Genomsnittlig|Kapaciteten rapporterade Autoskala när den körs.|Inga dimensioner|
|ScaleActionsInitiated|Skalningsåtgärder initieras|Antal|Totalt|Riktningen för åtgärden.|ScaleDirection|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Kör igång|Antal|Totalt|Antal startade arbetsflödeskörningar.|Inga dimensioner|
|RunsCompleted|Genomförda körningar|Antal|Totalt|Antal slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunsSucceeded|Kör lyckades|Antal|Totalt|Antal lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsFailed|Kör misslyckades|Antal|Totalt|Antal misslyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsCancelled|Kör avbröts|Antal|Totalt|Antal avbrutna arbetsflödeskörningar.|Inga dimensioner|
|RunLatency|Kör svarstid|Sekunder|Genomsnittlig|Svarstid för slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunSuccessLatency|Kör lyckade svarstid|Sekunder|Genomsnittlig|Svarstid för lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunThrottledEvents|Kör begränsad händelser|Antal|Totalt|Antal arbetsflödesåtgärds- eller utlösarbegränsade händelser.|Inga dimensioner|
|RunFailurePercentage|Kör procentandel fel|Procent|Totalt|Procentandel av arbetsflödet körs misslyckades.|Inga dimensioner|
|ActionsStarted|Åtgärder som har startats |Antal|Totalt|Antal startade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsCompleted|Åtgärder som har slutförts |Antal|Totalt|Antal slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSucceeded|Åtgärder har slutförts |Antal|Totalt|Antal lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsFailed|Åtgärder som misslyckades|Antal|Totalt|Antal misslyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSkipped|Åtgärder som hoppades över |Antal|Totalt|Antal överhoppade arbetsflödesåtgärder.|Inga dimensioner|
|ActionLatency|Åtgärden svarstid |Sekunder|Genomsnittlig|Svarstid för slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionSuccessLatency|Åtgärden lyckades svarstid |Sekunder|Genomsnittlig|Svarstid för lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionThrottledEvents|Åtgärden begränsas händelser|Antal|Totalt|Antal begränsade händelser för arbetsflödesåtgärder.|Inga dimensioner|
|TriggersStarted|Utlösare har startats |Antal|Totalt|Antal startade arbetsflödesutlösare.|Inga dimensioner|
|TriggersCompleted|Utlösare som har slutförts |Antal|Totalt|Antal slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggersSucceeded|Utlösare lyckades |Antal|Totalt|Antal lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggersFailed|Utlösare misslyckades |Antal|Totalt|Antal misslyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggersSkipped|Utlösare hoppades över|Antal|Totalt|Antal överhoppade arbetsflödesutlösare.|Inga dimensioner|
|TriggersFired|Utlösare utlöses |Antal|Totalt|Antal utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerLatency|Utlösaren svarstid |Sekunder|Genomsnittlig|Svarstid för slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggerFireLatency|Utlösaren brand svarstid |Sekunder|Genomsnittlig|Svartstid för utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerSuccessLatency|Utlösaren lyckade svarstid |Sekunder|Genomsnittlig|Svartstid för lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggerThrottledEvents|Utlösaren begränsas händelser|Antal|Totalt|Antal begränsade händelser för arbetsflödesutlösare.|Inga dimensioner|
|BillableActionExecutions|Fakturerbar åtgärd körningar|Antal|Totalt|Antal arbetsflödet åtgärd körningar komma debiteras.|Inga dimensioner|
|BillableTriggerExecutions|Fakturerbar utlösaren körningar|Antal|Totalt|Antal arbetsflödet utlösaren körningar komma debiteras.|Inga dimensioner|
|TotalBillableExecutions|Totalt antal fakturerbar körningar|Antal|Totalt|Antal arbetsflödet körningar komma debiteras.|Inga dimensioner|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|VIP-tillgänglighet|Antal|Genomsnittlig|Tillgängligheten för VIP-slutpunkter, baserat på avsökning|VipAddress VipPort|
|DipAvailability|DIP tillgänglighet|Antal|Genomsnittlig|Tillgängligheten för DIP slutpunkter, baserat på avsökning|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som överförs inom tidsperiod|VipAddress VipPort, riktning|
|PacketCount|Antal|Antal|Totalt|Totalt antal paket som skickas inom tidsperiod|VipAddress VipPort, riktning|
|SYNCount|SYN antal|Antal|Totalt|Totalt antal SYN-paket som skickas inom tidsperiod|VipAddress VipPort, riktning|
|SnatConnectionCount|SNAT Anslutningsräknare|Antal|Totalt|Totalt antal nya SNAT anslutningar som skapats i tidsperiod|VipAddress DipAddress, ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PacketsInDDoS|Inkommande paket DDoS|CountPerSecond|Genomsnittlig|Inkommande paket DDoS|Inga dimensioner|
|PacketsDroppedDDoS|Inkommande paket som tas bort DDoS|CountPerSecond|Genomsnittlig|Inkommande paket som tas bort DDoS|Inga dimensioner|
|PacketsForwardedDDoS|Inkommande paket som vidarebefordras DDoS|CountPerSecond|Genomsnittlig|Inkommande paket som vidarebefordras DDoS|Inga dimensioner|
|TCPPacketsInDDoS|Inkommande TCP-paket DDoS|CountPerSecond|Genomsnittlig|Inkommande TCP-paket DDoS|Inga dimensioner|
|TCPPacketsDroppedDDoS|Inkommande TCP-paket som tas bort DDoS|CountPerSecond|Genomsnittlig|Inkommande TCP-paket som tas bort DDoS|Inga dimensioner|
|TCPPacketsForwardedDDoS|Inkommande TCP-paket som vidarebefordras DDoS|CountPerSecond|Genomsnittlig|Inkommande TCP-paket som vidarebefordras DDoS|Inga dimensioner|
|UDPPacketsInDDoS|Inkommande UDP-paket DDoS|CountPerSecond|Genomsnittlig|Inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsDroppedDDoS|Inkommande UDP-paket som tas bort DDoS|CountPerSecond|Genomsnittlig|Inkommande UDP-paket som tas bort DDoS|Inga dimensioner|
|UDPPacketsForwardedDDoS|Inkommande UDP-paket som vidarebefordras DDoS|CountPerSecond|Genomsnittlig|Inkommande UDP-paket som vidarebefordras DDoS|Inga dimensioner|
|BytesInDDoS|Inkommande byte DDoS|BytesPerSecond|Genomsnittlig|Inkommande byte DDoS|Inga dimensioner|
|BytesDroppedDDoS|Inkommande byte bort DDoS|BytesPerSecond|Genomsnittlig|Inkommande byte bort DDoS|Inga dimensioner|
|BytesForwardedDDoS|Inkommande byte vidarebefordras DDoS|BytesPerSecond|Genomsnittlig|Inkommande byte vidarebefordras DDoS|Inga dimensioner|
|TCPBytesInDDoS|Inkommande TCP byte DDoS|BytesPerSecond|Genomsnittlig|Inkommande TCP byte DDoS|Inga dimensioner|
|TCPBytesDroppedDDoS|Inkommande TCP-byte bort DDoS|BytesPerSecond|Genomsnittlig|Inkommande TCP-byte bort DDoS|Inga dimensioner|
|TCPBytesForwardedDDoS|Inkommande TCP-byte vidarebefordras DDoS|BytesPerSecond|Genomsnittlig|Inkommande TCP-byte vidarebefordras DDoS|Inga dimensioner|
|UDPBytesInDDoS|Inkommande UDP byte DDoS|BytesPerSecond|Genomsnittlig|Inkommande UDP byte DDoS|Inga dimensioner|
|UDPBytesDroppedDDoS|Inkommande UDP byte bort DDoS|BytesPerSecond|Genomsnittlig|Inkommande UDP byte bort DDoS|Inga dimensioner|
|UDPBytesForwardedDDoS|Inkommande UDP byte vidarebefordras DDoS|BytesPerSecond|Genomsnittlig|Inkommande UDP byte vidarebefordras DDoS|Inga dimensioner|
|IfUnderDDoSAttack|Under DDoS attacker eller inte|Antal|Genomsnittlig|Under DDoS attacker eller inte|Inga dimensioner|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-lösning|CountPerSecond|Genomsnittlig|Inkommande TCP-paket för att utlösa DDoS-lösning|Inga dimensioner|
|DDoSTriggerUDPPackets|Inkommande UDP-paket för att utlösa DDoS-lösning|CountPerSecond|Genomsnittlig|Inkommande UDP-paket för att utlösa DDoS-lösning|Inga dimensioner|
|VipAvailability|Tillgänglighet|Antal|Genomsnittlig|Medeltillgänglighet IP-adress inom tidsperiod|Port|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som överförs inom tidsperiod|Port, riktning|
|PacketCount|Antal|Antal|Totalt|Totalt antal paket som skickas inom tidsperiod|Port, riktning|
|SynCount|SYN antal|Antal|Totalt|Totalt antal SYN-paket som skickas inom tidsperiod|Port, riktning|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Genomsnittlig|Antalet byte per sekund som Application Gateway har meddelat|Inga dimensioner|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Tunnel bandbredd|BytesPerSecond|Genomsnittlig|Genomsnittlig bandbredden för en tunnel i byte per sekund|ConnectionName RemoteIP|
|TunnelPeakBandwidth|Tunnel belastning bandbredd|BytesPerSecond|Genomsnittlig|Belastning bandbredden för en tunnel i byte per sekund|ConnectionName RemoteIP|
|TunnelEgressBytes|Tunnel utgående byte|Byte|Genomsnittlig|Utgående byte en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelIngressBytes|Tunnel ingång byte|Byte|Genomsnittlig|Inkommande byte en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelEgressPackets|Tunnel utgående paket|Antal|Genomsnittlig|Utgående antal en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelIngressPackets|Tunnel ingång paket|Antal|Genomsnittlig|Inkommande antal en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel utgång TS matchningsfel paket släpp|Antal|Genomsnittlig|Släpp för utgående paket från trafik selector matchar inte en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel ingång TS matchningsfel paket släpp|Antal|Genomsnittlig|Inkommande paket släpp från trafik selector matchar inte en tunnel i fem minuterna|ConnectionName RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesIn är större än|BytesIn är större än|Antal|Totalt|Byte ingressing Azure|Inga dimensioner|
|BytesOut är större än|BytesOut är större än|Antal|Totalt|Byte egressing Azure|Inga dimensioner|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Genomsnittlig|BITS ingressing Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Genomsnittlig|BITS egressing Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Frågor av slutpunkten som returnerade|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den angivna tiden|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status för endpoint av slutpunkten|Antal|Maximalt|1 om en slutpunkt avsökningen status är ”aktiverad”, annars 0.|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Registration.all|Åtgärder för registrering|Antal|Totalt|Antal alla lyckad registrering åtgärder (skapande frågor för uppdateringar och borttagningar). |Inga dimensioner|
|Registration.Create|Registrering skapar operationer|Antal|Totalt|Antal allt skapande av registreringen har lyckats.|Inga dimensioner|
|Registration.Update|Uppdateringsåtgärder för registrering|Antal|Totalt|Antal uppdateringar för alla registreringen har lyckats.|Inga dimensioner|
|Registration.Get|Registrering-läsåtgärder|Antal|Totalt|Antal frågor för alla registreringen har lyckats.|Inga dimensioner|
|Registration.delete|Registrering Delete-åtgärder|Antal|Totalt|Antal alla lyckad registrering borttagningar.|Inga dimensioner|
|inkommande|Inkommande meddelanden|Antal|Totalt|Antal alla lyckade skicka API-anrop. |Inga dimensioner|
|Incoming.Scheduled|Schemalagda Push-meddelanden som skickas|Antal|Totalt|Schemalagda Push-meddelanden avbröts|Inga dimensioner|
|Incoming.Scheduled.Cancel|Schemalagda Push-meddelanden avbröts|Antal|Totalt|Schemalagda Push-meddelanden avbröts|Inga dimensioner|
|Scheduled.Pending|Väntande schemalagda meddelanden|Antal|Totalt|Väntande schemalagda meddelanden|Inga dimensioner|
|installation.all|Installationen hanteringsåtgärder|Antal|Totalt|Installationen hanteringsåtgärder|Inga dimensioner|
|installation.Get|Hämta installationsåtgärder|Antal|Totalt|Hämta installationsåtgärder|Inga dimensioner|
|installation.upsert|Skapa eller uppdatera installationsåtgärder|Antal|Totalt|Skapa eller uppdatera installationsåtgärder|Inga dimensioner|
|installation.patch|Korrigering installationsåtgärder|Antal|Totalt|Korrigering installationsåtgärder|Inga dimensioner|
|installation.delete|Ta bort installationsåtgärder|Antal|Totalt|Ta bort installationsåtgärder|Inga dimensioner|
|Outgoing.allpns.Success|Lyckade meddelanden|Antal|Totalt|Antal alla lyckade meddelanden.|Inga dimensioner|
|Outgoing.allpns.invalidpayload|Nyttolasten fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom pns-systemet returnerade ett felaktigt nyttolast-fel.|Inga dimensioner|
|Outgoing.allpns.pnserror|Externa aviseringar systemfel|Antal|Totalt|Antal push-meddelanden kunde inte utföras eftersom det inte att kommunicera med pns-systemet (utesluter autentiseringsproblem).|Inga dimensioner|
|Outgoing.allpns.channelerror|Fel kanal|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom kanalen var ogiltigt inte är associerade med appen rätt begränsas eller upphört att gälla.|Inga dimensioner|
|Outgoing.allpns.badorexpiredchannel|Felaktig eller utgången kanal|Antal|Totalt|Antal push-meddelanden som kunde inte utföras eftersom kanalen/token/registrationId i registreringen har upphört att gälla eller är ogiltig.|Inga dimensioner|
|Outgoing.wns.Success|WNS lyckade meddelanden|Antal|Totalt|Antal alla lyckade meddelanden.|Inga dimensioner|
|Outgoing.wns.invalidcredentials|WNS auktorisering fel (ogiltig behörighet)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom pns-systemet accepterade inte de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna som har blockerats. (Windows Live kan inte identifiera autentiseringsuppgifterna).|Inga dimensioner|
|Outgoing.wns.badchannel|WNS felaktig kanal fel|Antal|Totalt|Antalet pushes som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS status: 404 hittades inte).|Inga dimensioner|
|Outgoing.wns.expiredchannel|WNS gått Kanalfel|Antal|Totalt|Antalet pushes som misslyckades eftersom ChannelURI har upphört att gälla (WNS status: 410 Gone).|Inga dimensioner|
|Outgoing.wns.throttled|WNS begränsas meddelanden|Antal|Totalt|Antalet pushes som misslyckades eftersom WNS begränsning är den här appen (WNS status: 406 inte godtagbara).|Inga dimensioner|
|Outgoing.wns.tokenproviderunreachable|WNS auktorisering fel (kan inte nås)|Antal|Totalt|Windows Live kan inte nås.|Inga dimensioner|
|Outgoing.wns.invalidtoken|WNS auktorisering fel (Ogiltigt Token)|Antal|Totalt|Den token som angetts för WNS är inte giltigt (WNS status: 401 obehörig).|Inga dimensioner|
|Outgoing.wns.wrongtoken|WNS auktorisering fel (felaktig Token)|Antal|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS status: 403 nekad). Detta kan inträffa om ChannelURI i registreringen är associerad med en annan app. Kontrollera att klientappen är associerad med samma app vars autentiseringsuppgifter finns i meddelandehubben.|Inga dimensioner|
|Outgoing.wns.invalidnotificationformat|WNS ogiltigt aviseringsresultat Format|Antal|Totalt|Formatet för meddelandet är ogiltigt (WNS status: 400). Observera att WNS inte avvisa alla ogiltig nyttolaster.|Inga dimensioner|
|Outgoing.wns.invalidnotificationsize|WNS – ogiltig meddelandestorlek|Antal|Totalt|Meddelande-nyttolasten är för stor (WNS status: 413).|Inga dimensioner|
|Outgoing.wns.channelthrottled|WNS-kanal begränsas|Antal|Totalt|Meddelandet ignorerades eftersom ChannelURI i registreringen begränsas (WNS svarshuvud: X-WNS-NotificationStatus:channelThrottled).|Inga dimensioner|
|Outgoing.wns.channeldisconnected|WNS-kanal som kopplats från|Antal|Totalt|Meddelandet ignorerades eftersom ChannelURI i registreringen begränsas (WNS svarshuvud: X-WNS-DeviceConnectionStatus: frånkopplad).|Inga dimensioner|
|Outgoing.wns.dropped|WNS bort meddelanden|Antal|Totalt|Meddelandet ignorerades eftersom ChannelURI i registreringen begränsas (X-WNS-NotificationStatus: avbrutna men inte X-WNS-DeviceConnectionStatus: frånkopplad).|Inga dimensioner|
|Outgoing.wns.pnserror|WNS-fel|Antal|Totalt|Meddelande som inte levereras på grund av fel i kommunikationen med WNS.|Inga dimensioner|
|Outgoing.wns.authenticationerror|WNS-autentiseringsfel|Antal|Totalt|Meddelande som inte levereras på grund av fel i kommunikationen med Windows Live ogiltiga autentiseringsuppgifter eller felaktig token.|Inga dimensioner|
|Outgoing.apns.Success|APN lyckade meddelanden|Antal|Totalt|Antal alla lyckade meddelanden.|Inga dimensioner|
|Outgoing.apns.invalidcredentials|APN auktorisering fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom pns-systemet accepterade inte de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna som har blockerats.|Inga dimensioner|
|Outgoing.apns.badchannel|APN felaktig kanal fel|Antal|Totalt|Antalet pushes som misslyckades eftersom token är ogiltig (APN-statuskod: 8).|Inga dimensioner|
|Outgoing.apns.expiredchannel|APN gått Kanalfel|Antal|Totalt|Antal token som upphävdes av APN feedback kanalen.|Inga dimensioner|
|Outgoing.apns.invalidnotificationsize|APNS – ogiltig meddelandestorlek|Antal|Totalt|Antalet pushes som misslyckades eftersom nyttolasten är för stort (APN-statuskod: 7).|Inga dimensioner|
|Outgoing.apns.pnserror|APN-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel i kommunikationen med APNS.|Inga dimensioner|
|Outgoing.GCM.Success|Lyckad GCM-meddelanden|Antal|Totalt|Antal alla lyckade meddelanden.|Inga dimensioner|
|Outgoing.GCM.invalidcredentials|GCM auktorisering fel (ogiltig behörighet)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom pns-systemet accepterade inte de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna som har blockerats.|Inga dimensioner|
|Outgoing.GCM.badchannel|GCM felaktig kanal fel|Antal|Totalt|Antalet pushes som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM resultat: Ogiltig registrering).|Inga dimensioner|
|Outgoing.GCM.expiredchannel|GCM gått Kanalfel|Antal|Totalt|Antalet pushes som misslyckades eftersom registrationId i registreringen har upphört att gälla (GCM resultat: NotRegistered).|Inga dimensioner|
|Outgoing.GCM.throttled|GCM begränsas meddelanden|Antal|Totalt|Antalet pushes som misslyckades eftersom GCM begränsas den här appen (GCM-statuskod: 501-599 eller resultat: tillgänglig).|Inga dimensioner|
|Outgoing.GCM.invalidnotificationformat|GCM ogiltigt aviseringsresultat Format|Antal|Totalt|Antalet pushes som misslyckades eftersom nyttolasten inte har formaterats korrekt (GCM resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|Outgoing.GCM.invalidnotificationsize|GCM – ogiltig meddelandestorlek|Antal|Totalt|Antalet pushes som misslyckades eftersom nyttolasten är för stort (GCM resultat: MessageTooBig).|Inga dimensioner|
|Outgoing.GCM.wrongchannel|GCM fel kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte är kopplad till den aktuella appen (GCM resultat: InvalidPackageName).|Inga dimensioner|
|Outgoing.GCM.pnserror|GCM-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel i kommunikationen med GCM.|Inga dimensioner|
|Outgoing.GCM.authenticationerror|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom pns-systemet inte accepterade den angivna autentiseringsuppgifter autentiseringsuppgifterna blockeras eller SenderId är inte korrekt konfigurerad i appen (GCM resultat: MismatchedSenderId).|Inga dimensioner|
|Outgoing.mpns.Success|MPNS lyckade meddelanden|Antal|Totalt|Antal alla lyckade meddelanden.|Inga dimensioner|
|Outgoing.mpns.invalidcredentials|Ogiltiga autentiseringsuppgifter för MPNS|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom pns-systemet accepterade inte de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna som har blockerats.|Inga dimensioner|
|Outgoing.mpns.badchannel|MPNS felaktig kanal fel|Antal|Totalt|Antalet pushes som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS status: 404 hittades inte).|Inga dimensioner|
|Outgoing.mpns.throttled|MPNS begränsas meddelanden|Antal|Totalt|Antalet pushes som misslyckades eftersom MPNS begränsning är den här appen (WNS MPNS: 406 inte godtagbara).|Inga dimensioner|
|Outgoing.mpns.invalidnotificationformat|Ogiltigt meddelande-Format för MPNS|Antal|Totalt|Antal push-meddelanden som har misslyckats eftersom nyttolasten för meddelandet är för stort.|Inga dimensioner|
|Outgoing.mpns.channeldisconnected|MPNS kanal frånkopplad|Antal|Totalt|Antalet pushes som misslyckades eftersom ChannelURI i registreringen kopplades (MPNS status: 412 hittades).|Inga dimensioner|
|Outgoing.mpns.dropped|MPNS bort meddelanden|Antal|Totalt|Antal push-meddelanden som utelämnats av MPNS (MPNS svarshuvud: X NotificationStatus: QueueFull eller Suppressed).|Inga dimensioner|
|Outgoing.mpns.pnserror|MPNS fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel i kommunikationen med MPNS.|Inga dimensioner|
|Outgoing.mpns.authenticationerror|MPNS autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom pns-systemet accepterade inte de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna som har blockerats.|Inga dimensioner|
|notificationhub.pushes|Alla utgående meddelanden|Antal|Totalt|Alla utgående meddelanden för notification hub|Inga dimensioner|
|Incoming.all.Requests|Alla inkommande förfrågningar|Antal|Totalt|Totalt antal inkommande begäranden för en meddelandehubb|Inga dimensioner|
|Incoming.all.failedrequests|Alla inkommande förfrågningar som inte kunde slutföras|Antal|Totalt|Totalt antal misslyckade förfrågningar för en meddelandehubb|Inga dimensioner|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SearchLatency|Sök svarstid|Sekunder|Genomsnittlig|Sök genomsnittlig svarstid för search-tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Sökfrågor per sekund|CountPerSecond|Genomsnittlig|Sökfrågor per sekund för search-tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Begränsad sökning frågor procent|Procent|Genomsnittlig|Procentandelen sökfrågor som har begränsats för söktjänsten|Inga dimensioner|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CPUXNS|CPU-användning per namnområde|Procent|Maximalt|Service bus premium namnområde CPU användning mått|Inga dimensioner|
|WSXNS|Minnesstorleksanvändning per namnrymd|Procent|Maximalt|Service bus premium-namnområde minne användning mått|Inga dimensioner|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Genomsnittlig|CPU-procent|Inga dimensioner|
|physical_data_read_percent|Data IO-procent|Procent|Genomsnittlig|Data IO-procent|Inga dimensioner|
|log_write_percent|Loggen IO-procent|Procent|Genomsnittlig|Loggen IO-procent|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Procent|Genomsnittlig|DTU-procent|Inga dimensioner|
|Lagring|Totalt antal databasens storlek|Byte|Maximalt|Totalt antal databasens storlek|Inga dimensioner|
|connection_successful|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|connection_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|blocked_by_firewall|Blockeras av brandvägg|Antal|Totalt|Blockeras av brandvägg|Inga dimensioner|
|deadlock|Deadlocks|Antal|Totalt|Deadlocks|Inga dimensioner|
|storage_percent|Databasstorlek i procent|Procent|Maximalt|Databasstorlek i procent|Inga dimensioner|
|xtp_storage_percent|Minnesintern OLTP lagring procent|Procent|Genomsnittlig|Minnesintern OLTP lagring procent|Inga dimensioner|
|workers_percent|Procentsatsen för arbetare|Procent|Genomsnittlig|Procentsatsen för arbetare|Inga dimensioner|
|sessions_percent|Sessioner procent|Procent|Genomsnittlig|Sessioner procent|Inga dimensioner|
|dtu_limit|DTU gräns|Antal|Genomsnittlig|DTU gräns|Inga dimensioner|
|dtu_used|DTU används|Antal|Genomsnittlig|DTU används|Inga dimensioner|
|dwu_limit|DWU gräns|Antal|Maximalt|DWU gräns|Inga dimensioner|
|dwu_consumption_percent|DWU-procent|Procent|Maximalt|DWU-procent|Inga dimensioner|
|dwu_used|DWU används|Antal|Maximalt|DWU används|Inga dimensioner|
|dw_cpu_percent|DW nivån CPU-procent|Procent|Genomsnittlig|DW nivån CPU-procent|dw_logical_node_id|
|dw_physical_data_read_percent|DW nod nivån Data IO-procent|Procent|Genomsnittlig|DW nod nivån Data IO-procent|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Genomsnittlig|CPU-procent|Inga dimensioner|
|database_cpu_percent|CPU-procent|Procent|Genomsnittlig|CPU-procent|DatabaseResourceId|
|physical_data_read_percent|Data IO-procent|Procent|Genomsnittlig|Data IO-procent|Inga dimensioner|
|database_physical_data_read_percent|Data IO-procent|Procent|Genomsnittlig|Data IO-procent|DatabaseResourceId|
|log_write_percent|Loggen IO-procent|Procent|Genomsnittlig|Loggen IO-procent|Inga dimensioner|
|database_log_write_percent|Loggen IO-procent|Procent|Genomsnittlig|Loggen IO-procent|DatabaseResourceId|
|dtu_consumption_percent|DTU-procent|Procent|Genomsnittlig|DTU-procent|Inga dimensioner|
|database_dtu_consumption_percent|DTU-procent|Procent|Genomsnittlig|DTU-procent|DatabaseResourceId|
|storage_percent|Lagringsprocent|Procent|Genomsnittlig|Lagringsprocent|Inga dimensioner|
|workers_percent|Procentsatsen för arbetare|Procent|Genomsnittlig|Procentsatsen för arbetare|Inga dimensioner|
|database_workers_percent|Procentsatsen för arbetare|Procent|Genomsnittlig|Procentsatsen för arbetare|DatabaseResourceId|
|sessions_percent|Sessioner procent|Procent|Genomsnittlig|Sessioner procent|Inga dimensioner|
|database_sessions_percent|Sessioner procent|Procent|Genomsnittlig|Sessioner procent|DatabaseResourceId|
|eDTU_limit|eDTU gräns|Antal|Genomsnittlig|eDTU gräns|Inga dimensioner|
|storage_limit|Lagringsgränsen|Byte|Genomsnittlig|Lagringsgränsen|Inga dimensioner|
|eDTU_used|eDTU används|Antal|Genomsnittlig|eDTU används|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Genomsnittlig|Använt lagringsutrymme|Inga dimensioner|
|database_storage_used|Använt lagringsutrymme|Byte|Genomsnittlig|Använt lagringsutrymme|DatabaseResourceId|
|xtp_storage_percent|Minnesintern OLTP lagring procent|Procent|Genomsnittlig|Minnesintern OLTP lagring procent|Inga dimensioner|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-procent|Procent|Genomsnittlig|DTU-procent|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-procent|Procent|Genomsnittlig|DTU-procent|DatabaseResourceId ElasticPoolResourceId|
|storage_used|Använt lagringsutrymme|Byte|Genomsnittlig|Använt lagringsutrymme|ElasticPoolResourceId|
|database_storage_used|Använt lagringsutrymme|Byte|Genomsnittlig|Använt lagringsutrymme|DatabaseResourceId ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Genomsnittlig|Konto som används för kapacitet|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|millisekunder|Genomsnittlig|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|millisekunder|Genomsnittlig|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Genomsnittlig|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-kapacitet|Byte|Genomsnittlig|Mängden lagringsutrymme som används av storage-konto Blob-tjänsten i byte.|BlobType|
|BlobCount|BLOB-antal|Antal|Genomsnittlig|Antal Blob storage-konto Blob-tjänsten.|BlobType|
|ContainerCount|Antal för BLOB-behållare|Antal|Genomsnittlig|Antal behållare i Blob storage-konto-tjänsten.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|millisekunder|Genomsnittlig|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|millisekunder|Genomsnittlig|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Genomsnittlig|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabell kapacitet|Byte|Genomsnittlig|Mängden lagringsutrymme som används av storage-konto tabelltjänsten i byte.|Inga dimensioner|
|TableCount|Tabellen antal|Antal|Genomsnittlig|Numret på tabellen i tabelltjänsten för storage-konto.|Inga dimensioner|
|TableEntityCount|Antal enheter för tabellen|Antal|Genomsnittlig|Antal tabellentiteter i tabelltjänsten för storage-konto.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|millisekunder|Genomsnittlig|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|millisekunder|Genomsnittlig|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Genomsnittlig|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kön kapacitet|Byte|Genomsnittlig|Mängden lagringsutrymme som används av storage-konto kötjänsten i byte.|Inga dimensioner|
|QueueCount|Antal i åtgärdskö|Antal|Genomsnittlig|Antal kön i Kötjänsten för storage-konto.|Inga dimensioner|
|QueueMessageCount|Antalet meddelanden för kön|Antal|Genomsnittlig|Det ungefärliga antalet meddelanden i kö i Kötjänsten för storage-konto.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|millisekunder|Genomsnittlig|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|millisekunder|Genomsnittlig|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Genomsnittlig|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Filen kapacitet|Byte|Genomsnittlig|Mängden lagringsutrymme som används av storage-konto File service i byte.|Inga dimensioner|
|FileCount|Antal filer|Antal|Genomsnittlig|Antal filer i tjänsten för File storage-konto.|Inga dimensioner|
|FileShareCount|Filresurs|Antal|Genomsnittlig|Antal delar i tjänsten för File storage-konto.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|millisekunder|Genomsnittlig|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|millisekunder|Genomsnittlig|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Genomsnittlig|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ResourceUtilization|SU % användning|Procent|Maximalt|SU % användning|Inga dimensioner|
|InputEvents|Inkommande händelser|Antal|Totalt|Inkommande händelser|Inga dimensioner|
|InputEventBytes|Händelse-byte|Byte|Totalt|Händelse-byte|Inga dimensioner|
|LateInputEvents|Inkommande händelser|Antal|Totalt|Inkommande händelser|Inga dimensioner|
|OutputEvents|Utdatahändelserna|Antal|Totalt|Utdatahändelserna|Inga dimensioner|
|ConversionErrors|Data Konverteringsfel|Antal|Totalt|Data Konverteringsfel|Inga dimensioner|
|Fel|Körningsfel|Antal|Totalt|Körningsfel|Inga dimensioner|
|DroppedOrAdjustedEvents|Oordnade händelser|Antal|Totalt|Oordnade händelser|Inga dimensioner|
|AMLCalloutRequests|Funktionen begäranden|Antal|Totalt|Funktionen begäranden|Inga dimensioner|
|AMLCalloutFailedRequests|Funktionsfel begäranden|Antal|Totalt|Funktionsfel begäranden|Inga dimensioner|
|AMLCalloutInputEvents|Funktionshändelser|Antal|Totalt|Funktionshändelser|Inga dimensioner|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Procent|Genomsnittlig|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Procent|Genomsnittlig|Minnesprocent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Totalt|Diskkölängd|Instans|
|HttpQueueLength|HTTP-Kölängd|Antal|Totalt|HTTP-Kölängd|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Ut data|Byte|Totalt|Ut data|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (exklusive funktioner)

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Ut data|Byte|Totalt|Ut data|Instans|
|Http101|HTTP 101|Antal|Totalt|HTTP 101|Instans|
|Http2xx|HTTP-2xx|Antal|Totalt|HTTP-2xx|Instans|
|Http3xx|HTTP-3xx|Antal|Totalt|HTTP-3xx|Instans|
|Http401|HTTP 401|Antal|Totalt|HTTP 401|Instans|
|Http403|HTTP 403|Antal|Totalt|HTTP 403|Instans|
|Http404|HTTP 404|Antal|Totalt|HTTP 404|Instans|
|Http406|HTTP 406|Antal|Totalt|HTTP 406|Instans|
|Http4xx|HTTP-4xx|Antal|Totalt|HTTP-4xx|Instans|
|Http5xx|HTTP-fel|Antal|Totalt|HTTP-fel|Instans|
|MemoryWorkingSet|Arbetsminnet för minne|Byte|Genomsnittlig|Arbetsminnet för minne|Instans|
|AverageMemoryWorkingSet|Genomsnittlig minne arbetsminne|Byte|Genomsnittlig|Genomsnittlig minne arbetsminne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Genomsnittlig|Genomsnittlig svarstid|Instans|
|FunctionExecutionUnits|Funktionen körning av enheter|Antal|Genomsnittlig|Funktionen körning av enheter|Instans|
|FunctionExecutionCount|Funktionen körningar|Antal|Genomsnittlig|Funktionen körningar|Instans|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funktioner)

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Ut data|Byte|Totalt|Ut data|Instans|
|Http5xx|HTTP-fel|Antal|Totalt|HTTP-fel|Instans|
|MemoryWorkingSet|Arbetsminnet för minne|Byte|Genomsnittlig|Arbetsminnet för minne|Instans|
|AverageMemoryWorkingSet|Genomsnittlig minne arbetsminne|Byte|Genomsnittlig|Genomsnittlig minne arbetsminne|Instans|
|FunctionExecutionUnits|Funktionen körning av enheter|Antal|Genomsnittlig|Funktionen körning av enheter|Instans|
|FunctionExecutionCount|Funktionen körningar|Antal|Genomsnittlig|Funktionen körningar|Instans|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Ut data|Byte|Totalt|Ut data|Instans|
|Http101|HTTP 101|Antal|Totalt|HTTP 101|Instans|
|Http2xx|HTTP-2xx|Antal|Totalt|HTTP-2xx|Instans|
|Http3xx|HTTP-3xx|Antal|Totalt|HTTP-3xx|Instans|
|Http401|HTTP 401|Antal|Totalt|HTTP 401|Instans|
|Http403|HTTP 403|Antal|Totalt|HTTP 403|Instans|
|Http404|HTTP 404|Antal|Totalt|HTTP 404|Instans|
|Http406|HTTP 406|Antal|Totalt|HTTP 406|Instans|
|Http4xx|HTTP-4xx|Antal|Totalt|HTTP-4xx|Instans|
|Http5xx|HTTP-fel|Antal|Totalt|HTTP-fel|Instans|
|MemoryWorkingSet|Arbetsminnet för minne|Byte|Genomsnittlig|Arbetsminnet för minne|Instans|
|AverageMemoryWorkingSet|Genomsnittlig minne arbetsminne|Byte|Genomsnittlig|Genomsnittlig minne arbetsminne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Genomsnittlig|Genomsnittlig svarstid|Instans|
|FunctionExecutionUnits|Funktionen körning av enheter|Antal|Genomsnittlig|Funktionen körning av enheter|Instans|
|FunctionExecutionCount|Funktionen körningar|Antal|Genomsnittlig|Funktionen körningar|Instans|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Ut data|Byte|Totalt|Ut data|Instans|
|Http101|HTTP 101|Antal|Totalt|HTTP 101|Instans|
|Http2xx|HTTP-2xx|Antal|Totalt|HTTP-2xx|Instans|
|Http3xx|HTTP-3xx|Antal|Totalt|HTTP-3xx|Instans|
|Http401|HTTP 401|Antal|Totalt|HTTP 401|Instans|
|Http403|HTTP 403|Antal|Totalt|HTTP 403|Instans|
|Http404|HTTP 404|Antal|Totalt|HTTP 404|Instans|
|Http406|HTTP 406|Antal|Totalt|HTTP 406|Instans|
|Http4xx|HTTP-4xx|Antal|Totalt|HTTP-4xx|Instans|
|Http5xx|HTTP-fel|Antal|Totalt|HTTP-fel|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Genomsnittlig|Genomsnittlig svarstid|Instans|
|CpuPercentage|CPU-procent|Procent|Genomsnittlig|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Procent|Genomsnittlig|Minnesprocent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Totalt|Diskkölängd|Instans|
|HttpQueueLength|HTTP-Kölängd|Antal|Totalt|HTTP-Kölängd|Instans|
|ActiveRequests|Aktiva begäranden|Antal|Totalt|Aktiva begäranden|Instans|
|TotalFrontEnds|Totalt antal frontwebbservrarna|Antal|Genomsnittlig|Totalt antal frontwebbservrarna|Instans|
|SmallAppServicePlanInstances|Liten App Service-Plan arbetare|Antal|Genomsnittlig|Liten App Service-Plan arbetare|Instans|
|MediumAppServicePlanInstances|Medelhög App Service-Plan arbetare|Antal|Genomsnittlig|Medelhög App Service-Plan arbetare|Instans|
|LargeAppServicePlanInstances|Stora App Service-Plan arbetare|Antal|Genomsnittlig|Stora App Service-Plan arbetare|Instans|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbetare|Antal|Genomsnittlig|Totalt antal arbetare|Instans|
|WorkersAvailable|Tillgängliga arbetare|Antal|Genomsnittlig|Tillgängliga arbetare|Instans|
|WorkersUsed|Använda arbetare|Antal|Genomsnittlig|Använda arbetare|Instans|

## <a name="next-steps"></a>Nästa steg
* [Läs mer om mätvärden i Azure-Monitor](monitoring-overview-metrics.md)
* [Skapa aviseringar på mått](insights-receive-alert-notifications.md)
* [Exportera mått till lagring, Event Hub eller logganalys](monitoring-overview-of-diagnostic-logs.md)
