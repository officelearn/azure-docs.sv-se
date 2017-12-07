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
ms.openlocfilehash: ef27a15bb6a6305f7a762716a20487ef983cb5d1
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>Stöds mått med Azure-Monitor
Azure-Monitor finns flera sätt att interagera med statistik, inklusive diagram dem i portalen, komma åt dem via REST API eller fråga dem med PowerShell eller CLI. Nedan finns en fullständig lista över alla just nu med Azure-Monitor mått pipeline.

> [!NOTE]
> Andra mått kan finnas i portalen eller med äldre API: er. Listan innehåller endast tillgängliga med konsoliderade Azure-Monitor mått pipeline mått. Fråga efter och komma åt mått med dimensioner ska du använda den [2017-05-01-preview api-version](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|qpu_metric|QPU|Antal|Medel|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|Inga dimensioner|
|memory_metric|Minne|Byte|Medel|Minne. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|Inga dimensioner|
|TotalConnectionRequests|Totalt antal begäranden|Antal|Medel|Totalt antal förfrågningar. Dessa är mottagna.|Inga dimensioner|
|SuccessfullConnectionsPerSec|Lyckade anslutningar Per sekund|CountPerSecond|Medel|Hastighet för lyckade anslutningen slutföras.|Inga dimensioner|
|TotalConnectionFailures|Totalt antal anslutningsfel|Antal|Medel|Totalt antal misslyckade anslutningsförsök.|Inga dimensioner|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medel|Aktuellt antal användarsessioner som har upprättats.|Inga dimensioner|
|QueryPoolBusyThreads|Frågan poolen upptagen trådar|Antal|Medel|Antal upptagen trådar i trådpoolen för frågan.|Inga dimensioner|
|CommandPoolJobQueueLength|Kölängd för kommandot poolen jobb|Antal|Medel|Antal jobb i kö i trådpoolen för kommandot.|Inga dimensioner|
|ProcessingPoolJobQueueLength|Kölängd för bearbetning poolen jobb|Antal|Medel|Antal icke-I/O-jobb i kö för bearbetningstrådpoolen.|Inga dimensioner|
|CurrentConnections|Anslutning: Aktuella anslutningar|Antal|Medel|Aktuellt antal klientanslutningar.|Inga dimensioner|
|CleanerCurrentPrice|Minne: Tydligare aktuella pris|Antal|Medel|Aktuella priset minne, $ byte/tidsvärdet, normaliserad till 1000.|Inga dimensioner|
|CleanerMemoryShrinkable|Minne: Tydligare minne krympning|Byte|Medel|Mängden minne i byte, föremål rensning av bakgrunden ett rengöringsband.|Inga dimensioner|
|CleanerMemoryNonshrinkable|Minne: Tydligare minne nonshrinkable|Byte|Medel|Mängden minne i byte som inte omfattas av rensning av bakgrunden ett rengöringsband.|Inga dimensioner|
|MemoryUsage|Minne: Minnesanvändning|Byte|Medel|Mängden minne på server-processen som används för att beräkna tydligare minnespris. Lika med prestandaräknaren Process\PrivateBytes plus storleken på minnesmappade data, ignoreras eventuella som mappats eller som har allokerats av xVelocity i minnet analytics motorn (VertiPaq) överskrider minnesgränsen för xVelocity-motorn.|Inga dimensioner|
|MemoryLimitHard|Minne: Minnesgränsen hårddisken|Byte|Medel|Hårda minnesgränsen, från konfigurationsfilen.|Inga dimensioner|
|MemoryLimitHigh|Minne: Minne begränsa hög|Byte|Medel|Hög minnesgränsen, från konfigurationsfilen.|Inga dimensioner|
|MemoryLimitLow|Minne: Minne gränsen låg|Byte|Medel|Minnesbrist gräns från konfigurationsfilen.|Inga dimensioner|
|MemoryLimitVertiPaq|Minne: Minne gränsen VertiPaq|Byte|Medel|I minnet gräns från konfigurationsfilen.|Inga dimensioner|
|Kvot|Minne: kvot|Byte|Medel|Aktuella minneskvoten, i byte. Minneskvoten kallas även en reservation för minne grant eller minne.|Inga dimensioner|
|QuotaBlocked|: Minneskvoten blockeras|Antal|Medel|Aktuellt antal begäranden om kvoten som blockeras tills andra minneskvoter frigörs.|Inga dimensioner|
|VertiPaqNonpaged|Minne: VertiPaq växlingsbart systemminne|Byte|Medel|Byte av minne låst i arbetsminnet för användning av motorn i minnet.|Inga dimensioner|
|VertiPaqPaged|Minne: VertiPaq-växlat minne|Byte|Medel|Antal byte växlingsbart minne som används för data i minnet.|Inga dimensioner|
|RowsReadPerSec|Bearbetning: Läsa rader per sek|CountPerSecond|Medel|Antal rader att läsa från alla relationsdatabaser.|Inga dimensioner|
|RowsConvertedPerSec|Bearbetning: Rader konverteras per sek|CountPerSecond|Medel|Antal rader konverteras under bearbetning.|Inga dimensioner|
|RowsWrittenPerSec|Bearbetning: Rader som skrivs per sekund|CountPerSecond|Medel|Antal rader skrivna under bearbetning.|Inga dimensioner|
|CommandPoolBusyThreads|Trådar: Kommandot poolen upptagen trådar|Antal|Medel|Antal upptagen trådar i trådpoolen för kommandot.|Inga dimensioner|
|CommandPoolIdleThreads|Trådar: Kommandot poolen inaktiva trådar|Antal|Medel|Antalet inaktiva trådar i trådpoolen för kommandot.|Inga dimensioner|
|LongParsingBusyThreads|Trådar: Long parsning upptagen trådar|Antal|Medel|Antal upptagen trådar i länge tolkning trådpoolen.|Inga dimensioner|
|LongParsingIdleThreads|Trådar: Long parsning inaktiva trådar|Antal|Medel|Antalet inaktiva trådar i länge tolkning trådpoolen.|Inga dimensioner|
|LongParsingJobQueueLength|Trådar: Parsning länge jobbet Kölängd|Antal|Medel|Antal jobb i kö för länge tolkning trådpoolen.|Inga dimensioner|
|ProcessingPoolBusyIOJobThreads|Trådar: Bearbetning av poolen upptagen i/o-jobbet trådar|Antal|Medel|Antal trådar som körs i/o-jobb i bearbetningstrådpoolen.|Inga dimensioner|
|ProcessingPoolBusyNonIOThreads|Trådar: Bearbetning av poolen upptagen icke-I/O-trådar|Antal|Medel|Antal trådar som icke-I/O-jobb som körs i bearbetningstrådpoolen.|Inga dimensioner|
|ProcessingPoolIOJobQueueLength|Trådar: Bearbetning av poolen Kölängd för i/o-jobb|Antal|Medel|Antal i/o-jobb i kö för bearbetningstrådpoolen.|Inga dimensioner|
|ProcessingPoolIdleIOJobThreads|Trådar: Bearbetning av poolen inaktiv i/o-jobbet trådar|Antal|Medel|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|Inga dimensioner|
|ProcessingPoolIdleNonIOThreads|Trådar: Bearbetning av poolen inaktiv icke-I/O-trådar|Antal|Medel|Antalet inaktiva trådar i bearbetningstrådpoolen som är dedikerad till icke-I/O-jobb.|Inga dimensioner|
|QueryPoolIdleThreads|Trådar: Frågan poolen inaktiva trådar|Antal|Medel|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|Inga dimensioner|
|QueryPoolJobQueueLength|Trådar: Frågan poolen jobbet kön lengt|Antal|Medel|Antal jobb i kö i trådpoolen för frågan.|Inga dimensioner|
|ShortParsingBusyThreads|Trådar: Kort parsning upptagen trådar|Antal|Medel|Antal upptagen trådar i kort tolkning trådpoolen.|Inga dimensioner|
|ShortParsingIdleThreads|Trådar: Kort parsning inaktiva trådar|Antal|Medel|Antalet inaktiva trådar i kort tolkning trådpoolen.|Inga dimensioner|
|ShortParsingJobQueueLength|Trådar: Kort parsning jobbet Kölängd|Antal|Medel|Antal jobb i kö för kort tolkning trådpoolen.|Inga dimensioner|
|memory_thrashing_metric|Minne knattrar|Procent|Medel|Genomsnittlig minne knattrar.|Inga dimensioner|
|mashup_engine_qpu_metric|M-motorn QPU|Antal|Medel|QPU användning av mashup-motorn processer|Inga dimensioner|
|mashup_engine_memory_metric|M-motorn minne|Byte|Medel|Minnesanvändning för mashup-motorn processer|Inga dimensioner|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|TotalRequests|Totalt antal Gateway-begäranden|Antal|Totalt|Antal begäranden för gateway|Plats, värdnamn|
|SuccessfulRequests|Rätt Gateway-begäranden|Antal|Totalt|Antalet begäranden som lyckats gateway|Plats, värdnamn|
|UnauthorizedRequests|Obehörig Gateway-begäranden|Antal|Totalt|Antal begäranden för obehörig gateway|Plats, värdnamn|
|FailedRequests|Gateway för misslyckade begäranden|Antal|Totalt|Antalet fel i gateway-begäranden|Plats, värdnamn|
|OtherRequests|Andra Gateway-begäranden|Antal|Totalt|Antal andra gateway-begäranden|Plats, värdnamn|
|Varaktighet|Varaktighet för Gateway-förfrågningar|Millisekunder|Medel|Övergripande varaktighet för Gateway-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Kapacitet (förhandsgranskning)|Procent|Maximal|Användning mått för ApiManagement service|Plats|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Inga dimensioner|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
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

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|connectedclients|Anslutna klienter|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed|Totalt antal åtgärder|Antal|Totalt||Inga dimensioner|
|cachehits|Cacheträffar|Antal|Totalt||Inga dimensioner|
|cachemisses|Cachemissar|Antal|Totalt||Inga dimensioner|
|getcommands|Hämtar|Antal|Totalt||Inga dimensioner|
|setcommands|Uppsättningar|Antal|Totalt||Inga dimensioner|
|evictedkeys|Avlägsnade nycklar|Antal|Totalt||Inga dimensioner|
|totalkeys|Totalt antal nycklar|Antal|Maximal||Inga dimensioner|
|expiredkeys|Utgångna nycklar|Antal|Totalt||Inga dimensioner|
|usedmemory|Använt minne|Byte|Maximal||Inga dimensioner|
|usedmemoryRss|Använt minne RSS|Byte|Maximal||Inga dimensioner|
|serverLoad|Serverbelastning|Procent|Maximal||Inga dimensioner|
|cacheWrite|Cacheskrivning|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead|Cacheläsning|BytesPerSecond|Maximal||Inga dimensioner|
|PercentProcessorTime|Processor|Procent|Maximal||Inga dimensioner|
|connectedclients0|Anslutna klienter (Fragmentera 0)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed0|Totalt antal åtgärder (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|cachehits0|Cacheträffar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|cachemisses0|Cachemissar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|getcommands0|Hämtar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|setcommands0|Anger (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|evictedkeys0|Avlägsnade nycklar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|totalkeys0|Totalt antal nycklar (Fragmentera 0)|Antal|Maximal||Inga dimensioner|
|expiredkeys0|Utgångna nycklar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|usedmemory0|Använt minne (Fragmentera 0)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss0|Använt minne RSS (Fragmentera 0)|Byte|Maximal||Inga dimensioner|
|serverLoad0|Belastningen på servern (Fragmentera 0)|Procent|Maximal||Inga dimensioner|
|cacheWrite0|Cache-skrivning (Fragmentera 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead0|Cache Läs (Fragmentera 0)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime0|CPU (Fragmentera 0)|Procent|Maximal||Inga dimensioner|
|connectedclients1|Anslutna klienter (Fragmentera 1)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed1|Totalt antal åtgärder (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|cachehits1|Cacheträffar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|cachemisses1|Cachemissar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|getcommands1|Hämtar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|setcommands1|Anger (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|evictedkeys1|Avlägsnade nycklar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|totalkeys1|Totalt antal nycklar (Fragmentera 1)|Antal|Maximal||Inga dimensioner|
|expiredkeys1|Utgångna nycklar (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
|usedmemory1|Använt minne (Fragmentera 1)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss1|Använt minne RSS (Fragmentera 1)|Byte|Maximal||Inga dimensioner|
|serverLoad1|Belastningen på servern (Fragmentera 1)|Procent|Maximal||Inga dimensioner|
|cacheWrite1|Cache-skrivning (Fragmentera 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead1|Cache Läs (Fragmentera 1)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime1|CPU (Fragmentera 1)|Procent|Maximal||Inga dimensioner|
|connectedclients2|Anslutna klienter (Fragmentera 2)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed2|Totalt antal åtgärder (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|cachehits2|Cacheträffar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|cachemisses2|Cachemissar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|getcommands2|Hämtar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|setcommands2|Anger (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|evictedkeys2|Avlägsnade nycklar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|totalkeys2|Totalt antal nycklar (Fragmentera 2)|Antal|Maximal||Inga dimensioner|
|expiredkeys2|Utgångna nycklar (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
|usedmemory2|Använt minne (Fragmentera 2)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss2|Använt minne RSS (Fragmentera 2)|Byte|Maximal||Inga dimensioner|
|serverLoad2|Belastningen på servern (Fragmentera 2)|Procent|Maximal||Inga dimensioner|
|cacheWrite2|Cache-skrivning (Fragmentera 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead2|Cache Läs (Fragmentera 2)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime2|CPU (Fragmentera 2)|Procent|Maximal||Inga dimensioner|
|connectedclients3|Anslutna klienter (Fragmentera 3)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed3|Totalt antal åtgärder (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|cachehits3|Cacheträffar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|cachemisses3|Cachemissar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|getcommands3|Hämtar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|setcommands3|Anger (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|evictedkeys3|Avlägsnade nycklar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|totalkeys3|Totalt antal nycklar (Fragmentera 3)|Antal|Maximal||Inga dimensioner|
|expiredkeys3|Utgångna nycklar (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
|usedmemory3|Använt minne (Fragmentera 3)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss3|Använt minne RSS (Fragmentera 3)|Byte|Maximal||Inga dimensioner|
|serverLoad3|Belastningen på servern (Fragmentera 3)|Procent|Maximal||Inga dimensioner|
|cacheWrite3|Cache-skrivning (Fragmentera 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead3|Cache Läs (Fragmentera 3)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime3|CPU (Fragmentera 3)|Procent|Maximal||Inga dimensioner|
|connectedclients4|Anslutna klienter (Fragmentera 4)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed4|Totalt antal åtgärder (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|cachehits4|Cacheträffar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|cachemisses4|Cachemissar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|getcommands4|Hämtar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|setcommands4|Anger (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|evictedkeys4|Avlägsnade nycklar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|totalkeys4|Totalt antal nycklar (Fragmentera 4)|Antal|Maximal||Inga dimensioner|
|expiredkeys4|Utgångna nycklar (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
|usedmemory4|Använt minne (Fragmentera 4)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss4|Använt minne RSS (Fragmentera 4)|Byte|Maximal||Inga dimensioner|
|serverLoad4|Belastningen på servern (Fragmentera 4)|Procent|Maximal||Inga dimensioner|
|cacheWrite4|Cache-skrivning (Fragmentera 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead4|Cache Läs (Fragmentera 4)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime4|CPU (Fragmentera 4)|Procent|Maximal||Inga dimensioner|
|connectedclients5|Anslutna klienter (Fragmentera 5)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed5|Totalt antal åtgärder (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|cachehits5|Cacheträffar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|cachemisses5|Cachemissar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|getcommands5|Hämtar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|setcommands5|Anger (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|evictedkeys5|Avlägsnade nycklar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|totalkeys5|Totalt antal nycklar (Fragmentera 5)|Antal|Maximal||Inga dimensioner|
|expiredkeys5|Utgångna nycklar (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
|usedmemory5|Använt minne (Fragmentera 5)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss5|Använt minne RSS (Fragmentera 5)|Byte|Maximal||Inga dimensioner|
|serverLoad5|Belastningen på servern (Fragmentera 5)|Procent|Maximal||Inga dimensioner|
|cacheWrite5|Cache-skrivning (Fragmentera 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead5|Cache Läs (Fragmentera 5)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime5|CPU (Fragmentera 5)|Procent|Maximal||Inga dimensioner|
|connectedclients6|Anslutna klienter (Fragmentera 6)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed6|Totalt antal åtgärder (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|cachehits6|Cacheträffar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|cachemisses6|Cachemissar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|getcommands6|Hämtar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|setcommands6|Anger (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|evictedkeys6|Avlägsnade nycklar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|totalkeys6|Totalt antal nycklar (Fragmentera 6)|Antal|Maximal||Inga dimensioner|
|expiredkeys6|Utgångna nycklar (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
|usedmemory6|Använt minne (Fragmentera 6)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss6|Använt minne RSS (Fragmentera 6)|Byte|Maximal||Inga dimensioner|
|serverLoad6|Belastningen på servern (Fragmentera 6)|Procent|Maximal||Inga dimensioner|
|cacheWrite6|Cache-skrivning (Fragmentera 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead6|Cache Läs (Fragmentera 6)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime6|CPU (Fragmentera 6)|Procent|Maximal||Inga dimensioner|
|connectedclients7|Anslutna klienter (Fragmentera 7)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed7|Totalt antal åtgärder (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|cachehits7|Cacheträffar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|cachemisses7|Cachemissar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|getcommands7|Hämtar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|setcommands7|Anger (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|evictedkeys7|Avlägsnade nycklar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|totalkeys7|Totalt antal nycklar (Fragmentera 7)|Antal|Maximal||Inga dimensioner|
|expiredkeys7|Utgångna nycklar (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
|usedmemory7|Använt minne (Fragmentera 7)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss7|Använt minne RSS (Fragmentera 7)|Byte|Maximal||Inga dimensioner|
|serverLoad7|Belastningen på servern (Fragmentera 7)|Procent|Maximal||Inga dimensioner|
|cacheWrite7|Cache-skrivning (Fragmentera 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead7|Cache Läs (Fragmentera 7)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime7|CPU (Fragmentera 7)|Procent|Maximal||Inga dimensioner|
|connectedclients8|Anslutna klienter (Fragmentera 8)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed8|Totalt antal åtgärder (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|cachehits8|Cacheträffar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|cachemisses8|Cachemissar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|getcommands8|Hämtar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|setcommands8|Anger (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|evictedkeys8|Avlägsnade nycklar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|totalkeys8|Totalt antal nycklar (Fragmentera 8)|Antal|Maximal||Inga dimensioner|
|expiredkeys8|Utgångna nycklar (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
|usedmemory8|Använt minne (Fragmentera 8)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss8|Använt minne RSS (Fragmentera 8)|Byte|Maximal||Inga dimensioner|
|serverLoad8|Belastningen på servern (Fragmentera 8)|Procent|Maximal||Inga dimensioner|
|cacheWrite8|Cache-skrivning (Fragmentera 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead8|Cache Läs (Fragmentera 8)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime8|CPU (Fragmentera 8)|Procent|Maximal||Inga dimensioner|
|connectedclients9|Anslutna klienter (Fragmentera 9)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed9|Totalt antal åtgärder (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|cachehits9|Cacheträffar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|cachemisses9|Cachemissar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|getcommands9|Hämtar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|setcommands9|Anger (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|evictedkeys9|Avlägsnade nycklar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|totalkeys9|Totalt antal nycklar (Fragmentera 9)|Antal|Maximal||Inga dimensioner|
|expiredkeys9|Utgångna nycklar (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
|usedmemory9|Använt minne (Fragmentera 9)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss9|Använt minne RSS (Fragmentera 9)|Byte|Maximal||Inga dimensioner|
|serverLoad9|Belastningen på servern (Fragmentera 9)|Procent|Maximal||Inga dimensioner|
|cacheWrite9|Cache-skrivning (Fragmentera 9)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead9|Cache Läs (Fragmentera 9)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime9|CPU (Fragmentera 9)|Procent|Maximal||Inga dimensioner|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medel|Procentandelen allokerat beräknings-enheter som för närvarande används av minst en virtuell dator.|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som tagits emot på alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik).|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte ut på alla nätverksgränssnitt som de virtuella datorerna (utgående trafik).|Inga dimensioner|
|Disk-lästa byte/sek|Diskläsning|BytesPerSecond|Medel|Genomsnittlig byte som har lästs från disken under perioden.|Inga dimensioner|
|Disk-skrivna byte/s|Skrivning till disk|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivs till disk under perioden.|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Diskläsning IOPS.|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Diskskrivning IOPS.|Inga dimensioner|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|TotalCalls|Totala anrop|Antal|Totalt|Totalt antal anrop.|Inga dimensioner|
|SuccessfulCalls|Utförda anrop|Antal|Totalt|Antal utförda anrop.|Inga dimensioner|
|TotalErrors|Totalt antal fel|Antal|Totalt|Totalt antal anrop med felsvar (HTTP-svarskod 44x eller 5xx).|Inga dimensioner|
|BlockedCalls|Blockerade anrop|Antal|Totalt|Antalet anrop som överskrider gränsen för hastighet eller kvot.|Inga dimensioner|
|ServerErrors|Serverfel|Antal|Totalt|Antal anrop med internt fel i tjänsten (HTTP-svarskod 5xx).|Inga dimensioner|
|ClientErrors|Klientfel|Antal|Totalt|Antal anrop med fel på klientsidan (HTTP-svarskod 4xx).|Inga dimensioner|
|DataIn|Data in|Byte|Totalt|Storlek på inkommande data i byte.|Inga dimensioner|
|DataOut|Data ut|Byte|Totalt|Storlek på utgående data i byte.|Inga dimensioner|
|Svarstid|Svarstid|Millisekunder|Medel|Svarstid i millisekunder.|Inga dimensioner|
|CharactersTranslated|Tecken som översätts|Antal|Totalt|Totalt antal tecken i inkommande begäran om text.|Inga dimensioner|
|SpeechSessionDuration|Tal varaktighet för sessionen|Sekunder|Totalt|Total varaktighet för tal-session i sekunder.|Inga dimensioner|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt antal byte som lästs från disk under övervakningsperioden|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Totalt antal byte som skrivs från disk under övervakningsperioden|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk|Inga dimensioner|
|CPU-kredit kvar|CPU-kredit kvar|Antal|Medel|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|CPU-krediter som används|CPU-krediter som används|Antal|Medel|Totalt antal krediter som används av den virtuella datorn|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt antal byte som lästs från disk under övervakningsperioden|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Totalt antal byte som skrivs från disk under övervakningsperioden|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk|Inga dimensioner|
|CPU-kredit kvar|CPU-kredit kvar|Antal|Medel|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|CPU-krediter som används|CPU-krediter som används|Antal|Medel|Totalt antal krediter som används av den virtuella datorn|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt antal byte som lästs från disk under övervakningsperioden|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Totalt antal byte som skrivs från disk under övervakningsperioden|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk|Inga dimensioner|
|CPU-kredit kvar|CPU-kredit kvar|Antal|Medel|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|CPU-krediter som används|CPU-krediter som används|Antal|Medel|Totalt antal krediter som används av den virtuella datorn|Inga dimensioner|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
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
|DCIProfilesCount|Antal instanser för profil|Antal|Sista||Inga dimensioner|
|DCIInteractionsPerMonthCount|Interaktioner antal per månad|Antal|Sista||Inga dimensioner|
|DCIKpisCount|KPI-antal|Antal|Sista||Inga dimensioner|
|DCISegmentsCount|Antal segment|Antal|Sista||Inga dimensioner|
|DCIPredictiveMatchPoliciesCount|Förutsägbara Matchningsantal|Antal|Sista||Inga dimensioner|
|DCIPredictionsCount|Förutsägelse antal|Antal|Sista||Inga dimensioner|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|JobEndedSuccess|Slutförda jobb|Antal|Totalt|Antal slutförda jobb.|Inga dimensioner|
|JobEndedFailure|Misslyckade jobb|Antal|Totalt|Antal misslyckade jobb.|Inga dimensioner|
|JobEndedCancelled|Avbrutna jobb|Antal|Totalt|Antal avbrutna jobb.|Inga dimensioner|
|JobAUEndedSuccess|Lyckad Australien tid|Sekunder|Totalt|Totalt antal Australien tid för slutförda jobb.|Inga dimensioner|
|JobAUEndedFailure|Misslyckade Australien tid|Sekunder|Totalt|Totalt antal Australien tid för misslyckade jobb.|Inga dimensioner|
|JobAUEndedCancelled|Annullerad Australien tid|Sekunder|Totalt|Total tid Australien för avbrutna jobb.|Inga dimensioner|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Byte|Maximal|Total mängd data som lagras på kontot.|Inga dimensioner|
|DataWritten|Data som skrivs|Byte|Totalt|Total mängd data som skrivs till kontot.|Inga dimensioner|
|DataRead|Lästa data|Byte|Totalt|Total mängd data har lästs från kontot.|Inga dimensioner|
|WriteRequests|Skrivåtgärder|Antal|Totalt|Antal data skrivåtgärder till kontot.|Inga dimensioner|
|ReadRequests|Läs begäranden|Antal|Totalt|Antal data läsbegäranden till kontot.|Inga dimensioner|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|compute_limit|Compute-gränsen för enhet|Antal|Medel|Compute-gränsen för enhet|Inga dimensioner|
|compute_consumption_percent|Beräkna procentandelen enhet|Procent|Medel|Beräkna procentandelen enhet|Inga dimensioner|
|memory_percent|Minne|Procent|Medel|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Procent|Medel|IO-procent|Inga dimensioner|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagringsgränsen|Byte|Medel|Lagringsgränsen|Inga dimensioner|
|active_connections|Totalt antal aktiva anslutningar|Antal|Medel|Totalt antal aktiva anslutningar|Inga dimensioner|
|connections_failed|Totalt antal misslyckade anslutningar|Antal|Medel|Totalt antal misslyckade anslutningar|Inga dimensioner|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|compute_limit|Compute-gränsen för enhet|Antal|Medel|Compute-gränsen för enhet|Inga dimensioner|
|compute_consumption_percent|Beräkna procentandelen enhet|Procent|Medel|Beräkna procentandelen enhet|Inga dimensioner|
|memory_percent|Minne|Procent|Medel|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Procent|Medel|IO-procent|Inga dimensioner|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagringsgränsen|Byte|Medel|Lagringsgränsen|Inga dimensioner|
|active_connections|Totalt antal aktiva anslutningar|Antal|Medel|Totalt antal aktiva anslutningar|Inga dimensioner|
|connections_failed|Totalt antal misslyckade anslutningar|Antal|Medel|Totalt antal misslyckade anslutningar|Inga dimensioner|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
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
|d2c.endpoints.latency.eventHubs|Meddelandet svarstid för Event Hub-slutpunkter|Millisekunder|Medel|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång i Event Hub slutpunkt, i millisekunder|Inga dimensioner|
|d2c.endpoints.egress.serviceBusQueues|Skicka meddelanden till Service Bus-kö slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till Service Bus-kö slutpunkter|Inga dimensioner|
|d2c.endpoints.latency.serviceBusQueues|Meddelandet svarstid för Service Bus-kö-slutpunkter|Millisekunder|Medel|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång till en slutpunkt för Service Bus-kö, i millisekunder|Inga dimensioner|
|d2c.endpoints.egress.serviceBusTopics|Skicka meddelanden till Service Bus-ämne slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till Service Bus-ämne slutpunkter|Inga dimensioner|
|d2c.endpoints.latency.serviceBusTopics|Meddelandet svarstiden för Service Bus-ämne slutpunkter|Millisekunder|Medel|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång till en slutpunkt för Service Bus-ämne, i millisekunder|Inga dimensioner|
|d2c.endpoints.egress.builtIn.events|Skicka meddelanden till inbyggd slutpunkt (meddelanden/händelser)|Antal|Totalt|Antal gånger som meddelanden som skrivits till inbyggd slutpunkt (meddelanden/händelser)|Inga dimensioner|
|d2c.endpoints.latency.builtIn.events|Meddelandet svarstid för inbyggd slutpunkt (meddelanden/händelser)|Millisekunder|Medel|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång i inbyggd slutpunkt (meddelanden/händelser), i millisekunder |Inga dimensioner|
|d2c.endpoints.egress.Storage|Meddelanden som har levererats till lagring slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till lagring slutpunkter|Inga dimensioner|
|d2c.endpoints.latency.Storage|Meddelandet svarstid för lagring-slutpunkter|Millisekunder|Medel|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång till en slutpunkt för lagring, i millisekunder|Inga dimensioner|
|d2c.endpoints.egress.Storage.bytes|Data skrivs till lagring|Byte|Totalt|Mängden data i byte som skrivits till lagring slutpunkter|Inga dimensioner|
|d2c.endpoints.egress.Storage.BLOBs|BLOB skrivs till lagring|Antal|Totalt|Antal blobbar som skrivs till lagring slutpunkter|Inga dimensioner|
|d2c.Twin.Read.Success|Lyckad dubbla läser från enheter|Antal|Totalt|Antal alla lyckade enhet initieras dubbla läser.|Inga dimensioner|
|d2c.Twin.Read.failure|Det gick inte dubbla läsningar av enheter|Antal|Totalt|Antalet alla misslyckades enhet initieras dubbla läsningar.|Inga dimensioner|
|d2c.Twin.Read.size|Svarsstorlek dubbla läsningar av enheter|Byte|Medel|Medel, min och max för alla lyckade enhet initieras dubbla läsningar.|Inga dimensioner|
|d2c.Twin.Update.Success|Lyckad dubbla uppdateringar från enheter|Antal|Totalt|Antal uppdateringar för alla lyckade enhet initieras dubbla.|Inga dimensioner|
|d2c.Twin.Update.failure|Det gick inte dubbla uppdateringar från enheter|Antal|Totalt|Antalet alla misslyckades enhet initieras dubbla uppdateringar.|Inga dimensioner|
|d2c.Twin.Update.size|Storleken på dubbla uppdateringar från enheter|Byte|Medel|Medel, min och max storlek för alla lyckade enhet initieras dubbla uppdateringar.|Inga dimensioner|
|c2d.methods.Success|Lyckad direkta metoden anrop|Antal|Totalt|Antal alla lyckade direkt metodanrop.|Inga dimensioner|
|c2d.methods.failure|Det gick inte direkt metod anrop|Antal|Totalt|Det gick inte att direkt metodanrop antalet alla.|Inga dimensioner|
|c2d.methods.requestSize|Storleken på direkta metoden anrop för begäran|Byte|Medel|Medel, min och max för alla lyckade direkta metodbegäranden.|Inga dimensioner|
|c2d.methods.responseSize|Storlek för direkta metoden anrop|Byte|Medel|Medel, min och max för alla lyckade direkta metoden svar.|Inga dimensioner|
|c2d.Twin.Read.Success|Lyckad dubbla läser från serverdelen|Antal|Totalt|Antal alla lyckade tillbaka-end-initierad dubbla läser.|Inga dimensioner|
|c2d.Twin.Read.failure|Misslyckade dubbla läser från serverdelen|Antal|Totalt|Antalet alla misslyckades tillbaka-end-initierad dubbla läsningar.|Inga dimensioner|
|c2d.Twin.Read.size|Storlek för dubbla läser från serverdelen|Byte|Medel|Medel, min och max för alla lyckade tillbaka-end-initierade dubbla läsningar.|Inga dimensioner|
|c2d.Twin.Update.Success|Lyckad dubbla uppdateringar från serverdelen|Antal|Totalt|Antal uppdateringar för alla lyckade tillbaka-end-initierad dubbla.|Inga dimensioner|
|c2d.Twin.Update.failure|Misslyckade dubbla uppdateringar från serverdelen|Antal|Totalt|Antalet alla misslyckades tillbaka-end-initierad dubbla uppdateringar.|Inga dimensioner|
|c2d.Twin.Update.size|Storleken på dubbla uppdateringar från serverdelen|Byte|Medel|Medel, min och max storlek för alla lyckade tillbaka-end-initierad dubbla uppdateringar.|Inga dimensioner|
|twinQueries.success|Lyckad dubbla frågor|Antal|Totalt|Antal alla lyckade dubbla frågor.|Inga dimensioner|
|twinQueries.failure|Misslyckade dubbla frågor|Antal|Totalt|Antal alla misslyckade dubbla frågor.|Inga dimensioner|
|twinQueries.resultSize|Dubbla frågor storlek|Byte|Medel|Medel, min och max resultatet av alla lyckade dubbla frågor storlek.|Inga dimensioner|
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
|dailyMessageQuotaUsed|Sammanlagt antal meddelanden som används|Antal|Medel|Antal Totalt antal meddelanden som används idag. Detta är en kumulativ värde som har återställts till noll i UTC 00:00 varje dag.|Inga dimensioner|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|RegistrationAttempts|Registrering försök|Antal|Totalt|Antal enheter registreringar försökte|ProvisioningServiceName IotHubName, Status|
|DeviceAssignments|Enheter som är tilldelade|Antal|Totalt|Antalet enheter som har tilldelats en IoT-hubb|ProvisioningServiceName IotHubName|
|AttestationAttempts|Attesteringen försök|Antal|Totalt|Antalet enheter intyg försökte|ProvisioningServiceName, Status, protokollet|

## <a name="microsoftdocumentdbdatabaseaccounts-cosmosdb"></a>Microsoft.DocumentDB/databaseAccounts (CosmosDB)
|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|TotalRequests|Totalt antal begäranden|Antal|Antal|Antal begäranden|DatabaseAccount, samlingsnamn, DatabaseName, Region, StatusCode|
|MongoRequests|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden|DatabaseAccount, samlingsnamn, DatabaseName, Region, ErrorCode, CommandName|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|INREQS|Skicka förfrågningar|Antal|Totalt|Totalt antal inkommande skickar begäranden för ett namnområde|Inga dimensioner|
|SUCCREQ|Slutförda förfrågningar|Antal|Totalt|Totalt antal slutförda begäranden för ett namnområde|Inga dimensioner|
|FAILREQ|Misslyckade förfrågningar|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde|Inga dimensioner|
|SVRBSY|Upptagen server|Antal|Totalt|Totalt antal fel med upptagen server för ett namnområde|Inga dimensioner|
|INTERR|Interna serverfel|Antal|Totalt|Totalt antal interna serverfel för ett namnområde|Inga dimensioner|
|MISCERR|Övriga fel|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde|Inga dimensioner|
|INMSGS|Inkommande meddelanden (föråldrad)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde. Det här måttet är föråldrad. Använd mått för inkommande meddelanden i stället|Inga dimensioner|
|EHINMSGS|Inkommande meddelanden|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde|Inga dimensioner|
|OUTMSGS|Utgående meddelanden (föråldrad)|Antal|Totalt|Total utgående meddelanden för ett namnområde. Det här måttet är föråldrad. Använd i stället utgående meddelanden mått|Inga dimensioner|
|EHOUTMSGS|Utgående meddelanden|Antal|Totalt|Totalt antal utgående meddelanden för ett namnområde|Inga dimensioner|
|EHINMBS|Inkommande byte (inaktuell)|Byte|Totalt|Event Hub inkommande genomströmningen för ett namnområde. Det här måttet är föråldrad. Använd i stället inkommande byte mått|Inga dimensioner|
|EHINBYTES|Inkommande byte|Byte|Totalt|Dataflöde för inkommande meddelanden för händelsehubb för en namnområde|Inga dimensioner|
|EHOUTMBS|Utgående byte (inaktuell)|Byte|Totalt|Hubb utgående meddelande händelsegenomflöde för ett namnområde. Det här måttet är föråldrad. Använd i stället utgående byte mått|Inga dimensioner|
|EHOUTBYTES|Utgående byte|Byte|Totalt|Dataflöde för utgående meddelanden för händelsehubb för ett namnområde|Inga dimensioner|
|EHABL|Arkivera kvarvarande meddelanden|Antal|Totalt|Arkiverade meddelanden för händelsehubb i kvarvarande uppgifter för ett namnområde|Inga dimensioner|
|EHAMSGS|Arkivera meddelanden|Antal|Totalt|Arkiverade meddelanden för händelsehubb i ett namnområde|Inga dimensioner|
|EHAMBS|Arkivera meddelandedataflöde|Byte|Totalt|Dataflöde för arkiverade meddelanden för händelsehubb i ett namnområde|Inga dimensioner|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerade värde|Antal|Medel|Värdet som beräknats med Autoskala när den körs|MetricTriggerSource|
|MetricThreshold|Mått tröskelvärde|Antal|Medel|Konfigurerade Autoskala tröskelvärdet när Autoskala kördes.|MetricTriggerRule|
|ObservedCapacity|Observerade kapacitet|Antal|Medel|Kapaciteten rapporterade Autoskala när den körs.|Inga dimensioner|
|ScaleActionsInitiated|Skalningsåtgärder initieras|Antal|Totalt|Riktningen för åtgärden.|ScaleDirection|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Antal|Totalt|Antal startade arbetsflödeskörningar.|Inga dimensioner|
|RunsCompleted|Slutförda körningar|Antal|Totalt|Antal slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunsSucceeded|Lyckade körningar|Antal|Totalt|Antal lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsFailed|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsCancelled|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbetsflödeskörningar.|Inga dimensioner|
|RunLatency|Svarstid för körning|Sekunder|Medel|Svarstid för slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunSuccessLatency|Svarstid för lyckad körning|Sekunder|Medel|Svarstid för lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunThrottledEvents|Kör begränsade händelser|Antal|Totalt|Antal arbetsflödesåtgärds- eller utlösarbegränsade händelser.|Inga dimensioner|
|RunFailurePercentage|Kör felprocent|Procent|Totalt|Misslyckade arbetsflödeskörningar i procent.|Inga dimensioner|
|ActionsStarted|Startade åtgärder |Antal|Totalt|Antal startade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsCompleted|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSucceeded|Lyckade åtgärder |Antal|Totalt|Antal lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsFailed|Misslyckade åtgärder|Antal|Totalt|Antal misslyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSkipped|Överhoppade åtgärder |Antal|Totalt|Antal överhoppade arbetsflödesåtgärder.|Inga dimensioner|
|ActionLatency|Svarstid för åtgärd |Sekunder|Medel|Svarstid för slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionSuccessLatency|Svarstid för lyckad åtgärd |Sekunder|Medel|Svarstid för lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionThrottledEvents|Begränsade händelser för åtgärder|Antal|Totalt|Antal begränsade händelser för arbetsflödesåtgärder.|Inga dimensioner|
|TriggersStarted|Startade utlösare |Antal|Totalt|Antal startade arbetsflödesutlösare.|Inga dimensioner|
|TriggersCompleted|Slutförda utlösare |Antal|Totalt|Antal slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggersSucceeded|Lyckade utlösare |Antal|Totalt|Antal lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggersFailed|Misslyckade utlösare |Antal|Totalt|Antal misslyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggersSkipped|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbetsflödesutlösare.|Inga dimensioner|
|TriggersFired|Aktiverade utlösare |Antal|Totalt|Antal utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerLatency|Latensutlösare |Sekunder|Medel|Svarstid för slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggerFireLatency|Brandlatensutlösare |Sekunder|Medel|Svartstid för utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerSuccessLatency|Framgångslatensutlösare |Sekunder|Medel|Svartstid för lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggerThrottledEvents|Utlösare för begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbetsflödesutlösare.|Inga dimensioner|
|BillableActionExecutions|Fakturerbara åtgärdskörningar|Antal|Totalt|Antal körningar av arbetsflödesåtgärder som faktureras.|Inga dimensioner|
|BillableTriggerExecutions|Fakturerbara utlösarkörningar|Antal|Totalt|Antal körningar av arbetsflödesutlösare som faktureras.|Inga dimensioner|
|TotalBillableExecutions|Totalt antal fakturerbara körningar|Antal|Totalt|Antal arbetsflödeskörningar som faktureras.|Inga dimensioner|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|VipAvailability|VIP-tillgänglighet|Antal|Medel|Tillgängligheten för VIP-slutpunkter, baserat på avsökning|VipAddress VipPort|
|DipAvailability|DIP tillgänglighet|Antal|Medel|Tillgängligheten för DIP slutpunkter, baserat på avsökning|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som överförs inom tidsperiod|VipAddress VipPort, riktning|
|PacketCount|Antal|Antal|Totalt|Totalt antal paket som skickas inom tidsperiod|VipAddress VipPort, riktning|
|SYNCount|SYN antal|Antal|Totalt|Totalt antal SYN-paket som skickas inom tidsperiod|VipAddress VipPort, riktning|
|SnatConnectionCount|SNAT Anslutningsräknare|Antal|Totalt|Totalt antal nya SNAT anslutningar som skapats i tidsperiod|VipAddress DipAddress, ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|PacketsInDDoS|Inkommande paket DDoS|CountPerSecond|Medel|Inkommande paket DDoS|Inga dimensioner|
|PacketsDroppedDDoS|Inkommande paket som tas bort DDoS|CountPerSecond|Medel|Inkommande paket som tas bort DDoS|Inga dimensioner|
|PacketsForwardedDDoS|Inkommande paket som vidarebefordras DDoS|CountPerSecond|Medel|Inkommande paket som vidarebefordras DDoS|Inga dimensioner|
|TCPPacketsInDDoS|Inkommande TCP-paket DDoS|CountPerSecond|Medel|Inkommande TCP-paket DDoS|Inga dimensioner|
|TCPPacketsDroppedDDoS|Inkommande TCP-paket som tas bort DDoS|CountPerSecond|Medel|Inkommande TCP-paket som tas bort DDoS|Inga dimensioner|
|TCPPacketsForwardedDDoS|Inkommande TCP-paket som vidarebefordras DDoS|CountPerSecond|Medel|Inkommande TCP-paket som vidarebefordras DDoS|Inga dimensioner|
|UDPPacketsInDDoS|Inkommande UDP-paket DDoS|CountPerSecond|Medel|Inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsDroppedDDoS|Inkommande UDP-paket som tas bort DDoS|CountPerSecond|Medel|Inkommande UDP-paket som tas bort DDoS|Inga dimensioner|
|UDPPacketsForwardedDDoS|Inkommande UDP-paket som vidarebefordras DDoS|CountPerSecond|Medel|Inkommande UDP-paket som vidarebefordras DDoS|Inga dimensioner|
|BytesInDDoS|Inkommande byte DDoS|BytesPerSecond|Medel|Inkommande byte DDoS|Inga dimensioner|
|BytesDroppedDDoS|Inkommande byte bort DDoS|BytesPerSecond|Medel|Inkommande byte bort DDoS|Inga dimensioner|
|BytesForwardedDDoS|Inkommande byte vidarebefordras DDoS|BytesPerSecond|Medel|Inkommande byte vidarebefordras DDoS|Inga dimensioner|
|TCPBytesInDDoS|Inkommande TCP byte DDoS|BytesPerSecond|Medel|Inkommande TCP byte DDoS|Inga dimensioner|
|TCPBytesDroppedDDoS|Inkommande TCP-byte bort DDoS|BytesPerSecond|Medel|Inkommande TCP-byte bort DDoS|Inga dimensioner|
|TCPBytesForwardedDDoS|Inkommande TCP-byte vidarebefordras DDoS|BytesPerSecond|Medel|Inkommande TCP-byte vidarebefordras DDoS|Inga dimensioner|
|UDPBytesInDDoS|Inkommande UDP byte DDoS|BytesPerSecond|Medel|Inkommande UDP byte DDoS|Inga dimensioner|
|UDPBytesDroppedDDoS|Inkommande UDP byte bort DDoS|BytesPerSecond|Medel|Inkommande UDP byte bort DDoS|Inga dimensioner|
|UDPBytesForwardedDDoS|Inkommande UDP byte vidarebefordras DDoS|BytesPerSecond|Medel|Inkommande UDP byte vidarebefordras DDoS|Inga dimensioner|
|IfUnderDDoSAttack|Under DDoS attacker eller inte|Antal|Medel|Under DDoS attacker eller inte|Inga dimensioner|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-lösning|CountPerSecond|Medel|Inkommande TCP-paket för att utlösa DDoS-lösning|Inga dimensioner|
|DDoSTriggerUDPPackets|Inkommande UDP-paket för att utlösa DDoS-lösning|CountPerSecond|Medel|Inkommande UDP-paket för att utlösa DDoS-lösning|Inga dimensioner|
|VipAvailability|Tillgänglighet|Antal|Medel|Medeltillgänglighet IP-adress inom tidsperiod|Port|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som överförs inom tidsperiod|Port, riktning|
|PacketCount|Antal|Antal|Totalt|Totalt antal paket som skickas inom tidsperiod|Port, riktning|
|SynCount|SYN antal|Antal|Totalt|Totalt antal SYN-paket som skickas inom tidsperiod|Port, riktning|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Medel|Antalet byte per sekund som Application Gateway har meddelat|Inga dimensioner|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Tunnel bandbredd|BytesPerSecond|Medel|Genomsnittlig bandbredden för en tunnel i byte per sekund|ConnectionName RemoteIP|
|TunnelPeakBandwidth|Tunnel belastning bandbredd|BytesPerSecond|Medel|Belastning bandbredden för en tunnel i byte per sekund|ConnectionName RemoteIP|
|TunnelEgressBytes|Tunnel utgående byte|Byte|Medel|Utgående byte en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelIngressBytes|Tunnel ingång byte|Byte|Medel|Inkommande byte en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelEgressPackets|Tunnel utgående paket|Antal|Medel|Utgående antal en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelIngressPackets|Tunnel ingång paket|Antal|Medel|Inkommande antal en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel utgång TS matchningsfel paket släpp|Antal|Medel|Släpp för utgående paket från trafik selector matchar inte en tunnel i fem minuterna|ConnectionName RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel ingång TS matchningsfel paket släpp|Antal|Medel|Inkommande paket släpp från trafik selector matchar inte en tunnel i fem minuterna|ConnectionName RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|BytesIn är större än|BytesIn är större än|Antal|Totalt|Byte ingressing Azure|Inga dimensioner|
|BytesOut är större än|BytesOut är större än|Antal|Totalt|Byte egressing Azure|Inga dimensioner|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS ingressing Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS egressing Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|QpsByEndpoint|Frågor av slutpunkten som returnerade|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den angivna tiden|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status för endpoint av slutpunkten|Antal|Maximal|1 om en slutpunkt avsökningen status är ”aktiverad”, annars 0.|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|Registration.all|Registreringsåtgärder|Antal|Totalt|Totalt antal genomförda registreringsåtgärder (skapa, uppdatera, fråga och ta bort). |Inga dimensioner|
|Registration.Create|Registreringsskaparåtgärder|Antal|Totalt|Totalt antal registreringar som skapats.|Inga dimensioner|
|Registration.Update|Registreringsuppdateringsåtgärder|Antal|Totalt|Totalt antal genomförda registreringsuppdateringar.|Inga dimensioner|
|Registration.Get|Registreringsläsningsåtgärder|Antal|Totalt|Totalt antal utförda registreringsfrågor.|Inga dimensioner|
|Registration.delete|Registreringsborttagningsåtgärder|Antal|Totalt|Totalt antal registreringar som tagits bort.|Inga dimensioner|
|inkommande|Inkommande meddelanden|Antal|Totalt|Totalt antal API-anrop som skickats. |Inga dimensioner|
|Incoming.Scheduled|Schemalagt push-meddelande har skickats|Antal|Totalt|Schemalagt push-meddelande har avbrutits|Inga dimensioner|
|Incoming.Scheduled.Cancel|Schemalagt push-meddelande har avbrutits|Antal|Totalt|Schemalagt push-meddelande har avbrutits|Inga dimensioner|
|Scheduled.Pending|Schemalagda meddelanden som väntar|Antal|Totalt|Schemalagda meddelanden som väntar|Inga dimensioner|
|installation.all|Installationshanteringsåtgärder|Antal|Totalt|Installationshanteringsåtgärder|Inga dimensioner|
|installation.Get|Åtgärder för att hämta installation|Antal|Totalt|Åtgärder för att hämta installation|Inga dimensioner|
|installation.upsert|Skapa eller uppdatera installationsåtgärder|Antal|Totalt|Skapa eller uppdatera installationsåtgärder|Inga dimensioner|
|installation.patch|Åtgärder för att korrigera installation|Antal|Totalt|Åtgärder för att korrigera installation|Inga dimensioner|
|installation.delete|Åtgärder för att ta bort installation|Antal|Totalt|Åtgärder för att ta bort installation|Inga dimensioner|
|Outgoing.allpns.Success|Levererade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|Outgoing.allpns.invalidpayload|Nyttolastfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS returnerade ett nyttolastfel.|Inga dimensioner|
|Outgoing.allpns.pnserror|Externa meddelandesystemfel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med PNS (inte autentiseringsproblem).|Inga dimensioner|
|Outgoing.allpns.channelerror|Kanalfel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att kanalen var ogiltig, inte kopplad till rätt app, begränsad eller hade upphört att gälla.|Inga dimensioner|
|Outgoing.allpns.badorexpiredchannel|Felaktig eller utgången kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av utgången eller ogiltig kanal/token/registrationId i registreringen.|Inga dimensioner|
|Outgoing.wns.Success|WNS – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|Outgoing.wns.invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade. (Windows Live kan inte identifiera autentiseringsuppgifterna).|Inga dimensioner|
|Outgoing.wns.badchannel|WNS – felaktig kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inga dimensioner|
|Outgoing.wns.expiredchannel|WNS – utgången kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att ChannelURI har upphört att gälla (WNS-status: 410 Sidan finns inte).|Inga dimensioner|
|Outgoing.wns.throttled|WNS – begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom WNS begränsar den här appen (WNS-status: 406 Ogiltigt format).|Inga dimensioner|
|Outgoing.wns.tokenproviderunreachable|WNS-auktoriseringsfel (kan inte nås)|Antal|Totalt|Windows Live kan inte nås.|Inga dimensioner|
|Outgoing.wns.invalidtoken|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Token som angavs i WNS är inte giltig (WNS-status: 401 Ej behörig).|Inga dimensioner|
|Outgoing.wns.wrongtoken|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS status: 403 nekad). Detta kan inträffa om ChannelURI i registreringen är associerad med en annan app. Kontrollera att klientappen är associerad med samma app vars autentiseringsuppgifter finns i meddelandehubben.|Inga dimensioner|
|Outgoing.wns.invalidnotificationformat|WNS – ogiltigt meddelandeformat|Antal|Totalt|Formatet för meddelandet är ogiltigt (WNS status: 400). Observera att WNS inte avvisa alla ogiltig nyttolaster.|Inga dimensioner|
|Outgoing.wns.invalidnotificationsize|WNS – ogiltig meddelandestorlek|Antal|Totalt|Meddelandets nyttolast är för stor (WNS-status: 413).|Inga dimensioner|
|Outgoing.wns.channelthrottled|WNS – begränsad kanal|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svarshuvud: X-WNS-NotificationStatus:channelThrottled).|Inga dimensioner|
|Outgoing.wns.channeldisconnected|WNS – frånkopplad kanal|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svarshuvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|Outgoing.wns.dropped|WNS – utelämnade meddelanden|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: utelämnat men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|Outgoing.wns.pnserror|WNS-fel|Antal|Totalt|Meddelandet levererades inte på grund av kommunikationsproblem med WNS.|Inga dimensioner|
|Outgoing.wns.authenticationerror|WNS-auktoriseringsfel|Antal|Totalt|Meddelanden som inte levererats på grund av kommunikationsproblem med ogiltiga Windows Live-autentiseringsuppgifter eller fel token.|Inga dimensioner|
|Outgoing.apns.Success|APNS – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|Outgoing.apns.invalidcredentials|APNs-auktoriseringsfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|Outgoing.apns.badchannel|APNS – felaktig kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av ogiltig token (APNs-statuskod: 8).|Inga dimensioner|
|Outgoing.apns.expiredchannel|APNS – utgången kanal|Antal|Totalt|Antal token som ogiltigförklarades av APNs-feedbackkanalen.|Inga dimensioner|
|Outgoing.apns.invalidnotificationsize|APNS – ogiltig meddelandestorlek|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av för stor nyttolast (APNs-statuskod: 7).|Inga dimensioner|
|Outgoing.apns.pnserror|APNS-fel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med APNs.|Inga dimensioner|
|Outgoing.GCM.Success|GCM – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|Outgoing.GCM.invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|Outgoing.GCM.badchannel|GCM – felaktig kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att registrationId i registreringen inte kändes igen (GCM-resultat: ogiltig registrering).|Inga dimensioner|
|Outgoing.GCM.expiredchannel|GCM – utgången kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|Outgoing.GCM.throttled|GCM – begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att GCM begränsar appen (GCM-statuskod: 501-599 eller resultat:Unavailable).|Inga dimensioner|
|Outgoing.GCM.invalidnotificationformat|GCM – ogiltigt meddelandeformat|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom nyttolasten var felformaterad (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|Outgoing.GCM.invalidnotificationsize|GCM – ogiltig meddelandestorlek|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av för stor nyttolast (GCM-resultat: MessageTooBig).|Inga dimensioner|
|Outgoing.GCM.wrongchannel|GCM – fel kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att registrationId i registreringen inte är kopplat till den aktuella appen (GCM-resultat: InvalidPackageName).|Inga dimensioner|
|Outgoing.GCM.pnserror|GCM-fel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med GCM.|Inga dimensioner|
|Outgoing.GCM.authenticationerror|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna, autentiseringsuppgifterna var blockerade eller SenderId var felkonfigurerat i appen (GCM-resultat: MismatchedSenderId).|Inga dimensioner|
|Outgoing.mpns.Success|MPNS – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|Outgoing.mpns.invalidcredentials|MPNS – ogiltiga autentiseringsuppgifter|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|Outgoing.mpns.badchannel|MPNS – felaktig kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inga dimensioner|
|Outgoing.mpns.throttled|MPNS – begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom MPNS begränsar den här appen (WNS MPNS: 406 Ogiltigt format).|Inga dimensioner|
|Outgoing.mpns.invalidnotificationformat|MPNS – ogiltigt meddelandeformat|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom nyttolasten för meddelanden var för stor.|Inga dimensioner|
|Outgoing.mpns.channeldisconnected|MPNS – frånkopplad kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att ChannelURI i registreringen frånkopplades (MPNS-status: 412 hittades inte).|Inga dimensioner|
|Outgoing.mpns.dropped|MPNS – utelämnade meddelanden|Antal|Totalt|Antal push-meddelanden som utelämnats av MPNS (MPNS-svarshuvud: X-NotificationStatus: QueueFull eller Suppressed).|Inga dimensioner|
|Outgoing.mpns.pnserror|MPNS-fel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med MPNS.|Inga dimensioner|
|Outgoing.mpns.authenticationerror|MPNS-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|notificationhub.pushes|Alla utgående meddelanden|Antal|Totalt|Alla utgående meddelanden för notification hub|Inga dimensioner|
|Incoming.all.Requests|Alla inkommande förfrågningar|Antal|Totalt|Totalt antal inkommande begäranden för en meddelandehubb|Inga dimensioner|
|Incoming.all.failedrequests|Alla inkommande förfrågningar som inte kunde slutföras|Antal|Totalt|Totalt antal misslyckade förfrågningar för en meddelandehubb|Inga dimensioner|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|SearchLatency|Sök svarstid|Sekunder|Medel|Sök genomsnittlig svarstid för search-tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Sökfrågor per sekund|CountPerSecond|Medel|Sökfrågor per sekund för search-tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Begränsade sökfrågor i procent|Procent|Medel|Procentandelen sökfrågor som har begränsats för söktjänsten|Inga dimensioner|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|CPUXNS|Processoranvändning per namnområde|Procent|Maximal|CPU-användningsmått för Service Bus Premium-namnområde|Inga dimensioner|
|WSXNS|Minnesstorleksanvändning per namnområde|Procent|Maximal|Minnesanvändningsmått för Service Bus Premium-namnområde|Inga dimensioner|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inga dimensioner|
|log_write_percent|Loggen IO-procent|Procent|Medel|Loggen IO-procent|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|Inga dimensioner|
|lagring|Totalt antal databasens storlek|Byte|Maximal|Totalt antal databasens storlek|Inga dimensioner|
|connection_successful|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|connection_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|blocked_by_firewall|Blockeras av brandvägg|Antal|Totalt|Blockeras av brandvägg|Inga dimensioner|
|deadlock|Deadlocks|Antal|Totalt|Deadlocks|Inga dimensioner|
|storage_percent|Databasstorlek i procent|Procent|Maximal|Databasstorlek i procent|Inga dimensioner|
|xtp_storage_percent|Minnesintern OLTP lagring procent|Procent|Medel|Minnesintern OLTP lagring procent|Inga dimensioner|
|workers_percent|Procentsatsen för arbetare|Procent|Medel|Procentsatsen för arbetare|Inga dimensioner|
|sessions_percent|Sessioner procent|Procent|Medel|Sessioner procent|Inga dimensioner|
|dtu_limit|DTU gräns|Antal|Medel|DTU gräns|Inga dimensioner|
|dtu_used|DTU används|Antal|Medel|DTU används|Inga dimensioner|
|dwu_limit|DWU gräns|Antal|Maximal|DWU gräns|Inga dimensioner|
|dwu_consumption_percent|DWU-procent|Procent|Maximal|DWU-procent|Inga dimensioner|
|dwu_used|DWU används|Antal|Maximal|DWU används|Inga dimensioner|
|dw_cpu_percent|DW nivån CPU-procent|Procent|Medel|DW nivån CPU-procent|dw_logical_node_id|
|dw_physical_data_read_percent|DW nod nivån Data IO-procent|Procent|Medel|DW nod nivån Data IO-procent|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|database_cpu_percent|CPU-procent|Procent|Medel|CPU-procent|DatabaseResourceId|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inga dimensioner|
|database_physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|DatabaseResourceId|
|log_write_percent|Loggen IO-procent|Procent|Medel|Loggen IO-procent|Inga dimensioner|
|database_log_write_percent|Loggen IO-procent|Procent|Medel|Loggen IO-procent|DatabaseResourceId|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|Inga dimensioner|
|database_dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|DatabaseResourceId|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inga dimensioner|
|workers_percent|Procentsatsen för arbetare|Procent|Medel|Procentsatsen för arbetare|Inga dimensioner|
|database_workers_percent|Procentsatsen för arbetare|Procent|Medel|Procentsatsen för arbetare|DatabaseResourceId|
|sessions_percent|Sessioner procent|Procent|Medel|Sessioner procent|Inga dimensioner|
|database_sessions_percent|Sessioner procent|Procent|Medel|Sessioner procent|DatabaseResourceId|
|eDTU_limit|eDTU gräns|Antal|Medel|eDTU gräns|Inga dimensioner|
|storage_limit|Lagringsgränsen|Byte|Medel|Lagringsgränsen|Inga dimensioner|
|eDTU_used|eDTU används|Antal|Medel|eDTU används|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|database_storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|DatabaseResourceId|
|xtp_storage_percent|Minnesintern OLTP lagring procent|Procent|Medel|Minnesintern OLTP lagring procent|Inga dimensioner|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|DatabaseResourceId ElasticPoolResourceId|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|ElasticPoolResourceId|
|database_storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|DatabaseResourceId ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Medel|Konto som används för kapacitet|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-kapacitet|Byte|Medel|Mängden lagringsutrymme som används av storage-konto Blob-tjänsten i byte.|BlobType|
|BlobCount|BLOB-antal|Antal|Medel|Antal Blob storage-konto Blob-tjänsten.|BlobType|
|ContainerCount|Antal för BLOB-behållare|Antal|Medel|Antal behållare i Blob storage-konto-tjänsten.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|TableCapacity|Tabell kapacitet|Byte|Medel|Mängden lagringsutrymme som används av storage-konto tabelltjänsten i byte.|Inga dimensioner|
|TableCount|Tabellen antal|Antal|Medel|Numret på tabellen i tabelltjänsten för storage-konto.|Inga dimensioner|
|TableEntityCount|Antal enheter för tabellen|Antal|Medel|Antal tabellentiteter i tabelltjänsten för storage-konto.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|QueueCapacity|Kön kapacitet|Byte|Medel|Mängden lagringsutrymme som används av storage-konto kötjänsten i byte.|Inga dimensioner|
|QueueCount|Antal i åtgärdskö|Antal|Medel|Antal kön i Kötjänsten för storage-konto.|Inga dimensioner|
|QueueMessageCount|Antalet meddelanden för kön|Antal|Medel|Det ungefärliga antalet meddelanden i kö i Kötjänsten för storage-konto.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|FileCapacity|Filen kapacitet|Byte|Medel|Mängden lagringsutrymme som används av storage-konto File service i byte.|Inga dimensioner|
|FileCount|Antal filer|Antal|Medel|Antal filer i tjänsten för File storage-konto.|Inga dimensioner|
|FileShareCount|Filresurs|Antal|Medel|Antal delar i tjänsten för File storage-konto.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure.|GeoType ApiName|
|Utgång|Utgång|Byte|Totalt|Mängden utgående data i byte. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång.|GeoType ApiName|
|SuccessServerLatency|Lyckade Server svarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckade E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|ResourceUtilization|SU % utnyttjande|Procent|Maximal|SU % utnyttjande|Inga dimensioner|
|InputEvents|Indatahändelser|Antal|Totalt|Indatahändelser|Inga dimensioner|
|InputEventBytes|Indatahändelsebyte|Byte|Totalt|Indatahändelsebyte|Inga dimensioner|
|LateInputEvents|Sena indatahändelser|Antal|Totalt|Sena indatahändelser|Inga dimensioner|
|OutputEvents|Utdatahändelser|Antal|Totalt|Utdatahändelser|Inga dimensioner|
|ConversionErrors|Datakonverteringsfel|Antal|Totalt|Datakonverteringsfel|Inga dimensioner|
|Fel|Körningsfel|Antal|Totalt|Körningsfel|Inga dimensioner|
|DroppedOrAdjustedEvents|Oordnade händelser|Antal|Totalt|Oordnade händelser|Inga dimensioner|
|AMLCalloutRequests|Funktionsförfrågningar|Antal|Totalt|Funktionsförfrågningar|Inga dimensioner|
|AMLCalloutFailedRequests|Misslyckade funktionsförfrågningar|Antal|Totalt|Misslyckade funktionsförfrågningar|Inga dimensioner|
|AMLCalloutInputEvents|Funktionshändelser|Antal|Totalt|Funktionshändelser|Inga dimensioner|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Procent|Medel|Minnesprocent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Totalt|Diskkölängd|Instans|
|HttpQueueLength|Http-kölängd|Antal|Totalt|Http-kölängd|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (exklusive funktioner)

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|HTTP 2xx|Antal|Totalt|HTTP 2xx|Instans|
|Http3xx|HTTP 3xx|Antal|Totalt|HTTP 3xx|Instans|
|Http401|HTTP 401|Antal|Totalt|HTTP 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http 4xx|Antal|Totalt|Http 4xx|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Medel|Genomsnittligt arbetsminne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Medel|Genomsnittlig svarstid|Instans|
|FunctionExecutionUnits|Funktionskörningsenheter|Antal|Medel|Funktionskörningsenheter|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Antal|Medel|Funktionen Antal körningar|Instans|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funktioner)

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Medel|Genomsnittligt arbetsminne|Instans|
|FunctionExecutionUnits|Funktionskörningsenheter|Antal|Medel|Funktionskörningsenheter|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Antal|Medel|Funktionen Antal körningar|Instans|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|HTTP 2xx|Antal|Totalt|HTTP 2xx|Instans|
|Http3xx|HTTP 3xx|Antal|Totalt|HTTP 3xx|Instans|
|Http401|HTTP 401|Antal|Totalt|HTTP 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http 4xx|Antal|Totalt|Http 4xx|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Medel|Genomsnittligt arbetsminne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Medel|Genomsnittlig svarstid|Instans|
|FunctionExecutionUnits|Funktionskörningsenheter|Antal|Medel|Funktionskörningsenheter|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Antal|Medel|Funktionen Antal körningar|Instans|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|HTTP 2xx|Antal|Totalt|HTTP 2xx|Instans|
|Http3xx|HTTP 3xx|Antal|Totalt|HTTP 3xx|Instans|
|Http401|HTTP 401|Antal|Totalt|HTTP 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http 4xx|Antal|Totalt|Http 4xx|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Medel|Genomsnittlig svarstid|Instans|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Procent|Medel|Minnesprocent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Totalt|Diskkölängd|Instans|
|HttpQueueLength|Http-kölängd|Antal|Totalt|Http-kölängd|Instans|
|ActiveRequests|Aktiva förfrågningar|Antal|Totalt|Aktiva förfrågningar|Instans|
|TotalFrontEnds|Totalt antal klienter|Antal|Medel|Totalt antal klienter|Instans|
|SmallAppServicePlanInstances|App Service-plan för arbeten (låg användning)|Antal|Medel|App Service-plan för arbeten (låg användning)|Instans|
|MediumAppServicePlanInstances|App Service-plan för arbeten (medelhög användning)|Antal|Medel|App Service-plan för arbeten (medelhög användning)|Instans|
|LargeAppServicePlanInstances|App Service-plan för arbeten (hög användning)|Antal|Medel|App Service-plan för arbeten (hög användning)|Instans|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Mått|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbeten|Antal|Medel|Totalt antal arbeten|Instans|
|WorkersAvailable|Tillgängliga arbeten|Antal|Medel|Tillgängliga arbeten|Instans|
|WorkersUsed|Använda arbeten|Antal|Medel|Använda arbeten|Instans|

## <a name="next-steps"></a>Nästa steg
* [Läs mer om mätvärden i Azure-Monitor](monitoring-overview-metrics.md)
* [Skapa aviseringar på mått](insights-receive-alert-notifications.md)
* [Exportera mått till lagring, Event Hub eller logganalys](monitoring-overview-of-diagnostic-logs.md)
