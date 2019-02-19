---
title: Azure Monitor stöds mått efter resurstyp
description: Lista över mått som är tillgängliga för varje resurstyp med Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 09/14/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 8ee900554371644f374e4aeed51f1eeb0c18569e
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408875"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor
Azure Monitor innehåller flera olika sätt att interagera med mätvärden, inklusive diagram dem i portalen, få åtkomst till dem via REST API eller frågor till dem med PowerShell eller CLI. Nedan visas en fullständig lista över alla mått som är tillgänglig med Azure Monitor mått pipeline. Andra mått kan finnas i portalen eller med äldre API: er. Listan nedan innehåller endast mått som är tillgängliga med hjälp av konsoliderade pipelinen för Azure Monitor-mått. Fråga efter och komma åt de här måtten Använd den [2018-01-01 api-versionen](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och visas för varje enskild kö. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|qpu_metric|QPU|Antal|Medel|QPU. Intervall 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|memory_metric|Minne|Byte|Medel|Minne. Intervall 0 – 25 GB för S1, 0 – 50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|TotalConnectionRequests|Total antal anslutningsbegäranden|Antal|Medel|Total antal anslutningsbegäranden. Det här är anslutningsbegäranden.|ServerResourceType|
|SuccessfullConnectionsPerSec|Lyckade anslutningar Per sekund|CountPerSecond|Medel|Hastighet för lyckade anslutningsavslutningar.|ServerResourceType|
|TotalConnectionFailures|Totalt antal misslyckade anslutningar|Antal|Medel|Antalet misslyckade anslutningsförsök.|ServerResourceType|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medel|Aktuellt antal etablerade användarsessioner.|ServerResourceType|
|QueryPoolBusyThreads|Upptagna trådar för Frågepoolen|Antal|Medel|Antal upptagna trådar i frågetrådspoolen.|ServerResourceType|
|CommandPoolJobQueueLength|Kommandot Pool Jobbkölängd|Antal|Medel|Antal jobb i kön för kommandotrådspoolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Bearbetning av poolen Jobbkölängd|Antal|Medel|Antal icke-I/O-jobb i kön för bearbetningstrådpoolen.|ServerResourceType|
|CurrentConnections|Anslutning: Aktuella anslutningar|Antal|Medel|Aktuellt antal etablerade klientanslutningar.|ServerResourceType|
|CleanerCurrentPrice|Minne: Aktuellt pris för rensaren|Antal|Medel|Aktuellt pris för minne, $/ byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Minne: Rensningsminne Krympbart|Byte|Medel|Mängden minne i byte, tas bort av bakgrundsrensaren.|ServerResourceType|
|CleanerMemoryNonshrinkable|Minne: Rensningsminne krympbart|Byte|Medel|Mängden minne i byte, inte tas bort av bakgrundsrensaren.|ServerResourceType|
|MemoryUsage|Minne: Minnesanvändning|Byte|Medel|Minnesanvändning för serverprocessen som används för att beräkna tydligare minnespris. Lika med räknare Process\PrivateBytes plus storleken på minnesmappade data och ignorerar minne som mappats eller allokerats av xVelocity-minnesinterna analyser-motorns (VertiPaq) utöver xVelocity-motorns minnesgräns.|ServerResourceType|
|MemoryLimitHard|Minne: Minnesgräns, hård|Byte|Medel|Hård minnesgräns, från konfigurationsfilen.|ServerResourceType|
|MemoryLimitHigh|Minne: Minnesgräns hög|Byte|Medel|Hög minnesgräns, från konfigurationsfilen.|ServerResourceType|
|MemoryLimitLow|Minne: Minnesgräns låg|Byte|Medel|Låg minnesgräns, från konfigurationsfilen.|ServerResourceType|
|MemoryLimitVertiPaq|Minne: Minnesgräns VertiPaq|Byte|Medel|Minnesintern gräns, från konfigurationsfilen.|ServerResourceType|
|Kvot|Minne: Kvot|Byte|Medel|Aktuella minneskvoten, i byte. Minneskvoten är även känd som en minnesreservation för bevilja eller minne.|ServerResourceType|
|QuotaBlocked|Minne: Kvot blockerad|Antal|Medel|Aktuella antalet kvotbegäranden som blockeras tills andra minneskvoter.|ServerResourceType|
|VertiPaqNonpaged|Minne: VertiPaq Nonpaged|Byte|Medel|Byte av minne låst i arbetsminnet för användning av InMemory-motorn.|ServerResourceType|
|VertiPaqPaged|Minne: VertiPaq växlat|Byte|Medel|Bytes med växlingsbart minne som används för data i minnet.|ServerResourceType|
|RowsReadPerSec|Bearbetar: Rader lästa per sekund|CountPerSecond|Medel|Frekvensen som rader läses från alla relationella databaser.|ServerResourceType|
|RowsConvertedPerSec|Bearbetar: Rader konverterade per sekund|CountPerSecond|Medel|Frekvensen som rader konverteras under bearbetning.|ServerResourceType|
|RowsWrittenPerSec|Bearbetar: Rader skrivna per sekund|CountPerSecond|Medel|Frekvensen som rader skrivs vid bearbetning.|ServerResourceType|
|CommandPoolBusyThreads|Trådar: Kommandot upptagna trådar i kommandopoolen|Antal|Medel|Antal upptagna trådar i kommandotrådspoolen.|ServerResourceType|
|CommandPoolIdleThreads|Trådar: Kommandot frågepoolen inaktiva trådar|Antal|Medel|Antal inaktiva trådar i kommandotrådspoolen.|ServerResourceType|
|LongParsingBusyThreads|Trådar: Lång parsning upptagna trådar|Antal|Medel|Antal upptagna trådar i den lång parsningstrådspoolen.|ServerResourceType|
|LongParsingIdleThreads|Trådar: Lång parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i den lång parsningstrådspoolen.|ServerResourceType|
|LongParsingJobQueueLength|Trådar: Lång parsning jobbkölängd|Antal|Medel|Antal jobb i kön för den lång parsningstrådspoolen.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Trådar: Bearbetningspoolen upptagen i/o-jobbtrådar|Antal|Medel|Antal trådar som kör i/o-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Trådar: Bearbetning av poolen upptagen icke-I/O-trådar|Antal|Medel|Antal trådar som kör icke-I/O-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Trådar: Bearbetningspoolen i/o-jobbkölängd|Antal|Medel|Antal i/o-jobb i kön för bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Trådar: Bearbetningspoolen inaktiva i/o-jobbtrådar|Antal|Medel|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Trådar: Bearbetning av poolen inaktiva icke-I/O-trådar|Antal|Medel|Antal inaktiva trådar i bearbetningstrådpoolen som är dedikerade till icke-I/O-jobb.|ServerResourceType|
|QueryPoolIdleThreads|Trådar: Frågepoolen inaktiva trådar|Antal|Medel|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|ServerResourceType|
|QueryPoolJobQueueLength|Trådar: Fråga efter pool jobbkölängd|Antal|Medel|Antal jobb i kön för frågetrådspoolen.|ServerResourceType|
|ShortParsingBusyThreads|Trådar: Kort parsning upptagna trådar|Antal|Medel|Antal upptagna trådar i den korta parsningstrådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Trådar: Kort parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i den korta parsningstrådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Trådar: Kort parsning jobbkölängd|Antal|Medel|Antal jobb i kön för den korta parsningstrådpoolen.|ServerResourceType|
|memory_thrashing_metric|Minnesförslöing|Procent|Medel|Genomsnittlig minnesförslöing.|ServerResourceType|
|mashup_engine_qpu_metric|M-motor QPU|Antal|Medel|QPU-användning efter motorprocesser|ServerResourceType|
|mashup_engine_memory_metric|M-Motorminne|Byte|Medel|Minnesanvändning efter motorprocesser|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalRequests|Total Gateway Requests|Antal|Totalt|Antal gateway-begäranden|Plats, värdnamn|
|SuccessfulRequests|Lyckade Gateway-begäranden|Antal|Totalt|Antal lyckade gateway-begäranden|Plats, värdnamn|
|UnauthorizedRequests|Ej auktoriserade Gateway-begäranden|Antal|Totalt|Antal ej auktoriserade gateway-begäranden|Plats, värdnamn|
|FailedRequests|Misslyckade Gatewaybegäranden|Antal|Totalt|Antalet fel i gateway-begäranden|Plats, värdnamn|
|OtherRequests|Andra Gateway-begäranden|Antal|Totalt|Antal andra gateway-begäranden|Plats, värdnamn|
|Varaktighet|Varaktighet för Gateway-begäranden|Millisekunder|Medel|Övergripande varaktighet av Gateway-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Kapacitet|Procent|Medel|Utnyttjande mått för ApiManagement-tjänsten|Plats|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Runbook, Status|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CoreCount|Antal dedikerade kärnor|Antal|Totalt|Totalt antal reserverade kärnor i batch-konto|Inga dimensioner|
|TotalNodeCount|Dedikerad Nodantal|Antal|Totalt|Totalt antal dedikerade noder i batch-konto|Inga dimensioner|
|LowPriorityCoreCount|Antal kärnor för LowPriority|Antal|Totalt|Totala antalet kärnor med låg prioritet i batch-konto|Inga dimensioner|
|TotalLowPriorityNodeCount|Antal noder med låg prioritet|Antal|Totalt|Totalt antal noder med låg prioritet i batch-konto|Inga dimensioner|
|CreatingNodeCount|Skapa Nodantal|Antal|Totalt|Antalet noder som håller på att skapas|Inga dimensioner|
|StartingNodeCount|Startar Nodantal|Antal|Totalt|Antalet noder som startar|Inga dimensioner|
|WaitingForStartTaskNodeCount|Väntar på för Starta aktivitet antal noder|Antal|Totalt|Antalet noder som väntar på att åtgärden starta har slutförts|Inga dimensioner|
|StartTaskFailedNodeCount|Startuppgiften misslyckades Nodantal|Antal|Totalt|Antalet noder där aktiviteten starta misslyckades|Inga dimensioner|
|IdleNodeCount|Inaktiv Nodantal|Antal|Totalt|Antal inaktiva noder|Inga dimensioner|
|OfflineNodeCount|Offline Nodantal|Antal|Totalt|Antalet noder som är offline|Inga dimensioner|
|RebootingNodeCount|Starta om Nodantal|Antal|Totalt|Antal omstart noder|Inga dimensioner|
|ReimagingNodeCount|Återställningen av avbildningen Nodantal|Antal|Totalt|Antalet noder för återställning av avbildning|Inga dimensioner|
|RunningNodeCount|Kör Nodantal|Antal|Totalt|Antalet noder som körs|Inga dimensioner|
|LeavingPoolNodeCount|Lämnar Pool Nodantal|Antal|Totalt|Antalet noder som lämnar poolen|Inga dimensioner|
|UnusableNodeCount|Oanvändbara Nodantal|Antal|Totalt|Antalet noder som inte kan användas|Inga dimensioner|
|PreemptedNodeCount|Allokeringar Nodantal|Antal|Totalt|Antalet allokeringar noder|Inga dimensioner|
|TaskStartEvent|Aktiviteten starta händelser|Antal|Totalt|Totalt antal aktiviteter som har startat|Inga dimensioner|
|TaskCompleteEvent|Uppgift slutförd händelser|Antal|Totalt|Totalt antal aktiviteter som har slutförts|Inga dimensioner|
|TaskFailEvent|Uppgift misslyckas händelser|Antal|Totalt|Totalt antal aktiviteter som har slutförts i ett felaktigt tillstånd|Inga dimensioner|
|PoolCreateEvent|Händelser för skapande av pool|Antal|Totalt|Totalt antal pooler som har skapats|Inga dimensioner|
|PoolResizeStartEvent|Pool Resize Start händelser|Antal|Totalt|Totalt antal poolen ändrar storlek som har startat|Inga dimensioner|
|PoolResizeCompleteEvent|Pool Resize fullständig händelser|Antal|Totalt|Totalt antal poolen ändrar storlek som har slutförts|Inga dimensioner|
|PoolDeleteStartEvent|Pool ta bort Start händelser|Antal|Totalt|Totalt antal borttagningar för poolen som har startat|Inga dimensioner|
|PoolDeleteCompleteEvent|Pool ta bort klar händelser|Antal|Totalt|Totalt antal borttagningar för poolen som har slutförts|Inga dimensioner|
|JobDeleteCompleteEvent|Jobbet Ta bort klar händelser|Antal|Totalt|Totalt antal jobb som har tagits bort.|Inga dimensioner|
|JobDeleteStartEvent|Jobbet Ta bort Start händelser|Antal|Totalt|Totalt antal jobb som har begärts att tas bort.|Inga dimensioner|
|JobDisableCompleteEvent|Jobbet inaktivera fullständig händelser|Antal|Totalt|Totalt antal jobb som har inaktiverats.|Inga dimensioner|
|JobDisableStartEvent|Jobbet inaktivera Start-händelser|Antal|Totalt|Totalt antal jobb som har begärt att inaktiveras.|Inga dimensioner|
|JobStartEvent|Jobbet Start händelser|Antal|Totalt|Totalt antal jobb som har startats.|Inga dimensioner|
|JobTerminateCompleteEvent|Jobbet Avsluta fullständig händelser|Antal|Totalt|Totalt antal jobb som har avslutats.|Inga dimensioner|
|JobTerminateStartEvent|Jobbet Avsluta Start-händelser|Antal|Totalt|Totalt antal jobb som har begärt att avslutas.|Inga dimensioner|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedclients|Anslutna klienter|Antal|Maximal||ShardId|
|totalcommandsprocessed|Totalt antal åtgärder|Antal|Totalt||ShardId|
|cachehits|Cacheträffar|Antal|Totalt||ShardId|
|cachemisses|Cachemissar|Antal|Totalt||ShardId|
|getcommands|Hämtar|Antal|Totalt||ShardId|
|setcommands|Uppsättningar|Antal|Totalt||ShardId|
|operationsPerSecond|Åtgärder Per sekund|Antal|Maximal||ShardId|
|evictedkeys|Avlägsnade nycklar|Antal|Totalt||ShardId|
|totalkeys|Totalt antal nycklar|Antal|Maximal||ShardId|
|expiredkeys|Utgångna nycklar|Antal|Totalt||ShardId|
|usedmemory|Använt minne|Byte|Maximal||ShardId|
|usedmemorypercentage|Procentandelen använt minne|Procent|Maximal||ShardId|
|usedmemoryRss|Använt minne RSS|Byte|Maximal||ShardId|
|serverLoad|Serverbelastning|Procent|Maximal||ShardId|
|cacheWrite|Cacheskrivning|BytesPerSecond|Maximal||ShardId|
|cacheRead|Cacheläsning|BytesPerSecond|Maximal||ShardId|
|percentProcessorTime|Processor|Procent|Maximal||ShardId|
|cacheLatency|Cachelagra svarstid mikrosekunder (förhandsversion)|Antal|Medel||ShardId, SampleType|
|fel|Fel|Antal|Maximal||ShardId, ErrorType|
|connectedclients0|Anslutna klienter (fragment 0)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed0|Totalt antal åtgärder (fragment 0)|Antal|Totalt||Inga dimensioner|
|cachehits0|Cacheträffar (fragment 0)|Antal|Totalt||Inga dimensioner|
|cachemisses0|Cachemissar (fragment 0)|Antal|Totalt||Inga dimensioner|
|getcommands0|Hämtar (fragment 0)|Antal|Totalt||Inga dimensioner|
|setcommands0|Uppsättningar (Shard 0)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond0|Åtgärder Per sekund (fragment 0)|Antal|Maximal||Inga dimensioner|
|evictedkeys0|Avlägsnade nycklar (fragment 0)|Antal|Totalt||Inga dimensioner|
|totalkeys0|Totalt antal nycklar (fragment 0)|Antal|Maximal||Inga dimensioner|
|expiredkeys0|Utgångna nycklar (fragment 0)|Antal|Totalt||Inga dimensioner|
|usedmemory0|Använt minne (fragment 0)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss0|Använt minne RSS (fragment 0)|Byte|Maximal||Inga dimensioner|
|serverLoad0|Belastningen på servern (fragment 0)|Procent|Maximal||Inga dimensioner|
|cacheWrite0|Cacheskrivning (fragment 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead0|Cacheläsning (fragment 0)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime0|CPU (fragment 0)|Procent|Maximal||Inga dimensioner|
|connectedclients1|Anslutna klienter (Shard 1)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed1|Totalt antal åtgärder (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachehits1|Cacheträffar (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachemisses1|Cachemissar (Shard 1)|Antal|Totalt||Inga dimensioner|
|getcommands1|Hämtar (Shard 1)|Antal|Totalt||Inga dimensioner|
|setcommands1|Uppsättningar (Shard 1)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond1|Åtgärder Per sekund (Shard 1)|Antal|Maximal||Inga dimensioner|
|evictedkeys1|Avlägsnade nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|totalkeys1|Totalt antal nycklar (Shard 1)|Antal|Maximal||Inga dimensioner|
|expiredkeys1|Utgångna nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|usedmemory1|Använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss1|Använt minne RSS (Shard 1)|Byte|Maximal||Inga dimensioner|
|serverLoad1|Belastningen på servern (Shard 1)|Procent|Maximal||Inga dimensioner|
|cacheWrite1|Cacheskrivning (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead1|Cacheläsning (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime1|CPU (Shard 1)|Procent|Maximal||Inga dimensioner|
|connectedclients2|Anslutna klienter (fragment 2)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed2|Totalt antal åtgärder (fragment 2)|Antal|Totalt||Inga dimensioner|
|cachehits2|Cacheträffar (fragment 2)|Antal|Totalt||Inga dimensioner|
|cachemisses2|Cachemissar (fragment 2)|Antal|Totalt||Inga dimensioner|
|getcommands2|Hämtar (fragment 2)|Antal|Totalt||Inga dimensioner|
|setcommands2|Uppsättningar (fragment 2)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond2|Åtgärder Per sekund (fragment 2)|Antal|Maximal||Inga dimensioner|
|evictedkeys2|Avlägsnade nycklar (fragment 2)|Antal|Totalt||Inga dimensioner|
|totalkeys2|Totalt antal nycklar (fragment 2)|Antal|Maximal||Inga dimensioner|
|expiredkeys2|Utgångna nycklar (fragment 2)|Antal|Totalt||Inga dimensioner|
|usedmemory2|Använt minne (fragment 2)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss2|Använt minne RSS (fragment 2)|Byte|Maximal||Inga dimensioner|
|serverLoad2|Belastningen på servern (fragment 2)|Procent|Maximal||Inga dimensioner|
|cacheWrite2|Cacheskrivning (fragment 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead2|Cacheläsning (fragment 2)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime2|CPU (fragment 2)|Procent|Maximal||Inga dimensioner|
|connectedclients3|Anslutna klienter (fragment 3)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed3|Totalt antal åtgärder (fragment 3)|Antal|Totalt||Inga dimensioner|
|cachehits3|Cacheträffar (fragment 3)|Antal|Totalt||Inga dimensioner|
|cachemisses3|Cachemissar (fragment 3)|Antal|Totalt||Inga dimensioner|
|getcommands3|Hämtar (fragment 3)|Antal|Totalt||Inga dimensioner|
|setcommands3|Uppsättningar (fragment 3)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond3|Åtgärder Per sekund (fragment 3)|Antal|Maximal||Inga dimensioner|
|evictedkeys3|Avlägsnade nycklar (fragment 3)|Antal|Totalt||Inga dimensioner|
|totalkeys3|Totalt antal nycklar (fragment 3)|Antal|Maximal||Inga dimensioner|
|expiredkeys3|Utgångna nycklar (fragment 3)|Antal|Totalt||Inga dimensioner|
|usedmemory3|Använt minne (fragment 3)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss3|Använt minne RSS (fragment 3)|Byte|Maximal||Inga dimensioner|
|serverLoad3|Belastningen på servern (fragment 3)|Procent|Maximal||Inga dimensioner|
|cacheWrite3|Cacheskrivning (fragment 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead3|Cacheläsning (fragment 3)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime3|CPU (fragment 3)|Procent|Maximal||Inga dimensioner|
|connectedclients4|Anslutna klienter (fragment 4)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed4|Totalt antal åtgärder (fragment 4)|Antal|Totalt||Inga dimensioner|
|cachehits4|Cacheträffar (fragment 4)|Antal|Totalt||Inga dimensioner|
|cachemisses4|Cachemissar (fragment 4)|Antal|Totalt||Inga dimensioner|
|getcommands4|Hämtar (fragment 4)|Antal|Totalt||Inga dimensioner|
|setcommands4|Uppsättningar (fragment 4)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond4|Åtgärder Per sekund (fragment 4)|Antal|Maximal||Inga dimensioner|
|evictedkeys4|Avlägsnade nycklar (fragment 4)|Antal|Totalt||Inga dimensioner|
|totalkeys4|Totalt antal nycklar (fragment 4)|Antal|Maximal||Inga dimensioner|
|expiredkeys4|Utgångna nycklar (fragment 4)|Antal|Totalt||Inga dimensioner|
|usedmemory4|Använt minne (fragment 4)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss4|Använt minne RSS (fragment 4)|Byte|Maximal||Inga dimensioner|
|serverLoad4|Belastningen på servern (fragment 4)|Procent|Maximal||Inga dimensioner|
|cacheWrite4|Cacheskrivning (fragment 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead4|Cacheläsning (fragment 4)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime4|CPU (fragment 4)|Procent|Maximal||Inga dimensioner|
|connectedclients5|Anslutna klienter (fragment 5)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed5|Totalt antal åtgärder (fragment 5)|Antal|Totalt||Inga dimensioner|
|cachehits5|Cacheträffar (fragment 5)|Antal|Totalt||Inga dimensioner|
|cachemisses5|Cachemissar (fragment 5)|Antal|Totalt||Inga dimensioner|
|getcommands5|Hämtar (fragment 5)|Antal|Totalt||Inga dimensioner|
|setcommands5|Uppsättningar (fragment 5)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond5|Åtgärder Per sekund (fragment 5)|Antal|Maximal||Inga dimensioner|
|evictedkeys5|Avlägsnade nycklar (fragment 5)|Antal|Totalt||Inga dimensioner|
|totalkeys5|Totalt antal nycklar (fragment 5)|Antal|Maximal||Inga dimensioner|
|expiredkeys5|Utgångna nycklar (fragment 5)|Antal|Totalt||Inga dimensioner|
|usedmemory5|Använt minne (fragment 5)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss5|Använt minne RSS (fragment 5)|Byte|Maximal||Inga dimensioner|
|serverLoad5|Belastningen på servern (fragment 5)|Procent|Maximal||Inga dimensioner|
|cacheWrite5|Cacheskrivning (fragment 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead5|Cacheläsning (fragment 5)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime5|CPU (fragment 5)|Procent|Maximal||Inga dimensioner|
|connectedclients6|Anslutna klienter (fragment 6)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed6|Totalt antal åtgärder (fragment 6)|Antal|Totalt||Inga dimensioner|
|cachehits6|Cacheträffar (fragment 6)|Antal|Totalt||Inga dimensioner|
|cachemisses6|Cachemissar (fragment 6)|Antal|Totalt||Inga dimensioner|
|getcommands6|Hämtar (fragment 6)|Antal|Totalt||Inga dimensioner|
|setcommands6|Uppsättningar (Shard 6)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond6|Åtgärder Per sekund (fragment 6)|Antal|Maximal||Inga dimensioner|
|evictedkeys6|Avlägsnade nycklar (fragment 6)|Antal|Totalt||Inga dimensioner|
|totalkeys6|Totalt antal nycklar (fragment 6)|Antal|Maximal||Inga dimensioner|
|expiredkeys6|Utgångna nycklar (fragment 6)|Antal|Totalt||Inga dimensioner|
|usedmemory6|Använt minne (fragment 6)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss6|Använt minne RSS (fragment 6)|Byte|Maximal||Inga dimensioner|
|serverLoad6|Belastningen på servern (fragment 6)|Procent|Maximal||Inga dimensioner|
|cacheWrite6|Cacheskrivning (fragment 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead6|Cacheläsning (fragment 6)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime6|CPU (fragment 6)|Procent|Maximal||Inga dimensioner|
|connectedclients7|Anslutna klienter (Shard 7)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed7|Totalt antal åtgärder (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachehits7|Cache Hits (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachemisses7|Cachemissar (Shard 7)|Antal|Totalt||Inga dimensioner|
|getcommands7|Hämtar (Shard 7)|Antal|Totalt||Inga dimensioner|
|setcommands7|Uppsättningar (Shard 7)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond7|Åtgärder Per sekund (Shard 7)|Antal|Maximal||Inga dimensioner|
|evictedkeys7|Avlägsnade nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|totalkeys7|Totalt antal nycklar (Shard 7)|Antal|Maximal||Inga dimensioner|
|expiredkeys7|Utgångna nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|usedmemory7|Använt minne (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss7|Använt minne RSS (Shard 7)|Byte|Maximal||Inga dimensioner|
|serverLoad7|Belastningen på servern (Shard 7)|Procent|Maximal||Inga dimensioner|
|cacheWrite7|Cacheskrivning (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead7|Cacheläsning (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime7|CPU (Shard 7)|Procent|Maximal||Inga dimensioner|
|connectedclients8|Anslutna klienter (fragment 8)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed8|Totalt antal åtgärder (fragment 8)|Antal|Totalt||Inga dimensioner|
|cachehits8|Cacheträffar (fragment 8)|Antal|Totalt||Inga dimensioner|
|cachemisses8|Cachemissar (fragment 8)|Antal|Totalt||Inga dimensioner|
|getcommands8|Hämtar (fragment 8)|Antal|Totalt||Inga dimensioner|
|setcommands8|Uppsättningar (fragment 8)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond8|Åtgärder Per sekund (fragment 8)|Antal|Maximal||Inga dimensioner|
|evictedkeys8|Avlägsnade nycklar (fragment 8)|Antal|Totalt||Inga dimensioner|
|totalkeys8|Totalt antal nycklar (fragment 8)|Antal|Maximal||Inga dimensioner|
|expiredkeys8|Utgångna nycklar (fragment 8)|Antal|Totalt||Inga dimensioner|
|usedmemory8|Använt minne (fragment 8)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss8|Använt minne RSS (fragment 8)|Byte|Maximal||Inga dimensioner|
|serverLoad8|Belastningen på servern (fragment 8)|Procent|Maximal||Inga dimensioner|
|cacheWrite8|Cacheskrivning (fragment 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead8|Cacheläsning (fragment 8)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime8|CPU (fragment 8)|Procent|Maximal||Inga dimensioner|
|connectedclients9|Anslutna klienter (fragment 9)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed9|Totalt antal åtgärder (fragment 9)|Antal|Totalt||Inga dimensioner|
|cachehits9|Cacheträffar (fragment 9)|Antal|Totalt||Inga dimensioner|
|cachemisses9|Cachemissar (fragment 9)|Antal|Totalt||Inga dimensioner|
|getcommands9|Hämtar (fragment 9)|Antal|Totalt||Inga dimensioner|
|setcommands9|Uppsättningar (fragment 9)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond9|Åtgärder Per sekund (fragment 9)|Antal|Maximal||Inga dimensioner|
|evictedkeys9|Avlägsnade nycklar (fragment 9)|Antal|Totalt||Inga dimensioner|
|totalkeys9|Totalt antal nycklar (fragment 9)|Antal|Maximal||Inga dimensioner|
|expiredkeys9|Utgångna nycklar (fragment 9)|Antal|Totalt||Inga dimensioner|
|usedmemory9|Använt minne (fragment 9)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss9|Använt minne RSS (fragment 9)|Byte|Maximal||Inga dimensioner|
|serverLoad9|Belastningen på servern (fragment 9)|Procent|Maximal||Inga dimensioner|
|cacheWrite9|Cacheskrivning (fragment 9)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead9|Cacheläsning (fragment 9)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime9|CPU (fragment 9)|Procent|Maximal||Inga dimensioner|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer.|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik).|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik).|Inga dimensioner|
|Lästa byte på disk/sek|Diskläsning|BytesPerSecond|Medel|Genomsnittligt antal byte lästa från disk under övervakningsperioden.|Inga dimensioner|
|Skrivna byte på disk/sek|Diskskrivning|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivits till disk under övervakningsperioden.|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk.|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk.|Inga dimensioner|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer.|RoleInstanceId|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik).|RoleInstanceId|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik).|RoleInstanceId|
|Lästa byte på disk/sek|Diskläsning|BytesPerSecond|Medel|Genomsnittligt antal byte lästa från disk under övervakningsperioden.|RoleInstanceId|
|Skrivna byte på disk/sek|Diskskrivning|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivits till disk under övervakningsperioden.|RoleInstanceId|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Medel|Lästa IOPS på disk.|RoleInstanceId|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Medel|Skrivna IOPS på disk.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalCalls|Totala anrop|Antal|Totalt|Totalt antal anrop.|Inga dimensioner|
|SuccessfulCalls|Utförda anrop|Antal|Totalt|Antal utförda anrop.|Inga dimensioner|
|TotalErrors|Totalt antal fel|Antal|Totalt|Totalt antal anrop med felsvar (HTTP-svarskod 44x eller 5xx).|Inga dimensioner|
|BlockedCalls|Blockerade anrop|Antal|Totalt|Antalet anrop som överskrider gränsen för hastighet eller kvot.|Inga dimensioner|
|ServerErrors|Serverfel|Antal|Totalt|Antal anrop med internt fel i tjänsten (HTTP-svarskod 5xx).|Inga dimensioner|
|ClientErrors|Klientfel|Antal|Totalt|Antal anrop med fel på klientsidan (HTTP-svarskod 4xx).|Inga dimensioner|
|DataIn|Data in|Byte|Totalt|Storlek på inkommande data i byte.|Inga dimensioner|
|DataOut|Data ut|Byte|Totalt|Storlek på utgående data i byte.|Inga dimensioner|
|Svarstid|Svarstid|MilliSeconds|Medel|Svarstid i millisekunder.|Inga dimensioner|
|CharactersTranslated|Översatta tecken|Antal|Totalt|Totalt antal tecken i inkommande textbegäran.|Inga dimensioner|
|SpeechSessionDuration|Varaktighet för talsession|Sekunder|Totalt|Total varaktighet för talsession i sekunder.|Inga dimensioner|
|TotalTransactions|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner.|Inga dimensioner|
|TotalTokenCalls|Tokenanrop totalt|Antal|Totalt|Totalt antal tokenanrop.|Inga dimensioner|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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
|Lästa byte per disk/sek|Lästa byte på datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek lästa från en enskild disk under övervakningsperioden|SlotId|
|Skrivna byte per disk/sek|Skrivna byte på datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek skrivna till en enskild disk under övervakningsperioden|SlotId|
|Läsåtgärder per disk/sek|Läsåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under läsning från en enskild disk under övervakningsperioden|SlotId|
|Skrivåtgärder per disk/sek|Skrivåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under skrivning till en enskild disk under övervakningsperioden|SlotId|
|Ködjup per disk|Datadiskködjup (förhandsversion)|Antal|Medel|Datadiskködjup (eller kölängd)|SlotId|
|Lästa byte per operativsystemdisk/sek|Lästa byte per operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek lästa från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivna byte per operativsystemdisk/sek|Skrivna byte per operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek skrivna till en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Läsåtgärder per operativsystemdisk/sek|Läsåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under läsning från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivåtgärder per operativsystemdisk/sek|Skrivåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under skrivning till en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Ködjup per operativsystemdisk|Operativsystemdisks Ködjup (förhandsversion)|Antal|Medel|Operativsystemdisks ködjup (eller kölängd)|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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
|Lästa byte per disk/sek|Lästa byte på datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek lästa från en enskild disk under övervakningsperioden|SlotId|
|Skrivna byte per disk/sek|Skrivna byte på datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek skrivna till en enskild disk under övervakningsperioden|SlotId|
|Läsåtgärder per disk/sek|Läsåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under läsning från en enskild disk under övervakningsperioden|SlotId|
|Skrivåtgärder per disk/sek|Skrivåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under skrivning till en enskild disk under övervakningsperioden|SlotId|
|Ködjup per disk|Datadiskködjup (förhandsversion)|Antal|Medel|Datadiskködjup (eller kölängd)|SlotId|
|Lästa byte per operativsystemdisk/sek|OS-Disk-lästa byte/sek|CountPerSecond|Medel|Totalt antal byte/sek lästa från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivna byte per operativsystemdisk/sek|Skrivna byte per operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek skrivna till en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Läsåtgärder per operativsystemdisk/sek|Läsåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under läsning från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivåtgärder per operativsystemdisk/sek|Skrivåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under skrivning till en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Ködjup per operativsystemdisk|Operativsystemdisks Ködjup (förhandsversion)|Antal|Medel|Operativsystemdisks ködjup (eller kölängd)|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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
|Lästa byte per disk/sek|Lästa byte på datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek lästa från en enskild disk under övervakningsperioden|SlotId|
|Skrivna byte per disk/sek|Skrivna byte på datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek skrivna till en enskild disk under övervakningsperioden|SlotId|
|Läsåtgärder per disk/sek|Läsåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under läsning från en enskild disk under övervakningsperioden|SlotId|
|Skrivåtgärder per disk/sek|Skrivåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under skrivning till en enskild disk under övervakningsperioden|SlotId|
|Ködjup per disk|Datadiskködjup (förhandsversion)|Antal|Medel|Datadiskködjup (eller kölängd)|SlotId|
|Lästa byte per operativsystemdisk/sek|Lästa byte per operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek lästa från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivna byte per operativsystemdisk/sek|Skrivna byte per operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt antal byte/sek skrivna till en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Läsåtgärder per operativsystemdisk/sek|Läsåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under läsning från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivåtgärder per operativsystemdisk/sek|Skrivåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Medel|Totalt IOPS utfört under skrivning till en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Ködjup per operativsystemdisk|Operativsystemdisks Ködjup (förhandsversion)|Antal|Medel|Operativsystemdisks ködjup (eller kölängd)|Inga dimensioner|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|CPU-användning|Antal|Medel|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Minnesanvändning|Byte|Medel|Total minnesanvändning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Mottagna nätverksbyte per sekund|Byte|Medel|Nätverksbyte som mottagits per sekund.|Inga dimensioner|
|NetworkBytesTransmittedPerSecond|Överförda nätverksbyte per sekund|Byte|Medel|Nätverksbyte som överförts per sekund.|Inga dimensioner|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totala antalet tillgängliga cpu-kärnor i ett hanterat kluster|Antal|Totalt|Totala antalet tillgängliga cpu-kärnor i ett hanterat kluster|Inga dimensioner|
|kube_node_status_allocatable_memory_bytes|Totala mängden tillgängligt minne i ett hanterat kluster|Byte|Totalt|Totala mängden tillgängligt minne i ett hanterat kluster|Inga dimensioner|
|kube_pod_status_ready|Antalet poddar i färdigt tillstånd|Antal|Totalt|Antalet poddar i färdigt tillstånd|namnområdet, pod|
|kube_node_status_condition|Status för olika noden villkor|Antal|Totalt|Status för olika noden villkor|villkor, status, noden|
|kube_pod_status_phase|Antalet poddar av fas|Antal|Totalt|Antalet poddar av fas|fas, namnområde, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API-anrop|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationSuccessfulLines|Mappning av Import åtgärden lyckas rader|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationFailedLines|Det gick inte att mappa importåtgärden rader|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationTotalLines|Totalt antal rader för mappning av Import-åtgärden|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationRuntimeInSeconds|Mappning av Import åtgärden Runtime på några sekunder|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundProfileExportSucceeded|Utgående profil exporten lyckades|Antal|Totalt||Inga dimensioner|
|DCIOutboundProfileExportFailed|Utgående profil exporten misslyckades|Antal|Totalt||Inga dimensioner|
|DCIOutboundProfileExportDuration|Utgående profil Export varaktighet|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiExportSucceeded|Utgående Kpi-exporten lyckades|Antal|Totalt||Inga dimensioner|
|DCIOutboundKpiExportFailed|Utgående Kpi-exporten misslyckades|Antal|Totalt||Inga dimensioner|
|DCIOutboundKpiExportDuration|Varaktighet för utgående Kpi-Export|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiExportStarted|Utgående Kpi Export igång|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiRecordCount|Antal för utgående Kpi-poster|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundProfileExportCount|Antalet för Export av utgående profiler|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialProfileExportFailed|Utgående första profil exporten misslyckades|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialProfileExportSucceeded|Utgående första profil exporten lyckades|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialKpiExportFailed|Utgående inledande Kpi-exporten misslyckades|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialKpiExportSucceeded|Utgående inledande Kpi-exporten lyckades|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialProfileExportDurationInSeconds|Utgående första profil Export varaktighet i sekunder|Sekunder|Totalt||Inga dimensioner|
|AdlaJobForStandardKpiFailed|Adla-jobb för Standard-Kpi misslyckades i sekunder|Sekunder|Totalt||Inga dimensioner|
|AdlaJobForStandardKpiTimeOut|Adla-jobb för Standard Kpi-tidsgräns i sekunder|Sekunder|Totalt||Inga dimensioner|
|AdlaJobForStandardKpiCompleted|Adla-jobb för Standard-Kpi i sekunder|Sekunder|Totalt||Inga dimensioner|
|ImportASAValuesFailed|Importera ASA värden misslyckades antal|Antal|Totalt||Inga dimensioner|
|ImportASAValuesSucceeded|Importera ASA värden lyckades antal|Antal|Totalt||Inga dimensioner|
|DCIProfilesCount|Antal instanser för profil|Antal|Sista||Inga dimensioner|
|DCIInteractionsPerMonthCount|Interaktioner per månad antal|Antal|Sista||Inga dimensioner|
|DCIKpisCount|KPI-antal|Antal|Sista||Inga dimensioner|
|DCISegmentsCount|Antal segment|Antal|Sista||Inga dimensioner|
|DCIPredictiveMatchPoliciesCount|Förutsägande Matchningsantal|Antal|Sista||Inga dimensioner|
|DCIPredictionsCount|Antal för förutsägelse|Antal|Sista||Inga dimensioner|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FailedRuns|Misslyckade körningar|Antal|Totalt||pipelineName, activityName, windowEnd, windowStart|
|SuccessfulRuns|Lyckade körningar|Antal|Totalt||pipelineName, activityName, windowEnd, windowStart|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PipelineFailedRuns|Det gick inte mått för pipeline-körningar|Antal|Totalt||FailureType namn|
|PipelineSucceededRuns|Lyckades mått för pipeline-körningar|Antal|Totalt||FailureType namn|
|ActivityFailedRuns|Det gick inte aktivitetsmått för körningar|Antal|Totalt||ActivityType, PipelineName, FailureType, namn|
|ActivitySucceededRuns|Lyckades aktivitetsmått för körningar|Antal|Totalt||ActivityType, PipelineName, FailureType, namn|
|TriggerFailedRuns|Det gick inte utlösaren körs mått|Antal|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Lyckades utlösaren körs mått|Antal|Totalt||Namn, FailureType|
|IntegrationRuntimeCpuPercentage|Integration runtime CPU-användning|Procent|Medel||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Tillgängligt minne för integration runtime|Byte|Medel||IntegrationRuntimeName, NodeName|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobEndedSuccess|Lyckade jobb|Antal|Totalt|Antal genomförda jobb.|Inga dimensioner|
|JobEndedFailure|Misslyckade jobb|Antal|Totalt|Totalt antal misslyckade jobb.|Inga dimensioner|
|JobEndedCancelled|Avbrutna jobb|Antal|Totalt|Antal avbrutna jobb.|Inga dimensioner|
|JobAUEndedSuccess|Lyckad AU-tid|Sekunder|Totalt|Totalt antal AU-tid för genomförda jobb.|Inga dimensioner|
|JobAUEndedFailure|Misslyckade AU-tid|Sekunder|Totalt|Totalt antal AU-tid för misslyckade jobb.|Inga dimensioner|
|JobAUEndedCancelled|Avbrutna AU-tid|Sekunder|Totalt|Totalt antal AU-tid för avbrutna jobb.|Inga dimensioner|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Byte|Maximal|Totala mängden data som lagras i kontot.|Inga dimensioner|
|DataWritten|Skrivna data|Byte|Totalt|Totala mängden data som skrivs till kontot.|Inga dimensioner|
|DataRead|Lästa data|Byte|Totalt|Total mängd data läses från kontot.|Inga dimensioner|
|WriteRequests|Skrivförfrågningar|Antal|Totalt|Antal data diskskrivningsbegäranden till kontot.|Inga dimensioner|
|ReadRequests|Läsbegäranden|Antal|Totalt|Antal data diskläsningsbegäranden till kontot.|Inga dimensioner|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|memory_percent|Minne|Procent|Medel|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Procent|Medel|IO-procent|Inga dimensioner|
|storage_percent|Storage-procent|Procent|Medel|Storage-procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Gränsen för lagring|Byte|Medel|Gränsen för lagring|Inga dimensioner|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Procent|Medel|Procent för lagring av Server-loggen|Inga dimensioner|
|serverlog_storage_usage|Server logglagring som används|Byte|Medel|Server logglagring som används|Inga dimensioner|
|serverlog_storage_limit|Log storage gränsen|Byte|Medel|Log storage gränsen|Inga dimensioner|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|network_bytes_egress|Nätverk ut|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk in|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|memory_percent|Minne|Procent|Medel|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Procent|Medel|IO-procent|Inga dimensioner|
|storage_percent|Storage-procent|Procent|Medel|Storage-procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Gränsen för lagring|Byte|Medel|Gränsen för lagring|Inga dimensioner|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Procent|Medel|Procent för lagring av Server-loggen|Inga dimensioner|
|serverlog_storage_usage|Server logglagring som används|Byte|Medel|Server logglagring som används|Inga dimensioner|
|serverlog_storage_limit|Log storage gränsen|Byte|Medel|Log storage gränsen|Inga dimensioner|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|seconds_behind_master|Replikeringsfördröjning i sekunder|Antal|Medel|Replikeringsfördröjning i sekunder|Inga dimensioner|
|network_bytes_egress|Nätverk ut|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk in|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|memory_percent|Minne|Procent|Medel|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Procent|Medel|IO-procent|Inga dimensioner|
|storage_percent|Storage-procent|Procent|Medel|Storage-procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Gränsen för lagring|Byte|Medel|Gränsen för lagring|Inga dimensioner|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Procent|Medel|Procent för lagring av Server-loggen|Inga dimensioner|
|serverlog_storage_usage|Server logglagring som används|Byte|Medel|Server logglagring som används|Inga dimensioner|
|serverlog_storage_limit|Log storage gränsen|Byte|Medel|Log storage gränsen|Inga dimensioner|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|network_bytes_egress|Nätverk ut|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk in|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetri message send försök|Antal|Totalt|Antal meddelanden för enhet till moln-telemetri försökte skickas till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.ingress.success|Telemetrimeddelanden som skickas|Antal|Totalt|Antal telemetrimeddelanden från enheten till molnet har skickats till din IoT hub|Inga dimensioner|
|c2d.commands.egress.complete.success|Kommandon har slutförts|Antal|Totalt|Antal moln till enhet kommandon har slutförts av enheten|Inga dimensioner|
|c2d.commands.egress.abandon.success|Kommandon övergivna|Antal|Totalt|Antal moln till enhet kommandon som avbrutits av enheten|Inga dimensioner|
|c2d.commands.egress.reject.success|Kommandon som avvisats|Antal|Totalt|Antal moln till enhet kommandon som avvisats av enheten|Inga dimensioner|
|devices.totalDevices|Totalt antal enheter (inaktuell)|Antal|Totalt|Antalet enheter som registrerats till din IoT hub|Inga dimensioner|
|devices.connectedDevices.allProtocol|Anslutna enheter (inaktuell) |Antal|Totalt|Antalet enheter som är anslutna till din IoT hub|Inga dimensioner|
|d2c.telemetry.egress.success|Routning: telemetrimeddelanden som levereras|Antal|Totalt|Antal gånger som meddelanden levereras till alla slutpunkter med hjälp av IoT Hub routning. Om ett meddelande skickas till flera slutpunkter, ökar detta värde med ett för varje lyckad leverans. Om ett meddelande skickas till samma slutpunkt flera gånger, ökar detta värde med ett för varje lyckad leverans.|Inga dimensioner|
|d2c.telemetry.egress.dropped|Routning: telemetrimeddelanden som tas bort |Antal|Totalt|Antal gånger som meddelanden har tagits bort av IoT Hub routning på grund av döda slutpunkter. Det här värdet räknas inte meddelanden som levereras till återställningsplats väg som utelämnade meddelanden inte levereras det.|Inga dimensioner|
|d2c.telemetry.egress.orphaned|Routning: telemetrimeddelanden som överblivna |Antal|Totalt|Antal gånger som meddelanden frånkopplade genom IoT Hub routning eftersom de inte matchar några regler för routning (inklusive återställningsplats regeln). |Inga dimensioner|
|d2c.telemetry.egress.invalid|Routning: telemetrimeddelanden inkompatibla|Antal|Totalt|Antal gånger som IoT Hub routning inte kunde skicka meddelanden på grund av inkompatibilitet med slutpunkten. Det här värdet inkluderar inte återförsök.|Inga dimensioner|
|d2c.telemetry.egress.fallback|Routning: skicka meddelanden till återställningsplats|Antal|Totalt|Antal gånger som IoT Hub routning levererade meddelanden till slutpunkten som är associerade med återställningsplats vägen.|Inga dimensioner|
|d2c.endpoints.egress.eventHubs|Routning: skicka meddelanden till Event Hub|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.eventHubs|Routning: meddelande svarstiden för Event Hub|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och meddelandet inkommande till en Event Hub-slutpunkt.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusQueues|Routning: skicka meddelanden till Service Bus-kö|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till Service Bus-kö-slutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusQueues|Routning: meddelande svarstid för Service Bus-kö|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande inkommande i en slutpunkt för Service Bus-kö.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusTopics|Routning: skicka meddelanden till Service Bus-ämne|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till Service Bus-ämnet slutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusTopics|Routning: meddelande svarstid för Service Bus-ämne|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande inkommande i en slutpunkt för Service Bus-ämne.|Inga dimensioner|
|d2c.endpoints.egress.builtIn.events|Routning: skicka meddelanden till meddelanden/händelser|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till den inbyggda slutpunkten (meddelanden/händelser).|Inga dimensioner|
|d2c.endpoints.latency.builtIn.events|Routning: meddelande svarstiden för meddelanden/händelser|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande ingående i den inbyggda slutpunkten (meddelanden/händelser).|Inga dimensioner|
|d2c.endpoints.egress.storage|Routning: skicka meddelanden till storage|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till storage-slutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.storage|Routning: meddelande svarstid för lagring|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande inkommande till en slutpunkt för lagring.|Inga dimensioner|
|d2c.endpoints.egress.storage.bytes|Routning: data som levereras till storage|Byte|Totalt|Hur mycket data (byte) IoT Hub routning levereras till storage-slutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.storage.blobs|Routning: blobar som levereras till storage|Antal|Totalt|Antal gånger som IoT Hub routning levereras BLOB storage-slutpunkter.|Inga dimensioner|
|d2c.twin.read.success|Lyckad twin läser från enheter|Antal|Totalt|Antal för alla lyckade enhet-initierad tvilling läser.|Inga dimensioner|
|d2c.twin.read.failure|Det gick inte twin läsningar från enheter|Antal|Totalt|Det gick inte att enheten-initierad twin läsningar uppräkning av alla.|Inga dimensioner|
|d2c.twin.read.size|Svarsstorlek för tvilling läser från enheter|Byte|Medel|Genomsnitt, min och max för alla lyckade enhet-initierad twin läsningar.|Inga dimensioner|
|d2c.twin.update.success|Lyckad twin uppdateringar från enheter|Antal|Totalt|Antal uppdateringar för alla lyckade enhet-initierad enhetstvilling.|Inga dimensioner|
|d2c.twin.update.failure|Det gick inte uppdateringar för enhetstvilling från enheter|Antal|Totalt|Det gick inte att uppdateringar för enheten-initierad enhetstvilling uppräkning av alla.|Inga dimensioner|
|d2c.twin.update.size|Storleken på uppdateringar för enhetstvilling från enheter|Byte|Medel|Genomsnitt, min och maxstorleken på alla lyckade enhet-initierad twin uppdateringar.|Inga dimensioner|
|c2d.methods.success|Lyckad direkt metod anrop|Antal|Totalt|Antal anrop för alla lyckade direkt metod.|Inga dimensioner|
|c2d.methods.failure|Det gick inte direkt metod anrop|Antal|Totalt|Uppräkning av alla misslyckade anropen för direkt metod.|Inga dimensioner|
|c2d.methods.requestSize|Storlek på direkt metod anrop|Byte|Medel|Genomsnitt, min och max för alla lyckade begäranden för direkt metod.|Inga dimensioner|
|c2d.methods.responseSize|Svarsstorlek för direkt metod anrop|Byte|Medel|Genomsnitt, min och max för alla direkt metod lyckades.|Inga dimensioner|
|c2d.twin.read.success|Lyckad twin läser från backend-server|Antal|Totalt|Antal för alla lyckade tillbaka-end-initierad tvilling läser.|Inga dimensioner|
|c2d.twin.read.failure|Misslyckade twin läser från backend-server|Antal|Totalt|Det gick inte att tillbaka-end-initierad twin läsningar uppräkning av alla.|Inga dimensioner|
|c2d.twin.read.size|Svarsstorlek för tvilling läser från backend-server|Byte|Medel|Genomsnitt, min och max för alla lyckade tillbaka-end-initierad twin läsningar.|Inga dimensioner|
|c2d.twin.update.success|Lyckad twin uppdateringar från backend-server|Antal|Totalt|Antal uppdateringar för alla lyckade tillbaka-end-initierad enhetstvilling.|Inga dimensioner|
|c2d.twin.update.failure|Misslyckade twin uppdateringar från backend-server|Antal|Totalt|Uppräkning av alla misslyckades tillbaka-end-initierad twin uppdateringar.|Inga dimensioner|
|c2d.twin.update.size|Storleken på twin uppdateringar från backend-server|Byte|Medel|Genomsnitt, min och maxstorleken på alla lyckade tillbaka-end-initierad twin uppdateringar.|Inga dimensioner|
|twinQueries.success|Lyckad twin frågor|Antal|Totalt|Totalt antal alla lyckade twin frågor.|Inga dimensioner|
|twinQueries.failure|Misslyckade twin frågor|Antal|Totalt|Totalt antal alla misslyckade twin frågor.|Inga dimensioner|
|twinQueries.resultSize|Twin frågor storlek|Byte|Medel|Genomsnitt, min och max för resultatet storleken på alla lyckade twin frågor.|Inga dimensioner|
|jobs.createTwinUpdateJob.success|Lyckad som skapats för tvilling uppdatera jobb|Antal|Totalt|Totalt antal alla har skapats på twin uppdateringsjobb.|Inga dimensioner|
|jobs.createTwinUpdateJob.failure|Misslyckade som skapats för tvilling uppdatera jobb|Antal|Totalt|Uppräkning av alla misslyckad generering av twin uppdatera jobb.|Inga dimensioner|
|jobs.createDirectMethodJob.success|Lyckad skapande av metoden anrops-jobb|Antal|Totalt|Totalt antal alla har skapats på direkt metod anrop jobb.|Inga dimensioner|
|jobs.createDirectMethodJob.failure|Misslyckade skapande av metoden anrops-jobb|Antal|Totalt|Totalt antal alla misslyckad generering av direkt metod anrop jobb.|Inga dimensioner|
|jobs.listJobs.success|Utförda anrop lista jobb|Antal|Totalt|Antal lyckade anrop lista jobb.|Inga dimensioner|
|jobs.listJobs.failure|De misslyckade anropen lista jobb|Antal|Totalt|Antal misslyckade anrop lista jobb.|Inga dimensioner|
|jobs.cancelJob.success|Avbryta lyckade jobb|Antal|Totalt|Antal lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobs.cancelJob.failure|Avbryta misslyckade jobb|Antal|Totalt|Antal misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobs.queryJobs.success|Lyckad jobbfrågor|Antal|Totalt|Antal lyckade anrop till fråga jobb.|Inga dimensioner|
|jobs.queryJobs.failure|Misslyckade jobbfrågor|Antal|Totalt|Antal misslyckade anrop till fråga jobb.|Inga dimensioner|
|jobs.completed|Slutförda jobb|Antal|Totalt|Totalt antal alla slutförda jobb.|Inga dimensioner|
|jobs.Failed|Misslyckade jobb|Antal|Totalt|Totalt antal alla misslyckade jobb.|Inga dimensioner|
|d2c.telemetry.ingress.sendThrottle|Antalet begränsningsfel|Antal|Totalt|Begränsar antalet begränsningsfel på grund av enheten dataflöde|Inga dimensioner|
|dailyMessageQuotaUsed|Sammanlagt antal meddelanden som används|Antal|Medel|Antal Totalt antal meddelanden som används idag. Detta är en ackumulerad värde som nollställs på 00:00 UTC varje dag.|Inga dimensioner|
|deviceDataUsage|Totalt antal devicedata användning (inaktuell)|Byte|Totalt|Byte som överfördes till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|deviceDataUsageV2|Dataanvändning för totalt antal enheter (förhandsversion)|Byte|Totalt|Byte som överfördes till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|totalDeviceCount|Totalt antal enheter (förhandsversion)|Antal|Medel|Antalet enheter som registrerats till din IoT hub|Inga dimensioner|
|connectedDeviceCount|Anslutna enheter (förhandsversion)|Antal|Medel|Antalet enheter som är anslutna till din IoT hub|Inga dimensioner|
|Konfigurationer|Konfiguration av mått|Antal|Totalt|Mått för konfigurationsåtgärder|Inga dimensioner|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Appresursprovidern|Antal|Totalt|Antal försök registreringar för enhet|ProvisioningServiceName IotHubName, Status|
|DeviceAssignments|Tilldelade enheter|Antal|Totalt|Antalet enheter som är tilldelade till en IoT hub|ProvisioningServiceName IotHubName|
|AttestationAttempts|Attestering försök|Antal|Totalt|Antalet enheter attesteringar försökte|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

### <a name="request-metrics"></a>Begäran-mått

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner| Tid Precision| Äldre mått mappning | Användning |
|---|---|---|---|---|---| ---| ---| ---|
| TotalRequests |   Totalt antal begäranden| Antal   | Antal | Antalet begäranden som görs|  DatabaseName, CollectionName, Region, StatusCode|   Alla |   TotalRequests Http 2xx, Http 3xx, Http 400, Http 401, interna serverfel, tjänsten är inte tillgänglig, begränsad begäranden, genomsnittlig begäranden per sekund |    Används för att övervaka förfrågningar per statuskod, samling vid en minutersnivå. Genomsnittlig begäranden per sekund får använda antal aggregering på minut och dela med 60. |
| MetadataRequests |    Metadata Requests   |Antal| Antal   | Antalet metadataförfrågningar. Azure Cosmos DB upprätthåller samling för varje konto, där du kan räkna upp samlingar, databaser osv och konfigurationer, utan kostnad.    | DatabaseName, CollectionName, Region, StatusCode| Alla|  |Används för att övervaka begränsningar på grund av metadataförfrågningar.|
| MongoRequests |   Mongo-begäranden| Antal | Antal|  Antal Mongo-begäranden gjorda   | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Alla |Mongo Query Request Rate, Mongo Update Request Rate, Mongo Delete Request Rate, Mongo Insert Request Rate, Mongo Count Request Rate|   Används för att övervaka Mongo begäran fel, skriver du användningar per kommando. |


### <a name="request-unit-metrics"></a>Begär enhetsmått

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner| Tid Precision| Äldre mått mappning | Användning |
|---|---|---|---|---|---| ---| ---| ---|
| MongoRequestCharge|   Kostnad för mongo-begäran |  Antal   |Totalt  |Mongo-programbegäran som förbrukas|  DatabaseName, CollectionName, Region, CommandName, ErrorCode|   Alla |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Används för att övervaka Mongo resource ru: er på en minut.|
| TotalRequestUnits |Totalt antal enheter för programbegäran|   Antal|  Totalt|  Enheter som används för begäran| DatabaseName, CollectionName, Region, StatusCode    |Alla|   TotalRequestUnits|  Används för att övervaka Totalt antal RU användning vid en minutersnivå. Genomsnittlig RU som förbrukas per sekund får använda totala aggregering på minut och dela med 60.|
| ProvisionedThroughput |Etablerat dataflöde|    Antal|  Maximal |Etablerat dataflöde med samling Precision|  DatabaseName, samlingsnamn|   5M| |   Används för att övervaka etablerat dataflöde per samling.|

### <a name="storage-metrics"></a>Storage-mått

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner| Tid Precision| Äldre mått mappning | Användning |
|---|---|---|---|---|---| ---| ---| ---|
| AvailableStorage| Tillgängligt lagringsutrymme   |Byte| Totalt|  Totalt tillgängligt lagringsutrymme per 5 minuter kornighet per region|   DatabaseName, samlingsnamn, Region|   5M| Tillgängligt lagringsutrymme|   Används för att övervaka tillgängligt lagringsutrymme kapacitet (gäller endast för fast storage samlingar) lägsta Granulariteten bör vara 5 minuter.| 
| DataUsage |Dataanvändning |Byte| Totalt   |Total dataanvändning rapporteras med 5 minuter Precision per region|    DatabaseName, samlingsnamn, Region|   5M  |Datastorlek  | Används för att övervaka total dataanvändning vid insamling och region, bör lägsta Granulariteten vara 5 minuter.|
| IndexUsage|   Index användning|    Byte|  Totalt   |Total användning för Index rapporteras med 5 minuter Precision per region|    DatabaseName, samlingsnamn, Region|   5M| Indexstorlek| Används för att övervaka total dataanvändning vid insamling och region, bör lägsta Granulariteten vara 5 minuter. |
| DocumentQuota|    Dokumentet kvot| Byte|  Totalt|  Totala lagringskvoten rapporterade med 5 minuter Precision per region. Gäller för f| DatabaseName, samlingsnamn, Region|   5M  |Lagringskapacitet|  Används för att övervaka totala kvoten för insamling och region, bör lägsta Granulariteten vara 5 minuter.|
| DocumentCount|    Antal dokument| Antal   |Totalt  |Totaldocument antal som rapporterats med 5 minuter Precision per region|  DatabaseName, samlingsnamn, Region|   5M  |Antal dokument|Används för att övervaka dokumentantal i samlingen och region, bör lägsta Granulariteten vara 5 minuter.|

### <a name="latency-metrics"></a>Mått för datainmatningssvarstider

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner| Tid Precision| Användning |
|---|---|---|---|---|---| ---| ---| ---|
| ReplicationLatency    | Replikeringsfördröjning|  MilliSeconds|   Lägsta, högsta, genomsnitt | P99 replikeringsfördröjning mellan käll- och regioner för geo-aktiverat konto| SourceRegion, TargetRegion| Alla | Används för att övervaka P99 replikeringsfördröjning mellan de två regionerna för ett konto med geo-replikerade. |

### <a name="availability-metrics"></a>Mått på tillgänglighet

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner| Tid Precision| Äldre mått mappning | Användning |
|---|---|---|---|---|---| ---| ---| ---|
| ServiceAvailability   | Tjänsttillgänglighet| Procent |Lägsta, högsta|   Tillgänglighet för begäranden med en timme Precision|  |   1H  | Tjänsttillgänglighet  | Det här är procent av totalt antal skickade begäranden. En begäran anses vara misslyckades på grund av systemfel om statuskoden är 410, 500 eller 503 som används för att övervaka tillgängligheten för konto på timme kornighet. |

### <a name="cassandra-api-metrics"></a>Cassandra-API-mått

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner| Tid Precision| Användning |
|---|---|---|---|---|---| ---| ---| ---|
| CassandraRequests | Cassandra-begäranden |  Antal|  Antal|  Antal Cassandra API-begäranden gjorda|  DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType|   Alla| Används för att övervaka Cassandra begäranden med en minut precision. Genomsnittlig begäranden per sekund får använda antal aggregering på minut och dela med 60.|
| CassandraRequestCharges|  Avgifter för Cassandra-begäran| Antal|   Sum, Min, Max, genomsn.| Programbegäran som förbrukas av Cassandra API-begäranden|   DatabaseName, CollectionName, Region, OperationType, ResourceType|  Alla| Används för att övervaka ru: er som används per minut av ett Cassandra-API-konto.|
| CassandraConnectionClosures   | Cassandra-anslutning öppettider |Antal| Antal   |Antal stängda Cassandra-anslutningar|    ClosureReason, Region|  Alla | Används för att övervaka anslutningen mellan klienter och Azure Cosmos DB Cassandra-API.|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats till det här avsnittet|Inga dimensioner|
|PublishFailCount|Misslyckade händelser|Antal|Totalt|Det gick inte att publicera till det här avsnittet Totalt antal händelser|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationer för det här avsnittet|Inga dimensioner|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MatchedEventCount|Matchande händelser|Antal|Totalt|Totalt antal händelser som matchas mot den här händelseprenumerationen|Inga dimensioner|
|DeliveryAttemptFailCount|Händelser gick inte att leverera|Antal|Totalt|Det gick inte att leverera till den här händelseprenumerationen Totalt antal händelser|Fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser som levereras till den här händelseprenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|Bearbetningstid för mål|Millisekunder|Medel|Bearbetningstid för målet i millisekunder|Inga dimensioner|
|DroppedEventCount|Avbrutna händelser|Antal|Totalt|Totalt antal avbrutna händelser som matchar den här händelseprenumerationen|Inga dimensioner|
|DeadLetteredCount|Död bokstäver händelser|Antal|Totalt|Totalt antal död bokstäver händelser som matchar den här händelseprenumerationen|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats till det här avsnittet|Inga dimensioner|
|PublishFailCount|Misslyckade händelser|Antal|Totalt|Det gick inte att publicera till det här avsnittet Totalt antal händelser|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationer för det här avsnittet|Inga dimensioner|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade begäranden (förhandsversion)|Antal|Totalt|Lyckade begäranden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|ServerErrors|Serverfel. (Förhandsversion)|Antal|Totalt|Serverfel för Microsoft.EventHub. (Förhandsversion)|EntityName|
|UserErrors|Användarfel. (Förhandsversion)|Antal|Totalt|Användarfel för Microsoft.EventHub. (Förhandsversion)|EntityName|
|QuotaExceededErrors|Kvoten överskridits-fel. (Förhandsversion)|Antal|Totalt|Kvoten överskridits-fel för Microsoft.EventHub. (Förhandsversion)|EntityName|
|ThrottledRequests|Begränsade begäranden. (Förhandsversion)|Antal|Totalt|Begränsade begäranden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|IncomingRequests|Inkommande förfrågningar (förhandsversion)|Antal|Totalt|Inkommande begäranden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|IncomingMessages|Inkommande meddelanden (förhandsversion)|Antal|Totalt|Inkommande meddelanden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|OutgoingMessages|Utgående meddelanden (förhandsversion)|Antal|Totalt|Utgående meddelanden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|IncomingBytes|Inkommande byte. (Förhandsversion)|Byte|Totalt|Inkommande byte för Microsoft.EventHub. (Förhandsversion)|EntityName|
|OutgoingBytes|Utgående byte. (Förhandsversion)|Byte|Totalt|Utgående byte för Microsoft.EventHub. (Förhandsversion)|EntityName|
|ActiveConnections|ActiveConnections (förhandsversion)|Antal|Medel|Totalt antal aktiva anslutningar för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ConnectionsOpened|Öppnade anslutningar. (Förhandsversion)|Antal|Medel|Öppnade Microsoft.EventHub-anslutningar. (Förhandsversion)|EntityName|
|ConnectionsClosed|Stängda anslutningar. (Förhandsversion)|Antal|Medel|Stängda Microsoft.EventHub-anslutningar. (Förhandsversion)|EntityName|
|CaptureBacklog|Hämta kvarvarande uppgifter. (Förhandsversion)|Antal|Totalt|Hämta kvarvarande uppgifter för Microsoft.EventHub. (Förhandsversion)|EntityName|
|CapturedMessages|Hämtade meddelanden. (Förhandsversion)|Antal|Totalt|Hämtade meddelanden för Microsoft.EventHub. (Förhandsversion)|EntityName|
|CapturedBytes|Hämtade byte. (Förhandsversion)|Byte|Totalt|Hämtade byte för Microsoft.EventHub. (Förhandsversion)|EntityName|
|Storlek|Storlek (förhandsversion)|Byte|Medel|Storlek på en EventHub i byte. (Förhandsversion)|EntityName|
|INREQS|Inkommande förfrågningar|Antal|Totalt|Totat antal inkommande sändningsförfrågningar för ett namnområde|Inga dimensioner|
|SUCCREQ|Slutförda förfrågningar|Antal|Totalt|Totalt antal slutförda begäranden för ett namnområde|Inga dimensioner|
|FAILREQ|Misslyckade förfrågningar|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde|Inga dimensioner|
|SVRBSY|Upptagen server|Antal|Totalt|Totalt antal fel med upptagen server för ett namnområde|Inga dimensioner|
|INTERR|Interna serverfel|Antal|Totalt|Totalt antal interna serverfel för ett namnområde|Inga dimensioner|
|MISCERR|Övriga fel|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde|Inga dimensioner|
|INMSGS|Inkommande meddelanden (inaktuellt)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde. Det här mätvärdet är inaktuellt. Använd inkommande meddelanden istället|Inga dimensioner|
|EHINMSGS|Inkommande meddelanden|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde|Inga dimensioner|
|OUTMSGS|Utgående meddelanden (inaktuellt)|Antal|Totalt|Totalt antal utgående meddelanden för ett namnområde. Det här mätvärdet är inaktuellt. Använd utgående meddelanden istället|Inga dimensioner|
|EHOUTMSGS|Utgående meddelanden|Antal|Totalt|Totalt antal utgående meddelanden för ett namnområde|Inga dimensioner|
|EHINMBS|Inkommande byte (inaktuellt)|Byte|Totalt|Inkommande meddelandedataflöde för Händelsehubb för ett namnområde. Det här mätvärdet är inaktuellt. Använd inkommande byte istället|Inga dimensioner|
|EHINBYTES|Inkommande byte|Byte|Totalt|Dataflöde för inkommande meddelanden för händelsehubb för en namnområde|Inga dimensioner|
|EHOUTMBS|Utgående byte (inaktuellt)|Byte|Totalt|Utgående meddelandedataflöde för Händelsehubb för ett namnområde. Det här mätvärdet är inaktuellt. Använd utgående byte istället|Inga dimensioner|
|EHOUTBYTES|Utgående byte|Byte|Totalt|Dataflöde för utgående meddelanden för händelsehubb för ett namnområde|Inga dimensioner|
|EHABL|Arkivera kvarvarande meddelanden|Antal|Totalt|Arkiverade meddelanden för händelsehubb i kvarvarande uppgifter för ett namnområde|Inga dimensioner|
|EHAMSGS|Arkivera meddelanden|Antal|Totalt|Arkiverade meddelanden för händelsehubb i ett namnområde|Inga dimensioner|
|EHAMBS|Arkivera meddelandedataflöde|Byte|Totalt|Dataflöde för arkiverade meddelanden för händelsehubb i ett namnområde|Inga dimensioner|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade begäranden (förhandsversion)|Antal|Totalt|Lyckade begäranden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ServerErrors|Serverfel. (Förhandsversion)|Antal|Totalt|Serverfel för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|UserErrors|Användarfel. (Förhandsversion)|Antal|Totalt|Användarfel för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|QuotaExceededErrors|Kvoten överskridits-fel. (Förhandsversion)|Antal|Totalt|Kvoten överskridits-fel för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ThrottledRequests|Begränsade begäranden. (Förhandsversion)|Antal|Totalt|Begränsade begäranden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|IncomingRequests|Inkommande förfrågningar (förhandsversion)|Antal|Totalt|Inkommande begäranden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|IncomingMessages|Inkommande meddelanden (förhandsversion)|Antal|Totalt|Inkommande meddelanden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|OutgoingMessages|Utgående meddelanden (förhandsversion)|Antal|Totalt|Utgående meddelanden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|IncomingBytes|Inkommande byte. (Förhandsversion)|Byte|Totalt|Inkommande byte för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|OutgoingBytes|Utgående byte. (Förhandsversion)|Byte|Totalt|Utgående byte för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ActiveConnections|ActiveConnections (förhandsversion)|Antal|Medel|Totalt antal aktiva anslutningar för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ConnectionsOpened|Öppnade anslutningar. (Förhandsversion)|Antal|Medel|Öppnade Microsoft.EventHub-anslutningar. (Förhandsversion)|Inga dimensioner|
|ConnectionsClosed|Stängda anslutningar. (Förhandsversion)|Antal|Medel|Stängda Microsoft.EventHub-anslutningar. (Förhandsversion)|Inga dimensioner|
|CaptureBacklog|Hämta kvarvarande uppgifter. (Förhandsversion)|Antal|Totalt|Hämta kvarvarande uppgifter för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|CapturedMessages|Hämtade meddelanden. (Förhandsversion)|Antal|Totalt|Hämtade meddelanden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|CapturedBytes|Hämtade byte. (Förhandsversion)|Byte|Totalt|Hämtade byte för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|Processor|CPU (förhandsversion)|Procent|Maximal|Processoranvändning för Event Hub-klustret i procentandel|Roll|
|AvailableMemory|Tillgängligt minne (förhandsversion)|Antal|Maximal|Tillgängligt minne för Event Hub-klustret i byte|Roll|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway-begäranden|Antal|Totalt|Antal gateway-begäranden|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategoriserat Gateway-begäranden|Antal|Totalt|Antal gatewaybegäranden efter kategorier (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerat mätvärde|Antal|Medel|Värdet som beräknas av autoskalning vid körning|MetricTriggerSource|
|MetricThreshold|Tröskelvärde för mått|Antal|Medel|Det inställda tröskelvärdet när autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Observerad kapacitet|Antal|Medel|Den kapacitet som rapporteras till autoskalning vid körning.|Inga dimensioner|
|ScaleActionsInitiated|Skalningsåtgärder har initierats|Antal|Totalt|Riktningen på skalningsåtgärden.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components
(Förhandsversion)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|availabilityResults/varaktighet|Testlängd|MilliSeconds|Medel|Testlängd|availabilityResult/name, availabilityResult/plats, availabilityResult/lyckades|
|billingMeters/telemetryCount|Antal datapunkter|Antal|Totalt|Antalet datapunkter som skickas till den här Application Insights-resursen. Det här måttet bearbetas med en fördröjning på upp till två timmar.|billing/telemetryItemType, billing/telemetryItemSource|
|billingMeters/telemetrySize|Datapunktsvolym|Byte|Totalt|Mängden data som skickas till den här Application Insights-resursen. Det här måttet bearbetas med en fördröjning på upp till två timmar.|billing/telemetryItemType, billing/telemetryItemSource|
|browserTimings/networkDuration|Nätverksanslutningstid för sidhämtning|MilliSeconds|Medel|Tiden mellan användarförfrågning och användaranslutning. Innehåller DNS-sökning och transportanslutning anslutning.|Inga dimensioner|
|browserTimings/processingDuration|Klientbehandlingstid|MilliSeconds|Medel|Tiden mellan ta emot de sista byten av ett dokument till dess att DOM har lästs. Asynkrona begäranden kan fortfarande vara under bearbetning.|Inga dimensioner|
|browserTimings/receiveDuration|Tar emot svarstid|MilliSeconds|Medel|Tiden mellan de första och de sista byten, eller till frånkoppling.|Inga dimensioner|
|browserTimings/sendDuration|Tid för att skicka förfrågan|MilliSeconds|Medel|Tiden mellan nätverksanslutning och mottagande av de första byten.|Inga dimensioner|
|browserTimings/totalDuration|Sidhämtningstid för webbläsare|MilliSeconds|Medel|Tiden från användarförfrågan till dess att DOM, formatmallar, skript och bilder har lästs in.|Inga dimensioner|
|beroenden/antal|Beroendeanrop|Antal|Antal|Antal anrop gjorda av programmet till externa resurser|beroendetyp /, beroende/performanceBucket, beroende/lyckades, åtgärden/syntetiska, molnet/Rollinstans, molnet/roleName|
|beroenden/varaktighet|Beroendevaraktighet|MilliSeconds|Medel|Varaktigheten för anrop gjorda av programmet till externa resurser.|beroendetyp /, beroende/performanceBucket, beroende/lyckades, åtgärden/syntetiska, molnet/Rollinstans, molnet/roleName|
|beroenden/misslyckades|Beroendefel|Antal|Antal|Antal misslyckade beroendeanrop från programmet till externa resurser.|beroendetyp /, beroende/performanceBucket, åtgärden/syntetiska, molnet/Rollinstans, molnet/roleName|
|pageViews/count|Sidvisning|Antal|Antal|Antal sidvisningar.|operation/synthetic|
|pageViews/varaktighet|Inläsningstid för sidvisning|MilliSeconds|Medel|Inläsningstid för sidvisning|operation/synthetic|
|performanceCounters/requestExecutionTime|Körningstid för HTTP-begäranden|MilliSeconds|Medel|Körningstid för de senaste förfrågningarna.|molnet/Rollinstans|
|performanceCounters/requestsInQueue|HTTP-begäranden i programkön|Antal|Medel|Programfrågeköns längd|molnet/Rollinstans|
|performanceCounters/requestsPerSecond|Hastighet för HTTP-begäranden|CountPerSecond|Medel|Hastigheten per sekund för alla förfrågningar till programmet från ASP.NET.|molnet/Rollinstans|
|performanceCounters/exceptionsPerSecond|Antal undantag|CountPerSecond|Medel|Antalet hanterade och ohanterade undantag som rapporterats till Windows, inklusive .NET-undantag och ohanterade undantag som har konverterats till .NET-undantag.|molnet/Rollinstans|
|performanceCounters/processIOBytesPerSecond|IO-frekvens för process|BytesPerSecond|Medel|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|molnet/Rollinstans|
|performanceCounters/processCpuPercentage|Processoranvändning för process|Procent|Medel|Procentandelen av förfluten tid som alla processtrådar använde i processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet indikerar prestandan för w3wp-processen ensam.|molnet/Rollinstans|
|performanceCounters/processorCpuPercentage|Processortid|Procent|Medel|Den procentandel av tiden som processorn ägnat åt icke-inaktiva trådar|molnet/Rollinstans|
|performanceCounters/memoryAvailableBytes|Ledigt minne|Byte|Medel|Fysiskt minne som är omedelbart tillgängligt för att tilldelas en process eller för att användas av systemet.|molnet/Rollinstans|
|performanceCounters/processPrivateBytes|Privata byte för process|Byte|Medel|Minne som har tilldelats exklusivt för att övervaka programprocesser.|molnet/Rollinstans|
|begäranden/varaktighet|Serversvarstid |MilliSeconds|Medel|Tid från det att en HTTP-förfrågning mottagits till dess att svaret har skickats.|begäran/performanceBucket, begäran/Resultatkod, åtgärden/syntetiska, molnet/Rollinstans, begäran/lyckades, molnet/roleName|
|antal och tjänstbegäranden|Serverbegäranden|Antal|Antal|Antal slutförda HTTP-förfrågningar.|begäran/performanceBucket, begäran/Resultatkod, åtgärden/syntetiska, molnet/Rollinstans, begäran/lyckades, molnet/roleName|
|begäranden/misslyckades|Misslyckade förfrågningar|Antal|Antal|Antal HTTP-begäranden markeras som misslyckat. I de flesta fall är detta förfrågningar med svarskoden > = 400 och inte lika med 401.|begäran/performanceBucket, begäran/Resultatkod, åtgärden/syntetiska, molnet/Rollinstans, molnet/roleName|
|undantag/antal|Undantag|Antal|Totalt|Kombinerat antal undantagsfel utan felhantering.|molnet/roleName, molnet/Rollinstans, klient/typ|
|undantag/webbläsare|Webbläsarundantag|Antal|Totalt|Antal undantagsfel utan felhantering som har utlösts i webbläsaren.|Inga dimensioner|
|undantag/server|Serverundantag|Antal|Totalt|Antal undantagsfel utan felhantering som har utlösts i serverprogrammet.|molnet/roleName, molnet/Rollinstans|
|spårningar/antal|Antal spårningar|Antal|Totalt|Antal spårningsdokument|spårningen/severityLevel, åtgärden/syntetiska, molnet/roleName, molnet/Rollinstans|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Totalt antal Service API-träffar|Antal|Antal|Totalt antal Service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Total svarstid för Service API|Millisekunder|Medel|Total svarstid för Service API-begäranden|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Totalt antal Service API-resultat|Antal|Antal|Totalt antal Service API-resultat|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ClusterDataCapacityFactor|Cache-användning|Procent|Medel|På en nivå i omfånget för kluster|Inga dimensioner|
|QueryDuration|Frågevaraktighet|Millisekunder|Medel|Frågor varaktighet i sekunder|QueryStatus|
|IngestionsLoadFactor|Datainmatning användning|Procent|Medel|Förhållandet mellan används inmatning kortplatser i klustret|Inga dimensioner|
|IsEngineAnsweringQuery|Keep Alive|Antal|Medel|Kontrollera förstånd anger klustret svarar på frågor|Inga dimensioner|
|IngestCommandOriginalSizeInMb|Datainmatning volymen (i MB)|Antal|Totalt|Totala mängden insamlade data i klustret (i MB)|Inga dimensioner|
|EventAgeSeconds|Datainmatningssvarstid (i sekunder)|Sekunder|Medel|Datainmatning tid från källan (t.ex. meddelandet är EventHub) till klustret i sekunder|Inga dimensioner|
|EventReceivedFromEventHub|Händelser som bearbetas (för Event Hubs)|Antal|Totalt|Antalet händelser som bearbetas av klustret om mata in från Event Hub|Inga dimensioner|
|IngestionResult|Datainmatning resultat|Antal|Antal|Antal åtgärder för inmatning|IngestionResultDetails|
|EngineCPU|Processor|Procent|Medel|Processor på en nivå|Inga dimensioner|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Antal|Antal|Antal API-anrop|ApiCategory ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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
|BillingUsageNativeOperation|Faktureringsanvändning för körningar av interna åtgärder|Antal|Totalt|Det antal ursprungliga åtgärdskörningar som faktureras.|Inga dimensioner|
|BillingUsageStandardConnector|Faktureringsanvändning för körningar av standardanslutningsappar|Antal|Totalt|Det antal standardanslutningsappskörningar som faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Faktureringsanvändning för körningar av lagringsförbrukning|Antal|Totalt|Det antal lagringsförbrukningskörningar som faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Faktureringsanvändning för körningar av interna åtgärder|Antal|Totalt|Det antal ursprungliga åtgärdskörningar som faktureras.|Inga dimensioner|
|BillingUsageStandardConnector|Faktureringsanvändning för körningar av standardanslutningsappar|Antal|Totalt|Det antal standardanslutningsappskörningar som faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Faktureringsanvändning för körningar av lagringsförbrukning|Antal|Totalt|Det antal lagringsförbrukningskörningar som faktureras.|Inga dimensioner|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageOtherLatency|Genomsnittlig svarstid för andra|ms/op|Medel|Genomsnittlig andra svarstid (som inte är läsning eller skrivning) i millisekunder per åtgärd|Inga dimensioner|
|AverageReadLatency|Genomsnittlig läsfördröjning|ms/op|Medel|Genomsnittlig läsa svarstid i millisekunder per åtgärd|Inga dimensioner|
|AverageTotalLatency|Genomsnittlig svarstid för total|ms/op|Medel|Genomsnittlig total svarstid i millisekunder per åtgärd|Inga dimensioner|
|AverageWriteLatency|Genomsnittligt antal skrivåtgärder svarstid|ms/op|Medel|Genomsnittligt antal skrivåtgärder svarstid i millisekunder per åtgärd|Inga dimensioner|
|FilesystemOtherOps|Filsystem övriga åtgärder|OPS|Medel|Antal filsystem övriga åtgärder (som inte är läsning eller skrivning)|Inga dimensioner|
|FilesystemReadOps|Läsåtgärder för filsystem|OPS|Medel|Antal filsystem läsåtgärder|Inga dimensioner|
|FilesystemTotalOps|Totalt antal åtgärder för filsystem|OPS|Medel|Summan av alla filsystemsåtgärder|Inga dimensioner|
|FilesystemWriteOps|Filsystem skrivning ops|OPS|Medel|Antalet skrivåtgärder för filsystem|Inga dimensioner|
|IoBytesPerOtherOps|I/o-byte per övriga åtgärder|byte/op|Medel|Många av i/ut byte per övriga åtgärder (som inte är läsning eller skrivning)|Inga dimensioner|
|IoBytesPerReadOps|I/o-byte per Läs ops|byte/op|Medel|Antalet byte per Läsåtgärden In/ut|Inga dimensioner|
|IoBytesPerTotalOps|I/o-byte per op över alla åtgärder|byte/op|Medel|Summan av alla In/ut byte åtgärden|Inga dimensioner|
|IoBytesPerWriteOps|I/o-byte per skrivning ops|byte/op|Medel|Många av i/ut byte per skrivåtgärd|Inga dimensioner|
|OtherIops|Andra iops|åtgärder per sekund|Medel|Andra In/ut åtgärd per sekund|Inga dimensioner|
|OtherThroughput|Andra dataflöde|Mbit/s|Medel|Andra dataflöde (som inte är läsning eller skrivning) i megabyte per sekund|Inga dimensioner|
|ReadIops|Läsa iops|åtgärder per sekund|Medel|Läsa In/ut-åtgärder per sekund|Inga dimensioner|
|ReadThroughput|Läs dataflöde|Mbit/s|Medel|Läsa dataflöde i megabyte per sekund|Inga dimensioner|
|TotalIops|Totalt antal iops|åtgärder per sekund|Medel|Summan av alla In/ut-åtgärder per sekund|Inga dimensioner|
|TotalThroughput|Totalt dataflöde|Mbit/s|Medel|Summan av alla dataflöde i megabyte per sekund|Inga dimensioner|
|VolumeAllocatedSize|Volymen som allokerats storlek|byte|Medel|Allokerade storleken på volymen (inte den faktiska används byte)|Inga dimensioner|
|VolumeLogicalSize|Logisk storlek|byte|Medel|Logiska storleken på volymen (används byte)|Inga dimensioner|
|VolumeSnapshotSize|Volymstorlek för ögonblicksbild|byte|Medel|Storleken på alla ögonblicksbilder i volym|Inga dimensioner|
|WriteIops|Skriva iops|åtgärder per sekund|Medel|Skriv In/ut-åtgärder per sekund|Inga dimensioner|
|WriteThroughput|Skriva dataflöde|Mbit/s|Medel|Skriva dataflöde i megabyte per sekund|Inga dimensioner|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volymstorlek för poolen som allokerats|byte|Medel|Allokerade storleken på poolen (inte den faktiska används byte)|Inga dimensioner|
|VolumePoolAllocatedUsed|Volymen poolen allokeras används|byte|Medel|Allokerade storleken på poolen som används|Inga dimensioner|
|VolumePoolTotalLogicalSize|Volymen Totalt antal logiska poolstorlek|byte|Medel|Summan av den logiska storleken på alla volymer som hör till poolen|Inga dimensioner|
|VolumePoolTotalSnapshotSize|Volymstorlek pool totala ögonblicksbild|byte|Medel|Summan av alla ögonblicksbilder i pool|Inga dimensioner|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesSentRate|Skickade byte|Antal|Totalt|Antalet byte som skickas nätverksgränssnittet|Inga dimensioner|
|BytesReceivedRate|Mottagna byte|Antal|Totalt|Antalet byte nätverksgränssnittet som tagits emot|Inga dimensioner|
|PacketsSentRate|Paket som skickats|Antal|Totalt|Antal paket nätverksgränssnittet skickas|Inga dimensioner|
|PacketsReceivedRate|Mottagna paket|Antal|Totalt|Antal paket nätverksgränssnittet som tagits emot|Inga dimensioner|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|Tillgänglighet för data-sökväg|Antal|Medel|Genomsnittlig belastningsutjämnaren data sökväg tillgänglighet per varaktighet|FrontendIPAddress, FrontendPort|
|DipAvailability|Hälsostatus för avsökning|Antal|Medel|Genomsnittlig belastningsutjämnaren avsökningen hälsostatus per varaktighet|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Antal byte|Antal|Totalt|Sammanlagt antal byte som skickas inom tidsperioden|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Antal paket|Antal|Totalt|Totalt antal paket som skickas inom tidsperioden|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN-antal|Antal|Totalt|Totalt antal SYN-paket som skickas inom tidsperioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Antal för SNAT-anslutningar|Antal|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tidsperioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allokerade SNAT portar (förhandsversion)|Antal|Totalt|Totalt antal SNAT-portar som allokerats inom tidsperioden|FrontendIPAddress, BackendIPAddress, protokolltyp|
|UsedSnatPorts|Använda SNAT portar (förhandsversion)|Antal|Totalt|Totalt antal SNAT-portar som används inom tidsperioden|FrontendIPAddress, BackendIPAddress, protokolltyp|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Fråga volym|Antal|Totalt|Antal frågor som hanteras för en DNS-zon|Inga dimensioner|
|RecordSetCount|Antal för uppsättningen av poster|Antal|Maximal|Antal postuppsättningar i en DNS-zon|Inga dimensioner|
|RecordSetCapacityUtilization|Kapacitetsutnyttjande för uppsättningen av poster|Procent|Maximal|Procent av postuppsättning kapacitet som används av en DNS-zon|Inga dimensioner|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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
|TCPBytesInDDoS|Inkommande TCP-byte DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte DDoS|Inga dimensioner|
|TCPBytesDroppedDDoS|Inkommande TCP-byte bort DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte bort DDoS|Inga dimensioner|
|TCPBytesForwardedDDoS|Inkommande TCP-byte vidarebefordras DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte vidarebefordras DDoS|Inga dimensioner|
|UDPBytesInDDoS|Inkommande byte UDP DDoS|BytesPerSecond|Maximal|Inkommande byte UDP DDoS|Inga dimensioner|
|UDPBytesDroppedDDoS|Inkommande byte för UDP bort DDoS|BytesPerSecond|Maximal|Inkommande byte för UDP bort DDoS|Inga dimensioner|
|UDPBytesForwardedDDoS|Inkommande byte för UDP vidarebefordras DDoS|BytesPerSecond|Maximal|Inkommande byte för UDP vidarebefordras DDoS|Inga dimensioner|
|IfUnderDDoSAttack|Under DDoS angrepp eller inte|Antal|Maximal|Under DDoS angrepp eller inte|Inga dimensioner|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-åtgärd|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-åtgärd|Inga dimensioner|
|DDoSTriggerUDPPackets|Inkommande UDP-paket för att utlösa DDoS-åtgärd|CountPerSecond|Maximal|Inkommande UDP-paket för att utlösa DDoS-åtgärd|Inga dimensioner|
|DDoSTriggerSYNPackets|Inkommande SYN-paket för att utlösa DDoS-åtgärd|CountPerSecond|Maximal|Inkommande SYN-paket för att utlösa DDoS-åtgärd|Inga dimensioner|
|VipAvailability|Tillgänglighet för data-sökväg|Antal|Medel|Genomsnittlig tillgänglighet för IP-adress per varaktighet|Port|
|ByteCount|Antal byte|Antal|Totalt|Sammanlagt antal byte som skickas inom tidsperioden|Port, riktning|
|PacketCount|Antal paket|Antal|Totalt|Totalt antal paket som skickas inom tidsperioden|Port, riktning|
|SynCount|SYN-antal|Antal|Totalt|Totalt antal SYN-paket som skickas inom tidsperioden|Port, riktning|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Totalt|Antalet byte per sekund som Application Gateway har hämtats|Inga dimensioner|
|UnhealthyHostCount|Defekt antal värdar per Backendpool|Antal|Medel|Antalet felaktiga backend-värdar|BackendSettingsPool|
|HealthyHostCount|Felfri värd-antal|Antal|Medel|Antalet felfri backend-värdar|BackendSettingsPool|
|TotalRequests|Totalt antal begäranden|Antal|Totalt|Antal lyckade begäranden som Application Gateway har hämtats|BackendSettingsPool|
|FailedRequests|Misslyckade förfrågningar|Antal|Totalt|Antal misslyckade begäranden som Application Gateway har hämtats|BackendSettingsPool|
|ResponseStatus|Svarsstatus|Antal|Totalt|HTTP-svarsstatus som returneras av Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar som upprättats via Application Gateway|Inga dimensioner|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S bandbredd|BytesPerSecond|Medel|Genomsnittlig plats-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SBandwidth|P2S bandbredden|BytesPerSecond|Medel|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Antal för P2S-anslutningar|Antal|Maximal|Antal för punkt-till-plats-anslutning för en gateway|Protokoll|
|TunnelAverageBandwidth|Tunnel bandbredd|BytesPerSecond|Medel|Genomsnittlig bandbredd en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tunnel utgående byte|Byte|Totalt|Utgående byte en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tunnel inkommande byte|Byte|Totalt|Inkommande byte en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel utgående paket|Antal|Totalt|Utgående paket antal en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel inkommande paket|Antal|Totalt|Inkommande paket antal en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel utgående TS matchningsfel paket släpp|Antal|Totalt|Utgående paket släpp ökas från trafik väljare matchningsfel en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel ingående TS matchningsfel paket släpp|Antal|Totalt|Inkommande paket släpp ökas från trafik väljare matchningsfel en tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS ingressing Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS egressing Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS ingressing Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS egressing Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS ingressing Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS egressing Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Frågor efter slutpunkten returnerade|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt som returnerades i angiven tidsperiod|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Slutpunktsstatus av slutpunkten|Antal|Maximal|1 om en slutpunkt avsökningen status är ”aktiverad”, annars 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Avsökningar misslyckades|Procent|Medel|% av tiden har anslutning övervakningsavsökningar misslyckades|Inga dimensioner|
|AverageRoundtripMs|Genomsn. Fram och åter tid (ms)|MilliSeconds|Medel|Genomsnittlig nätverk fram och åter tid (ms) för anslutning övervakningsavsökningar som skickas mellan källa och mål|Inga dimensioner|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RequestCount|Antal begäranden|Antal|Totalt|Antalet klientbegäranden som hanteras av HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Begärandestorlek|Byte|Totalt|Antalet byte som skickas när begäranden från klienter till HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Svarsstorlek|Byte|Totalt|Antalet byte som skickas som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Antal förfrågningar för serverdel|Antal|Totalt|Antalet begäranden som skickas från HTTP/S-proxy till serverdelar|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Svarstid för backend-begäran|MilliSeconds|Medel|Tid som beräknas från när begäran har skickats av HTTP/S-proxyn till serverdelen tills HTTP/S-proxyn fick den sista byten av svaret från serverdelen|Serverdel|
|TotalLatency|Total svarstid|MilliSeconds|Medel|Tid som beräknas från när klientbegäran togs emot av HTTP/S-proxyn tills klienten godkänt den sista byten svar från HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Serverdelens hälsotillstånd procent|Procent|Medel|Procentandelen av lyckade hälsorapporter avsökningar från HTTP/S-proxy till serverdelar|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall begäran antal|Antal|Totalt|Antalet klientbegäranden bearbetas av Brandvägg för webbaserade program|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|registration.all|Registreringsåtgärder|Antal|Totalt|Totalt antal genomförda registreringsåtgärder (skapa, uppdatera, fråga och ta bort). |Inga dimensioner|
|registration.create|Registreringsskaparåtgärder|Antal|Totalt|Totalt antal registreringar som skapats.|Inga dimensioner|
|registration.update|Registreringsuppdateringsåtgärder|Antal|Totalt|Totalt antal genomförda registreringsuppdateringar.|Inga dimensioner|
|registration.get|Registreringsläsningsåtgärder|Antal|Totalt|Totalt antal utförda registreringsfrågor.|Inga dimensioner|
|registration.delete|Registreringsborttagningsåtgärder|Antal|Totalt|Totalt antal registreringar som tagits bort.|Inga dimensioner|
|incoming|Inkommande meddelanden|Antal|Totalt|Totalt antal API-anrop som skickats. |Inga dimensioner|
|incoming.scheduled|Schemalagt push-meddelande har skickats|Antal|Totalt|Schemalagt push-meddelande har avbrutits|Inga dimensioner|
|incoming.scheduled.cancel|Schemalagt push-meddelande har avbrutits|Antal|Totalt|Schemalagt push-meddelande har avbrutits|Inga dimensioner|
|Scheduled.Pending|Schemalagda meddelanden som väntar|Antal|Totalt|Schemalagda meddelanden som väntar|Inga dimensioner|
|installation.all|Installationshanteringsåtgärder|Antal|Totalt|Installationshanteringsåtgärder|Inga dimensioner|
|installation.get|Åtgärder för att hämta installation|Antal|Totalt|Åtgärder för att hämta installation|Inga dimensioner|
|installation.upsert|Skapa eller uppdatera installationsåtgärder|Antal|Totalt|Skapa eller uppdatera installationsåtgärder|Inga dimensioner|
|installation.patch|Åtgärder för att korrigera installation|Antal|Totalt|Åtgärder för att korrigera installation|Inga dimensioner|
|installation.delete|Åtgärder för att ta bort installation|Antal|Totalt|Åtgärder för att ta bort installation|Inga dimensioner|
|outgoing.allpns.success|Levererade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.allpns.invalidpayload|Nyttolastfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS returnerade ett nyttolastfel.|Inga dimensioner|
|outgoing.allpns.pnserror|Externa meddelandesystemfel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med PNS (inte autentiseringsproblem).|Inga dimensioner|
|outgoing.allpns.channelerror|Kanalfel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av att kanalen var ogiltig, inte kopplad till rätt app, begränsad eller hade upphört att gälla.|Inga dimensioner|
|outgoing.allpns.badorexpiredchannel|Felaktig eller utgången kanal|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av utgången eller ogiltig kanal/token/registrationId i registreringen.|Inga dimensioner|
|outgoing.wns.success|WNS – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.wns.invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade. (Windows Live kan inte identifiera autentiseringsuppgifterna).|Inga dimensioner|
|outgoing.wns.badchannel|WNS – felaktig kanal|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: (404 hittades inte).|Inga dimensioner|
|outgoing.wns.expiredchannel|WNS – utgången kanal|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 sidan finns inte).|Inga dimensioner|
|outgoing.wns.throttled|WNS – begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som inte eftersom WNS begränsar den här appen (WNS-status: 406 Ogiltigt format).|Inga dimensioner|
|outgoing.wns.tokenproviderunreachable|WNS-auktoriseringsfel (kan inte nås)|Antal|Totalt|Windows Live kan inte nås.|Inga dimensioner|
|outgoing.wns.invalidtoken|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Token som angavs i WNS är inte giltig (WNS-status: 401 Ej behörig).|Inga dimensioner|
|outgoing.wns.wrongtoken|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Token som angavs i WNS är giltig men för ett annat program (WNS-status: 403 Forbidden). Detta kan inträffa om ChannelURI i registreringen är associerad med en annan app. Kontrollera att klientappen är associerad med samma app vars autentiseringsuppgifter finns i meddelandehubben.|Inga dimensioner|
|outgoing.wns.invalidnotificationformat|WNS – ogiltigt meddelandeformat|Antal|Totalt|Formatet för meddelandet är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nyttolaster.|Inga dimensioner|
|outgoing.wns.invalidnotificationsize|WNS – ogiltig meddelandestorlek|Antal|Totalt|Meddelandets nyttolast är för stor (WNS-status: 413).|Inga dimensioner|
|outgoing.wns.channelthrottled|WNS – begränsad kanal|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svarshuvud: X-WNS-NotificationStatus:channelThrottled).|Inga dimensioner|
|outgoing.wns.channeldisconnected|WNS – frånkopplad kanal|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svarshuvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|outgoing.wns.dropped|WNS – utelämnade meddelanden|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: utelämnat men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|outgoing.wns.pnserror|WNS-fel|Antal|Totalt|Meddelandet levererades inte på grund av kommunikationsproblem med WNS.|Inga dimensioner|
|outgoing.wns.authenticationerror|WNS-auktoriseringsfel|Antal|Totalt|Meddelanden som inte levererats på grund av kommunikationsproblem med ogiltiga Windows Live-autentiseringsuppgifter eller fel token.|Inga dimensioner|
|outgoing.apns.success|APNS – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.apns.invalidcredentials|APNs-auktoriseringsfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.apns.badchannel|APNS – felaktig kanal|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom token är ogiltigt (APNS-statuskod: 8).|Inga dimensioner|
|outgoing.apns.expiredchannel|APNS – utgången kanal|Antal|Totalt|Antal token som ogiltigförklarades av APNs-feedbackkanalen.|Inga dimensioner|
|outgoing.apns.invalidnotificationsize|APNS – ogiltig meddelandestorlek|Antal|Totalt|Antal push-meddelanden som inte eftersom nyttolasten var för stor (APNS-statuskod: 7).|Inga dimensioner|
|outgoing.apns.pnserror|APNS-fel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med APNs.|Inga dimensioner|
|outgoing.gcm.success|GCM – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.gcm.invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.gcm.badchannel|GCM – felaktig kanal|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen inte kändes igen (GCM-resultat: Ogiltig registrering).|Inga dimensioner|
|outgoing.gcm.expiredchannel|GCM – utgången kanal|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|Outgoing.GCM.throttled|GCM – begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom GCM – begränsade den här appen (GCM-statuskod: 501-599 eller resultat: inte tillgänglig).|Inga dimensioner|
|outgoing.gcm.invalidnotificationformat|GCM – ogiltigt meddelandeformat|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nyttolasten inte har formaterats korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|outgoing.gcm.invalidnotificationsize|GCM – ogiltig meddelandestorlek|Antal|Totalt|Antal push-meddelanden som inte eftersom nyttolasten var för stor (GCM-resultat: MessageTooBig).|Inga dimensioner|
|outgoing.gcm.wrongchannel|GCM – fel kanal|Antal|Totalt|Antal push-meddelanden misslyckades på grund av att registrationId i registreringen inte är kopplad till den aktuella appen (GCM-resultat: InvalidPackageName).|Inga dimensioner|
|outgoing.gcm.pnserror|GCM-fel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med GCM.|Inga dimensioner|
|outgoing.gcm.authenticationerror|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden misslyckades eftersom PNS inte godkände de angivna autentiseringsuppgifterna, autentiseringsuppgifterna var blockerade eller SenderId var felkonfigurerat i appen (GCM-resultat: MismatchedSenderId).|Inga dimensioner|
|outgoing.mpns.success|MPNS – lyckade meddelanden|Antal|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.mpns.invalidcredentials|MPNS – ogiltiga autentiseringsuppgifter|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.mpns.badchannel|MPNS – felaktig kanal|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: (404 hittades inte).|Inga dimensioner|
|outgoing.mpns.throttled|MPNS – begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som inte eftersom MPNS begränsar den här appen (WNS MPNS: 406 Ogiltigt format).|Inga dimensioner|
|outgoing.mpns.invalidnotificationformat|MPNS – ogiltigt meddelandeformat|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom nyttolasten för meddelanden var för stor.|Inga dimensioner|
|outgoing.mpns.channeldisconnected|MPNS – frånkopplad kanal|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen kopplades (MPNS-status: 412 som gick inte att hitta IT-avdelning).|Inga dimensioner|
|outgoing.mpns.dropped|MPNS – utelämnade meddelanden|Antal|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svarshuvud: X-NotificationStatus: QueueFull eller ignorerade).|Inga dimensioner|
|outgoing.mpns.pnserror|MPNS-fel|Antal|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med MPNS.|Inga dimensioner|
|outgoing.mpns.authenticationerror|MPNS-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|notificationhub.pushes|Alla utgående meddelanden|Antal|Totalt|Alla utgående meddelanden i meddelandehubben|Inga dimensioner|
|incoming.all.requests|Alla inkommande förfrågningar|Antal|Totalt|Totalt antal inkommande förfrågningar för en meddelandehubb|Inga dimensioner|
|incoming.all.failedrequests|Alla inkommande misslyckade förfrågningar|Antal|Totalt|Totalt antal inkommande misslyckade förfrågningar för en meddelandehubb|Inga dimensioner|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_ % Ledigai|% Ledigai-noder|Antal|Medel|Average_ % Ledigai|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % ledigt utrymme|Ledigt utrymme i procent|Antal|Medel|Average_ % ledigt utrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % Användai-noder|% Användai-noder|Antal|Medel|Average_ % Användai-noder|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Använt utrymme i procent för Average_|Använt utrymme i procent|Antal|Medel|Använt utrymme i procent för Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/sek|Disk – lästa byte/sek|Antal|Medel|Average_Disk lästa byte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Diskläsningar/sek|Diskläsningar/sek|Antal|Medel|Average_Disk Diskläsningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk disköverföringar/sek|Disköverföringar/sek|Antal|Medel|Average_Disk disköverföringar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/sek|Disk – skrivna byte/sek|Antal|Medel|Average_Disk skrivna byte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Diskskrivningar/sek|Diskskrivningar/sek|Antal|Medel|Average_Disk Diskskrivningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Ledigt utrymme i MB|Antal|Medel|Average_Free megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk byte/sek|Logisk Disk byte/sek|Antal|Medel|Average_Logical Disk byte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ tillgängligt minne i procent|Tillgängligt minne i procent|Antal|Medel|Average_ tillgängligt minne i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Tillgängligt växlingsutrymme i Average_ %|Tillgängligt växlingsutrymme i procent|Antal|Medel|Tillgängligt växlingsutrymme i Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Använt minne i procent för Average_|Använt minne i procent|Antal|Medel|Använt minne i procent för Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % använt växlingsutrymme|Använt växlingsutrymme i procent|Antal|Medel|Average_ % använt växlingsutrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available minne i megabyte|Tillgängligt minne i megabyte|Antal|Medel|Average_Available minne i megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte växlingsutrymme|Tillgängliga megabyte växlingsutrymme|Antal|Medel|Average_Available megabyte växlingsutrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Diskläsningar/sek|Sidläsningar/sek|Antal|Medel|Average_Page Diskläsningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Diskskrivningar/sek|Sidskrivningar/sek|Antal|Medel|Average_Page Diskskrivningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sek|Sidor/sek|Antal|Medel|Average_Pages/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlingsutrymme i procent|Använt växlingsutrymme i megabyte|Antal|Medel|Average_Used megabyte växlingsutrymme i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i megabyte|Använt minne Mbyte|Antal|Medel|Average_Used minne i megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Överförda Average_Total byte|Totalt antal överförda byte|Antal|Medel|Överförda Average_Total byte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte som tagits emot|Totalt antal byte mottaget|Antal|Medel|Average_Total byte som tagits emot|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Totalt antal byte|Antal|Medel|Average_Total byte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total antal skickade paket|Totalt antal skickade paket|Antal|Medel|Average_Total antal skickade paket|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total paket som tas emot|Totalt antal mottagna paket|Antal|Medel|Average_Total paket som tas emot|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx fel|Totalt antal Rx-fel|Antal|Medel|Average_Total Rx fel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx-fel|Totalt antal Tx-fel|Antal|Medel|Average_Total Tx-fel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Totalt antal kollisioner|Antal|Medel|Average_Total kollisioner|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskläsning|Genomsn. S/diskläsning|Antal|Medel|Average_Avg. S/diskläsning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/disköverföring|Genomsn. S/disköverföring|Antal|Medel|Average_Avg. S/disköverföring|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskskrivning|Genomsn. S/diskskrivning|Antal|Medel|Average_Avg. S/diskskrivning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk byte/sek|Fysisk Disk byte/sek|Antal|Medel|Average_Physical Disk byte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Privilegierad tid i Average_Pct|PCT privilegierad tid|Antal|Medel|Privilegierad tid i Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användartid|PCT användartid|Antal|Medel|Average_Pct användartid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne kB|Använt minne kB|Antal|Medel|Average_Used minne kB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Virtuella delat minne|Antal|Medel|Average_Virtual delat minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % DPC-tid|% DPC-tid|Antal|Medel|Average_ % DPC-tid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ inaktivitetstid i procent|Inaktivitetstid i procent|Antal|Medel|Average_ inaktivitetstid i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ avbrottstid i procent|Avbrottstid i procent|Antal|Medel|Average_ avbrottstid i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % / o-väntetid|Väntetid i procent i/o|Antal|Medel|Average_ % / o-väntetid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ Nice Time|Nice Time|Antal|Medel|Average_ Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Privilegierad tid i procent för Average_|Privilegierad tid i procent|Antal|Medel|Privilegierad tid i procent för Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % processortid|Tid i procent för processor|Antal|Medel|Average_ % processortid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ Användartid i procent|Användartid i procent|Antal|Medel|Average_ Användartid i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Ledigt fysiskt minne|Antal|Medel|Average_Free fysiskt minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Ledigt utrymme i växlingsfiler|Antal|Medel|Average_Free utrymme i växlingsfiler|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Ledigt virtuellt minne|Antal|Medel|Average_Free virtuellt minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processer|Antal|Medel|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagrad i växlingsfiler|Storlek lagrad i växlingsfiler|Antal|Medel|Average_Size lagrad i växlingsfiler|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Drifttid|Antal|Medel|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Användare|Antal|Medel|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskläsning|Genomsn. S/diskläsning|Antal|Medel|Average_Avg. S/diskläsning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskskrivning|Genomsn. S/diskskrivning|Antal|Medel|Average_Avg. S/diskskrivning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Aktuell diskkölängd|Antal|Medel|Average_Current diskkölängd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Diskläsningar/sek|Diskläsningar/sek|Antal|Medel|Average_Disk Diskläsningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk disköverföringar/sek|Disköverföringar/sek|Antal|Medel|Average_Disk disköverföringar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Diskskrivningar/sek|Diskskrivningar/sek|Antal|Medel|Average_Disk Diskskrivningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Ledigt utrymme i MB|Antal|Medel|Average_Free megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % ledigt utrymme|Ledigt utrymme i procent|Antal|Medel|Average_ % ledigt utrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte|Tillgängliga megabyte|Antal|Medel|Average_Available megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % allokerade byte som används|% Allokerade byte som används|Antal|Medel|Average_ % allokerade byte som används|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes per sekund|Mottagna byte/sek|Antal|Medel|Average_Bytes per sekund|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes/sek|Skickade byte/sek|Antal|Medel|Average_Bytes/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/sek|Byte totalt/sek|Antal|Medel|Average_Bytes totalt/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % processortid|Tid i procent för processor|Antal|Medel|Average_ % processortid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Kölängd|Kölängd för processor|Antal|Medel|Average_Processor Kölängd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pulsslag|Pulsslag|Antal|Totalt|Pulsslag|Computer, OSType, Version, SourceComputerId|
|Uppdatering|Uppdatering|Antal|Medel|Uppdatering|Dator, produkt, klassificering, UpdateState, valfritt, godkända|
|Händelse|Händelse|Antal|Medel|Händelse|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryDuration|Frågevaraktighet|Millisekunder|Medel|DAX-frågan varaktighet i sista intervallet|Inga dimensioner|
|QueryPoolJobQueueLength|Trådar: Fråga efter pool jobbkölängd|Antal|Medel|Antal jobb i kön för frågetrådspoolen.|Inga dimensioner|
|qpu_high_utilization_metric|QPU hög användning|Antal|Totalt|QPU hög användning i sista minuten, 1 för hög QPU-användning, annars 0|Inga dimensioner|
|memory_metric|Minne|Byte|Medel|Minne. Intervallet 0 – 3 GB för A1, 0 – 5 GB för A2, A3 0 – 10 GB, 0 – 25 GB för A4, 0 – 50 GB a5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Minnesförslöing|Procent|Medel|Genomsnittlig minnesförslöing.|Inga dimensioner|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SearchLatency|Svarstid för sökning|Sekunder|Medel|Svarstid för genomsnittlig sökning för search-tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Sökfrågor per sekund|CountPerSecond|Medel|Sökfrågor per sekund för search-tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Begränsade sökfrågor i procent|Procent|Medel|Procentandel av sökfrågor som har begränsats för search-tjänsten|Inga dimensioner|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade begäranden (förhandsversion)|Antal|Totalt|Totalt antal slutförda begäranden för ett namnområde (förhandsversion)|EntityName|
|ServerErrors|Serverfel. (Förhandsversion)|Antal|Totalt|Serverfel för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|UserErrors|Användarfel. (Förhandsversion)|Antal|Totalt|Användarfel för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|ThrottledRequests|Begränsade begäranden. (Förhandsversion)|Antal|Totalt|Begränsade begäranden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|IncomingRequests|Inkommande förfrågningar (förhandsversion)|Antal|Totalt|Inkommande begäranden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|IncomingMessages|Inkommande meddelanden (förhandsversion)|Antal|Totalt|Inkommande meddelanden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|OutgoingMessages|Utgående meddelanden (förhandsversion)|Antal|Totalt|Utgående meddelanden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|ActiveConnections|ActiveConnections (förhandsversion)|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft.ServiceBus. (Förhandsversion)|Inga dimensioner|
|Storlek|Storlek (förhandsversion)|Byte|Medel|Storlek på kö/ämne i byte. (Förhandsversion)|EntityName|
|Meddelanden|Antal meddelanden i kö/ämne. (Förhandsversion)|Antal|Medel|Antal meddelanden i kö/ämne. (Förhandsversion)|EntityName|
|ActiveMessages|Antal aktiva meddelanden i kö/ämne. (Förhandsversion)|Antal|Medel|Antal aktiva meddelanden i kö/ämne. (Förhandsversion)|EntityName|
|CPUXNS|Processoranvändning per namnområde|Procent|Maximal|CPU-användningsmått för Service Bus Premium-namnområde|Inga dimensioner|
|WSXNS|Minnesstorleksanvändning per namnområde|Procent|Maximal|Minnesanvändningsmått för Service Bus Premium-namnområde|Inga dimensioner|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ConnectionCount|Antal anslutningar|Antal|Maximal|Mängden användaranslutning.|Slutpunkt|
|MessageCount|Antal meddelanden|Antal|Totalt|Den totala mängden meddelanden.|Inga dimensioner|
|InboundTraffic|Inkommande trafik|Byte|Totalt|Inkommande trafik för tjänsten|Inga dimensioner|
|OutboundTraffic|Utgående trafik|Byte|Totalt|Utgående trafik för tjänsten|Inga dimensioner|
|UserErrors|Användarfel|Procent|Maximal|Procentandelen användarfel|Inga dimensioner|
|SystemErrors|Systemfel|Procent|Maximal|Procentandelen fel i filsystemet|Inga dimensioner|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inga dimensioner|
|log_write_percent|Logg-IO-procent|Procent|Medel|Logg-IO-procent|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|Inga dimensioner|
|lagring|Totala databasstorleken|Byte|Maximal|Totala databasstorleken|Inga dimensioner|
|connection_successful|Anslutningarna lyckades|Antal|Totalt|Anslutningarna lyckades|Inga dimensioner|
|connection_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|blocked_by_firewall|Blockeras av brandvägg|Antal|Totalt|Blockeras av brandvägg|Inga dimensioner|
|deadlock|Dödlägen|Antal|Totalt|Dödlägen|Inga dimensioner|
|storage_percent|Databasstorlek i procent|Procent|Maximal|Databasstorlek i procent|Inga dimensioner|
|xtp_storage_percent|Procent för in-Memory OLTP-lagring|Procent|Medel|Procent för in-Memory OLTP-lagring|Inga dimensioner|
|workers_percent|Arbetare procent|Procent|Medel|Arbetare procent|Inga dimensioner|
|sessions_percent|Sessioner procent|Procent|Medel|Sessioner procent|Inga dimensioner|
|dtu_limit|DTU-gräns|Antal|Medel|DTU-gräns|Inga dimensioner|
|dtu_used|DTU används|Antal|Medel|DTU används|Inga dimensioner|
|dwu_limit|DWU-gräns|Antal|Maximal|DWU-gräns|Inga dimensioner|
|dwu_consumption_percent|DWU-procent|Procent|Maximal|DWU-procent|Inga dimensioner|
|dwu_used|Använda DWU|Antal|Maximal|Använda DWU|Inga dimensioner|
|dw_cpu_percent|DW nivån CPU-procent|Procent|Medel|DW nivån CPU-procent|DwLogicalNodeId|
|dw_physical_data_read_percent|DW noden på Data IO-procent|Procent|Medel|DW noden på Data IO-procent|DwLogicalNodeId|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inga dimensioner|
|log_write_percent|Logg-IO-procent|Procent|Medel|Logg-IO-procent|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|Inga dimensioner|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inga dimensioner|
|workers_percent|Arbetare procent|Procent|Medel|Arbetare procent|Inga dimensioner|
|sessions_percent|Sessioner procent|Procent|Medel|Sessioner procent|Inga dimensioner|
|eDTU_limit|eDTU-gränsen|Antal|Medel|eDTU-gränsen|Inga dimensioner|
|storage_limit|Gränsen för lagring|Byte|Medel|Gränsen för lagring|Inga dimensioner|
|eDTU_used|edtu: er används|Antal|Medel|edtu: er används|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inga dimensioner|
|xtp_storage_percent|Procent för in-Memory OLTP-lagring|Procent|Medel|Procent för in-Memory OLTP-lagring|Inga dimensioner|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|virtual_core_count|Antal virtuella kärnor|Antal|Medel|Antal virtuella kärnor|Inga dimensioner|
|avg_cpu_percent|Genomsnittlig CPU-procent|Procent|Medel|Genomsnittlig CPU-procent|Inga dimensioner|
|reserved_storage_mb|Reserverat lagringsutrymme|Antal|Medel|Reserverat lagringsutrymme|Inga dimensioner|
|storage_space_used_mb|Lagringsutrymme som används|Antal|Medel|Lagringsutrymme som används|Inga dimensioner|
|io_requests|Antal för i/o-begäranden|Antal|Medel|Antal för i/o-begäranden|Inga dimensioner|
|io_bytes_read|Lästa byte i i/o|Byte|Medel|Lästa byte i i/o|Inga dimensioner|
|io_bytes_written|Skrivna i/o-byte|Byte|Medel|Skrivna i/o-byte|Inga dimensioner|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Totalt|Kapacitet som används av konto|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, GeoType, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|Blobkapacitet|Byte|Totalt|Mängden lagring som används av lagringskontots Blob Service i byte.|BlobType|
|BlobCount|Antalet blobar|Antal|Totalt|Antalet blobar i lagringskontots Blob Service.|BlobType|
|ContainerCount|Antal blobcontainrar|Antal|Medel|Antalet containrar i lagringskontots Blob Service.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, GeoType, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Filkapacitet|Byte|Medel|Mängden lagring som används av lagringskontots filtjänst i byte.|Inga dimensioner|
|FileCount|Antal filer|Antal|Medel|Antalet filer i lagringskontots filtjänst.|Inga dimensioner|
|FileShareCount|Antal filresurser|Antal|Medel|Antalet filresurser i lagringskontots filtjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, GeoType, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kökapacitet|Byte|Medel|Mängden lagring som används av lagringskontots kötjänst i byte.|Inga dimensioner|
|QueueCount|Antal köer|Antal|Medel|Antalet köer i lagringskontots kötjänst.|Inga dimensioner|
|QueueMessageCount|Antal kömeddelanden|Antal|Medel|Ungefärligt antal kömeddelanden i lagringskontots kötjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, GeoType, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabellkapacitet|Byte|Medel|Mängden lagring som används av lagringskontots tabelltjänst i byte.|Inga dimensioner|
|TableCount|Antal tabeller|Antal|Medel|Antalet tabeller i lagringskontots tabelltjänst.|Inga dimensioner|
|TableEntityCount|Antal tabellentiteter|Antal|Medel|Antalet tabellentiteter i lagringskontots tabelltjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, GeoType, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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
|EarlyInputEvents|Tidiga indatahändelser|Antal|Totalt|Tidiga indatahändelser|Inga dimensioner|
|OutputWatermarkDelaySeconds|Vattenstämpelfördröjning|Sekunder|Maximal|Vattenstämpelfördröjning|Inga dimensioner|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingående fått meddelanden|Antal|Totalt|Antal meddelanden som läses från alla händelsehubb eller IoT-hubb händelsekällor|Inga dimensioner|
|IngressReceivedInvalidMessages|Ingående erhöll ett felaktigt meddelande|Antal|Totalt|Antal felaktiga meddelanden läses från alla händelsehubb eller IoT-hubb händelsekällor|Inga dimensioner|
|IngressReceivedBytes|Ingående har tagit emot byte|Byte|Totalt|Antal byte som läses från alla händelsekällor|Inga dimensioner|
|IngressStoredBytes|Ingående lagras byte|Byte|Totalt|Total storlek på händelser som har bearbetats och tillgängliga för frågor|Inga dimensioner|
|IngressStoredEvents|Ingående lagrade händelser|Antal|Totalt|Antal Flat händelser har bearbetats och tillgängliga för frågor|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Tidsförskjutningen för Ingress mottagna meddelanden|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet är i kö i händelsekällan och den tid som bearbetas i ingående|Inga dimensioner|
|IngressReceivedMessagesCountLag|Inkommande meddelanden som tas emot antal fördröjning|Antal|Medel|Skillnaden mellan sekvensnumret för den sista köas meddelandet i käll-partition och sekvens antal meddelande som bearbetas i ingående|Inga dimensioner|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingående fått meddelanden|Antal|Totalt|Antal meddelanden som läses från händelsekällan|Inga dimensioner|
|IngressReceivedInvalidMessages|Ingående erhöll ett felaktigt meddelande|Antal|Totalt|Antal felaktiga meddelanden läses från händelsekällan|Inga dimensioner|
|IngressReceivedBytes|Ingående har tagit emot byte|Byte|Totalt|Antal byte som läses från händelsekällan|Inga dimensioner|
|IngressStoredBytes|Ingående lagras byte|Byte|Totalt|Total storlek på händelser som har bearbetats och tillgängliga för frågor|Inga dimensioner|
|IngressStoredEvents|Ingående lagrade händelser|Antal|Totalt|Antal Flat händelser har bearbetats och tillgängliga för frågor|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Tidsförskjutningen för Ingress mottagna meddelanden|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet är i kö i händelsekällan och den tid som bearbetas i ingående|Inga dimensioner|
|IngressReceivedMessagesCountLag|Inkommande meddelanden som tas emot antal fördröjning|Antal|Medel|Skillnaden mellan sekvensnumret för den sista köas meddelandet i käll-partition och sekvens antal meddelande som bearbetas i ingående|Inga dimensioner|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Procent|Medel|Minnesprocent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Medel|Diskkölängd|Instans|
|HttpQueueLength|Http-kölängd|Antal|Medel|Http-kölängd|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (exklusive funktioner)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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
|PrivateBytes|Privata byte|Byte|Medel|Privata byte|Instans|
|IoReadBytesPerSecond|I/O lästa byte per sekund|BytesPerSecond|Totalt|I/O lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|I/O skrivna byte per sekund|BytesPerSecond|Totalt|I/O skrivna byte per sekund|Instans|
|IoOtherBytesPerSecond|I/O övrigt byte per sekund|BytesPerSecond|Totalt|I/O övrigt byte per sekund|Instans|
|IoReadOperationsPerSecond|I/O-läsningsåtgärder per sekund|BytesPerSecond|Totalt|I/O-läsningsåtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/O-skrivåtgärder per sekund|BytesPerSecond|Totalt|I/O-skrivåtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|I/O övriga åtgärder per sekund|BytesPerSecond|Totalt|I/O övriga åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Förfrågningar i programkö|Antal|Medel|Förfrågningar i programkö|Instans|
|CurrentAssemblies|Aktuellt antal sammansättningar|Antal|Medel|Aktuellt antal sammansättningar|Instans|
|TotalAppDomains|Totalt antal appdomäner|Antal|Medel|Totalt antal appdomäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal appdomäner som inte lästs in|Antal|Medel|Totalt antal appdomäner som inte lästs in|Instans|
|Gen0Collections|Generation 0-skräpinsamlingar|Antal|Totalt|Generation 0-skräpinsamlingar|Instans|
|Gen1Collections|Generation 1-skräpinsamlingar|Antal|Totalt|Generation 1-skräpinsamlingar|Instans|
|Gen2Collections|Generation 2-skräpinsamlingar|Antal|Totalt|Generation 2-skräpinsamlingar|Instans|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funktioner)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Medel|Genomsnittligt arbetsminne|Instans|
|FunctionExecutionUnits|Funktionskörningsenheter|Antal|Totalt|Funktionskörningsenheter|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Antal|Totalt|Funktionen Antal körningar|Instans|
|PrivateBytes|Privata byte|Byte|Medel|Privata byte|Instans|
|IoReadBytesPerSecond|I/O lästa byte per sekund|BytesPerSecond|Totalt|I/O lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|I/O skrivna byte per sekund|BytesPerSecond|Totalt|I/O skrivna byte per sekund|Instans|
|IoOtherBytesPerSecond|I/O övrigt byte per sekund|BytesPerSecond|Totalt|I/O övrigt byte per sekund|Instans|
|IoReadOperationsPerSecond|I/O-läsningsåtgärder per sekund|BytesPerSecond|Totalt|I/O-läsningsåtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/O-skrivåtgärder per sekund|BytesPerSecond|Totalt|I/O-skrivåtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|I/O övriga åtgärder per sekund|BytesPerSecond|Totalt|I/O övriga åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Förfrågningar i programkö|Antal|Medel|Förfrågningar i programkö|Instans|
|CurrentAssemblies|Aktuellt antal sammansättningar|Antal|Medel|Aktuellt antal sammansättningar|Instans|
|TotalAppDomains|Totalt antal appdomäner|Antal|Medel|Totalt antal appdomäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal appdomäner som inte lästs in|Antal|Medel|Totalt antal appdomäner som inte lästs in|Instans|
|Gen0Collections|Generation 0-skräpinsamlingar|Antal|Totalt|Generation 0-skräpinsamlingar|Instans|
|Gen1Collections|Generation 1-skräpinsamlingar|Antal|Totalt|Generation 1-skräpinsamlingar|Instans|
|Gen2Collections|Generation 2-skräpinsamlingar|Antal|Totalt|Generation 2-skräpinsamlingar|Instans|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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
|PrivateBytes|Privata byte|Byte|Medel|Privata byte|Instans|
|IoReadBytesPerSecond|I/O lästa byte per sekund|BytesPerSecond|Totalt|I/O lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|I/O skrivna byte per sekund|BytesPerSecond|Totalt|I/O skrivna byte per sekund|Instans|
|IoOtherBytesPerSecond|I/O övrigt byte per sekund|BytesPerSecond|Totalt|I/O övrigt byte per sekund|Instans|
|IoReadOperationsPerSecond|I/O-läsningsåtgärder per sekund|BytesPerSecond|Totalt|I/O-läsningsåtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/O-skrivåtgärder per sekund|BytesPerSecond|Totalt|I/O-skrivåtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|I/O övriga åtgärder per sekund|BytesPerSecond|Totalt|I/O övriga åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Förfrågningar i programkö|Antal|Medel|Förfrågningar i programkö|Instans|
|CurrentAssemblies|Aktuellt antal sammansättningar|Antal|Medel|Aktuellt antal sammansättningar|Instans|
|TotalAppDomains|Totalt antal appdomäner|Antal|Medel|Totalt antal appdomäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal appdomäner som inte lästs in|Antal|Medel|Totalt antal appdomäner som inte lästs in|Instans|
|Gen0Collections|Generation 0-skräpinsamlingar|Antal|Totalt|Generation 0-skräpinsamlingar|Instans|
|Gen1Collections|Generation 1-skräpinsamlingar|Antal|Totalt|Generation 1-skräpinsamlingar|Instans|
|Gen2Collections|Generation 2-skräpinsamlingar|Antal|Totalt|Generation 2-skräpinsamlingar|Instans|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbeten|Antal|Medel|Totalt antal arbeten|Inga dimensioner|
|WorkersAvailable|Tillgängliga arbeten|Antal|Medel|Tillgängliga arbeten|Inga dimensioner|
|WorkersUsed|Använda arbeten|Antal|Medel|Använda arbeten|Inga dimensioner|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Procent|Medel|Minnesprocent|Instans|

## <a name="next-steps"></a>Nästa steg
* [Läs mer om mätvärden i Azure Monitor](../../azure-monitor/platform/data-collection.md)
* [Skapa aviseringar för mått](../../azure-monitor/platform/alerts-overview.md)
* [Exportera mått till lagring, Event Hub eller Log Analytics](../../azure-monitor/platform/diagnostic-logs-overview.md)

