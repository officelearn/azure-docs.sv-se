---
title: Azure Monitor mått som stöds efter resurs typ
description: Lista över mått som är tillgängliga för varje resurs typ med Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: ba3535679f37916a18aae5fe7dbe4e9114cea695
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664921"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor

Azure Monitor tillhandahåller flera olika sätt att interagera med mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av PowerShell eller CLI. Nedan visas en fullständig lista över alla mått som för närvarande är tillgängliga med Azure Monitor mått pipelinen. Andra mått kan vara tillgängliga i portalen eller med äldre API: er. Listan nedan innehåller endast mått som är tillgängliga med den konsoliderade Azure Monitor mått pipelinen. Använd [2018-01-01 API-versionen](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) för att fråga efter och komma åt dessa mått.

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|qpu_metric|QPU|Antal|Medel|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|memory_metric|Minne|Skickade|Medel|Minnesoptimerade. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|private_bytes_metric|Privata byte |Skickade|Medel|Den totala mängden minne som processerna för Analysis Services motor processen och kombinations programmet har allokerat, inklusive minne som delas med andra processer.|ServerResourceType|
|virtual_bytes_metric|Virtuella byte |Skickade|Medel|Den aktuella storleken för det virtuella adress utrymmet som Analysis Services motor process-och kombinations programmets process processer använder.|ServerResourceType|
|TotalConnectionRequests|Totalt antal anslutnings begär Anden|Antal|Medel|Totalt antal anslutnings begär Anden. Dessa är mottagna.|ServerResourceType|
|SuccessfullConnectionsPerSec|Lyckade anslutningar per sekund|CountPerSecond|Medel|Antal slutförda slut för ande av anslutningar.|ServerResourceType|
|TotalConnectionFailures|Totalt antal anslutnings problem|Antal|Medel|Totalt antal misslyckade anslutnings försök.|ServerResourceType|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medel|Aktuellt antal användarsessioner som har upprättats.|ServerResourceType|
|QueryPoolBusyThreads|Upptagna trådar för frågeprocessorn|Antal|Medel|Antalet upptagna trådar i trådpoolen.|ServerResourceType|
|CommandPoolJobQueueLength|Kölängd för kommando bassäng|Antal|Medel|Antal jobb i kön för kommando tråds poolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Bearbetar Kölängd för poolen|Antal|Medel|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|CurrentConnections|Anslutning: aktuella anslutningar|Antal|Medel|Aktuellt antal upprättade klient anslutningar.|ServerResourceType|
|CleanerCurrentPrice|Minne: aktuellt pris för renare|Antal|Medel|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Minne: krympnings utrymme för rensning|Skickade|Medel|Mängden minne i byte som kan rensas av bakgrunds rensaren.|ServerResourceType|
|CleanerMemoryNonshrinkable|Minne: det går inte att krympa rengörings minnet|Skickade|Medel|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|ServerResourceType|
|MemoryUsage|Minne: minnes användning|Skickade|Medel|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar minne som har mappats eller allokerats av xVelocity i Memory Analytics Engine (VertiPaq) utöver minnes gränsen för xVelocity-motorn.|ServerResourceType|
|MemoryLimitHard|Minne: minnes gräns hårt|Skickade|Medel|Hård minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitHigh|Minne: minnes gräns hög|Skickade|Medel|Hög minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitLow|Minne: minnes gräns låg|Skickade|Medel|Låg minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitVertiPaq|Minne: minnes gräns VertiPaq|Skickade|Medel|Minnes intern gräns, från konfigurations filen.|ServerResourceType|
|Kvot|Minne: kvot|Skickade|Medel|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|ServerResourceType|
|QuotaBlocked|Minne: kvot blockerad|Antal|Medel|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|ServerResourceType|
|VertiPaqNonpaged|Minne: VertiPaq som inte är växlat|Skickade|Medel|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|ServerResourceType|
|VertiPaqPaged|Minne: VertiPaq växlat|Skickade|Medel|Byte av växlings Bart minne som används för minnes intern data.|ServerResourceType|
|RowsReadPerSec|Bearbetar: rader lästa per sekund|CountPerSecond|Medel|Antalet rader som läses från alla Relations databaser.|ServerResourceType|
|RowsConvertedPerSec|Bearbetar: rader konverterade per sekund|CountPerSecond|Medel|Antal rader som konverterats under bearbetning.|ServerResourceType|
|RowsWrittenPerSec|Bearbetar: rader skrivna per sekund|CountPerSecond|Medel|Antal rader som skrivs under bearbetningen.|ServerResourceType|
|CommandPoolBusyThreads|Trådar: pool med upptagna kommando trådar|Antal|Medel|Antalet upptagna trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolIdleThreads|Trådar: inaktiva trådar för kommando pool|Antal|Medel|Antal inaktiva trådar i kommando tråds-poolen.|ServerResourceType|
|LongParsingBusyThreads|Trådar: lång parsning av upptagna trådar|Antal|Medel|Antalet upptagna trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingIdleThreads|Trådar: inaktiva trådar för lång parsning|Antal|Medel|Antalet inaktiva trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingJobQueueLength|Trådar: lång parsning av jobb Kölängd|Antal|Medel|Antal jobb i kön för den långa parsande trådpoolen.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Trådar: bearbetning av pool upptagna I/O-jobb trådar|Antal|Medel|Antal trådar som kör i/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Trådar: bearbetning av pool upptagna icke-I/O-trådar|Antal|Medel|Antal trådar som kör icke-I/O-jobb i bearbetningen av trådpoolen.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Trådar: bearbeta pool I/O-jobb Kölängd|Antal|Medel|Antalet I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Trådar: behandlar inaktiva I/O-jobb trådar för poolen|Antal|Medel|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Trådar: behandlar inaktiva icke-I/O-trådar som behandlar poolen|Antal|Medel|Antalet inaktiva trådar i bearbetnings trådens pool som är dedikerad till icke-I/O-jobb.|ServerResourceType|
|QueryPoolIdleThreads|Trådar: inaktiva trådar i lagringspoolen|Antal|Medel|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|QueryPoolJobQueueLength|Trådar: Kölängd för jobbkö|Antal|Medel|Antal jobb i kön för trådpoolen för Query.|ServerResourceType|
|ShortParsingBusyThreads|Trådar: kort parsning upptagna trådar|Antal|Medel|Antalet upptagna trådar i den kort parsar trådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Trådar: kort parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i kort parsar trådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Trådar: kort parsning av jobb Kölängd|Antal|Medel|Antal jobb i kö för kort parsar trådpoolen.|ServerResourceType|
|memory_thrashing_metric|Nedskräpning för minne|Procent|Medel|Genomsnittligt minne nedskräpning.|ServerResourceType|
|mashup_engine_qpu_metric|M-motor QPU|Antal|Medel|QPU användning av kombinations motor processer|ServerResourceType|
|mashup_engine_memory_metric|M motor minne|Skickade|Medel|Minnes användning per kombinations motor processer|ServerResourceType|
|mashup_engine_private_bytes_metric|M motor privata byte |Skickade|Medel|Den totala mängden minnes kombinations programmets behållar processer har allokerats, inte inklusive minne som delas med andra processer.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Virtuella byte i M-motor |Skickade|Medel|Den aktuella storleken för processerna för kombinations behållare för virtuella adress utrymmen använder.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Antal|Totalt|Det totala antalet Gateway-begäranden under en viss period. Det kan segmenteras av olika dimensioner för att hjälpa dig att diagnostisera problem. |Plats, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Totalt antal Gateway-begäranden|Antal|Totalt|Det totala antalet Gateway-begäranden under en viss period. Måttet är inaktuellt, vi rekommenderar att du använder det nya `Requests` måttet. |Plats, värdnamn|
|SuccessfulRequests|Lyckade Gateway-begäranden|Antal|Totalt|Det totala antalet lyckade Gateway-begäranden under en viss period. Måttet är inaktuellt, vi rekommenderar att du använder det nya `Requests` måttet.|Plats, värdnamn|
|UnauthorizedRequests|Obehöriga Gateway-begäranden|Antal|Totalt| Det totala antalet otillåtna Gateway-begäranden under en viss period. Måttet är inaktuellt, vi rekommenderar att du använder det nya `Requests` måttet.|Plats, värdnamn|
|FailedRequests|Misslyckade Gateway-begäranden|Antal|Totalt|Det totala antalet misslyckade Gateway-begäranden under en viss period. Måttet är inaktuellt, vi rekommenderar att du använder det nya `Requests` måttet.|Plats, värdnamn|
|OtherRequests|Andra gateway-begäranden|Antal|Totalt|Det totala antalet Gateway-begäranden under en viss period som inte omfattas av lyckade, obehöriga eller misslyckade kategorier. Måttet är inaktuellt, vi rekommenderar att du använder det nya `Requests` måttet. |Plats, värdnamn|
|Längd|Total varaktighet för gateway-begäranden|Millisekunder|Medel|Tiden mellan när API Management tar emot en begäran från en klient och när den returnerar ett svar till klienten.|Plats, värdnamn|
|Kapacitet|Kapacitet|Procent|Medel|Indikator för belastningen på en API Management instans för att fatta välgrundade beslut om att skala instansen till att hantera mer belastning.|Plats|
|EventHubTotalEvents|Totalt antal EventHub-händelser|Antal|Totalt|Det totala antalet händelser som skickats till EventHub från API Management under en viss period.|Plats|
|EventHubSuccessfulEvents|Lyckade EventHub-händelser|Antal|Totalt|Det totala antalet lyckade EventHub-händelser under en viss period.|Plats|
|EventHubTotalFailedEvents|Misslyckade EventHub-händelser|Antal|Totalt|Det totala antalet misslyckade EventHub-händelser under en viss period.|Plats|
|EventHubRejectedEvents|Avvisade EventHub-händelser|Antal|Totalt|Det totala antalet nekade EventHub-händelser (felaktig konfiguration eller otillåten) under en viss period.|Plats|
|EventHubThrottledEvents|Begränsade EventHub-händelser|Antal|Totalt|Det totala antalet begränsade EventHub-händelser under en viss period.|Plats|
|EventHubTimedoutEvents|Tids gränsen nåddes för EventHub-händelser|Antal|Totalt|Det totala antalet uppnådde tids gränsen för EventHub-händelser under en viss period.|Plats|
|EventHubDroppedEvents|Ignorerade EventHub-händelser|Antal|Totalt|Det totala antalet händelser som hoppades över på grund av att gränsen för kös Tor lek nåddes under en viss period.|Plats|
|EventHubTotalBytesSent|Storlek på EventHub-händelser|Skickade|Totalt|Den totala storleken för EventHub-händelser i byte under en viss period.|Plats|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Runbook, status|
|TotalUpdateDeploymentRuns|Total uppdaterings distribution körs|Antal|Totalt|Totalt antal körningar av program uppdaterings distribution|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Den totala uppdaterings distributions datorn körs|Antal|Totalt|Total distributions dator för program uppdatering körs i en körning av program uppdaterings distribution|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft. batch/batchAccounts

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CoreCount|Dedikerat antal kärnor|Antal|Totalt|Totalt antal dedikerade kärnor i batch-kontot|Inga dimensioner|
|TotalNodeCount|Antal dedikerade noder|Antal|Totalt|Totalt antal dedikerade noder i batch-kontot|Inga dimensioner|
|LowPriorityCoreCount|Antal LowPriority kärnor|Antal|Totalt|Totalt antal låg prioritets kärnor i batch-kontot|Inga dimensioner|
|TotalLowPriorityNodeCount|Antal noder med låg prioritet|Antal|Totalt|Totalt antal noder med låg prioritet i batch-kontot|Inga dimensioner|
|CreatingNodeCount|Antal noder skapas|Antal|Totalt|Antal noder som skapas|Inga dimensioner|
|StartingNodeCount|Antalet noder startas|Antal|Totalt|Antal noder som börjar|Inga dimensioner|
|WaitingForStartTaskNodeCount|Väntar på att starta aktiviteter antal noder|Antal|Totalt|Antal noder som väntar på att start aktiviteten ska slutföras|Inga dimensioner|
|StartTaskFailedNodeCount|Starta aktivitet antal misslyckade noder|Antal|Totalt|Antal noder där start aktiviteten misslyckades|Inga dimensioner|
|IdleNodeCount|Antal inaktiva noder|Antal|Totalt|Antal inaktiva noder|Inga dimensioner|
|OfflineNodeCount|Antal offline-noder|Antal|Totalt|Antal offline-noder|Inga dimensioner|
|RebootingNodeCount|Antalet noder startas om|Antal|Totalt|Antal omstarter av noder|Inga dimensioner|
|ReimagingNodeCount|Antal noder för avbildning|Antal|Totalt|Antal avbildnings noder|Inga dimensioner|
|RunningNodeCount|Antal noder som körs|Antal|Totalt|Antal noder som körs|Inga dimensioner|
|LeavingPoolNodeCount|Antal noder för att lämna pooler|Antal|Totalt|Antal noder som lämnar poolen|Inga dimensioner|
|UnusableNodeCount|Antal noder som inte kan användas|Antal|Totalt|Antal oanvändbara noder|Inga dimensioner|
|PreemptedNodeCount|Antal misslyckade noder|Antal|Totalt|Antal misslyckade noder|Inga dimensioner|
|TaskStartEvent|Aktivitetens start händelser|Antal|Totalt|Totalt antal aktiviteter som har startat|Inga dimensioner|
|TaskCompleteEvent|Uppgift slutförda händelser|Antal|Totalt|Totalt antal slutförda uppgifter|Inga dimensioner|
|TaskFailEvent|Aktivitet, misslyckade händelser|Antal|Totalt|Totalt antal uppgifter som har slutförts i felaktigt tillstånd|Inga dimensioner|
|PoolCreateEvent|Skapa händelser för pool|Antal|Totalt|Totalt antal pooler som har skapats|Inga dimensioner|
|PoolResizeStartEvent|Starta händelser för att ändra storlek på poolen|Antal|Totalt|Totalt antal storleks ändringar för pooler som har startat|Inga dimensioner|
|PoolResizeCompleteEvent|Slutför händelser för storleks ändring av pool|Antal|Totalt|Totalt antal ändrade pooler som har slutförts|Inga dimensioner|
|PoolDeleteStartEvent|Start händelser för borttagning av pool|Antal|Totalt|Totalt antal borttagningar av pooler som har startat|Inga dimensioner|
|PoolDeleteCompleteEvent|Slutför händelse för borttagning av pool|Antal|Totalt|Totalt antal borttagna pooler som har slutförts|Inga dimensioner|
|JobDeleteCompleteEvent|Slutför händelser för borttagning av jobb|Antal|Totalt|Totalt antal jobb som har tagits bort.|Inga dimensioner|
|JobDeleteStartEvent|Start händelser för jobb borttagning|Antal|Totalt|Totalt antal jobb som har begärts att tas bort.|Inga dimensioner|
|JobDisableCompleteEvent|Jobb för att inaktivera slutförda händelser|Antal|Totalt|Totalt antal jobb som har inaktiverats.|Inga dimensioner|
|JobDisableStartEvent|Jobb inaktivera start händelser|Antal|Totalt|Totalt antal jobb som har begärts att inaktive ras.|Inga dimensioner|
|JobStartEvent|Jobb start händelser|Antal|Totalt|Totalt antal jobb som har startats.|Inga dimensioner|
|JobTerminateCompleteEvent|Slutför händelser för avsluta jobb|Antal|Totalt|Totalt antal jobb som har avslut ATS.|Inga dimensioner|
|JobTerminateStartEvent|Jobb som avslutar start händelser|Antal|Totalt|Totalt antal jobb som har begärts att avslutas.|Inga dimensioner|

## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedclients|Anslutna klienter|Antal|Maximal||ShardId|
|totalcommandsprocessed|Totalt antal åtgärder|Antal|Totalt||ShardId|
|cachehits|Cacheträffar|Antal|Totalt||ShardId|
|cachemisses|Cachemissar|Antal|Totalt||ShardId|
|getcommands|Sparas|Antal|Totalt||ShardId|
|setcommands|Definierar|Antal|Totalt||ShardId|
|operationsPerSecond|Åtgärder per sekund|Antal|Maximal||ShardId|
|evictedkeys|Avlägsnade nycklar|Antal|Totalt||ShardId|
|totalkeys|Totalt antal nycklar|Antal|Maximal||ShardId|
|expiredkeys|Förfallna nycklar|Antal|Totalt||ShardId|
|usedmemory|Använt minne|Skickade|Maximal||ShardId|
|usedmemorypercentage|Använd minnes procent|Procent|Maximal||ShardId|
|usedmemoryRss|RSS för använt minne|Skickade|Maximal||ShardId|
|serverLoad|Server belastning|Procent|Maximal||ShardId|
|cacheWrite|Skrivcache|BytesPerSecond|Maximal||ShardId|
|cacheRead|Läsning cache|BytesPerSecond|Maximal||ShardId|
|percentProcessorTime|Processor|Procent|Maximal||ShardId|
|cacheLatency|Mikrosekunder för cache-fördröjning (för hands version)|Antal|Medel||ShardId, SampleType|
|fel|Fel|Antal|Maximal||ShardId, ErrorType|
|connectedclients0|Anslutna klienter (Shard 0)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed0|Totalt antal åtgärder (Shard 0)|Antal|Totalt||Inga dimensioner|
|cachehits0|Cacheträffar (Shard 0)|Antal|Totalt||Inga dimensioner|
|cachemisses0|Cachemissar (Shard 0)|Antal|Totalt||Inga dimensioner|
|getcommands0|Hämtar (Shard 0)|Antal|Totalt||Inga dimensioner|
|setcommands0|Uppsättningar (Shard 0)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond0|Åtgärder per sekund (Shard 0)|Antal|Maximal||Inga dimensioner|
|evictedkeys0|Avlägsnade nycklar (Shard 0)|Antal|Totalt||Inga dimensioner|
|totalkeys0|Totalt antal nycklar (Shard 0)|Antal|Maximal||Inga dimensioner|
|expiredkeys0|Utgångna nycklar (Shard 0)|Antal|Totalt||Inga dimensioner|
|usedmemory0|Använt minne (Shard 0)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss0|Använt minne RSS (Shard 0)|Skickade|Maximal||Inga dimensioner|
|serverLoad0|Server belastning (Shard 0)|Procent|Maximal||Inga dimensioner|
|cacheWrite0|Skrivcache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead0|Läsning av cache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime0|PROCESSOR (Shard 0)|Procent|Maximal||Inga dimensioner|
|connectedclients1|Anslutna klienter (Shard 1)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed1|Totalt antal åtgärder (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachehits1|Cacheträffar (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachemisses1|Cachemissar (Shard 1)|Antal|Totalt||Inga dimensioner|
|getcommands1|Hämtar (Shard 1)|Antal|Totalt||Inga dimensioner|
|setcommands1|Uppsättningar (Shard 1)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond1|Åtgärder per sekund (Shard 1)|Antal|Maximal||Inga dimensioner|
|evictedkeys1|Avlägsnade nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|totalkeys1|Totalt antal nycklar (Shard 1)|Antal|Maximal||Inga dimensioner|
|expiredkeys1|Utgångna nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|usedmemory1|Använt minne (Shard 1)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss1|RSS för använt minne (Shard 1)|Skickade|Maximal||Inga dimensioner|
|serverLoad1|Server belastning (Shard 1)|Procent|Maximal||Inga dimensioner|
|cacheWrite1|Skrivcache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead1|Läsning av cache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime1|PROCESSOR (Shard 1)|Procent|Maximal||Inga dimensioner|
|connectedclients2|Anslutna klienter (Shard 2)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed2|Totalt antal åtgärder (Shard 2)|Antal|Totalt||Inga dimensioner|
|cachehits2|Cacheträffar (Shard 2)|Antal|Totalt||Inga dimensioner|
|cachemisses2|Cachemissar (Shard 2)|Antal|Totalt||Inga dimensioner|
|getcommands2|Hämtar (Shard 2)|Antal|Totalt||Inga dimensioner|
|setcommands2|Uppsättningar (Shard 2)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond2|Åtgärder per sekund (Shard 2)|Antal|Maximal||Inga dimensioner|
|evictedkeys2|Avlägsnade nycklar (Shard 2)|Antal|Totalt||Inga dimensioner|
|totalkeys2|Totalt antal nycklar (Shard 2)|Antal|Maximal||Inga dimensioner|
|expiredkeys2|Utgångna nycklar (Shard 2)|Antal|Totalt||Inga dimensioner|
|usedmemory2|Använt minne (Shard 2)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss2|Använt minne RSS (Shard 2)|Skickade|Maximal||Inga dimensioner|
|serverLoad2|Server belastning (Shard 2)|Procent|Maximal||Inga dimensioner|
|cacheWrite2|Skrivcache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead2|Läsning av cache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime2|PROCESSOR (Shard 2)|Procent|Maximal||Inga dimensioner|
|connectedclients3|Anslutna klienter (Shard 3)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed3|Totalt antal åtgärder (Shard 3)|Antal|Totalt||Inga dimensioner|
|cachehits3|Cacheträffar (Shard 3)|Antal|Totalt||Inga dimensioner|
|cachemisses3|Cachemissar (Shard 3)|Antal|Totalt||Inga dimensioner|
|getcommands3|Hämtar (Shard 3)|Antal|Totalt||Inga dimensioner|
|setcommands3|Uppsättningar (Shard 3)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond3|Åtgärder per sekund (Shard 3)|Antal|Maximal||Inga dimensioner|
|evictedkeys3|Avlägsnade nycklar (Shard 3)|Antal|Totalt||Inga dimensioner|
|totalkeys3|Totalt antal nycklar (Shard 3)|Antal|Maximal||Inga dimensioner|
|expiredkeys3|Utgångna nycklar (Shard 3)|Antal|Totalt||Inga dimensioner|
|usedmemory3|Använt minne (Shard 3)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss3|Använt minne RSS (Shard 3)|Skickade|Maximal||Inga dimensioner|
|serverLoad3|Server belastning (Shard 3)|Procent|Maximal||Inga dimensioner|
|cacheWrite3|Skrivcache (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead3|Läsning av cacheminne (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime3|PROCESSOR (Shard 3)|Procent|Maximal||Inga dimensioner|
|connectedclients4|Anslutna klienter (Shard 4)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed4|Totalt antal åtgärder (Shard 4)|Antal|Totalt||Inga dimensioner|
|cachehits4|Cacheträffar (Shard 4)|Antal|Totalt||Inga dimensioner|
|cachemisses4|Cachemissar (Shard 4)|Antal|Totalt||Inga dimensioner|
|getcommands4|Hämtar (Shard 4)|Antal|Totalt||Inga dimensioner|
|setcommands4|Uppsättningar (Shard 4)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond4|Åtgärder per sekund (Shard 4)|Antal|Maximal||Inga dimensioner|
|evictedkeys4|Avlägsnade nycklar (Shard 4)|Antal|Totalt||Inga dimensioner|
|totalkeys4|Totalt antal nycklar (Shard 4)|Antal|Maximal||Inga dimensioner|
|expiredkeys4|Utgångna nycklar (Shard 4)|Antal|Totalt||Inga dimensioner|
|usedmemory4|Använt minne (Shard 4)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss4|Använt minne RSS (Shard 4)|Skickade|Maximal||Inga dimensioner|
|serverLoad4|Server belastning (Shard 4)|Procent|Maximal||Inga dimensioner|
|cacheWrite4|Skrivcache (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead4|Läsning av cacheminne (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime4|PROCESSOR (Shard 4)|Procent|Maximal||Inga dimensioner|
|connectedclients5|Anslutna klienter (Shard 5)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed5|Totalt antal åtgärder (Shard 5)|Antal|Totalt||Inga dimensioner|
|cachehits5|Cacheträffar (Shard 5)|Antal|Totalt||Inga dimensioner|
|cachemisses5|Cachemissar (Shard 5)|Antal|Totalt||Inga dimensioner|
|getcommands5|Hämtar (Shard 5)|Antal|Totalt||Inga dimensioner|
|setcommands5|Uppsättningar (Shard 5)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond5|Åtgärder per sekund (Shard 5)|Antal|Maximal||Inga dimensioner|
|evictedkeys5|Avlägsnade nycklar (Shard 5)|Antal|Totalt||Inga dimensioner|
|totalkeys5|Totalt antal nycklar (Shard 5)|Antal|Maximal||Inga dimensioner|
|expiredkeys5|Utgångna nycklar (Shard 5)|Antal|Totalt||Inga dimensioner|
|usedmemory5|Använt minne (Shard 5)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss5|Använt minne RSS (Shard 5)|Skickade|Maximal||Inga dimensioner|
|serverLoad5|Server belastning (Shard 5)|Procent|Maximal||Inga dimensioner|
|cacheWrite5|Skrivcache (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead5|Läsning av cacheminne (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime5|PROCESSOR (Shard 5)|Procent|Maximal||Inga dimensioner|
|connectedclients6|Anslutna klienter (Shard 6)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed6|Totalt antal åtgärder (Shard 6)|Antal|Totalt||Inga dimensioner|
|cachehits6|Cacheträffar (Shard 6)|Antal|Totalt||Inga dimensioner|
|cachemisses6|Cachemissar (Shard 6)|Antal|Totalt||Inga dimensioner|
|getcommands6|Hämtar (Shard 6)|Antal|Totalt||Inga dimensioner|
|setcommands6|Uppsättningar (Shard 6)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond6|Åtgärder per sekund (Shard 6)|Antal|Maximal||Inga dimensioner|
|evictedkeys6|Avlägsnade nycklar (Shard 6)|Antal|Totalt||Inga dimensioner|
|totalkeys6|Totalt antal nycklar (Shard 6)|Antal|Maximal||Inga dimensioner|
|expiredkeys6|Utgångna nycklar (Shard 6)|Antal|Totalt||Inga dimensioner|
|usedmemory6|Använt minne (Shard 6)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss6|Använt minne RSS (Shard 6)|Skickade|Maximal||Inga dimensioner|
|serverLoad6|Server belastning (Shard 6)|Procent|Maximal||Inga dimensioner|
|cacheWrite6|Skrivcache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead6|Läsning av cache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime6|PROCESSOR (Shard 6)|Procent|Maximal||Inga dimensioner|
|connectedclients7|Anslutna klienter (Shard 7)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed7|Totalt antal åtgärder (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachehits7|Cacheträffar (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachemisses7|Cachemissar (Shard 7)|Antal|Totalt||Inga dimensioner|
|getcommands7|Hämtar (Shard 7)|Antal|Totalt||Inga dimensioner|
|setcommands7|Uppsättningar (Shard 7)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond7|Åtgärder per sekund (Shard 7)|Antal|Maximal||Inga dimensioner|
|evictedkeys7|Avlägsnade nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|totalkeys7|Totalt antal nycklar (Shard 7)|Antal|Maximal||Inga dimensioner|
|expiredkeys7|Utgångna nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|usedmemory7|Använt minne (Shard 7)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss7|Använt minne RSS (Shard 7)|Skickade|Maximal||Inga dimensioner|
|serverLoad7|Server belastning (Shard 7)|Procent|Maximal||Inga dimensioner|
|cacheWrite7|Skrivcache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead7|Läsning av cache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime7|PROCESSOR (Shard 7)|Procent|Maximal||Inga dimensioner|
|connectedclients8|Anslutna klienter (Shard 8)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed8|Totalt antal åtgärder (Shard 8)|Antal|Totalt||Inga dimensioner|
|cachehits8|Cacheträffar (Shard 8)|Antal|Totalt||Inga dimensioner|
|cachemisses8|Cachemissar (Shard 8)|Antal|Totalt||Inga dimensioner|
|getcommands8|Hämtar (Shard 8)|Antal|Totalt||Inga dimensioner|
|setcommands8|Uppsättningar (Shard 8)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond8|Åtgärder per sekund (Shard 8)|Antal|Maximal||Inga dimensioner|
|evictedkeys8|Avlägsnade nycklar (Shard 8)|Antal|Totalt||Inga dimensioner|
|totalkeys8|Totalt antal nycklar (Shard 8)|Antal|Maximal||Inga dimensioner|
|expiredkeys8|Utgångna nycklar (Shard 8)|Antal|Totalt||Inga dimensioner|
|usedmemory8|Använt minne (Shard 8)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss8|Använt minne RSS (Shard 8)|Skickade|Maximal||Inga dimensioner|
|serverLoad8|Server belastning (Shard 8)|Procent|Maximal||Inga dimensioner|
|cacheWrite8|Skrivcache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead8|Läsning av cache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime8|PROCESSOR (Shard 8)|Procent|Maximal||Inga dimensioner|
|connectedclients9|Anslutna klienter (Shard 9)|Antal|Maximal||Inga dimensioner|
|totalcommandsprocessed9|Totalt antal åtgärder (Shard 9)|Antal|Totalt||Inga dimensioner|
|cachehits9|Cacheträffar (Shard 9)|Antal|Totalt||Inga dimensioner|
|cachemisses9|Cachemissar (Shard 9)|Antal|Totalt||Inga dimensioner|
|getcommands9|Hämtar (Shard 9)|Antal|Totalt||Inga dimensioner|
|setcommands9|Uppsättningar (Shard 9)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond9|Åtgärder per sekund (Shard 9)|Antal|Maximal||Inga dimensioner|
|evictedkeys9|Avlägsnade nycklar (Shard 9)|Antal|Totalt||Inga dimensioner|
|totalkeys9|Totalt antal nycklar (Shard 9)|Antal|Maximal||Inga dimensioner|
|expiredkeys9|Utgångna nycklar (Shard 9)|Antal|Totalt||Inga dimensioner|
|usedmemory9|Använt minne (Shard 9)|Skickade|Maximal||Inga dimensioner|
|usedmemoryRss9|Använt minne RSS (Shard 9)|Skickade|Maximal||Inga dimensioner|
|serverLoad9|Server belastning (Shard 9)|Procent|Maximal||Inga dimensioner|
|cacheWrite9|Skrivcache (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead9|Läsning av cacheminne (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime9|PROCESSOR (Shard 9)|Procent|Maximal||Inga dimensioner|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|Inga dimensioner|
|Nätverk – inkommande|Nätverk – inkommande|Skickade|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|Inga dimensioner|
|Nätverk – utgående|Nätverk – utgående|Skickade|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|Inga dimensioner|
|Disk-lästa byte/s|Disk läsning|BytesPerSecond|Medel|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|Inga dimensioner|
|Disk-skrivna byte/s|Disk skrivning|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|Inga dimensioner|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Disk-Läs-IOPS.|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Skriv IOPS för disk.|Inga dimensioner|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domän namn/platser/roller

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|RoleInstanceId|
|Nätverk – inkommande|Nätverk – inkommande|Skickade|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|RoleInstanceId|
|Nätverk – utgående|Nätverk – utgående|Skickade|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|RoleInstanceId|
|Disk-lästa byte/s|Disk läsning|BytesPerSecond|Medel|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|RoleInstanceId|
|Disk-skrivna byte/s|Disk skrivning|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|RoleInstanceId|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Disk-Läs-IOPS.|RoleInstanceId|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Skriv IOPS för disk.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalCalls|Totalt antal anrop|Antal|Totalt|Totalt antal anrop.|ApiName, OperationName, region|
|SuccessfulCalls|Lyckade anrop|Antal|Totalt|Antal lyckade anrop.|ApiName, OperationName, region|
|TotalErrors|Totalt antal fel|Antal|Totalt|Totalt antal anrop med fel svar (HTTP-svarskod 4xx eller 5xx).|ApiName, OperationName, region|
|BlockedCalls|Blockerade anrop|Antal|Totalt|Antal anrop som har överskridit frekvensen eller kvot gränsen.|ApiName, OperationName, region|
|ServerErrors|Server fel|Antal|Totalt|Antal anrop med internt tjänst fel (HTTP-svarskod 5xx).|ApiName, OperationName, region|
|ClientErrors|Klient fel|Antal|Totalt|Antal anrop med fel på klient sidan (HTTP-svarskod 4xx).|ApiName, OperationName, region|
|Datain|Data i|Skickade|Totalt|Storlek på inkommande data i byte.|ApiName, OperationName, region|
|Data|Data ut|Skickade|Totalt|Storlek på utgående data i byte.|ApiName, OperationName, region|
|Svarstid|Svarstid|Millisekunder|Medel|Svars tid i millisekunder.|ApiName, OperationName, region|
|CharactersTranslated|Översatta tecken|Antal|Totalt|Totalt antal tecken i begäran om inkommande text.|ApiName, OperationName, region|
|CharactersTrained|Upptränade tecken|Antal|Totalt|Totalt antal tecken som har tränats.|ApiName, OperationName, region|
|SpeechSessionDuration|Tal sessionens varaktighet|Sekunder|Totalt|Total varaktighet för talläget på några sekunder.|ApiName, OperationName, region|
|TotalTransactions|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner.|Inga dimensioner|
|TotalTokenCalls|Totalt antal token-anrop|Antal|Totalt|Totalt antal token-anrop.|ApiName, OperationName, region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inga dimensioner|
|Nätverk – inkommande|Nätverk i fakturerbart|Skickade|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Fakturerbart för nätverk|Skickade|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Skickade|Totalt|Byte som lästs från disk under övervaknings perioden|Inga dimensioner|
|Disk-skrivna byte|Disk-skrivna byte|Skickade|Totalt|Byte som skrivits till disk under övervaknings perioden|Inga dimensioner|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Disk-Läs-IOPS|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Skriv IOPS för disk|Inga dimensioner|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medel|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk (inaktuellt)|Antal|Medel|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|KÖDJUP för operativ system per disk|OS-KÖDJUP (inaktuellt)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Data disk Queue djup|Data disk Queue djup (för hands version)|Antal|Medel|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Inkommande flöden|Inkommande flöden (förhands granskning)|Antal|Medel|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inga dimensioner|
|Utgående flöden|Utgående flöden (förhands granskning)|Antal|Medel|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Högsta skapande frekvens för inkommande flöden|Maximal skapande frekvens för inkommande flöden (för hands version)|CountPerSecond|Medel|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inga dimensioner|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden (för hands version)|CountPerSecond|Medel|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medel|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medel|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medel|Läsning av Premium OS-diskcachen|Inga dimensioner|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medel|Read missar i Premium OS-diskcachen|Inga dimensioner|
|Totalt nätverk|Totalt nätverk|Skickade|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Totalt nätverk|Totalt nätverk|Skickade|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|VMName|
|Nätverk – inkommande|Nätverk i fakturerbart|Skickade|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|VMName|
|Nätverk – utgående|Fakturerbart för nätverk|Skickade|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|VMName|
|Lästa byte på disk|Lästa byte på disk|Skickade|Totalt|Byte som lästs från disk under övervaknings perioden|VMName|
|Disk-skrivna byte|Disk-skrivna byte|Skickade|Totalt|Byte som skrivits till disk under övervaknings perioden|VMName|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Disk-Läs-IOPS|VMName|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Skriv IOPS för disk|VMName|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medel|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk (inaktuellt)|Antal|Medel|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|KÖDJUP för operativ system per disk|OS-KÖDJUP (inaktuellt)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|LUN, VMName|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|LUN, VMName|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Data disk Queue djup|Data disk Queue djup (för hands version)|Antal|Medel|Data diskens ködjup (eller Kölängd)|LUN, VMName|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|VMName|
|Inkommande flöden|Inkommande flöden (förhands granskning)|Antal|Medel|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|VMName|
|Utgående flöden|Utgående flöden (förhands granskning)|Antal|Medel|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|VMName|
|Högsta skapande frekvens för inkommande flöden|Maximal skapande frekvens för inkommande flöden (för hands version)|CountPerSecond|Medel|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|VMName|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden (för hands version)|CountPerSecond|Medel|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|VMName|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medel|Läsning av Premium data disk-cache|LUN, VMName|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medel|Read missar i Premium data-diskcachen|LUN, VMName|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medel|Läsning av Premium OS-diskcachen|VMName|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medel|Read missar i Premium OS-diskcachen|VMName|
|Totalt nätverk|Totalt nätverk|Skickade|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|VMName|
|Totalt nätverk|Totalt nätverk|Skickade|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|CPU-användning|Antal|Medel|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Minnes användning|Skickade|Medel|Total minnes användning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Mottagna nätverks byte per sekund|Skickade|Medel|Mottagna nätverks byte per sekund.|Inga dimensioner|
|NetworkBytesTransmittedPerSecond|Överförda nätverks byte per sekund|Skickade|Medel|Överförda nätverks byte per sekund.|Inga dimensioner|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalPullCount|Totalt antal hämtningar|Antal|Medel|Totalt antal bild hämtningar|Inga dimensioner|
|SuccessfulPullCount|Antal lyckade pull-överföringar|Antal|Medel|Antal lyckade bild hämtningar|Inga dimensioner|
|TotalPushCount|Totalt antal push-meddelanden|Antal|Medel|Totalt antal push-meddelanden för avbildningar|Inga dimensioner|
|SuccessfulPushCount|Antal lyckade push-meddelanden|Antal|Medel|Antal lyckade avbildnings push-meddelanden|Inga dimensioner|
|RunDuration|Körnings tid|Millisekunder|Totalt|Körnings tid i millisekunder|Inga dimensioner|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Antal|Totalt|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Inga dimensioner|
|kube_node_status_allocatable_memory_bytes|Total mängd tillgängligt minne i ett hanterat kluster|Skickade|Totalt|Total mängd tillgängligt minne i ett hanterat kluster|Inga dimensioner|
|kube_pod_status_ready|Antal poddar i klart läge|Antal|Totalt|Antal poddar i klart läge|namnrymd, Pod|
|kube_node_status_condition|Status för olika nod villkor|Antal|Totalt|Status för olika nod villkor|villkor, status, status2, nod|
|kube_pod_status_phase|Antal poddar per fas|Antal|Totalt|Antal poddar per fas|fas, namnrymd, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft. CustomerInsights/hubbar

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API-anrop|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationSuccessfulLines|Raderna för att mappa import åtgärd har slutförts|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationFailedLines|Det gick inte att mappa rader i import åtgärden|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationTotalLines|Mappa totalt antal rader för import åtgärd|Antal|Totalt||Inga dimensioner|
|DCIMappingImportOperationRuntimeInSeconds|Mappar import åtgärds körning på några sekunder|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundProfileExportSucceeded|Utgående profil har exporter ATS|Antal|Totalt||Inga dimensioner|
|DCIOutboundProfileExportFailed|Det gick inte att exportera utgående profil|Antal|Totalt||Inga dimensioner|
|DCIOutboundProfileExportDuration|Export varaktighet för utgående profil|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiExportSucceeded|Utgående KPI-export lyckades|Antal|Totalt||Inga dimensioner|
|DCIOutboundKpiExportFailed|Utgående KPI-export misslyckades|Antal|Totalt||Inga dimensioner|
|DCIOutboundKpiExportDuration|Utgående KPI-exportens varaktighet|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiExportStarted|Utgående KPI-export har startat|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiRecordCount|Antal utgående KPI-poster|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundProfileExportCount|Antal exporter av utgående profil|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialProfileExportFailed|Det gick inte att exportera utgående initial profil|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialProfileExportSucceeded|Den utgående initial profil exporten lyckades|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialKpiExportFailed|Den utgående inledande KPI-exporten misslyckades|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialKpiExportSucceeded|Utgående inledande KPI-export lyckades|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundInitialProfileExportDurationInSeconds|Utgående tid för export av initial profil i sekunder|Sekunder|Totalt||Inga dimensioner|
|AdlaJobForStandardKpiFailed|Adla-jobbet för standard-KPI misslyckades på några sekunder|Sekunder|Totalt||Inga dimensioner|
|AdlaJobForStandardKpiTimeOut|Adla-jobb för standard-KPI-tidsgräns i sekunder|Sekunder|Totalt||Inga dimensioner|
|AdlaJobForStandardKpiCompleted|Adla-jobb för standard-KPI har slutförts på några sekunder|Sekunder|Totalt||Inga dimensioner|
|ImportASAValuesFailed|Antal misslyckade import ASA-värden|Antal|Totalt||Inga dimensioner|
|ImportASAValuesSucceeded|Antal lyckade import-värden för ASA|Antal|Totalt||Inga dimensioner|
|DCIProfilesCount|Antal profil instanser|Antal|pågå||Inga dimensioner|
|DCIInteractionsPerMonthCount|Antal interaktioner per månad|Antal|pågå||Inga dimensioner|
|DCIKpisCount|KPI-antal|Antal|pågå||Inga dimensioner|
|DCISegmentsCount|Antal segment|Antal|pågå||Inga dimensioner|
|DCIPredictiveMatchPoliciesCount|Förutsägelse matchnings antal|Antal|pågå||Inga dimensioner|
|DCIPredictionsCount|Antal förutsägelser|Antal|pågå||Inga dimensioner|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|NICReadThroughput|Läs data flöde (nätverk)|BytesPerSecond|Medel|Läs data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|NICWriteThroughput|Skriv data flöde (nätverk)|BytesPerSecond|Medel|Skriv data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|CloudReadThroughputPerShare|Data flöde för moln hämtning (resurs)|BytesPerSecond|Medel|Ladda ned data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|CloudUploadThroughputPerShare|Moln överförings data flöde (resurs)|BytesPerSecond|Medel|Ladda upp data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|BytesUploadedToCloudPerShare|Överförda moln byte (resurs)|Skickade|Medel|Det totala antalet byte som överförs till Azure från en resurs under rapporterings perioden.|Dela|
|Enhet|Total kapacitet|Skickade|Medel|Total kapacitet|Inga dimensioner|
|Availablecapacity;)|Tillgänglig kapacitet|Skickade|Medel|Tillgänglig kapacitet i byte under rapporterings perioden.|Inga dimensioner|
|CloudUploadThroughput|Moln överförings data flöde|BytesPerSecond|Medel|Moln överförings data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|CloudReadThroughput|Data flöde för moln hämtning|BytesPerSecond|Medel|Molnet Ladda ned data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|BytesUploadedToCloud|Överförda moln byte (enhet)|Skickade|Medel|Det totala antalet byte som överförs till Azure från en enhet under rapporterings perioden.|Inga dimensioner|
|HyperVVirtualProcessorUtilization|Edge Compute-procent CPU|Procent|Medel|CPU-användning i procent|InstanceName|
|HyperVMemoryUtilization|Edge Compute-minnes användning|Procent|Medel|Mängden RAM-minne som används|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FailedRuns|Misslyckade körningar|Antal|Totalt||pipelineName, activityName|
|SuccessfulRuns|Lyckade körningar|Antal|Totalt||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PipelineFailedRuns|Misslyckad pipeline kör mått|Antal|Totalt||FailureType, namn|
|PipelineSucceededRuns|Slutförd pipeline kör mått|Antal|Totalt||FailureType, namn|
|ActivityFailedRuns|Misslyckad aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Genomförd aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Misslyckad utlösare kör mått|Antal|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Lyckade utlösare kör mått|Antal|Totalt||Namn, FailureType|
|IntegrationRuntimeCpuPercentage|PROCESSOR användning för integration runtime|Procent|Medel||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAvailableMemory|Tillgängligt minne för integration runtime|Skickade|Medel||IntegrationRuntimeName, nodnamn|
|MaxAllowedResourceCount|Maximalt antal tillåtna entiteter|Antal|Maximal||Inga dimensioner|
|MaxAllowedFactorySizeInGbUnits|Högsta tillåtna fabriks storlek (GB enhet)|Antal|Maximal||Inga dimensioner|
|ResourceCount|Totalt antal entiteter|Antal|Maximal||Inga dimensioner|
|FactorySizeInGbUnits|Total fabriks storlek (GB enhet)|Antal|Maximal||Inga dimensioner|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/konton

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobEndedSuccess|Slutförda jobb|Antal|Totalt|Antal lyckade jobb.|Inga dimensioner|
|JobEndedFailure|Misslyckade jobb|Antal|Totalt|Antal misslyckade jobb.|Inga dimensioner|
|JobEndedCancelled|Avbrutna jobb|Antal|Totalt|Antal avbrutna jobb.|Inga dimensioner|
|JobAUEndedSuccess|Lyckad AU-tid|Sekunder|Totalt|Total AU-tid för lyckade jobb.|Inga dimensioner|
|JobAUEndedFailure|Misslyckad AU-tid|Sekunder|Totalt|Total AU-tid för misslyckade jobb.|Inga dimensioner|
|JobAUEndedCancelled|Tid för avbruten AU|Sekunder|Totalt|Total AU-tid för avbrutna jobb.|Inga dimensioner|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Skickade|Maximal|Den totala mängden data som lagras i kontot.|Inga dimensioner|
|DataWritten|Skrivna data|Skickade|Totalt|Total mängd data som skrivs till kontot.|Inga dimensioner|
|DataRead|Lästa data|Skickade|Totalt|Total mängd data som lästs från kontot.|Inga dimensioner|
|WriteRequests|Skriv förfrågningar|Antal|Totalt|Antal data skrivnings begär anden till kontot.|Inga dimensioner|
|ReadRequests|Läs begär Anden|Antal|Totalt|Antal data läsnings begär anden till kontot.|Inga dimensioner|

## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|memory_percent|Minnes procent|Procent|Medel|Minnes procent|Inga dimensioner|
|io_consumption_percent|I/o procent|Procent|Medel|I/o procent|Inga dimensioner|
|storage_percent|Lagrings procent|Procent|Medel|Lagrings procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Skickade|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagrings gräns|Skickade|Medel|Lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medel|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Server logg lagring används|Skickade|Medel|Server logg lagring används|Inga dimensioner|
|serverlog_storage_limit|Server logg lagrings gräns|Skickade|Medel|Server logg lagrings gräns|Inga dimensioner|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Antal|Medel|Fördröjning för replikering på några sekunder|Inga dimensioner|
|backup_storage_used|Lagring av säkerhets kopior som används|Skickade|Medel|Lagring av säkerhets kopior som används|Inga dimensioner|
|network_bytes_egress|Nätverk – utgående|Skickade|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk – inkommande|Skickade|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|memory_percent|Minnes procent|Procent|Medel|Minnes procent|Inga dimensioner|
|io_consumption_percent|I/o procent|Procent|Medel|I/o procent|Inga dimensioner|
|storage_percent|Lagrings procent|Procent|Medel|Lagrings procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Skickade|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagrings gräns|Skickade|Medel|Lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medel|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Server logg lagring används|Skickade|Medel|Server logg lagring används|Inga dimensioner|
|serverlog_storage_limit|Server logg lagrings gräns|Skickade|Medel|Server logg lagrings gräns|Inga dimensioner|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Antal|Medel|Fördröjning för replikering på några sekunder|Inga dimensioner|
|backup_storage_used|Lagring av säkerhets kopior som används|Skickade|Medel|Lagring av säkerhets kopior som används|Inga dimensioner|
|network_bytes_egress|Nätverk – utgående|Skickade|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk – inkommande|Skickade|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|memory_percent|Minnes procent|Procent|Medel|Minnes procent|Inga dimensioner|
|io_consumption_percent|I/o procent|Procent|Medel|I/o procent|Inga dimensioner|
|storage_percent|Lagrings procent|Procent|Medel|Lagrings procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Skickade|Medel|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagrings gräns|Skickade|Medel|Lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medel|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Server logg lagring används|Skickade|Medel|Server logg lagring används|Inga dimensioner|
|serverlog_storage_limit|Server logg lagrings gräns|Skickade|Medel|Server logg lagrings gräns|Inga dimensioner|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|backup_storage_used|Lagring av säkerhets kopior som används|Skickade|Medel|Lagring av säkerhets kopior som används|Inga dimensioner|
|network_bytes_egress|Nätverk – utgående|Skickade|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk – inkommande|Skickade|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|pg_replica_log_delay_in_seconds|Replik fördröjning|Sekunder|Maximal|Replik fördröjning i sekunder|Inga dimensioner|
|pg_replica_log_delay_in_bytes|Maximal fördröjning mellan repliker|Skickade|Maximal|Fördröjning i byte för den flesta isolerings repliken|Inga dimensioner|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|memory_percent|Minnes procent|Procent|Medel|Minnes procent|Inga dimensioner|
|IOPS|IOPS|Antal|Medel|I/o-åtgärder per sekund|Inga dimensioner|
|storage_percent|Lagrings procent|Procent|Medel|Lagrings procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Skickade|Medel|Använt lagringsutrymme|Inga dimensioner|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inga dimensioner|
|network_bytes_egress|Nätverk – utgående|Skickade|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk – inkommande|Skickade|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|D2C. telemetri. ingress. allProtocol|Skicka försök för telemetri|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inga dimensioner|
|D2C. telemetri. ingress. lyckades|Meddelande om telemetri|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inga dimensioner|
|C2D. commands. utgående. Complete. lyckades|Slutförda kommandon|Antal|Totalt|Antalet moln-till-enhet-kommandon som har slutförts av enheten|Inga dimensioner|
|C2D. commands. utgående. Abandon. lyckades|Övergivna kommandon|Antal|Totalt|Antalet moln-till-enhet-kommandon som avbrutits av enheten|Inga dimensioner|
|C2D. commands. rekasta. Success|Nekade kommandon|Antal|Totalt|Antal moln-till-enhet-kommandon som avvisats av enheten|Inga dimensioner|
|enheter. totalDevices|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|Devices. connectedDevices. allProtocol|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|D2C. telemetri. utgående. lyckades|Routning: telemetri meddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inga dimensioner|
|D2C. telemetri. utgående.|Routning: telemetri ignoreras |Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inga dimensioner|
|D2C. telemetri. utgående. överblivna|Routning: telemetri-meddelanden har överblivna |Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inga dimensioner|
|D2C. telemetri. utgående. ogiltig|Routning: telemetri-meddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inga dimensioner|
|D2C. telemetri. utgående. fallback|Routning: meddelanden levererade till reserv|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inga dimensioner|
|D2C. endpoints. utgående. eventHubs|Routning: meddelanden levererade till Händelsehubben|Antal|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|D2C. endpoints. latens. eventHubs|Routning: meddelande fördröjning för Event Hub|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusQueues|Routning: meddelanden levererade till Service Bus kö|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusQueues|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusTopics|Routning: meddelanden levererade till Service Bus ämnet|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusTopics|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inga dimensioner|
|D2C. endpoints. utgående. Builtity. events|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser). Det här måttet börjar bara fungera när routning är aktiverat (https://aka.ms/iotrouting) för IoT Hub.|Inga dimensioner|
|D2C. endpoints. latens. Builtin. events|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser). Det här måttet börjar bara fungera när routning är aktiverat (https://aka.ms/iotrouting) för IoT Hub.|Inga dimensioner|
|D2C. endpoints. utgående. Storage|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. svars tid. Storage|Routning: meddelande fördröjning för lagring|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. byte|Routning: data som levereras till lagring|Skickade|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. blob|Routning: blobbar levererade till lagring|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inga dimensioner|
|EventGridDeliveries|Event Grid leveranser (för hands version)|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar typen av händelse (https://aka.ms/ioteventgrid).|Resultat, EventType|
|EventGridLatency|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|Typ|
|D2C. delad. lyckades|Lyckades dubbla läsningar från enheter|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inga dimensioner|
|D2C. delad.|Misslyckade dubbla läsningar från enheter|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C., delad. storlek|Svars storlek för dubbla läsningar från enheter|Skickade|Medel|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C. delad. Update. lyckades|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. delad. Update. Failure|Misslyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. dubbla. Update. size|Storlek på dubbla uppdateringar från enheter|Skickade|Medel|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|C2D. Methods. Success|Direkta metod anrop|Antal|Totalt|Antalet lyckade direkta metod anrop.|Inga dimensioner|
|C2D. Methods. Failure|Misslyckade direkta metod anrop|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Inga dimensioner|
|C2D. Methods. requestSize|Begär ande storlek för direkta metod anrop|Skickade|Medel|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inga dimensioner|
|C2D. Methods. responseSize|Svars storlek för direkta metod anrop|Skickade|Medel|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inga dimensioner|
|C2D. delad. lyckades|Lyckades dubbla läspaket från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inga dimensioner|
|C2D. delad.|Det gick inte att dubbla läsningar från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D., delad. storlek|Svars storlek för dubbla läsningar från Server delen|Skickade|Medel|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inga dimensioner|
|C2D. delad. Update. lyckades|Lyckades dubbla uppdateringar från Server delen|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inga dimensioner|
|C2D. delad. Update. Failure|Misslyckade dubbla uppdateringar från Server delen|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D. dubbla. Update. size|Storlek på dubbla uppdateringar från Server delen|Skickade|Medel|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inga dimensioner|
|twinQueries. lyckades|Lyckades dubbla frågor|Antal|Totalt|Antalet lyckade dubbla frågor.|Inga dimensioner|
|twinQueries. Failure|Misslyckade dubbla frågor|Antal|Totalt|Antalet misslyckade dubbla frågor.|Inga dimensioner|
|twinQueries.resultSize|Resultat storlek för dubbla frågor|Skickade|Medel|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inga dimensioner|
|Jobs. createTwinUpdateJob. lyckades|Skapandet av dubbla uppdaterings jobb lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|Jobs. createTwinUpdateJob. Failure|Det gick inte att skapa dubbla uppdaterings jobb|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|Jobs. createDirectMethodJob. lyckades|Lyckade skapande av metod anrops jobb|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inga dimensioner|
|Jobs. createDirectMethodJob. Failure|Det gick inte att skapa metod anrops jobb|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inga dimensioner|
|Jobs. listJobs. lyckades|Lyckade anrop till List jobb|Antal|Totalt|Antalet lyckade anrop till List jobb.|Inga dimensioner|
|Jobs. listJobs. Failure|Misslyckade anrop till List jobb|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Inga dimensioner|
|Jobs. cancelJob. lyckades|Slutförda jobb avbokningar|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|Jobs. cancelJob. Failure|Misslyckade jobb-annulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|Jobs. queryJobs. lyckades|Slutförda jobb frågor|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inga dimensioner|
|Jobs. queryJobs. Failure|Misslyckade jobb frågor|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inga dimensioner|
|jobb. slutfört|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inga dimensioner|
|jobb. misslyckades|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inga dimensioner|
|D2C. telemetri. ingress. sendThrottle|Antal begränsnings fel|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inga dimensioner|
|dailyMessageQuotaUsed|Totalt antal meddelanden som används|Antal|Medel|Antal sammanlagt antal meddelanden som använts idag. Detta är ett ackumulerat värde som återställs till noll vid 00:00 UTC varje dag.|Inga dimensioner|
|deviceDataUsage|Total användning av enhets data|Skickade|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|totalDeviceCount|Totalt antal enheter (förhands granskning)|Antal|Medel|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|connectedDeviceCount|Anslutna enheter (förhands granskning)|Antal|Medel|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|konfigurationer|Konfigurations mått|Antal|Totalt|Mått för konfigurations åtgärder|Inga dimensioner|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Registrerings försök|Antal|Totalt|Antal enhets registreringar som försökts|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Tilldelade enheter|Antal|Totalt|Antal enheter som har tilldelats till en IoT-hubb|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attesterings försök|Antal|Totalt|Antal försök till enhets attestering|ProvisioningServiceName, status, protokoll|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AvailableStorage|Tillgängligt lagrings utrymme|Skickade|Totalt|Totalt tillgängligt lagrings utrymme som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|CassandraConnectionClosures|Cassandra-anslutningens stängningar|Antal|Totalt|Antalet Cassandra-anslutningar som stängdes, rapporteras med en 1 minuts kornig het|Region, ClosureReason|
|CassandraRequestCharges|Avgifter för Cassandra-begäran|Antal|Totalt|Ru: er förbrukat för Cassandra begär Anden|DatabaseName, samlings namn, region, OperationType, ResourceType|
|CassandraRequests|Cassandra-begäranden|Antal|Antal|Antal Cassandra-begäranden som gjorts|DatabaseName, samlings namn, region, OperationType, ResourceType, ErrorCode|
|DataUsage|Dataanvändning|Skickade|Totalt|Total data användning rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentCount|Antal dokument|Antal|Totalt|Totalt antal dokument som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentQuota|Dokument kvot|Skickade|Totalt|Total lagrings kvot som rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|IndexUsage|Index användning|Skickade|Totalt|Total användning av index rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|MetadataRequests|Begär Anden om metadata|Antal|Antal|Antal metadata-begäranden. Cosmos DB behåller systemets metadata-samling för varje konto, så att du kan räkna upp samlingar, databaser osv. och deras konfigurationer kostnads fritt.|DatabaseName, samlings namn, region, StatusCode, |
|MongoRequestCharge|Mongo begär ande avgift|Antal|Totalt|Mongo enheter för förbrukad begäran|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequests|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden som gjorts|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|ProvisionedThroughput|Etablerat dataflöde|Antal|Maximal|Etablerat dataflöde|DatabaseName, samlings namn|
|ReplicationLatency|Fördröjning för P99-replikering|Millisekunder|Medel|P99 för replikering i käll-och mål regioner för geo-aktiverat konto|SourceRegion, TargetRegion|
|ServiceAvailability|Tjänst tillgänglighet|Procent|Medel|Konto begär tillgänglighet på en timme, dag eller månads kornig het|Inga dimensioner|
|TotalRequestUnits|Totalt antal enheter för programbegäran|Antal|Totalt|Förbrukade enheter för begär Ande|DatabaseName, samlings namn, region, StatusCode, OperationType|
|TotalRequests|Totalt antal förfrågningar|Antal|Antal|Antal begär Anden som gjorts|DatabaseName, samlings namn, region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Antal|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inga dimensioner|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medel|Varaktighet för mål bearbetning i millisekunder|Inga dimensioner|
|DroppedEventCount|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|DeadLetteredCount|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishFailCount|Misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Antal|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|

## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|Entitetsnamnet |
|ServerErrors|Server fel.|Antal|Totalt|Server fel för Microsoft. EventHub.|Entitetsnamnet |
|UserErrors|Användar fel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|Entitetsnamnet |
|QuotaExceededErrors|Kvoten överskreds.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|Entitetsnamnet |
|ThrottledRequests|Begränsade begär Anden.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|Entitetsnamnet |
|IncomingRequests|Inkommande begär Anden|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Entitetsnamnet|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|OutgoingMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|IncomingBytes|Inkommande byte.|Skickade|Totalt|Inkommande byte för Microsoft. EventHub.|Entitetsnamnet|
|OutgoingBytes|Utgående byte.|Skickade|Totalt|Utgående byte för Microsoft. EventHub.|Entitetsnamnet|
|ActiveConnections|ActiveConnections|Antal|Medel|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inga dimensioner|
|ConnectionsOpened|Öppna anslutningar.|Antal|Medel|Anslutningar som har öppnats för Microsoft. EventHub.|Entitetsnamnet|
|ConnectionsClosed|Stängda anslutningar.|Antal|Medel|Stängda anslutningar för Microsoft. EventHub.|Entitetsnamnet|
|CaptureBacklog|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Entitetsnamnet|
|CapturedMessages|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|CapturedBytes|Hämtade byte.|Skickade|Totalt|Hämtade byte för Microsoft. EventHub.|Entitetsnamnet|
|Storlek|Storlek|Skickade|Medel|Storlek på en EventHub i byte.|Entitetsnamnet|
|INREQS|Inkommande begär Anden (inaktuellt)|Antal|Totalt|Totalt antal inkommande sändnings begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|SUCCREQ|Lyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal slutförda begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|FAILREQ|Misslyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|SVRBSY|Serverns upptaget fel (inaktuellt)|Antal|Totalt|Totalt antal upptagen server-fel för en namnrymd (inaktuell)|Inga dimensioner|
|MELLAN|Interna Server fel (inaktuellt)|Antal|Totalt|Totalt antal interna Server fel för en namnrymd (inaktuell)|Inga dimensioner|
|MISCERR|Andra fel (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|INMSGS|Inkommande meddelanden (inaktuellt) (inaktuellt)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namn område. Måttet är föråldrat. Använd måtten inkommande e-postmeddelanden i stället (inaktuellt)|Inga dimensioner|
|EHINMSGS|Inkommande meddelanden (inaktuellt)|Antal|Totalt|Totalt antal inkommande meddelanden för en namnrymd (inaktuell)|Inga dimensioner|
|OUTMSGS|Utgående meddelanden (inaktuellt) (inaktuellt)|Antal|Totalt|Totalt antal utgående meddelanden för ett namn område. Måttet är föråldrat. Använd utgående meddelande mått i stället (inaktuellt)|Inga dimensioner|
|EHOUTMSGS|Utgående meddelanden (inaktuellt)|Antal|Totalt|Totalt antal utgående meddelanden för en namnrymd (inaktuell)|Inga dimensioner|
|EHINMBS|Inkommande byte (inaktuellt) (inaktuellt)|Skickade|Totalt|Inkommande meddelande data flöde för Event Hub för ett namn område. Måttet är föråldrat. Använd måttet inkommande byte i stället (inaktuellt)|Inga dimensioner|
|EHINBYTES|Inkommande byte (inaktuellt)|Skickade|Totalt|Inkommande meddelande data flöde för Event Hub för en namnrymd (inaktuell)|Inga dimensioner|
|EHOUTMBS|Utgående byte (inaktuellt) (inaktuellt)|Skickade|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd. Måttet är föråldrat. Använd utgående byte-mått i stället (inaktuellt)|Inga dimensioner|
|EHOUTBYTES|Utgående byte (inaktuellt)|Skickade|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd (inaktuell)|Inga dimensioner|
|EHABL|Arkivera efter släpning meddelanden (inaktuellt)|Antal|Totalt|Event Hub arkivera meddelanden i efter släpning för en namnrymd (inaktuell)|Inga dimensioner|
|EHAMSGS|Arkivera meddelanden (inaktuellt)|Antal|Totalt|Arkiverade meddelanden i Event Hub i ett namn område (inaktuellt)|Inga dimensioner|
|EHAMBS|Arkiv meddelande genom strömning (inaktuellt)|Skickade|Totalt|Det arkiverade meddelande flödet i Event Hub i ett namn område (inaktuellt)|Inga dimensioner|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/Clusters

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade förfrågningar (för hands version)|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|ServerErrors|Server fel. (Förhandsversion)|Antal|Totalt|Server fel för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|UserErrors|Användar fel. (Förhandsversion)|Antal|Totalt|Användar fel för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|QuotaExceededErrors|Kvoten överskreds. (Förhandsversion)|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|ThrottledRequests|Begränsade begär Anden. (Förhandsversion)|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|IncomingRequests|Inkommande förfrågningar (för hands version)|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|IncomingMessages|Inkommande meddelanden (förhands granskning)|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|OutgoingMessages|Utgående meddelanden (förhands granskning)|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|IncomingBytes|Inkommande byte. (Förhandsversion)|Skickade|Totalt|Inkommande byte för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|OutgoingBytes|Utgående byte. (Förhandsversion)|Skickade|Totalt|Utgående byte för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|ActiveConnections|ActiveConnections (för hands version)|Antal|Medel|Totalt antal aktiva anslutningar för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|ConnectionsOpened|Öppna anslutningar. (Förhandsversion)|Antal|Medel|Anslutningar som har öppnats för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|ConnectionsClosed|Stängda anslutningar. (Förhandsversion)|Antal|Medel|Stängda anslutningar för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|CaptureBacklog|Samla in efter släpning. (Förhandsversion)|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|CapturedMessages|Fångade meddelanden. (Förhandsversion)|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|CapturedBytes|Hämtade byte. (Förhandsversion)|Skickade|Totalt|Hämtade byte för Microsoft. EventHub. (Förhandsversion)|Inga dimensioner|
|Processor|PROCESSOR (för hands version)|Procent|Maximal|CPU-användning för Event Hub-klustret i procent|Roll|
|AvailableMemory|Tillgängligt minne (förhands granskning)|Antal|Maximal|Tillgängligt minne för Event Hub-klustret i byte|Roll|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kluster

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategoriserade Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden per kategori (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Antal aktiva arbetare|Antal|Maximal|Antal aktiva arbetare|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerat mått värde|Antal|Medel|Det värde som beräknas genom autoskalning vid körning|MetricTriggerSource|
|MetricThreshold|Mätnings tröskel|Antal|Medel|Det konfigurerade tröskelvärdet för autoskalning vid autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Observerad kapacitet|Antal|Medel|Kapaciteten som rapporteras till autoskalning när den kördes.|Inga dimensioner|
|ScaleActionsInitiated|Initierade skalnings åtgärder|Antal|Totalt|Riktningen för skalnings åtgärden.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

(Offentlig för hands version)

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Tillgänglighet|Procent|Medel|Procent slutförda tillgänglighets test har slutförts|availabilityResult/namn, availabilityResult/plats|
|availabilityResults/antal|Tillgänglighetstester|Antal|Antal|Antal tillgänglighets test|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|availabilityResults/varaktighet|Tillgänglighets testets varaktighet|Millisekunder|Medel|Tillgänglighets testets varaktighet|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|browserTimings/networkDuration|Nätverks anslutnings tid för sid inläsning|Millisekunder|Medel|Tid mellan användar förfrågan och nätverks anslutning. Inkluderar DNS-sökning och transport anslutning.|Inga dimensioner|
|browserTimings/processingDuration|Klient bearbetnings tid|Millisekunder|Medel|Tiden mellan att ta emot sista byten i ett dokument tills DOM har lästs in. Asynkrona begär Anden kan fortfarande bearbetas.|Inga dimensioner|
|browserTimings/receiveDuration|Tar emot svars tid|Millisekunder|Medel|Tiden mellan den första och sista byten, eller till från koppling.|Inga dimensioner|
|browserTimings/sendDuration|Tid för att skicka begäran|Millisekunder|Medel|Tiden mellan nätverks anslutning och mottagande av den första byten.|Inga dimensioner|
|browserTimings/totalDuration|Sid inläsnings tid för webbläsare|Millisekunder|Medel|Tid från användar förfrågan tills DOM, formatmallar, skript och bilder har lästs in.|Inga dimensioner|
|beroenden/antal|Beroende anrop|Antal|Antal|Antal anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/varaktighet|Beroende varaktighet|Millisekunder|Medel|Varaktigheten för anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/misslyckades|Beroende anrops problem|Antal|Antal|Antal misslyckade beroende anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|pageViews/antal|Sid visningar|Antal|Antal|Antal sid visningar.|åtgärd/syntetisk|
|pageViews/varaktighet|Inläsnings tid för sid visning|Millisekunder|Medel|Inläsnings tid för sid visning|åtgärd/syntetisk|
|performanceCounters/requestExecutionTime|Körnings tid för HTTP-begäran|Millisekunder|Medel|Körnings tid för den senaste begäran.|Cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-begäranden i program kön|Antal|Medel|Längden på program begär ande kön.|Cloud/roleInstance|
|performanceCounters/requestsPerSecond|Hastighet för HTTP-begäran|CountPerSecond|Medel|Takten för alla förfrågningar till programmet per sekund från ASP.NET.|Cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Undantags frekvens|CountPerSecond|Medel|Antal hanterade och ohanterade undantag som rapporter ATS till Windows, inklusive .NET-undantag och ohanterade undantag som konverterats till .NET-undantag.|Cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process-IO-hastighet|BytesPerSecond|Medel|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|Cloud/roleInstance|
|performanceCounters/processCpuPercentage|Processor-CPU|Procent|Medel|Procent andelen av förfluten tid som alla process trådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger enbart prestanda för W3wp-processen.|Cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor tid|Procent|Medel|Den procent andel av tiden som processorn ägnat åt icke-inaktiva trådar.|Cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Tillgängligt minne|Skickade|Medel|Fysiskt minne som är omedelbart tillgängligt för allokering till en process eller för system användning.|Cloud/roleInstance|
|performanceCounters/processPrivateBytes|Privata byte för process|Skickade|Medel|Minne som tilldelats exklusivt för de övervakade program processerna.|Cloud/roleInstance|
|begär Anden/varaktighet|Server svars tid|Millisekunder|Medel|Tiden mellan att ta emot en HTTP-förfrågan och avsluta sändningen av svaret.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/antal|Server begär Anden|Antal|Antal|Antal slutförda HTTP-förfrågningar.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/misslyckade|Misslyckade förfrågningar|Antal|Antal|Antal HTTP-begäranden som marker ATS som misslyckade. I de flesta fall är dessa förfrågningar med svars kod > = 400 och inte lika med 401.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Cloud/roleName|
|begär Anden/pris|Server begär ande frekvens|CountPerSecond|Medel|Antal server begär Anden per sekund|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|undantag/antal|Undantag|Antal|Antal|Sammanlagt antal undantag som inte har fångats.|Cloud/roleName, Cloud/roleInstance, Client/Type|
|undantag/webbläsare|Webbläsarundantag|Antal|Antal|Antal ej fångade undantag som har utlösts i webbläsaren.|Inga dimensioner|
|undantag/Server|Server undantag|Antal|Antal|Antal ej fångade undantag som har utlösts i serverprogrammet.|Cloud/roleName, Cloud/roleInstance|
|spårning/antal|Anden|Antal|Antal|Spårnings dokument antal|Trace/severityLevel, operation/syntetisk, Cloud/roleName, Cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Totalt antal tjänst-API-träffar|Antal|Antal|Antal totala service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Övergripande service API-latens|Millisekunder|Medel|Övergripande svars tid för service API-begäranden|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Totalt antal service API-resultat|Antal|Antal|Antal totala service API-resultat|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CacheUtilization|Användning av cache|Procent|Medel|Användnings nivå i kluster omfånget|Inget|
|QueryDuration|Frågans varaktighet|Millisekunder|Medel|Frågornas varaktighet i sekunder|Status för fråga|
|IngestionUtilization|Förbruknings användning|Procent|Medel|Förhållandet mellan använda inmatnings platser i klustret|Inget|
|KeepAlive|Behåll Alive|Antal|Medel|Sanity check anger att klustret svarar på frågor|Inget|
|IngestionVolumeInMB|Inmatnings volym (i MB)|Antal|Totalt|Total mängd inmatade data till klustret (i MB)|Databas|
|IngestionLatencyInSeconds|Inmatnings svars tid (i sekunder)|Sekunder|Medel|Hämtnings tiden från källan (t. ex. meddelandet är i EventHub) till klustret på några sekunder|Inget|
|EventProcessedForEventHubs|Bearbetade händelser (för Event Hubs)|Antal|Totalt|Antal händelser som bearbetas av klustret vid inmatning från Händelsehubben|Inget|
|IngestionResult|Inmatnings resultat|Antal|Antal|Antal inmatnings åtgärder|Status|
|Processor|Processor|Procent|Medel|PROCESSOR användnings nivå|Inget|
| ContinuousExportNumOfRecordsExported | Antal poster som exporter ATS vid löpande export | Antal | Totalt | Antal poster som har exporter ATS för varje lagrings artefakt som skrivits under export åtgärden  | Inget |
| ExportUtilization | Exportera användning | Procent | Maximal | Exportera användning | Inget |
| ContinuousExportPendingCount | Antal väntande pågående export | Antal | Maximal | Antal väntande kontinuerliga export jobb som är klara för körning | Inget |
| ContinuousExportMaxLatenessMinutes | Högsta antal förseningar i minuter för kontinuerlig export | Antal | Maximal | Max tid i minuter för all kontinuerlig export som väntar och är redo för körning | Inget |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft. LocationBasedServices/konton

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Antal|Antal|Antal API-anrop|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inga dimensioner|
|RunsCompleted|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsSucceeded|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsFailed|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inga dimensioner|
|RunsCancelled|Körningarna har avbrutits|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunLatency|Körnings fördröjning|Sekunder|Medel|Svars tiden för slutförda arbets flödes körningar.|Inga dimensioner|
|RunSuccessLatency|Kör svars tid|Sekunder|Medel|Svars tiden för slutfört arbets flöde körs.|Inga dimensioner|
|RunThrottledEvents|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga dimensioner|
|RunFailurePercentage|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga dimensioner|
|ActionsStarted|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inga dimensioner|
|ActionsCompleted|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsSucceeded|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsFailed|Misslyckade åtgärder|Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSkipped|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga dimensioner|
|ActionLatency|Åtgärds svars tid |Sekunder|Medel|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionSuccessLatency|Svars tid för åtgärd |Sekunder|Medel|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionThrottledEvents|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga dimensioner|
|TriggersStarted|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inga dimensioner|
|TriggersCompleted|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersSucceeded|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersFailed|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga dimensioner|
|TriggersSkipped|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inga dimensioner|
|TriggersFired|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerLatency|Utlös fördröjning |Sekunder|Medel|Latens för slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggerFireLatency|Utlös brand fördröjning |Sekunder|Medel|Fördröjning för utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerSuccessLatency|Utlös svars tid för lyckade |Sekunder|Medel|Svars tid för lyckade arbets flödes utlösare.|Inga dimensioner|
|TriggerThrottledEvents|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga dimensioner|
|BillableActionExecutions|Fakturerbara åtgärds körningar|Antal|Totalt|Antal körningar av arbets flödes åtgärder som faktureras.|Inga dimensioner|
|BillableTriggerExecutions|Fakturerbara Utlösar-körningar|Antal|Totalt|Antal körningar av arbets flödes utlösare som faktureras.|Inga dimensioner|
|TotalBillableExecutions|Totalt antal fakturerbara körningar|Antal|Totalt|Antal arbets flödes körningar som faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Fakturerings användning för intern åtgärds körningar|Antal|Totalt|Antal körningar av interna åtgärder som debiteras.|Inga dimensioner|
|BillingUsageStandardConnector|Fakturerings användning för standard kopplings körningar|Antal|Totalt|Antal standard kopplings körningar som faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Fakturerings användning för lagrings förbruknings körningar|Antal|Totalt|Antal lagrings förbruknings körningar som faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Fakturerings användning för intern åtgärds körningar|Antal|Totalt|Antal körningar av interna åtgärder som debiteras.|Inga dimensioner|
|BillingUsageStandardConnector|Fakturerings användning för standard kopplings körningar|Antal|Totalt|Antal standard kopplings körningar som faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Fakturerings användning för lagrings förbruknings körningar|Antal|Totalt|Antal lagrings förbruknings körningar som faktureras.|Inga dimensioner|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inga dimensioner|
|RunsCompleted|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsSucceeded|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsFailed|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inga dimensioner|
|RunsCancelled|Körningarna har avbrutits|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunLatency|Körnings fördröjning|Sekunder|Medel|Svars tiden för slutförda arbets flödes körningar.|Inga dimensioner|
|RunSuccessLatency|Kör svars tid|Sekunder|Medel|Svars tiden för slutfört arbets flöde körs.|Inga dimensioner|
|RunThrottledEvents|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga dimensioner|
|RunStartThrottledEvents|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inga dimensioner|
|RunFailurePercentage|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga dimensioner|
|ActionsStarted|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inga dimensioner|
|ActionsCompleted|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsSucceeded|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsFailed|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSkipped|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga dimensioner|
|ActionLatency|Åtgärds svars tid |Sekunder|Medel|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionSuccessLatency|Svars tid för åtgärd |Sekunder|Medel|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionThrottledEvents|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga dimensioner|
|TriggersStarted|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inga dimensioner|
|TriggersCompleted|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersSucceeded|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersFailed|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga dimensioner|
|TriggersSkipped|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inga dimensioner|
|TriggersFired|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerLatency|Utlös fördröjning |Sekunder|Medel|Latens för slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggerFireLatency|Utlös brand fördröjning |Sekunder|Medel|Fördröjning för utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerSuccessLatency|Utlös svars tid för lyckade |Sekunder|Medel|Svars tid för lyckade arbets flödes utlösare.|Inga dimensioner|
|TriggerThrottledEvents|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Användning av arbets flödes processor för Integration Service Environment|Procent|Medel|Användning av arbets flödes processor för integrerings tjänst miljö.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Minnes användning för arbets flöde för Integration Service Environment|Procent|Medel|Arbets flödets minnes användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Anslutnings processor användning för Integration Service Environment|Procent|Medel|Anslutnings processor användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Anslutnings minnes användning för Integration Service Environment|Procent|Medel|Anslutnings minnes användning för integrerings tjänst miljön.|Inga dimensioner|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Slutförda körningar|Slutförda körningar|Antal|Totalt|Antal körningar som har slutförts för den här arbets ytan|Scenario|
|Startade körningar|Startade körningar|Antal|Totalt|Antal körningar som har startats för den här arbets ytan|Scenario|
|Misslyckade körningar|Misslyckade körningar|Antal|Totalt|Antalet körningar som misslyckades för den här arbets ytan|Scenario|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konton

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Antal|Antal|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Tillgänglighet för API: erna|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageOtherLatency|Genomsnittlig annan svars tid|MS/op|Medel|Genomsnittlig annan svars tid (som inte är läsning eller skrivning) i millisekunder per åtgärd|Inga dimensioner|
|AverageReadLatency|Genomsnittlig Läs fördröjning|MS/op|Medel|Genomsnittlig Läs fördröjning i millisekunder per åtgärd|Inga dimensioner|
|AverageTotalLatency|Genomsnittlig total svars tid|MS/op|Medel|Genomsnittlig total svars tid i millisekunder per åtgärd|Inga dimensioner|
|AverageWriteLatency|Genomsnittlig Skriv fördröjning|MS/op|Medel|Genomsnittlig Skriv fördröjning i millisekunder per åtgärd|Inga dimensioner|
|FilesystemOtherOps|Fil Systems annat OPS|hantering|Medel|Antal andra åtgärder för fil systemet (som inte läses eller skrivas)|Inga dimensioner|
|FilesystemReadOps|Read-Ops för fil system|hantering|Medel|Antal Läs åtgärder för fil systemet|Inga dimensioner|
|FilesystemTotalOps|Fil system totalt OPS|hantering|Medel|Summan av alla fil Systems åtgärder|Inga dimensioner|
|FilesystemWriteOps|Skriv Ops för fil system|hantering|Medel|Antal Skriv åtgärder i fil systemet|Inga dimensioner|
|IoBytesPerOtherOps|IO-byte per annat OPS|byte/op|Medel|Antal in-/ut-byte per andra åtgärder (som inte kan läsas eller skrivas)|Inga dimensioner|
|IoBytesPerReadOps|IO-byte per Read-OPS|byte/op|Medel|Antal in-/ut-byte per Läs åtgärd|Inga dimensioner|
|IoBytesPerTotalOps|IO-byte per op för alla åtgärder|byte/op|Medel|Summan av alla in-/ut-byte-åtgärder|Inga dimensioner|
|IoBytesPerWriteOps|IO-byte per Skriv åtgärd|byte/op|Medel|Antal in-/ut-byte per Skriv åtgärd|Inga dimensioner|
|OtherIops|Andra IOPS|åtgärder per sekund|Medel|Annan in/ut-åtgärd per sekund|Inga dimensioner|
|OtherThroughput|Annat data flöde|S|Medel|Annat data flöde (som inte läses eller skrivs) i megabyte per sekund|Inga dimensioner|
|ReadIops|Läs IOPS|åtgärder per sekund|Medel|Läs-/ut-åtgärder per sekund|Inga dimensioner|
|ReadThroughput|Läs data flöde|S|Medel|Läs data flöde i megabyte per sekund|Inga dimensioner|
|TotalIops|Totalt antal IOPS|åtgärder per sekund|Medel|Summan av alla in/ut-åtgärder per sekund|Inga dimensioner|
|TotalThroughput|Totalt data flöde|S|Medel|Summan av alla data flöde i megabyte per sekund|Inga dimensioner|
|VolumeAllocatedSize|Allokerad volym storlek|Skickade|Medel|Allokerad storlek på volymen (inte de faktiska använda bytena)|Inga dimensioner|
|VolumeLogicalSize|Logisk volym storlek|Skickade|Medel|Den logiska storleken på volymen (använda byte)|Inga dimensioner|
|VolumeSnapshotSize|Storlek på volym ögonblicks bild|Skickade|Medel|Storlek på alla ögonblicks bilder i volymen|Inga dimensioner|
|WriteIops|Skriv IOPS|åtgärder per sekund|Medel|Skriv in/ut-åtgärder per sekund|Inga dimensioner|
|WriteThroughput|Skriv data flöde|S|Medel|Skriv data flöde i megabyte per sekund|Inga dimensioner|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Allokerad storlek för volym pool|Skickade|Medel|Allokerad storlek för poolen (inte de faktiska använda bytena)|Inga dimensioner|
|VolumePoolAllocatedUsed|Allokerad volym pool används|Skickade|Medel|Allokerad använt storlek på poolen|Inga dimensioner|
|VolumePoolTotalLogicalSize|Total logisk storlek i volymprocent|Skickade|Medel|Summan av den logiska storleken för alla volymer som tillhör poolen|Inga dimensioner|
|VolumePoolTotalSnapshotSize|Total storlek på ögonblicks bild av volym pool|Skickade|Medel|Summan av alla ögonblicks bilder i poolen|Inga dimensioner|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesSentRate|Skickade byte|Antal|Totalt|Antal byte som nätverks gränssnittet har skickats|Inga dimensioner|
|BytesReceivedRate|Mottagna byte|Antal|Totalt|Antal byte som nätverks gränssnittet har tagits emot|Inga dimensioner|
|PacketsSentRate|Skickade paket|Antal|Totalt|Antal paket som nätverks gränssnittet har skickats|Inga dimensioner|
|PacketsReceivedRate|Mottagna paket|Antal|Totalt|Antal paket som nätverks gränssnittet har fått|Inga dimensioner|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|Tillgänglighet för data Sök väg|Antal|Medel|Genomsnittlig tillgänglighet för Load Balancer data Sök väg per tids längd|FrontendIPAddress, FrontendPort|
|DipAvailability|Status för hälso avsökning|Antal|Medel|Genomsnittlig status för Load Balancer hälso avsökning per tids period|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som skickats inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tids perioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allokerade SNAT-portar (förhands granskning)|Antal|Totalt|Totalt antal SNAT-portar som allokerats inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Använda SNAT-portar (förhands granskning)|Antal|Totalt|Totalt antal SNAT-portar som använts inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Fråga volym|Antal|Totalt|Antal frågor som hanteras för en DNS-zon|Inga dimensioner|
|RecordSetCount|Antal post uppsättningar|Antal|Maximal|Antal post uppsättningar i en DNS-zon|Inga dimensioner|
|RecordSetCapacityUtilization|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en DNS-zon|Inga dimensioner|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS för inkommande paket|CountPerSecond|Maximal|DDoS för inkommande paket|Inga dimensioner|
|PacketsDroppedDDoS|Inkommande paket som släppts DDoS|CountPerSecond|Maximal|Inkommande paket som släppts DDoS|Inga dimensioner|
|PacketsForwardedDDoS|Vidarebefordrade inkommande paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande paket DDoS|Inga dimensioner|
|TCPPacketsInDDoS|DDoS inkommande TCP-paket|CountPerSecond|Maximal|DDoS inkommande TCP-paket|Inga dimensioner|
|TCPPacketsDroppedDDoS|Inkommande TCP-paket ignorerade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket ignorerade DDoS|Inga dimensioner|
|TCPPacketsForwardedDDoS|Inkommande TCP-paket, vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket, vidarebefordrade DDoS|Inga dimensioner|
|UDPPacketsInDDoS|DDoS för inkommande UDP-paket|CountPerSecond|Maximal|DDoS för inkommande UDP-paket|Inga dimensioner|
|UDPPacketsDroppedDDoS|Ignorerade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Ignorerade inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsForwardedDDoS|Vidarebefordrade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande UDP-paket DDoS|Inga dimensioner|
|BytesInDDoS|DDoS för inkommande byte|BytesPerSecond|Maximal|DDoS för inkommande byte|Inga dimensioner|
|BytesDroppedDDoS|Ignorerade inkommande byte DDoS|BytesPerSecond|Maximal|Ignorerade inkommande byte DDoS|Inga dimensioner|
|BytesForwardedDDoS|Inkommande byte, vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande byte, vidarebefordrade DDoS|Inga dimensioner|
|TCPBytesInDDoS|DDoS för inkommande TCP-byte|BytesPerSecond|Maximal|DDoS för inkommande TCP-byte|Inga dimensioner|
|TCPBytesDroppedDDoS|Inkommande TCP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte utelämnade DDoS|Inga dimensioner|
|TCPBytesForwardedDDoS|Inkommande TCP byte-vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande TCP byte-vidarebefordrade DDoS|Inga dimensioner|
|UDPBytesInDDoS|DDoS för inkommande UDP-byte|BytesPerSecond|Maximal|DDoS för inkommande UDP-byte|Inga dimensioner|
|UDPBytesDroppedDDoS|Inkommande UDP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte utelämnade DDoS|Inga dimensioner|
|UDPBytesForwardedDDoS|Inkommande UDP byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande UDP byte vidarebefordrade DDoS|Inga dimensioner|
|IfUnderDDoSAttack|Under DDoS-attack eller inte|Antal|Maximal|Under DDoS-attack eller inte|Inga dimensioner|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerUDPPackets|Ingående UDP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Ingående UDP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerSYNPackets|Inkommande SYN paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande SYN paket för att utlösa DDoS-minskning|Inga dimensioner|
|VipAvailability|Tillgänglighet för data Sök väg|Antal|Medel|Genomsnittlig tillgänglighet för IP-adress per tids längd|Port|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som skickats inom tids perioden|Port, riktning|
|PacketCount|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|Port, riktning|
|SynCount|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|Port, riktning|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ApplicationRuleHit|Antal träffar för program regler|Antal|Totalt|Antal gånger som program regler träffades|Status, orsak, protokoll|
|NetworkRuleHit|Antal träffar för nätverks regler|Antal|Totalt|Antal gånger som nätverks regler träffades|Status, orsak, protokoll|
|DataProcessed|Bearbetade data|Skickade|Totalt|Mängd data som passerar brand väggen|Inga dimensioner|
|FirewallHealthState|Hälso tillstånd för brand vägg|Procent|Medel|Anger hälso tillståndet för brand väggen|Status, orsak|
|SNATPortUtilization|SNAT-port användning|Procent|Medel|Procent andelen SNAT-portar som används av brand väggen|Inga dimensioner|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Totalt|Antal byte per sekund som Application Gateway har betjänat|Inga dimensioner|
|UnhealthyHostCount|Antal felaktiga värdar|Antal|Medel|Antal värdar för skadade Server delar|BackendSettingsPool|
|healthyHostCount|Antal felfria värdar|Antal|Medel|Antal felfria Server dels värdar|BackendSettingsPool|
|TotalRequests|Totalt antal förfrågningar|Antal|Totalt|Antal lyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|FailedRequests|Misslyckade begäranden|Antal|Totalt|Antal misslyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|ResponseStatus|Svars status|Antal|Totalt|Http-svarets status returnerades av Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inga dimensioner|
|CapacityUnits|Aktuella kapacitets enheter|Antal|Medel|Förbrukade kapacitets enheter|Inga dimensioner|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageBandwidth|Gatewayens S2S-bandbredd|BytesPerSecond|Medel|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inga dimensioner|
|P2SBandwidth|Gateway P2S-bandbredd|BytesPerSecond|Medel|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Antal P2S-anslutningar|Antal|Maximal|Antal anslutningar punkt-till-plats för en gateway|Protokoll|
|TunnelAverageBandwidth|Tunnel bandbredd|BytesPerSecond|Medel|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Utgående byte i tunnel|Skickade|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Ingångs byte för tunnel|Skickade|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Utgående tunnel paket|Antal|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Inkommande tunnel paket|Antal|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Ignorerade TS-matchnings paket för tunnel|Antal|Totalt|Utgående paket-antal från trafik väljare matchnings fel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel om ingångs paket för TS-matchningsfel|Antal|Totalt|Inkommande paket Drop Count från trafik väljarens matchnings fel i en tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS inkommande Azure per sekund|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS utgående Azure per sekund|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS inkommande Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS utgående Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS inkommande Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS utgående Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Returnerade frågor efter slut punkt|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den aktuella tids ramen|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Slut punkts status efter slut punkt|Antal|Maximal|1 om avsöknings status för en slut punkt är "aktive rad", annars 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Avsökningar misslyckades|Procent|Medel|% av anslutnings övervaknings avsökningarna misslyckades|Inga dimensioner|
|AverageRoundtripMs|Genomsnittlig tid för fördröjning (MS)|Millisekunder|Medel|Genomsnittlig tid för nätverks fördröjning (MS) för anslutnings övervaknings avsökningar som skickas mellan källa och mål|Inga dimensioner|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|requestCount|Antal begäranden|Antal|Totalt|Antalet klient förfrågningar som hanteras av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Begär ande storlek|Skickade|Totalt|Antalet byte som har skickats som begär Anden från klienter till HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Svars storlek|Skickade|Totalt|Antalet byte som skickats som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Antal Server dels begär Anden|Antal|Totalt|Antalet förfrågningar som skickats från HTTP/S-proxyn till Server delar|HttpStatus, HttpStatusGroup, Server del|
|BackendRequestLatency|Svars tid för Server del|Millisekunder|Medel|Tiden som beräknas från när begäran skickades av HTTP/S-proxyn till Server delen tills HTTP/S-proxyn fick den senaste svars byten från Server delen|Backend|
|TotalLatency|Total svars tid|Millisekunder|Medel|Den tid som beräknas från när klientbegäran togs emot av HTTP/S-proxy tills klienten bekräftade den senaste svars byten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Server delens hälso procent|Procent|Medel|Procent andelen lyckade hälso avsökningar från HTTP/S-proxyn till Server delar|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/Namespaces/NotificationHubs

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|registrering. alla|Registrerings åtgärder|Antal|Totalt|Antalet lyckade registrerings åtgärder (skapar uppdateringar av frågor och borttagningar). |Inga dimensioner|
|registrering. skapa|Skapa åtgärder för registrering|Antal|Totalt|Antalet lyckade registreringar som skapas.|Inga dimensioner|
|registrering. Update|Registrera uppdaterings åtgärder|Antal|Totalt|Antalet lyckade registrerings uppdateringar.|Inga dimensioner|
|registrering. Hämta|Läs åtgärder för registrering|Antal|Totalt|Antalet lyckade registrerings frågor.|Inga dimensioner|
|registrering. Delete|Åtgärder för att ta bort registrering|Antal|Totalt|Antalet lyckade registrerings borttagningar.|Inga dimensioner|
|inkommande|Inkommande meddelanden|Antal|Totalt|Antalet lyckade sändnings-API-anrop. |Inga dimensioner|
|inkommande. schemalagd|Schemalagda push-meddelanden har skickats|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|inkommande. schemalagd. Avbryt|Schemalagda push-meddelanden har avbrutits|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|schemalagt. väntar|Väntande schemalagda meddelanden|Antal|Totalt|Väntande schemalagda meddelanden|Inga dimensioner|
|installation. alla|Installations hanterings åtgärder|Antal|Totalt|Installations hanterings åtgärder|Inga dimensioner|
|installation. get|Hämta installations åtgärder|Antal|Totalt|Hämta installations åtgärder|Inga dimensioner|
|installation. upsert|Skapa eller uppdatera installations åtgärder|Antal|Totalt|Skapa eller uppdatera installations åtgärder|Inga dimensioner|
|installation. patch|Installation av korrigerings åtgärder|Antal|Totalt|Installation av korrigerings åtgärder|Inga dimensioner|
|installation. Delete|Ta bort installations åtgärder|Antal|Totalt|Ta bort installations åtgärder|Inga dimensioner|
|utgående. allpns. lyckades|Lyckade aviseringar|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. allpns. invalidpayload|Nytto Last fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS returnerade ett felaktigt nytto Last fel.|Inga dimensioner|
|utgående. allpns. pnserror|Fel i externt meddelande system|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av ett problem med att kommunicera med PNS (exkluderar autentiseringsproblem).|Inga dimensioner|
|utgående. allpns. channelerror|Kanal fel|Antal|Totalt|Antal push-meddelanden som inte har misslyckats på grund av att kanalen var ogiltig och som inte är associerad med rätt app-begränsning eller upphörde att gälla.|Inga dimensioner|
|utgående. allpns. badorexpiredchannel|Dåliga eller utgångna kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom kanal/token/registrationId i registreringen har upphört att gälla eller är ogiltiga.|Inga dimensioner|
|utgående. WNS. lyckades|WNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. WNS. invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras. (Windows Live känner inte igen autentiseringsuppgifterna).|Inga dimensioner|
|utgående. WNS. badchannel|WNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inga dimensioner|
|utgående. WNS. expiredchannel|WNS utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 borta).|Inga dimensioner|
|utgående. WNS. begränsad|WNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom WNS begränsar den här appen (WNS-status: 406 är inte acceptabel).|Inga dimensioner|
|utgående. WNS. tokenproviderunreachable|WNS-auktoriseringsfel (kan inte kontaktas)|Antal|Totalt|Windows Live är inte tillgängligt.|Inga dimensioner|
|utgående. WNS. invalidtoken|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Den token som angetts för WNS är inte giltig (WNS-status: 401 otillåten).|Inga dimensioner|
|utgående. WNS. wrongtoken|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS-status: 403 förbjuden). Detta kan inträffa om ChannelURI i registreringen är kopplad till en annan app. Kontrol lera att klient programmet är associerat med samma app vars autentiseringsuppgifter finns i Notification Hub.|Inga dimensioner|
|utgående. WNS. invalidnotificationformat|WNS ogiltigt meddelande format|Antal|Totalt|Meddelandets format är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nytto laster.|Inga dimensioner|
|utgående. WNS. invalidnotificationsize|WNS ogiltig meddelande storleks fel|Antal|Totalt|Meddelande nytto lasten är för stor (WNS-status: 413).|Inga dimensioner|
|utgående. WNS. channelthrottled|WNS-kanalen är begränsad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-NotificationStatus: channelThrottled).|Inga dimensioner|
|utgående. WNS. channeldisconnected|WNS-kanalen är frånkopplad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|utgående. WNS. släppt|WNS borttagna meddelanden|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: släppt men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|utgående. WNS. pnserror|WNS-fel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med WNS.|Inga dimensioner|
|utgående. WNS. authenticationerror|WNS-autentiseringsfel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med ogiltiga Windows Live-autentiseringsuppgifter eller felaktigt token.|Inga dimensioner|
|utgående. APN. lyckades|APN-lyckade meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. APN. invalidcredentials|APN-auktoriseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. APN. badchannel|APN dåligt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att token är ogiltig (APN binära protokoll status kod: 8. Status kod för APNS HTTP-protokoll: 400 med "BadDeviceToken").|Inga dimensioner|
|utgående. APN. expiredchannel|APN utgånget i kanal fel|Antal|Totalt|Antalet token som ogiltig förklarades av APNS-feedback-kanalen.|Inga dimensioner|
|utgående. APN. invalidnotificationsize|APN ogiltigt meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som inte kunde utföras eftersom nytto lasten var för stor (APN binära protokoll status kod: 7).|Inga dimensioner|
|utgående. APN. pnserror|APN-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med APN.|Inga dimensioner|
|utgående. GCM. lyckades|GCM slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. GCM. invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. GCM. badchannel|GCM felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM-resultat: ogiltig registrering).|Inga dimensioner|
|utgående. GCM. expiredchannel|GCM utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|utgående. GCM. begränsad|GCM-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom GCM begränsade till den här appen (GCM-status kod: 501-599 eller resultat: ej tillgängligt).|Inga dimensioner|
|utgående. GCM. invalidnotificationformat|GCM ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten inte formaterades korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|utgående. GCM. invalidnotificationsize|GCM ogiltig meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (GCM-resultat: MessageTooBig).|Inga dimensioner|
|utgående. GCM. wrongchannel|GCM fel kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte är kopplad till den aktuella appen (GCM result: InvalidPackageName).|Inga dimensioner|
|utgående. GCM. pnserror|GCM-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med GCM.|Inga dimensioner|
|utgående. GCM. authenticationerror|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna som autentiseringsuppgifterna är blockerade eller om SenderId inte har kon figurer ATS korrekt i appen (GCM result: MismatchedSenderId).|Inga dimensioner|
|utgående. MPNS. lyckades|MPNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. MPNS. invalidcredentials|MPNS ogiltiga autentiseringsuppgifter|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. MPNS. badchannel|MPNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inga dimensioner|
|utgående. MPNS. begränsad|MPNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom MPNS begränsar den här appen (WNS MPNS: 406 är inte acceptabel).|Inga dimensioner|
|utgående. MPNS. invalidnotificationformat|MPNS ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten för meddelandet var för stor.|Inga dimensioner|
|utgående. MPNS. channeldisconnected|MPNS-kanalen är frånkopplad|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att ChannelURI i registreringen var frånkopplad (MPNS status: 412 hittades inte).|Inga dimensioner|
|utgående. MPNS. släppt|MPNS borttagna meddelanden|Antal|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svars huvud: X-NotificationStatus: QueueFull eller undertrycks).|Inga dimensioner|
|utgående. MPNS. pnserror|MPNS-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med MPNS.|Inga dimensioner|
|utgående. MPNS. authenticationerror|MPNS-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|notificationhub. push-meddelanden|Alla utgående meddelanden|Antal|Totalt|Alla utgående aviseringar för Notification Hub|Inga dimensioner|
|inkommande. alla. förfrågningar|Alla inkommande begär Anden|Antal|Totalt|Totalt antal inkommande begär Anden för en Notification Hub|Inga dimensioner|
|inkommande. alla. failedrequests|Alla inkommande misslyckade förfrågningar|Antal|Totalt|Totalt antal inkommande misslyckade begär Anden för en Notification Hub|Inga dimensioner|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_ kostnads fri noder i procent|Kostnads fri noder i procent|Antal|Medel|Average_ kostnads fri noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledigt utrymme i procent|Ledigt utrymme i procent|Antal|Medel|Average_ ledigt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Använd noder i procent|% Använda noder i procent|Antal|Medel|Average_% Använd noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt utrymme i procent|Använt utrymme i procent|Antal|Medel|Average_ använt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/s|Disk-lästa byte/s|Antal|Medel|Average_Disk lästa byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Disk läsningar/SEK|Antal|Medel|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Disk överföringar/SEK|Antal|Medel|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/s|Disk-skrivna byte/s|Antal|Medel|Average_Disk skrivna byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Disk skrivningar/SEK|Antal|Medel|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Lediga megabyte|Antal|Medel|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk-byte/s|Logisk disk byte/SEK|Antal|Medel|Average_Logical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt minne|Tillgängligt minne i procent|Antal|Medel|Average_% tillgängligt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt växlings utrymme|Tillgängligt växlings utrymme i procent|Antal|Medel|Average_% tillgängligt växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt minne|Använt minne i procent|Antal|Medel|Average_% använt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt växlings utrymme i procent|Använt växlings utrymme i procent|Antal|Medel|Average_ använt växlings utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB minne|Tillgängligt minne i megabyte|Antal|Medel|Average_Available MB minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Växling vid Average_Available megabyte|Tillgängliga megabyte växlings utrymme|Antal|Medel|Växling vid Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page läsningar/s|Sid läsningar/s|Antal|Medel|Average_Page läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page skrivningar/SEK|Sid skrivningar/SEK|Antal|Medel|Average_Page skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per sekund|Sidor/s|Antal|Medel|Average_Pages per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlings utrymme|Använt megabyte växlings utrymme|Antal|Medel|Average_Used megabyte växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i MB|Använt minne i MB|Antal|Medel|Average_Used minne i MB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total byte|Totalt antal överförda byte|Antal|Medel|Skickade Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna byte|Totalt antal mottagna byte|Antal|Medel|Average_Total mottagna byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Totalt antal byte|Antal|Medel|Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total paket|Totalt antal överförda paket|Antal|Medel|Skickade Average_Total paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna paket|Totalt antal mottagna paket|Antal|Medel|Average_Total mottagna paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total RX-fel|Totalt antal mottagna mottagnings fel|Antal|Medel|Average_Total RX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-fel|Totalt antal TX-fel|Antal|Medel|Average_Total TX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Totalt antal kollisioner|Antal|Medel|Average_Total kollisioner|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/läsning|Medel s/disk läsning|Antal|Medel|Average_Avg. Disk s/läsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/överföring|Medel s/disk överföring|Antal|Medel|Average_Avg. Disk s/överföring|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/skrivning|Medel s/disk skrivning|Antal|Medel|Average_Avg. Disk s/skrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk-byte/s|Fysisk disk-byte/SEK|Antal|Medel|Average_Physical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegie rad tid|PCT privilegie rad tid|Antal|Medel|Average_Pct privilegie rad tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användar tid|PCT-användar tid|Antal|Medel|Average_Pct användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i KB|Använt minne i KB|Antal|Medel|Average_Used minne i KB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Virtuellt delat minne|Antal|Medel|Average_Virtual delat minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tid|DPC-tid i procent|Antal|Medel|Average_% DPC-tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledig tid i procent|Ledig tid i procent|Antal|Medel|Average_ ledig tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Avbrotts tid i procent för Average_%|% Avbrotts tid|Antal|Medel|Avbrotts tid i procent för Average_%|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% i/o-vänte tid|% I/o-vänte tid|Antal|Medel|Average_% i/o-vänte tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% bra tid|% Trevligt tid|Antal|Medel|Average_% bra tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ privilegie rad tid i procent|Privilegie rad tid i procent|Antal|Medel|Average_ privilegie rad tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|% Processor tid|Antal|Medel|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% användar tid|Användar tid i procent|Antal|Medel|Average_% användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Ledigt fysiskt minne|Antal|Medel|Average_Free fysiskt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Ledigt utrymme i växlingsfiler|Antal|Medel|Average_Free utrymme i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Ledigt virtuellt minne|Antal|Medel|Average_Free virtuellt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processer|Antal|Medel|Average_Processes|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagras i växlingsfiler|Storlek lagrad i växlingsfiler|Antal|Medel|Average_Size lagras i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Drift tid|Antal|Medel|Average_Uptime|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Användare|Antal|Medel|Average_Users|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/läsning|Medel s/disk läsning|Antal|Medel|Average_Avg. Disk s/läsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/skrivning|Medel s/disk skrivning|Antal|Medel|Average_Avg. Disk s/skrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Aktuell diskkölängd|Antal|Medel|Average_Current diskkölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Disk läsningar/SEK|Antal|Medel|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Disk överföringar/SEK|Antal|Medel|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Disk skrivningar/SEK|Antal|Medel|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Lediga megabyte|Antal|Medel|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledigt utrymme i procent|Ledigt utrymme i procent|Antal|Medel|Average_ ledigt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte|Tillgängliga megabyte|Antal|Medel|Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% allokerade byte som används|% Allokerade byte som används|Antal|Medel|Average_% allokerade byte som används|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes mottagna/SEK|Mottagna byte/SEK|Antal|Medel|Average_Bytes mottagna/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Bytes per sekund|Skickade byte/SEK|Antal|Medel|Skickade Average_Bytes per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/SEK|Totalt antal byte/s|Antal|Medel|Average_Bytes totalt/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|% Processor tid|Antal|Medel|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Kölängd|Kölängd för processor|Antal|Medel|Average_Processor Kölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Tveka|Tveka|Antal|Totalt|Tveka|Dator, OSType, version, SourceComputerId|
|Uppdatera|Uppdatera|Antal|Medel|Uppdatera|Dator, produkt, klassificering, UpdateState, valfri, godkänd|
|Händelse|Händelse|Antal|Medel|Händelse|Källa, EventLog, dator, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryDuration|Frågans varaktighet|Millisekunder|Medel|DAX-frågans varaktighet i det sista intervallet|Inga dimensioner|
|QueryPoolJobQueueLength|Trådar: Kölängd för jobbkö|Antal|Medel|Antal jobb i kön för trådpoolen för Query.|Inga dimensioner|
|qpu_high_utilization_metric|QPU hög användning|Antal|Totalt|QPU hög användning under den senaste minuten, 1 för hög QPU användning, annars 0|Inga dimensioner|
|memory_metric|Minne|Skickade|Medel|Minnesoptimerade. Intervallet 0-3 GB för a1, 0-5 GB för a2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Nedskräpning för minne|Procent|Medel|Genomsnittligt minne nedskräpning.|Inga dimensioner|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ListenerConnections – lyckad|ListenerConnections – lyckad|Antal|Totalt|Lyckade ListenerConnections för Microsoft. Relay.|Entitetsnamnet|
|ListenerConnections – ClientError|ListenerConnections – ClientError|Antal|Totalt|ClientError på ListenerConnections för Microsoft. Relay.|Entitetsnamnet|
|ListenerConnections – ServerError|ListenerConnections – ServerError|Antal|Totalt|ServerError på ListenerConnections för Microsoft. Relay.|Entitetsnamnet|
|SenderConnections – lyckad|SenderConnections – lyckad|Antal|Totalt|Lyckade SenderConnections för Microsoft. Relay.|Entitetsnamnet|
|SenderConnections – ClientError|SenderConnections – ClientError|Antal|Totalt|ClientError på SenderConnections för Microsoft. Relay.|Entitetsnamnet|
|SenderConnections – ServerError|SenderConnections – ServerError|Antal|Totalt|ServerError på SenderConnections för Microsoft. Relay.|Entitetsnamnet|
|ListenerConnections – TotalRequests|ListenerConnections – TotalRequests|Antal|Totalt|Totalt ListenerConnections för Microsoft. Relay.|Entitetsnamnet|
|SenderConnections – TotalRequests|SenderConnections – TotalRequests|Antal|Totalt|Totalt antal SenderConnections-begäranden för Microsoft. Relay.|Entitetsnamnet|
|ActiveConnections|ActiveConnections|Antal|Totalt|Totalt ActiveConnections för Microsoft. Relay.|Entitetsnamnet|
|ActiveListeners|ActiveListeners|Antal|Totalt|Totalt ActiveListeners för Microsoft. Relay.|Entitetsnamnet|
|BytesTransferred|BytesTransferred|Antal|Totalt|Totalt BytesTransferred för Microsoft. Relay.|Entitetsnamnet|
|ListenerDisconnects|ListenerDisconnects|Antal|Totalt|Totalt ListenerDisconnects för Microsoft. Relay.|Entitetsnamnet|
|SenderDisconnects|SenderDisconnects|Antal|Totalt|Totalt SenderDisconnects för Microsoft. Relay.|Entitetsnamnet|

## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SearchLatency|Sök svars tid|Sekunder|Medel|Genomsnittlig Sök fördröjning för Sök tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Sök frågor per sekund|CountPerSecond|Medel|Sök efter frågor per sekund för Sök tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Begränsade Sök frågor i procent|Procent|Medel|Procent andel Sök frågor som har begränsats för Sök tjänsten|Inga dimensioner|

## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade förfrågningar (för hands version)|Antal|Totalt|Totalt antal slutförda begär Anden för en namnrymd (förhands granskning)|Entitetsnamnet|
|ServerErrors|Server fel. (Förhandsversion)|Antal|Totalt|Server fel för Microsoft. Service Bus. (Förhandsversion)|Entitetsnamnet|
|UserErrors|Användar fel. (Förhandsversion)|Antal|Totalt|Användar fel för Microsoft. Service Bus. (Förhandsversion)|Entitetsnamnet|
|ThrottledRequests|Begränsade begär Anden. (Förhandsversion)|Antal|Totalt|Begränsade begär Anden för Microsoft. Service Bus. (Förhandsversion)|Entitetsnamnet|
|IncomingRequests|Inkommande förfrågningar (för hands version)|Antal|Totalt|Inkommande begär Anden för Microsoft. Service Bus. (Förhandsversion)|Entitetsnamnet|
|IncomingMessages|Inkommande meddelanden (förhands granskning)|Antal|Totalt|Inkommande meddelanden för Microsoft. Service Bus. (Förhandsversion)|Entitetsnamnet|
|OutgoingMessages|Utgående meddelanden (förhands granskning)|Antal|Totalt|Utgående meddelanden för Microsoft. Service Bus. (Förhandsversion)|Entitetsnamnet|
|ActiveConnections|ActiveConnections (för hands version)|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft. Service Bus. (Förhandsversion)|Inga dimensioner|
|Storlek|Storlek (förhands granskning)|Skickade|Medel|Storlek på en kö/ett ämne i byte. (Förhandsversion)|Entitetsnamnet|
|Meddelanden|Antal meddelanden i en kö/ett ämne. (Förhandsversion)|Antal|Medel|Antal meddelanden i en kö/ett ämne. (Förhandsversion)|Entitetsnamnet|
|ActiveMessages|Antal aktiva meddelanden i en kö/ett ämne. (Förhandsversion)|Antal|Medel|Antal aktiva meddelanden i en kö/ett ämne. (Förhandsversion)|Entitetsnamnet|
|DeadletteredMessages|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne. (Förhandsversion)|Antal|Medel|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne. (Förhandsversion)|Entitetsnamnet|
|ScheduledMessages|Antal schemalagda meddelanden i en kö/ett ämne. (Förhandsversion)|Antal|Medel|Antal schemalagda meddelanden i en kö/ett ämne. (Förhandsversion)|Entitetsnamnet|
|CPUXNS|CPU-användning per namnrymd|Procent|Maximal|PROCESSOR användnings mått för Service Bus Premium-namnrymd|Inga dimensioner|
|WSXNS|Minnes storleks användning per namnrymd|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd|Inga dimensioner|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/program

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Antal|Medel|CPU allokerad till den här behållaren i millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Skickade|Medel|Minne som allokerats till den här behållaren i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Antal|Medel|Faktisk processor användning i millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Skickade|Medel|Faktisk minnes användning i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Medel|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Medel|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Antal|Medel|Status för Service Fabric nätprogram|ApplicationName, status|
|ServiceStatus|ServiceStatus|Antal|Medel|Hälso status för en tjänst i Service Fabric nätprogram|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Antal|Medel|Hälso status för en tjänst replik i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName|
|Container status|Container status|Antal|Medel|Status för behållaren i Service Fabric nätprogram|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Antal|Medel|Antal omstarter för en behållare i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ConnectionCount|Antal anslutningar|Antal|Maximal|Mängden användar anslutning.|Slutpunkt|
|MessageCount|Antal meddelanden|Antal|Totalt|Den totala mängden meddelanden.|Inga dimensioner|
|InboundTraffic|Inkommande trafik|Skickade|Totalt|Inkommande trafik trafik för tjänsten|Inga dimensioner|
|OutboundTraffic|Utgående trafik|Skickade|Totalt|Utgående trafik för tjänsten|Inga dimensioner|
|UserErrors|Användar fel|Procent|Maximal|Procent andel användar fel|Inga dimensioner|
|SystemErrors|Systemfel|Procent|Maximal|Procent andelen system fel|Inga dimensioner|

## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|allocated_data_storage|Allokerat data utrymme|Skickade|Medel|Allokerat data utrymme. Ej tillämpligt för data lager.|Inga dimensioner|
|app_cpu_billed|App-CPU fakturerad|Antal|Totalt|App-CPU fakturerad. Gäller databaser utan server.|Inga dimensioner|
|app_cpu_percent|CPU-procent för app|Procent|Medel|CPU-procent för app. Gäller databaser utan server.|Inga dimensioner|
|app_memory_percent|Procent andel använt program minne|Procent|Medel|Procent andel använt program minne. Gäller databaser utan server.|Inga dimensioner|
|blocked_by_firewall|Blockerad av brand väggen|Antal|Totalt|Blockerad av brand väggen|Inga dimensioner|
|cache_hit_percent|Procent andel cacheträffar|Procent|Maximal|Procent av cacheträffar. Gäller endast för data lager.|Inga dimensioner|
|cache_used_percent|Procent andel som används|Procent|Maximal|Procent andel som används. Gäller endast för data lager.|Inga dimensioner|
|connection_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|connection_successful|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|cpu_limit|PROCESSOR gräns|Antal|Medel|PROCESSOR gräns. Gäller för vCore-baserade databaser.|Inga dimensioner|
|cpu_used|Använd CPU|Antal|Medel|Använd CPU. Gäller för vCore-baserade databaser.|Inga dimensioner|
|hamn|Låsningar|Antal|Totalt|Låsningar. Ej tillämpligt för data lager.|Inga dimensioner|
|diff_backup_size_bytes|Lagrings storlek för differentiell säkerhets kopia|Skickade|Maximal|Kumulativ lagrings storlek för differentiell säkerhets kopia. Gäller för Generell användning-och Affärskritisk-databaser. Gäller för närvarande inte för hantera instans databaser.|Inga dimensioner|
|dtu_limit|DTU-gräns|Antal|Medel|DTU-gräns. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_used|Använt DTU|Antal|Medel|DTU används. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dw_cpu_percent|DW CPU-procent för nodenivå|Procent|Medel|DW CPU-procent för nodenivå|DwLogicalNodeId|
|dw_physical_data_read_percent|Data IO-procent för DW Node Level|Procent|Medel|Data IO-procent för DW Node Level|DwLogicalNodeId|
|dwu_consumption_percent|DWU procent|Procent|Maximal|DWU procent. Gäller endast för data lager.|Inga dimensioner|
|dwu_limit|DWU-gräns|Antal|Maximal|DWU-gräns. Gäller endast för data lager.|Inga dimensioner|
|dwu_used|DWU som används|Antal|Maximal|DWU som används. Gäller endast för data lager.|Inga dimensioner|
|full_backup_size_bytes|Lagrings storlek för fullständig säkerhets kopia|Skickade|Maximal|Ackumulerad lagrings storlek för fullständig säkerhets kopia. Gäller för Generell användning-och Affärskritisk-databaser. Gäller för närvarande inte för hantera instans databaser.|Inga dimensioner|
|local_tempdb_usage_percent|Lokal tempdb-procent|Procent|Medel|Lokal tempdb-procent. Gäller endast för data lager.|Inga dimensioner|
|log_backup_size_bytes|Logg lagrings storlek för logg säkerhets kopia|Skickade|Maximal|Sammanlagd lagrings storlek för logg säkerhets kopia. Gäller för Generell användning-och Affärskritisk-databaser. Gäller för närvarande inte för hantera instans databaser.|Inga dimensioner|
|log_write_percent|Logg IO-procent|Procent|Medel|Logg IO-procent. Ej tillämpligt för data lager.|Inga dimensioner|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inga dimensioner|
|sessions_percent|Sessioner i procent|Procent|Medel|Sessioner i procent. Ej tillämpligt för data lager.|Inga dimensioner|
|sqlserver_process_core_percent|SQL Server process kärn procent|Procent|Maximal|Det här måttet är en plats hållare som inte har fyllts i för tillfället.|Inga dimensioner|
|sqlserver_process_memory_percent|SQL Server process minne i procent|Procent|Maximal|Det här måttet är en plats hållare som inte har fyllts i för tillfället.|Inga dimensioner|
|lagring|Använt data utrymme|Skickade|Maximal|Total databas storlek. Ej tillämpligt för data lager.|Inga dimensioner|
|storage_percent|Använt data utrymme i procent|Procent|Maximal|Storlek i procent för databasen. Ej tillämpligt för data lager eller storskaliga databaser.|Inga dimensioner|
|tempdb_data_size|Data fil storlek i tempdb i KB|Antal|Maximal|Data fil storlek för tempdb i KB. Ej tillämpligt för data lager. Det här måttet är tillgängligt för databaser som använder vCore inköps modell eller 100 DTU och högre för DTU-baserade inköps modeller.|Inga dimensioner|
|tempdb_log_size|TempDB-logg fils storlek kilobyte|Antal|Maximal|TempDB-logg fils storlek kilobyte. Ej tillämpligt för data lager. Det här måttet är tillgängligt för databaser som använder vCore inköps modell eller 100 DTU och högre för DTU-baserade inköps modeller.|Inga dimensioner|
|tempdb_log_used_percent|Procent använt tempdb-logg|Procent|Maximal|TempDB procent logg används. Ej tillämpligt för data lager. Det här måttet är tillgängligt för databaser som använder vCore inköps modell eller 100 DTU och högre för DTU-baserade inköps modeller.|Inga dimensioner|
|workers_percent|Arbetare i procent|Procent|Medel|Arbetare i procent. Ej tillämpligt för data lager.|Inga dimensioner|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Procent|Medel|Minnes intern OLTP-lagring i procent. Ej tillämpligt för data lager.|Inga dimensioner|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/Servers/elasticPools

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|allocated_data_storage|Allokerat data utrymme|Skickade|Medel|Allokerat data utrymme|Inga dimensioner|
|allocated_data_storage_percent|Allokerat data utrymme i procent|Procent|Maximal|Allokerat data utrymme i procent|Inga dimensioner|
|cpu_limit|PROCESSOR gräns|Antal|Medel|PROCESSOR gräns. Gäller vCore-baserade elastiska pooler.|Inga dimensioner|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inga dimensioner|
|cpu_used|Använd CPU|Antal|Medel|Använd CPU. Gäller vCore-baserade elastiska pooler.|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_limit|eDTU-gräns|Antal|Medel|eDTU-gräns. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_used|eDTU använt|Antal|Medel|eDTU används. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|log_write_percent|Logg IO-procent|Procent|Medel|Logg IO-procent|Inga dimensioner|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inga dimensioner|
|sessions_percent|Sessioner i procent|Procent|Medel|Sessioner i procent|Inga dimensioner|
|storage_limit|Max storlek för data|Skickade|Medel|Lagrings gräns|Inga dimensioner|
|storage_percent|Använt data utrymme i procent||Procent|Medel|Lagrings procent|Inga dimensioner|
|storage_used|Använt data utrymme|Skickade|Medel|Använt lagringsutrymme|Inga dimensioner|
|sqlserver_process_core_percent|SQL Server process kärn procent|Procent|Maximal|Det här måttet är en plats hållare som inte har fyllts i för tillfället.|Inga dimensioner|
|sqlserver_process_memory_percent|SQL Server process minne i procent|Procent|Maximal|Det här måttet är en plats hållare som inte har fyllts i för tillfället.|Inga dimensioner|
|tempdb_data_size|Data fil storlek i tempdb i KB|Antal|Maximal|Data fil storlek för tempdb i KB. Ej tillämpligt för data lager. Det här måttet är tillgängligt för databaser som använder vCore inköps modell eller 100 DTU och högre för DTU-baserade inköps modeller.|Inga dimensioner|
|tempdb_log_size|TempDB-logg fils storlek kilobyte|Antal|Maximal|TempDB-logg fils storlek kilobyte. Ej tillämpligt för data lager. Det här måttet är tillgängligt för databaser som använder vCore inköps modell eller 100 DTU och högre för DTU-baserade inköps modeller.|Inga dimensioner|
|tempdb_log_used_percent|Procent använt tempdb-logg|Procent|Maximal|TempDB procent logg används. Ej tillämpligt för data lager. Det här måttet är tillgängligt för databaser som använder vCore inköps modell eller 100 DTU och högre för DTU-baserade inköps modeller.|Inga dimensioner|
|workers_percent|Arbetare i procent|Procent|Medel|Arbetare i procent|Inga dimensioner|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Procent|Medel|Minnes intern OLTP-lagring i procent|Inga dimensioner|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|avg_cpu_percent|Genomsnittlig CPU-procent|Procent|Medel|Genomsnittlig CPU-procent|Inga dimensioner|
|io_bytes_read|Lästa IO-byte|Skickade|Medel|Lästa IO-byte|Inga dimensioner|
|io_requests|Antal IO-begäranden|Antal|Medel|Antal IO-begäranden|Inga dimensioner|
|io_bytes_written|Skrivna IO-byte|Skickade|Medel|Skrivna IO-byte|Inga dimensioner|
|reserved_storage_mb|Reserverat lagrings utrymme|Antal|Medel|Reserverat lagrings utrymme|Inga dimensioner|
|storage_space_used_mb|Använt lagrings utrymme|Antal|Medel|Använt lagrings utrymme|Inga dimensioner|
|virtual_core_count|Antal virtuella kärnor|Antal|Medel|Antal virtuella kärnor|Inga dimensioner|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Skickade|Medel|Kapacitet för använt konto|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Skickade|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Skickade|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medel|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-latens|Millisekunder|Medel|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-kapacitet|Skickade|Medel|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|BLOB-antal|Antal|Totalt|Antalet BLOB i lagrings kontots Blob Service.|BlobType|       |BlobCount|BLOB-antal|Antal|Medel|Antalet BLOB i lagrings kontots Blob Service.|BlobType,-nivå|
|ContainerCount|Antal BLOB-behållare|Antal|Medel|Antalet behållare i lagrings kontots Blob Service.|Inga dimensioner|
|IndexCapacity|Index kapacitet|Skickade|Medel|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Skickade|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Skickade|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medel|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-latens|Millisekunder|Medel|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Fil kapacitet|Skickade|Medel|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|Inga dimensioner|
|FileCount|Antal filer|Antal|Medel|Antalet filer i lagrings kontots fil tjänst.|Inga dimensioner|
|FileShareCount|Antal fil resurser|Antal|Medel|Antalet fil resurser i lagrings kontots fil tjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Skickade|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Skickade|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medel|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-latens|Millisekunder|Medel|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kös Kap kap.|Skickade|Medel|Mängden lagrings utrymme som används av lagrings kontots Kötjänst i byte.|Inga dimensioner|
|QueueCount|Antal köer|Antal|Medel|Antalet köer i lagrings kontots Kötjänst.|Inga dimensioner|
|QueueMessageCount|Antal meddelanden i kö|Antal|Medel|Ungefärligt antal köa meddelanden i lagrings kontots Kötjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Skickade|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Skickade|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medel|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-latens|Millisekunder|Medel|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabell kapacitet|Skickade|Medel|Mängden lagrings utrymme som används av lagrings kontots Table service i byte.|Inga dimensioner|
|TableCount|Antal tabeller|Antal|Medel|Antalet tabeller i lagrings kontots Table service.|Inga dimensioner|
|TableEntityCount|Antal tabell enheter|Antal|Medel|Antalet tabell enheter i lagrings kontots Table service.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Skickade|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Skickade|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medel|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-latens|Millisekunder|Medel|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultat av Sync-session|Antal|Medel|Mått som loggar värdet 1 varje gång Server slut punkten slutför en Sync-session med moln slut punkten|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte som har synkroniserats|Skickade|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång den registrerade servern registrerar ett pulsslag med moln slut punkten|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Återkalla moln nivå|Skickade|Totalt|Total storlek på data som återkallas av servern|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ResourceUtilization|SU%-användning|Procent|Maximal|SU%-användning|LogicalName, PartitionId|
|InputEvents|Inmatade händelser|Antal|Totalt|Inmatade händelser|LogicalName, PartitionId|
|InputEventBytes|Inkommande händelse-byte|Skickade|Totalt|Inkommande händelse-byte|LogicalName, PartitionId|
|LateInputEvents|Sena ingångs händelser|Antal|Totalt|Sena ingångs händelser|LogicalName, PartitionId|
|OutputEvents|Utgående händelser|Antal|Totalt|Utgående händelser|LogicalName, PartitionId|
|ConversionErrors|Data konverterings fel|Antal|Totalt|Data konverterings fel|LogicalName, PartitionId|
|Fel|Körnings fel|Antal|Totalt|Körnings fel|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Händelser som inte är i ordning|Antal|Totalt|Händelser som inte är i ordning|LogicalName, PartitionId|
|AMLCalloutRequests|Funktions begär Anden|Antal|Totalt|Funktions begär Anden|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Misslyckade funktions begär Anden|Antal|Totalt|Misslyckade funktions begär Anden|LogicalName, PartitionId|
|AMLCalloutInputEvents|Funktions händelser|Antal|Totalt|Funktions händelser|LogicalName, PartitionId|
|DeserializationError|Fel vid deserialisering av indataport|Antal|Totalt|Fel vid deserialisering av indataport|LogicalName, PartitionId|
|EarlyInputEvents|Tidiga ingångs händelser|Antal|Totalt|Tidiga ingångs händelser|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Fördröjning för vattenstämpel|Sekunder|Maximal|Fördröjning för vattenstämpel|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Eftersläpande inloggade ingångs händelser|Antal|Maximal|Eftersläpande inloggade ingångs händelser|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Mottagna inmatade källor|Antal|Totalt|Mottagna inmatade källor|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/miljöer

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedBytes|Inkommande mottagna byte|Skickade|Totalt|Antal byte som lästs från alla händelse källor|Inga dimensioner|
|IngressStoredBytes|Inkommande lagrade byte|Skickade|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Medel|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/miljöer/eventsources

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedBytes|Inkommande mottagna byte|Skickade|Totalt|Antal byte som lästs från händelse källan|Inga dimensioner|
|IngressStoredBytes|Inkommande lagrade byte|Skickade|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Medel|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk-lästa byte/s|BytesPerSecond|Medel|Genomsnittligt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|DiskWriteBytesPerSecond|Disk-skrivna byte/s|BytesPerSecond|Medel|Genomsnittligt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Skickade|Totalt|Totalt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|Disk-skrivna byte|Disk-skrivna byte|Skickade|Totalt|Totalt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|DiskReadOperations|Disk Läs åtgärder|Antal|Totalt|Antalet Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskWriteOperations|Disk skrivnings åtgärder|Antal|Totalt|Antalet i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Genomsnittligt antal Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Genomsnittligt antal i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskReadLatency|Läs fördröjning för disk|Millisekunder|Medel|Total Läs fördröjning. Summan av Läs fördröjningen för enheten och kärnan.|Inga dimensioner|
|DiskWriteLatency|Skriv fördröjning för disk|Millisekunder|Medel|Total Skriv fördröjning. Summan av Skriv fördröjningarna för enheten och kärnan.|Inga dimensioner|
|NetworkInBytesPerSecond|Nätverk i byte/s|BytesPerSecond|Medel|Genomsnittligt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|NetworkOutBytesPerSecond|Nätverks-out-byte/SEK|BytesPerSecond|Medel|Genomsnittligt nätverks data flöde för överförd trafik.|Inga dimensioner|
|Nätverk – inkommande|Nätverk – inkommande|Skickade|Totalt|Totalt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|Nätverk – utgående|Nätverk – utgående|Skickade|Totalt|Totalt nätverks data flöde för överförd trafik.|Inga dimensioner|
|MemoryUsed|Använt minne|Skickade|Medel|Mängden dator minne som används av den virtuella datorn.|Inga dimensioner|
|MemoryGranted|Beviljat minne|Skickade|Medel|Mängden minne som har beviljats för den virtuella datorn av värden. Minnet beviljas inte för värden förrän den är påslagen en gång och beviljat minne kan växlas ut eller visas borta om VMkernel behöver minnet.|Inga dimensioner|
|MemoryActive|Aktivt minne|Skickade|Medel|Mängden minne som används av den virtuella datorn i det tidigare lilla tids fönstret. Detta är "true"-antalet hur mycket minne som den virtuella datorn för närvarande behöver. Ytterligare, oanvänt minne kan växlas ut eller visas utan att påverka gästens prestanda.|Inga dimensioner|
|Procent CPU|Procent CPU|Procent|Medel|PROCESSOR användningen. Det här värdet rapporteras med 100% som representerar alla processor kärnor i systemet. Till exempel är en 2-vägs virtuell dator som använder 50% av ett system med fyra kärnor fullständigt med två kärnor.|Inga dimensioner|
|PercentageCpuReady|Procent andel CPU klar|Millisekunder|Totalt|Ready Time är den tid som väntar på att CPU: er ska bli tillgängliga under det tidigare uppdaterings intervallet.|Inga dimensioner|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medel|Minnes procent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Medel|Diskkölängd|Instans|
|HttpQueueLength|Längd på http-kö|Antal|Medel|Längd på http-kö|Instans|
|BytesReceived|Data i|Skickade|Totalt|Data i|Instans|
|Bytes sent|Data ut|Skickade|Totalt|Data ut|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (exklusive funktioner)

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Skickade|Totalt|Data i|Instans|
|Bytes sent|Data ut|Skickade|Totalt|Data ut|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Http-2xx|Antal|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Antal|Totalt|Http-3xx|Instans|
|Http401|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http-4xx|Antal|Totalt|Http-4xx|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Minnes arbets mängd|Skickade|Medel|Minnes arbets mängd|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Skickade|Medel|Genomsnittlig arbets mängd för minne|Instans|
|AverageResponseTime|Genomsnittlig svars tid|Sekunder|Medel|Genomsnittlig svars tid|Instans|
|AppConnections|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Konsolindataobjekt|Antal referenser|Antal|Medel|Antal referenser|Instans|
|Konversation|Antal trådar|Antal|Medel|Antal trådar|Instans|
|PrivateBytes|Privata byte|Skickade|Medel|Privata byte|Instans|
|IoReadBytesPerSecond|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoOtherBytesPerSecond|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Begär anden i program kön|Antal|Medel|Begär anden i program kön|Instans|
|CurrentAssemblies|Aktuella sammansättningar|Antal|Medel|Aktuella sammansättningar|Instans|
|TotalAppDomains|Totalt antal app-domäner|Antal|Medel|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal app-domäner som har inaktiverats|Antal|Medel|Totalt antal app-domäner som har inaktiverats|Instans|
|Gen0Collections|Skräp insamling för gen 0|Antal|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Skräp insamling för gen 1|Antal|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Gen 2 skräp insamling|Antal|Totalt|Gen 2 skräp insamling|Instans|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (Functions)

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data i|Skickade|Totalt|Data i|Instans|
|Bytes sent|Data ut|Skickade|Totalt|Data ut|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Minnes arbets mängd|Skickade|Medel|Minnes arbets mängd|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Skickade|Medel|Genomsnittlig arbets mängd för minne|Instans|
|FunctionExecutionUnits|Funktions körnings enheter|MB/millisekunder|Totalt|[Funktions körnings enheter](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instans|
|FunctionExecutionCount|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|PrivateBytes|Privata byte|Skickade|Medel|Privata byte|Instans|
|IoReadBytesPerSecond|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoOtherBytesPerSecond|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Begär anden i program kön|Antal|Medel|Begär anden i program kön|Instans|
|CurrentAssemblies|Aktuella sammansättningar|Antal|Medel|Aktuella sammansättningar|Instans|
|TotalAppDomains|Totalt antal app-domäner|Antal|Medel|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal app-domäner som har inaktiverats|Antal|Medel|Totalt antal app-domäner som har inaktiverats|Instans|
|Gen0Collections|Skräp insamling för gen 0|Antal|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Skräp insamling för gen 1|Antal|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Gen 2 skräp insamling|Antal|Totalt|Gen 2 skräp insamling|Instans|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Skickade|Totalt|Data i|Instans|
|Bytes sent|Data ut|Skickade|Totalt|Data ut|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Http-2xx|Antal|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Antal|Totalt|Http-3xx|Instans|
|Http401|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http-4xx|Antal|Totalt|Http-4xx|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Minnes arbets mängd|Skickade|Medel|Minnes arbets mängd|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Skickade|Medel|Genomsnittlig arbets mängd för minne|Instans|
|AverageResponseTime|Genomsnittlig svars tid|Sekunder|Medel|Genomsnittlig svars tid|Instans|
|FunctionExecutionUnits|Funktions körnings enheter|Antal|Totalt|Funktions körnings enheter|Instans|
|FunctionExecutionCount|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|AppConnections|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Konsolindataobjekt|Antal referenser|Antal|Medel|Antal referenser|Instans|
|Konversation|Antal trådar|Antal|Medel|Antal trådar|Instans|
|PrivateBytes|Privata byte|Skickade|Medel|Privata byte|Instans|
|IoReadBytesPerSecond|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoOtherBytesPerSecond|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Begär anden i program kön|Antal|Medel|Begär anden i program kön|Instans|
|CurrentAssemblies|Aktuella sammansättningar|Antal|Medel|Aktuella sammansättningar|Instans|
|TotalAppDomains|Totalt antal app-domäner|Antal|Medel|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal app-domäner som har inaktiverats|Antal|Medel|Totalt antal app-domäner som har inaktiverats|Instans|
|Gen0Collections|Skräp insamling för gen 0|Antal|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Skräp insamling för gen 1|Antal|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Gen 2 skräp insamling|Antal|Totalt|Gen 2 skräp insamling|Instans|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Skickade|Totalt|Data i|Instans|
|Bytes sent|Data ut|Skickade|Totalt|Data ut|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Http-2xx|Antal|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Antal|Totalt|Http-3xx|Instans|
|Http401|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http-4xx|Antal|Totalt|Http-4xx|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|AverageResponseTime|Genomsnittlig svars tid|Sekunder|Medel|Genomsnittlig svars tid|Instans|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medel|Minnes procent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Medel|Diskkölängd|Instans|
|HttpQueueLength|Längd på http-kö|Antal|Medel|Längd på http-kö|Instans|
|ActiveRequests|Aktiva begär Anden|Antal|Totalt|Aktiva begär Anden|Instans|
|TotalFrontEnds|Totalt antal klient delar|Antal|Medel|Totalt antal klient delar|Inga dimensioner|
|SmallAppServicePlanInstances|Små App Service planera arbetare|Antal|Medel|Små App Service planera arbetare|Inga dimensioner|
|MediumAppServicePlanInstances|Medel App Service planera arbetare|Antal|Medel|Medel App Service planera arbetare|Inga dimensioner|
|LargeAppServicePlanInstances|Stora App Service planera arbetare|Antal|Medel|Stora App Service planera arbetare|Inga dimensioner|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Mått|Mått visnings namn|Enhet|Sammansättnings typ|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbetare|Antal|Medel|Totalt antal arbetare|Inga dimensioner|
|WorkersAvailable|Tillgängliga arbetare|Antal|Medel|Tillgängliga arbetare|Inga dimensioner|
|WorkersUsed|Använda arbetare|Antal|Medel|Använda arbetare|Inga dimensioner|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medel|Minnes procent|Instans|

## <a name="next-steps"></a>Nästa steg
* [Läs om mått i Azure Monitor](data-platform.md)
* [Skapa aviseringar för mått](alerts-overview.md)
* [Exportera mått till lagring, Event Hub eller Log Analytics](resource-logs-overview.md)
