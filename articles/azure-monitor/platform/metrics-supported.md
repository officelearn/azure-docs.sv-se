---
title: Azure Monitor stöds mått efter resurstyp
description: Lista över mått som är tillgängliga för varje resurstyp med Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 70f6e26d423781ba53865304a3fe8440fb120a7a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705179"
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
|qpu_metric|QPU|Count|Average|QPU. Intervall 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|memory_metric|Minne|Byte|Average|Minne. Intervall 0 – 25 GB för S1, 0 – 50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|TotalConnectionRequests|Total antal anslutningsbegäranden|Count|Average|Total antal anslutningsbegäranden. Det här är anslutningsbegäranden.|ServerResourceType|
|SuccessfullConnectionsPerSec|Lyckade anslutningar Per sekund|CountPerSecond|Average|Hastighet för lyckade anslutningsavslutningar.|ServerResourceType|
|TotalConnectionFailures|Totalt antal misslyckade anslutningar|Count|Average|Antalet misslyckade anslutningsförsök.|ServerResourceType|
|CurrentUserSessions|Aktuella användarsessioner|Count|Average|Aktuellt antal etablerade användarsessioner.|ServerResourceType|
|QueryPoolBusyThreads|Upptagna trådar för Frågepoolen|Count|Average|Antal upptagna trådar i frågetrådspoolen.|ServerResourceType|
|CommandPoolJobQueueLength|Kommandot Pool Jobbkölängd|Count|Average|Antal jobb i kön för kommandotrådspoolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Bearbetning av poolen Jobbkölängd|Count|Average|Antal icke-I/O-jobb i kön för bearbetningstrådpoolen.|ServerResourceType|
|CurrentConnections|Anslutning: Aktuella anslutningar|Count|Average|Aktuellt antal etablerade klientanslutningar.|ServerResourceType|
|CleanerCurrentPrice|Minne: Aktuellt pris för rensaren|Count|Average|Aktuellt pris för minne, $/ byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Minne: Rensningsminne Krympbart|Byte|Average|Mängden minne i byte, tas bort av bakgrundsrensaren.|ServerResourceType|
|CleanerMemoryNonshrinkable|Minne: Rensningsminne krympbart|Byte|Average|Mängden minne i byte, inte tas bort av bakgrundsrensaren.|ServerResourceType|
|MemoryUsage|Minne: Minnesanvändning|Byte|Average|Minnesanvändning för serverprocessen som används för att beräkna tydligare minnespris. Lika med räknare Process\PrivateBytes plus storleken på minnesmappade data och ignorerar minne som mappats eller allokerats av xVelocity-minnesinterna analyser-motorns (VertiPaq) utöver xVelocity-motorns minnesgräns.|ServerResourceType|
|MemoryLimitHard|Minne: Minnesgräns, hård|Byte|Average|Hård minnesgräns, från konfigurationsfilen.|ServerResourceType|
|MemoryLimitHigh|Minne: Minnesgräns hög|Byte|Average|Hög minnesgräns, från konfigurationsfilen.|ServerResourceType|
|MemoryLimitLow|Minne: Minnesgräns låg|Byte|Average|Låg minnesgräns, från konfigurationsfilen.|ServerResourceType|
|MemoryLimitVertiPaq|Minne: Minnesgräns VertiPaq|Byte|Average|Minnesintern gräns, från konfigurationsfilen.|ServerResourceType|
|Kvot|Minne: Kvot|Byte|Average|Aktuella minneskvoten, i byte. Minneskvoten är även känd som en minnesreservation för bevilja eller minne.|ServerResourceType|
|QuotaBlocked|Minne: Kvot blockerad|Count|Average|Aktuella antalet kvotbegäranden som blockeras tills andra minneskvoter.|ServerResourceType|
|VertiPaqNonpaged|Minne: VertiPaq Nonpaged|Byte|Average|Byte av minne låst i arbetsminnet för användning av InMemory-motorn.|ServerResourceType|
|VertiPaqPaged|Minne: VertiPaq växlat|Byte|Average|Bytes med växlingsbart minne som används för data i minnet.|ServerResourceType|
|RowsReadPerSec|Bearbetar: Rader lästa per sekund|CountPerSecond|Average|Frekvensen som rader läses från alla relationella databaser.|ServerResourceType|
|RowsConvertedPerSec|Bearbetar: Rader konverterade per sekund|CountPerSecond|Average|Frekvensen som rader konverteras under bearbetning.|ServerResourceType|
|RowsWrittenPerSec|Bearbetar: Rader skrivna per sekund|CountPerSecond|Average|Frekvensen som rader skrivs vid bearbetning.|ServerResourceType|
|CommandPoolBusyThreads|Trådar: Kommandot upptagna trådar i kommandopoolen|Count|Average|Antal upptagna trådar i kommandotrådspoolen.|ServerResourceType|
|CommandPoolIdleThreads|Trådar: Kommandot frågepoolen inaktiva trådar|Count|Average|Antal inaktiva trådar i kommandotrådspoolen.|ServerResourceType|
|LongParsingBusyThreads|Trådar: Lång parsning upptagna trådar|Count|Average|Antal upptagna trådar i den lång parsningstrådspoolen.|ServerResourceType|
|LongParsingIdleThreads|Trådar: Lång parsning inaktiva trådar|Count|Average|Antal inaktiva trådar i den lång parsningstrådspoolen.|ServerResourceType|
|LongParsingJobQueueLength|Trådar: Lång parsning jobbkölängd|Count|Average|Antal jobb i kön för den lång parsningstrådspoolen.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Trådar: Bearbetningspoolen upptagen i/o-jobbtrådar|Count|Average|Antal trådar som kör i/o-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Trådar: Bearbetning av poolen upptagen icke-I/O-trådar|Count|Average|Antal trådar som kör icke-I/O-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Trådar: Bearbetningspoolen i/o-jobbkölängd|Count|Average|Antal i/o-jobb i kön för bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Trådar: Bearbetningspoolen inaktiva i/o-jobbtrådar|Count|Average|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Trådar: Bearbetning av poolen inaktiva icke-I/O-trådar|Count|Average|Antal inaktiva trådar i bearbetningstrådpoolen som är dedikerade till icke-I/O-jobb.|ServerResourceType|
|QueryPoolIdleThreads|Trådar: Frågepoolen inaktiva trådar|Count|Average|Antal inaktiva trådar för i/o-jobb i bearbetningstrådpoolen.|ServerResourceType|
|QueryPoolJobQueueLength|Trådar: Fråga efter pool jobbkölängd|Count|Average|Antal jobb i kön för frågetrådspoolen.|ServerResourceType|
|ShortParsingBusyThreads|Trådar: Kort parsning upptagna trådar|Count|Average|Antal upptagna trådar i den korta parsningstrådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Trådar: Kort parsning inaktiva trådar|Count|Average|Antal inaktiva trådar i den korta parsningstrådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Trådar: Kort parsning jobbkölängd|Count|Average|Antal jobb i kön för den korta parsningstrådpoolen.|ServerResourceType|
|memory_thrashing_metric|Minnesförslöing|Percent|Average|Genomsnittlig minnesförslöing.|ServerResourceType|
|mashup_engine_qpu_metric|M-motor QPU|Count|Average|QPU-användning efter motorprocesser|ServerResourceType|
|mashup_engine_memory_metric|M-Motorminne|Byte|Average|Minnesanvändning efter motorprocesser|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalRequests|Total Gateway Requests|Count|Totalt|Antal gateway-begäranden|Plats, värdnamn|
|SuccessfulRequests|Lyckade Gateway-begäranden|Count|Totalt|Antal lyckade gateway-begäranden|Plats, värdnamn|
|UnauthorizedRequests|Ej auktoriserade Gateway-begäranden|Count|Totalt|Antal ej auktoriserade gateway-begäranden|Plats, värdnamn|
|FailedRequests|Misslyckade Gatewaybegäranden|Count|Totalt|Antalet fel i gateway-begäranden|Plats, värdnamn|
|OtherRequests|Andra Gateway-begäranden|Count|Totalt|Antal andra gateway-begäranden|Plats, värdnamn|
|Duration|Varaktighet för Gateway-begäranden|Millisekunder|Average|Övergripande varaktighet av Gateway-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Kapacitet|Percent|Average|Utnyttjande mått för ApiManagement-tjänsten|Location|
|EventHubTotalEvents|Totalt antal EventHub-händelser|Count|Totalt|Antalet händelser som skickas till EventHub|Location|
|EventHubSuccessfulEvents|Lyckad EventHub-händelser|Count|Totalt|Antal lyckade EventHub-händelser|Location|
|EventHubTotalFailedEvents|Misslyckade EventHub-händelser|Count|Totalt|Antalet misslyckade EventHub-händelser|Location|
|EventHubRejectedEvents|Nekade EventHub-händelser|Count|Totalt|Antal avvisade EventHub-händelser (felaktigt konfiguration eller Ej behörig)|Location|
|EventHubThrottledEvents|Begränsade EventHub-händelser|Count|Totalt|Antal begränsade händelser för EventHub|Location|
|EventHubTimedoutEvents|Tidsgränsen nåddes EventHub-händelser|Count|Totalt|Antalet timeout EventHub-händelser|Location|
|EventHubDroppedEvents|Utelämnade EventHub-händelser|Count|Totalt|Antalet händelser som hoppades över på grund av kön storleksgränsen har nåtts|Location|
|EventHubTotalBytesSent|Storleken för EventHub-händelser|Byte|Totalt|Total storlek på händelser för EventHub i byte|Location|
|Begäranden|Begäranden|Count|Totalt|Gateway-begäranden|Plats, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Count|Totalt|Det totala antalet jobb|Runbook, Status|
|TotalUpdateDeploymentRuns|Samlad uppdatering körs|Count|Totalt|Totalt antal programuppdateringsdistribution körs|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Samlad uppdatering dator körs|Count|Totalt|Totalt antal software update distributionsdatorn körs i en programuppdateringsdistribution som kör|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CoreCount|Antal dedikerade kärnor|Count|Totalt|Totalt antal reserverade kärnor i batch-konto|Inga dimensioner|
|TotalNodeCount|Dedikerad Nodantal|Count|Totalt|Totalt antal dedikerade noder i batch-konto|Inga dimensioner|
|LowPriorityCoreCount|Antal kärnor för LowPriority|Count|Totalt|Totala antalet kärnor med låg prioritet i batch-konto|Inga dimensioner|
|TotalLowPriorityNodeCount|Antal noder med låg prioritet|Count|Totalt|Totalt antal noder med låg prioritet i batch-konto|Inga dimensioner|
|CreatingNodeCount|Skapa Nodantal|Count|Totalt|Antalet noder som håller på att skapas|Inga dimensioner|
|StartingNodeCount|Startar Nodantal|Count|Totalt|Antalet noder som startar|Inga dimensioner|
|WaitingForStartTaskNodeCount|Väntar på för Starta aktivitet antal noder|Count|Totalt|Antalet noder som väntar på att åtgärden starta har slutförts|Inga dimensioner|
|StartTaskFailedNodeCount|Startuppgiften misslyckades Nodantal|Count|Totalt|Antalet noder där aktiviteten starta misslyckades|Inga dimensioner|
|IdleNodeCount|Inaktiv Nodantal|Count|Totalt|Antal inaktiva noder|Inga dimensioner|
|OfflineNodeCount|Offline Nodantal|Count|Totalt|Antalet noder som är offline|Inga dimensioner|
|RebootingNodeCount|Starta om Nodantal|Count|Totalt|Antal omstart noder|Inga dimensioner|
|ReimagingNodeCount|Återställningen av avbildningen Nodantal|Count|Totalt|Antalet noder för återställning av avbildning|Inga dimensioner|
|RunningNodeCount|Kör Nodantal|Count|Totalt|Antalet noder som körs|Inga dimensioner|
|LeavingPoolNodeCount|Lämnar Pool Nodantal|Count|Totalt|Antalet noder som lämnar poolen|Inga dimensioner|
|UnusableNodeCount|Oanvändbara Nodantal|Count|Totalt|Antalet noder som inte kan användas|Inga dimensioner|
|PreemptedNodeCount|Allokeringar Nodantal|Count|Totalt|Antalet allokeringar noder|Inga dimensioner|
|TaskStartEvent|Aktiviteten starta händelser|Count|Totalt|Totalt antal aktiviteter som har startat|Inga dimensioner|
|TaskCompleteEvent|Uppgift slutförd händelser|Count|Totalt|Totalt antal aktiviteter som har slutförts|Inga dimensioner|
|TaskFailEvent|Uppgift misslyckas händelser|Count|Totalt|Totalt antal aktiviteter som har slutförts i ett felaktigt tillstånd|Inga dimensioner|
|PoolCreateEvent|Händelser för skapande av pool|Count|Totalt|Totalt antal pooler som har skapats|Inga dimensioner|
|PoolResizeStartEvent|Pool Resize Start händelser|Count|Totalt|Totalt antal poolen ändrar storlek som har startat|Inga dimensioner|
|PoolResizeCompleteEvent|Pool Resize fullständig händelser|Count|Totalt|Totalt antal poolen ändrar storlek som har slutförts|Inga dimensioner|
|PoolDeleteStartEvent|Pool ta bort Start händelser|Count|Totalt|Totalt antal borttagningar för poolen som har startat|Inga dimensioner|
|PoolDeleteCompleteEvent|Pool ta bort klar händelser|Count|Totalt|Totalt antal borttagningar för poolen som har slutförts|Inga dimensioner|
|JobDeleteCompleteEvent|Jobbet Ta bort klar händelser|Count|Totalt|Totalt antal jobb som har tagits bort.|Inga dimensioner|
|JobDeleteStartEvent|Jobbet Ta bort Start händelser|Count|Totalt|Totalt antal jobb som har begärts att tas bort.|Inga dimensioner|
|JobDisableCompleteEvent|Jobbet inaktivera fullständig händelser|Count|Totalt|Totalt antal jobb som har inaktiverats.|Inga dimensioner|
|JobDisableStartEvent|Jobbet inaktivera Start-händelser|Count|Totalt|Totalt antal jobb som har begärt att inaktiveras.|Inga dimensioner|
|JobStartEvent|Jobbet Start händelser|Count|Totalt|Totalt antal jobb som har startats.|Inga dimensioner|
|JobTerminateCompleteEvent|Jobbet Avsluta fullständig händelser|Count|Totalt|Totalt antal jobb som har avslutats.|Inga dimensioner|
|JobTerminateStartEvent|Jobbet Avsluta Start-händelser|Count|Totalt|Totalt antal jobb som har begärt att avslutas.|Inga dimensioner|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedclients|Anslutna klienter|Count|Maximal||ShardId|
|totalcommandsprocessed|Totalt antal åtgärder|Count|Totalt||ShardId|
|cachehits|Cacheträffar|Count|Totalt||ShardId|
|cachemisses|Cachemissar|Count|Totalt||ShardId|
|getcommands|Hämtar|Count|Totalt||ShardId|
|setcommands|Uppsättningar|Count|Totalt||ShardId|
|operationsPerSecond|Åtgärder Per sekund|Count|Maximal||ShardId|
|evictedkeys|Avlägsnade nycklar|Count|Totalt||ShardId|
|totalkeys|Totalt antal nycklar|Count|Maximal||ShardId|
|expiredkeys|Utgångna nycklar|Count|Totalt||ShardId|
|usedmemory|Använt minne|Byte|Maximal||ShardId|
|usedmemorypercentage|Procentandelen använt minne|Percent|Maximal||ShardId|
|usedmemoryRss|Använt minne RSS|Byte|Maximal||ShardId|
|serverLoad|Belastningen på servern|Percent|Maximal||ShardId|
|cacheWrite|Cacheskrivning|BytesPerSecond|Maximal||ShardId|
|cacheRead|Cacheläsning|BytesPerSecond|Maximal||ShardId|
|percentProcessorTime|Processor|Percent|Maximal||ShardId|
|cacheLatency|Cachelagra svarstid mikrosekunder (förhandsversion)|Count|Average||ShardId, SampleType|
|fel|Fel|Count|Maximal||ShardId, ErrorType|
|connectedclients0|Anslutna klienter (fragment 0)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed0|Totalt antal åtgärder (fragment 0)|Count|Totalt||Inga dimensioner|
|cachehits0|Cacheträffar (fragment 0)|Count|Totalt||Inga dimensioner|
|cachemisses0|Cachemissar (fragment 0)|Count|Totalt||Inga dimensioner|
|getcommands0|Hämtar (fragment 0)|Count|Totalt||Inga dimensioner|
|setcommands0|Uppsättningar (Shard 0)|Count|Totalt||Inga dimensioner|
|operationsPerSecond0|Åtgärder Per sekund (fragment 0)|Count|Maximal||Inga dimensioner|
|evictedkeys0|Avlägsnade nycklar (fragment 0)|Count|Totalt||Inga dimensioner|
|totalkeys0|Totalt antal nycklar (fragment 0)|Count|Maximal||Inga dimensioner|
|expiredkeys0|Utgångna nycklar (fragment 0)|Count|Totalt||Inga dimensioner|
|usedmemory0|Använt minne (fragment 0)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss0|Använt minne RSS (fragment 0)|Byte|Maximal||Inga dimensioner|
|serverLoad0|Belastningen på servern (fragment 0)|Percent|Maximal||Inga dimensioner|
|cacheWrite0|Cacheskrivning (fragment 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead0|Cacheläsning (fragment 0)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime0|CPU (fragment 0)|Percent|Maximal||Inga dimensioner|
|connectedclients1|Anslutna klienter (Shard 1)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed1|Totalt antal åtgärder (Shard 1)|Count|Totalt||Inga dimensioner|
|cachehits1|Cacheträffar (Shard 1)|Count|Totalt||Inga dimensioner|
|cachemisses1|Cachemissar (Shard 1)|Count|Totalt||Inga dimensioner|
|getcommands1|Hämtar (Shard 1)|Count|Totalt||Inga dimensioner|
|setcommands1|Uppsättningar (Shard 1)|Count|Totalt||Inga dimensioner|
|operationsPerSecond1|Åtgärder Per sekund (Shard 1)|Count|Maximal||Inga dimensioner|
|evictedkeys1|Avlägsnade nycklar (Shard 1)|Count|Totalt||Inga dimensioner|
|totalkeys1|Totalt antal nycklar (Shard 1)|Count|Maximal||Inga dimensioner|
|expiredkeys1|Utgångna nycklar (Shard 1)|Count|Totalt||Inga dimensioner|
|usedmemory1|Använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss1|Använt minne RSS (Shard 1)|Byte|Maximal||Inga dimensioner|
|serverLoad1|Belastningen på servern (Shard 1)|Percent|Maximal||Inga dimensioner|
|cacheWrite1|Cacheskrivning (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead1|Cacheläsning (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime1|CPU (Shard 1)|Percent|Maximal||Inga dimensioner|
|connectedclients2|Anslutna klienter (fragment 2)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed2|Totalt antal åtgärder (fragment 2)|Count|Totalt||Inga dimensioner|
|cachehits2|Cacheträffar (fragment 2)|Count|Totalt||Inga dimensioner|
|cachemisses2|Cachemissar (fragment 2)|Count|Totalt||Inga dimensioner|
|getcommands2|Hämtar (fragment 2)|Count|Totalt||Inga dimensioner|
|setcommands2|Uppsättningar (fragment 2)|Count|Totalt||Inga dimensioner|
|operationsPerSecond2|Åtgärder Per sekund (fragment 2)|Count|Maximal||Inga dimensioner|
|evictedkeys2|Avlägsnade nycklar (fragment 2)|Count|Totalt||Inga dimensioner|
|totalkeys2|Totalt antal nycklar (fragment 2)|Count|Maximal||Inga dimensioner|
|expiredkeys2|Utgångna nycklar (fragment 2)|Count|Totalt||Inga dimensioner|
|usedmemory2|Använt minne (fragment 2)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss2|Använt minne RSS (fragment 2)|Byte|Maximal||Inga dimensioner|
|serverLoad2|Belastningen på servern (fragment 2)|Percent|Maximal||Inga dimensioner|
|cacheWrite2|Cacheskrivning (fragment 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead2|Cacheläsning (fragment 2)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime2|CPU (fragment 2)|Percent|Maximal||Inga dimensioner|
|connectedclients3|Anslutna klienter (fragment 3)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed3|Totalt antal åtgärder (fragment 3)|Count|Totalt||Inga dimensioner|
|cachehits3|Cacheträffar (fragment 3)|Count|Totalt||Inga dimensioner|
|cachemisses3|Cachemissar (fragment 3)|Count|Totalt||Inga dimensioner|
|getcommands3|Hämtar (fragment 3)|Count|Totalt||Inga dimensioner|
|setcommands3|Uppsättningar (fragment 3)|Count|Totalt||Inga dimensioner|
|operationsPerSecond3|Åtgärder Per sekund (fragment 3)|Count|Maximal||Inga dimensioner|
|evictedkeys3|Avlägsnade nycklar (fragment 3)|Count|Totalt||Inga dimensioner|
|totalkeys3|Totalt antal nycklar (fragment 3)|Count|Maximal||Inga dimensioner|
|expiredkeys3|Utgångna nycklar (fragment 3)|Count|Totalt||Inga dimensioner|
|usedmemory3|Använt minne (fragment 3)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss3|Använt minne RSS (fragment 3)|Byte|Maximal||Inga dimensioner|
|serverLoad3|Belastningen på servern (fragment 3)|Percent|Maximal||Inga dimensioner|
|cacheWrite3|Cacheskrivning (fragment 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead3|Cacheläsning (fragment 3)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime3|CPU (fragment 3)|Percent|Maximal||Inga dimensioner|
|connectedclients4|Anslutna klienter (fragment 4)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed4|Totalt antal åtgärder (fragment 4)|Count|Totalt||Inga dimensioner|
|cachehits4|Cacheträffar (fragment 4)|Count|Totalt||Inga dimensioner|
|cachemisses4|Cachemissar (fragment 4)|Count|Totalt||Inga dimensioner|
|getcommands4|Hämtar (fragment 4)|Count|Totalt||Inga dimensioner|
|setcommands4|Uppsättningar (fragment 4)|Count|Totalt||Inga dimensioner|
|operationsPerSecond4|Åtgärder Per sekund (fragment 4)|Count|Maximal||Inga dimensioner|
|evictedkeys4|Avlägsnade nycklar (fragment 4)|Count|Totalt||Inga dimensioner|
|totalkeys4|Totalt antal nycklar (fragment 4)|Count|Maximal||Inga dimensioner|
|expiredkeys4|Utgångna nycklar (fragment 4)|Count|Totalt||Inga dimensioner|
|usedmemory4|Använt minne (fragment 4)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss4|Använt minne RSS (fragment 4)|Byte|Maximal||Inga dimensioner|
|serverLoad4|Belastningen på servern (fragment 4)|Percent|Maximal||Inga dimensioner|
|cacheWrite4|Cacheskrivning (fragment 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead4|Cacheläsning (fragment 4)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime4|CPU (fragment 4)|Percent|Maximal||Inga dimensioner|
|connectedclients5|Anslutna klienter (fragment 5)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed5|Totalt antal åtgärder (fragment 5)|Count|Totalt||Inga dimensioner|
|cachehits5|Cacheträffar (fragment 5)|Count|Totalt||Inga dimensioner|
|cachemisses5|Cachemissar (fragment 5)|Count|Totalt||Inga dimensioner|
|getcommands5|Hämtar (fragment 5)|Count|Totalt||Inga dimensioner|
|setcommands5|Uppsättningar (fragment 5)|Count|Totalt||Inga dimensioner|
|operationsPerSecond5|Åtgärder Per sekund (fragment 5)|Count|Maximal||Inga dimensioner|
|evictedkeys5|Avlägsnade nycklar (fragment 5)|Count|Totalt||Inga dimensioner|
|totalkeys5|Totalt antal nycklar (fragment 5)|Count|Maximal||Inga dimensioner|
|expiredkeys5|Utgångna nycklar (fragment 5)|Count|Totalt||Inga dimensioner|
|usedmemory5|Använt minne (fragment 5)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss5|Använt minne RSS (fragment 5)|Byte|Maximal||Inga dimensioner|
|serverLoad5|Belastningen på servern (fragment 5)|Percent|Maximal||Inga dimensioner|
|cacheWrite5|Cacheskrivning (fragment 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead5|Cacheläsning (fragment 5)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime5|CPU (fragment 5)|Percent|Maximal||Inga dimensioner|
|connectedclients6|Anslutna klienter (fragment 6)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed6|Totalt antal åtgärder (fragment 6)|Count|Totalt||Inga dimensioner|
|cachehits6|Cacheträffar (fragment 6)|Count|Totalt||Inga dimensioner|
|cachemisses6|Cachemissar (fragment 6)|Count|Totalt||Inga dimensioner|
|getcommands6|Hämtar (fragment 6)|Count|Totalt||Inga dimensioner|
|setcommands6|Uppsättningar (Shard 6)|Count|Totalt||Inga dimensioner|
|operationsPerSecond6|Åtgärder Per sekund (fragment 6)|Count|Maximal||Inga dimensioner|
|evictedkeys6|Avlägsnade nycklar (fragment 6)|Count|Totalt||Inga dimensioner|
|totalkeys6|Totalt antal nycklar (fragment 6)|Count|Maximal||Inga dimensioner|
|expiredkeys6|Utgångna nycklar (fragment 6)|Count|Totalt||Inga dimensioner|
|usedmemory6|Använt minne (fragment 6)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss6|Använt minne RSS (fragment 6)|Byte|Maximal||Inga dimensioner|
|serverLoad6|Belastningen på servern (fragment 6)|Percent|Maximal||Inga dimensioner|
|cacheWrite6|Cacheskrivning (fragment 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead6|Cacheläsning (fragment 6)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime6|CPU (fragment 6)|Percent|Maximal||Inga dimensioner|
|connectedclients7|Anslutna klienter (Shard 7)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed7|Totalt antal åtgärder (Shard 7)|Count|Totalt||Inga dimensioner|
|cachehits7|Cache Hits (Shard 7)|Count|Totalt||Inga dimensioner|
|cachemisses7|Cachemissar (Shard 7)|Count|Totalt||Inga dimensioner|
|getcommands7|Hämtar (Shard 7)|Count|Totalt||Inga dimensioner|
|setcommands7|Uppsättningar (Shard 7)|Count|Totalt||Inga dimensioner|
|operationsPerSecond7|Åtgärder Per sekund (Shard 7)|Count|Maximal||Inga dimensioner|
|evictedkeys7|Avlägsnade nycklar (Shard 7)|Count|Totalt||Inga dimensioner|
|totalkeys7|Totalt antal nycklar (Shard 7)|Count|Maximal||Inga dimensioner|
|expiredkeys7|Utgångna nycklar (Shard 7)|Count|Totalt||Inga dimensioner|
|usedmemory7|Använt minne (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss7|Använt minne RSS (Shard 7)|Byte|Maximal||Inga dimensioner|
|serverLoad7|Belastningen på servern (Shard 7)|Percent|Maximal||Inga dimensioner|
|cacheWrite7|Cacheskrivning (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead7|Cacheläsning (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime7|CPU (Shard 7)|Percent|Maximal||Inga dimensioner|
|connectedclients8|Anslutna klienter (fragment 8)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed8|Totalt antal åtgärder (fragment 8)|Count|Totalt||Inga dimensioner|
|cachehits8|Cacheträffar (fragment 8)|Count|Totalt||Inga dimensioner|
|cachemisses8|Cachemissar (fragment 8)|Count|Totalt||Inga dimensioner|
|getcommands8|Hämtar (fragment 8)|Count|Totalt||Inga dimensioner|
|setcommands8|Uppsättningar (fragment 8)|Count|Totalt||Inga dimensioner|
|operationsPerSecond8|Åtgärder Per sekund (fragment 8)|Count|Maximal||Inga dimensioner|
|evictedkeys8|Avlägsnade nycklar (fragment 8)|Count|Totalt||Inga dimensioner|
|totalkeys8|Totalt antal nycklar (fragment 8)|Count|Maximal||Inga dimensioner|
|expiredkeys8|Utgångna nycklar (fragment 8)|Count|Totalt||Inga dimensioner|
|usedmemory8|Använt minne (fragment 8)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss8|Använt minne RSS (fragment 8)|Byte|Maximal||Inga dimensioner|
|serverLoad8|Belastningen på servern (fragment 8)|Percent|Maximal||Inga dimensioner|
|cacheWrite8|Cacheskrivning (fragment 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead8|Cacheläsning (fragment 8)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime8|CPU (fragment 8)|Percent|Maximal||Inga dimensioner|
|connectedclients9|Anslutna klienter (fragment 9)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed9|Totalt antal åtgärder (fragment 9)|Count|Totalt||Inga dimensioner|
|cachehits9|Cacheträffar (fragment 9)|Count|Totalt||Inga dimensioner|
|cachemisses9|Cachemissar (fragment 9)|Count|Totalt||Inga dimensioner|
|getcommands9|Hämtar (fragment 9)|Count|Totalt||Inga dimensioner|
|setcommands9|Uppsättningar (fragment 9)|Count|Totalt||Inga dimensioner|
|operationsPerSecond9|Åtgärder Per sekund (fragment 9)|Count|Maximal||Inga dimensioner|
|evictedkeys9|Avlägsnade nycklar (fragment 9)|Count|Totalt||Inga dimensioner|
|totalkeys9|Totalt antal nycklar (fragment 9)|Count|Maximal||Inga dimensioner|
|expiredkeys9|Utgångna nycklar (fragment 9)|Count|Totalt||Inga dimensioner|
|usedmemory9|Använt minne (fragment 9)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss9|Använt minne RSS (fragment 9)|Byte|Maximal||Inga dimensioner|
|serverLoad9|Belastningen på servern (fragment 9)|Percent|Maximal||Inga dimensioner|
|cacheWrite9|Cacheskrivning (fragment 9)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead9|Cacheläsning (fragment 9)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime9|CPU (fragment 9)|Percent|Maximal||Inga dimensioner|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Average|Procentandel tilldelade beräkningsenheter som används av virtuella datorer.|Inga dimensioner|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik).|Inga dimensioner|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik).|Inga dimensioner|
|Disk-lästa byte/sek|Diskläsning|BytesPerSecond|Average|Genomsnittligt antal byte lästa från disk under övervakningsperioden.|Inga dimensioner|
|Disk-skrivna byte/s|Diskskrivning|BytesPerSecond|Average|Genomsnittligt antal byte som skrivs från disk under övervakningsperioden.|Inga dimensioner|
|Disk-lästa/sek|Disk-lästa/sek|CountPerSecond|Average|Lästa IOPS på disk.|Inga dimensioner|
|Disk skrivåtgärder/sek|Disk skrivåtgärder/sek|CountPerSecond|Average|Skrivna IOPS på disk.|Inga dimensioner|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Average|Procentandel tilldelade beräkningsenheter som används av virtuella datorer.|RoleInstanceId|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik).|RoleInstanceId|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik).|RoleInstanceId|
|Disk-lästa byte/sek|Diskläsning|BytesPerSecond|Average|Genomsnittligt antal byte lästa från disk under övervakningsperioden.|RoleInstanceId|
|Disk-skrivna byte/s|Diskskrivning|BytesPerSecond|Average|Genomsnittligt antal byte som skrivs från disk under övervakningsperioden.|RoleInstanceId|
|Disk-lästa/sek|Disk-lästa/sek|CountPerSecond|Average|Lästa IOPS på disk.|RoleInstanceId|
|Disk skrivåtgärder/sek|Disk skrivåtgärder/sek|CountPerSecond|Average|Skrivna IOPS på disk.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalCalls|Totala anrop|Count|Totalt|Totalt antal anrop.|ApiName, OperationName, Region|
|SuccessfulCalls|Utförda anrop|Count|Totalt|Antalet utförda anrop.|ApiName, OperationName, Region|
|TotalErrors|Totalt antal fel|Count|Totalt|Totalt antal anrop med felsvar (HTTP-svarskod 4xx eller 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Blockerade anrop|Count|Totalt|Antal anrop som har överskridits hastighet eller kvot.|ApiName, OperationName, Region|
|ServerErrors|Serverfel|Count|Totalt|Antal anrop med internt fel i tjänsten (HTTP-svarskod 5xx).|ApiName, OperationName, Region|
|ClientErrors|Klientfel|Count|Totalt|Antal anrop med fel på klientsidan (HTTP-svarskod 4xx).|ApiName, OperationName, Region|
|DataIn|Data i|Byte|Totalt|Storlek på inkommande data i byte.|ApiName, OperationName, Region|
|DataOut|Data ut|Byte|Totalt|Storlek på utgående data i byte.|ApiName, OperationName, Region|
|Svarstid|Svarstid|MilliSeconds|Average|Svarstid i millisekunder.|ApiName, OperationName, Region|
|CharactersTranslated|Översatta tecken|Count|Totalt|Totalt antal tecken i inkommande textbegäran.|ApiName, OperationName, Region|
|CharactersTrained|Tecken som har tränats|Count|Totalt|Totalt antal tecken som har tränats.|ApiName, OperationName, Region|
|SpeechSessionDuration|Tal sessionens längd|Sekunder|Totalt|Total varaktighet för talsession i sekunder.|ApiName, OperationName, Region|
|TotalTransactions|Totalt antal transaktioner|Count|Totalt|Totalt antal transaktioner.|Inga dimensioner|
|TotalTokenCalls|Totalt antal Token anrop|Count|Totalt|Totalt antal token anrop.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inga dimensioner|
|Nätverk – inkommande|Nätverk i fakturerbara|Byte|Totalt|Antalet fakturerbara byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Nätverk ut fakturerbara|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Byte som skrivs från disk under övervakningsperioden|Inga dimensioner|
|Disk-lästa/sek|Disk-lästa/sek|CountPerSecond|Average|Lästa IOPS på disk|Inga dimensioner|
|Disk skrivåtgärder/sek|Disk skrivåtgärder/sek|CountPerSecond|Average|Skrivna IOPS på disk|Inga dimensioner|
|Återstående CPU-krediter|Återstående CPU-krediter|Count|Average|Totalt antal krediter tillgängliga för plötsliga toppar|Inga dimensioner|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Count|Average|Totalt antal krediter som använts av den virtuella datorn|Inga dimensioner|
|Per Disk-lästa byte/sek|Datadisk-lästa byte/sek (inaktuell)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperioden|SlotId|
|Per Disk-skrivna byte/s|Data Disk-skrivna byte/s (inaktuell)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperioden|SlotId|
|Per Disk-lästa/sek|Datadisk-lästa/sek (inaktuell)|CountPerSecond|Average|Läs IOPS från en enskild disk under övervakningsperioden|SlotId|
|Per Disk skrivåtgärder/sek|Data Disk skrivåtgärder/sek (inaktuell)|CountPerSecond|Average|Skriva IOPS från en enskild disk under övervakningsperioden|SlotId|
|Ködjup per Disk|Datadiskködjup (inaktuell)|Count|Average|Data Datadiskködjup (eller Kölängd)|SlotId|
|OS Per Disk-lästa byte/s|OS-Disk lästa byte/sek (inaktuell)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivåtgärder Per Operativsystemdisk byte/sek|(Föråldrad) OS-disken skrivna byte/sek|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|PER Disk Läs åtgärder/sek|OS-Diskläsningar/sek (inaktuell)|CountPerSecond|Average|Läs IOPS från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivåtgärder Per Operativsystemdisk/sek|Operativsystemdisks skrivåtgärder/sek (inaktuell)|CountPerSecond|Average|Skriva IOPS från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|PER Operativsystemdisk|Operativsystemdisks Ködjup (inaktuell)|Count|Average|Operativsystemdisks ködjup (eller Kölängd)|Inga dimensioner|
|Datadisk-lästa byte/sek|Datadisk-lästa byte/sek (förhandsversion)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperioden|LUN|
|Data Disk-skrivna byte/s|Data Disk-skrivna byte/s (förhandsversion)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperioden|LUN|
|Datadisk-lästa/sek|Datadisk-lästa/sek (förhandsversion)|CountPerSecond|Average|Läs IOPS från en enskild disk under övervakningsperioden|LUN|
|Data Disk skrivåtgärder/sek|Data Disk skrivåtgärder/sek (förhandsversion)|CountPerSecond|Average|Skriva IOPS från en enskild disk under övervakningsperioden|LUN|
|Data av Datadiskködjup|Data Datadiskködjup (förhandsversion)|Count|Average|Data Datadiskködjup (eller Kölängd)|LUN|
|OS-Disk-lästa byte/sek|OS-Disk-lästa byte/sek (förhandsversion)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|OS-Disk-skrivna byte/sek|OS-disken skrivna byte/sek (förhandsversion)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|OS-Diskläsningar/sek|OS-Diskläsningar/sek (förhandsversion)|CountPerSecond|Average|Läs IOPS från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Operativsystemdisks skrivåtgärder/sek|Operativsystemdisks skrivåtgärder/sek (förhandsversion)|CountPerSecond|Average|Skriva IOPS från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Operativsystemdisks ködjup|Operativsystemdisks ködjup (förhandsversion)|Count|Average|Operativsystemdisks ködjup (eller Kölängd)|Inga dimensioner|
|Inkommande flöden|Inkommande flöden (förhandsversion)|Count|Average|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik kommer in till den virtuella datorn)|Inga dimensioner|
|Utgående flöden|Utgående flöden (förhandsversion)|Count|Average|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Inkommande flöden skapa en högsta hastighet|Inkommande flöden maximala Skapa pris (förhandsversion)|CountPerSecond|Average|Skapa en högsta mängden inkommande flöden (trafik kommer in till den virtuella datorn)|Inga dimensioner|
|Utgående flöden skapa en högsta hastighet|Utgående flöden maximala Skapa pris (förhandsversion)|CountPerSecond|Average|Skapa en högsta mängden utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Läs för Premium Data Disk Cacheträff|Premium Data Disk Läs Cacheträff (förhandsversion)|Percent|Average|Läs för Premium Data Disk Cacheträff|LUN|
|Premium Disk Läs missar för cachelagrade metadata|Premium Disk Läs missar för cachelagrade metadata (förhandsversion)|Percent|Average|Premium Disk Läs missar för cachelagrade metadata|LUN|
|Premium OS-disken Cacheläsning träffar|Premium OS-disken Cacheläsning når (förhandsversion)|Percent|Average|Premium OS-disken Cacheläsning träffar|Inga dimensioner|
|Läs för Premium OS Disk cachemiss|Premium OS Disk Läs cachemiss (förhandsversion)|Percent|Average|Läs för Premium OS Disk cachemiss|Inga dimensioner|
|Nätverket totalt|Nätverket totalt|Byte|Totalt|Antalet byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk ut totalt|Nätverk ut totalt|Byte|Totalt|Antalet byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|VMName|
|Nätverk – inkommande|Nätverk i fakturerbara|Byte|Totalt|Antalet fakturerbara byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|VMName|
|Nätverk – utgående|Nätverk ut fakturerbara|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|VMName|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|VMName|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Byte som skrivs från disk under övervakningsperioden|VMName|
|Disk-lästa/sek|Disk-lästa/sek|CountPerSecond|Average|Lästa IOPS på disk|VMName|
|Disk skrivåtgärder/sek|Disk skrivåtgärder/sek|CountPerSecond|Average|Skrivna IOPS på disk|VMName|
|Återstående CPU-krediter|Återstående CPU-krediter|Count|Average|Totalt antal krediter tillgängliga för plötsliga toppar|Inga dimensioner|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Count|Average|Totalt antal krediter som använts av den virtuella datorn|Inga dimensioner|
|Per Disk-lästa byte/sek|Datadisk-lästa byte/sek (inaktuell)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperioden|SlotId|
|Per Disk-skrivna byte/s|Data Disk-skrivna byte/s (inaktuell)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperioden|SlotId|
|Per Disk-lästa/sek|Datadisk-lästa/sek (inaktuell)|CountPerSecond|Average|Läs IOPS från en enskild disk under övervakningsperioden|SlotId|
|Per Disk skrivåtgärder/sek|Data Disk skrivåtgärder/sek (inaktuell)|CountPerSecond|Average|Skriva IOPS från en enskild disk under övervakningsperioden|SlotId|
|Ködjup per Disk|Datadiskködjup (inaktuell)|Count|Average|Data Datadiskködjup (eller Kölängd)|SlotId|
|OS Per Disk-lästa byte/s|OS-Disk lästa byte/sek (inaktuell)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivåtgärder Per Operativsystemdisk byte/sek|(Föråldrad) OS-disken skrivna byte/sek|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|PER Disk Läs åtgärder/sek|OS-Diskläsningar/sek (inaktuell)|CountPerSecond|Average|Läs IOPS från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|Skrivåtgärder Per Operativsystemdisk/sek|Operativsystemdisks skrivåtgärder/sek (inaktuell)|CountPerSecond|Average|Skriva IOPS från en enskild disk under övervakningsperioden för operativsystemdisken|Inga dimensioner|
|PER Operativsystemdisk|Operativsystemdisks Ködjup (inaktuell)|Count|Average|Operativsystemdisks ködjup (eller Kölängd)|Inga dimensioner|
|Datadisk-lästa byte/sek|Datadisk-lästa byte/sek (förhandsversion)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperioden|LUN, VMName|
|Data Disk-skrivna byte/s|Data Disk-skrivna byte/s (förhandsversion)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperioden|LUN, VMName|
|Datadisk-lästa/sek|Datadisk-lästa/sek (förhandsversion)|CountPerSecond|Average|Läs IOPS från en enskild disk under övervakningsperioden|LUN, VMName|
|Data Disk skrivåtgärder/sek|Data Disk skrivåtgärder/sek (förhandsversion)|CountPerSecond|Average|Skriva IOPS från en enskild disk under övervakningsperioden|LUN, VMName|
|Data av Datadiskködjup|Data Datadiskködjup (förhandsversion)|Count|Average|Data Datadiskködjup (eller Kölängd)|LUN, VMName|
|OS-Disk-lästa byte/sek|OS-Disk-lästa byte/sek (förhandsversion)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperioden för operativsystemdisken|VMName|
|OS-Disk-skrivna byte/sek|OS-disken skrivna byte/sek (förhandsversion)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperioden för operativsystemdisken|VMName|
|OS-Diskläsningar/sek|OS-Diskläsningar/sek (förhandsversion)|CountPerSecond|Average|Läs IOPS från en enskild disk under övervakningsperioden för operativsystemdisken|VMName|
|Operativsystemdisks skrivåtgärder/sek|Operativsystemdisks skrivåtgärder/sek (förhandsversion)|CountPerSecond|Average|Skriva IOPS från en enskild disk under övervakningsperioden för operativsystemdisken|VMName|
|Operativsystemdisks ködjup|Operativsystemdisks ködjup (förhandsversion)|Count|Average|Operativsystemdisks ködjup (eller Kölängd)|VMName|
|Inkommande flöden|Inkommande flöden (förhandsversion)|Count|Average|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik kommer in till den virtuella datorn)|VMName|
|Utgående flöden|Utgående flöden (förhandsversion)|Count|Average|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|VMName|
|Inkommande flöden skapa en högsta hastighet|Inkommande flöden maximala Skapa pris (förhandsversion)|CountPerSecond|Average|Skapa en högsta mängden inkommande flöden (trafik kommer in till den virtuella datorn)|VMName|
|Utgående flöden skapa en högsta hastighet|Utgående flöden maximala Skapa pris (förhandsversion)|CountPerSecond|Average|Skapa en högsta mängden utgående flöden (trafik som går ut från den virtuella datorn)|VMName|
|Läs för Premium Data Disk Cacheträff|Premium Data Disk Läs Cacheträff (förhandsversion)|Percent|Average|Läs för Premium Data Disk Cacheträff|LUN, VMName|
|Premium Disk Läs missar för cachelagrade metadata|Premium Disk Läs missar för cachelagrade metadata (förhandsversion)|Percent|Average|Premium Disk Läs missar för cachelagrade metadata|LUN, VMName|
|Premium OS-disken Cacheläsning träffar|Premium OS-disken Cacheläsning når (förhandsversion)|Percent|Average|Premium OS-disken Cacheläsning träffar|VMName|
|Läs för Premium OS Disk cachemiss|Premium OS Disk Läs cachemiss (förhandsversion)|Percent|Average|Läs för Premium OS Disk cachemiss|VMName|
|Nätverket totalt|Nätverket totalt|Byte|Totalt|Antalet byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|VMName|
|Nätverk ut totalt|Nätverk ut totalt|Byte|Totalt|Antalet byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|CPU-användning|Count|Average|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Minnesanvändning|Byte|Average|Total minnesanvändning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Mottagna nätverksbyte Per sekund|Byte|Average|Nätverksbyte per sekund.|Inga dimensioner|
|NetworkBytesTransmittedPerSecond|Nätverks-byte skickas Per sekund|Byte|Average|Nätverks-byte skickas per sekund.|Inga dimensioner|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalPullCount|Totala Pull-antal|Count|Average|Antal bild hämtar totalt|Inga dimensioner|
|SuccessfulPullCount|Antal lyckade Pull|Count|Average|Antal lyckade avbildningar|Inga dimensioner|
|TotalPushCount|Totalt antal Push-antal|Count|Average|Antal bild push-meddelanden totalt|Inga dimensioner|
|SuccessfulPushCount|Lyckad sändning antal|Count|Average|Antalet lyckade bild push-meddelanden|Inga dimensioner|
|RunDuration|Körningens varaktighet|Millisekunder|Totalt|Körningens varaktighet i millisekunder|Inga dimensioner|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totala antalet tillgängliga cpu-kärnor i ett hanterat kluster|Count|Totalt|Totala antalet tillgängliga cpu-kärnor i ett hanterat kluster|Inga dimensioner|
|kube_node_status_allocatable_memory_bytes|Totala mängden tillgängligt minne i ett hanterat kluster|Byte|Totalt|Totala mängden tillgängligt minne i ett hanterat kluster|Inga dimensioner|
|kube_pod_status_ready|Antalet poddar i färdigt tillstånd|Count|Totalt|Antalet poddar i färdigt tillstånd|namnområdet, pod|
|kube_node_status_condition|Status för olika noden villkor|Count|Totalt|Status för olika noden villkor|villkor, status, status2, noden|
|kube_pod_status_phase|Antalet poddar av fas|Count|Totalt|Antalet poddar av fas|fas, namnområde, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API-anrop|Count|Totalt||Inga dimensioner|
|DCIMappingImportOperationSuccessfulLines|Mappning av Import åtgärden lyckas rader|Count|Totalt||Inga dimensioner|
|DCIMappingImportOperationFailedLines|Det gick inte att mappa importåtgärden rader|Count|Totalt||Inga dimensioner|
|DCIMappingImportOperationTotalLines|Totalt antal rader för mappning av Import-åtgärden|Count|Totalt||Inga dimensioner|
|DCIMappingImportOperationRuntimeInSeconds|Mappning av Import åtgärden Runtime på några sekunder|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundProfileExportSucceeded|Utgående profil exporten lyckades|Count|Totalt||Inga dimensioner|
|DCIOutboundProfileExportFailed|Utgående profil exporten misslyckades|Count|Totalt||Inga dimensioner|
|DCIOutboundProfileExportDuration|Utgående profil Export varaktighet|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiExportSucceeded|Utgående Kpi-exporten lyckades|Count|Totalt||Inga dimensioner|
|DCIOutboundKpiExportFailed|Utgående Kpi-exporten misslyckades|Count|Totalt||Inga dimensioner|
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
|ImportASAValuesFailed|Importera ASA värden misslyckades antal|Count|Totalt||Inga dimensioner|
|ImportASAValuesSucceeded|Importera ASA värden lyckades antal|Count|Totalt||Inga dimensioner|
|DCIProfilesCount|Antal instanser för profil|Count|Sista||Inga dimensioner|
|DCIInteractionsPerMonthCount|Interaktioner per månad antal|Count|Sista||Inga dimensioner|
|DCIKpisCount|KPI-antal|Count|Sista||Inga dimensioner|
|DCISegmentsCount|Antal segment|Count|Sista||Inga dimensioner|
|DCIPredictiveMatchPoliciesCount|Förutsägande Matchningsantal|Count|Sista||Inga dimensioner|
|DCIPredictionsCount|Antal för förutsägelse|Count|Sista||Inga dimensioner|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|NICReadThroughput|Läs dataflöde (nätverk)|BytesPerSecond|Average|Läsgenomströmning för gränssnitt på enheten i rapporteringsperioden för alla volymer i gatewayen.|Instansnamn|
|NICWriteThroughput|Skriva dataflöde (nätverk)|BytesPerSecond|Average|Genomströmning för skrivning av nätverksgränssnitt på enheten i rapporteringsperioden för alla volymer i gatewayen.|Instansnamn|
|CloudReadThroughputPerShare|Molnet Download dataflöde (resurs)|BytesPerSecond|Average|Ladda ned dataflödet till Azure från en resurs under rapporteringsperioden.|Dela|
|CloudUploadThroughputPerShare|Molnet Uppladdningsdataflödet (resurs)|BytesPerSecond|Average|Uppladdningsdataflödet till Azure från en resurs under rapporteringsperioden.|Dela|
|BytesUploadedToCloudPerShare|Molnet byte överförs (resurs)|Byte|Average|Det totala antalet byte som har överförts till Azure från en resurs under rapporteringsperioden.|Dela|
|TotalCapacity|Total kapacitet|Byte|Average|Total kapacitet|Inga dimensioner|
|AvailableCapacity|Tillgänglig kapacitet|Byte|Average|Den tillgängliga kapaciteten i byte under rapporteringsperioden.|Inga dimensioner|
|CloudUploadThroughput|Molnet Uppladdningsdataflödet|BytesPerSecond|Average|Molnet uppladdningsdataflödet till Azure under rapporteringsperioden.|Inga dimensioner|
|CloudReadThroughput|Molnet Download dataflöde|BytesPerSecond|Average|Molnet download dataflödet till Azure under rapporteringsperioden.|Inga dimensioner|
|BytesUploadedToCloud|Molnet byte överförs (enhet)|Byte|Average|Det totala antalet byte som har överförts till Azure från en enhet under rapporteringsperioden.|Inga dimensioner|
|HyperVVirtualProcessorUtilization|Edge-beräkning – procent CPU|Percent|Average|Procent CPU-användning|Instansnamn|
|HyperVMemoryUtilization|Edge-beräkning - minnesanvändning|Percent|Average|Mängden RAM-minne som används|Instansnamn|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FailedRuns|Misslyckade körningar|Count|Totalt||pipelineName, activityName|
|SuccessfulRuns|Lyckade körningar|Count|Totalt||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PipelineFailedRuns|Det gick inte mått för pipeline-körningar|Count|Totalt||FailureType namn|
|PipelineSucceededRuns|Lyckades mått för pipeline-körningar|Count|Totalt||FailureType namn|
|ActivityFailedRuns|Det gick inte aktivitetsmått för körningar|Count|Totalt||ActivityType, PipelineName, FailureType, namn|
|ActivitySucceededRuns|Lyckades aktivitetsmått för körningar|Count|Totalt||ActivityType, PipelineName, FailureType, namn|
|TriggerFailedRuns|Det gick inte utlösaren körs mått|Count|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Lyckades utlösaren körs mått|Count|Totalt||Namn, FailureType|
|IntegrationRuntimeCpuPercentage|Integration runtime CPU-användning|Percent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Tillgängligt minne för integration runtime|Byte|Average||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Högsta tillåtna enheter antal|Count|Maximal||Inga dimensioner|
|MaxAllowedFactorySizeInGbUnits|Maximalt tillåten factory storlek (GB-enhet)|Count|Maximal||Inga dimensioner|
|ResourceCount|Antal totala enheter|Count|Maximal||Inga dimensioner|
|FactorySizeInGbUnits|Totalt antal factory storlek (GB-enhet)|Count|Maximal||Inga dimensioner|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobEndedSuccess|Lyckade jobb|Count|Totalt|Antal genomförda jobb.|Inga dimensioner|
|JobEndedFailure|Misslyckade jobb|Count|Totalt|Totalt antal misslyckade jobb.|Inga dimensioner|
|JobEndedCancelled|Avbrutna jobb|Count|Totalt|Antal avbrutna jobb.|Inga dimensioner|
|JobAUEndedSuccess|Lyckad AU-tid|Sekunder|Totalt|Totalt antal AU-tid för genomförda jobb.|Inga dimensioner|
|JobAUEndedFailure|Misslyckade AU-tid|Sekunder|Totalt|Totalt antal AU-tid för misslyckade jobb.|Inga dimensioner|
|JobAUEndedCancelled|Avbrutna AU-tid|Sekunder|Totalt|Totalt antal AU-tid för avbrutna jobb.|Inga dimensioner|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Byte|Maximal|Totala mängden data som lagras i kontot.|Inga dimensioner|
|DataWritten|Data som skrivs|Byte|Totalt|Totala mängden data som skrivs till kontot.|Inga dimensioner|
|DataRead|Lästa|Byte|Totalt|Total mängd data läses från kontot.|Inga dimensioner|
|WriteRequests|Skrivförfrågningar|Count|Totalt|Antal data diskskrivningsbegäranden till kontot.|Inga dimensioner|
|ReadRequests|Läsbegäranden|Count|Totalt|Antal data diskläsningsbegäranden till kontot.|Inga dimensioner|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|memory_percent|Minne|Percent|Average|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Percent|Average|IO-procent|Inga dimensioner|
|storage_percent|Storage-procent|Percent|Average|Storage-procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Gränsen för lagring|Byte|Average|Gränsen för lagring|Inga dimensioner|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Percent|Average|Procent för lagring av Server-loggen|Inga dimensioner|
|serverlog_storage_usage|Server logglagring som används|Byte|Average|Server logglagring som används|Inga dimensioner|
|serverlog_storage_limit|Log storage gränsen|Byte|Average|Log storage gränsen|Inga dimensioner|
|active_connections|Aktiva anslutningar|Count|Average|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga dimensioner|
|seconds_behind_master|Replikeringsfördröjning i sekunder|Count|Average|Replikeringsfördröjning i sekunder|Inga dimensioner|
|backup_storage_used|Används för lagring av säkerhetskopior|Byte|Average|Används för lagring av säkerhetskopior|Inga dimensioner|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|memory_percent|Minne|Percent|Average|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Percent|Average|IO-procent|Inga dimensioner|
|storage_percent|Storage-procent|Percent|Average|Storage-procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Gränsen för lagring|Byte|Average|Gränsen för lagring|Inga dimensioner|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Percent|Average|Procent för lagring av Server-loggen|Inga dimensioner|
|serverlog_storage_usage|Server logglagring som används|Byte|Average|Server logglagring som används|Inga dimensioner|
|serverlog_storage_limit|Log storage gränsen|Byte|Average|Log storage gränsen|Inga dimensioner|
|active_connections|Aktiva anslutningar|Count|Average|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga dimensioner|
|seconds_behind_master|Replikeringsfördröjning i sekunder|Count|Average|Replikeringsfördröjning i sekunder|Inga dimensioner|
|backup_storage_used|Används för lagring av säkerhetskopior|Byte|Average|Används för lagring av säkerhetskopior|Inga dimensioner|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|memory_percent|Minne|Percent|Average|Minne|Inga dimensioner|
|io_consumption_percent|IO-procent|Percent|Average|IO-procent|Inga dimensioner|
|storage_percent|Storage-procent|Percent|Average|Storage-procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Gränsen för lagring|Byte|Average|Gränsen för lagring|Inga dimensioner|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Percent|Average|Procent för lagring av Server-loggen|Inga dimensioner|
|serverlog_storage_usage|Server logglagring som används|Byte|Average|Server logglagring som används|Inga dimensioner|
|serverlog_storage_limit|Log storage gränsen|Byte|Average|Log storage gränsen|Inga dimensioner|
|active_connections|Aktiva anslutningar|Count|Average|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga dimensioner|
|backup_storage_used|Används för lagring av säkerhetskopior|Byte|Average|Används för lagring av säkerhetskopior|Inga dimensioner|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|pg_replica_log_delay_in_seconds|Repliken fördröjning|Sekunder|Maximal|Repliken fördröjning i sekunder|Inga dimensioner|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas|Byte|Maximal|Lag i byte för de mest släpar replik|Inga dimensioner|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|memory_percent|Minne|Percent|Average|Minne|Inga dimensioner|
|IOPs|IOPS|Count|Average|I/o-åtgärder per sekund|Inga dimensioner|
|storage_percent|Storage-procent|Percent|Average|Storage-procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|active_connections|Aktiva anslutningar|Count|Average|Aktiva anslutningar|Inga dimensioner|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetri message send försök|Count|Totalt|Antal meddelanden för enhet till moln-telemetri försökte skickas till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.ingress.success|Telemetrimeddelanden som skickas|Count|Totalt|Antal telemetrimeddelanden från enheten till molnet har skickats till din IoT hub|Inga dimensioner|
|c2d.commands.egress.complete.success|Kommandon har slutförts|Count|Totalt|Antal moln till enhet kommandon har slutförts av enheten|Inga dimensioner|
|c2d.commands.egress.abandon.success|Kommandon övergivna|Count|Totalt|Antal moln till enhet kommandon som avbrutits av enheten|Inga dimensioner|
|c2d.commands.egress.reject.success|Kommandon som avvisats|Count|Totalt|Antal moln till enhet kommandon som avvisats av enheten|Inga dimensioner|
|devices.totalDevices|Totalt antal enheter (inaktuell)|Count|Totalt|Antalet enheter som registrerats till din IoT hub|Inga dimensioner|
|devices.connectedDevices.allProtocol|Anslutna enheter (inaktuell) |Count|Totalt|Antalet enheter som är anslutna till din IoT hub|Inga dimensioner|
|d2c.telemetry.egress.success|Routning: telemetrimeddelanden som levereras|Count|Totalt|Antal gånger som meddelanden levereras till alla slutpunkter med hjälp av IoT Hub routning. Om ett meddelande skickas till flera slutpunkter, ökar detta värde med ett för varje lyckad leverans. Om ett meddelande skickas till samma slutpunkt flera gånger, ökar detta värde med ett för varje lyckad leverans.|Inga dimensioner|
|d2c.telemetry.egress.dropped|Routning: telemetrimeddelanden som tas bort |Count|Totalt|Antal gånger som meddelanden har tagits bort av IoT Hub routning på grund av döda slutpunkter. Det här värdet räknas inte meddelanden som levereras till återställningsplats väg som utelämnade meddelanden inte levereras det.|Inga dimensioner|
|d2c.telemetry.egress.orphaned|Routning: telemetrimeddelanden som överblivna |Count|Totalt|Antal gånger som meddelanden frånkopplade genom IoT Hub routning eftersom de inte matchar några regler för routning (inklusive återställningsplats regeln). |Inga dimensioner|
|d2c.telemetry.egress.invalid|Routning: telemetrimeddelanden inkompatibla|Count|Totalt|Antal gånger som IoT Hub routning inte kunde skicka meddelanden på grund av inkompatibilitet med slutpunkten. Det här värdet inkluderar inte återförsök.|Inga dimensioner|
|d2c.telemetry.egress.fallback|Routning: skicka meddelanden till återställningsplats|Count|Totalt|Antal gånger som IoT Hub routning levererade meddelanden till slutpunkten som är associerade med återställningsplats vägen.|Inga dimensioner|
|d2c.endpoints.egress.eventHubs|Routning: skicka meddelanden till Event Hub|Count|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.eventHubs|Routning: meddelande svarstiden för Event Hub|Millisekunder|Average|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och meddelandet inkommande till en Event Hub-slutpunkt.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusQueues|Routning: skicka meddelanden till Service Bus-kö|Count|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till Service Bus-kö-slutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusQueues|Routning: meddelande svarstid för Service Bus-kö|Millisekunder|Average|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande inkommande i en slutpunkt för Service Bus-kö.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusTopics|Routning: skicka meddelanden till Service Bus-ämne|Count|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till Service Bus-ämnet slutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusTopics|Routning: meddelande svarstid för Service Bus-ämne|Millisekunder|Average|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande inkommande i en slutpunkt för Service Bus-ämne.|Inga dimensioner|
|d2c.endpoints.egress.builtIn.events|Routning: skicka meddelanden till meddelanden/händelser|Count|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till den inbyggda slutpunkten (meddelanden/händelser). Det här måttet startar bara fungerar när en routning är aktiverat (https://aka.ms/iotrouting) för IoT-hubben.|Inga dimensioner|
|d2c.endpoints.latency.builtIn.events|Routning: meddelande svarstiden för meddelanden/händelser|Millisekunder|Average|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande ingående i den inbyggda slutpunkten (meddelanden/händelser). Det här måttet startar bara fungerar när en routning är aktiverat (https://aka.ms/iotrouting) för IoT-hubben.|Inga dimensioner|
|d2c.endpoints.egress.storage|Routning: skicka meddelanden till storage|Count|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till storage-slutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.storage|Routning: meddelande svarstid för lagring|Millisekunder|Average|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande inkommande till en slutpunkt för lagring.|Inga dimensioner|
|d2c.endpoints.egress.storage.bytes|Routning: data som levereras till storage|Byte|Totalt|Hur mycket data (byte) IoT Hub routning levereras till storage-slutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.storage.blobs|Routning: blobar som levereras till storage|Count|Totalt|Antal gånger som IoT Hub routning levereras BLOB storage-slutpunkter.|Inga dimensioner|
|EventGridDeliveries|Event Grid-leveranser (förhandsversion)|Count|Totalt|Antalet IoT Hub-händelser publiceras till Event Grid. Använd dimensionen resultatet för antalet lyckade och misslyckade begäranden. Händelsetyp dimensionen visar vilken typ av händelse (https://aka.ms/ioteventgrid).|Resultatet, händelsetyp|
|EventGridLatency|Genomsnittlig svarstid (millisekunder) från när Iot Hub-händelsen genererades när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelsetyper. Använda dimensionen händelsetyp för att se svarstiden för en viss typ av händelse.|Händelsetyp|
|d2c.twin.read.success|Lyckad twin läser från enheter|Count|Totalt|Antal för alla lyckade enhet-initierad tvilling läser.|Inga dimensioner|
|d2c.twin.read.failure|Det gick inte twin läsningar från enheter|Count|Totalt|Det gick inte att enheten-initierad twin läsningar uppräkning av alla.|Inga dimensioner|
|d2c.twin.read.size|Svarsstorlek för tvilling läser från enheter|Byte|Average|Genomsnitt, min och max för alla lyckade enhet-initierad twin läsningar.|Inga dimensioner|
|d2c.twin.update.success|Lyckad twin uppdateringar från enheter|Count|Totalt|Antal uppdateringar för alla lyckade enhet-initierad enhetstvilling.|Inga dimensioner|
|d2c.twin.update.failure|Det gick inte uppdateringar för enhetstvilling från enheter|Count|Totalt|Det gick inte att uppdateringar för enheten-initierad enhetstvilling uppräkning av alla.|Inga dimensioner|
|d2c.twin.update.size|Storleken på uppdateringar för enhetstvilling från enheter|Byte|Average|Genomsnitt, min och maxstorleken på alla lyckade enhet-initierad twin uppdateringar.|Inga dimensioner|
|c2d.methods.success|Lyckad direkt metod anrop|Count|Totalt|Antal anrop för alla lyckade direkt metod.|Inga dimensioner|
|c2d.methods.failure|Det gick inte direkt metod anrop|Count|Totalt|Uppräkning av alla misslyckade anropen för direkt metod.|Inga dimensioner|
|c2d.methods.requestSize|Storlek på direkt metod anrop|Byte|Average|Genomsnitt, min och max för alla lyckade begäranden för direkt metod.|Inga dimensioner|
|c2d.methods.responseSize|Svarsstorlek för direkt metod anrop|Byte|Average|Genomsnitt, min och max för alla direkt metod lyckades.|Inga dimensioner|
|c2d.twin.read.success|Lyckad twin läser från backend-server|Count|Totalt|Antal för alla lyckade tillbaka-end-initierad tvilling läser.|Inga dimensioner|
|c2d.twin.read.failure|Misslyckade twin läser från backend-server|Count|Totalt|Det gick inte att tillbaka-end-initierad twin läsningar uppräkning av alla.|Inga dimensioner|
|c2d.twin.read.size|Svarsstorlek för tvilling läser från backend-server|Byte|Average|Genomsnitt, min och max för alla lyckade tillbaka-end-initierad twin läsningar.|Inga dimensioner|
|c2d.twin.update.success|Lyckad twin uppdateringar från backend-server|Count|Totalt|Antal uppdateringar för alla lyckade tillbaka-end-initierad enhetstvilling.|Inga dimensioner|
|c2d.twin.update.failure|Misslyckade twin uppdateringar från backend-server|Count|Totalt|Uppräkning av alla misslyckades tillbaka-end-initierad twin uppdateringar.|Inga dimensioner|
|c2d.twin.update.size|Storleken på twin uppdateringar från backend-server|Byte|Average|Genomsnitt, min och maxstorleken på alla lyckade tillbaka-end-initierad twin uppdateringar.|Inga dimensioner|
|twinQueries.success|Lyckad twin frågor|Count|Totalt|Totalt antal alla lyckade twin frågor.|Inga dimensioner|
|twinQueries.failure|Misslyckade twin frågor|Count|Totalt|Totalt antal alla misslyckade twin frågor.|Inga dimensioner|
|twinQueries.resultSize|Twin frågor storlek|Byte|Average|Genomsnitt, min och max för resultatet storleken på alla lyckade twin frågor.|Inga dimensioner|
|jobs.createTwinUpdateJob.success|Lyckad som skapats för tvilling uppdatera jobb|Count|Totalt|Totalt antal alla har skapats på twin uppdateringsjobb.|Inga dimensioner|
|jobs.createTwinUpdateJob.failure|Misslyckade som skapats för tvilling uppdatera jobb|Count|Totalt|Uppräkning av alla misslyckad generering av twin uppdatera jobb.|Inga dimensioner|
|jobs.createDirectMethodJob.success|Lyckad skapande av metoden anrops-jobb|Count|Totalt|Totalt antal alla har skapats på direkt metod anrop jobb.|Inga dimensioner|
|jobs.createDirectMethodJob.failure|Misslyckade skapande av metoden anrops-jobb|Count|Totalt|Totalt antal alla misslyckad generering av direkt metod anrop jobb.|Inga dimensioner|
|jobs.listJobs.success|Utförda anrop lista jobb|Count|Totalt|Antal lyckade anrop lista jobb.|Inga dimensioner|
|jobs.listJobs.failure|De misslyckade anropen lista jobb|Count|Totalt|Antal misslyckade anrop lista jobb.|Inga dimensioner|
|jobs.cancelJob.success|Avbryta lyckade jobb|Count|Totalt|Antal lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobs.cancelJob.failure|Avbryta misslyckade jobb|Count|Totalt|Antal misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobs.queryJobs.success|Lyckad jobbfrågor|Count|Totalt|Antal lyckade anrop till fråga jobb.|Inga dimensioner|
|jobs.queryJobs.failure|Misslyckade jobbfrågor|Count|Totalt|Antal misslyckade anrop till fråga jobb.|Inga dimensioner|
|jobs.completed|Slutförda jobb|Count|Totalt|Totalt antal alla slutförda jobb.|Inga dimensioner|
|jobs.Failed|Misslyckade jobb|Count|Totalt|Totalt antal alla misslyckade jobb.|Inga dimensioner|
|d2c.telemetry.ingress.sendThrottle|Antalet begränsningsfel|Count|Totalt|Begränsar antalet begränsningsfel på grund av enheten dataflöde|Inga dimensioner|
|dailyMessageQuotaUsed|Sammanlagt antal meddelanden som används|Count|Average|Antal Totalt antal meddelanden som används idag. Detta är en ackumulerad värde som nollställs på 00:00 UTC varje dag.|Inga dimensioner|
|deviceDataUsage|Dataanvändning för totalt för enhet|Byte|Totalt|Byte som överfördes till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|totalDeviceCount|Totalt antal enheter (förhandsversion)|Count|Average|Antalet enheter som registrerats till din IoT hub|Inga dimensioner|
|connectedDeviceCount|Anslutna enheter (förhandsversion)|Count|Average|Antalet enheter som är anslutna till din IoT hub|Inga dimensioner|
|Konfigurationer|Konfiguration av mått|Count|Totalt|Mått för konfigurationsåtgärder|Inga dimensioner|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Appresursprovidern|Count|Totalt|Antal försök registreringar för enhet|ProvisioningServiceName IotHubName, Status|
|DeviceAssignments|Tilldelade enheter|Count|Totalt|Antalet enheter som är tilldelade till en IoT hub|ProvisioningServiceName IotHubName|
|AttestationAttempts|Attestering försök|Count|Totalt|Antalet enheter attesteringar försökte|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AvailableStorage|Tillgängligt lagringsutrymme|Byte|Totalt|Totalt tillgängligt lagringsutrymme som rapporteras med 5 minuter Precision|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Cassandra-anslutning öppettider|Count|Totalt|Antalet Cassandra-anslutningar som har stängts rapporteras med en precision på 1 minut|Region, ClosureReason|
|CassandraRequestCharges|Avgifter för Cassandra-begäran|Count|Totalt|Antalet förbrukade ru för Cassandra-begäranden som görs|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra-begäranden|Count|Count|Antalet förfrågningar för Cassandra|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Dataanvändning|Byte|Totalt|Total dataanvändning rapporteras med 5 minuter Precision|CollectionName, DatabaseName, Region|
|DocumentCount|Antal dokument|Count|Totalt|Antal totala dokument rapporteras med 5 minuter Precision|CollectionName, DatabaseName, Region|
|DocumentQuota|Dokumentet kvot|Byte|Totalt|Totala lagringskvoten rapporteras med 5 minuter Precision|CollectionName, DatabaseName, Region|
|IndexUsage|Index användning|Byte|Totalt|Totalt antal index användning per 5 minuter kornighet|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests|Count|Count|Antalet metadataförfrågningar. Cosmos DB upprätthåller samling för varje konto, där du kan räkna upp samlingar, databaser osv och konfigurationer, utan kostnad.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Kostnad för mongo-begäran|Count|Totalt|Mongo-programbegäran som förbrukas|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo-begäranden|Count|Count|Antal Mongo-begäranden gjorda|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Etablerat dataflöde|Count|Maximal|Etablerat dataflöde|DatabaseName, samlingsnamn|
|ReplicationLatency|P99 Replikeringsfördröjning|MilliSeconds|Average|P99 replikeringsfördröjning mellan käll- och regioner för geo-aktiverat konto|SourceRegion, TargetRegion|
|ServiceAvailability|Tjänsttillgänglighet|Percent|Average|Tillgänglighet för begäranden med en timme, dag eller månad Precision|Inga dimensioner|
|TotalRequestUnits|Totalt antal enheter för programbegäran|Count|Totalt|Enheter som används för begäran|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Totalt antal förfrågningar|Count|Count|Antalet begäranden som görs|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Count|Totalt|Totalt antal händelser som publicerats till det här avsnittet|Inga dimensioner|
|PublishFailCount|Publicera misslyckade händelser|Count|Totalt|Det gick inte att publicera till det här avsnittet Totalt antal händelser|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Count|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationer för det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Publicera svarstid för lyckad|Count|Totalt|Publicera lyckad svarstid i millisekunder|Inga dimensioner|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MatchedEventCount|Matchande händelser|Count|Totalt|Totalt antal händelser som matchas mot den här händelseprenumerationen|Inga dimensioner|
|DeliveryAttemptFailCount|Händelser gick inte att leverera|Count|Totalt|Det gick inte att leverera till den här händelseprenumerationen Totalt antal händelser|Fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Count|Totalt|Totalt antal händelser som levereras till den här händelseprenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|Bearbetningstid för mål|Millisekunder|Average|Bearbetningstid för målet i millisekunder|Inga dimensioner|
|DroppedEventCount|Avbrutna händelser|Count|Totalt|Totalt antal avbrutna händelser som matchar den här händelseprenumerationen|DropReason|
|DeadLetteredCount|Död bokstäver händelser|Count|Totalt|Totalt antal död bokstäver händelser som matchar den här händelseprenumerationen|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Count|Totalt|Totalt antal händelser som publicerats till det här avsnittet|Inga dimensioner|
|PublishFailCount|Misslyckade händelser|Count|Totalt|Det gick inte att publicera till det här avsnittet Totalt antal händelser|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Count|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationer för det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Publicera svarstid för lyckad|Count|Totalt|Publicera lyckad svarstid i millisekunder|Inga dimensioner|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade begäranden|Count|Totalt|Lyckade begäranden för Microsoft.EventHub.|EntityName, |
|ServerErrors|Serverfel.|Count|Totalt|Serverfel för Microsoft.EventHub.|EntityName, |
|UserErrors|Användarfel.|Count|Totalt|Användarfel för Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Kvoten överskridits-fel.|Count|Totalt|Kvoten överskridits-fel för Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Begränsade begäranden.|Count|Totalt|Begränsade begäranden för Microsoft.EventHub.|EntityName, |
|IncomingRequests|Inkommande begäranden|Count|Totalt|Inkommande begäranden för Microsoft.EventHub.|EntityName|
|IncomingMessages|Inkommande meddelanden|Count|Totalt|Inkommande meddelanden för Microsoft.EventHub.|EntityName|
|OutgoingMessages|Utgående meddelanden|Count|Totalt|Utgående meddelanden för Microsoft.EventHub.|EntityName|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft.EventHub.|EntityName|
|OutgoingBytes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Totalt antal aktiva anslutningar för Microsoft.EventHub.|Inga dimensioner|
|ConnectionsOpened|Öppnade anslutningar.|Count|Average|Öppnade Microsoft.eventhub-anslutningar.|EntityName|
|ConnectionsClosed|Stängda anslutningar.|Count|Average|Stängda Microsoft.eventhub-anslutningar.|EntityName|
|CaptureBacklog|Hämta kvarvarande uppgifter.|Count|Totalt|Hämta kvarvarande uppgifter för Microsoft.EventHub.|EntityName|
|CapturedMessages|Hämtade meddelanden.|Count|Totalt|Hämtade meddelanden för Microsoft.EventHub.|EntityName|
|CapturedBytes|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft.EventHub.|EntityName|
|Size|Size|Byte|Average|Storleken på en EventHub i byte.|EntityName|
|INREQS|Inkommande begäranden (inaktuell)|Count|Totalt|Totalt antal inkommande sändningsförfrågningar för ett namnområde (inaktuell)|Inga dimensioner|
|SUCCREQ|Lyckade begäranden (inaktuell)|Count|Totalt|Totalt antal slutförda begäranden för ett namnområde (inaktuell)|Inga dimensioner|
|FAILREQ|Misslyckade begäranden (inaktuell)|Count|Totalt|Totalt antal misslyckade begäranden för ett namnområde (inaktuell)|Inga dimensioner|
|SVRBSY|Upptagen server (inaktuell)|Count|Totalt|Totalt antal fel med upptagen server för ett namnområde (inaktuell)|Inga dimensioner|
|INTERR|Interna serverfel (inaktuell)|Count|Totalt|Totalt antal interna serverfel för ett namnområde (inaktuell)|Inga dimensioner|
|MISCERR|Andra fel (inaktuell)|Count|Totalt|Totalt antal misslyckade begäranden för ett namnområde (inaktuell)|Inga dimensioner|
|INMSGS|Inkommande meddelanden (inaktuellt) (inaktuell)|Count|Totalt|Totalt antal inkommande meddelanden för ett namnområde. Det här mätvärdet är inaktuellt. Använd inkommande meddelanden istället (inaktuell)|Inga dimensioner|
|EHINMSGS|Inkommande meddelanden (inaktuellt)|Count|Totalt|Totalt antal inkommande meddelanden för ett namnområde (inaktuell)|Inga dimensioner|
|OUTMSGS|Utgående meddelanden (inaktuellt) (inaktuell)|Count|Totalt|Totalt antal utgående meddelanden för ett namnområde. Det här mätvärdet är inaktuellt. Använd utgående meddelanden istället (inaktuell)|Inga dimensioner|
|EHOUTMSGS|Utgående meddelanden (inaktuellt)|Count|Totalt|Totalt antal utgående meddelanden för ett namnområde (inaktuell)|Inga dimensioner|
|EHINMBS|Inkommande byte (inaktuellt) (inaktuell)|Byte|Totalt|Inkommande meddelandedataflöde för Händelsehubb för ett namnområde. Det här mätvärdet är inaktuellt. Använd inkommande byte istället (inaktuell)|Inga dimensioner|
|EHINBYTES|Inkommande byte (inaktuellt)|Byte|Totalt|Inkommande meddelandedataflöde för Händelsehubb för ett namnområde (inaktuell)|Inga dimensioner|
|EHOUTMBS|Utgående byte (inaktuellt) (inaktuell)|Byte|Totalt|Utgående meddelandedataflöde för Händelsehubb för ett namnområde. Det här mätvärdet är inaktuellt. Använd utgående byte istället (inaktuell)|Inga dimensioner|
|EHOUTBYTES|Utgående byte (inaktuellt)|Byte|Totalt|Utgående meddelandedataflöde för Händelsehubb för ett namnområde (inaktuell)|Inga dimensioner|
|EHABL|Arkivera kvarvarande meddelanden (inaktuell)|Count|Totalt|Arkiverade meddelanden för Händelsehubb i kvarvarande uppgifter för ett namnområde (inaktuell)|Inga dimensioner|
|EHAMSGS|Arkivera meddelanden (inaktuell)|Count|Totalt|Event Hub arkiverade meddelanden i en namnrymd (inaktuell)|Inga dimensioner|
|EHAMBS|Arkivera meddelandedataflöde (inaktuell)|Byte|Totalt|Dataflöde arkiverade meddelanden för Händelsehubb i ett namnområde (inaktuell)|Inga dimensioner|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade begäranden (förhandsversion)|Count|Totalt|Lyckade begäranden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ServerErrors|Serverfel. (Förhandsversion)|Count|Totalt|Serverfel för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|UserErrors|Användarfel. (Förhandsversion)|Count|Totalt|Användarfel för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|QuotaExceededErrors|Kvoten överskridits-fel. (Förhandsversion)|Count|Totalt|Kvoten överskridits-fel för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ThrottledRequests|Begränsade begäranden. (Förhandsversion)|Count|Totalt|Begränsade begäranden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|IncomingRequests|Inkommande förfrågningar (förhandsversion)|Count|Totalt|Inkommande begäranden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|IncomingMessages|Inkommande meddelanden (förhandsversion)|Count|Totalt|Inkommande meddelanden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|OutgoingMessages|Utgående meddelanden (förhandsversion)|Count|Totalt|Utgående meddelanden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|IncomingBytes|Inkommande byte. (Förhandsversion)|Byte|Totalt|Inkommande byte för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|OutgoingBytes|Utgående byte. (Förhandsversion)|Byte|Totalt|Utgående byte för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ActiveConnections|ActiveConnections (förhandsversion)|Count|Average|Totalt antal aktiva anslutningar för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|ConnectionsOpened|Öppnade anslutningar. (Förhandsversion)|Count|Average|Öppnade Microsoft.eventhub-anslutningar. (Förhandsversion)|Inga dimensioner|
|ConnectionsClosed|Stängda anslutningar. (Förhandsversion)|Count|Average|Stängda Microsoft.eventhub-anslutningar. (Förhandsversion)|Inga dimensioner|
|CaptureBacklog|Hämta kvarvarande uppgifter. (Förhandsversion)|Count|Totalt|Hämta kvarvarande uppgifter för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|CapturedMessages|Hämtade meddelanden. (Förhandsversion)|Count|Totalt|Hämtade meddelanden för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|CapturedBytes|Hämtade byte. (Förhandsversion)|Byte|Totalt|Hämtade byte för Microsoft.EventHub. (Förhandsversion)|Inga dimensioner|
|Processor|CPU (förhandsversion)|Percent|Maximal|CPU-belastningen för Event Hub-klustret som en procentandel|Role|
|AvailableMemory|Tillgängligt minne (förhandsversion)|Count|Maximal|Tillgängligt minne för Event Hub-klustret i byte|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway-begäranden|Count|Totalt|Antal gateway-begäranden|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategoriserat Gateway-begäranden|Count|Totalt|Antal gatewaybegäranden efter kategorier (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Antal aktiva medarbetare|Count|Maximal|Antal aktiva medarbetare|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerat mätvärde|Count|Average|Värdet som beräknas av autoskalning vid körning|MetricTriggerSource|
|MetricThreshold|Tröskelvärde för mått|Count|Average|Det inställda tröskelvärdet när autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Observerad kapacitet|Count|Average|Kapaciteten som rapporteras till autoskalning vid körning.|Inga dimensioner|
|ScaleActionsInitiated|Skalningsåtgärder har initierats|Count|Totalt|Riktningen på skalningsåtgärden.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Förhandsversion)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Tillgänglighet|Procent|Average|Procentandel av har slutförts tillgänglighetstester|availabilityResult/name availabilityResult/plats|
|availabilityResults/antal|Tillgänglighetstester|Count|Count|Antal tillgänglighetstester|availabilityResult/name, availabilityResult/plats, availabilityResult/lyckades|
|availabilityResults/varaktighet|Varaktigheten för tillgänglighetstestet|MilliSeconds|Average|Varaktigheten för tillgänglighetstestet|availabilityResult/name, availabilityResult/plats, availabilityResult/lyckades|
|browserTimings/networkDuration|Nätverket connect sidinläsningstiden|MilliSeconds|Average|Tiden mellan användarförfrågning och användaranslutning. Innehåller DNS-sökning och transportanslutning anslutning.|Inga dimensioner|
|browserTimings/processingDuration|Klientbehandlingstid|MilliSeconds|Average|Tiden mellan ta emot de sista byten av ett dokument till dess att DOM har lästs. Asynkrona begäranden kan fortfarande vara under bearbetning.|Inga dimensioner|
|browserTimings/receiveDuration|Tar emot svarstid|MilliSeconds|Average|Tid mellan den första och sista byten, eller till frånkoppling.|Inga dimensioner|
|browserTimings/sendDuration|Skicka tid för begäran|MilliSeconds|Average|Tiden mellan nätverksanslutning och mottagande den första byten.|Inga dimensioner|
|browserTimings/totalDuration|Webbsideinläsningar|MilliSeconds|Average|Tiden från användarförfrågan till dess att DOM, formatmallar, skript och bilder har lästs in.|Inga dimensioner|
|beroenden/antal|Beroendeanrop|Count|Count|Antal anrop gjorda av programmet till externa resurser.|beroendetyp /, beroende/performanceBucket, beroende/lyckades, åtgärden/syntetiska, molnet/Rollinstans, molnet/roleName|
|beroenden/varaktighet|Beroendevaraktighet|MilliSeconds|Average|Varaktighet för anrop gjorda av programmet till externa resurser.|beroendetyp /, beroende/performanceBucket, beroende/lyckades, åtgärden/syntetiska, molnet/Rollinstans, molnet/roleName|
|beroenden/misslyckades|Beroendefel för anrop|Count|Count|Totalt antal misslyckade beroendeanrop från programmet till externa resurser.|beroendetyp /, beroende/performanceBucket, åtgärden/syntetiska, molnet/Rollinstans, molnet/roleName|
|pageViews/count|Sidvisningar|Count|Count|Antal sidvisningar.|operation/synthetic|
|pageViews/varaktighet|Inläsningstid för Sidvisning|MilliSeconds|Average|Inläsningstid för Sidvisning|operation/synthetic|
|performanceCounters/requestExecutionTime|Körningstid för HTTP-begäran|MilliSeconds|Average|Körningstid för den senaste begäran.|molnet/Rollinstans|
|performanceCounters/requestsInQueue|HTTP-förfrågningar i programkö|Count|Average|Längden på programbegärandekön.|molnet/Rollinstans|
|performanceCounters/requestsPerSecond|HTTP-förfrågan|CountPerSecond|Average|Hastighet för alla förfrågningar till programmet per sekund från ASP.NET.|molnet/Rollinstans|
|performanceCounters/exceptionsPerSecond|antal undantag|CountPerSecond|Average|Antal hanterade och ohanterade undantag som rapporterats till windows, inklusive .NET-undantag och ohanterade undantag som har konverterats till .NET-undantag.|molnet/Rollinstans|
|performanceCounters/processIOBytesPerSecond|Behandlingstakten för i/o|BytesPerSecond|Average|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|molnet/Rollinstans|
|performanceCounters/processCpuPercentage|Processoranvändning för process|Percent|Average|Procentandelen av förfluten tid som alla processtrådar använde i processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet indikerar prestandan för w3wp-processen ensam.|molnet/Rollinstans|
|performanceCounters/processorCpuPercentage|Processortid|Percent|Average|Procentandelen av tiden som processorn ägnat åt icke-inaktiva trådar.|molnet/Rollinstans|
|performanceCounters/memoryAvailableBytes|Tillgängligt minne|Byte|Average|Fysiskt minne som är omedelbart tillgängligt för allokering till en process eller för systemanvändning.|molnet/Rollinstans|
|performanceCounters/processPrivateBytes|Privata byte för process|Byte|Average|Minne som tilldelats exklusivt för att övervaka programprocesser.|molnet/Rollinstans|
|begäranden/varaktighet|Svarstid för servern|MilliSeconds|Average|Tiden mellan ta emot en HTTP-begäran och slutför att svaret.|begäran/performanceBucket, begäran/Resultatkod, åtgärden/syntetiska, molnet/Rollinstans, begäran/lyckades, molnet/roleName|
|antal och tjänstbegäranden|Serverbegäranden|Count|Count|Antal HTTP-begäranden som har slutförts.|begäran/performanceBucket, begäran/Resultatkod, åtgärden/syntetiska, molnet/Rollinstans, begäran/lyckades, molnet/roleName|
|begäranden/misslyckades|Misslyckade förfrågningar|Count|Count|Antal HTTP-begäranden markeras som misslyckat. I de flesta fall är detta förfrågningar med svarskoden > = 400 och inte lika med 401.|begäran/performanceBucket, begäran/Resultatkod, åtgärden/syntetiska, molnet/Rollinstans, molnet/roleName|
|begäranden/hastighet|Server-begärandehastighet|CountPerSecond|Average|Antal serverbegäranden per sekund|begäran/performanceBucket, begäran/Resultatkod, åtgärden/syntetiska, molnet/Rollinstans, begäran/lyckades, molnet/roleName|
|undantag/antal|Undantag|Count|Count|Kombinerat antal undantagsfel utan felhantering.|molnet/roleName, molnet/Rollinstans, klient/typ|
|undantag/webbläsare|Webbläsarundantag|Count|Count|Antal undantagsfel som utlösts i webbläsaren.|Inga dimensioner|
|undantag/server|Serverundantag|Count|Count|Antal undantagsfel som utlösts i serverprogrammet.|molnet/roleName, molnet/Rollinstans|
|spårningar/antal|spårningar|Count|Count|Antal spårningsdokument|spårningen/severityLevel, åtgärden/syntetiska, molnet/roleName, molnet/Rollinstans|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Totalt antal Service Api-träffar|Count|Count|Totalt antal service api-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Total svarstid för Service Api|Millisekunder|Average|Total svarstid för service api-begäranden|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Totalt antal Service Api-resultat|Count|Count|Totalt antal service api-resultat|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CacheUtilization|Cache-användning|Percent|Average|På en nivå i omfånget för kluster|Inga|
|QueryDuration|Frågevaraktighet|Millisekunder|Average|Frågor varaktighet i sekunder|Frågestatus|
|IngestionUtilization|Datainmatning användning|Percent|Average|Förhållandet mellan används inmatning kortplatser i klustret|Inga|
|KeepAlive|Keep Alive|Count|Average|Kontrollera förstånd anger klustret svarar på frågor|Inga|
|IngestionVolumeInMB|Datainmatning volymen (i MB)|Count|Totalt|Totala mängden insamlade data i klustret (i MB)|Databas|
|IngestionLatencyInSeconds|Datainmatningssvarstid (i sekunder)|Sekunder|Average|Datainmatning tid från källan (t.ex. meddelandet är EventHub) till klustret i sekunder|Ingen|
|EventProcessedForEventHubs|Händelser som bearbetas (för Event Hubs)|Count|Totalt|Antalet händelser som bearbetas av klustret om mata in från Event Hub|Inga|
|IngestionResult|Datainmatning resultat|Count|Count|Antal åtgärder för inmatning|Status|
|Processor|Processor|Percent|Average|Processor på en nivå|Inga|
| ContinuousExportNumOfRecordsExported | Antalet poster som exporteras i löpande export | Count | Totalt | Antalet poster som har exporterats för varje storage artefakten skrivs under exportåtgärden  | Inga |
| ExportUtilization | Exportera användning | Percent | Maximal | Exportera användning | Ingen |
| ContinuousExportPendingCount | Löpande Export väntande antal | Count | Maximal | Antalet väntande kontinuerlig exportera jobb som är redo för körning | Ingen |
| ContinuousExportMaxLatenessMinutes | Löpande Export Max Lateness minuter | Count | Maximal | Den maximala tid i minuter för alla kontinuerlig export som är väntande och redo för körning | Inga |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Count|Count|Antal API-anrop|ApiCategory ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Count|Totalt|Antal arbetsflödeskörningar startade.|Inga dimensioner|
|RunsCompleted|Slutförda körningar|Count|Totalt|Antal arbetsflödeskörningar slutförda.|Inga dimensioner|
|RunsSucceeded|Lyckade körningar|Count|Totalt|Antal lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsFailed|Misslyckade körningar|Count|Totalt|Antal arbetsflödeskörningar misslyckade.|Inga dimensioner|
|RunsCancelled|Avbrutna körningar|Count|Totalt|Antal arbetsflödeskörningar avbröts.|Inga dimensioner|
|RunLatency|Svarstid för körning|Sekunder|Average|Svarstid för slutförda körs.|Inga dimensioner|
|RunSuccessLatency|Svarstid för lyckad körning|Sekunder|Average|Svarstid för lyckade arbetsflödeskörningar körs.|Inga dimensioner|
|RunThrottledEvents|Kör begränsade händelser|Count|Totalt|Antal arbetsflödesåtgärds- eller begränsade händelser.|Inga dimensioner|
|RunFailurePercentage|Kör felprocent|Percent|Totalt|Misslyckade arbetsflödeskörningar arbetsflödeskörningar i procent.|Inga dimensioner|
|ActionsStarted|Startade åtgärder |Count|Totalt|Antal arbetsflödesåtgärder startades.|Inga dimensioner|
|ActionsCompleted|Slutförda åtgärder |Count|Totalt|Antal slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSucceeded|Lyckade åtgärder |Count|Totalt|Antalet arbetsflödesåtgärder lyckades.|Inga dimensioner|
|ActionsFailed|Misslyckade åtgärder|Count|Totalt|Det gick inte att antalet arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSkipped|Överhoppade åtgärder |Count|Totalt|Antalet arbetsflödesåtgärder hoppades över.|Inga dimensioner|
|ActionLatency|Svarstid för åtgärd |Sekunder|Average|Svarstid för slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionSuccessLatency|Svarstid för lyckad åtgärd |Sekunder|Average|Svarstid för lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionThrottledEvents|Begränsade händelser för åtgärder|Count|Totalt|Antal arbetsflödesåtgärden begränsade händelser...|Inga dimensioner|
|TriggersStarted|Startade utlösare |Count|Totalt|Antal startade arbetsflödesutlösare.|Inga dimensioner|
|TriggersCompleted|Slutförda utlösare |Count|Totalt|Antal slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggersSucceeded|Lyckade utlösare |Count|Totalt|Många utlösare har slutförts.|Inga dimensioner|
|TriggersFailed|Misslyckade utlösare |Count|Totalt|Det gick inte att antalet arbetsflödesutlösare.|Inga dimensioner|
|TriggersSkipped|Ignorerade utlösare|Count|Totalt|Antal arbetsflödesutlösare hoppades över.|Inga dimensioner|
|TriggersFired|Aktiverade utlösare |Count|Totalt|Många utlösare utlöses.|Inga dimensioner|
|TriggerLatency|Latensutlösare |Sekunder|Average|Svarstid för slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggerFireLatency|Brandlatensutlösare |Sekunder|Average|Svartstid för utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerSuccessLatency|Framgångslatensutlösare |Sekunder|Average|Svarstid för lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggerThrottledEvents|Utlösare för begränsade händelser|Count|Totalt|Antal arbetsflödesutlösaren begränsade händelser.|Inga dimensioner|
|BillableActionExecutions|Fakturerbara Åtgärdskörningar|Count|Totalt|Antal körningar av arbetsflödesåtgärder som faktureras.|Inga dimensioner|
|BillableTriggerExecutions|Fakturerbara Utlösarkörningar|Count|Totalt|Antal körningar faktureras.|Inga dimensioner|
|TotalBillableExecutions|Totalt antal fakturerbara körningar|Count|Totalt|Antal arbetsflödeskörningar som faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Debiterad användning för interna åtgärden körningar|Count|Totalt|Antal interna åtgärden körningar som faktureras.|Inga dimensioner|
|BillingUsageStandardConnector|Debiterad användning för Standardanslutningsprogram|Count|Totalt|Antal standardanslutningsprogram faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Debiterad användning för Storage förbrukning körningar|Count|Totalt|Antal storage förbrukning körningar faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Debiterad användning för interna åtgärden körningar|Count|Totalt|Antal interna åtgärden körningar som faktureras.|Inga dimensioner|
|BillingUsageStandardConnector|Debiterad användning för Standardanslutningsprogram|Count|Totalt|Antal standardanslutningsprogram faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Debiterad användning för Storage förbrukning körningar|Count|Totalt|Antal storage förbrukning körningar faktureras.|Inga dimensioner|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Count|Totalt|Antal arbetsflödeskörningar startade.|Inga dimensioner|
|RunsCompleted|Slutförda körningar|Count|Totalt|Antal arbetsflödeskörningar slutförda.|Inga dimensioner|
|RunsSucceeded|Lyckade körningar|Count|Totalt|Antal lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsFailed|Misslyckade körningar|Count|Totalt|Antal arbetsflödeskörningar misslyckade.|Inga dimensioner|
|RunsCancelled|Avbrutna körningar|Count|Totalt|Antal arbetsflödeskörningar avbröts.|Inga dimensioner|
|RunLatency|Svarstid för körning|Sekunder|Average|Svarstid för slutförda körs.|Inga dimensioner|
|RunSuccessLatency|Svarstid för lyckad körning|Sekunder|Average|Svarstid för lyckade arbetsflödeskörningar körs.|Inga dimensioner|
|RunThrottledEvents|Kör begränsade händelser|Count|Totalt|Antal arbetsflödesåtgärds- eller begränsade händelser.|Inga dimensioner|
|RunStartThrottledEvents|Kör Start begränsade händelser|Count|Totalt|Antal körningsstart begränsade händelser.|Inga dimensioner|
|RunFailurePercentage|Kör felprocent|Percent|Totalt|Misslyckade arbetsflödeskörningar arbetsflödeskörningar i procent.|Inga dimensioner|
|ActionsStarted|Startade åtgärder |Count|Totalt|Antal arbetsflödesåtgärder startades.|Inga dimensioner|
|ActionsCompleted|Slutförda åtgärder |Count|Totalt|Antal slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSucceeded|Lyckade åtgärder |Count|Totalt|Antalet arbetsflödesåtgärder lyckades.|Inga dimensioner|
|ActionsFailed|Misslyckade åtgärder |Count|Totalt|Det gick inte att antalet arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSkipped|Överhoppade åtgärder |Count|Totalt|Antalet arbetsflödesåtgärder hoppades över.|Inga dimensioner|
|ActionLatency|Svarstid för åtgärd |Sekunder|Average|Svarstid för slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionSuccessLatency|Svarstid för lyckad åtgärd |Sekunder|Average|Svarstid för lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionThrottledEvents|Begränsade händelser för åtgärder|Count|Totalt|Antal arbetsflödesåtgärden begränsade händelser...|Inga dimensioner|
|TriggersStarted|Startade utlösare |Count|Totalt|Antal startade arbetsflödesutlösare.|Inga dimensioner|
|TriggersCompleted|Slutförda utlösare |Count|Totalt|Antal slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggersSucceeded|Lyckade utlösare |Count|Totalt|Många utlösare har slutförts.|Inga dimensioner|
|TriggersFailed|Misslyckade utlösare |Count|Totalt|Det gick inte att antalet arbetsflödesutlösare.|Inga dimensioner|
|TriggersSkipped|Ignorerade utlösare|Count|Totalt|Antal arbetsflödesutlösare hoppades över.|Inga dimensioner|
|TriggersFired|Aktiverade utlösare |Count|Totalt|Många utlösare utlöses.|Inga dimensioner|
|TriggerLatency|Latensutlösare |Sekunder|Average|Svarstid för slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggerFireLatency|Brandlatensutlösare |Sekunder|Average|Svartstid för utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerSuccessLatency|Framgångslatensutlösare |Sekunder|Average|Svarstid för lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggerThrottledEvents|Utlösare för begränsade händelser|Count|Totalt|Antal arbetsflödesutlösaren begränsade händelser.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Arbetsflöde för processoranvändning för Integreringstjänstmiljö|Percent|Average|Arbetsflöde för processoranvändning för integreringstjänstmiljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Arbetsflöde för minnesanvändning för Integreringstjänstmiljö|Percent|Average|Arbetsflöde för minnesanvändning för integreringstjänstmiljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Connector processoranvändning för Integreringstjänstmiljö|Percent|Average|Connector processoranvändning för integreringstjänstmiljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Connector minnesanvändning för Integreringstjänstmiljö|Percent|Average|Connector minnesanvändning för integreringstjänstmiljön.|Inga dimensioner|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Slutförda körningar|Slutförda körningar|Count|Totalt|Antal arbetsflödeskörningar slutförda har för den här arbetsytan|Scenario|
|Startade körningar|Startade körningar|Count|Totalt|Antal kör igång för den här arbetsytan|Scenario|
|Misslyckade körningar|Misslyckade körningar|Count|Totalt|Antal körs misslyckades för den här arbetsytan|Scenario|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Count|Count|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|
|Tillgänglighet|Tillgänglighet|Procent|Average|Tillgänglighet för API: er|ApiCategory ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageOtherLatency|Genomsnittlig svarstid för andra|ms/op|Average|Genomsnittlig andra svarstid (som inte är läsning eller skrivning) i millisekunder per åtgärd|Inga dimensioner|
|AverageReadLatency|Genomsnittlig läsfördröjning|ms/op|Average|Genomsnittlig läsa svarstid i millisekunder per åtgärd|Inga dimensioner|
|AverageTotalLatency|Genomsnittlig svarstid för total|ms/op|Average|Genomsnittlig total svarstid i millisekunder per åtgärd|Inga dimensioner|
|AverageWriteLatency|Genomsnittligt antal skrivåtgärder svarstid|ms/op|Average|Genomsnittligt antal skrivåtgärder svarstid i millisekunder per åtgärd|Inga dimensioner|
|FilesystemOtherOps|Filsystem övriga åtgärder|OPS|Average|Antal filsystem övriga åtgärder (som inte är läsning eller skrivning)|Inga dimensioner|
|FilesystemReadOps|Läsåtgärder för filsystem|OPS|Average|Antal filsystem läsåtgärder|Inga dimensioner|
|FilesystemTotalOps|Totalt antal åtgärder för filsystem|OPS|Average|Summan av alla filsystemsåtgärder|Inga dimensioner|
|FilesystemWriteOps|Filsystem skrivning ops|OPS|Average|Antalet skrivåtgärder för filsystem|Inga dimensioner|
|IoBytesPerOtherOps|I/o-byte per övriga åtgärder|byte/op|Average|Många av i/ut byte per övriga åtgärder (som inte är läsning eller skrivning)|Inga dimensioner|
|IoBytesPerReadOps|I/o-byte per Läs ops|byte/op|Average|Antalet byte per Läsåtgärden In/ut|Inga dimensioner|
|IoBytesPerTotalOps|I/o-byte per op över alla åtgärder|byte/op|Average|Summan av alla In/ut byte åtgärden|Inga dimensioner|
|IoBytesPerWriteOps|I/o-byte per skrivning ops|byte/op|Average|Många av i/ut byte per skrivåtgärd|Inga dimensioner|
|OtherIops|Andra iops|åtgärder per sekund|Average|Andra In/ut åtgärd per sekund|Inga dimensioner|
|OtherThroughput|Andra dataflöde|Mbit/s|Average|Andra dataflöde (som inte är läsning eller skrivning) i megabyte per sekund|Inga dimensioner|
|ReadIops|Läsa iops|åtgärder per sekund|Average|Läsa In/ut-åtgärder per sekund|Inga dimensioner|
|ReadThroughput|Läs dataflöde|Mbit/s|Average|Läsa dataflöde i megabyte per sekund|Inga dimensioner|
|TotalIops|Totalt antal iops|åtgärder per sekund|Average|Summan av alla In/ut-åtgärder per sekund|Inga dimensioner|
|TotalThroughput|Totalt dataflöde|Mbit/s|Average|Summan av alla dataflöde i megabyte per sekund|Inga dimensioner|
|VolumeAllocatedSize|Volymen som allokerats storlek|byte|Average|Allokerade storleken på volymen (inte den faktiska används byte)|Inga dimensioner|
|VolumeLogicalSize|Logisk storlek|byte|Average|Logiska storleken på volymen (används byte)|Inga dimensioner|
|VolumeSnapshotSize|Volymstorlek för ögonblicksbild|byte|Average|Storleken på alla ögonblicksbilder i volym|Inga dimensioner|
|WriteIops|Skriva iops|åtgärder per sekund|Average|Skriv In/ut-åtgärder per sekund|Inga dimensioner|
|WriteThroughput|Skriva dataflöde|Mbit/s|Average|Skriva dataflöde i megabyte per sekund|Inga dimensioner|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volymstorlek för poolen som allokerats|byte|Average|Allokerade storleken på poolen (inte den faktiska används byte)|Inga dimensioner|
|VolumePoolAllocatedUsed|Volymen poolen allokeras används|byte|Average|Allokerade storleken på poolen som används|Inga dimensioner|
|VolumePoolTotalLogicalSize|Volymen Totalt antal logiska poolstorlek|byte|Average|Summan av den logiska storleken på alla volymer som hör till poolen|Inga dimensioner|
|VolumePoolTotalSnapshotSize|Volymstorlek pool totala ögonblicksbild|byte|Average|Summan av alla ögonblicksbilder i pool|Inga dimensioner|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesSentRate|Skickade byte|Count|Totalt|Antalet byte som skickas nätverksgränssnittet|Inga dimensioner|
|BytesReceivedRate|Mottagna byte|Count|Totalt|Antalet byte nätverksgränssnittet som tagits emot|Inga dimensioner|
|PacketsSentRate|Paket som skickats|Count|Totalt|Antal paket nätverksgränssnittet skickas|Inga dimensioner|
|PacketsReceivedRate|Mottagna paket|Count|Totalt|Antal paket nätverksgränssnittet som tagits emot|Inga dimensioner|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|Tillgänglighet för data-sökväg|Count|Average|Genomsnittlig belastningsutjämnaren data sökväg tillgänglighet per varaktighet|FrontendIPAddress, FrontendPort|
|DipAvailability|Hälsostatus för avsökning|Count|Average|Genomsnittlig belastningsutjämnaren avsökningen hälsostatus per varaktighet|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Antal byte|Count|Totalt|Sammanlagt antal byte som skickas inom tidsperioden|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Antal paket|Count|Totalt|Totalt antal paket som skickas inom tidsperioden|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN-antal|Count|Totalt|Totalt antal SYN-paket som skickas inom tidsperioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Antal för SNAT-anslutningar|Count|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tidsperioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allokerade SNAT portar (förhandsversion)|Count|Totalt|Totalt antal SNAT-portar som allokerats inom tidsperioden|FrontendIPAddress, BackendIPAddress, protokolltyp|
|UsedSnatPorts|Använda SNAT portar (förhandsversion)|Count|Totalt|Totalt antal SNAT-portar som används inom tidsperioden|FrontendIPAddress, BackendIPAddress, protokolltyp|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Fråga volym|Count|Totalt|Antal frågor som hanteras för en DNS-zon|Inga dimensioner|
|RecordSetCount|Antal för uppsättningen av poster|Count|Maximal|Antal postuppsättningar i en DNS-zon|Inga dimensioner|
|RecordSetCapacityUtilization|Kapacitetsutnyttjande för uppsättningen av poster|Percent|Maximal|Procent av postuppsättning kapacitet som används av en DNS-zon|Inga dimensioner|

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
|IfUnderDDoSAttack|Under DDoS angrepp eller inte|Count|Maximal|Under DDoS angrepp eller inte|Inga dimensioner|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-åtgärd|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-åtgärd|Inga dimensioner|
|DDoSTriggerUDPPackets|Inkommande UDP-paket för att utlösa DDoS-åtgärd|CountPerSecond|Maximal|Inkommande UDP-paket för att utlösa DDoS-åtgärd|Inga dimensioner|
|DDoSTriggerSYNPackets|Inkommande SYN-paket för att utlösa DDoS-åtgärd|CountPerSecond|Maximal|Inkommande SYN-paket för att utlösa DDoS-åtgärd|Inga dimensioner|
|VipAvailability|Tillgänglighet för data-sökväg|Count|Average|Genomsnittlig tillgänglighet för IP-adress per varaktighet|Port|
|ByteCount|Antal byte|Count|Totalt|Sammanlagt antal byte som skickas inom tidsperioden|Port, riktning|
|PacketCount|Antal paket|Count|Totalt|Totalt antal paket som skickas inom tidsperioden|Port, riktning|
|SynCount|SYN-antal|Count|Totalt|Totalt antal SYN-paket som skickas inom tidsperioden|Port, riktning|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ApplicationRuleHit|Antal träffar för regler för program|Count|Totalt|Antal gånger som har träffar för regler för program|Status, Reason, Protocol|
|NetworkRuleHit|Antal träffar nätverk i regler|Count|Totalt|Antal gånger som Nätverksregler nåddes|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Totalt|Antalet byte per sekund som Application Gateway har hämtats|Inga dimensioner|
|UnhealthyHostCount|Defekt antal värdar per Backendpool|Count|Average|Antalet felaktiga backend-värdar|BackendSettingsPool|
|HealthyHostCount|Felfri värd-antal|Count|Average|Antalet felfri backend-värdar|BackendSettingsPool|
|TotalRequests|Totalt antal förfrågningar|Count|Totalt|Antal lyckade begäranden som Application Gateway har hämtats|BackendSettingsPool|
|FailedRequests|Misslyckade begäranden|Count|Totalt|Antal misslyckade begäranden som Application Gateway har hämtats|BackendSettingsPool|
|ResponseStatus|Svarsstatus|Count|Totalt|HTTP-svarsstatus som returneras av Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuella anslutningar|Count|Totalt|Antal aktuella anslutningar som upprättats via Application Gateway|Inga dimensioner|
|CapacityUnits|Aktuella kapacitetsenheter|Count|Average|Kapacitetsenheter som används|Inga dimensioner|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S bandbredd|BytesPerSecond|Average|Genomsnittlig plats-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SBandwidth|P2S bandbredden|BytesPerSecond|Average|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Antal för P2S-anslutningar|Count|Maximal|Antal för punkt-till-plats-anslutning för en gateway|Protocol|
|TunnelAverageBandwidth|Tunnel bandbredd|BytesPerSecond|Average|Genomsnittlig bandbredd en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tunnel utgående byte|Byte|Totalt|Utgående byte en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tunnel inkommande byte|Byte|Totalt|Inkommande byte en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel utgående paket|Count|Totalt|Utgående paket antal en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel inkommande paket|Count|Totalt|Inkommande paket antal en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel utgående TS matchningsfel paket släpp|Count|Totalt|Utgående paket släpp ökas från trafik väljare matchningsfel en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel ingående TS matchningsfel paket släpp|Count|Totalt|Inkommande paket släpp ökas från trafik väljare matchningsfel en tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|BITS ingressing Azure per sekund|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|BITS egressing Azure per sekund|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|BITS ingressing Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|BITS egressing Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|BITS ingressing Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|BITS egressing Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Frågor efter slutpunkten returnerade|Count|Totalt|Antal gånger som en Traffic Manager-slutpunkt som returnerades i angiven tidsperiod|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Slutpunktsstatus av slutpunkten|Count|Maximal|1 om en slutpunkt avsökningen status är ”aktiverad”, annars 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Avsökningar misslyckades|Percent|Average|% av tiden har anslutning övervakningsavsökningar misslyckades|Inga dimensioner|
|AverageRoundtripMs|Genomsn. Fram och åter tid (ms)|MilliSeconds|Average|Genomsnittlig nätverk fram och åter tid (ms) för anslutning övervakningsavsökningar som skickas mellan källa och mål|Inga dimensioner|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RequestCount|Antal begäranden|Count|Totalt|Antalet klientbegäranden som hanteras av HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Begärandestorlek|Byte|Totalt|Antalet byte som skickas när begäranden från klienter till HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Svarsstorlek|Byte|Totalt|Antalet byte som skickas som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Antal förfrågningar för serverdel|Count|Totalt|Antalet begäranden som skickas från HTTP/S-proxy till serverdelar|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Svarstid för backend-begäran|MilliSeconds|Average|Tid som beräknas från när begäran har skickats av HTTP/S-proxyn till serverdelen tills HTTP/S-proxyn fick den sista byten av svaret från serverdelen|Serverdel|
|TotalLatency|Total svarstid|MilliSeconds|Average|Tid som beräknas från när klientbegäran togs emot av HTTP/S-proxyn tills klienten godkänt den sista byten svar från HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Serverdelens hälsotillstånd procent|Percent|Average|Procentandelen av lyckade hälsorapporter avsökningar från HTTP/S-proxy till serverdelar|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall begäran antal|Count|Totalt|Antalet klientbegäranden bearbetas av Brandvägg för webbaserade program|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|registration.all|Registreringsåtgärder|Count|Totalt|Totalt antal genomförda registreringsåtgärder (skapande, uppdatera, fråga och borttagningar). |Inga dimensioner|
|registration.create|Registreringsskaparåtgärder|Count|Totalt|Totalt antal registreringar.|Inga dimensioner|
|registration.update|Registreringsuppdateringsåtgärder|Count|Totalt|Totalt antal genomförda registreringsuppdateringar.|Inga dimensioner|
|registration.get|Registreringsläsningsåtgärder|Count|Totalt|Totalt antal utförda registreringsfrågor.|Inga dimensioner|
|registration.delete|Registreringsborttagningsåtgärder|Count|Totalt|Totalt antal registreringar.|Inga dimensioner|
|incoming|Inkommande meddelanden|Count|Totalt|Det totalt antalet skicka API-anrop. |Inga dimensioner|
|incoming.scheduled|Schemalagda Push-meddelanden som skickas|Count|Totalt|Schemalagt Push-meddelande har avbrutits|Inga dimensioner|
|incoming.scheduled.cancel|Schemalagt Push-meddelande har avbrutits|Count|Totalt|Schemalagt Push-meddelande har avbrutits|Inga dimensioner|
|Scheduled.Pending|Schemalagda meddelanden som väntar|Count|Totalt|Schemalagda meddelanden som väntar|Inga dimensioner|
|installation.all|Installationshanteringsåtgärder|Count|Totalt|Installationshanteringsåtgärder|Inga dimensioner|
|installation.get|Hämta installationsåtgärder|Count|Totalt|Hämta installationsåtgärder|Inga dimensioner|
|installation.upsert|Skapa eller uppdatera installationsåtgärder|Count|Totalt|Skapa eller uppdatera installationsåtgärder|Inga dimensioner|
|installation.patch|Patch-installationsåtgärder|Count|Totalt|Patch-installationsåtgärder|Inga dimensioner|
|installation.delete|Ta bort Installation|Count|Totalt|Ta bort Installation|Inga dimensioner|
|outgoing.allpns.success|Lyckade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.allpns.invalidpayload|Nyttolastfel|Count|Totalt|Antal push-meddelanden som inte levererades eftersom PNS returnerade ett nyttolastfel.|Inga dimensioner|
|outgoing.allpns.pnserror|Externa Meddelandesystemfel|Count|Totalt|Antal push-meddelanden misslyckades på grund av kommunikationsproblem med PNS (inte autentiseringsproblem).|Inga dimensioner|
|outgoing.allpns.channelerror|Kanalfel|Count|Totalt|Antal push-meddelanden som misslyckades på grund av att kanalen var ogiltig inte är kopplade till rätt app ska vara begränsad eller upphört att gälla.|Inga dimensioner|
|outgoing.allpns.badorexpiredchannel|Felaktig eller utgången kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom den kanal/token/registrationId i registreringen har upphört att gälla eller är ogiltig.|Inga dimensioner|
|outgoing.wns.success|Wns – lyckade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.wns.invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade. (Windows Live kan inte identifiera autentiseringsuppgifterna).|Inga dimensioner|
|outgoing.wns.badchannel|Wns – felaktig kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: (404 hittades inte).|Inga dimensioner|
|outgoing.wns.expiredchannel|Wns – utgången kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 sidan finns inte).|Inga dimensioner|
|outgoing.wns.throttled|Wns – begränsade meddelanden|Count|Totalt|Antal push-meddelanden som inte eftersom WNS begränsar den här appen (WNS-status: 406 Ogiltigt format).|Inga dimensioner|
|outgoing.wns.tokenproviderunreachable|WNS-auktoriseringsfel (kan inte nås)|Count|Totalt|Windows Live kan inte nås.|Inga dimensioner|
|outgoing.wns.invalidtoken|WNS-auktoriseringsfel (ogiltig Token)|Count|Totalt|Token som angavs i WNS är inte giltig (WNS-status: 401 Ej behörig).|Inga dimensioner|
|outgoing.wns.wrongtoken|WNS-auktoriseringsfel (fel Token)|Count|Totalt|Token som angavs i WNS är giltig men för ett annat program (WNS-status: 403 Forbidden). Detta kan inträffa om ChannelURI i registreringen är associerad med en annan app. Kontrollera att klientappen är associerad med samma app vars autentiseringsuppgifter finns i meddelandehubben.|Inga dimensioner|
|outgoing.wns.invalidnotificationformat|Wns – ogiltigt meddelandeformat|Count|Totalt|Formatet för meddelandet är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nyttolaster.|Inga dimensioner|
|outgoing.wns.invalidnotificationsize|Wns – ogiltig meddelandestorlek|Count|Totalt|Meddelandets nyttolast är för stor (WNS-status: 413).|Inga dimensioner|
|outgoing.wns.channelthrottled|Wns – begränsad kanal|Count|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svarshuvud: X-WNS-NotificationStatus:channelThrottled).|Inga dimensioner|
|outgoing.wns.channeldisconnected|Wns – frånkopplad kanal|Count|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svarshuvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|outgoing.wns.dropped|Wns – utelämnade meddelanden|Count|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: utelämnat men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|outgoing.wns.pnserror|WNS-fel|Count|Totalt|Meddelandet levererades inte på grund av kommunikationsproblem med WNS.|Inga dimensioner|
|outgoing.wns.authenticationerror|WNS-auktoriseringsfel|Count|Totalt|Meddelandet levererades inte på grund av kommunikationsproblem med ogiltiga Windows Live-autentiseringsuppgifter eller fel token.|Inga dimensioner|
|outgoing.apns.success|Apns – lyckade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.apns.invalidcredentials|APNS-auktoriseringsfel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.apns.badchannel|Apns – felaktig kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom token är ogiltigt (APNS-statuskod: 8).|Inga dimensioner|
|outgoing.apns.expiredchannel|Apns – utgången kanal|Count|Totalt|Antal token som ogiltigförklarades av APNS-feedbackkanalen.|Inga dimensioner|
|outgoing.apns.invalidnotificationsize|APNS ogiltig meddelandestorlek|Count|Totalt|Antal push-meddelanden som inte eftersom nyttolasten var för stor (APNS-statuskod: 7).|Inga dimensioner|
|outgoing.apns.pnserror|APNS-fel|Count|Totalt|Antal push-meddelanden som misslyckades på grund av kommunikationsproblem med APNS.|Inga dimensioner|
|outgoing.gcm.success|GCM – lyckade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.gcm.invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.gcm.badchannel|GCM – felaktig kanal|Count|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen inte kändes igen (GCM-resultat: Ogiltig registrering).|Inga dimensioner|
|outgoing.gcm.expiredchannel|GCM – utgången kanal|Count|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|Outgoing.GCM.throttled|GCM – begränsade meddelanden|Count|Totalt|Antal push-meddelanden som misslyckades eftersom GCM – begränsade den här appen (GCM-statuskod: 501-599 eller resultat: inte tillgänglig).|Inga dimensioner|
|outgoing.gcm.invalidnotificationformat|GCM – ogiltigt meddelandeformat|Count|Totalt|Antal push-meddelanden som misslyckades eftersom nyttolasten inte har formaterats korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|outgoing.gcm.invalidnotificationsize|GCM – ogiltig meddelandestorlek|Count|Totalt|Antal push-meddelanden som inte eftersom nyttolasten var för stor (GCM-resultat: MessageTooBig).|Inga dimensioner|
|outgoing.gcm.wrongchannel|GCM – fel kanal|Count|Totalt|Antal push-meddelanden misslyckades på grund av att registrationId i registreringen inte är kopplad till den aktuella appen (GCM-resultat: InvalidPackageName).|Inga dimensioner|
|outgoing.gcm.pnserror|GCM-fel|Count|Totalt|Antal push-meddelanden som misslyckades på grund av kommunikationsproblem med GCM.|Inga dimensioner|
|outgoing.gcm.authenticationerror|GCM-autentiseringsfel|Count|Totalt|Antal push-meddelanden misslyckades eftersom PNS inte godkände de angivna autentiseringsuppgifterna, autentiseringsuppgifterna var blockerade eller SenderId var felkonfigurerat i appen (GCM-resultat: MismatchedSenderId).|Inga dimensioner|
|outgoing.mpns.success|Mpns – lyckade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.mpns.invalidcredentials|Mpns – ogiltiga autentiseringsuppgifter|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.mpns.badchannel|Mpns – felaktig kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: (404 hittades inte).|Inga dimensioner|
|outgoing.mpns.throttled|Mpns – begränsade meddelanden|Count|Totalt|Antal push-meddelanden som inte eftersom MPNS begränsar den här appen (WNS MPNS: 406 Ogiltigt format).|Inga dimensioner|
|outgoing.mpns.invalidnotificationformat|Mpns – ogiltigt meddelandeformat|Count|Totalt|Antal push-meddelanden som misslyckades eftersom nyttolasten för meddelanden var för stort.|Inga dimensioner|
|outgoing.mpns.channeldisconnected|Mpns – frånkopplad kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen kopplades (MPNS-status: 412 som gick inte att hitta IT-avdelning).|Inga dimensioner|
|outgoing.mpns.dropped|Mpns – utelämnade meddelanden|Count|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svarshuvud: X-NotificationStatus: QueueFull eller ignorerade).|Inga dimensioner|
|outgoing.mpns.pnserror|MPNS-fel|Count|Totalt|Antal push-meddelanden som misslyckades på grund av kommunikationsproblem med MPNS.|Inga dimensioner|
|outgoing.mpns.authenticationerror|MPNS-autentiseringsfel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|notificationhub.pushes|Alla utgående meddelanden|Count|Totalt|Alla utgående meddelanden i meddelandehubben|Inga dimensioner|
|incoming.all.requests|Alla inkommande förfrågningar|Count|Totalt|Totalt antal inkommande förfrågningar för en meddelandehubb|Inga dimensioner|
|incoming.all.failedrequests|Alla inkommande misslyckade förfrågningar|Count|Totalt|Totalt antal inkommande misslyckade förfrågningar för en meddelandehubb|Inga dimensioner|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_ % Ledigai|% Ledigai-noder|Count|Average|Average_ % Ledigai|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % ledigt utrymme|Ledigt utrymme i procent|Count|Average|Average_ % ledigt utrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % Användai-noder|% Användai-noder|Count|Average|Average_ % Användai-noder|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Använt utrymme i procent för Average_|Använt utrymme i procent|Count|Average|Använt utrymme i procent för Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/sek|Disk – lästa byte/sek|Count|Average|Average_Disk lästa byte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Diskläsningar/sek|Diskläsningar/sek|Count|Average|Average_Disk Diskläsningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk disköverföringar/sek|Disköverföringar/sek|Count|Average|Average_Disk disköverföringar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/sek|Disk – skrivna byte/sek|Count|Average|Average_Disk skrivna byte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Diskskrivningar/sek|Diskskrivningar/sek|Count|Average|Average_Disk Diskskrivningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Ledigt utrymme i MB|Count|Average|Average_Free megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk byte/sek|Logisk Disk byte/sek|Count|Average|Average_Logical Disk byte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ tillgängligt minne i procent|Tillgängligt minne i procent|Count|Average|Average_ tillgängligt minne i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Tillgängligt växlingsutrymme i Average_ %|Tillgängligt växlingsutrymme i procent|Count|Average|Tillgängligt växlingsutrymme i Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Använt minne i procent för Average_|Använt minne i procent|Count|Average|Använt minne i procent för Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % använt växlingsutrymme|Använt växlingsutrymme i procent|Count|Average|Average_ % använt växlingsutrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available minne i megabyte|Tillgängligt minne i megabyte|Count|Average|Average_Available minne i megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte växlingsutrymme|Tillgängliga megabyte växlingsutrymme|Count|Average|Average_Available megabyte växlingsutrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Diskläsningar/sek|Sidläsningar/sek|Count|Average|Average_Page Diskläsningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Diskskrivningar/sek|Sidskrivningar/sek|Count|Average|Average_Page Diskskrivningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sek|Sidor/sek|Count|Average|Average_Pages/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlingsutrymme i procent|Använt växlingsutrymme i megabyte|Count|Average|Average_Used megabyte växlingsutrymme i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i megabyte|Använt minne Mbyte|Count|Average|Average_Used minne i megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Överförda Average_Total byte|Totalt antal överförda byte|Count|Average|Överförda Average_Total byte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte som tagits emot|Totalt antal byte mottaget|Count|Average|Average_Total byte som tagits emot|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Totalt antal byte|Count|Average|Average_Total byte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total antal skickade paket|Totalt antal skickade paket|Count|Average|Average_Total antal skickade paket|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total paket som tas emot|Totalt antal mottagna paket|Count|Average|Average_Total paket som tas emot|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx fel|Totalt antal Rx-fel|Count|Average|Average_Total Rx fel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx-fel|Totalt antal Tx-fel|Count|Average|Average_Total Tx-fel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Totalt antal kollisioner|Count|Average|Average_Total kollisioner|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskläsning|Genomsn. S/diskläsning|Count|Average|Average_Avg. S/diskläsning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/disköverföring|Genomsn. S/disköverföring|Count|Average|Average_Avg. S/disköverföring|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskskrivning|Genomsn. S/diskskrivning|Count|Average|Average_Avg. S/diskskrivning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk byte/sek|Fysisk Disk byte/sek|Count|Average|Average_Physical Disk byte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Privilegierad tid i Average_Pct|PCT privilegierad tid|Count|Average|Privilegierad tid i Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användartid|PCT användartid|Count|Average|Average_Pct användartid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne kB|Använt minne kB|Count|Average|Average_Used minne kB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Virtuella delat minne|Count|Average|Average_Virtual delat minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % DPC-tid|% DPC-tid|Count|Average|Average_ % DPC-tid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ inaktivitetstid i procent|Inaktivitetstid i procent|Count|Average|Average_ inaktivitetstid i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ avbrottstid i procent|Avbrottstid i procent|Count|Average|Average_ avbrottstid i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % / o-väntetid|Väntetid i procent i/o|Count|Average|Average_ % / o-väntetid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ Nice Time|Nice Time|Count|Average|Average_ Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Privilegierad tid i procent för Average_|Privilegierad tid i procent|Count|Average|Privilegierad tid i procent för Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % processortid|Tid i procent för processor|Count|Average|Average_ % processortid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ Användartid i procent|Användartid i procent|Count|Average|Average_ Användartid i procent|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Ledigt fysiskt minne|Count|Average|Average_Free fysiskt minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Ledigt utrymme i växlingsfiler|Count|Average|Average_Free utrymme i växlingsfiler|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Ledigt virtuellt minne|Count|Average|Average_Free virtuellt minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processer|Count|Average|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagrad i växlingsfiler|Storlek lagrad i växlingsfiler|Count|Average|Average_Size lagrad i växlingsfiler|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Drifttid|Count|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Användare|Count|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskläsning|Genomsn. S/diskläsning|Count|Average|Average_Avg. S/diskläsning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskskrivning|Genomsn. S/diskskrivning|Count|Average|Average_Avg. S/diskskrivning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Aktuell diskkölängd|Count|Average|Average_Current diskkölängd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Diskläsningar/sek|Diskläsningar/sek|Count|Average|Average_Disk Diskläsningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk disköverföringar/sek|Disköverföringar/sek|Count|Average|Average_Disk disköverföringar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Diskskrivningar/sek|Diskskrivningar/sek|Count|Average|Average_Disk Diskskrivningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Ledigt utrymme i MB|Count|Average|Average_Free megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % ledigt utrymme|Ledigt utrymme i procent|Count|Average|Average_ % ledigt utrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte|Tillgängliga megabyte|Count|Average|Average_Available megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % allokerade byte som används|% Allokerade byte som används|Count|Average|Average_ % allokerade byte som används|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes per sekund|Mottagna byte/sek|Count|Average|Average_Bytes per sekund|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes/sek|Skickade byte/sek|Count|Average|Average_Bytes/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/sek|Byte totalt/sek|Count|Average|Average_Bytes totalt/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % processortid|Tid i procent för processor|Count|Average|Average_ % processortid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Kölängd|Kölängd för processor|Count|Average|Average_Processor Kölängd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Count|Totalt|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Uppdatera|Uppdatera|Count|Average|Uppdatera|Dator, produkt, klassificering, UpdateState, valfritt, godkända|
|Händelse|Händelse|Count|Average|Händelse|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryDuration|Frågevaraktighet|Millisekunder|Average|DAX-frågan varaktighet i sista intervallet|Inga dimensioner|
|QueryPoolJobQueueLength|Trådar: Fråga efter pool jobbkölängd|Count|Average|Antal jobb i kön för frågetrådspoolen.|Inga dimensioner|
|qpu_high_utilization_metric|QPU hög användning|Count|Totalt|QPU hög användning i sista minuten, 1 för hög QPU-användning, annars 0|Inga dimensioner|
|memory_metric|Minne|Byte|Average|Minne. Intervallet 0 – 3 GB för A1, 0 – 5 GB för A2, A3 0 – 10 GB, 0 – 25 GB för A4, 0 – 50 GB a5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Minnesförslöing|Percent|Average|Genomsnittlig minnesförslöing.|Inga dimensioner|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Totalt|Lyckad ListenerConnections för Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Totalt|ClientError på ListenerConnections för Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Totalt|ServerError på ListenerConnections för Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Totalt|Lyckade SenderConnections för Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Totalt|ClientError på SenderConnections för Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Totalt|ServerError på SenderConnections för Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Totalt|Totalt ListenerConnections för Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Totalt|Totalt SenderConnections-förfrågningar för Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Totalt|Totalt ActiveConnections för Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Totalt|Totalt ActiveListeners för Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Totalt|Totalt BytesTransferred för Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Totalt|Totalt ListenerDisconnects för Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Totalt|Total SenderDisconnects för Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SearchLatency|Svarstid för sökning|Sekunder|Average|Svarstid för genomsnittlig sökning för search-tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Sökfrågor per sekund|CountPerSecond|Average|Sökfrågor per sekund för search-tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Begränsade sökfrågor i procent|Percent|Average|Procentandel av sökfrågor som har begränsats för search-tjänsten|Inga dimensioner|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade begäranden (förhandsversion)|Count|Totalt|Totalt antal slutförda begäranden för ett namnområde (förhandsversion)|EntityName|
|ServerErrors|Serverfel. (Förhandsversion)|Count|Totalt|Serverfel för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|UserErrors|Användarfel. (Förhandsversion)|Count|Totalt|Användarfel för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|ThrottledRequests|Begränsade begäranden. (Förhandsversion)|Count|Totalt|Begränsade begäranden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|IncomingRequests|Inkommande förfrågningar (förhandsversion)|Count|Totalt|Inkommande begäranden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|IncomingMessages|Inkommande meddelanden (förhandsversion)|Count|Totalt|Inkommande meddelanden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|OutgoingMessages|Utgående meddelanden (förhandsversion)|Count|Totalt|Utgående meddelanden för Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|ActiveConnections|ActiveConnections (förhandsversion)|Count|Totalt|Totalt antal aktiva anslutningar för Microsoft.ServiceBus. (Förhandsversion)|Inga dimensioner|
|Size|Storlek (förhandsversion)|Byte|Average|Storlek på kö/ämne i byte. (Förhandsversion)|EntityName|
|Meddelanden|Antal meddelanden i kö/ämne. (Förhandsversion)|Count|Average|Antal meddelanden i kö/ämne. (Förhandsversion)|EntityName|
|ActiveMessages|Antal aktiva meddelanden i kö/ämne. (Förhandsversion)|Count|Average|Antal aktiva meddelanden i kö/ämne. (Förhandsversion)|EntityName|
|DeadletteredMessages|Antal lettered förlorade meddelanden i kö/ämne. (Förhandsversion)|Count|Average|Antal lettered förlorade meddelanden i kö/ämne. (Förhandsversion)|EntityName|
|ScheduledMessages|Antal schemalagda meddelanden i kö/ämne. (Förhandsversion)|Count|Average|Antal schemalagda meddelanden i kö/ämne. (Förhandsversion)|EntityName|
|CPUXNS|Processoranvändning per namnområde|Percent|Maximal|Service bus premium namnområde CPU-användningsmått|Inga dimensioner|
|WSXNS|Minnesstorleksanvändning per namnområde|Percent|Maximal|Minnesanvändningsmått för Service bus premium-namnområde|Inga dimensioner|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|CPU som allokerats till den här behållaren i millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Byte|Average|Minne som allokeras till den här behållaren i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Faktisk CPU-användning i millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Byte|Average|Användning av faktiska minne i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Average|Användning av processor för den här behållaren som procentandel av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Average|Användning av processor för den här behållaren som procentandel av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Status för Service Fabric-nät program|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Count|Average|Hälsostatus för en tjänst i Service Fabric-nät program|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Hälsostatus för en tjänst-replik i Service Fabric-nät program|ApplicationName, Status, tjänstnamn, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Average|Status för behållare i Service Fabric-nät program|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Count|Average|Starta om räknare för en behållare i Service Fabric-nät program|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ConnectionCount|Antal anslutningar|Count|Maximal|Mängden användaranslutning.|Slutpunkt|
|MessageCount|Antal meddelanden|Count|Totalt|Den totala mängden meddelanden.|Inga dimensioner|
|InboundTraffic|Inkommande trafik|Byte|Totalt|Inkommande trafik för tjänsten|Inga dimensioner|
|OutboundTraffic|Utgående trafik|Byte|Totalt|Utgående trafik för tjänsten|Inga dimensioner|
|UserErrors|Användarfel|Percent|Maximal|Procentandelen användarfel|Inga dimensioner|
|SystemErrors|Systemfel|Percent|Maximal|Procentandelen fel i filsystemet|Inga dimensioner|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|physical_data_read_percent|Data IO-procent|Percent|Average|Data IO-procent|Inga dimensioner|
|log_write_percent|Logg-IO-procent|Percent|Average|Logg-IO-procent|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Percent|Average|DTU-procent|Inga dimensioner|
|lagring|Datautrymme som används|Byte|Maximal|Totala databasstorleken|Inga dimensioner|
|connection_successful|Anslutningarna lyckades|Count|Totalt|Anslutningarna lyckades|Inga dimensioner|
|connection_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga dimensioner|
|blocked_by_firewall|Blockeras av brandvägg|Count|Totalt|Blockeras av brandvägg|Inga dimensioner|
|deadlock|Låsningar|Count|Totalt|Låsningar|Inga dimensioner|
|storage_percent|Datautrymme använt procent|Percent|Maximal|Databasstorlek i procent|Inga dimensioner|
|xtp_storage_percent|Procent för in-Memory OLTP-lagring|Percent|Average|Procent för in-Memory OLTP-lagring|Inga dimensioner|
|workers_percent|Arbetare procent|Percent|Average|Arbetare procent|Inga dimensioner|
|sessions_percent|Sessioner procent|Percent|Average|Sessioner procent|Inga dimensioner|
|dtu_limit|DTU-gräns|Count|Average|DTU-gräns|Inga dimensioner|
|dtu_used|DTU används|Count|Average|DTU används|Inga dimensioner|
|cpu_limit|CPU-begränsning|Count|Average|CPU-begränsning|Inga dimensioner|
|cpu_used|Processorn som används|Count|Average|Processorn som används|Inga dimensioner|
|dwu_limit|DWU-gräns|Count|Maximal|DWU-gräns|Inga dimensioner|
|dwu_consumption_percent|DWU-procent|Percent|Maximal|DWU-procent|Inga dimensioner|
|dwu_used|Använda DWU|Count|Maximal|Använda DWU|Inga dimensioner|
|dw_cpu_percent|DW nivån CPU-procent|Percent|Average|DW nivån CPU-procent|DwLogicalNodeId|
|dw_physical_data_read_percent|DW noden på Data IO-procent|Percent|Average|DW noden på Data IO-procent|DwLogicalNodeId|
    |cache_hit_percent|Cacheträff i procent|Percent|Maximal|Cacheträff i procent|Inga dimensioner|
|cache_used_percent|Använd cache-procent|Percent|Maximal|Använd cache-procent|Inga dimensioner|
|local_tempdb_usage_percent|Lokala tempdb-procent|Percent|Average|Lokala tempdb-procent|Inga dimensioner|
|app_cpu_billed|App CPU som faktureras|Count|Totalt|App CPU som faktureras|Inga dimensioner|
|app_cpu_percent|App CPU-procent|Percent|Average|App CPU-procent|Inga dimensioner|
|app_memory_percent|App används minnesprocent|Percent|Average|App används minnesprocent|Inga dimensioner|
|allocated_data_storage|Data som allokerats|Byte|Average|Data som allokerats|Inga dimensioner|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|physical_data_read_percent|Data IO-procent|Percent|Average|Data IO-procent|Inga dimensioner|
|log_write_percent|Logg-IO-procent|Percent|Average|Logg-IO-procent|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Percent|Average|DTU-procent|Inga dimensioner|
|storage_percent|Datautrymme använt procent||Percent|Average|Lagringsprocent|Inga dimensioner|
|workers_percent|Arbetare procent|Percent|Average|Arbetare procent|Inga dimensioner|
|sessions_percent|Sessioner procent|Percent|Average|Sessioner procent|Inga dimensioner|
|eDTU_limit|eDTU-gränsen|Count|Average|eDTU-gränsen|Inga dimensioner|
|storage_limit|Maximal datastorlek|Byte|Average|Gränsen för lagring|Inga dimensioner|
|eDTU_used|edtu: er används|Count|Average|edtu: er används|Inga dimensioner|
|storage_used|Datautrymme som används|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|xtp_storage_percent|Procent för in-Memory OLTP-lagring|Percent|Average|Procent för in-Memory OLTP-lagring|Inga dimensioner|
|cpu_limit|CPU-begränsning|Count|Average|CPU-begränsning|Inga dimensioner|
|cpu_used|Processorn som används|Count|Average|Processorn som används|Inga dimensioner|
|allocated_data_storage|Data som allokerats|Byte|Average|Data som allokerats|Inga dimensioner|
|allocated_data_storage_percent|Data allokerat utrymme procent|Percent|Maximal|Data allokerat utrymme procent|Inga dimensioner|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|virtual_core_count|Antal virtuella kärnor|Count|Average|Antal virtuella kärnor|Inga dimensioner|
|avg_cpu_percent|Genomsnittlig CPU-procent|Percent|Average|Genomsnittlig CPU-procent|Inga dimensioner|
|reserved_storage_mb|Reserverat lagringsutrymme|Count|Average|Reserverat lagringsutrymme|Inga dimensioner|
|storage_space_used_mb|Lagringsutrymme som används|Count|Average|Lagringsutrymme som används|Inga dimensioner|
|io_requests|Antal för i/o-begäranden|Count|Average|Antal för i/o-begäranden|Inga dimensioner|
|io_bytes_read|Lästa byte i i/o|Byte|Average|Lästa byte i i/o|Inga dimensioner|
|io_bytes_written|Skrivna i/o-byte|Byte|Average|Skrivna i/o-byte|Inga dimensioner|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Medel|Kapacitet som används av konto|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, GeoType, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|Blobkapacitet|Byte|Average|Mängden lagring som används av lagringskontots Blob Service i byte.|BlobType, nivå|
|BlobCount|Antalet blobar|Antal|Totalt|Antalet blobar i lagringskontots Blob Service.|BlobType|       |BlobCount|Antalet blobar|Count|Average|Antalet blobar i lagringskontots Blob Service.|BlobType, nivå|
|ContainerCount|Antal blobcontainrar|Antal|Medel|Antalet containrar i lagringskontots Blob Service.|Inga dimensioner|
|IndexCapacity|Index kapacitet|Byte|Average|Mängden lagringsutrymme som används av ADLS Gen2 (hierarkisk) Index i byte.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, GeoType, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Filkapacitet|Byte|Average|Mängden lagringsutrymme som används av lagringskontots filtjänst i byte.|Inga dimensioner|
|FileCount|Antal filer|Count|Average|Antalet filer i lagringskontots filtjänst.|Inga dimensioner|
|FileShareCount|Filresurs|Count|Average|Antal filresurser i lagringskontots filtjänst.|Inga dimensioner|
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

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Synkronisera Session resultat|Count|Average|Mått som loggar ett värde på 1 varje gång Serverslutpunkten har har slutförts synkroniseringssession med Molnslutpunkten|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Filer som har synkroniserats|Count|Totalt|Antal filer synkroniseras|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Filer som inte synkroniserar|Count|Totalt|Antal filer som misslyckades med att synkronisera|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total filstorlek som överfördes för synkroniseringssessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Onlinestatus för Server|Count|Maximal|Mått som loggar ett värde på 1 varje gång den registrerade servern har registrerar ett pulsslag med Molnslutpunkten|Servernamn|
|StorageSyncRecallIOTotalSizeBytes|Lagringsnivåer återkallande i molnet|Byte|Totalt|Total storlek på data som återställs av servern|Servernamn|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ResourceUtilization|SU % utnyttjande|Percent|Maximal|SU % utnyttjande|LogicalName, PartitionId|
|InputEvents|Indatahändelser|Count|Totalt|Indatahändelser|LogicalName, PartitionId|
|InputEventBytes|Indatahändelsebyte|Byte|Totalt|Indatahändelsebyte|LogicalName, PartitionId|
|LateInputEvents|Sena indatahändelser|Count|Totalt|Sena indatahändelser|LogicalName, PartitionId|
|OutputEvents|Utdatahändelser|Count|Totalt|Utdatahändelser|LogicalName, PartitionId|
|ConversionErrors|Datakonverteringsfel|Count|Totalt|Datakonverteringsfel|LogicalName, PartitionId|
|Fel|Körningsfel|Count|Totalt|Körningsfel|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Oordnade händelser|Count|Totalt|Oordnade händelser|LogicalName, PartitionId|
|AMLCalloutRequests|Funktionsförfrågningar|Count|Totalt|Funktionsförfrågningar|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Misslyckade funktionsförfrågningar|Count|Totalt|Misslyckade funktionsförfrågningar|LogicalName, PartitionId|
|AMLCalloutInputEvents|Funktionshändelser|Count|Totalt|Funktionshändelser|LogicalName, PartitionId|
|DeserializationError|Fel vid deserialisering av indata|Count|Totalt|Fel vid deserialisering av indata|LogicalName, PartitionId|
|EarlyInputEvents|Tidiga indatahändelser|Count|Totalt|Tidiga indatahändelser|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Vattenstämpelfördröjning|Sekunder|Maximal|Vattenstämpelfördröjning|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Eftersläpande inmatningshändelser|Count|Maximal|Eftersläpande inmatningshändelser|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Indatakällorna har tagits emot|Count|Totalt|Indatakällorna har tagits emot|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingående fått meddelanden|Count|Totalt|Antal meddelanden som läses från alla händelsehubb eller IoT-hubb händelsekällor|Inga dimensioner|
|IngressReceivedInvalidMessages|Ingående erhöll ett felaktigt meddelande|Count|Totalt|Antal felaktiga meddelanden läses från alla händelsehubb eller IoT-hubb händelsekällor|Inga dimensioner|
|IngressReceivedBytes|Ingående har tagit emot byte|Byte|Totalt|Antal byte som läses från alla händelsekällor|Inga dimensioner|
|IngressStoredBytes|Ingående lagras byte|Byte|Totalt|Total storlek på händelser som har bearbetats och tillgängliga för frågor|Inga dimensioner|
|IngressStoredEvents|Ingående lagrade händelser|Count|Totalt|Antal Flat händelser har bearbetats och tillgängliga för frågor|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Tidsförskjutningen för Ingress mottagna meddelanden|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet är i kö i händelsekällan och den tid som bearbetas i ingående|Inga dimensioner|
|IngressReceivedMessagesCountLag|Inkommande meddelanden som tas emot antal fördröjning|Count|Average|Skillnaden mellan sekvensnumret för den sista köas meddelandet i käll-partition och sekvens antal meddelande som bearbetas i ingående|Inga dimensioner|
|WarmStorageMaxProperties|Varma Max lagringsegenskaper|Count|Maximal|Högsta antalet av egenskaper som används i miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Store för PAYG-SKU|Inga dimensioner|
|WarmStorageUsedProperties|Varma Storage används egenskaper |Count|Maximal|Antal egenskaper som används i miljön för S1/S2 SKU och antal egenskaper som används av varmt Store för PAYG-SKU|Inga dimensioner|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingående fått meddelanden|Count|Totalt|Antal meddelanden som läses från händelsekällan|Inga dimensioner|
|IngressReceivedInvalidMessages|Ingående erhöll ett felaktigt meddelande|Count|Totalt|Antal felaktiga meddelanden läses från händelsekällan|Inga dimensioner|
|IngressReceivedBytes|Ingående har tagit emot byte|Byte|Totalt|Antal byte som läses från händelsekällan|Inga dimensioner|
|IngressStoredBytes|Ingående lagras byte|Byte|Totalt|Total storlek på händelser som har bearbetats och tillgängliga för frågor|Inga dimensioner|
|IngressStoredEvents|Ingående lagrade händelser|Count|Totalt|Antal Flat händelser har bearbetats och tillgängliga för frågor|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Tidsförskjutningen för Ingress mottagna meddelanden|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet är i kö i händelsekällan och den tid som bearbetas i ingående|Inga dimensioner|
|IngressReceivedMessagesCountLag|Inkommande meddelanden som tas emot antal fördröjning|Count|Average|Skillnaden mellan sekvensnumret för den sista köas meddelandet i käll-partition och sekvens antal meddelande som bearbetas i ingående|Inga dimensioner|
|WarmStorageMaxProperties|Varma Max lagringsegenskaper|Count|Maximal|Högsta antalet av egenskaper som används i miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Store för PAYG-SKU|Inga dimensioner|
|WarmStorageUsedProperties|Varma Storage används egenskaper |Count|Maximal|Antal egenskaper som används i miljön för S1/S2 SKU och antal egenskaper som används av varmt Store för PAYG-SKU|Inga dimensioner|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk-lästa byte/sek|BytesPerSecond|Average|Genomsnittlig genomströmning på grund av läsåtgärder under provet.|Inga dimensioner|
|DiskWriteBytesPerSecond|Disk-skrivna byte/s|BytesPerSecond|Average|Genomsnittlig genomströmning på grund av skrivåtgärder under provet.|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt dataflöde per disk på grund av läsåtgärder under provet.|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Totalt dataflöde per disk på grund av skrivåtgärder under provet.|Inga dimensioner|
|DiskReadOperations|Disk-läsåtgärder|Count|Totalt|Antalet i/o-läsåtgärder i föregående exempel period. Observera att dessa åtgärder kan vara en variabel storlek.|Inga dimensioner|
|DiskWriteOperations|Diskskrivningsåtgärderna|Count|Totalt|Antalet i/o-skrivåtgärder i föregående exempel period. Observera att dessa åtgärder kan vara en variabel storlek.|Inga dimensioner|
|Disk-lästa/sek|Disk-lästa/sek|CountPerSecond|Average|Det genomsnittliga antalet i/o-läsåtgärder i föregående exempel period. Observera att dessa åtgärder kan vara en variabel storlek.|Inga dimensioner|
|Disk skrivåtgärder/sek|Disk skrivåtgärder/sek|CountPerSecond|Average|Det genomsnittliga antalet i/o-skrivåtgärder i föregående exempel period. Observera att dessa åtgärder kan vara en variabel storlek.|Inga dimensioner|
|DiskReadLatency|Disk Read Latency|Millisekunder|Average|Totalt antal Läs svarstid. Summan av enheten och kernel läsa svarstider.|Inga dimensioner|
|DiskWriteLatency|Skrivfördröjningen för disk|Millisekunder|Average|Totalt antal skrivåtgärder svarstid. Summan av enheten och kernel skrivningssvarstider.|Inga dimensioner|
|NetworkInBytesPerSecond|Nätverk i byte/sek|BytesPerSecond|Average|Genomsnittlig nätverksgenomflöde för mottagna trafiken.|Inga dimensioner|
|NetworkOutBytesPerSecond|Nätverk ut byte/sek|BytesPerSecond|Average|Genomsnittlig nätverkets dataflöde för överförda trafik.|Inga dimensioner|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Totalt antal nätverksgenomflöde för mottagna trafiken.|Inga dimensioner|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Totalt antal nätverkets dataflöde för överförda trafik.|Inga dimensioner|
|MemoryUsed|Använt minne|Byte|Average|Mängden minne i datorn som används av den virtuella datorn.|Inga dimensioner|
|MemoryGranted|Minne som har beviljats|Byte|Average|Mängden minne som tilldelades till den virtuella datorn för värden. Minne tilldelas inte till värden förrän den har använt en gång och beviljas minne kan byta ut eller ballooned bort om VMkernel behöver minnet.|Inga dimensioner|
|MemoryActive|Minne aktiv|Byte|Average|Mängden minne som används av den virtuella datorn i den senaste kort tidsperiod. Det här är ”true” antalet hur mycket minne den virtuella datorn har för närvarande behöver. Ytterligare, oanvända minne kan byta ut eller ballooned utan att påverka gästens prestanda.|Inga dimensioner|
|Procent CPU|Procent CPU|Procent|Average|CPU-användning. Det här värdet rapporteras med 100% som representerar alla processorkärnor på systemet. Exempelvis kan använder en 2-vägs virtuell dator med 50% av ett system med fyra kärnor helt två kärnor.|Inga dimensioner|
|PercentageCpuReady|Procent CPU klar|Millisekunder|Totalt|Ready time ligger tiden spendera väntar CPU(s) ska bli tillgänglig i det senaste uppdateringsintervallet.|Inga dimensioner|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Percent|Average|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Percent|Average|Minnesprocent|Instans|
|DiskQueueLength|Disk Queue Length|Count|Average|Disk Queue Length|Instans|
|HttpQueueLength|Http Queue Length|Count|Average|Http Queue Length|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (exklusive funktioner)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Count|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Count|Totalt|Http 101|Instans|
|Http2xx|HTTP 2xx|Count|Totalt|HTTP 2xx|Instans|
|Http3xx|HTTP 3xx|Count|Totalt|HTTP 3xx|Instans|
|Http401|Http 401|Count|Totalt|Http 401|Instans|
|Http403|HTTP 403|Count|Totalt|HTTP 403|Instans|
|Http404|Http 404|Count|Totalt|Http 404|Instans|
|Http406|HTTP 406 Ogiltigt format|Count|Totalt|HTTP 406 Ogiltigt format|Instans|
|Http4xx|HTTP 4xx|Count|Totalt|HTTP 4xx|Instans|
|Http5xx|HTTP-serverfel|Count|Totalt|HTTP-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Average|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Average|Genomsnittligt arbetsminne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Average|Genomsnittlig svarstid|Instans|
|AppConnections|Anslutningar|Count|Average|Anslutningar|Instans|
|Hanterar|Antal referenser|Count|Average|Antal referenser|Instans|
|Trådar|Antal tråd|Count|Average|Antal tråd|Instans|
|PrivateBytes|Privata byte|Byte|Average|Privata byte|Instans|
|IoReadBytesPerSecond|I/O lästa byte Per sekund|BytesPerSecond|Totalt|I/O lästa byte Per sekund|Instans|
|IoWriteBytesPerSecond|I/O skrivna byte Per sekund|BytesPerSecond|Totalt|I/O skrivna byte Per sekund|Instans|
|IoOtherBytesPerSecond|I/O övrigt byte Per sekund|BytesPerSecond|Totalt|I/O övrigt byte Per sekund|Instans|
|IoReadOperationsPerSecond|I/o-läsningsåtgärder Per sekund|BytesPerSecond|Totalt|I/o-läsningsåtgärder Per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-skrivåtgärder Per sekund|BytesPerSecond|Totalt|I/o-skrivåtgärder Per sekund|Instans|
|IoOtherOperationsPerSecond|I/O övriga åtgärder Per sekund|BytesPerSecond|Totalt|I/O övriga åtgärder Per sekund|Instans|
|RequestsInApplicationQueue|Förfrågningar i programkö|Count|Average|Förfrågningar i programkö|Instans|
|CurrentAssemblies|Aktuellt antal sammansättningar|Count|Average|Aktuellt antal sammansättningar|Instans|
|TotalAppDomains|Totalt antal Appdomäner|Count|Average|Totalt antal Appdomäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal Appdomäner som tas bort från minnet|Count|Average|Totalt antal Appdomäner som tas bort från minnet|Instans|
|Gen0Collections|Generation 0-skräpinsamlingar|Count|Totalt|Generation 0-skräpinsamlingar|Instans|
|Gen1Collections|Generation 1-skräpinsamlingar|Count|Totalt|Generation 1-skräpinsamlingar|Instans|
|Gen2Collections|Generation 2-skräpinsamlingar|Count|Totalt|Generation 2-skräpinsamlingar|Instans|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funktioner)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http5xx|HTTP-serverfel|Count|Totalt|HTTP-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Average|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Average|Genomsnittligt arbetsminne|Instans|
|FunctionExecutionUnits|Funktionskörningsenheter|MB / millisekunder|Totalt|[Funktionskörningsenheter](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Count|Totalt|Funktionen Antal körningar|Instans|
|PrivateBytes|Privata byte|Byte|Average|Privata byte|Instans|
|IoReadBytesPerSecond|I/O lästa byte Per sekund|BytesPerSecond|Totalt|I/O lästa byte Per sekund|Instans|
|IoWriteBytesPerSecond|I/O skrivna byte Per sekund|BytesPerSecond|Totalt|I/O skrivna byte Per sekund|Instans|
|IoOtherBytesPerSecond|I/O övrigt byte Per sekund|BytesPerSecond|Totalt|I/O övrigt byte Per sekund|Instans|
|IoReadOperationsPerSecond|I/o-läsningsåtgärder Per sekund|BytesPerSecond|Totalt|I/o-läsningsåtgärder Per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-skrivåtgärder Per sekund|BytesPerSecond|Totalt|I/o-skrivåtgärder Per sekund|Instans|
|IoOtherOperationsPerSecond|I/O övriga åtgärder Per sekund|BytesPerSecond|Totalt|I/O övriga åtgärder Per sekund|Instans|
|RequestsInApplicationQueue|Förfrågningar i programkö|Count|Average|Förfrågningar i programkö|Instans|
|CurrentAssemblies|Aktuellt antal sammansättningar|Count|Average|Aktuellt antal sammansättningar|Instans|
|TotalAppDomains|Totalt antal Appdomäner|Count|Average|Totalt antal Appdomäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal Appdomäner som tas bort från minnet|Count|Average|Totalt antal Appdomäner som tas bort från minnet|Instans|
|Gen0Collections|Generation 0-skräpinsamlingar|Count|Totalt|Generation 0-skräpinsamlingar|Instans|
|Gen1Collections|Generation 1-skräpinsamlingar|Count|Totalt|Generation 1-skräpinsamlingar|Instans|
|Gen2Collections|Generation 2-skräpinsamlingar|Count|Totalt|Generation 2-skräpinsamlingar|Instans|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Count|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Count|Totalt|Http 101|Instans|
|Http2xx|HTTP 2xx|Count|Totalt|HTTP 2xx|Instans|
|Http3xx|HTTP 3xx|Count|Totalt|HTTP 3xx|Instans|
|Http401|Http 401|Count|Totalt|Http 401|Instans|
|Http403|HTTP 403|Count|Totalt|HTTP 403|Instans|
|Http404|Http 404|Count|Totalt|Http 404|Instans|
|Http406|HTTP 406 Ogiltigt format|Count|Totalt|HTTP 406 Ogiltigt format|Instans|
|Http4xx|HTTP 4xx|Count|Totalt|HTTP 4xx|Instans|
|Http5xx|HTTP-serverfel|Count|Totalt|HTTP-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Average|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Average|Genomsnittligt arbetsminne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Average|Genomsnittlig svarstid|Instans|
|FunctionExecutionUnits|Funktionskörningsenheter|Count|Totalt|Funktionskörningsenheter|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Count|Totalt|Funktionen Antal körningar|Instans|
|AppConnections|Anslutningar|Count|Average|Anslutningar|Instans|
|Hanterar|Antal referenser|Count|Average|Antal referenser|Instans|
|Trådar|Antal tråd|Count|Average|Antal tråd|Instans|
|PrivateBytes|Privata byte|Byte|Average|Privata byte|Instans|
|IoReadBytesPerSecond|I/O lästa byte Per sekund|BytesPerSecond|Totalt|I/O lästa byte Per sekund|Instans|
|IoWriteBytesPerSecond|I/O skrivna byte Per sekund|BytesPerSecond|Totalt|I/O skrivna byte Per sekund|Instans|
|IoOtherBytesPerSecond|I/O övrigt byte Per sekund|BytesPerSecond|Totalt|I/O övrigt byte Per sekund|Instans|
|IoReadOperationsPerSecond|I/o-läsningsåtgärder Per sekund|BytesPerSecond|Totalt|I/o-läsningsåtgärder Per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-skrivåtgärder Per sekund|BytesPerSecond|Totalt|I/o-skrivåtgärder Per sekund|Instans|
|IoOtherOperationsPerSecond|I/O övriga åtgärder Per sekund|BytesPerSecond|Totalt|I/O övriga åtgärder Per sekund|Instans|
|RequestsInApplicationQueue|Förfrågningar i programkö|Count|Average|Förfrågningar i programkö|Instans|
|CurrentAssemblies|Aktuellt antal sammansättningar|Count|Average|Aktuellt antal sammansättningar|Instans|
|TotalAppDomains|Totalt antal Appdomäner|Count|Average|Totalt antal Appdomäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal Appdomäner som tas bort från minnet|Count|Average|Totalt antal Appdomäner som tas bort från minnet|Instans|
|Gen0Collections|Generation 0-skräpinsamlingar|Count|Totalt|Generation 0-skräpinsamlingar|Instans|
|Gen1Collections|Generation 1-skräpinsamlingar|Count|Totalt|Generation 1-skräpinsamlingar|Instans|
|Gen2Collections|Generation 2-skräpinsamlingar|Count|Totalt|Generation 2-skräpinsamlingar|Instans|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Count|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Count|Totalt|Http 101|Instans|
|Http2xx|HTTP 2xx|Count|Totalt|HTTP 2xx|Instans|
|Http3xx|HTTP 3xx|Count|Totalt|HTTP 3xx|Instans|
|Http401|Http 401|Count|Totalt|Http 401|Instans|
|Http403|HTTP 403|Count|Totalt|HTTP 403|Instans|
|Http404|Http 404|Count|Totalt|Http 404|Instans|
|Http406|HTTP 406 Ogiltigt format|Count|Totalt|HTTP 406 Ogiltigt format|Instans|
|Http4xx|HTTP 4xx|Count|Totalt|HTTP 4xx|Instans|
|Http5xx|HTTP-serverfel|Count|Totalt|HTTP-serverfel|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Average|Genomsnittlig svarstid|Instans|
|CpuPercentage|CPU-procent|Percent|Average|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Percent|Average|Minnesprocent|Instans|
|DiskQueueLength|Disk Queue Length|Count|Average|Disk Queue Length|Instans|
|HttpQueueLength|Http Queue Length|Count|Average|Http Queue Length|Instans|
|ActiveRequests|Aktiva begäranden|Count|Totalt|Aktiva begäranden|Instans|
|TotalFrontEnds|Totalt antal klienter|Count|Average|Totalt antal klienter|Inga dimensioner|
|SmallAppServicePlanInstances|Små App Service-Plan arbetare|Count|Average|Små App Service-Plan arbetare|Inga dimensioner|
|MediumAppServicePlanInstances|Medelhög App Service-Plan arbetare|Count|Average|Medelhög App Service-Plan arbetare|Inga dimensioner|
|LargeAppServicePlanInstances|Arbetare för stora App Service-Plan|Count|Average|Arbetare för stora App Service-Plan|Inga dimensioner|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbeten|Count|Average|Totalt antal arbeten|Inga dimensioner|
|WorkersAvailable|Tillgängliga arbeten|Count|Average|Tillgängliga arbeten|Inga dimensioner|
|WorkersUsed|Använda arbeten|Count|Average|Använda arbeten|Inga dimensioner|
|CpuPercentage|CPU-procent|Percent|Average|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Percent|Average|Minnesprocent|Instans|

## <a name="next-steps"></a>Nästa steg
* [Läs mer om mätvärden i Azure Monitor](data-platform.md)
* [Skapa aviseringar för mått](alerts-overview.md)
* [Exportera mått till lagring, Event Hub eller Log Analytics](diagnostic-logs-overview.md)
