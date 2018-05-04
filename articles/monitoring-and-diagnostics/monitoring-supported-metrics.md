---
title: Azure övervakaren mått - stöds mått per resurstypen | Microsoft Docs
description: Lista över tillgängliga för varje resurstyp av med Azure-Monitor mått.
author: anirudhcavale
manager: ashwink
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ancav
ms.openlocfilehash: 34d115f36e64a34b4382ce4e11e93a0c06215c5d
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="supported-metrics-with-azure-monitor"></a>Stöds mått med Azure-Monitor
Azure-Monitor finns flera sätt att interagera med statistik, inklusive diagram dem i portalen, komma åt dem via REST API eller fråga dem med PowerShell eller CLI. Nedan finns en fullständig lista över alla just nu med Azure-Monitor mått pipeline. Andra mått kan finnas i portalen eller med äldre API: er. Listan nedan innehåller endast tillgängliga med konsoliderade Azure-Monitor mått pipeline mått. Fråga efter och komma åt de här måtten ska du använda den [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|qpu_metric|QPU|Antal|Medel|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|memory_metric|Minne|Byte|Medel|Minne. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|TotalConnectionRequests|Totalt antal begäranden|Antal|Medel|Totalt antal förfrågningar. Dessa är mottagna.|ServerResourceType|
|SuccessfullConnectionsPerSec|Lyckade anslutningar Per sekund|CountPerSecond|Medel|Hastighet för lyckade anslutningen slutföras.|ServerResourceType|
|TotalConnectionFailures|Totalt antal anslutningsfel|Antal|Medel|Totalt antal misslyckade anslutningsförsök.|ServerResourceType|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medel|Aktuellt antal användarsessioner som har upprättats.|ServerResourceType|
|QueryPoolBusyThreads|Frågan poolen upptagen trådar|Antal|Medel|Antal upptagen trådar i trådpoolen för frågan.|ServerResourceType|
|CommandPoolJobQueueLength|Kölängd för kommandot poolen jobb|Antal|Medel|Antal jobb i kö i trådpoolen för kommandot.|ServerResourceType|
|ProcessingPoolJobQueueLength|Kölängd för bearbetning poolen jobb|Antal|Medel|Antal icke-I/O-jobb i kö för bearbetningstrådpoolen.|ServerResourceType|
|CurrentConnections|Anslutning: Aktuella anslutningar|Antal|Medel|Aktuellt antal klientanslutningar.|ServerResourceType|
|CleanerCurrentPrice|Minne: Tydligare aktuella pris|Antal|Medel|Aktuella priset minne, $ byte/tidsvärdet, normaliserad till 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Minne: Tydligare minne krympning|Byte|Medel|Mängden minne i byte, föremål rensning av bakgrunden ett rengöringsband.|ServerResourceType|
|CleanerMemoryNonshrinkable|Minne: Tydligare minne nonshrinkable|Byte|Medel|Mängden minne i byte som inte omfattas av rensning av bakgrunden ett rengöringsband.|ServerResourceType|
|MemoryUsage|Minne: Minnesanvändning|Byte|Medel|Mängden minne på server-processen som används för att beräkna tydligare minnespris. Lika med prestandaräknaren Process\PrivateBytes plus storleken på minnesmappade data, ignoreras eventuella som mappats eller som har allokerats av xVelocity i minnet analytics motorn (VertiPaq) överskrider minnesgränsen för xVelocity-motorn.|ServerResourceType|
|MemoryLimitHard|Minne: Minnesgränsen hårddisken|Byte|Medel|Hårda minnesgränsen, från konfigurationsfilen.|ServerResourceType|
|MemoryLimitHigh|Minne: Minne begränsa hög|Byte|Medel|Hög minnesgränsen, från konfigurationsfilen.|ServerResourceType|
|MemoryLimitLow|Minne: Minne gränsen låg|Byte|Medel|Minnesbrist gräns från konfigurationsfilen.|ServerResourceType|
|MemoryLimitVertiPaq|Minne: Minne gränsen VertiPaq|Byte|Medel|I minnet gräns från konfigurationsfilen.|ServerResourceType|
|Kvot|Minne: kvot|Byte|Medel|Aktuella minneskvoten, i byte. Minneskvoten kallas även en reservation för minne grant eller minne.|ServerResourceType|
|QuotaBlocked|: Minneskvoten blockeras|Antal|Medel|Aktuellt antal begäranden om kvoten som blockeras tills andra minneskvoter frigörs.|ServerResourceType|
|VertiPaqNonpaged|Minne: VertiPaq växlingsbart systemminne|Byte|Medel|Byte av minne låst i arbetsminnet för användning av motorn i minnet.|ServerResourceType|
|VertiPaqPaged|Minne: VertiPaq-växlat minne|Byte|Medel|Antal byte växlingsbart minne som används för data i minnet.|ServerResourceType|
|RowsReadPerSec|Bearbetning: Läsa rader per sek|CountPerSecond|Medel|Antal rader att läsa från alla relationsdatabaser.|ServerResourceType|
|RowsConvertedPerSec|Bearbetning: Rader konverteras per sek|CountPerSecond|Medel|Antal rader konverteras under bearbetning.|ServerResourceType|
|RowsWrittenPerSec|Bearbetning: Rader som skrivs per sekund|CountPerSecond|Medel|Antal rader skrivna under bearbetning.|ServerResourceType|
|CommandPoolBusyThreads|Trådar: Kommandot poolen upptagen trådar|Antal|Medel|Antal upptagen trådar i trådpoolen för kommandot.|ServerResourceType|
|CommandPoolIdleThreads|Trådar: Kommandot poolen inaktiva trådar|Antal|Medel|Antalet inaktiva trådar i trådpoolen för kommandot.|ServerResourceType|
|LongParsingBusyThreads|Trådar: Long parsning upptagen trådar|Antal|Medel|Antal upptagen trådar i länge tolkning trådpoolen.|ServerResourceType|
|LongParsingIdleThreads|Trådar: Long parsning inaktiva trådar|Antal|Medel|Antalet inaktiva trådar i länge tolkning trådpoolen.|ServerResourceType|
|LongParsingJobQueueLength|Trådar: Parsning länge jobbet Kölängd|Antal|Medel|Antal jobb i kö för länge tolkning trådpoolen.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Trådar: Bearbetning av poolen upptagen i/o-jobbet trådar|Antal|Medel|Antal trådar som körs i/o-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Trådar: Bearbetning av poolen upptagen icke-I/O-trådar|Antal|Medel|Antal trådar som icke-I/O-jobb som körs i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Trådar: Bearbetning av poolen Kölängd för i/o-jobb|Antal|Medel|Antal i/o-jobb i kö för bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Trådar: Bearbetning av poolen inaktiv i/o-jobbet trådar|Antal|Medel|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Trådar: Bearbetning av poolen inaktiv icke-I/O-trådar|Antal|Medel|Antalet inaktiva trådar i bearbetningstrådpoolen som är dedikerad till icke-I/O-jobb.|ServerResourceType|
|QueryPoolIdleThreads|Trådar: Frågan poolen inaktiva trådar|Antal|Medel|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|ServerResourceType|
|QueryPoolJobQueueLength|Trådar: Frågan poolen jobbet kön lengt|Antal|Medel|Antal jobb i kö i trådpoolen för frågan.|ServerResourceType|
|ShortParsingBusyThreads|Trådar: Kort parsning upptagen trådar|Antal|Medel|Antal upptagen trådar i kort tolkning trådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Trådar: Kort parsning inaktiva trådar|Antal|Medel|Antalet inaktiva trådar i kort tolkning trådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Trådar: Kort parsning jobbet Kölängd|Antal|Medel|Antal jobb i kö för kort tolkning trådpoolen.|ServerResourceType|
|memory_thrashing_metric|Minne knattrar|Procent|Medel|Genomsnittlig minne knattrar.|ServerResourceType|
|mashup_engine_qpu_metric|M-motorn QPU|Antal|Medel|QPU användning av mashup-motorn processer|ServerResourceType|
|mashup_engine_memory_metric|M-motorn minne|Byte|Medel|Minnesanvändning för mashup-motorn processer|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalRequests|Totalt antal Gateway-begäranden|Antal|Totalt|Antal begäranden för gateway|Plats, värdnamn|
|SuccessfulRequests|Rätt Gateway-begäranden|Antal|Totalt|Antalet begäranden som lyckats gateway|Plats, värdnamn|
|UnauthorizedRequests|Obehörig Gateway-begäranden|Antal|Totalt|Antal begäranden för obehörig gateway|Plats, värdnamn|
|FailedRequests|Gateway för misslyckade begäranden|Antal|Totalt|Antalet fel i gateway-begäranden|Plats, värdnamn|
|OtherRequests|Andra Gateway-begäranden|Antal|Totalt|Antal andra gateway-begäranden|Plats, värdnamn|
|Varaktighet|Varaktighet för Gateway-förfrågningar|Millisekunder|Medel|Övergripande varaktighet för Gateway-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Kapacitet (förhandsgranskning)|Procent|Medel|Användning mått för ApiManagement service|Plats|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Runbook, Status|

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
|connectedclients|Anslutna klienter|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed|Totalt antal åtgärder|Antal|Totalt||Inga dimensioner|
|cachehits|Cacheträffar|Antal|Totalt||Inga dimensioner|
|cachemisses|Cachemissar|Antal|Totalt||Inga dimensioner|
|getcommands|Hämtar|Antal|Totalt||Inga dimensioner|
|setcommands|Uppsättningar|Antal|Totalt||Inga dimensioner|
|operationsPerSecond|Åtgärder Per sekund|Antal|Totalt||Inga dimensioner|
|evictedkeys|Avlägsnade nycklar|Antal|Totalt||Inga dimensioner|
|totalkeys|Totalt antal nycklar|Antal|Maximal||Inga dimensioner|
|expiredkeys|Utgångna nycklar|Antal|Totalt||Inga dimensioner|
|usedmemory|Använt minne|Byte|Maximal||Inga dimensioner|
|usedmemoryRss|Använt minne RSS|Byte|Maximal||Inga dimensioner|
|serverLoad|Serverbelastning|Procent|Maximal||Inga dimensioner|
|cacheWrite|Cacheskrivning|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead|Cacheläsning|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime|Processor|Procent|Maximal||Inga dimensioner|
|connectedclients0|Anslutna klienter (Fragmentera 0)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed0|Totalt antal åtgärder (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|cachehits0|Cacheträffar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|cachemisses0|Cachemissar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|getcommands0|Hämtar (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|setcommands0|Anger (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond0|Åtgärder Per sekund (Fragmentera 0)|Antal|Totalt||Inga dimensioner|
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
|operationsPerSecond1|Åtgärder Per sekund (Fragmentera 1)|Antal|Totalt||Inga dimensioner|
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
|operationsPerSecond2|Åtgärder Per sekund (Fragmentera 2)|Antal|Totalt||Inga dimensioner|
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
|operationsPerSecond3|Åtgärder Per sekund (Fragmentera 3)|Antal|Totalt||Inga dimensioner|
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
|operationsPerSecond4|Åtgärder Per sekund (Fragmentera 4)|Antal|Totalt||Inga dimensioner|
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
|operationsPerSecond5|Åtgärder Per sekund (Fragmentera 5)|Antal|Totalt||Inga dimensioner|
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
|operationsPerSecond6|Åtgärder Per sekund (Fragmentera 6)|Antal|Totalt||Inga dimensioner|
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
|operationsPerSecond7|Åtgärder Per sekund (Fragmentera 7)|Antal|Totalt||Inga dimensioner|
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
|operationsPerSecond8|Åtgärder Per sekund (Fragmentera 8)|Antal|Totalt||Inga dimensioner|
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
|operationsPerSecond9|Åtgärder Per sekund (Fragmentera 9)|Antal|Totalt||Inga dimensioner|
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

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer.|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik).|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik).|Inga dimensioner|
|Lästa byte på disk/sek|Diskläsning|BytesPerSecond|Medel|Genomsnittligt antal byte lästa från disk under övervakningsperioden.|Inga dimensioner|
|Skrivna byte på disk/sek|Diskskrivning|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivits till disk under övervakningsperioden.|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk.|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk.|Inga dimensioner|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer.|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik).|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik).|Inga dimensioner|
|Lästa byte på disk/sek|Diskläsning|BytesPerSecond|Medel|Genomsnittligt antal byte lästa från disk under övervakningsperioden.|Inga dimensioner|
|Skrivna byte på disk/sek|Diskskrivning|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivits till disk under övervakningsperioden.|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk.|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk.|Inga dimensioner|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
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
|CharactersTranslated|Översatta tecken|Antal|Totalt|Totalt antal tecken i inkommande textbegäran.|Inga dimensioner|
|SpeechSessionDuration|Varaktighet för talsession|Sekunder|Totalt|Total varaktighet för talsession i sekunder.|Inga dimensioner|
|TotalTransactions|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner|Inga dimensioner|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt antal byte som lästs från disk under övervakningsperioden|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Totalt antal byte som skrivs från disk under övervakningsperioden|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk|Inga dimensioner|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter tillgängliga för plötsliga toppar|Inga dimensioner|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medel|Totalt antal krediter som använts av den virtuella datorn|Inga dimensioner|
|Per Disk-lästa byte/sek|Datadisk-lästa byte/sek (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund läsa från en enda disk under perioden|Fack-ID|
|Per Disk-skrivna byte/s|Data Disk-skrivna byte/s (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund som skrivs till en enskild disk under perioden|Fack-ID|
|Per Disk läsåtgärder per sekund|Datadisk läsåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när läsning från en enda disk under perioden|Fack-ID|
|Per Disk skrivåtgärder per sekund|Data Disk skrivåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när skrivning till en enskild disk under perioden|Fack-ID|
|Per Disk QD|Datadisk QD (förhandsgranskning)|Antal|Medel|Data Disk ködjup (eller Kölängd)|Fack-ID|
|OS Per Disk-lästa byte/s|OS-Disk-lästa byte/sek (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund läsa från en enda disk under övervakning för OS-disk|Inga dimensioner|
|OS Per Disk-skrivna byte/s|OS-Disk-skrivna byte/s (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund som skrivs till en enskild disk under perioden för OS-disk|Inga dimensioner|
|OS Per Disk läsåtgärder per sekund|OS-disken läsåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när läsning från en enda disk under perioden för OS-disk|Inga dimensioner|
|OS Per Disk skrivåtgärder per sekund|OS-disken skrivåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när skrivning till en enskild disk under perioden för OS-disk|Inga dimensioner|
|OS Per Disk QD|OS-disken QD (förhandsgranskning)|Antal|Medel|OS-disken ködjup (eller Kölängd)|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt antal byte som lästs från disk under övervakningsperioden|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Totalt antal byte som skrivs från disk under övervakningsperioden|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk|Inga dimensioner|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter tillgängliga för plötsliga toppar|Inga dimensioner|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medel|Totalt antal krediter som använts av den virtuella datorn|Inga dimensioner|
|Per Disk-lästa byte/sek|Datadisk-lästa byte/sek (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund läsa från en enda disk under perioden|Fack-ID|
|Per Disk-skrivna byte/s|Data Disk-skrivna byte/s (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund som skrivs till en enskild disk under perioden|Fack-ID|
|Per Disk läsåtgärder per sekund|Datadisk läsåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när läsning från en enda disk under perioden|Fack-ID|
|Per Disk skrivåtgärder per sekund|Data Disk skrivåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när skrivning till en enskild disk under perioden|Fack-ID|
|Per Disk QD|Datadisk QD (förhandsgranskning)|Antal|Medel|Data Disk ködjup (eller Kölängd)|Fack-ID|
|OS Per Disk-lästa byte/s|OS-Disk-lästa byte/sek|CountPerSecond|Medel|Totalt antal byte per sekund läsa från en enda disk under övervakning för OS-disk|Inga dimensioner|
|OS Per Disk-skrivna byte/s|OS-Disk-skrivna byte/s (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund som skrivs till en enskild disk under perioden för OS-disk|Inga dimensioner|
|OS Per Disk läsåtgärder per sekund|OS-disken läsåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när läsning från en enda disk under perioden för OS-disk|Inga dimensioner|
|OS Per Disk skrivåtgärder per sekund|OS-disken skrivåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när skrivning till en enskild disk under perioden för OS-disk|Inga dimensioner|
|OS Per Disk QD|OS-disken QD (förhandsgranskning)|Antal|Medel|OS-disken ködjup (eller Kölängd)|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt antal byte som lästs från disk under övervakningsperioden|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Totalt antal byte som skrivs från disk under övervakningsperioden|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk|Inga dimensioner|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter tillgängliga för plötsliga toppar|Inga dimensioner|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medel|Totalt antal krediter som använts av den virtuella datorn|Inga dimensioner|
|Per Disk-lästa byte/sek|Datadisk-lästa byte/sek (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund läsa från en enda disk under perioden|Fack-ID|
|Per Disk-skrivna byte/s|Data Disk-skrivna byte/s (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund som skrivs till en enskild disk under perioden|Fack-ID|
|Per Disk läsåtgärder per sekund|Datadisk läsåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när läsning från en enda disk under perioden|Fack-ID|
|Per Disk skrivåtgärder per sekund|Data Disk skrivåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när skrivning till en enskild disk under perioden|Fack-ID|
|Per Disk QD|Datadisk QD (förhandsgranskning)|Antal|Medel|Data Disk ködjup (eller Kölängd)|Fack-ID|
|OS Per Disk-lästa byte/s|OS-Disk-lästa byte/sek (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund läsa från en enda disk under övervakning för OS-disk|Inga dimensioner|
|OS Per Disk-skrivna byte/s|OS-Disk-skrivna byte/s (förhandsgranskning)|CountPerSecond|Medel|Totalt antal byte per sekund som skrivs till en enskild disk under perioden för OS-disk|Inga dimensioner|
|OS Per Disk läsåtgärder per sekund|OS-disken läsåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när läsning från en enda disk under perioden för OS-disk|Inga dimensioner|
|OS Per Disk skrivåtgärder per sekund|OS-disken skrivåtgärder per sekund (förhandsgranskning)|CountPerSecond|Medel|IOPS totalt utföras när skrivning till en enskild disk under perioden för OS-disk|Inga dimensioner|
|OS Per Disk QD|OS-disken QD (förhandsgranskning)|Antal|Medel|OS-disken ködjup (eller Kölängd)|Inga dimensioner|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|CPU-användning|Antal|Medel|CPU-användning på alla kärnor i millicores.|ContainerName|
|MemoryUsage|Minnesanvändning|Byte|Medel|Användning av totalt minne i byte.|ContainerName|

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
|DCIProfilesCount|Antal instanser för profil|Antal|Sista||Inga dimensioner|
|DCIInteractionsPerMonthCount|Interaktioner antal per månad|Antal|Sista||Inga dimensioner|
|DCIKpisCount|KPI-antal|Antal|Sista||Inga dimensioner|
|DCISegmentsCount|Antal segment|Antal|Sista||Inga dimensioner|
|DCIPredictiveMatchPoliciesCount|Förutsägbara Matchningsantal|Antal|Sista||Inga dimensioner|
|DCIPredictionsCount|Förutsägelse antal|Antal|Sista||Inga dimensioner|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FailedRuns|Misslyckade körningar|Antal|Totalt||pipelineName, activityName, windowEnd, windowStart|
|SuccessfulRuns|Lyckade körningar|Antal|Totalt||pipelineName, activityName, windowEnd, windowStart|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PipelineFailedRuns|Det gick inte pipelinen körs mått|Antal|Totalt||FailureType namn|
|PipelineSucceededRuns|Pipelinen körs mått är klar|Antal|Totalt||FailureType namn|
|ActivityFailedRuns|Det gick inte aktiviteten körs mått|Antal|Totalt||ActivityType PipelineName FailureType, namn|
|ActivitySucceededRuns|Lyckades aktiviteten körs mått|Antal|Totalt||ActivityType PipelineName FailureType, namn|
|TriggerFailedRuns|Det gick inte utlösaren körs mått|Antal|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Lyckades utlösaren körs mått|Antal|Totalt||Namn, FailureType|
|IntegrationRuntimeCpuPercentage|Integration runtime CPU-användning|Procent|Medel||IntegrationRuntimeName nodnamn|
|IntegrationRuntimeAvailableMemory|Integration runtime-minne|Byte|Medel||IntegrationRuntimeName nodnamn|

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
|TotalStorage|Totalt lagringsutrymme|Byte|Maximal|Total mängd data som lagras på kontot.|Inga dimensioner|
|DataWritten|Skrivna data|Byte|Totalt|Total mängd data som skrivs till kontot.|Inga dimensioner|
|DataRead|Lästa data|Byte|Totalt|Total mängd data har lästs från kontot.|Inga dimensioner|
|WriteRequests|Skrivåtgärder|Antal|Totalt|Antal data skrivåtgärder till kontot.|Inga dimensioner|
|ReadRequests|Läs begäranden|Antal|Totalt|Antal data läsbegäranden till kontot.|Inga dimensioner|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|memory_percent|Minne|Procent|Medel|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Procent|Medel|IO-procent|Inga dimensioner|
|storage_percent|Storage-procent|Procent|Medel|Storage-procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagringsgränsen|Byte|Medel|Lagringsgränsen|Inga dimensioner|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Procent|Medel|Procent för lagring av Server-loggen|Inga dimensioner|
|serverlog_storage_usage|Log-serverlagring används|Byte|Medel|Log-serverlagring används|Inga dimensioner|
|serverlog_storage_limit|Lagringsgränsen för Server-loggen|Byte|Medel|Lagringsgränsen för Server-loggen|Inga dimensioner|
|active_connections|Totalt antal aktiva anslutningar|Antal|Medel|Totalt antal aktiva anslutningar|Inga dimensioner|
|connections_failed|Totalt antal misslyckade anslutningar|Antal|Totalt|Totalt antal misslyckade anslutningar|Inga dimensioner|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|memory_percent|Minne|Procent|Medel|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Procent|Medel|IO-procent|Inga dimensioner|
|storage_percent|Storage-procent|Procent|Medel|Storage-procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagringsgränsen|Byte|Medel|Lagringsgränsen|Inga dimensioner|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Procent|Medel|Procent för lagring av Server-loggen|Inga dimensioner|
|serverlog_storage_usage|Log-serverlagring används|Byte|Medel|Log-serverlagring används|Inga dimensioner|
|serverlog_storage_limit|Lagringsgränsen för Server-loggen|Byte|Medel|Lagringsgränsen för Server-loggen|Inga dimensioner|
|active_connections|Totalt antal aktiva anslutningar|Antal|Totalt|Totalt antal aktiva anslutningar|Inga dimensioner|
|connections_failed|Totalt antal misslyckade anslutningar|Antal|Totalt|Totalt antal misslyckade anslutningar|Inga dimensioner|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetri meddelande Skicka försök|Antal|Totalt|Antal meddelanden enhet till moln telemetri försökte skickas till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.Ingress.Success|Telemetri meddelanden som skickas|Antal|Totalt|Antal enhet till moln telemetri meddelanden som skickats till din IoT-hubb|Inga dimensioner|
|c2d.commands.egress.Complete.Success|Kommandon som slutförd|Antal|Totalt|Antal moln till enhet kommandon har slutförts av enheten|Inga dimensioner|
|c2d.commands.egress.abandon.success|Avbrutna kommandon|Antal|Totalt|Antal moln till enhet kommandon avbrutna av enheten|Inga dimensioner|
|c2d.commands.egress.Reject.Success|Kommandon som avvisat|Antal|Totalt|Antal moln till enhet kommandon som avvisats av enheten|Inga dimensioner|
|devices.totalDevices|Totalt antal enheter|Antal|Totalt|Antalet enheter som registrerats för din IoT-hubb|Inga dimensioner|
|devices.connectedDevices.allProtocol|Anslutna enheter|Antal|Totalt|Antalet enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.egress.Success|Telemetri meddelanden|Antal|Totalt|Antal gånger som meddelanden som skrivits till slutpunkter (totalt)|Inga dimensioner|
|d2c.telemetry.egress.dropped|Ignorerade meddelanden|Antal|Totalt|Antal meddelanden som utelämnats eftersom leverans slutpunkten har döda|Inga dimensioner|
|d2c.telemetry.egress.orphaned|Överblivna meddelanden|Antal|Totalt|Antal meddelanden som inte matchar några vägar inklusive återställningsplats flöde|Inga dimensioner|
|d2c.telemetry.egress.invalid|Ogiltig meddelanden|Antal|Totalt|Antalet meddelanden som inte levereras på grund av inkompatibilitet med slutpunkten|Inga dimensioner|
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
|d2c.telemetry.ingress.sendThrottle|Antalet fel som bandbreddsbegränsning|Antal|Totalt|Begränsar antalet bandbreddsbegränsning felen på grund av enheten genomflöde|Inga dimensioner|
|dailyMessageQuotaUsed|Sammanlagt antal meddelanden som används|Antal|Medel|Antal Totalt antal meddelanden som används idag. Detta är en kumulativ värde som har återställts till noll i UTC 00:00 varje dag.|Inga dimensioner|
|deviceDataUsage|Totalt antal devicedata användning|Antal|Totalt|Byte som överförs till och från alla enheter som är anslutna till IotHub|Inga dimensioner|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Registrering försök|Antal|Totalt|Antal enheter registreringar försökte|ProvisioningServiceName IotHubName, Status|
|DeviceAssignments|Enheter som är tilldelade|Antal|Totalt|Antalet enheter som har tilldelats en IoT-hubb|ProvisioningServiceName IotHubName|
|AttestationAttempts|Attesteringen försök|Antal|Totalt|Antalet enheter intyg försökte|ProvisioningServiceName, Status, protokollet|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MetadataRequests|Metadatabegäranden|Antal|Antal|Antal metadatabegäranden om. Cosmos DB underhåller system metadatasamlingen för varje konto där du kan räkna upp samlingar, databaser osv och konfigurationer, utan kostnad.|GlobalDatabaseAccountName, DatabaseName, samlingsnamn, Region, StatusCode|
|MongoRequestCharge|Mongo begäran kostnad|Antal|Totalt|Mongo Frågeenheter används|GlobalDatabaseAccountName, DatabaseName, samlingsnamn, Region, CommandName, felkod|
|MongoRequests|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden|GlobalDatabaseAccountName, DatabaseName, samlingsnamn, Region, CommandName, felkod|
|TotalRequestUnits|Totalt antal Frågeenheter|Antal|Totalt|Enheter som används för begäran|GlobalDatabaseAccountName, DatabaseName, samlingsnamn, Region, StatusCode|
|TotalRequests|Totalt antal begäranden|Antal|Antal|Antal begäranden|GlobalDatabaseAccountName, DatabaseName, samlingsnamn, Region, StatusCode|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade begäranden (förhandsgranskning)|Antal|Totalt|Lyckade begäranden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|ServerErrors|Serverfel. (Förhandsversion)|Antal|Totalt|Serverfel för Microsoft.EventHub. (Förhandsversion)|EntityName|
|UserErrors|Användarfel. (Förhandsversion)|Antal|Totalt|Användarfel för Microsoft.EventHub. (Förhandsversion)|EntityName|
|QuotaExceededErrors|Kvoten överskridits-fel. (Förhandsversion)|Antal|Totalt|Kvoten överskridits-fel för Microsoft.EventHub. (Förhandsversion)|EntityName|
|ThrottledRequests|Begränsade begäranden. (Förhandsversion)|Antal|Totalt|Begränsade begäranden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|IncomingRequests|Inkommande begäranden (förhandsgranskning)|Antal|Totalt|Inkommande begäranden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|IncomingMessages|Inkommande meddelanden (förhandsgranskning)|Antal|Totalt|Inkommande meddelanden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|OutgoingMessages|Utgående meddelanden (förhandsgranskning)|Antal|Totalt|Utgående meddelanden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|IncomingBytes|Inkommande byte. (Förhandsversion)|Byte|Totalt|Inkommande byte för Microsoft.EventHub. (Förhandsversion)|EntityName|
|OutgoingBytes|Utgående byte. (Förhandsversion)|Byte|Totalt|Utgående byte för Microsoft.EventHub. (Förhandsversion)|EntityName|
|ActiveConnections|ActiveConnections (förhandsgranskning)|Antal|Medel|Totalt antal aktiva anslutningar för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ConnectionsOpened|Öppnade anslutningar. (Förhandsversion)|Antal|Medel|Öppnade Microsoft.EventHub-anslutningar. (Förhandsversion)|EntityName|
|ConnectionsClosed|Stängda anslutningar. (Förhandsversion)|Antal|Medel|Stängda Microsoft.EventHub-anslutningar. (Förhandsversion)|EntityName|
|CaptureBacklog|Hämta kvarvarande uppgifter. (Förhandsversion)|Antal|Totalt|Hämta kvarvarande uppgifter för Microsoft.EventHub. (Förhandsversion)|EntityName|
|CapturedMessages|Hämtade meddelanden. (Förhandsversion)|Antal|Totalt|Hämtade meddelanden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|CapturedBytes|Hämtade byte. (Förhandsversion)|Byte|Totalt|Hämtade byte för Microsoft.EventHub. (Förhandsversion)|EntityName|
|Storlek|Storlek (förhandsgranskning)|Byte|Medel|Storleken på en EventHub i byte. (Förhandsversion)|EntityName|
|INREQS|Inkommande förfrågningar|Antal|Totalt|Totat antal inkommande sändningsförfrågningar för ett namnområde|Inga dimensioner|
|SUCCREQ|Slutförda förfrågningar|Antal|Totalt|Totalt antal slutförda begäranden för ett namnområde|Inga dimensioner|
|FAILREQ|Misslyckade förfrågningar|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde|Inga dimensioner|
|SVRBSY|Upptagen server|Antal|Totalt|Totalt antal fel med upptagen server för ett namnområde|Inga dimensioner|
|INTERR|Interna serverfel|Antal|Totalt|Totalt antal interna serverfel för ett namnområde|Inga dimensioner|
|MISCERR|Övriga fel|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde|Inga dimensioner|
|INMSGS|Inkommande meddelanden (inaktuellt)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde. Det här måttet är föråldrad. Använd mått för inkommande meddelanden i stället|Inga dimensioner|
|EHINMSGS|Inkommande meddelanden|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde|Inga dimensioner|
|OUTMSGS|Utgående meddelanden (inaktuellt)|Antal|Totalt|Total utgående meddelanden för ett namnområde. Det här måttet är föråldrad. Använd i stället utgående meddelanden mått|Inga dimensioner|
|EHOUTMSGS|Utgående meddelanden|Antal|Totalt|Totalt antal utgående meddelanden för ett namnområde|Inga dimensioner|
|EHINMBS|Inkommande byte (inaktuellt)|Byte|Totalt|Event Hub inkommande genomströmningen för ett namnområde. Det här måttet är föråldrad. Använd i stället inkommande byte mått|Inga dimensioner|
|EHINBYTES|Inkommande byte|Byte|Totalt|Dataflöde för inkommande meddelanden för händelsehubb för en namnområde|Inga dimensioner|
|EHOUTMBS|Utgående byte (inaktuellt)|Byte|Totalt|Hubb utgående meddelande händelsegenomflöde för ett namnområde. Det här måttet är föråldrad. Använd i stället utgående byte mått|Inga dimensioner|
|EHOUTBYTES|Utgående byte|Byte|Totalt|Dataflöde för utgående meddelanden för händelsehubb för ett namnområde|Inga dimensioner|
|EHABL|Arkivera kvarvarande meddelanden|Antal|Totalt|Arkiverade meddelanden för händelsehubb i kvarvarande uppgifter för ett namnområde|Inga dimensioner|
|EHAMSGS|Arkivera meddelanden|Antal|Totalt|Arkiverade meddelanden för händelsehubb i ett namnområde|Inga dimensioner|
|EHAMBS|Arkivera meddelandedataflöde|Byte|Totalt|Dataflöde för arkiverade meddelanden för händelsehubb i ett namnområde|Inga dimensioner|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway-begäranden|Antal|Totalt|Antal begäranden för gateway|ClusterDnsName HttpStatus|
|CategorizedGatewayRequests|Kategoriserade Gateway-begäranden|Antal|Totalt|Antal begäranden för gateway efter kategorier (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName HttpStatus|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerat mätvärde|Antal|Medel|Värdet som beräknas av autoskalning vid körning|MetricTriggerSource|
|MetricThreshold|Tröskelvärde för mått|Antal|Medel|Det inställda tröskelvärdet när autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Observerad kapacitet|Antal|Medel|Den kapacitet som rapporteras till autoskalning vid körning.|Inga dimensioner|
|ScaleActionsInitiated|Skalningsåtgärder har initierats|Antal|Totalt|Riktningen på skalningsåtgärden.|ScaleDirection|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Totalt antal Service API-träffar|Antal|Antal|Totalt antal Service API-träffar|ActivityType ActivityName|
|ServiceApiLatency|Total svarstid för Service API|Millisekunder|Medel|Total svarstid för Service API-begäranden|ActivityType ActivityName, StatusCode|
|ServiceApiResult|Totalt antal Service API-resultat|Antal|Antal|Totalt antal Service API-resultat|ActivityType ActivityName, StatusCode|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Svarstid|Svarstid|Millisekunder|Medel|Varaktighet för API-anrop|OperationName OperationResult|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
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

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|VIP-tillgänglighet|Antal|Medel|Tillgängligheten för VIP-slutpunkter, baserat på avsökning|VipAddress VipPort|
|DipAvailability|DIP tillgänglighet|Antal|Medel|Tillgängligheten för DIP slutpunkter, baserat på avsökning|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som överförs inom tidsperiod|VipAddress VipPort, riktning|
|PacketCount|Antal|Antal|Totalt|Totalt antal paket som skickas inom tidsperiod|VipAddress VipPort, riktning|
|SYNCount|SYN antal|Antal|Totalt|Totalt antal SYN-paket som skickas inom tidsperiod|VipAddress VipPort, riktning|
|SnatConnectionCount|SNAT Anslutningsräknare|Antal|Totalt|Totalt antal nya SNAT anslutningar som skapats i tidsperiod|VipAddress DipAddress, ConnectionState|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Frågan volym|Antal|Totalt|Antal frågor som visas för en DNS-zon|Inga dimensioner|
|RecordSetCount|Antal för uppsättningen av poster|Antal|Maximal|Antal poster i en DNS-zon|Inga dimensioner|
|RecordSetCapacityUtilization|Postuppsättningen kapacitetsutnyttjande|Procent|Maximal|Procent av postuppsättning kapacitet som används av en DNS-zon|Inga dimensioner|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PacketsInDDoS|Inkommande paket DDoS|CountPerSecond|Maximal|Inkommande paket DDoS|Inga dimensioner|
|PacketsDroppedDDoS|Inkommande paket som tas bort DDoS|CountPerSecond|Maximal|Inkommande paket som tas bort DDoS|Inga dimensioner|
|PacketsForwardedDDoS|Inkommande paket som vidarebefordras DDoS|CountPerSecond|Maximal|Inkommande paket som vidarebefordras DDoS|Inga dimensioner|
|TCPPacketsInDDoS|Inkommande TCP-paket DDoS|CountPerSecond|Maximal|Inkommande TCP-paket DDoS|Inga dimensioner|
|TCPPacketsDroppedDDoS|Inkommande TCP-paket som tas bort DDoS|CountPerSecond|Maximal|Inkommande TCP-paket som tas bort DDoS|Inga dimensioner|
|TCPPacketsForwardedDDoS|Inkommande TCP-paket som vidarebefordras DDoS|CountPerSecond|Maximal|Inkommande TCP-paket som vidarebefordras DDoS|Inga dimensioner|
|UDPPacketsInDDoS|Inkommande UDP-paket DDoS|CountPerSecond|Maximal|Inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsDroppedDDoS|Inkommande UDP-paket som tas bort DDoS|CountPerSecond|Maximal|Inkommande UDP-paket som tas bort DDoS|Inga dimensioner|
|UDPPacketsForwardedDDoS|Inkommande UDP-paket som vidarebefordras DDoS|CountPerSecond|Maximal|Inkommande UDP-paket som vidarebefordras DDoS|Inga dimensioner|
|BytesInDDoS|Inkommande byte DDoS|BytesPerSecond|Maximal|Inkommande byte DDoS|Inga dimensioner|
|BytesDroppedDDoS|Inkommande byte bort DDoS|BytesPerSecond|Maximal|Inkommande byte bort DDoS|Inga dimensioner|
|BytesForwardedDDoS|Inkommande byte vidarebefordras DDoS|BytesPerSecond|Maximal|Inkommande byte vidarebefordras DDoS|Inga dimensioner|
|TCPBytesInDDoS|Inkommande TCP byte DDoS|BytesPerSecond|Maximal|Inkommande TCP byte DDoS|Inga dimensioner|
|TCPBytesDroppedDDoS|Inkommande TCP-byte bort DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte bort DDoS|Inga dimensioner|
|TCPBytesForwardedDDoS|Inkommande TCP-byte vidarebefordras DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte vidarebefordras DDoS|Inga dimensioner|
|UDPBytesInDDoS|Inkommande UDP byte DDoS|BytesPerSecond|Maximal|Inkommande UDP byte DDoS|Inga dimensioner|
|UDPBytesDroppedDDoS|Inkommande UDP byte bort DDoS|BytesPerSecond|Maximal|Inkommande UDP byte bort DDoS|Inga dimensioner|
|UDPBytesForwardedDDoS|Inkommande UDP byte vidarebefordras DDoS|BytesPerSecond|Maximal|Inkommande UDP byte vidarebefordras DDoS|Inga dimensioner|
|IfUnderDDoSAttack|Under DDoS attacker eller inte|Antal|Maximal|Under DDoS attacker eller inte|Inga dimensioner|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-lösning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-lösning|Inga dimensioner|
|DDoSTriggerUDPPackets|Inkommande UDP-paket för att utlösa DDoS-lösning|CountPerSecond|Maximal|Inkommande UDP-paket för att utlösa DDoS-lösning|Inga dimensioner|
|DDoSTriggerSYNPackets|Inkommande SYN paket för att utlösa DDoS-lösning|CountPerSecond|Maximal|Inkommande SYN paket för att utlösa DDoS-lösning|Inga dimensioner|
|VipAvailability|Tillgänglighet|Antal|Medel|Medeltillgänglighet IP-adress inom tidsperiod|Port|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som överförs inom tidsperiod|Port, riktning|
|PacketCount|Antal|Antal|Totalt|Totalt antal paket som skickas inom tidsperiod|Port, riktning|
|SynCount|SYN antal|Antal|Totalt|Totalt antal SYN-paket som skickas inom tidsperiod|Port, riktning|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Totalt|Antalet byte per sekund som Application Gateway har meddelat|Inga dimensioner|
|UnhealthyHostCount|Felaktiga värden antal|Antal|Medel|Antal felaktiga backend-värdar. Du kan filtrera på en per backend-pool för att visa felfri/ohälsosamt värdar i en specifik serverdelspool.|BackendSettingsPool|
|healthyHostCount|Felfri värden antal|Antal|Medel|Antalet felfri backend-värdar. Du kan filtrera på en per backend-pool för att visa felfri/ohälsosamt värdar i en specifik serverdelspool.|BackendSettingsPool. |
|TotalRequests|Totalt antal begäranden|Antal|Totalt|Antal slutförda förfrågningar som Application Gateway har hanteras|BackendSettingsPool|
|FailedRequests|Misslyckade förfrågningar|Antal|Totalt|Antal misslyckade begäranden som har meddelat Programgateway|BackendSettingsPool|
|ResponseStatus|Response-Status|Antal|Totalt|HTTP-svarsstatus returnerades av Application Gateway. Svaret status kod distribution kan vara ytterligare categoized att visa svar i 2xx, 3xx, 4xx och 5xx kategorier.|HttpStatusGroup|
|CurrentConnections|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar med Programgateway|Inga dimensioner|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Tunnel bandbredd|BytesPerSecond|Medel|Genomsnittlig bandbredden för en tunnel i byte per sekund|ConnectionName RemoteIP|
|TunnelEgressBytes|Tunnel utgående byte|Byte|Totalt|Utgående byte en tunnel|ConnectionName RemoteIP|
|TunnelIngressBytes|Tunnel ingång byte|Byte|Totalt|Inkommande byte en tunnel|ConnectionName RemoteIP|
|TunnelEgressPackets|Tunnel utgående paket|Antal|Totalt|Utgående antal en tunnel|ConnectionName RemoteIP|
|TunnelIngressPackets|Tunnel ingång paket|Antal|Totalt|Inkommande antal en tunnel|ConnectionName RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel utgång TS matchningsfel paket släpp|Antal|Totalt|Utgående släpp antal från trafik selector matchar inte en tunnel|ConnectionName RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel ingång TS matchningsfel paket släpp|Antal|Totalt|Inkommande släpp antal från trafik selector matchar inte en tunnel|ConnectionName RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS ingressing Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS egressing Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Frågor av slutpunkten som returnerade|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den angivna tiden|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status för endpoint av slutpunkten|Antal|Maximal|1 om en slutpunkt avsökningen status är ”aktiverad”, annars 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Avsökningar misslyckades|Procent|Medel|% av anslutningen övervakning avsökningar misslyckades|Inga dimensioner|
|AverageRoundtripMs|Genomsn. Tidsfördröjningen (ms)|Millisekunder|Medel|Genomsnittlig nätverket tidsfördröjningen (ms) för övervakning av avsökningar som skickas mellan käll- och -anslutning|Inga dimensioner|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Registration.all|Registreringsåtgärder|Antal|Totalt|Totalt antal genomförda registreringsåtgärder (skapa, uppdatera, fråga och ta bort). |Inga dimensioner|
|Registration.Create|Registreringsskaparåtgärder|Antal|Totalt|Totalt antal registreringar som skapats.|Inga dimensioner|
|registration.update|Registreringsuppdateringsåtgärder|Antal|Totalt|Totalt antal genomförda registreringsuppdateringar.|Inga dimensioner|
|registration.get|Registreringsläsningsåtgärder|Antal|Totalt|Totalt antal utförda registreringsfrågor.|Inga dimensioner|
|registration.delete|Registreringsborttagningsåtgärder|Antal|Totalt|Totalt antal registreringar som tagits bort.|Inga dimensioner|
|inkommande|Inkommande meddelanden|Antal|Totalt|Totalt antal API-anrop som skickats. |Inga dimensioner|
|Incoming.Scheduled|Schemalagt push-meddelande har skickats|Antal|Totalt|Schemalagt push-meddelande har avbrutits|Inga dimensioner|
|Incoming.Scheduled.Cancel|Schemalagt push-meddelande har avbrutits|Antal|Totalt|Schemalagt push-meddelande har avbrutits|Inga dimensioner|
|Scheduled.Pending|Schemalagda meddelanden som väntar|Antal|Totalt|Schemalagda meddelanden som väntar|Inga dimensioner|
|installation.all|Installationshanteringsåtgärder|Antal|Totalt|Installationshanteringsåtgärder|Inga dimensioner|
|installation.get|Åtgärder för att hämta installation|Antal|Totalt|Åtgärder för att hämta installation|Inga dimensioner|
|installation.upsert|Skapa eller uppdatera installationsåtgärder|Antal|Totalt|Skapa eller uppdatera installationsåtgärder|Inga dimensioner|
|installation.patch|Åtgärder för att korrigera installation|Antal|Totalt|Åtgärder för att korrigera installation|Inga dimensioner|
|installation.delete|Åtgärder för att ta bort installation|Antal|Totalt|Åtgärder för att ta bort installation|Inga dimensioner|
|Outgoing.allpns.Success|Levererade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.allpns.invalidpayload|Nyttolastfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS returnerade ett nyttolastfel.|Inga dimensioner|
|Outgoing.allpns.pnserror|Externa meddelandesystemfel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med PNS (inte autentiseringsproblem).|Inga dimensioner|
|Outgoing.allpns.channelerror|Kanalfel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att kanalen var ogiltig, inte kopplad till rätt app, begränsad eller hade upphört att gälla.|Inga dimensioner|
|Outgoing.allpns.badorexpiredchannel|Felaktig eller utgången kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av utgången eller ogiltig kanal/token/registrationId i registreringen.|Inga dimensioner|
|Outgoing.wns.Success|WNS – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|Outgoing.wns.invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade. (Windows Live kan inte identifiera autentiseringsuppgifterna).|Inga dimensioner|
|outgoing.wns.badchannel|WNS – felaktig kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inga dimensioner|
|outgoing.wns.expiredchannel|WNS – utgången kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att ChannelURI har upphört att gälla (WNS-status: 410 Sidan finns inte).|Inga dimensioner|
|Outgoing.wns.throttled|WNS – begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom WNS begränsar den här appen (WNS-status: 406 Ogiltigt format).|Inga dimensioner|
|Outgoing.wns.tokenproviderunreachable|WNS-auktoriseringsfel (kan inte nås)|Antal|Totalt|Windows Live kan inte nås.|Inga dimensioner|
|outgoing.wns.invalidtoken|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Token som angavs i WNS är inte giltig (WNS-status: 401 Ej behörig).|Inga dimensioner|
|outgoing.wns.wrongtoken|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS status: 403 nekad). Detta kan inträffa om ChannelURI i registreringen är associerad med en annan app. Kontrollera att klientappen är associerad med samma app vars autentiseringsuppgifter finns i meddelandehubben.|Inga dimensioner|
|outgoing.wns.invalidnotificationformat|WNS – ogiltigt meddelandeformat|Antal|Totalt|Formatet för meddelandet är ogiltigt (WNS status: 400). Observera att WNS inte avvisa alla ogiltig nyttolaster.|Inga dimensioner|
|Outgoing.wns.invalidnotificationsize|WNS – ogiltig meddelandestorlek|Antal|Totalt|Meddelandets nyttolast är för stor (WNS-status: 413).|Inga dimensioner|
|Outgoing.wns.channelthrottled|WNS – begränsad kanal|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svarshuvud: X-WNS-NotificationStatus:channelThrottled).|Inga dimensioner|
|Outgoing.wns.channeldisconnected|WNS – frånkopplad kanal|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svarshuvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|outgoing.wns.dropped|WNS – utelämnade meddelanden|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: utelämnat men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|outgoing.wns.pnserror|WNS-fel|Antal|Totalt|Meddelandet levererades inte på grund av kommunikationsproblem med WNS.|Inga dimensioner|
|Outgoing.wns.authenticationerror|WNS-auktoriseringsfel|Antal|Totalt|Meddelanden som inte levererats på grund av kommunikationsproblem med ogiltiga Windows Live-autentiseringsuppgifter eller fel token.|Inga dimensioner|
|Outgoing.apns.Success|APNS – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|Outgoing.apns.invalidcredentials|APNs-auktoriseringsfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.apns.badchannel|APNS – felaktig kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av ogiltig token (APNs-statuskod: 8).|Inga dimensioner|
|outgoing.apns.expiredchannel|APNS – utgången kanal|Antal|Totalt|Antal token som ogiltigförklarades av APNs-feedbackkanalen.|Inga dimensioner|
|Outgoing.apns.invalidnotificationsize|APNS – ogiltig meddelandestorlek|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av för stor nyttolast (APNs-statuskod: 7).|Inga dimensioner|
|outgoing.apns.pnserror|APNS-fel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med APNs.|Inga dimensioner|
|Outgoing.GCM.Success|GCM – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|Outgoing.GCM.invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.gcm.badchannel|GCM – felaktig kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att registrationId i registreringen inte kändes igen (GCM-resultat: ogiltig registrering).|Inga dimensioner|
|Outgoing.GCM.expiredchannel|GCM – utgången kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|Outgoing.GCM.throttled|GCM – begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att GCM begränsar appen (GCM-statuskod: 501-599 eller resultat:Unavailable).|Inga dimensioner|
|outgoing.gcm.invalidnotificationformat|GCM – ogiltigt meddelandeformat|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom nyttolasten var felformaterad (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|Outgoing.GCM.invalidnotificationsize|GCM – ogiltig meddelandestorlek|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av för stor nyttolast (GCM-resultat: MessageTooBig).|Inga dimensioner|
|Outgoing.GCM.wrongchannel|GCM – fel kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att registrationId i registreringen inte är kopplat till den aktuella appen (GCM-resultat: InvalidPackageName).|Inga dimensioner|
|outgoing.gcm.pnserror|GCM-fel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med GCM.|Inga dimensioner|
|Outgoing.GCM.authenticationerror|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna, autentiseringsuppgifterna var blockerade eller SenderId var felkonfigurerat i appen (GCM-resultat: MismatchedSenderId).|Inga dimensioner|
|Outgoing.mpns.Success|MPNS – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|Outgoing.mpns.invalidcredentials|MPNS – ogiltiga autentiseringsuppgifter|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.mpns.badchannel|MPNS – felaktig kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inga dimensioner|
|Outgoing.mpns.throttled|MPNS – begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom MPNS begränsar den här appen (WNS MPNS: 406 Ogiltigt format).|Inga dimensioner|
|outgoing.mpns.invalidnotificationformat|MPNS – ogiltigt meddelandeformat|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom nyttolasten för meddelanden var för stor.|Inga dimensioner|
|Outgoing.mpns.channeldisconnected|MPNS – frånkopplad kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att ChannelURI i registreringen frånkopplades (MPNS-status: 412 hittades inte).|Inga dimensioner|
|outgoing.mpns.dropped|MPNS – utelämnade meddelanden|Antal|Totalt|Antal push-meddelanden som utelämnats av MPNS (MPNS-svarshuvud: X-NotificationStatus: QueueFull eller Suppressed).|Inga dimensioner|
|outgoing.mpns.pnserror|MPNS-fel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med MPNS.|Inga dimensioner|
|Outgoing.mpns.authenticationerror|MPNS-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|notificationhub.pushes|Alla utgående meddelanden|Antal|Totalt|Alla utgående meddelanden i meddelandehubben|Inga dimensioner|
|Incoming.all.Requests|Alla inkommande förfrågningar|Antal|Totalt|Totalt antal inkommande förfrågningar för en meddelandehubb|Inga dimensioner|
|Incoming.all.failedrequests|Alla inkommande misslyckade förfrågningar|Antal|Totalt|Totalt antal inkommande misslyckade förfrågningar för en meddelandehubb|Inga dimensioner|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces
(Förhandsversion)

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_ % Ledigai|Average_ % Ledigai|Antal|Medel|Average_ % Ledigai|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ % ledigt utrymme|Average_ % ledigt utrymme|Antal|Medel|Average_ % ledigt utrymme|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ används i procent|Average_ används i procent|Antal|Medel|Average_ används i procent|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Använt utrymme i procent för Average_|Använt utrymme i procent för Average_|Antal|Medel|Använt utrymme i procent för Average_|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Disk lästa byte/sek|Average_Disk lästa byte/sek|Antal|Medel|Average_Disk lästa byte/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Disk Diskläsningar/sek|Average_Disk Diskläsningar/sek|Antal|Medel|Average_Disk Diskläsningar/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Disk disköverföringar/sek|Average_Disk disköverföringar/sek|Antal|Medel|Average_Disk disköverföringar/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Disk skrivna byte/s|Average_Disk skrivna byte/s|Antal|Medel|Average_Disk skrivna byte/s|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Disk Diskskrivningar/sek|Average_Disk Diskskrivningar/sek|Antal|Medel|Average_Disk Diskskrivningar/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Free megabyte|Average_Free megabyte|Antal|Medel|Average_Free megabyte|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Logical Disk byte/sek|Average_Logical Disk byte/sek|Antal|Medel|Average_Logical Disk byte/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ tillgängligt minne i procent|Average_ tillgängligt minne i procent|Antal|Medel|Average_ tillgängligt minne i procent|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Tillgängligt växlingsutrymme i Average_ %|Tillgängligt växlingsutrymme i Average_ %|Antal|Medel|Tillgängligt växlingsutrymme i Average_ %|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Använt minne i procent för Average_|Använt minne i procent för Average_|Antal|Medel|Använt minne i procent för Average_|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ % använt växlingsutrymme|Average_ % använt växlingsutrymme|Antal|Medel|Average_ % använt växlingsutrymme|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Available minne i megabyte|Average_Available minne i megabyte|Antal|Medel|Average_Available minne i megabyte|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Available megabyte växlingsutrymme|Average_Available megabyte växlingsutrymme|Antal|Medel|Average_Available megabyte växlingsutrymme|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Page Diskläsningar/sek|Average_Page Diskläsningar/sek|Antal|Medel|Average_Page Diskläsningar/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Page Diskskrivningar/sek|Average_Page Diskskrivningar/sek|Antal|Medel|Average_Page Diskskrivningar/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Pages per sekund|Average_Pages per sekund|Antal|Medel|Average_Pages per sekund|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Used megabyte växlingsutrymme|Average_Used megabyte växlingsutrymme|Antal|Medel|Average_Used megabyte växlingsutrymme|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Used minne i megabyte|Average_Used minne i megabyte|Antal|Medel|Average_Used minne i megabyte|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Total byte som överförs|Average_Total byte som överförs|Antal|Medel|Average_Total byte som överförs|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Total mottagna byte|Average_Total mottagna byte|Antal|Medel|Average_Total mottagna byte|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Total byte|Average_Total byte|Antal|Medel|Average_Total byte|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Total antal skickade paket|Average_Total antal skickade paket|Antal|Medel|Average_Total antal skickade paket|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Total paket som tagits emot|Average_Total paket som tagits emot|Antal|Medel|Average_Total paket som tagits emot|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Total Rx fel|Average_Total Rx fel|Antal|Medel|Average_Total Rx fel|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Total Tx-fel|Average_Total Tx-fel|Antal|Medel|Average_Total Tx-fel|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Total kollisioner|Average_Total kollisioner|Antal|Medel|Average_Total kollisioner|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Avg. Disk sek/läsning|Average_Avg. Disk sek/läsning|Antal|Medel|Average_Avg. Disk sek/läsning|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Avg. Disk sek/disköverföring|Average_Avg. Disk sek/disköverföring|Antal|Medel|Average_Avg. Disk sek/disköverföring|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Avg. Disk sek/skrivning|Average_Avg. Disk sek/skrivning|Antal|Medel|Average_Avg. Disk sek/skrivning|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Physical Disk byte/sek|Average_Physical Disk byte/sek|Antal|Medel|Average_Physical Disk byte/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Privilegierad tid i Average_Pct|Privilegierad tid i Average_Pct|Antal|Medel|Privilegierad tid i Average_Pct|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Användartid i Average_Pct|Användartid i Average_Pct|Antal|Medel|Användartid i Average_Pct|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Used minne Kbyte|Average_Used minne Kbyte|Antal|Medel|Average_Used minne Kbyte|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Virtual delat minne|Average_Virtual delat minne|Antal|Medel|Average_Virtual delat minne|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ % DPC-tid|Average_ % DPC-tid|Antal|Medel|Average_ % DPC-tid|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ inaktivitetstid i procent|Average_ inaktivitetstid i procent|Antal|Medel|Average_ inaktivitetstid i procent|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ avbrottstid i procent|Average_ avbrottstid i procent|Antal|Medel|Average_ avbrottstid i procent|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ %-i/o-väntetid|Average_ %-i/o-väntetid|Antal|Medel|Average_ %-i/o-väntetid|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ Nice Time|Average_ Nice Time|Antal|Medel|Average_ Nice Time|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Privilegierad tid i procent för Average_|Privilegierad tid i procent för Average_|Antal|Medel|Privilegierad tid i procent för Average_|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ % processortid|Average_ % processortid|Antal|Medel|Average_ % processortid|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ Användartid i procent|Average_ Användartid i procent|Antal|Medel|Average_ Användartid i procent|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Free fysiskt minne|Average_Free fysiskt minne|Antal|Medel|Average_Free fysiskt minne|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Free utrymme i växlingsfiler|Average_Free utrymme i växlingsfiler|Antal|Medel|Average_Free utrymme i växlingsfiler|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Free virtuellt minne|Average_Free virtuellt minne|Antal|Medel|Average_Free virtuellt minne|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Processes|Average_Processes|Antal|Medel|Average_Processes|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Size lagrad i växlingsfiler|Average_Size lagrad i växlingsfiler|Antal|Medel|Average_Size lagrad i växlingsfiler|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Uptime|Average_Uptime|Antal|Medel|Average_Uptime|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Users|Average_Users|Antal|Medel|Average_Users|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Avg. Disk sek/läsning|Average_Avg. Disk sek/läsning|Antal|Medel|Average_Avg. Disk sek/läsning|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Avg. Disk sek/skrivning|Average_Avg. Disk sek/skrivning|Antal|Medel|Average_Avg. Disk sek/skrivning|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Current diskkölängd|Average_Current diskkölängd|Antal|Medel|Average_Current diskkölängd|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Disk Diskläsningar/sek|Average_Disk Diskläsningar/sek|Antal|Medel|Average_Disk Diskläsningar/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Disk disköverföringar/sek|Average_Disk disköverföringar/sek|Antal|Medel|Average_Disk disköverföringar/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Disk Diskskrivningar/sek|Average_Disk Diskskrivningar/sek|Antal|Medel|Average_Disk Diskskrivningar/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Free megabyte|Average_Free megabyte|Antal|Medel|Average_Free megabyte|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ % ledigt utrymme|Average_ % ledigt utrymme|Antal|Medel|Average_ % ledigt utrymme|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Available megabyte|Average_Available megabyte|Antal|Medel|Average_Available megabyte|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ % allokerade byte som används|Average_ % allokerade byte som används|Antal|Medel|Average_ % allokerade byte som används|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Bytes/sek|Average_Bytes/sek|Antal|Medel|Average_Bytes/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Bytes per sekund|Average_Bytes per sekund|Antal|Medel|Average_Bytes per sekund|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Bytes totalt/sek|Average_Bytes totalt/sek|Antal|Medel|Average_Bytes totalt/sek|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_ % processortid|Average_ % processortid|Antal|Medel|Average_ % processortid|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Average_Processor Kölängd|Average_Processor Kölängd|Antal|Medel|Average_Processor Kölängd|Datorn ObjectName, InstanceName, räknarsökväg, SourceSystem|
|Pulsslag|Pulsslag|Antal|Medel|Pulsslag|OSType, Datorversion, SourceComputerId|
|Uppdatering|Uppdatering|Antal|Medel|Uppdatering|Dator, produkt, klassificering, UpdateState valfria, godkända|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryDuration|Frågan varaktighet|Millisekunder|Medel|DAX-frågor varaktighet i det sista intervallet|Inga dimensioner|
|QueryPoolJobQueueLength|Trådar: Kölängd frågan poolen jobb|Antal|Medel|Antal jobb i kö i trådpoolen för frågan.|Inga dimensioner|
|qpu_high_utilization_metric|QPU hög belastning|Antal|Totalt|QPU hög användning i den senaste minuten, 1 för hög QPU användning, annars 0|Inga dimensioner|
|memory_metric|Minne|Byte|Medel|Minne. Intervallet 0-3 GB för A1, 0-5 GB för A2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Minne knattrar|Procent|Medel|Genomsnittlig minne knattrar.|Inga dimensioner|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Antal|Totalt|Lyckade ListenerConnections för Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Antal|Totalt|ClientError på ListenerConnections för Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Antal|Totalt|ServerError på ListenerConnections för Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Antal|Totalt|Lyckade SenderConnections för Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Antal|Totalt|ClientError på SenderConnections för Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Antal|Totalt|ServerError på SenderConnections för Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Antal|Totalt|Totalt ListenerConnections för Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Antal|Totalt|Totalt SenderConnections-förfrågningar för Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Antal|Totalt|Totalt ActiveConnections för Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Antal|Totalt|Totalt ActiveListeners för Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Antal|Totalt|Totalt BytesTransferred för Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Antal|Totalt|Totalt ListenerDisconnects för Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Antal|Totalt|Total SenderDisconnects för Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SearchLatency|Sök svarstid|Sekunder|Medel|Sök genomsnittlig svarstid för search-tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Sökfrågor per sekund|CountPerSecond|Medel|Sökfrågor per sekund för search-tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Begränsade sökfrågor i procent|Procent|Medel|Procentandelen sökfrågor som har begränsats för söktjänsten|Inga dimensioner|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade begäranden (förhandsgranskning)|Antal|Totalt|Totalt antal lyckade begäranden för ett namnområde (förhandsgranskning)|EntityName, |
|ServerErrors|Serverfel. (Förhandsversion)|Antal|Totalt|Serverfel för Microsoft.ServiceBus. (Förhandsversion)|EntityName, |
|UserErrors|Användarfel. (Förhandsversion)|Antal|Totalt|Användarfel för Microsoft.ServiceBus. (Förhandsversion)|EntityName, |
|ThrottledRequests|Begränsade begäranden. (Förhandsversion)|Antal|Totalt|Begränsade begäranden för Microsoft.ServiceBus. (Förhandsversion)|EntityName, |
|IncomingRequests|Inkommande begäranden (förhandsgranskning)|Antal|Totalt|Inkommande begäranden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|IncomingMessages|Inkommande meddelanden (förhandsgranskning)|Antal|Totalt|Inkommande meddelanden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|OutgoingMessages|Utgående meddelanden (förhandsgranskning)|Antal|Totalt|Utgående meddelanden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|ActiveConnections|ActiveConnections (förhandsgranskning)|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft.ServiceBus. (Förhandsversion)|Inga dimensioner|
|Storlek|Storlek (förhandsgranskning)|Byte|Medel|Storleken på en kö/avsnittet i byte. (Förhandsversion)|EntityName|
|Meddelanden|Antal meddelanden i kö/avsnittet. (Förhandsversion)|Antal|Medel|Antal meddelanden i kö/avsnittet. (Förhandsversion)|EntityName|
|ActiveMessages|Antal aktiva meddelanden i kö/avsnittet. (Förhandsversion)|Antal|Medel|Antal aktiva meddelanden i kö/avsnittet. (Förhandsversion)|EntityName|
|CPUXNS|Processoranvändning per namnområde|Procent|Maximal|CPU-användningsmått för Service Bus Premium-namnområde|Inga dimensioner|
|WSXNS|Minnesstorleksanvändning per namnområde|Procent|Maximal|Minnesanvändningsmått för Service Bus Premium-namnområde|Inga dimensioner|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
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
|dw_cpu_percent|DW nivån CPU-procent|Procent|Medel|DW nivån CPU-procent|DwLogicalNodeId|
|dw_physical_data_read_percent|DW nod nivån Data IO-procent|Procent|Medel|DW nod nivån Data IO-procent|DwLogicalNodeId|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inga dimensioner|
|log_write_percent|Loggen IO-procent|Procent|Medel|Loggen IO-procent|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|Inga dimensioner|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inga dimensioner|
|workers_percent|Procentsatsen för arbetare|Procent|Medel|Procentsatsen för arbetare|Inga dimensioner|
|sessions_percent|Sessioner procent|Procent|Medel|Sessioner procent|Inga dimensioner|
|eDTU_limit|eDTU gräns|Antal|Medel|eDTU gräns|Inga dimensioner|
|storage_limit|Lagringsgränsen|Byte|Medel|Lagringsgränsen|Inga dimensioner|
|eDTU_used|eDTU används|Antal|Medel|eDTU används|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|xtp_storage_percent|Minnesintern OLTP lagring procent|Procent|Medel|Minnesintern OLTP lagring procent|Inga dimensioner|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|ElasticPoolResourceId|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Medel|Kapacitet som används av konto|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType ApiName|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType ApiName|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|Blobkapacitet|Byte|Totalt|Mängden lagring som används av lagringskontots Blob Service i byte.|BlobType|
|BlobCount|Antalet blobar|Antal|Totalt|Antalet blobar i lagringskontots Blob Service.|BlobType|
|ContainerCount|Antal blobbehållare|Antal|Medel|Antalet behållare i lagringskontots Blob Service.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType ApiName|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType ApiName|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabellkapacitet|Byte|Medel|Mängden lagring som används av lagringskontots tabelltjänst i byte.|Inga dimensioner|
|TableCount|Antal tabeller|Antal|Medel|Antalet tabeller i lagringskontots tabelltjänst.|Inga dimensioner|
|TableEntityCount|Antal tabellentiteter|Antal|Medel|Antalet tabellentiteter i lagringskontots tabelltjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType ApiName|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType ApiName|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kökapacitet|Byte|Medel|Mängden lagring som används av lagringskontots kötjänst i byte.|Inga dimensioner|
|QueueCount|Antal köer|Antal|Medel|Antalet köer i lagringskontots kötjänst.|Inga dimensioner|
|QueueMessageCount|Antal kömeddelanden|Antal|Medel|Ungefärligt antal kömeddelanden i lagringskontots kötjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType ApiName|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType ApiName|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Filkapacitet|Byte|Medel|Mängden lagring som används av lagringskontots filtjänst i byte.|Inga dimensioner|
|FileCount|Antal filer|Antal|Medel|Antalet filer i lagringskontots filtjänst.|Inga dimensioner|
|FileShareCount|Antal filresurser|Antal|Medel|Antalet filresurser i lagringskontots filtjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerats av fel. Använd ResponseType dimensionen i många olika typer av svar.|ResponseType GeoType, ApiName|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingång data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType ApiName|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType ApiName|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid som används av Azure Storage för att bearbeta en begäran om lyckades, i millisekunder. Det här värdet inkluderar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType ApiName|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType ApiName|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet TotalBillableRequests och dividera med antalet tillämpliga begäranden, inklusive de som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
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
|DeserializationError|Fel vid deserialisering av indata|Antal|Totalt|Fel vid deserialisering av indata|Inga dimensioner|
|EarlyInputEvents|Händelser vars tillämpningstid ligger tidigare än deras ankomsttid.|Antal|Totalt|Händelser vars tillämpningstid ligger tidigare än deras ankomsttid.|Inga dimensioner|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Procent|Medel|Minnesprocent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Medel|Diskkölängd|Instans|
|HttpQueueLength|Http-kölängd|Antal|Medel|Http-kölängd|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (exklusive funktioner)

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
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
|AppConnections|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Handtag|Antal referenser|Antal|Medel|Antal referenser|Instans|
|Trådar|Antal trådar|Antal|Medel|Antal trådar|Instans|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funktioner)

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Medel|Genomsnittligt arbetsminne|Instans|
|FunctionExecutionUnits|Funktionskörningsenheter|Antal|Totalt|Funktionskörningsenheter|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Antal|Totalt|Funktionen Antal körningar|Instans|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
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
|FunctionExecutionUnits|Funktionskörningsenheter|Antal|Totalt|Funktionskörningsenheter|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Antal|Totalt|Funktionen Antal körningar|Instans|
|AppConnections|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Handtag|Antal referenser|Antal|Medel|Antal referenser|Instans|
|Trådar|Antal trådar|Antal|Medel|Antal trådar|Instans|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
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
|DiskQueueLength|Diskkölängd|Antal|Medel|Diskkölängd|Instans|
|HttpQueueLength|Http-kölängd|Antal|Medel|Http-kölängd|Instans|
|ActiveRequests|Aktiva förfrågningar|Antal|Totalt|Aktiva förfrågningar|Instans|
|TotalFrontEnds|Totalt antal klienter|Antal|Medel|Totalt antal klienter|Inga dimensioner|
|SmallAppServicePlanInstances|App Service-plan för arbeten (låg användning)|Antal|Medel|App Service-plan för arbeten (låg användning)|Inga dimensioner|
|MediumAppServicePlanInstances|App Service-plan för arbeten (medelhög användning)|Antal|Medel|App Service-plan för arbeten (medelhög användning)|Inga dimensioner|
|LargeAppServicePlanInstances|App Service-plan för arbeten (hög användning)|Antal|Medel|App Service-plan för arbeten (hög användning)|Inga dimensioner|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbeten|Antal|Medel|Totalt antal arbeten|Inga dimensioner|
|WorkersAvailable|Tillgängliga arbeten|Antal|Medel|Tillgängliga arbeten|Inga dimensioner|
|WorkersUsed|Använda arbeten|Antal|Medel|Använda arbeten|Inga dimensioner|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Procent|Medel|Minnesprocent|Instans|

## <a name="next-steps"></a>Nästa steg
* [Läs mer om mätvärden i Azure-Monitor](monitoring-overview-metrics.md)
* [Skapa aviseringar på mått](insights-receive-alert-notifications.md)
* [Exportera mått till lagring, Event Hub eller logganalys](monitoring-overview-of-diagnostic-logs.md)
