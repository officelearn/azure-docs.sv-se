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
ms.openlocfilehash: 49780ec342ea168d27ab8a029c41a1c18a6ffcc4
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019054"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor

Azure Monitor tillhandahåller flera olika sätt att interagera med mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av PowerShell eller CLI. Nedan visas en fullständig lista över alla mått som för närvarande är tillgängliga med Azure Monitor mått pipelinen. Andra mått kan vara tillgängliga i portalen eller med äldre API: er. Listan nedan innehåller endast mått som är tillgängliga med den konsoliderade Azure Monitor mått pipelinen. Använd [2018-01-01 API-versionen](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) för att fråga efter och komma åt dessa mått.

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och visas för varje enskild kö. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|memory_metric|Minne|Byte|Average|Minnesoptimerade. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|TotalConnectionRequests|Totalt antal anslutnings begär Anden|Count|Average|Totalt antal anslutnings begär Anden. Dessa är mottagna.|ServerResourceType|
|SuccessfullConnectionsPerSec|Lyckade anslutningar per sekund|CountPerSecond|Average|Antal slutförda slut för ande av anslutningar.|ServerResourceType|
|TotalConnectionFailures|Totalt antal anslutnings problem|Count|Average|Totalt antal misslyckade anslutnings försök.|ServerResourceType|
|CurrentUserSessions|Aktuella användarsessioner|Count|Average|Aktuellt antal användarsessioner som har upprättats.|ServerResourceType|
|QueryPoolBusyThreads|Upptagna trådar för frågeprocessorn|Count|Average|Antalet upptagna trådar i trådpoolen.|ServerResourceType|
|CommandPoolJobQueueLength|Kölängd för kommando bassäng|Count|Average|Antal jobb i kön för kommando tråds poolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Bearbetar Kölängd för poolen|Count|Average|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|CurrentConnections|Anslutning: Aktuella anslutningar|Count|Average|Aktuellt antal upprättade klient anslutningar.|ServerResourceType|
|CleanerCurrentPrice|Minne: Renare aktuellt pris|Count|Average|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Minne: Krympnings minne|Byte|Average|Mängden minne i byte som kan rensas av bakgrunds rensaren.|ServerResourceType|
|CleanerMemoryNonshrinkable|Minne: Det går inte att krympa rengörings minnet|Byte|Average|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|ServerResourceType|
|MemoryUsage|Minne: Minnesanvändning|Byte|Average|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar minne som har mappats eller allokerats av xVelocity i Memory Analytics Engine (VertiPaq) utöver minnes gränsen för xVelocity-motorn.|ServerResourceType|
|MemoryLimitHard|Minne: Minnes gräns hård|Byte|Average|Hård minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitHigh|Minne: Hög minnes gräns|Byte|Average|Hög minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitLow|Minne: Låg minnes gräns|Byte|Average|Låg minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitVertiPaq|Minne: Minnes gräns VertiPaq|Byte|Average|Minnes intern gräns, från konfigurations filen.|ServerResourceType|
|Kvot|Minne: Kvot|Byte|Average|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|ServerResourceType|
|QuotaBlocked|Minne: Blockerad kvot|Count|Average|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|ServerResourceType|
|VertiPaqNonpaged|Minne: VertiPaq är inte växlat|Byte|Average|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|ServerResourceType|
|VertiPaqPaged|Minne: VertiPaq-växlat|Byte|Average|Byte av växlings Bart minne som används för minnes intern data.|ServerResourceType|
|RowsReadPerSec|Bearbetar: Lästa rader per sekund|CountPerSecond|Average|Antalet rader som läses från alla Relations databaser.|ServerResourceType|
|RowsConvertedPerSec|Bearbetar: Konverterade rader per sekund|CountPerSecond|Average|Antal rader som konverterats under bearbetning.|ServerResourceType|
|RowsWrittenPerSec|Bearbetar: Skrivna rader per sekund|CountPerSecond|Average|Antal rader som skrivs under bearbetningen.|ServerResourceType|
|CommandPoolBusyThreads|Konversation Upptagna trådar för kommando pool|Count|Average|Antalet upptagna trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolIdleThreads|Konversation Inaktiva trådar för kommando pool|Count|Average|Antal inaktiva trådar i kommando tråds-poolen.|ServerResourceType|
|LongParsingBusyThreads|Konversation Lång parsning av upptagna trådar|Count|Average|Antalet upptagna trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingIdleThreads|Konversation Långa parsar inaktiva trådar|Count|Average|Antalet inaktiva trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingJobQueueLength|Konversation Lång parsning av jobb Kölängd|Count|Average|Antal jobb i kön för den långa parsande trådpoolen.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Konversation Processors upptagna I/O-jobb trådar|Count|Average|Antal trådar som kör i/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Konversation Processor belastning är upptagen för icke-I/O-trådar|Count|Average|Antal trådar som kör icke-I/O-jobb i bearbetningen av trådpoolen.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Konversation Processor längd I/O-jobbkö|Count|Average|Antalet I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Konversation Bearbetar inaktiva I/O-jobb trådar|Count|Average|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Konversation Inaktiva icke-I/O-trådar som behandlar poolen|Count|Average|Antalet inaktiva trådar i bearbetnings trådens pool som är dedikerad till icke-I/O-jobb.|ServerResourceType|
|QueryPoolIdleThreads|Konversation Inaktiva trådar för frågenod|Count|Average|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|QueryPoolJobQueueLength|Konversation Kölängd för jobbkö|Count|Average|Antal jobb i kön för trådpoolen för Query.|ServerResourceType|
|ShortParsingBusyThreads|Konversation Kort parsning upptagna trådar|Count|Average|Antalet upptagna trådar i den kort parsar trådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Konversation Korta parsar inaktiva trådar|Count|Average|Antal inaktiva trådar i kort parsar trådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Konversation Kort parsning av jobb Kölängd|Count|Average|Antal jobb i kö för kort parsar trådpoolen.|ServerResourceType|
|memory_thrashing_metric|Nedskräpning för minne|Percent|Average|Genomsnittligt minne nedskräpning.|ServerResourceType|
|mashup_engine_qpu_metric|M-motor QPU|Count|Average|QPU användning av kombinations motor processer|ServerResourceType|
|mashup_engine_memory_metric|M motor minne|Byte|Average|Minnes användning per kombinations motor processer|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Count|Totalt|Det totala antalet Gateway-begäranden under en viss period. Det kan segmenteras av olika dimensioner för att hjälpa dig att diagnostisera problem. |Plats, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Totalt antal Gateway-begäranden|Count|Totalt|Det totala antalet Gateway-begäranden under en viss period. Måttet är inaktuellt, vi rekommenderar att du använder `Requests` det nya måttet. |Plats, värdnamn|
|SuccessfulRequests|Lyckade Gateway-begäranden|Count|Totalt|Det totala antalet lyckade Gateway-begäranden under en viss period. Måttet är inaktuellt, vi rekommenderar att du använder `Requests` det nya måttet.|Plats, värdnamn|
|UnauthorizedRequests|Obehöriga Gateway-begäranden|Count|Totalt| Det totala antalet otillåtna Gateway-begäranden under en viss period. Måttet är inaktuellt, vi rekommenderar att du använder `Requests` det nya måttet.|Plats, värdnamn|
|FailedRequests|Misslyckade Gateway-begäranden|Count|Totalt|Det totala antalet misslyckade Gateway-begäranden under en viss period. Måttet är inaktuellt, vi rekommenderar att du använder `Requests` det nya måttet.|Plats, värdnamn|
|OtherRequests|Andra gateway-begäranden|Count|Totalt|Det totala antalet Gateway-begäranden under en viss period som inte omfattas av lyckade, obehöriga eller misslyckade kategorier. Måttet är inaktuellt, vi rekommenderar att du använder `Requests` det nya måttet. |Plats, värdnamn|
|Duration|Total varaktighet för gateway-begäranden|Millisekunder|Average|Tiden mellan när API Management tar emot en begäran från en klient och när den returnerar ett svar till klienten.|Plats, värdnamn|
|Kapacitet|Kapacitet|Percent|Average|Indikator för belastningen på en API Management instans för att fatta välgrundade beslut om att skala instansen till att hantera mer belastning.|Location|
|EventHubTotalEvents|Totalt antal EventHub-händelser|Count|Totalt|Det totala antalet händelser som skickats till EventHub från API Management under en viss period.|Location|
|EventHubSuccessfulEvents|Lyckade EventHub-händelser|Count|Totalt|Det totala antalet lyckade EventHub-händelser under en viss period.|Location|
|EventHubTotalFailedEvents|Misslyckade EventHub-händelser|Count|Totalt|Det totala antalet misslyckade EventHub-händelser under en viss period.|Location|
|EventHubRejectedEvents|Avvisade EventHub-händelser|Count|Totalt|Det totala antalet nekade EventHub-händelser (felaktig konfiguration eller otillåten) under en viss period.|Location|
|EventHubThrottledEvents|Begränsade EventHub-händelser|Count|Totalt|Det totala antalet begränsade EventHub-händelser under en viss period.|Location|
|EventHubTimedoutEvents|Tids gränsen nåddes för EventHub-händelser|Count|Totalt|Det totala antalet uppnådde tids gränsen för EventHub-händelser under en viss period.|Location|
|EventHubDroppedEvents|Ignorerade EventHub-händelser|Count|Totalt|Det totala antalet händelser som hoppades över på grund av att gränsen för kös Tor lek nåddes under en viss period.|Location|
|EventHubTotalBytesSent|Storlek på EventHub-händelser|Byte|Totalt|Den totala storleken för EventHub-händelser i byte under en viss period.|Location|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Count|Totalt|Det totala antalet jobb|Runbook, status|
|TotalUpdateDeploymentRuns|Total uppdaterings distribution körs|Count|Totalt|Totalt antal körningar av program uppdaterings distribution|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Den totala uppdaterings distributions datorn körs|Count|Totalt|Total distributions dator för program uppdatering körs i en körning av program uppdaterings distribution|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CoreCount|Dedikerat antal kärnor|Count|Totalt|Totalt antal dedikerade kärnor i batch-kontot|Inga dimensioner|
|TotalNodeCount|Antal dedikerade noder|Count|Totalt|Totalt antal dedikerade noder i batch-kontot|Inga dimensioner|
|LowPriorityCoreCount|Antal LowPriority kärnor|Count|Totalt|Totalt antal låg prioritets kärnor i batch-kontot|Inga dimensioner|
|TotalLowPriorityNodeCount|Antal noder med låg prioritet|Count|Totalt|Totalt antal noder med låg prioritet i batch-kontot|Inga dimensioner|
|CreatingNodeCount|Antal noder skapas|Count|Totalt|Antal noder som skapas|Inga dimensioner|
|StartingNodeCount|Antalet noder startas|Count|Totalt|Antal noder som börjar|Inga dimensioner|
|WaitingForStartTaskNodeCount|Väntar på att starta aktiviteter antal noder|Count|Totalt|Antal noder som väntar på att start aktiviteten ska slutföras|Inga dimensioner|
|StartTaskFailedNodeCount|Starta aktivitet antal misslyckade noder|Count|Totalt|Antal noder där start aktiviteten misslyckades|Inga dimensioner|
|IdleNodeCount|Antal inaktiva noder|Count|Totalt|Antal inaktiva noder|Inga dimensioner|
|OfflineNodeCount|Antal offline-noder|Count|Totalt|Antal offline-noder|Inga dimensioner|
|RebootingNodeCount|Antalet noder startas om|Count|Totalt|Antal omstarter av noder|Inga dimensioner|
|ReimagingNodeCount|Antal noder för avbildning|Count|Totalt|Antal avbildnings noder|Inga dimensioner|
|RunningNodeCount|Antal noder som körs|Count|Totalt|Antal noder som körs|Inga dimensioner|
|LeavingPoolNodeCount|Antal noder för att lämna pooler|Count|Totalt|Antal noder som lämnar poolen|Inga dimensioner|
|UnusableNodeCount|Antal noder som inte kan användas|Count|Totalt|Antal oanvändbara noder|Inga dimensioner|
|PreemptedNodeCount|Antal misslyckade noder|Count|Totalt|Antal misslyckade noder|Inga dimensioner|
|TaskStartEvent|Aktivitetens start händelser|Count|Totalt|Totalt antal aktiviteter som har startat|Inga dimensioner|
|TaskCompleteEvent|Uppgift slutförda händelser|Count|Totalt|Totalt antal slutförda uppgifter|Inga dimensioner|
|TaskFailEvent|Aktivitet, misslyckade händelser|Count|Totalt|Totalt antal uppgifter som har slutförts i felaktigt tillstånd|Inga dimensioner|
|PoolCreateEvent|Skapa händelser för pool|Count|Totalt|Totalt antal pooler som har skapats|Inga dimensioner|
|PoolResizeStartEvent|Starta händelser för att ändra storlek på poolen|Count|Totalt|Totalt antal storleks ändringar för pooler som har startat|Inga dimensioner|
|PoolResizeCompleteEvent|Slutför händelser för storleks ändring av pool|Count|Totalt|Totalt antal ändrade pooler som har slutförts|Inga dimensioner|
|PoolDeleteStartEvent|Start händelser för borttagning av pool|Count|Totalt|Totalt antal borttagningar av pooler som har startat|Inga dimensioner|
|PoolDeleteCompleteEvent|Slutför händelse för borttagning av pool|Count|Totalt|Totalt antal borttagna pooler som har slutförts|Inga dimensioner|
|JobDeleteCompleteEvent|Slutför händelser för borttagning av jobb|Count|Totalt|Totalt antal jobb som har tagits bort.|Inga dimensioner|
|JobDeleteStartEvent|Start händelser för jobb borttagning|Count|Totalt|Totalt antal jobb som har begärts att tas bort.|Inga dimensioner|
|JobDisableCompleteEvent|Jobb för att inaktivera slutförda händelser|Count|Totalt|Totalt antal jobb som har inaktiverats.|Inga dimensioner|
|JobDisableStartEvent|Jobb inaktivera start händelser|Count|Totalt|Totalt antal jobb som har begärts att inaktive ras.|Inga dimensioner|
|JobStartEvent|Jobb start händelser|Count|Totalt|Totalt antal jobb som har startats.|Inga dimensioner|
|JobTerminateCompleteEvent|Slutför händelser för avsluta jobb|Count|Totalt|Totalt antal jobb som har avslut ATS.|Inga dimensioner|
|JobTerminateStartEvent|Jobb som avslutar start händelser|Count|Totalt|Totalt antal jobb som har begärts att avslutas.|Inga dimensioner|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedclients|Anslutna klienter|Count|Maximal||ShardId|
|totalcommandsprocessed|Totalt antal åtgärder|Count|Totalt||ShardId|
|cachehits|Cacheträffar|Count|Totalt||ShardId|
|cachemisses|Cachemissar|Count|Totalt||ShardId|
|getcommands|Hämtar|Count|Totalt||ShardId|
|setcommands|Uppsättningar|Count|Totalt||ShardId|
|operationsPerSecond|Åtgärder per sekund|Count|Maximal||ShardId|
|evictedkeys|Avlägsnade nycklar|Count|Totalt||ShardId|
|totalkeys|Totalt antal nycklar|Count|Maximal||ShardId|
|expiredkeys|Utgångna nycklar|Count|Totalt||ShardId|
|usedmemory|Använt minne|Byte|Maximal||ShardId|
|usedmemorypercentage|Använd minnes procent|Percent|Maximal||ShardId|
|usedmemoryRss|RSS för använt minne|Byte|Maximal||ShardId|
|serverLoad|Serverbelastning|Percent|Maximal||ShardId|
|cacheWrite|Cacheskrivning|BytesPerSecond|Maximal||ShardId|
|cacheRead|Cacheläsning|BytesPerSecond|Maximal||ShardId|
|percentProcessorTime|Processor|Percent|Maximal||ShardId|
|cacheLatency|Mikrosekunder för cache-fördröjning (för hands version)|Count|Average||ShardId, SampleType|
|fel|Fel|Count|Maximal||ShardId, ErrorType|
|connectedclients0|Anslutna klienter (Shard 0)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed0|Totalt antal åtgärder (Shard 0)|Count|Totalt||Inga dimensioner|
|cachehits0|Cacheträffar (Shard 0)|Count|Totalt||Inga dimensioner|
|cachemisses0|Cachemissar (Shard 0)|Count|Totalt||Inga dimensioner|
|getcommands0|Hämtar (Shard 0)|Count|Totalt||Inga dimensioner|
|setcommands0|Uppsättningar (Shard 0)|Count|Totalt||Inga dimensioner|
|operationsPerSecond0|Åtgärder per sekund (Shard 0)|Count|Maximal||Inga dimensioner|
|evictedkeys0|Avlägsnade nycklar (Shard 0)|Count|Totalt||Inga dimensioner|
|totalkeys0|Totalt antal nycklar (Shard 0)|Count|Maximal||Inga dimensioner|
|expiredkeys0|Utgångna nycklar (Shard 0)|Count|Totalt||Inga dimensioner|
|usedmemory0|Använt minne (Shard 0)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss0|Använt minne RSS (Shard 0)|Byte|Maximal||Inga dimensioner|
|serverLoad0|Server belastning (Shard 0)|Percent|Maximal||Inga dimensioner|
|cacheWrite0|Skrivcache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead0|Läsning av cache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime0|PROCESSOR (Shard 0)|Percent|Maximal||Inga dimensioner|
|connectedclients1|Anslutna klienter (Shard 1)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed1|Totalt antal åtgärder (Shard 1)|Count|Totalt||Inga dimensioner|
|cachehits1|Cacheträffar (Shard 1)|Count|Totalt||Inga dimensioner|
|cachemisses1|Cachemissar (Shard 1)|Count|Totalt||Inga dimensioner|
|getcommands1|Hämtar (Shard 1)|Count|Totalt||Inga dimensioner|
|setcommands1|Uppsättningar (Shard 1)|Count|Totalt||Inga dimensioner|
|operationsPerSecond1|Åtgärder per sekund (Shard 1)|Count|Maximal||Inga dimensioner|
|evictedkeys1|Avlägsnade nycklar (Shard 1)|Count|Totalt||Inga dimensioner|
|totalkeys1|Totalt antal nycklar (Shard 1)|Count|Maximal||Inga dimensioner|
|expiredkeys1|Utgångna nycklar (Shard 1)|Count|Totalt||Inga dimensioner|
|usedmemory1|Använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss1|RSS för använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|serverLoad1|Server belastning (Shard 1)|Percent|Maximal||Inga dimensioner|
|cacheWrite1|Skrivcache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead1|Läsning av cache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime1|PROCESSOR (Shard 1)|Percent|Maximal||Inga dimensioner|
|connectedclients2|Anslutna klienter (Shard 2)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed2|Totalt antal åtgärder (Shard 2)|Count|Totalt||Inga dimensioner|
|cachehits2|Cacheträffar (Shard 2)|Count|Totalt||Inga dimensioner|
|cachemisses2|Cachemissar (Shard 2)|Count|Totalt||Inga dimensioner|
|getcommands2|Hämtar (Shard 2)|Count|Totalt||Inga dimensioner|
|setcommands2|Uppsättningar (Shard 2)|Count|Totalt||Inga dimensioner|
|operationsPerSecond2|Åtgärder per sekund (Shard 2)|Count|Maximal||Inga dimensioner|
|evictedkeys2|Avlägsnade nycklar (Shard 2)|Count|Totalt||Inga dimensioner|
|totalkeys2|Totalt antal nycklar (Shard 2)|Count|Maximal||Inga dimensioner|
|expiredkeys2|Utgångna nycklar (Shard 2)|Count|Totalt||Inga dimensioner|
|usedmemory2|Använt minne (Shard 2)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss2|Använt minne RSS (Shard 2)|Byte|Maximal||Inga dimensioner|
|serverLoad2|Server belastning (Shard 2)|Percent|Maximal||Inga dimensioner|
|cacheWrite2|Skrivcache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead2|Läsning av cache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime2|PROCESSOR (Shard 2)|Percent|Maximal||Inga dimensioner|
|connectedclients3|Anslutna klienter (Shard 3)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed3|Totalt antal åtgärder (Shard 3)|Count|Totalt||Inga dimensioner|
|cachehits3|Cacheträffar (Shard 3)|Count|Totalt||Inga dimensioner|
|cachemisses3|Cachemissar (Shard 3)|Count|Totalt||Inga dimensioner|
|getcommands3|Hämtar (Shard 3)|Count|Totalt||Inga dimensioner|
|setcommands3|Uppsättningar (Shard 3)|Count|Totalt||Inga dimensioner|
|operationsPerSecond3|Åtgärder per sekund (Shard 3)|Count|Maximal||Inga dimensioner|
|evictedkeys3|Avlägsnade nycklar (Shard 3)|Count|Totalt||Inga dimensioner|
|totalkeys3|Totalt antal nycklar (Shard 3)|Count|Maximal||Inga dimensioner|
|expiredkeys3|Utgångna nycklar (Shard 3)|Count|Totalt||Inga dimensioner|
|usedmemory3|Använt minne (Shard 3)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss3|Använt minne RSS (Shard 3)|Byte|Maximal||Inga dimensioner|
|serverLoad3|Server belastning (Shard 3)|Percent|Maximal||Inga dimensioner|
|cacheWrite3|Skrivcache (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead3|Läsning av cacheminne (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime3|PROCESSOR (Shard 3)|Percent|Maximal||Inga dimensioner|
|connectedclients4|Anslutna klienter (Shard 4)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed4|Totalt antal åtgärder (Shard 4)|Count|Totalt||Inga dimensioner|
|cachehits4|Cacheträffar (Shard 4)|Count|Totalt||Inga dimensioner|
|cachemisses4|Cachemissar (Shard 4)|Count|Totalt||Inga dimensioner|
|getcommands4|Hämtar (Shard 4)|Count|Totalt||Inga dimensioner|
|setcommands4|Uppsättningar (Shard 4)|Count|Totalt||Inga dimensioner|
|operationsPerSecond4|Åtgärder per sekund (Shard 4)|Count|Maximal||Inga dimensioner|
|evictedkeys4|Avlägsnade nycklar (Shard 4)|Count|Totalt||Inga dimensioner|
|totalkeys4|Totalt antal nycklar (Shard 4)|Count|Maximal||Inga dimensioner|
|expiredkeys4|Utgångna nycklar (Shard 4)|Count|Totalt||Inga dimensioner|
|usedmemory4|Använt minne (Shard 4)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss4|Använt minne RSS (Shard 4)|Byte|Maximal||Inga dimensioner|
|serverLoad4|Server belastning (Shard 4)|Percent|Maximal||Inga dimensioner|
|cacheWrite4|Skrivcache (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead4|Läsning av cacheminne (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime4|PROCESSOR (Shard 4)|Percent|Maximal||Inga dimensioner|
|connectedclients5|Anslutna klienter (Shard 5)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed5|Totalt antal åtgärder (Shard 5)|Count|Totalt||Inga dimensioner|
|cachehits5|Cacheträffar (Shard 5)|Count|Totalt||Inga dimensioner|
|cachemisses5|Cachemissar (Shard 5)|Count|Totalt||Inga dimensioner|
|getcommands5|Hämtar (Shard 5)|Count|Totalt||Inga dimensioner|
|setcommands5|Uppsättningar (Shard 5)|Count|Totalt||Inga dimensioner|
|operationsPerSecond5|Åtgärder per sekund (Shard 5)|Count|Maximal||Inga dimensioner|
|evictedkeys5|Avlägsnade nycklar (Shard 5)|Count|Totalt||Inga dimensioner|
|totalkeys5|Totalt antal nycklar (Shard 5)|Count|Maximal||Inga dimensioner|
|expiredkeys5|Utgångna nycklar (Shard 5)|Count|Totalt||Inga dimensioner|
|usedmemory5|Använt minne (Shard 5)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss5|Använt minne RSS (Shard 5)|Byte|Maximal||Inga dimensioner|
|serverLoad5|Server belastning (Shard 5)|Percent|Maximal||Inga dimensioner|
|cacheWrite5|Skrivcache (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead5|Läsning av cacheminne (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime5|PROCESSOR (Shard 5)|Percent|Maximal||Inga dimensioner|
|connectedclients6|Anslutna klienter (Shard 6)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed6|Totalt antal åtgärder (Shard 6)|Count|Totalt||Inga dimensioner|
|cachehits6|Cacheträffar (Shard 6)|Count|Totalt||Inga dimensioner|
|cachemisses6|Cachemissar (Shard 6)|Count|Totalt||Inga dimensioner|
|getcommands6|Hämtar (Shard 6)|Count|Totalt||Inga dimensioner|
|setcommands6|Uppsättningar (Shard 6)|Count|Totalt||Inga dimensioner|
|operationsPerSecond6|Åtgärder per sekund (Shard 6)|Count|Maximal||Inga dimensioner|
|evictedkeys6|Avlägsnade nycklar (Shard 6)|Count|Totalt||Inga dimensioner|
|totalkeys6|Totalt antal nycklar (Shard 6)|Count|Maximal||Inga dimensioner|
|expiredkeys6|Utgångna nycklar (Shard 6)|Count|Totalt||Inga dimensioner|
|usedmemory6|Använt minne (Shard 6)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss6|Använt minne RSS (Shard 6)|Byte|Maximal||Inga dimensioner|
|serverLoad6|Server belastning (Shard 6)|Percent|Maximal||Inga dimensioner|
|cacheWrite6|Skrivcache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead6|Läsning av cache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime6|PROCESSOR (Shard 6)|Percent|Maximal||Inga dimensioner|
|connectedclients7|Anslutna klienter (Shard 7)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed7|Totalt antal åtgärder (Shard 7)|Count|Totalt||Inga dimensioner|
|cachehits7|Cacheträffar (Shard 7)|Count|Totalt||Inga dimensioner|
|cachemisses7|Cachemissar (Shard 7)|Count|Totalt||Inga dimensioner|
|getcommands7|Hämtar (Shard 7)|Count|Totalt||Inga dimensioner|
|setcommands7|Uppsättningar (Shard 7)|Count|Totalt||Inga dimensioner|
|operationsPerSecond7|Åtgärder per sekund (Shard 7)|Count|Maximal||Inga dimensioner|
|evictedkeys7|Avlägsnade nycklar (Shard 7)|Count|Totalt||Inga dimensioner|
|totalkeys7|Totalt antal nycklar (Shard 7)|Count|Maximal||Inga dimensioner|
|expiredkeys7|Utgångna nycklar (Shard 7)|Count|Totalt||Inga dimensioner|
|usedmemory7|Använt minne (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss7|Använt minne RSS (Shard 7)|Byte|Maximal||Inga dimensioner|
|serverLoad7|Server belastning (Shard 7)|Percent|Maximal||Inga dimensioner|
|cacheWrite7|Skrivcache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead7|Läsning av cache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime7|PROCESSOR (Shard 7)|Percent|Maximal||Inga dimensioner|
|connectedclients8|Anslutna klienter (Shard 8)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed8|Totalt antal åtgärder (Shard 8)|Count|Totalt||Inga dimensioner|
|cachehits8|Cacheträffar (Shard 8)|Count|Totalt||Inga dimensioner|
|cachemisses8|Cachemissar (Shard 8)|Count|Totalt||Inga dimensioner|
|getcommands8|Hämtar (Shard 8)|Count|Totalt||Inga dimensioner|
|setcommands8|Uppsättningar (Shard 8)|Count|Totalt||Inga dimensioner|
|operationsPerSecond8|Åtgärder per sekund (Shard 8)|Count|Maximal||Inga dimensioner|
|evictedkeys8|Avlägsnade nycklar (Shard 8)|Count|Totalt||Inga dimensioner|
|totalkeys8|Totalt antal nycklar (Shard 8)|Count|Maximal||Inga dimensioner|
|expiredkeys8|Utgångna nycklar (Shard 8)|Count|Totalt||Inga dimensioner|
|usedmemory8|Använt minne (Shard 8)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss8|Använt minne RSS (Shard 8)|Byte|Maximal||Inga dimensioner|
|serverLoad8|Server belastning (Shard 8)|Percent|Maximal||Inga dimensioner|
|cacheWrite8|Skrivcache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead8|Läsning av cache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime8|PROCESSOR (Shard 8)|Percent|Maximal||Inga dimensioner|
|connectedclients9|Anslutna klienter (Shard 9)|Count|Maximal||Inga dimensioner|
|totalcommandsprocessed9|Totalt antal åtgärder (Shard 9)|Count|Totalt||Inga dimensioner|
|cachehits9|Cacheträffar (Shard 9)|Count|Totalt||Inga dimensioner|
|cachemisses9|Cachemissar (Shard 9)|Count|Totalt||Inga dimensioner|
|getcommands9|Hämtar (Shard 9)|Count|Totalt||Inga dimensioner|
|setcommands9|Uppsättningar (Shard 9)|Count|Totalt||Inga dimensioner|
|operationsPerSecond9|Åtgärder per sekund (Shard 9)|Count|Maximal||Inga dimensioner|
|evictedkeys9|Avlägsnade nycklar (Shard 9)|Count|Totalt||Inga dimensioner|
|totalkeys9|Totalt antal nycklar (Shard 9)|Count|Maximal||Inga dimensioner|
|expiredkeys9|Utgångna nycklar (Shard 9)|Count|Totalt||Inga dimensioner|
|usedmemory9|Använt minne (Shard 9)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss9|Använt minne RSS (Shard 9)|Byte|Maximal||Inga dimensioner|
|serverLoad9|Server belastning (Shard 9)|Percent|Maximal||Inga dimensioner|
|cacheWrite9|Skrivcache (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead9|Läsning av cacheminne (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|percentProcessorTime9|PROCESSOR (Shard 9)|Percent|Maximal||Inga dimensioner|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Average|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer.|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik).|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik).|Inga dimensioner|
|Lästa byte på disk/sek|Diskläsning|BytesPerSecond|Average|Genomsnittligt antal byte lästa från disk under övervakningsperioden.|Inga dimensioner|
|Skrivna byte på disk/sek|Diskskrivning|BytesPerSecond|Average|Genomsnittligt antal byte som skrivits till disk under övervakningsperioden.|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Average|Lästa IOPS på disk.|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Average|Skrivna IOPS på disk.|Inga dimensioner|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domän namn/platser/roller

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Average|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer.|RoleInstanceId|
|Nätverk in|Nätverk in|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik).|RoleInstanceId|
|Nätverk ut|Nätverk ut|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik).|RoleInstanceId|
|Lästa byte på disk/sek|Diskläsning|BytesPerSecond|Average|Genomsnittligt antal byte lästa från disk under övervakningsperioden.|RoleInstanceId|
|Skrivna byte på disk/sek|Diskskrivning|BytesPerSecond|Average|Genomsnittligt antal byte som skrivits till disk under övervakningsperioden.|RoleInstanceId|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Average|Lästa IOPS på disk.|RoleInstanceId|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Average|Skrivna IOPS på disk.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalCalls|Totala anrop|Count|Totalt|Totalt antal anrop.|ApiName, OperationName, region|
|SuccessfulCalls|Utförda anrop|Count|Totalt|Antal utförda anrop.|ApiName, OperationName, region|
|TotalErrors|Totalt antal fel|Count|Totalt|Totalt antal anrop med felsvar (HTTP-svarskod 44x eller 5xx).|ApiName, OperationName, region|
|BlockedCalls|Blockerade anrop|Count|Totalt|Antalet anrop som överskrider gränsen för hastighet eller kvot.|ApiName, OperationName, region|
|ServerErrors|Serverfel|Count|Totalt|Antal anrop med internt fel i tjänsten (HTTP-svarskod 5xx).|ApiName, OperationName, region|
|ClientErrors|Klientfel|Count|Totalt|Antal anrop med fel på klientsidan (HTTP-svarskod 4xx).|ApiName, OperationName, region|
|Datain|Data in|Byte|Totalt|Storlek på inkommande data i byte.|ApiName, OperationName, region|
|Data|Data ut|Byte|Totalt|Storlek på utgående data i byte.|ApiName, OperationName, region|
|Svarstid|Svarstid|Millisekunder|Average|Svarstid i millisekunder.|ApiName, OperationName, region|
|CharactersTranslated|Översatta tecken|Count|Totalt|Totalt antal tecken i inkommande textbegäran.|ApiName, OperationName, region|
|CharactersTrained|Upptränade tecken|Count|Totalt|Totalt antal tecken som har tränats.|ApiName, OperationName, region|
|SpeechSessionDuration|Varaktighet för talsession|Sekunder|Totalt|Total varaktighet för talsession i sekunder.|ApiName, OperationName, region|
|TotalTransactions|Totalt antal transaktioner|Count|Totalt|Totalt antal transaktioner.|Inga dimensioner|
|TotalTokenCalls|Tokenanrop totalt|Count|Totalt|Totalt antal tokenanrop.|ApiName, OperationName, region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inga dimensioner|
|Nätverk in|Nätverk in, fakturerbart|Byte|Totalt|Antal fakturerbara byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk ut|Nätverk ut, fakturerbart|Byte|Totalt|Antal fakturerbara byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte lästa från disk under övervakningsperioden|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Byte skrivna från disk under övervakningsperioden|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Average|Lästa IOPS på disk|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Average|Skrivna IOPS på disk|Inga dimensioner|
|Återstående CPU-krediter|Återstående CPU-krediter|Count|Average|Totalt antal krediter tillgängliga för plötsliga toppar|Inga dimensioner|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Count|Average|Totalt antal krediter som använts av den virtuella datorn|Inga dimensioner|
|Lästa byte per disk/sek|Lästa byte på datadisk/sek (inaktuellt)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperiod|SlotId|
|Skrivna byte per disk/sek|Skrivna byte på datadisk/sek (inaktuellt)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperiod|SlotId|
|Läsåtgärder per disk/sek|Läsåtgärder på datadisk/sek (inaktuellt)|CountPerSecond|Average|Lästa IOPS från en enda disk under övervakningsperioden|SlotId|
|Skrivåtgärder per disk/sek|Skrivåtgärder på datadisk/sek (inaktuellt)|CountPerSecond|Average|Skrivna IOPS från en enda disk under övervakningsperioden|SlotId|
|Ködjup per disk|Ködjup för datadisk (inaktuellt)|Count|Average|Datadiskködjup (eller kölängd)|SlotId|
|Lästa byte per operativsystemdisk/sek|Lästa byte på operativsystemdisk/sek (inaktuellt)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperiod för operativsystemdisk|Inga dimensioner|
|Skrivna byte per operativsystemdisk/sek|Skrivna byte på operativsystemdisk/sek (inaktuellt)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperiod för operativsystemdisk|Inga dimensioner|
|Läsåtgärder per operativsystemdisk/sek|Läsåtgärder på operativsystemdisk/sek (inaktuellt)|CountPerSecond|Average|Lästa IOPS från en enda disk under övervakningsperioden för operativsystemdisk|Inga dimensioner|
|Skrivåtgärder per operativsystemdisk/sek|Skrivåtgärder på operativsystemdisk/sek (inaktuellt)|CountPerSecond|Average|Skrivna IOPS från en enda disk under övervakningsperioden för operativsystemdisk|Inga dimensioner|
|Ködjup per operativsystemdisk|Ködjup för operativsystemdisk (inaktuellt)|Count|Average|Operativsystemdisks ködjup (eller kölängd)|Inga dimensioner|
|Lästa byte på datadisk/sek|Lästa byte på datadisk/sek (förhandsversion)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperiod|LUN|
|Skrivna byte på datadisk/sek|Skrivna byte på datadisk/sek (förhandsversion)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperiod|LUN|
|Läsåtgärder på datadisk/sek|Läsåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Average|Lästa IOPS från en enda disk under övervakningsperioden|LUN|
|Skrivåtgärder på datadisk/sek|Skrivåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Average|Skrivna IOPS från en enda disk under övervakningsperioden|LUN|
|Ködjup för datadisk|Ködjup för datadisk (förhandsversion)|Count|Average|Datadiskködjup (eller kölängd)|LUN|
|Lästa byte på operativsystemdisk/sek|Lästa byte per operativsystemdisk/sek (förhandsversion)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperiod för operativsystemdisk|Inga dimensioner|
|Skrivna byte på operativsystemdisk/sek|Skrivna byte per operativsystemdisk/sek (förhandsversion)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperiod för operativsystemdisk|Inga dimensioner|
|Läsåtgärder på operativsystemdisk/sek|Läsåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Average|Lästa IOPS från en enda disk under övervakningsperioden för operativsystemdisk|Inga dimensioner|
|Operativsystemdisks skrivåtgärder/sek|Skrivåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Average|Skrivna IOPS från en enda disk under övervakningsperioden för operativsystemdisk|Inga dimensioner|
|Ködjup för operativsystemdisk|Ködjup för operativsystemdisk (förhandsversion)|Count|Average|Operativsystemdisks ködjup (eller kölängd)|Inga dimensioner|
|Inkommande flöden|Inkommande flöden (förhandsversion)|Count|Average|Inkommande flöden är antalet aktuella flöden i inkommande riktning (inkommande trafik till den virtuella datorn)|Inga dimensioner|
|Utgående flöden|Utgående flöden (förhandsversion)|Count|Average|Utgående flöden är antalet aktuella flöden i utgående riktning (utgående trafik från den virtuella datorn)|Inga dimensioner|
|Maximal skapandefrekvens för inkommande flöden|Maximal skapandefrekvens för inkommande flöden (förhandsversion)|CountPerSecond|Average|Maximal skapandefrekvens för inkommande flöden (inkommande trafik till den virtuella datorn)|Inga dimensioner|
|Maximal skapandefrekvens för utgående flöden|Maximal skapandefrekvens för utgående flöden (förhandsversion)|CountPerSecond|Average|Maximal skapandefrekvens för utgående flöden (utgående trafik från den virtuella datorn)|Inga dimensioner|
|Cacheläsningsträff på premium-datadisk|Cacheläsningsträff på premium-datadisk (förhandsversion)|Percent|Average|Cacheläsningsträff på premium-datadisk|LUN|
|Cacheläsningsmiss på premium-datadisk|Cacheläsningsmiss på premium-datadisk (förhandsversion)|Percent|Average|Cacheläsningsmiss på premium-datadisk|LUN|
|Cacheläsningsträff på premium-operativsystemdisk|Cacheläsningsträff på premium-operativsystemdisk (förhandsversion)|Percent|Average|Cacheläsningsträff på premium-operativsystemdisk|Inga dimensioner|
|Cacheläsningsmiss på premium-operativsystemdisk|Cacheläsningsmiss på premium-operativsystemdisk (förhandsversion)|Percent|Average|Cacheläsningsmiss på premium-operativsystemdisk|Inga dimensioner|
|Nätverk in, totalt|Nätverk in, totalt|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|Inga dimensioner|
|Nätverk ut, totalt|Nätverk ut, totalt|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|Inga dimensioner|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|VMName|
|Nätverk in|Nätverk in, fakturerbart|Byte|Totalt|Antal fakturerbara byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|VMName|
|Nätverk ut|Nätverk ut, fakturerbart|Byte|Totalt|Antal fakturerbara byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|VMName|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte lästa från disk under övervakningsperioden|VMName|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Byte skrivna från disk under övervakningsperioden|VMName|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Average|Lästa IOPS på disk|VMName|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Average|Skrivna IOPS på disk|VMName|
|Återstående CPU-krediter|Återstående CPU-krediter|Count|Average|Totalt antal krediter tillgängliga för plötsliga toppar|Inga dimensioner|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Count|Average|Totalt antal krediter som använts av den virtuella datorn|Inga dimensioner|
|Lästa byte per disk/sek|Lästa byte på datadisk/sek (inaktuellt)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperiod|SlotId|
|Skrivna byte per disk/sek|Skrivna byte på datadisk/sek (inaktuellt)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperiod|SlotId|
|Läsåtgärder per disk/sek|Läsåtgärder på datadisk/sek (inaktuellt)|CountPerSecond|Average|Lästa IOPS från en enda disk under övervakningsperioden|SlotId|
|Skrivåtgärder per disk/sek|Skrivåtgärder på datadisk/sek (inaktuellt)|CountPerSecond|Average|Skrivna IOPS från en enda disk under övervakningsperioden|SlotId|
|Ködjup per disk|Ködjup för datadisk (inaktuellt)|Count|Average|Datadiskködjup (eller kölängd)|SlotId|
|Lästa byte per operativsystemdisk/sek|Lästa byte på operativsystemdisk/sek (inaktuellt)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperiod för operativsystemdisk|Inga dimensioner|
|Skrivna byte per operativsystemdisk/sek|Skrivna byte på operativsystemdisk/sek (inaktuellt)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperiod för operativsystemdisk|Inga dimensioner|
|Läsåtgärder per operativsystemdisk/sek|Läsåtgärder på operativsystemdisk/sek (inaktuellt)|CountPerSecond|Average|Lästa IOPS från en enda disk under övervakningsperioden för operativsystemdisk|Inga dimensioner|
|Skrivåtgärder per operativsystemdisk/sek|Skrivåtgärder på operativsystemdisk/sek (inaktuellt)|CountPerSecond|Average|Skrivna IOPS från en enda disk under övervakningsperioden för operativsystemdisk|Inga dimensioner|
|Ködjup per operativsystemdisk|Ködjup för operativsystemdisk (inaktuellt)|Count|Average|Operativsystemdisks ködjup (eller kölängd)|Inga dimensioner|
|Lästa byte på datadisk/sek|Lästa byte på datadisk/sek (förhandsversion)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperiod|LUN, VMName|
|Skrivna byte på datadisk/sek|Skrivna byte på datadisk/sek (förhandsversion)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperiod|LUN, VMName|
|Läsåtgärder på datadisk/sek|Läsåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Average|Lästa IOPS från en enda disk under övervakningsperioden|LUN, VMName|
|Skrivåtgärder på datadisk/sek|Skrivåtgärder per datadisk/sek (förhandsversion)|CountPerSecond|Average|Skrivna IOPS från en enda disk under övervakningsperioden|LUN, VMName|
|Ködjup för datadisk|Ködjup för datadisk (förhandsversion)|Count|Average|Datadiskködjup (eller kölängd)|LUN, VMName|
|Lästa byte på operativsystemdisk/sek|Lästa byte per operativsystemdisk/sek (förhandsversion)|CountPerSecond|Average|Byte/sek lästa från en enskild disk under övervakningsperiod för operativsystemdisk|VMName|
|Skrivna byte på operativsystemdisk/sek|Skrivna byte per operativsystemdisk/sek (förhandsversion)|CountPerSecond|Average|Byte/sek skrivna till en enskild disk under övervakningsperiod för operativsystemdisk|VMName|
|Läsåtgärder på operativsystemdisk/sek|Läsåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Average|Lästa IOPS från en enda disk under övervakningsperioden för operativsystemdisk|VMName|
|Operativsystemdisks skrivåtgärder/sek|Skrivåtgärder på operativsystemdisk/sek (förhandsversion)|CountPerSecond|Average|Skrivna IOPS från en enda disk under övervakningsperioden för operativsystemdisk|VMName|
|Ködjup för operativsystemdisk|Ködjup för operativsystemdisk (förhandsversion)|Count|Average|Operativsystemdisks ködjup (eller kölängd)|VMName|
|Inkommande flöden|Inkommande flöden (förhandsversion)|Count|Average|Inkommande flöden är antalet aktuella flöden i inkommande riktning (inkommande trafik till den virtuella datorn)|VMName|
|Utgående flöden|Utgående flöden (förhandsversion)|Count|Average|Utgående flöden är antalet aktuella flöden i utgående riktning (utgående trafik från den virtuella datorn)|VMName|
|Maximal skapandefrekvens för inkommande flöden|Maximal skapandefrekvens för inkommande flöden (förhandsversion)|CountPerSecond|Average|Maximal skapandefrekvens för inkommande flöden (inkommande trafik till den virtuella datorn)|VMName|
|Maximal skapandefrekvens för utgående flöden|Maximal skapandefrekvens för utgående flöden (förhandsversion)|CountPerSecond|Average|Maximal skapandefrekvens för utgående flöden (utgående trafik från den virtuella datorn)|VMName|
|Cacheläsningsträff på premium-datadisk|Cacheläsningsträff på premium-datadisk (förhandsversion)|Percent|Average|Cacheläsningsträff på premium-datadisk|LUN, VMName|
|Cacheläsningsmiss på premium-datadisk|Cacheläsningsmiss på premium-datadisk (förhandsversion)|Percent|Average|Cacheläsningsmiss på premium-datadisk|LUN, VMName|
|Cacheläsningsträff på premium-operativsystemdisk|Cacheläsningsträff på premium-operativsystemdisk (förhandsversion)|Percent|Average|Cacheläsningsträff på premium-operativsystemdisk|VMName|
|Cacheläsningsmiss på premium-operativsystemdisk|Cacheläsningsmiss på premium-operativsystemdisk (förhandsversion)|Percent|Average|Cacheläsningsmiss på premium-operativsystemdisk|VMName|
|Nätverk in, totalt|Nätverk in, totalt|Byte|Totalt|Antal byte som mottagits i alla nätverksgränssnitt av virtuella datorer (inkommande trafik)|VMName|
|Nätverk ut, totalt|Nätverk ut, totalt|Byte|Totalt|Antal byte som skickats ut i alla nätverksgränssnitt av virtuella datorer (utgående trafik)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|CPU-användning|Count|Average|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Minnesanvändning|Byte|Average|Total minnesanvändning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Mottagna nätverksbyte per sekund|Byte|Average|Nätverksbyte som mottagits per sekund.|Inga dimensioner|
|NetworkBytesTransmittedPerSecond|Överförda nätverksbyte per sekund|Byte|Average|Nätverksbyte som överförts per sekund.|Inga dimensioner|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalPullCount|Totalt antal hämtningar|Count|Average|Totalt antal bild hämtningar|Inga dimensioner|
|SuccessfulPullCount|Antal lyckade pull-överföringar|Count|Average|Antal lyckade bild hämtningar|Inga dimensioner|
|TotalPushCount|Totalt antal push-meddelanden|Count|Average|Totalt antal push-meddelanden för avbildningar|Inga dimensioner|
|SuccessfulPushCount|Antal lyckade push-meddelanden|Count|Average|Antal lyckade avbildnings push-meddelanden|Inga dimensioner|
|RunDuration|Körnings tid|Millisekunder|Totalt|Körnings tid i millisekunder|Inga dimensioner|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Count|Totalt|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Inga dimensioner|
|kube_node_status_allocatable_memory_bytes|Total mängd tillgängligt minne i ett hanterat kluster|Byte|Totalt|Total mängd tillgängligt minne i ett hanterat kluster|Inga dimensioner|
|kube_pod_status_ready|Antal poddar i klart läge|Count|Totalt|Antal poddar i klart läge|namnrymd, Pod|
|kube_node_status_condition|Status för olika nod villkor|Count|Totalt|Status för olika nod villkor|villkor, status, status2, nod|
|kube_pod_status_phase|Antal poddar per fas|Count|Totalt|Antal poddar per fas|fas, namnrymd, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API-anrop|Count|Totalt||Inga dimensioner|
|DCIMappingImportOperationSuccessfulLines|Raderna för att mappa import åtgärd har slutförts|Count|Totalt||Inga dimensioner|
|DCIMappingImportOperationFailedLines|Det gick inte att mappa rader i import åtgärden|Count|Totalt||Inga dimensioner|
|DCIMappingImportOperationTotalLines|Mappa totalt antal rader för import åtgärd|Count|Totalt||Inga dimensioner|
|DCIMappingImportOperationRuntimeInSeconds|Mappar import åtgärds körning på några sekunder|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundProfileExportSucceeded|Utgående profil har exporter ATS|Count|Totalt||Inga dimensioner|
|DCIOutboundProfileExportFailed|Det gick inte att exportera utgående profil|Count|Totalt||Inga dimensioner|
|DCIOutboundProfileExportDuration|Export varaktighet för utgående profil|Sekunder|Totalt||Inga dimensioner|
|DCIOutboundKpiExportSucceeded|Utgående KPI-export lyckades|Count|Totalt||Inga dimensioner|
|DCIOutboundKpiExportFailed|Utgående KPI-export misslyckades|Count|Totalt||Inga dimensioner|
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
|ImportASAValuesFailed|Antal misslyckade import ASA-värden|Count|Totalt||Inga dimensioner|
|ImportASAValuesSucceeded|Antal lyckade import-värden för ASA|Count|Totalt||Inga dimensioner|
|DCIProfilesCount|Antal profil instanser|Count|Sista||Inga dimensioner|
|DCIInteractionsPerMonthCount|Antal interaktioner per månad|Count|Sista||Inga dimensioner|
|DCIKpisCount|KPI-antal|Count|Sista||Inga dimensioner|
|DCISegmentsCount|Antal segment|Count|Sista||Inga dimensioner|
|DCIPredictiveMatchPoliciesCount|Förutsägelse matchnings antal|Count|Sista||Inga dimensioner|
|DCIPredictionsCount|Antal förutsägelser|Count|Sista||Inga dimensioner|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|NICReadThroughput|Läsningsdataflöde (nätverk)|BytesPerSecond|Average|Läsningsdataflöde för nätverksgränssnittet på enheten i rapporteringsperioden för alla volymer i gatewayen.|InstanceName|
|NICWriteThroughput|Skrivningsdataflöde (nätverk)|BytesPerSecond|Average|Skrivningsdataflöde för nätverksgränssnittet på enheten i rapporteringsperioden för alla volymer i gatewayen.|InstanceName|
|CloudReadThroughputPerShare|Dataflöde för molnnedladdning (resurs)|BytesPerSecond|Average|Dataflödet för nedladdning till Azure från en resurs under rapporteringsperioden.|Dela|
|CloudUploadThroughputPerShare|Dataflöde för molnuppladdning (resurs)|BytesPerSecond|Average|Dataflödet för uppladdning till Azure från en resurs under rapporteringsperioden.|Dela|
|BytesUploadedToCloudPerShare|Uppladdade molnbyte (resurs)|Byte|Average|Totalt antal byte som laddas upp till Azure från en resurs under rapporteringsperioden.|Dela|
|Enhet|Total kapacitet|Byte|Average|Total kapacitet|Inga dimensioner|
|Availablecapacity;)|Tillgänglig kapacitet|Byte|Average|Tillgänglig kapacitet i byte under rapporteringsperioden.|Inga dimensioner|
|CloudUploadThroughput|Dataflöde för molnuppladdning|BytesPerSecond|Average|Moln överförings data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|CloudReadThroughput|Data flöde för moln hämtning|BytesPerSecond|Average|Molnet Ladda ned data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|BytesUploadedToCloud|Uppladdade molnbyte (enhet)|Byte|Average|Totalt antal byte som laddas upp till Azure från en enhet under rapporteringsperioden.|Inga dimensioner|
|HyperVVirtualProcessorUtilization|Databehandling på gränsenheter: processorprocentandel|Percent|Average|Processoranvändning i procent|InstanceName|
|HyperVMemoryUtilization|Databehandling på gränsenheter: minnesanvändning|Percent|Average|Mängd RAM som används|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FailedRuns|Misslyckade körningar|Count|Totalt||pipelineName, activityName|
|SuccessfulRuns|Lyckade körningar|Count|Totalt||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PipelineFailedRuns|Misslyckad pipeline kör mått|Count|Totalt||FailureType, namn|
|PipelineSucceededRuns|Slutförd pipeline kör mått|Count|Totalt||FailureType, namn|
|ActivityFailedRuns|Misslyckad aktivitet kör mått|Count|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Genomförd aktivitet kör mått|Count|Totalt||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Misslyckad utlösare kör mått|Count|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Lyckade utlösare kör mått|Count|Totalt||Namn, FailureType|
|IntegrationRuntimeCpuPercentage|PROCESSOR användning för integration runtime|Percent|Average||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAvailableMemory|Tillgängligt minne för integration runtime|Byte|Average||IntegrationRuntimeName, nodnamn|
|MaxAllowedResourceCount|Maximalt antal tillåtna entiteter|Count|Maximal||Inga dimensioner|
|MaxAllowedFactorySizeInGbUnits|Högsta tillåtna fabriks storlek (GB enhet)|Count|Maximal||Inga dimensioner|
|ResourceCount|Totalt antal entiteter|Count|Maximal||Inga dimensioner|
|FactorySizeInGbUnits|Total fabriks storlek (GB enhet)|Count|Maximal||Inga dimensioner|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobEndedSuccess|Slutförda jobb|Count|Totalt|Antal lyckade jobb.|Inga dimensioner|
|JobEndedFailure|Misslyckade jobb|Count|Totalt|Antal misslyckade jobb.|Inga dimensioner|
|JobEndedCancelled|Avbrutna jobb|Count|Totalt|Antal avbrutna jobb.|Inga dimensioner|
|JobAUEndedSuccess|Lyckad AU-tid|Sekunder|Totalt|Total AU-tid för lyckade jobb.|Inga dimensioner|
|JobAUEndedFailure|Misslyckad AU-tid|Sekunder|Totalt|Total AU-tid för misslyckade jobb.|Inga dimensioner|
|JobAUEndedCancelled|Tid för avbruten AU|Sekunder|Totalt|Total AU-tid för avbrutna jobb.|Inga dimensioner|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Byte|Maximal|Den totala mängden data som lagras i kontot.|Inga dimensioner|
|DataWritten|Skrivna data|Byte|Totalt|Total mängd data som skrivs till kontot.|Inga dimensioner|
|DataRead|Lästa data|Byte|Totalt|Total mängd data som lästs från kontot.|Inga dimensioner|
|WriteRequests|Skriv förfrågningar|Count|Totalt|Antal data skrivnings begär anden till kontot.|Inga dimensioner|
|ReadRequests|Läs begär Anden|Count|Totalt|Antal data läsnings begär anden till kontot.|Inga dimensioner|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|memory_percent|Minnes procent|Percent|Average|Minnes procent|Inga dimensioner|
|io_consumption_percent|I/o procent|Percent|Average|I/o procent|Inga dimensioner|
|storage_percent|Lagrings procent|Percent|Average|Lagrings procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagrings gräns|Byte|Average|Lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Server logg lagrings procent|Percent|Average|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Server logg lagring används|Byte|Average|Server logg lagring används|Inga dimensioner|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Average|Server logg lagrings gräns|Inga dimensioner|
|active_connections|Aktiva anslutningar|Count|Average|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga dimensioner|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Count|Average|Fördröjning för replikering på några sekunder|Inga dimensioner|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Average|Lagring av säkerhets kopior som används|Inga dimensioner|
|network_bytes_egress|Nätverk ut|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk in|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|memory_percent|Minnes procent|Percent|Average|Minnes procent|Inga dimensioner|
|io_consumption_percent|I/o procent|Percent|Average|I/o procent|Inga dimensioner|
|storage_percent|Lagrings procent|Percent|Average|Lagrings procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagrings gräns|Byte|Average|Lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Server logg lagrings procent|Percent|Average|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Server logg lagring används|Byte|Average|Server logg lagring används|Inga dimensioner|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Average|Server logg lagrings gräns|Inga dimensioner|
|active_connections|Aktiva anslutningar|Count|Average|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga dimensioner|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Count|Average|Fördröjning för replikering på några sekunder|Inga dimensioner|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Average|Lagring av säkerhets kopior som används|Inga dimensioner|
|network_bytes_egress|Nätverk ut|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk in|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|memory_percent|Minnes procent|Percent|Average|Minnes procent|Inga dimensioner|
|io_consumption_percent|I/o procent|Percent|Average|I/o procent|Inga dimensioner|
|storage_percent|Lagrings procent|Percent|Average|Lagrings procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|storage_limit|Lagrings gräns|Byte|Average|Lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Server logg lagrings procent|Percent|Average|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Server logg lagring används|Byte|Average|Server logg lagring används|Inga dimensioner|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Average|Server logg lagrings gräns|Inga dimensioner|
|active_connections|Aktiva anslutningar|Count|Average|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga dimensioner|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Average|Lagring av säkerhets kopior som används|Inga dimensioner|
|network_bytes_egress|Nätverk ut|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk in|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|pg_replica_log_delay_in_seconds|Replik fördröjning|Sekunder|Maximal|Replik fördröjning i sekunder|Inga dimensioner|
|pg_replica_log_delay_in_bytes|Maximal fördröjning mellan repliker|Byte|Maximal|Fördröjning i byte för den flesta isolerings repliken|Inga dimensioner|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Percent|Average|CPU-procent|Inga dimensioner|
|memory_percent|Minnes procent|Percent|Average|Minnes procent|Inga dimensioner|
|IOPS|IOPS|Count|Average|I/o-åtgärder per sekund|Inga dimensioner|
|storage_percent|Lagrings procent|Percent|Average|Lagrings procent|Inga dimensioner|
|storage_used|Använt lagringsutrymme|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|active_connections|Aktiva anslutningar|Count|Average|Aktiva anslutningar|Inga dimensioner|
|network_bytes_egress|Nätverk ut|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Nätverk in|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Skicka försök för telemetri|Count|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.ingress.success|Meddelande om telemetri|Count|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inga dimensioner|
|C2D. commands. utgående. Complete. lyckades|Slutförda kommandon|Count|Totalt|Antalet moln-till-enhet-kommandon som har slutförts av enheten|Inga dimensioner|
|c2d.commands.egress.abandon.success|Övergivna kommandon|Count|Totalt|Antalet moln-till-enhet-kommandon som avbrutits av enheten|Inga dimensioner|
|c2d.commands.egress.reject.success|Nekade kommandon|Count|Totalt|Antal moln-till-enhet-kommandon som avvisats av enheten|Inga dimensioner|
|devices.totalDevices|Totalt antal enheter (inaktuella)|Count|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|devices.connectedDevices.allProtocol|Anslutna enheter (inaktuella) |Count|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.egress.success|Routning: telemetri meddelanden levereras|Count|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inga dimensioner|
|d2c.telemetry.egress.dropped|Routning: telemetri ignoreras |Count|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inga dimensioner|
|d2c.telemetry.egress.orphaned|Routning: telemetri-meddelanden har överblivna |Count|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inga dimensioner|
|d2c.telemetry.egress.invalid|Routning: telemetri-meddelanden är inkompatibla|Count|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inga dimensioner|
|d2c.telemetry.egress.fallback|Routning: meddelanden levererade till reserv|Count|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inga dimensioner|
|d2c.endpoints.egress.eventHubs|Routning: meddelanden levererade till Händelsehubben|Count|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.eventHubs|Routning: meddelande fördröjning för Event Hub|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusQueues|Routning: meddelanden levererade till Service Bus kö|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusQueues|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusTopics|Routning: meddelanden levererade till Service Bus ämnet|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusTopics|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inga dimensioner|
|d2c.endpoints.egress.builtIn.events|Routning: meddelanden som levereras till meddelanden/händelser|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser). Det här måttet börjar bara fungera när routning är https://aka.ms/iotrouting) aktiverat (för IoT Hub.|Inga dimensioner|
|d2c.endpoints.latency.builtIn.events|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser). Det här måttet börjar bara fungera när routning är https://aka.ms/iotrouting) aktiverat (för IoT Hub.|Inga dimensioner|
|D2C. endpoints. utgående. Storage|Routning: meddelanden som levereras till lagring|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inga dimensioner|
|d2c.endpoints.latency.storage|Routning: meddelande fördröjning för lagring|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. byte|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inga dimensioner|
|d2c.endpoints.egress.storage.blobs|Routning: blobbar levererade till lagring|Count|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inga dimensioner|
|EventGridDeliveries|Event Grid leveranser (för hands version)|Count|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen (https://aka.ms/ioteventgrid).|Resultat, EventType|
|EventGridLatency|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|Händelsetyp|
|d2c.twin.read.success|Lyckades dubbla läsningar från enheter|Count|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inga dimensioner|
|d2c.twin.read.failure|Misslyckade dubbla läsningar från enheter|Count|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|d2c.twin.read.size|Svars storlek för dubbla läsningar från enheter|Byte|Average|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|d2c.twin.update.success|Lyckade dubbla uppdateringar från enheter|Count|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|d2c.twin.update.failure|Misslyckade dubbla uppdateringar från enheter|Count|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|d2c.twin.update.size|Storlek på dubbla uppdateringar från enheter|Byte|Average|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|c2d.methods.success|Direkta metod anrop|Count|Totalt|Antalet lyckade direkta metod anrop.|Inga dimensioner|
|C2D. Methods. Failure|Misslyckade direkta metod anrop|Count|Totalt|Antalet misslyckade direkta metod anrop.|Inga dimensioner|
|C2D. Methods. requestSize|Begär ande storlek för direkta metod anrop|Byte|Average|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inga dimensioner|
|C2D. Methods. responseSize|Svars storlek för direkta metod anrop|Byte|Average|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inga dimensioner|
|c2d.twin.read.success|Lyckades dubbla läspaket från Server delen|Count|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inga dimensioner|
|c2d.twin.read.failure|Det gick inte att dubbla läsningar från Server delen|Count|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|c2d.twin.read.size|Svars storlek för dubbla läsningar från Server delen|Byte|Average|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inga dimensioner|
|c2d.twin.update.success|Lyckades dubbla uppdateringar från Server delen|Count|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inga dimensioner|
|c2d.twin.update.failure|Misslyckade dubbla uppdateringar från Server delen|Count|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|c2d.twin.update.size|Storlek på dubbla uppdateringar från Server delen|Byte|Average|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inga dimensioner|
|twinQueries. lyckades|Lyckades dubbla frågor|Count|Totalt|Antalet lyckade dubbla frågor.|Inga dimensioner|
|twinQueries. Failure|Misslyckade dubbla frågor|Count|Totalt|Antalet misslyckade dubbla frågor.|Inga dimensioner|
|twinQueries.resultSize|Resultat storlek för dubbla frågor|Byte|Average|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inga dimensioner|
|jobs.createTwinUpdateJob.success|Skapandet av dubbla uppdaterings jobb lyckades|Count|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|jobs.createTwinUpdateJob.failure|Det gick inte att skapa dubbla uppdaterings jobb|Count|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|jobs.createDirectMethodJob.success|Lyckade skapande av metod anrops jobb|Count|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inga dimensioner|
|jobs.createDirectMethodJob.failure|Det gick inte att skapa metod anrops jobb|Count|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inga dimensioner|
|jobs.listJobs.success|Lyckade anrop till List jobb|Count|Totalt|Antalet lyckade anrop till List jobb.|Inga dimensioner|
|Jobs. listJobs. Failure|Misslyckade anrop till List jobb|Count|Totalt|Antalet misslyckade anrop till List jobb.|Inga dimensioner|
|Jobs. cancelJob. lyckades|Slutförda jobb avbokningar|Count|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|Jobs. cancelJob. Failure|Misslyckade jobb-annulleringar|Count|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|Jobs. queryJobs. lyckades|Slutförda jobb frågor|Count|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inga dimensioner|
|Jobs. queryJobs. Failure|Misslyckade jobb frågor|Count|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inga dimensioner|
|jobb. slutfört|Slutförda jobb|Count|Totalt|Antalet slutförda jobb.|Inga dimensioner|
|jobb. misslyckades|Misslyckade jobb|Count|Totalt|Antalet misslyckade jobb.|Inga dimensioner|
|d2c.telemetry.ingress.sendThrottle|Antal begränsnings fel|Count|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inga dimensioner|
|dailyMessageQuotaUsed|Totalt antal meddelanden som används|Count|Average|Antal sammanlagt antal meddelanden som använts idag. Detta är ett ackumulerat värde som återställs till noll vid 00:00 UTC varje dag.|Inga dimensioner|
|deviceDataUsage|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|totalDeviceCount|Totalt antal enheter (förhands granskning)|Count|Average|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|connectedDeviceCount|Anslutna enheter (förhands granskning)|Count|Average|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|konfigurationer|Konfigurations mått|Count|Totalt|Mått för konfigurations åtgärder|Inga dimensioner|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Registrerings försök|Count|Totalt|Antal enhets registreringar som försökts|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Tilldelade enheter|Count|Totalt|Antal enheter som har tilldelats till en IoT-hubb|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attesterings försök|Count|Totalt|Antal försök till enhets attestering|ProvisioningServiceName, status, protokoll|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AvailableStorage|Tillgängligt lagrings utrymme|Byte|Totalt|Totalt tillgängligt lagrings utrymme som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|CassandraConnectionClosures|Cassandra-anslutningens stängningar|Count|Totalt|Antalet Cassandra-anslutningar som stängdes, rapporteras med en 1 minuts kornig het|Region, ClosureReason|
|CassandraRequestCharges|Avgifter för Cassandra-begäran|Count|Totalt|Ru: er förbrukat för Cassandra begär Anden|DatabaseName, samlings namn, region, OperationType, ResourceType|
|CassandraRequests|Cassandra-begäranden|Count|Count|Antal Cassandra-begäranden som gjorts|DatabaseName, samlings namn, region, OperationType, ResourceType, ErrorCode|
|DataUsage|Dataanvändning|Byte|Totalt|Total data användning rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentCount|Antal dokument|Count|Totalt|Totalt antal dokument som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentQuota|Dokument kvot|Byte|Totalt|Total lagrings kvot som rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|IndexUsage|Index användning|Byte|Totalt|Total användning av index rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|MetadataRequests|Begär Anden om metadata|Count|Count|Antal metadata-begäranden. Cosmos DB behåller systemets metadata-samling för varje konto, vilket gör att du kan räkna upp samlingar, databaser osv och deras konfigurationer kostnads fritt.|DatabaseName, samlings namn, region, StatusCode, |
|MongoRequestCharge|Mongo begär ande avgift|Count|Totalt|Mongo enheter för förbrukad begäran|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequests|Mongo-begäranden|Count|Count|Antal Mongo-begäranden som gjorts|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|ProvisionedThroughput|Etablerat dataflöde|Count|Maximal|Etablerat dataflöde|DatabaseName, samlings namn|
|ReplicationLatency|Fördröjning för P99-replikering|Millisekunder|Average|P99 för replikering i käll-och mål regioner för geo-aktiverat konto|SourceRegion, TargetRegion|
|ServiceAvailability|Tjänst tillgänglighet|Percent|Average|Konto begär tillgänglighet på en timme, dag eller månads kornig het|Inga dimensioner|
|TotalRequestUnits|Totalt antal enheter för programbegäran|Count|Totalt|Förbrukade enheter för begär Ande|DatabaseName, samlings namn, region, StatusCode, OperationType|
|TotalRequests|Totalt antal begäranden|Count|Count|Antal begär Anden som gjorts|DatabaseName, samlings namn, region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Count|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishFailCount|Publicera misslyckade händelser|Count|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Count|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Count|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MatchedEventCount|Matchande händelser|Count|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inga dimensioner|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Count|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Count|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Average|Varaktighet för mål bearbetning i millisekunder|Inga dimensioner|
|DroppedEventCount|Ignorerade händelser|Count|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|DeadLetteredCount|Obeställbara, Brevade händelser|Count|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Count|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishFailCount|Misslyckade händelser|Count|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Count|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Count|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Slutförda förfrågningar|Count|Totalt|Lyckade begäranden för Microsoft.EventHub.|Entitetsnamnet |
|ServerErrors|Serverfel.|Count|Totalt|Serverfel för Microsoft.EventHub.|Entitetsnamnet |
|UserErrors|Användarfel.|Count|Totalt|Användarfel för Microsoft.EventHub.|Entitetsnamnet |
|QuotaExceededErrors|Kvoten överskridits-fel.|Count|Totalt|Kvoten överskridits-fel för Microsoft.EventHub.|Entitetsnamnet |
|ThrottledRequests|Begränsade begäranden.|Count|Totalt|Begränsade begäranden för Microsoft.EventHub.|Entitetsnamnet |
|IncomingRequests|Inkommande förfrågningar|Count|Totalt|Inkommande begäranden för Microsoft.EventHub.|EntityName|
|IncomingMessages|Inkommande meddelanden|Count|Totalt|Inkommande meddelanden för Microsoft.EventHub.|EntityName|
|OutgoingMessages|Utgående meddelanden|Count|Totalt|Utgående meddelanden för Microsoft.EventHub.|EntityName|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft.EventHub.|EntityName|
|OutgoingBytes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Totalt antal aktiva anslutningar för Microsoft.EventHub.|Inga dimensioner|
|ConnectionsOpened|Öppnade anslutningar.|Count|Average|Öppnade Microsoft.EventHub-anslutningar.|EntityName|
|ConnectionsClosed|Stängda anslutningar.|Count|Average|Stängda Microsoft.EventHub-anslutningar.|EntityName|
|CaptureBacklog|Hämta kvarvarande uppgifter.|Count|Totalt|Hämta kvarvarande uppgifter för Microsoft.EventHub.|EntityName|
|CapturedMessages|Hämtade meddelanden.|Count|Totalt|Hämtade meddelanden för Microsoft.EventHub.|EntityName|
|CapturedBytes|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft.EventHub.|EntityName|
|Size|Size|Byte|Average|Storlek på en EventHub i byte.|EntityName|
|INREQS|Inkommande begär Anden (inaktuellt)|Count|Totalt|Totalt antal inkommande sändnings begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|SUCCREQ|Lyckade förfrågningar (inaktuellt)|Count|Totalt|Totalt antal slutförda begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|FAILREQ|Misslyckade förfrågningar (inaktuellt)|Count|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|SVRBSY|Serverns upptaget fel (inaktuellt)|Count|Totalt|Totalt antal upptagen server-fel för en namnrymd (inaktuell)|Inga dimensioner|
|MELLAN|Interna Server fel (inaktuellt)|Count|Totalt|Totalt antal interna Server fel för en namnrymd (inaktuell)|Inga dimensioner|
|MISCERR|Andra fel (inaktuellt)|Count|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|INMSGS|Inkommande meddelanden (inaktuellt) (inaktuellt)|Count|Totalt|Totalt antal inkommande meddelanden för ett namn område. Måttet är föråldrat. Använd måtten inkommande e-postmeddelanden i stället (inaktuellt)|Inga dimensioner|
|EHINMSGS|Inkommande meddelanden (inaktuellt)|Count|Totalt|Totalt antal inkommande meddelanden för en namnrymd (inaktuell)|Inga dimensioner|
|OUTMSGS|Utgående meddelanden (inaktuellt) (inaktuellt)|Count|Totalt|Totalt antal utgående meddelanden för ett namn område. Måttet är föråldrat. Använd utgående meddelande mått i stället (inaktuellt)|Inga dimensioner|
|EHOUTMSGS|Utgående meddelanden (inaktuellt)|Count|Totalt|Totalt antal utgående meddelanden för en namnrymd (inaktuell)|Inga dimensioner|
|EHINMBS|Inkommande byte (inaktuellt) (inaktuellt)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för ett namn område. Måttet är föråldrat. Använd måttet inkommande byte i stället (inaktuellt)|Inga dimensioner|
|EHINBYTES|Inkommande byte (inaktuellt)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för en namnrymd (inaktuell)|Inga dimensioner|
|EHOUTMBS|Utgående byte (inaktuellt) (inaktuellt)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd. Måttet är föråldrat. Använd utgående byte-mått i stället (inaktuellt)|Inga dimensioner|
|EHOUTBYTES|Utgående byte (inaktuellt)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd (inaktuell)|Inga dimensioner|
|EHABL|Arkivera efter släpning meddelanden (inaktuellt)|Count|Totalt|Event Hub arkivera meddelanden i efter släpning för en namnrymd (inaktuell)|Inga dimensioner|
|EHAMSGS|Arkivera meddelanden (inaktuellt)|Count|Totalt|Arkiverade meddelanden i Event Hub i ett namn område (inaktuellt)|Inga dimensioner|
|EHAMBS|Arkiv meddelande genom strömning (inaktuellt)|Byte|Totalt|Det arkiverade meddelande flödet i Event Hub i ett namn område (inaktuellt)|Inga dimensioner|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade förfrågningar (för hands version)|Count|Totalt|Lyckade begäranden för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|ServerErrors|Serverfel. (Förhandsgranskning)|Count|Totalt|Serverfel för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|UserErrors|Användarfel. (Förhandsgranskning)|Count|Totalt|Användarfel för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|QuotaExceededErrors|Kvoten överskridits-fel. (Förhandsgranskning)|Count|Totalt|Kvoten överskridits-fel för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|ThrottledRequests|Begränsade begäranden. (Förhandsgranskning)|Count|Totalt|Begränsade begäranden för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|IncomingRequests|Inkommande förfrågningar (för hands version)|Count|Totalt|Inkommande begäranden för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|IncomingMessages|Inkommande meddelanden (förhands granskning)|Count|Totalt|Inkommande meddelanden för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|OutgoingMessages|Utgående meddelanden (förhands granskning)|Count|Totalt|Utgående meddelanden för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|IncomingBytes|Inkommande byte. (Förhandsgranskning)|Byte|Totalt|Inkommande byte för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|OutgoingBytes|Utgående byte. (Förhandsgranskning)|Byte|Totalt|Utgående byte för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|ActiveConnections|ActiveConnections (för hands version)|Count|Average|Totalt antal aktiva anslutningar för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|ConnectionsOpened|Öppnade anslutningar. (Förhandsgranskning)|Count|Average|Öppnade Microsoft.EventHub-anslutningar. (Förhandsgranskning)|Inga dimensioner|
|ConnectionsClosed|Stängda anslutningar. (Förhandsgranskning)|Count|Average|Stängda Microsoft.EventHub-anslutningar. (Förhandsgranskning)|Inga dimensioner|
|CaptureBacklog|Hämta kvarvarande uppgifter. (Förhandsgranskning)|Count|Totalt|Hämta kvarvarande uppgifter för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|CapturedMessages|Hämtade meddelanden. (Förhandsgranskning)|Count|Totalt|Hämtade meddelanden för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|CapturedBytes|Hämtade byte. (Förhandsgranskning)|Byte|Totalt|Hämtade byte för Microsoft.EventHub. (Förhandsgranskning)|Inga dimensioner|
|Processor|PROCESSOR (för hands version)|Percent|Maximal|Processoranvändning för Event Hub-klustret i procentandel|Role|
|AvailableMemory|Tillgängligt minne (förhands granskning)|Count|Maximal|Tillgängligt minne för Event Hub-klustret i byte|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kluster

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway-begäranden|Count|Totalt|Antal Gateway-begäranden|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategoriserade Gateway-begäranden|Count|Totalt|Antal Gateway-begäranden per kategori (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Antal aktiva arbetare|Count|Maximal|Antal aktiva arbetare|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerat mätvärde|Count|Average|Värdet som beräknas av autoskalning vid körning|MetricTriggerSource|
|MetricThreshold|Tröskelvärde för mått|Count|Average|Det inställda tröskelvärdet när autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Observerad kapacitet|Count|Average|Den kapacitet som rapporteras till autoskalning vid körning.|Inga dimensioner|
|ScaleActionsInitiated|Skalningsåtgärder har initierats|Count|Totalt|Riktningen på skalningsåtgärden.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

(Offentlig för hands version)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Tillgänglighet|Procent|Average|Procent slutförda tillgänglighets test har slutförts|availabilityResult/namn, availabilityResult/plats|
|availabilityResults/antal|Tillgänglighetstester|Count|Count|Antal tillgänglighets test|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|availabilityResults/varaktighet|Varaktighet för tillgänglighetstest|Millisekunder|Average|Varaktighet för tillgänglighetstest|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|browserTimings/networkDuration|Nätverksanslutningstid för sidhämtning|Millisekunder|Average|Tid mellan användar förfrågan och nätverks anslutning. Inkluderar DNS-sökning och transport anslutning.|Inga dimensioner|
|browserTimings/processingDuration|Klientbehandlingstid|Millisekunder|Average|Tiden mellan att ta emot sista byten i ett dokument tills DOM har lästs in. Asynkrona begär Anden kan fortfarande bearbetas.|Inga dimensioner|
|browserTimings/receiveDuration|Tar emot svarstid|Millisekunder|Average|Tiden mellan de första och de sista byten, eller till frånkoppling.|Inga dimensioner|
|browserTimings/sendDuration|Tid för att skicka förfrågan|Millisekunder|Average|Tiden mellan nätverksanslutning och mottagande av de första byten.|Inga dimensioner|
|browserTimings/totalDuration|Sidhämtningstid för webbläsare|Millisekunder|Average|Tiden från användarförfrågan till dess att DOM, formatmallar, skript och bilder har lästs in.|Inga dimensioner|
|beroenden/antal|Beroendeanrop|Count|Count|Antal anrop gjorda av programmet till externa resurser|beroende/typ, beroende/performanceBucket, beroende/lyckades, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/varaktighet|Beroendevaraktighet|Millisekunder|Average|Varaktigheten för anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/misslyckades|Beroende anrops problem|Count|Count|Antal misslyckade beroendeanrop från programmet till externa resurser.|beroende/typ, beroende/performanceBucket, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|pageViews/antal|Sidvisning|Count|Count|Antal sidvisningar.|åtgärd/syntetisk|
|pageViews/varaktighet|Inläsningstid för sidvisning|Millisekunder|Average|Inläsningstid för sidvisning|åtgärd/syntetisk|
|performanceCounters/requestExecutionTime|Körningstid för HTTP-begäranden|Millisekunder|Average|Körningstid för de senaste förfrågningarna.|Cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-begäranden i programkön|Count|Average|Programfrågeköns längd|Cloud/roleInstance|
|performanceCounters/requestsPerSecond|Hastighet för HTTP-begäranden|CountPerSecond|Average|Hastigheten per sekund för alla förfrågningar till programmet från ASP.NET.|Cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Antal undantag|CountPerSecond|Average|Antalet hanterade och ohanterade undantag som rapporterats till Windows, inklusive .NET-undantag och ohanterade undantag som har konverterats till .NET-undantag.|Cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|IO-frekvens för process|BytesPerSecond|Average|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|Cloud/roleInstance|
|performanceCounters/processCpuPercentage|Processoranvändning för process|Percent|Average|Procent andelen av förfluten tid som alla process trådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger enbart prestanda för W3wp-processen.|Cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processortid|Percent|Average|Den procentandel av tiden som processorn ägnat åt icke-inaktiva trådar|Cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Ledigt minne|Byte|Average|Fysiskt minne som är omedelbart tillgängligt för att tilldelas en process eller för att användas av systemet.|Cloud/roleInstance|
|performanceCounters/processPrivateBytes|Privata byte för process|Byte|Average|Minne som har tilldelats exklusivt för att övervaka programprocesser.|Cloud/roleInstance|
|begär Anden/varaktighet|Serversvarstid|Millisekunder|Average|Tid från det att en HTTP-förfrågning mottagits till dess att svaret har skickats.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/antal|Serverbegäranden|Count|Count|Antal slutförda HTTP-förfrågningar.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/misslyckade|Misslyckade förfrågningar|Count|Count|Antal HTTP-begäranden som marker ATS som misslyckade. I de flesta fall är dessa förfrågningar med svars kod > = 400 och inte lika med 401.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Cloud/roleName|
|begär Anden/pris|Server begär ande frekvens|CountPerSecond|Average|Antal server begär Anden per sekund|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|undantag/antal|Undantag|Count|Count|Kombinerat antal undantagsfel utan felhantering.|Cloud/roleName, Cloud/roleInstance, Client/Type|
|undantag/webbläsare|Webbläsarundantag|Count|Count|Antal undantagsfel utan felhantering som har utlösts i webbläsaren.|Inga dimensioner|
|undantag/Server|Serverundantag|Count|Count|Antal undantagsfel utan felhantering som har utlösts i serverprogrammet.|Cloud/roleName, Cloud/roleInstance|
|spårning/antal|Spårningar|Count|Count|Antal spårningsdokument|Trace/severityLevel, operation/syntetisk, Cloud/roleName, Cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Totalt antal Service API-träffar|Count|Count|Totalt antal Service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Total svarstid för Service API|Millisekunder|Average|Total svarstid för Service API-begäranden|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Totalt antal Service API-resultat|Count|Count|Totalt antal Service API-resultat|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CacheUtilization|Användning av cache|Percent|Average|Användnings nivå i kluster omfånget|Inga|
|QueryDuration|Frågans varaktighet|Millisekunder|Average|Frågornas varaktighet i sekunder|Frågestatus|
|IngestionUtilization|Förbruknings användning|Percent|Average|Förhållandet mellan använda inmatnings platser i klustret|Inga|
|KeepAlive|Behåll Alive|Count|Average|Sanity check anger att klustret svarar på frågor|Inga|
|IngestionVolumeInMB|Inmatnings volym (i MB)|Count|Totalt|Total mängd inmatade data till klustret (i MB)|Databas|
|IngestionLatencyInSeconds|Inmatnings svars tid (i sekunder)|Sekunder|Average|Hämtnings tiden från källan (t. ex. meddelandet är i EventHub) till klustret på några sekunder|Inga|
|EventProcessedForEventHubs|Bearbetade händelser (för Event Hubs)|Count|Totalt|Antal händelser som bearbetas av klustret vid inmatning från Händelsehubben|Inga|
|IngestionResult|Inmatnings resultat|Count|Count|Antal inmatnings åtgärder|Status|
|Processor|Processor|Percent|Average|PROCESSOR användnings nivå|Inga|
| ContinuousExportNumOfRecordsExported | Antal poster som exporter ATS vid löpande export | Count | Totalt | Antal poster som har exporter ATS för varje lagrings artefakt som skrivits under export åtgärden  | Inga |
| ExportUtilization | Exportera användning | Percent | Maximal | Exportera användning | Inga |
| ContinuousExportPendingCount | Antal väntande pågående export | Count | Maximal | Antal väntande kontinuerliga export jobb som är klara för körning | Inga |
| ContinuousExportMaxLatenessMinutes | Högsta antal förseningar i minuter för kontinuerlig export | Count | Maximal | Max tid i minuter för all kontinuerlig export som väntar och är redo för körning | Inga |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Count|Count|Antal API-anrop|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Count|Totalt|Antal startade arbetsflödeskörningar.|Inga dimensioner|
|RunsCompleted|Slutförda körningar|Count|Totalt|Antal slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunsSucceeded|Lyckade körningar|Count|Totalt|Antal lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsFailed|Misslyckade körningar|Count|Totalt|Antal misslyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsCancelled|Körningarna har avbrutits|Count|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunLatency|Svarstid för körning|Sekunder|Average|Svarstid för slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunSuccessLatency|Svarstid för lyckad körning|Sekunder|Average|Svarstid för lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunThrottledEvents|Kör begränsade händelser|Count|Totalt|Antal arbetsflödesåtgärds- eller utlösarbegränsade händelser.|Inga dimensioner|
|RunFailurePercentage|Kör felprocent|Percent|Totalt|Misslyckade arbetsflödeskörningar i procent.|Inga dimensioner|
|ActionsStarted|Startade åtgärder |Count|Totalt|Antal startade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsCompleted|Slutförda åtgärder |Count|Totalt|Antal slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSucceeded|Lyckade åtgärder |Count|Totalt|Antal lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsFailed|Misslyckade åtgärder|Count|Totalt|Antal misslyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSkipped|Överhoppade åtgärder |Count|Totalt|Antal överhoppade arbetsflödesåtgärder.|Inga dimensioner|
|ActionLatency|Svarstid för åtgärd |Sekunder|Average|Svarstid för slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionSuccessLatency|Svarstid för lyckad åtgärd |Sekunder|Average|Svarstid för lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionThrottledEvents|Begränsade händelser för åtgärder|Count|Totalt|Antal begränsade händelser för arbetsflödesåtgärder.|Inga dimensioner|
|TriggersStarted|Startade utlösare |Count|Totalt|Antal startade arbetsflödesutlösare.|Inga dimensioner|
|TriggersCompleted|Slutförda utlösare |Count|Totalt|Antal slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggersSucceeded|Lyckade utlösare |Count|Totalt|Antal lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggersFailed|Misslyckade utlösare |Count|Totalt|Antal misslyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggersSkipped|Ignorerade utlösare|Count|Totalt|Antal överhoppade arbetsflödesutlösare.|Inga dimensioner|
|TriggersFired|Aktiverade utlösare |Count|Totalt|Antal utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerLatency|Latensutlösare |Sekunder|Average|Svarstid för slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggerFireLatency|Brandlatensutlösare |Sekunder|Average|Svartstid för utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerSuccessLatency|Framgångslatensutlösare |Sekunder|Average|Svartstid för lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggerThrottledEvents|Utlösare för begränsade händelser|Count|Totalt|Antal begränsade händelser för arbetsflödesutlösare.|Inga dimensioner|
|BillableActionExecutions|Fakturerbara åtgärdskörningar|Count|Totalt|Antal körningar av arbetsflödesåtgärder som faktureras.|Inga dimensioner|
|BillableTriggerExecutions|Fakturerbara utlösarkörningar|Count|Totalt|Antal körningar av arbetsflödesutlösare som faktureras.|Inga dimensioner|
|TotalBillableExecutions|Totalt antal fakturerbara körningar|Count|Totalt|Antal arbetsflödeskörningar som faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Faktureringsanvändning för körningar av interna åtgärder|Count|Totalt|Det antal ursprungliga åtgärdskörningar som faktureras.|Inga dimensioner|
|BillingUsageStandardConnector|Faktureringsanvändning för körningar av standardanslutningsappar|Count|Totalt|Det antal standardanslutningsappskörningar som faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Faktureringsanvändning för körningar av lagringsförbrukning|Count|Totalt|Det antal lagringsförbrukningskörningar som faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Faktureringsanvändning för körningar av interna åtgärder|Count|Totalt|Det antal ursprungliga åtgärdskörningar som faktureras.|Inga dimensioner|
|BillingUsageStandardConnector|Faktureringsanvändning för körningar av standardanslutningsappar|Count|Totalt|Det antal standardanslutningsappskörningar som faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Faktureringsanvändning för körningar av lagringsförbrukning|Count|Totalt|Det antal lagringsförbrukningskörningar som faktureras.|Inga dimensioner|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Count|Totalt|Antal startade arbetsflödeskörningar.|Inga dimensioner|
|RunsCompleted|Slutförda körningar|Count|Totalt|Antal slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunsSucceeded|Lyckade körningar|Count|Totalt|Antal lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsFailed|Misslyckade körningar|Count|Totalt|Antal misslyckade arbetsflödeskörningar.|Inga dimensioner|
|RunsCancelled|Körningarna har avbrutits|Count|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunLatency|Svarstid för körning|Sekunder|Average|Svarstid för slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunSuccessLatency|Svarstid för lyckad körning|Sekunder|Average|Svarstid för lyckade arbetsflödeskörningar.|Inga dimensioner|
|RunThrottledEvents|Kör begränsade händelser|Count|Totalt|Antal arbetsflödesåtgärds- eller utlösarbegränsade händelser.|Inga dimensioner|
|RunStartThrottledEvents|Begränsade händelser vid starta körningen|Count|Totalt|Antal begränsade händelser för arbetsflödet starta körning.|Inga dimensioner|
|RunFailurePercentage|Kör felprocent|Percent|Totalt|Misslyckade arbetsflödeskörningar i procent.|Inga dimensioner|
|ActionsStarted|Startade åtgärder |Count|Totalt|Antal startade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsCompleted|Slutförda åtgärder |Count|Totalt|Antal slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSucceeded|Lyckade åtgärder |Count|Totalt|Antal lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsFailed|Misslyckade åtgärder |Count|Totalt|Antal misslyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSkipped|Överhoppade åtgärder |Count|Totalt|Antal överhoppade arbetsflödesåtgärder.|Inga dimensioner|
|ActionLatency|Svarstid för åtgärd |Sekunder|Average|Svarstid för slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionSuccessLatency|Svarstid för lyckad åtgärd |Sekunder|Average|Svarstid för lyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionThrottledEvents|Begränsade händelser för åtgärder|Count|Totalt|Antal begränsade händelser för arbetsflödesåtgärder.|Inga dimensioner|
|TriggersStarted|Startade utlösare |Count|Totalt|Antal startade arbetsflödesutlösare.|Inga dimensioner|
|TriggersCompleted|Slutförda utlösare |Count|Totalt|Antal slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggersSucceeded|Lyckade utlösare |Count|Totalt|Antal lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggersFailed|Misslyckade utlösare |Count|Totalt|Antal misslyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggersSkipped|Ignorerade utlösare|Count|Totalt|Antal överhoppade arbetsflödesutlösare.|Inga dimensioner|
|TriggersFired|Aktiverade utlösare |Count|Totalt|Antal utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerLatency|Latensutlösare |Sekunder|Average|Svarstid för slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggerFireLatency|Brandlatensutlösare |Sekunder|Average|Svartstid för utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerSuccessLatency|Framgångslatensutlösare |Sekunder|Average|Svartstid för lyckade arbetsflödesutlösare.|Inga dimensioner|
|TriggerThrottledEvents|Utlösare för begränsade händelser|Count|Totalt|Antal begränsade händelser för arbetsflödesutlösare.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Arbetsflödets processoranvändning för integreringstjänstmiljön|Percent|Average|Arbetsflödets processoranvändning för integreringstjänstmiljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Arbetsflödets minnesanvändning för integreringstjänstmiljön|Percent|Average|Arbetsflödets minnesanvändning för integreringstjänstmiljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Anslutningsappens processoranvändning för integreringstjänstmiljön|Percent|Average|Anslutningsappens processoranvändning för integreringstjänstmiljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Anslutningsappens minnesanvändning för integreringstjänstmiljön|Percent|Average|Anslutningsappens minnesanvändning för integreringstjänstmiljön.|Inga dimensioner|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Slutförda körningar|Slutförda körningar|Count|Totalt|Antal körningar som har slutförts för den här arbets ytan|Scenario|
|Startade körningar|Startade körningar|Count|Totalt|Antal körningar som har startats för den här arbets ytan|Scenario|
|Misslyckade körningar|Misslyckade körningar|Count|Totalt|Antalet körningar som misslyckades för den här arbets ytan|Scenario|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konton

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Count|Count|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|
|Tillgänglighet|Tillgänglighet|Procent|Average|Tillgänglighet för API: erna|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageOtherLatency|Genomsnittlig annan svars tid|MS/op|Average|Genomsnittlig annan svars tid (som inte är läsning eller skrivning) i millisekunder per åtgärd|Inga dimensioner|
|AverageReadLatency|Genomsnittlig Läs fördröjning|MS/op|Average|Genomsnittlig Läs fördröjning i millisekunder per åtgärd|Inga dimensioner|
|AverageTotalLatency|Genomsnittlig total svars tid|MS/op|Average|Genomsnittlig total svars tid i millisekunder per åtgärd|Inga dimensioner|
|AverageWriteLatency|Genomsnittlig Skriv fördröjning|MS/op|Average|Genomsnittlig Skriv fördröjning i millisekunder per åtgärd|Inga dimensioner|
|FilesystemOtherOps|Fil Systems annat OPS|hantering|Average|Antal andra åtgärder för fil systemet (som inte läses eller skrivas)|Inga dimensioner|
|FilesystemReadOps|Read-Ops för fil system|hantering|Average|Antal Läs åtgärder för fil systemet|Inga dimensioner|
|FilesystemTotalOps|Fil system totalt OPS|hantering|Average|Summan av alla fil Systems åtgärder|Inga dimensioner|
|FilesystemWriteOps|Skriv Ops för fil system|hantering|Average|Antal Skriv åtgärder i fil systemet|Inga dimensioner|
|IoBytesPerOtherOps|IO-byte per annat OPS|byte/op|Average|Antal in-/ut-byte per andra åtgärder (som inte kan läsas eller skrivas)|Inga dimensioner|
|IoBytesPerReadOps|IO-byte per Read-OPS|byte/op|Average|Antal in-/ut-byte per Läs åtgärd|Inga dimensioner|
|IoBytesPerTotalOps|IO-byte per op för alla åtgärder|byte/op|Average|Summan av alla in-/ut-byte-åtgärder|Inga dimensioner|
|IoBytesPerWriteOps|IO-byte per Skriv åtgärd|byte/op|Average|Antal in-/ut-byte per Skriv åtgärd|Inga dimensioner|
|OtherIops|Andra IOPS|åtgärder per sekund|Average|Annan in/ut-åtgärd per sekund|Inga dimensioner|
|OtherThroughput|Annat data flöde|S|Average|Annat data flöde (som inte läses eller skrivs) i megabyte per sekund|Inga dimensioner|
|ReadIops|Läs IOPS|åtgärder per sekund|Average|Läs-/ut-åtgärder per sekund|Inga dimensioner|
|ReadThroughput|Läs data flöde|S|Average|Läs data flöde i megabyte per sekund|Inga dimensioner|
|TotalIops|Totalt antal IOPS|åtgärder per sekund|Average|Summan av alla in/ut-åtgärder per sekund|Inga dimensioner|
|TotalThroughput|Totalt dataflöde|S|Average|Summan av alla data flöde i megabyte per sekund|Inga dimensioner|
|VolumeAllocatedSize|Allokerad volym storlek|byte|Average|Allokerad storlek på volymen (inte de faktiska använda bytena)|Inga dimensioner|
|VolumeLogicalSize|Logisk volym storlek|byte|Average|Den logiska storleken på volymen (använda byte)|Inga dimensioner|
|VolumeSnapshotSize|Storlek på volym ögonblicks bild|byte|Average|Storlek på alla ögonblicks bilder i volymen|Inga dimensioner|
|WriteIops|Skriv IOPS|åtgärder per sekund|Average|Skriv in/ut-åtgärder per sekund|Inga dimensioner|
|WriteThroughput|Skriv data flöde|S|Average|Skriv data flöde i megabyte per sekund|Inga dimensioner|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Allokerad storlek för volym pool|byte|Average|Allokerad storlek för poolen (inte de faktiska använda bytena)|Inga dimensioner|
|VolumePoolAllocatedUsed|Allokerad volym pool används|byte|Average|Allokerad använt storlek på poolen|Inga dimensioner|
|VolumePoolTotalLogicalSize|Total logisk storlek i volymprocent|byte|Average|Summan av den logiska storleken för alla volymer som tillhör poolen|Inga dimensioner|
|VolumePoolTotalSnapshotSize|Total storlek på ögonblicks bild av volym pool|byte|Average|Summan av alla ögonblicks bilder i poolen|Inga dimensioner|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesSentRate|Skickade byte|Count|Totalt|Antal byte som nätverks gränssnittet har skickats|Inga dimensioner|
|BytesReceivedRate|Mottagna byte|Count|Totalt|Antal byte som nätverks gränssnittet har tagits emot|Inga dimensioner|
|PacketsSentRate|Skickade paket|Count|Totalt|Antal paket som nätverks gränssnittet har skickats|Inga dimensioner|
|PacketsReceivedRate|Mottagna paket|Count|Totalt|Antal paket som nätverks gränssnittet har fått|Inga dimensioner|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|Tillgänglighet för data Sök väg|Count|Average|Genomsnittlig tillgänglighet för Load Balancer data Sök väg per tids längd|FrontendIPAddress, FrontendPort|
|DipAvailability|Status för hälso avsökning|Count|Average|Genomsnittlig status för Load Balancer hälso avsökning per tids period|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Antal byte|Count|Totalt|Totalt antal byte som skickats inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Antal paket|Count|Totalt|Totalt antal överförda paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Antal SYN|Count|Totalt|Totalt antal skickade SYN paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Antal SNAT-anslutningar|Count|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tids perioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allokerade SNAT-portar (förhands granskning)|Count|Totalt|Totalt antal SNAT-portar som allokerats inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Använda SNAT-portar (förhands granskning)|Count|Totalt|Totalt antal SNAT-portar som använts inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Fråga volym|Count|Totalt|Antal frågor som hanteras för en DNS-zon|Inga dimensioner|
|RecordSetCount|Antal post uppsättningar|Count|Maximal|Antal post uppsättningar i en DNS-zon|Inga dimensioner|
|RecordSetCapacityUtilization|Kapacitets användning för post uppsättning|Percent|Maximal|Procent av post uppsättnings kapaciteten som används av en DNS-zon|Inga dimensioner|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
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
|IfUnderDDoSAttack|Under DDoS-attack eller inte|Count|Maximal|Under DDoS-attack eller inte|Inga dimensioner|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerUDPPackets|Ingående UDP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Ingående UDP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerSYNPackets|Inkommande SYN paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande SYN paket för att utlösa DDoS-minskning|Inga dimensioner|
|VipAvailability|Tillgänglighet för data Sök väg|Count|Average|Genomsnittlig tillgänglighet för IP-adress per tids längd|Port|
|ByteCount|Antal byte|Count|Totalt|Totalt antal byte som skickats inom tids perioden|Port, riktning|
|PacketCount|Antal paket|Count|Totalt|Totalt antal överförda paket inom tids perioden|Port, riktning|
|SynCount|Antal SYN|Count|Totalt|Totalt antal skickade SYN paket inom tids perioden|Port, riktning|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ApplicationRuleHit|Antal träffar för program regler|Count|Totalt|Antal gånger som program regler träffades|Status, orsak, protokoll|
|NetworkRuleHit|Antal träffar för nätverks regler|Count|Totalt|Antal gånger som nätverks regler träffades|Status, orsak, protokoll|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Totalt|Antal byte per sekund som Application Gateway har betjänat|Inga dimensioner|
|UnhealthyHostCount|Antal felaktiga värdar|Count|Average|Antal värdar för skadade Server delar|BackendSettingsPool|
|HealthyHostCount|Antal felfria värdar|Count|Average|Antal felfria Server dels värdar|BackendSettingsPool|
|TotalRequests|Totalt antal begäranden|Count|Totalt|Antal lyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|FailedRequests|Misslyckade förfrågningar|Count|Totalt|Antal misslyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|ResponseStatus|Svars status|Count|Totalt|Http-svarets status returnerades av Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuella anslutningar|Count|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inga dimensioner|
|CapacityUnits|Aktuella kapacitets enheter|Count|Average|Förbrukade kapacitets enheter|Inga dimensioner|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageBandwidth|Gatewayens S2S-bandbredd|BytesPerSecond|Average|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inga dimensioner|
|P2SBandwidth|Gateway P2S-bandbredd|BytesPerSecond|Average|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Antal P2S-anslutningar|Count|Maximal|Antal anslutningar punkt-till-plats för en gateway|Protocol|
|TunnelAverageBandwidth|Tunnel bandbredd|BytesPerSecond|Average|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Utgående byte i tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Ingångs byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Utgående tunnel paket|Count|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Inkommande tunnel paket|Count|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Ignorerade TS-matchnings paket för tunnel|Count|Totalt|Utgående paket-antal från trafik väljare matchnings fel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel om ingångs paket för TS-matchningsfel|Count|Totalt|Inkommande paket Drop Count från trafik väljarens matchnings fel i en tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|BITS inkommande Azure per sekund|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|BITS utgående Azure per sekund|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|BITS inkommande Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|BITS utgående Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|BITS inkommande Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|BITS utgående Azure per sekund|Inga dimensioner|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Returnerade frågor efter slut punkt|Count|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den aktuella tids ramen|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Slut punkts status efter slut punkt|Count|Maximal|1 om avsöknings status för en slut punkt är "aktive rad", annars 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Avsökningar misslyckades|Percent|Average|% av anslutnings övervaknings avsökningarna misslyckades|Inga dimensioner|
|AverageRoundtripMs|Genomsn. Svars tid för fördröjning (MS)|Millisekunder|Average|Genomsnittlig tid för nätverks fördröjning (MS) för anslutnings övervaknings avsökningar som skickas mellan källa och mål|Inga dimensioner|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RequestCount|Antal begäranden|Count|Totalt|Antalet klient förfrågningar som hanteras av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Begär ande storlek|Byte|Totalt|Antalet byte som har skickats som begär Anden från klienter till HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Svars storlek|Byte|Totalt|Antalet byte som skickats som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Antal Server dels begär Anden|Count|Totalt|Antalet förfrågningar som skickats från HTTP/S-proxyn till Server delar|HttpStatus, HttpStatusGroup, Server del|
|BackendRequestLatency|Svars tid för Server del|Millisekunder|Average|Tiden som beräknas från när begäran skickades av HTTP/S-proxyn till Server delen tills HTTP/S-proxyn fick den senaste svars byten från Server delen|Serverdel|
|TotalLatency|Total svars tid|Millisekunder|Average|Den tid som beräknas från när klientbegäran togs emot av HTTP/S-proxy tills klienten bekräftade den senaste svars byten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Server delens hälso procent|Percent|Average|Procent andelen lyckade hälso avsökningar från HTTP/S-proxyn till Server delar|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Antal begär Anden om webb programs brand vägg|Count|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|registrering. alla|Registreringsåtgärder|Count|Totalt|Totalt antal genomförda registreringsåtgärder (skapa, uppdatera, fråga och ta bort). |Inga dimensioner|
|registration.create|Registreringsskaparåtgärder|Count|Totalt|Totalt antal registreringar som skapats.|Inga dimensioner|
|registration.update|Registreringsuppdateringsåtgärder|Count|Totalt|Totalt antal genomförda registreringsuppdateringar.|Inga dimensioner|
|registration.get|Registreringsläsningsåtgärder|Count|Totalt|Totalt antal utförda registreringsfrågor.|Inga dimensioner|
|registration.delete|Registreringsborttagningsåtgärder|Count|Totalt|Totalt antal registreringar som tagits bort.|Inga dimensioner|
|inkommande|Inkommande meddelanden|Count|Totalt|Totalt antal API-anrop som skickats. |Inga dimensioner|
|incoming.scheduled|Schemalagt push-meddelande har skickats|Count|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|inkommande. schemalagd. Avbryt|Schemalagda push-meddelanden har avbrutits|Count|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|schemalagt. väntar|Schemalagda meddelanden som väntar|Count|Totalt|Schemalagda meddelanden som väntar|Inga dimensioner|
|installation.all|Installationshanteringsåtgärder|Count|Totalt|Installationshanteringsåtgärder|Inga dimensioner|
|installation.get|Åtgärder för att hämta installation|Count|Totalt|Åtgärder för att hämta installation|Inga dimensioner|
|installation.upsert|Skapa eller uppdatera installationsåtgärder|Count|Totalt|Skapa eller uppdatera installationsåtgärder|Inga dimensioner|
|installation.patch|Åtgärder för att korrigera installation|Count|Totalt|Åtgärder för att korrigera installation|Inga dimensioner|
|installation.delete|Åtgärder för att ta bort installation|Count|Totalt|Åtgärder för att ta bort installation|Inga dimensioner|
|outgoing.allpns.success|Levererade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.allpns.invalidpayload|Nyttolastfel|Count|Totalt|Antal push-meddelanden som inte levererades eftersom PNS returnerade ett nyttolastfel.|Inga dimensioner|
|outgoing.allpns.pnserror|Externa meddelandesystemfel|Count|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med PNS (inte autentiseringsproblem).|Inga dimensioner|
|outgoing.allpns.channelerror|Kanalfel|Count|Totalt|Antal push-meddelanden som inte levererades på grund av att kanalen var ogiltig, inte kopplad till rätt app, begränsad eller hade upphört att gälla.|Inga dimensioner|
|outgoing.allpns.badorexpiredchannel|Felaktig eller utgången kanal|Count|Totalt|Antal push-meddelanden som inte levererades på grund av utgången eller ogiltig kanal/token/registrationId i registreringen.|Inga dimensioner|
|utgående. WNS. lyckades|WNS – lyckade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.wns.invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Count|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade. (Windows Live känner inte igen autentiseringsuppgifterna).|Inga dimensioner|
|outgoing.wns.badchannel|WNS – felaktig kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS status: 404 hittades inte).|Inga dimensioner|
|outgoing.wns.expiredchannel|WNS – utgången kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 borta).|Inga dimensioner|
|utgående. WNS. begränsad|WNS – begränsade meddelanden|Count|Totalt|Antal push-meddelanden som misslyckades eftersom WNS begränsar den här appen (WNS status: 406 är inte acceptabelt).|Inga dimensioner|
|outgoing.wns.tokenproviderunreachable|WNS-auktoriseringsfel (kan inte nås)|Count|Totalt|Windows Live kan inte nås.|Inga dimensioner|
|outgoing.wns.invalidtoken|WNS-auktoriseringsfel (ogiltig token)|Count|Totalt|Den token som angetts för WNS är inte giltig (WNS-status: 401 ej behörig).|Inga dimensioner|
|outgoing.wns.wrongtoken|WNS-auktoriseringsfel (fel token)|Count|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS-status: 403 förbjuden). Detta kan inträffa om ChannelURI i registreringen är kopplad till en annan app. Kontrol lera att klient programmet är associerat med samma app vars autentiseringsuppgifter finns i Notification Hub.|Inga dimensioner|
|outgoing.wns.invalidnotificationformat|WNS – ogiltigt meddelandeformat|Count|Totalt|Meddelandets format är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nytto laster.|Inga dimensioner|
|utgående. WNS. invalidnotificationsize|WNS – ogiltig meddelandestorlek|Count|Totalt|Meddelande nytto lasten är för stor (WNS-status: 413).|Inga dimensioner|
|outgoing.wns.channelthrottled|WNS – begränsad kanal|Count|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-NotificationStatus:channelThrottled).|Inga dimensioner|
|outgoing.wns.channeldisconnected|WNS – frånkopplad kanal|Count|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-DeviceConnectionStatus: frånkopplad).|Inga dimensioner|
|outgoing.wns.dropped|WNS – utelämnade meddelanden|Count|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: utelämnat men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|outgoing.wns.pnserror|WNS-fel|Count|Totalt|Meddelandet levererades inte på grund av kommunikationsproblem med WNS.|Inga dimensioner|
|utgående. WNS. authenticationerror|WNS-auktoriseringsfel|Count|Totalt|Meddelanden som inte levererats på grund av kommunikationsproblem med ogiltiga Windows Live-autentiseringsuppgifter eller fel token.|Inga dimensioner|
|outgoing.apns.success|APNS – lyckade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.apns.invalidcredentials|APNs-auktoriseringsfel|Count|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.apns.badchannel|APNS – felaktig kanal|Count|Totalt|Antal push-meddelanden som misslyckades på grund av att token är ogiltig (APN binär protokoll status kod: 8. Status kod för APNS HTTP-protokoll: 400 med "BadDeviceToken").|Inga dimensioner|
|outgoing.apns.expiredchannel|APNS – utgången kanal|Count|Totalt|Antal token som ogiltigförklarades av APNs-feedbackkanalen.|Inga dimensioner|
|outgoing.apns.invalidnotificationsize|APNS – ogiltig meddelandestorlek|Count|Totalt|Antal push-meddelanden som inte kunde utföras eftersom nytto lasten var för stor (APN Binary-protokoll status kod: 7).|Inga dimensioner|
|outgoing.apns.pnserror|APNS-fel|Count|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med APNs.|Inga dimensioner|
|utgående. GCM. lyckades|GCM – lyckade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.gcm.invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Count|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.gcm.badchannel|GCM – felaktig kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM-resultat: Ogiltig registrering).|Inga dimensioner|
|outgoing.gcm.expiredchannel|GCM – utgången kanal|Count|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|utgående. GCM. begränsad|GCM – begränsade meddelanden|Count|Totalt|Antal push-meddelanden som misslyckades eftersom GCM begränsade till den här appen (GCM-status kod: 501-599 eller resultat: inte tillgängligt).|Inga dimensioner|
|outgoing.gcm.invalidnotificationformat|GCM – ogiltigt meddelandeformat|Count|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten inte formaterades korrekt (GCM resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|utgående. GCM. invalidnotificationsize|GCM – ogiltig meddelandestorlek|Count|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (GCM-resultat: MessageTooBig).|Inga dimensioner|
|outgoing.gcm.wrongchannel|GCM – fel kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte är kopplad till den aktuella appen (GCM-resultat: InvalidPackageName).|Inga dimensioner|
|outgoing.gcm.pnserror|GCM-fel|Count|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med GCM.|Inga dimensioner|
|utgående. GCM. authenticationerror|GCM-autentiseringsfel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna som autentiseringsuppgifterna är blockerade eller om SenderId inte har kon figurer ATS korrekt i appen (GCM-resultat: MismatchedSenderId).|Inga dimensioner|
|outgoing.mpns.success|MPNS – lyckade meddelanden|Count|Totalt|Totalt antal levererade meddelanden.|Inga dimensioner|
|outgoing.mpns.invalidcredentials|MPNS – ogiltiga autentiseringsuppgifter|Count|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|outgoing.mpns.badchannel|MPNS – felaktig kanal|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS status: 404 hittades inte).|Inga dimensioner|
|outgoing.mpns.throttled|MPNS – begränsade meddelanden|Count|Totalt|Antal push-meddelanden som misslyckades eftersom MPNS begränsar den här appen (WNS MPNS: 406 är inte acceptabelt).|Inga dimensioner|
|outgoing.mpns.invalidnotificationformat|MPNS – ogiltigt meddelandeformat|Count|Totalt|Antal push-meddelanden som inte levererades eftersom nyttolasten för meddelanden var för stor.|Inga dimensioner|
|outgoing.mpns.channeldisconnected|MPNS – frånkopplad kanal|Count|Totalt|Antal push-meddelanden som misslyckades på grund av att ChannelURI i registreringen var frånkopplad (MPNS status: 412 hittades inte).|Inga dimensioner|
|outgoing.mpns.dropped|MPNS – utelämnade meddelanden|Count|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svars huvud: X-NotificationStatus: QueueFull eller undertryckt).|Inga dimensioner|
|outgoing.mpns.pnserror|MPNS-fel|Count|Totalt|Antal push-meddelanden som inte levererades på grund av kommunikationsproblem med MPNS.|Inga dimensioner|
|outgoing.mpns.authenticationerror|MPNS-autentiseringsfel|Count|Totalt|Antal push-meddelanden som inte levererades eftersom PNS inte godkände de angivna autentiseringsuppgifterna eller på grund av att autentiseringsuppgifterna är blockerade.|Inga dimensioner|
|notificationhub. push-meddelanden|Alla utgående meddelanden|Count|Totalt|Alla utgående meddelanden i meddelandehubben|Inga dimensioner|
|incoming.all.requests|Alla inkommande förfrågningar|Count|Totalt|Totalt antal inkommande förfrågningar för en meddelandehubb|Inga dimensioner|
|incoming.all.failedrequests|Alla inkommande misslyckade förfrågningar|Count|Totalt|Totalt antal inkommande misslyckade förfrågningar för en meddelandehubb|Inga dimensioner|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_% kostnads fri noder i procent|Kostnads fri noder i procent|Count|Average|Average_% kostnads fri noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% ledigt utrymme|Ledigt utrymme i procent|Count|Average|Average_% ledigt utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använda noder i procent|% Använda noder i procent|Count|Average|Average_% använda noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt utrymme|Använt utrymme i procent|Count|Average|Average_% använt utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/s|Disk – lästa byte/sek|Count|Average|Average_Disk lästa byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Diskläsningar/sek|Count|Average|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Disköverföringar/sek|Count|Average|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/s|Disk – skrivna byte/sek|Count|Average|Average_Disk skrivna byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Diskskrivningar/sek|Count|Average|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Ledigt utrymme i MB|Count|Average|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk-byte/s|Logisk Disk byte/sek|Count|Average|Average_Logical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt minne|Tillgängligt minne i procent|Count|Average|Average_% tillgängligt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt växlings utrymme|Tillgängligt växlings utrymme i procent|Count|Average|Average_% tillgängligt växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt minne|Använt minne i procent|Count|Average|Average_% använt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt växlings utrymme|Använt växlings utrymme i procent|Count|Average|Average_% använt växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB minne|Tillgängligt minne i megabyte|Count|Average|Average_Available MB minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte växling|Tillgängliga megabyte växlings utrymme|Count|Average|Average_Available megabyte växling|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page läsningar/s|Sid läsningar/s|Count|Average|Average_Page läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page skrivningar/SEK|Sid skrivningar/SEK|Count|Average|Average_Page skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per sekund|Sidor/s|Count|Average|Average_Pages per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlings utrymme|Använt megabyte växlings utrymme|Count|Average|Average_Used megabyte växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i MB|Använt minne i MB|Count|Average|Average_Used minne i MB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte som skickats|Totalt antal överförda byte|Count|Average|Average_Total byte som skickats|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte mottagna|Totalt antal byte mottaget|Count|Average|Average_Total byte mottagna|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Totalt antal byte|Count|Average|Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Överförda Average_Total-paket|Totalt antal överförda paket|Count|Average|Överförda Average_Total-paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Mottagna Average_Total-paket|Totalt antal mottagna paket|Count|Average|Mottagna Average_Total-paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total RX-fel|Totalt antal mottagna mottagnings fel|Count|Average|Average_Total RX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-fel|Totalt antal TX-fel|Count|Average|Average_Total TX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Totalt antal kollisioner|Count|Average|Average_Total kollisioner|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskläsning|Genomsn. S/diskläsning|Count|Average|Average_Avg. S/diskläsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/disköverföring|Genomsn. S/disköverföring|Count|Average|Average_Avg. S/disköverföring|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskskrivning|Genomsn. S/diskskrivning|Count|Average|Average_Avg. S/diskskrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk-byte/s|Fysisk disk-byte/SEK|Count|Average|Average_Physical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Privilegie rad tid för Average_Pct|PCT privilegie rad tid|Count|Average|Privilegie rad tid för Average_Pct|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användar tid|PCT-användar tid|Count|Average|Average_Pct användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i KB|Använt minne i KB|Count|Average|Average_Used minne i KB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Virtuellt delat minne|Count|Average|Average_Virtual delat minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tid|DPC-tid i procent|Count|Average|Average_% DPC-tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledig tid i procent|Ledig tid i procent|Count|Average|Average_ ledig tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% avbrotts tid|% Avbrotts tid|Count|Average|Average_% avbrotts tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ i/o-vänte tid|% I/o-vänte tid|Count|Average|Average_ i/o-vänte tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% fin tid|% Trevligt tid|Count|Average|Average_% fin tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% privilegie rad tid|Privilegie rad tid i procent|Count|Average|Average_% privilegie rad tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|Tid i procent för processor|Count|Average|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% användar tid|Användar tid i procent|Count|Average|Average_% användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Ledigt fysiskt minne|Count|Average|Average_Free fysiskt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Ledigt utrymme i växlingsfiler|Count|Average|Average_Free utrymme i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Ledigt virtuellt minne|Count|Average|Average_Free virtuellt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processer|Count|Average|Average_Processes|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagras i växlingsfiler|Storlek lagrad i växlingsfiler|Count|Average|Average_Size lagras i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Drifttid|Count|Average|Average_Uptime|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Användare|Count|Average|Average_Users|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskläsning|Genomsn. S/diskläsning|Count|Average|Average_Avg. S/diskläsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskskrivning|Genomsn. S/diskskrivning|Count|Average|Average_Avg. S/diskskrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current-diskkölängd|Aktuell diskkölängd|Count|Average|Average_Current-diskkölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Diskläsningar/sek|Count|Average|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Disköverföringar/sek|Count|Average|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Diskskrivningar/sek|Count|Average|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Ledigt utrymme i MB|Count|Average|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% ledigt utrymme|Ledigt utrymme i procent|Count|Average|Average_% ledigt utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Tillgängliga megabyte|Count|Average|Average_Available MB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% allokerade byte som används|% Allokerade byte som används|Count|Average|Average_% allokerade byte som används|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Mottagna Average_Bytes per sekund|Mottagna byte/sek|Count|Average|Mottagna Average_Bytes per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Bytes per sekund|Skickade byte/sek|Count|Average|Skickade Average_Bytes per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/SEK|Totalt antal byte/s|Count|Average|Average_Bytes totalt/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|Tid i procent för processor|Count|Average|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Längd på Average_Processor-kön|Kölängd för processor|Count|Average|Längd på Average_Processor-kön|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pulsslag|Pulsslag|Count|Totalt|Pulsslag|Computer, OSType, Version, SourceComputerId|
|Uppdatera|Uppdatera|Count|Average|Uppdatera|Dator, produkt, klassificering, UpdateState, valfri, godkänd|
|Händelse|Händelse|Count|Average|Händelse|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryDuration|Frågans varaktighet|Millisekunder|Average|DAX-frågans varaktighet i det sista intervallet|Inga dimensioner|
|QueryPoolJobQueueLength|Konversation Kölängd för jobbkö|Count|Average|Antal jobb i kön för trådpoolen för Query.|Inga dimensioner|
|qpu_high_utilization_metric|QPU hög användning|Count|Totalt|QPU hög användning under den senaste minuten, 1 för hög QPU användning, annars 0|Inga dimensioner|
|memory_metric|Minne|Byte|Average|Minnesoptimerade. Intervallet 0-3 GB för a1, 0-5 GB för a2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Nedskräpning för minne|Percent|Average|Genomsnittligt minne nedskräpning.|Inga dimensioner|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Totalt|Lyckade ListenerConnections för Microsoft.Relay.|EntityName|
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
|SearchLatency|Sök svars tid|Sekunder|Average|Genomsnittlig Sök fördröjning för Sök tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Sökfrågor per sekund|CountPerSecond|Average|Sök efter frågor per sekund för Sök tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Begränsade sökfrågor i procent|Percent|Average|Procent andel Sök frågor som har begränsats för Sök tjänsten|Inga dimensioner|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade förfrågningar (för hands version)|Count|Totalt|Totalt antal slutförda begär Anden för en namnrymd (förhands granskning)|EntityName|
|ServerErrors|Serverfel. (Förhandsgranskning)|Count|Totalt|Serverfel för Microsoft.ServiceBus. (Förhandsgranskning)|EntityName|
|UserErrors|Användarfel. (Förhandsgranskning)|Count|Totalt|Användarfel för Microsoft.ServiceBus. (Förhandsgranskning)|EntityName|
|ThrottledRequests|Begränsade begäranden. (Förhandsgranskning)|Count|Totalt|Begränsade begäranden för Microsoft.ServiceBus. (Förhandsgranskning)|EntityName|
|IncomingRequests|Inkommande förfrågningar (för hands version)|Count|Totalt|Inkommande begäranden för Microsoft.ServiceBus. (Förhandsgranskning)|EntityName|
|IncomingMessages|Inkommande meddelanden (förhands granskning)|Count|Totalt|Inkommande meddelanden för Microsoft.ServiceBus. (Förhandsgranskning)|EntityName|
|OutgoingMessages|Utgående meddelanden (förhands granskning)|Count|Totalt|Utgående meddelanden för Microsoft.ServiceBus. (Förhandsgranskning)|EntityName|
|ActiveConnections|ActiveConnections (för hands version)|Count|Totalt|Totalt antal aktiva anslutningar för Microsoft.ServiceBus. (Förhandsgranskning)|Inga dimensioner|
|Size|Storlek (förhands granskning)|Byte|Average|Storlek på kö/ämne i byte. (Förhandsgranskning)|EntityName|
|Meddelanden|Antal meddelanden i kö/ämne. (Förhandsgranskning)|Count|Average|Antal meddelanden i kö/ämne. (Förhandsgranskning)|EntityName|
|ActiveMessages|Antal aktiva meddelanden i kö/ämne. (Förhandsgranskning)|Count|Average|Antal aktiva meddelanden i kö/ämne. (Förhandsgranskning)|EntityName|
|DeadletteredMessages|Antal obeställbara meddelanden i kö/ämne. (Förhandsgranskning)|Count|Average|Antal obeställbara meddelanden i kö/ämne. (Förhandsgranskning)|EntityName|
|ScheduledMessages|Antal schemalagda meddelanden i kö/ämne. (Förhandsgranskning)|Count|Average|Antal schemalagda meddelanden i kö/ämne. (Förhandsgranskning)|EntityName|
|CPUXNS|Processoranvändning per namnområde|Percent|Maximal|CPU-användningsmått för Service Bus Premium-namnområde|Inga dimensioner|
|WSXNS|Minnesstorleksanvändning per namnområde|Percent|Maximal|Minnesanvändningsmått för Service Bus Premium-namnområde|Inga dimensioner|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|CPU allokerad till den här behållaren i millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Byte|Average|Minne som allokerats till den här behållaren i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Faktisk processor användning i millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Byte|Average|Faktisk minnes användning i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Average|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Average|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Status för Service Fabric nätprogram|ApplicationName, status|
|ServiceStatus|ServiceStatus|Count|Average|Hälso status för en tjänst i Service Fabric nätprogram|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Hälso status för en tjänst replik i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName|
|Container status|Container status|Count|Average|Status för behållaren i Service Fabric nätprogram|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Count|Average|Antal omstarter för en behållare i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ConnectionCount|Antal anslutningar|Count|Maximal|Mängden användar anslutning.|Slutpunkt|
|MessageCount|Antal meddelanden|Count|Totalt|Den totala mängden meddelanden.|Inga dimensioner|
|InboundTraffic|Inkommande trafik|Byte|Totalt|Inkommande trafik trafik för tjänsten|Inga dimensioner|
|OutboundTraffic|Utgående trafik|Byte|Totalt|Utgående trafik för tjänsten|Inga dimensioner|
|UserErrors|Användar fel|Percent|Maximal|Procent andel användar fel|Inga dimensioner|
|SystemErrors|Systemfel|Percent|Maximal|Procent andelen system fel|Inga dimensioner|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|Processorprocent|Percent|Average|Processorprocent|Inga dimensioner|
|physical_data_read_percent|Data I/O-procent|Percent|Average|Data I/O-procent|Inga dimensioner|
|log_write_percent|Logg I/O-procent|Percent|Average|Logg I/O-procent|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Percent|Average|DTU-procent|Inga dimensioner|
|lagring|Använt datautrymme|Byte|Maximal|Total databas storlek|Inga dimensioner|
|connection_successful|Lyckade anslutningar|Count|Totalt|Lyckade anslutningar|Inga dimensioner|
|connection_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga dimensioner|
|blocked_by_firewall|Blockerad av brand väggen|Count|Totalt|Blockerad av brand väggen|Inga dimensioner|
|hamn|Dödlägen|Count|Totalt|Dödlägen|Inga dimensioner|
|storage_percent|Använt data utrymme i procent|Percent|Maximal|Databasstorlek i procent|Inga dimensioner|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Percent|Average|Minnes intern OLTP-lagring i procent|Inga dimensioner|
|workers_percent|Arbetarprocent|Percent|Average|Arbetarprocent|Inga dimensioner|
|sessions_percent|Sessionsprocent|Percent|Average|Sessionsprocent|Inga dimensioner|
|dtu_limit|DTU-gräns|Count|Average|DTU-gräns|Inga dimensioner|
|dtu_used|Använt DTU|Count|Average|Använt DTU|Inga dimensioner|
|cpu_limit|PROCESSOR gräns|Count|Average|PROCESSOR gräns|Inga dimensioner|
|cpu_used|Använd CPU|Count|Average|Använd CPU|Inga dimensioner|
|dwu_limit|DWU-gräns|Count|Maximal|DWU-gräns|Inga dimensioner|
|dwu_consumption_percent|DWU procent|Percent|Maximal|DWU procent|Inga dimensioner|
|dwu_used|DWU som används|Count|Maximal|DWU som används|Inga dimensioner|
|dw_cpu_percent|DW CPU-procent för nodenivå|Percent|Average|DW CPU-procent för nodenivå|DwLogicalNodeId|
|dw_physical_data_read_percent|Data IO-procent för DW Node Level|Percent|Average|Data IO-procent för DW Node Level|DwLogicalNodeId|
    |cache_hit_percent|Procent andel cacheträffar|Percent|Maximal|Procent andel cacheträffar|Inga dimensioner|
|cache_used_percent|Procent andel som används|Percent|Maximal|Procent andel som används|Inga dimensioner|
|local_tempdb_usage_percent|Lokal tempdb-procent|Percent|Average|Lokal tempdb-procent|Inga dimensioner|
|app_cpu_billed|App CPU debiterad|Count|Totalt|App CPU debiterad|Inga dimensioner|
|app_cpu_percent|CPU-procent för app|Percent|Average|CPU-procent för app|Inga dimensioner|
|app_memory_percent|Procent andel använt program minne|Percent|Average|Procent andel använt program minne|Inga dimensioner|
|allocated_data_storage|Allokerat datautrymme|Byte|Average|Allokerat datautrymme|Inga dimensioner|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|Processorprocent|Percent|Average|Processorprocent|Inga dimensioner|
|physical_data_read_percent|Data I/O-procent|Percent|Average|Data I/O-procent|Inga dimensioner|
|log_write_percent|Logg I/O-procent|Percent|Average|Logg I/O-procent|Inga dimensioner|
|dtu_consumption_percent|DTU-procent|Percent|Average|DTU-procent|Inga dimensioner|
|storage_percent|Använt data utrymme i procent||Percent|Average|Lagrings procent|Inga dimensioner|
|workers_percent|Arbetarprocent|Percent|Average|Arbetarprocent|Inga dimensioner|
|sessions_percent|Sessionsprocent|Percent|Average|Sessionsprocent|Inga dimensioner|
|eDTU_limit|eDTU-gräns|Count|Average|eDTU-gräns|Inga dimensioner|
|storage_limit|Maximal datastorlek|Byte|Average|Lagrings gräns|Inga dimensioner|
|eDTU_used|eDTU använt|Count|Average|eDTU använt|Inga dimensioner|
|storage_used|Använt datautrymme|Byte|Average|Använt lagringsutrymme|Inga dimensioner|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Percent|Average|Minnes intern OLTP-lagring i procent|Inga dimensioner|
|cpu_limit|PROCESSOR gräns|Count|Average|PROCESSOR gräns|Inga dimensioner|
|cpu_used|Använd CPU|Count|Average|Använd CPU|Inga dimensioner|
|allocated_data_storage|Allokerat datautrymme|Byte|Average|Allokerat datautrymme|Inga dimensioner|
|allocated_data_storage_percent|Allokerat data utrymme i procent|Percent|Maximal|Allokerat data utrymme i procent|Inga dimensioner|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|virtual_core_count|Antal virtuella kärnor|Count|Average|Antal virtuella kärnor|Inga dimensioner|
|avg_cpu_percent|Genomsnittlig CPU-procent|Percent|Average|Genomsnittlig CPU-procent|Inga dimensioner|
|reserved_storage_mb|Reserverat lagrings utrymme|Count|Average|Reserverat lagrings utrymme|Inga dimensioner|
|storage_space_used_mb|Använt lagrings utrymme|Count|Average|Använt lagrings utrymme|Inga dimensioner|
|io_requests|Antal IO-begäranden|Count|Average|Antal IO-begäranden|Inga dimensioner|
|io_bytes_read|Lästa IO-byte|Byte|Average|Lästa IO-byte|Inga dimensioner|
|io_bytes_written|Skrivna IO-byte|Byte|Average|Skrivna IO-byte|Inga dimensioner|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Medel|Kapacitet som används av konto|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|Blobkapacitet|Byte|Average|Mängden lagring som används av lagringskontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|Antalet blobar|Antal|Totalt|Antalet blobar i lagringskontots Blob Service.|BlobType|       |BlobCount|Antalet blobar|Count|Average|Antalet blobar i lagringskontots Blob Service.|BlobType,-nivå|
|ContainerCount|Antal blobcontainrar|Antal|Medel|Antalet containrar i lagringskontots Blob Service.|Inga dimensioner|
|IndexCapacity|Indexkapacitet|Byte|Average|Mängden lagring som används av ADLS Gen2-indexet (hierarkiskt) i byte.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Filkapacitet|Byte|Average|Mängden lagring som används av lagringskontots filtjänst i byte.|Inga dimensioner|
|FileCount|Antal filer|Count|Average|Antalet filer i lagringskontots filtjänst.|Inga dimensioner|
|FileShareCount|Antal filresurser|Count|Average|Antalet filresurser i lagringskontots filtjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kökapacitet|Byte|Medel|Mängden lagring som används av lagringskontots kötjänst i byte.|Inga dimensioner|
|QueueCount|Antal köer|Antal|Medel|Antalet köer i lagringskontots kötjänst.|Inga dimensioner|
|QueueMessageCount|Antal kömeddelanden|Antal|Medel|Ungefärligt antal kömeddelanden i lagringskontots kötjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabellkapacitet|Byte|Medel|Mängden lagring som används av lagringskontots tabelltjänst i byte.|Inga dimensioner|
|TableCount|Antal tabeller|Antal|Medel|Antalet tabeller i lagringskontots tabelltjänst.|Inga dimensioner|
|TableEntityCount|Antal tabellentiteter|Antal|Medel|Antalet tabellentiteter i lagringskontots tabelltjänst.|Inga dimensioner|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Egress|Egress|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultat av Sync-session|Count|Average|Mått som loggar värdet 1 varje gång Server slut punkten slutför en Sync-session med moln slut punkten|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synkroniserade filer|Count|Totalt|Antal filer som har synkroniserats|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Count|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Serverns onlinestatus|Count|Maximal|Mått som loggar värdet 1 varje gång den registrerade servern registrerar ett pulsslag med moln slut punkten|Servernamn|
|StorageSyncRecallIOTotalSizeBytes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|Servernamn|

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
|IngressReceivedMessages|Ingress mottagna meddelanden|Count|Totalt|Antal meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Count|Totalt|Antal ogiltiga meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från alla händelse källor|Inga dimensioner|
|IngressStoredBytes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Ingress lagrade händelser|Count|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Count|Average|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Count|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Count|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress mottagna meddelanden|Count|Totalt|Antal meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Count|Totalt|Antal ogiltiga meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från händelse källan|Inga dimensioner|
|IngressStoredBytes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Ingress lagrade händelser|Count|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Count|Average|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Count|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Count|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Lästa byte på disk/sek|BytesPerSecond|Average|Genomsnittligt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|DiskWriteBytesPerSecond|Skrivna byte på disk/sek|BytesPerSecond|Average|Genomsnittligt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|Skrivna byte på disk|Skrivna byte på disk|Byte|Totalt|Totalt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|DiskReadOperations|Disk Läs åtgärder|Count|Totalt|Antalet Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskWriteOperations|Disk skrivnings åtgärder|Count|Totalt|Antalet i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|Läsåtgärder på disk/sek|Läsåtgärder på disk/sek|CountPerSecond|Average|Genomsnittligt antal Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|Skrivåtgärder på disk/sek|Skrivåtgärder på disk/sek|CountPerSecond|Average|Genomsnittligt antal i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskReadLatency|Läs fördröjning för disk|Millisekunder|Average|Total Läs fördröjning. Summan av Läs fördröjningen för enheten och kärnan.|Inga dimensioner|
|DiskWriteLatency|Skriv fördröjning för disk|Millisekunder|Average|Total Skriv fördröjning. Summan av Skriv fördröjningarna för enheten och kärnan.|Inga dimensioner|
|NetworkInBytesPerSecond|Nätverk i byte/s|BytesPerSecond|Average|Genomsnittligt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|NetworkOutBytesPerSecond|Nätverks-out-byte/SEK|BytesPerSecond|Average|Genomsnittligt nätverks data flöde för överförd trafik.|Inga dimensioner|
|Nätverk in|Nätverk in|Byte|Totalt|Totalt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|Nätverk ut|Nätverk ut|Byte|Totalt|Totalt nätverks data flöde för överförd trafik.|Inga dimensioner|
|MemoryUsed|Använt minne|Byte|Average|Mängden dator minne som används av den virtuella datorn.|Inga dimensioner|
|MemoryGranted|Beviljat minne|Byte|Average|Mängden minne som har beviljats för den virtuella datorn av värden. Minnet beviljas inte för värden förrän den är påslagen en gång och beviljat minne kan växlas ut eller visas borta om VMkernel behöver minnet.|Inga dimensioner|
|MemoryActive|Aktivt minne|Byte|Average|Mängden minne som används av den virtuella datorn i det tidigare lilla tids fönstret. Detta är "true"-antalet hur mycket minne som den virtuella datorn för närvarande behöver. Ytterligare, oanvänt minne kan växlas ut eller visas utan att påverka gästens prestanda.|Inga dimensioner|
|Procent CPU|Procent CPU|Procent|Average|PROCESSOR användningen. Det här värdet rapporteras med 100% som representerar alla processor kärnor i systemet. Till exempel är en 2-vägs virtuell dator som använder 50% av ett system med fyra kärnor fullständigt med två kärnor.|Inga dimensioner|
|PercentageCpuReady|Procent andel CPU klar|Millisekunder|Totalt|Ready Time är den tid som väntar på att CPU: er ska bli tillgängliga under det tidigare uppdaterings intervallet.|Inga dimensioner|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Percent|Average|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Percent|Average|Minnesprocent|Instans|
|DiskQueueLength|Diskkölängd|Count|Average|Diskkölängd|Instans|
|HttpQueueLength|Http-kölängd|Count|Average|Http-kölängd|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (exklusive funktioner)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Count|Totalt|Begäranden|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Count|Totalt|Http 101|Instans|
|Http2xx|HTTP 2xx|Count|Totalt|HTTP 2xx|Instans|
|Http3xx|HTTP 3xx|Count|Totalt|HTTP 3xx|Instans|
|Http401|HTTP 401|Count|Totalt|HTTP 401|Instans|
|Http403|Http 403|Count|Totalt|Http 403|Instans|
|Http404|Http 404|Count|Totalt|Http 404|Instans|
|Http406|Http 406|Count|Totalt|Http 406|Instans|
|Http4xx|Http 4xx|Count|Totalt|Http 4xx|Instans|
|Http5xx|Http-serverfel|Count|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Average|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Average|Genomsnittligt arbetsminne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Average|Genomsnittlig svarstid|Instans|
|AppConnections|Anslutningar|Count|Average|Anslutningar|Instans|
|Handtag|Antal referenser|Count|Average|Antal referenser|Instans|
|Trådar|Antal trådar|Count|Average|Antal trådar|Instans|
|PrivateBytes|Privata byte|Byte|Average|Privata byte|Instans|
|IoReadBytesPerSecond|I/O lästa byte per sekund|BytesPerSecond|Totalt|I/O lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|I/O skrivna byte per sekund|BytesPerSecond|Totalt|I/O skrivna byte per sekund|Instans|
|IoOtherBytesPerSecond|I/O övrigt byte per sekund|BytesPerSecond|Totalt|I/O övrigt byte per sekund|Instans|
|IoReadOperationsPerSecond|I/O-läsningsåtgärder per sekund|BytesPerSecond|Totalt|I/O-läsningsåtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/O-skrivåtgärder per sekund|BytesPerSecond|Totalt|I/O-skrivåtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|I/O övriga åtgärder per sekund|BytesPerSecond|Totalt|I/O övriga åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Förfrågningar i programkö|Count|Average|Förfrågningar i programkö|Instans|
|CurrentAssemblies|Aktuellt antal sammansättningar|Count|Average|Aktuellt antal sammansättningar|Instans|
|TotalAppDomains|Totalt antal appdomäner|Count|Average|Totalt antal appdomäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal appdomäner som inte lästs in|Count|Average|Totalt antal appdomäner som inte lästs in|Instans|
|Gen0Collections|Generation 0-skräpinsamlingar|Count|Totalt|Generation 0-skräpinsamlingar|Instans|
|Gen1Collections|Generation 1-skräpinsamlingar|Count|Totalt|Generation 1-skräpinsamlingar|Instans|
|Gen2Collections|Generation 2-skräpinsamlingar|Count|Totalt|Generation 2-skräpinsamlingar|Instans|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (Functions)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http5xx|Http-serverfel|Count|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Average|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Average|Genomsnittligt arbetsminne|Instans|
|FunctionExecutionUnits|Funktionskörningsenheter|MB/millisekunder|Totalt|[Funktions körnings enheter](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Count|Totalt|Funktionen Antal körningar|Instans|
|PrivateBytes|Privata byte|Byte|Average|Privata byte|Instans|
|IoReadBytesPerSecond|I/O lästa byte per sekund|BytesPerSecond|Totalt|I/O lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|I/O skrivna byte per sekund|BytesPerSecond|Totalt|I/O skrivna byte per sekund|Instans|
|IoOtherBytesPerSecond|I/O övrigt byte per sekund|BytesPerSecond|Totalt|I/O övrigt byte per sekund|Instans|
|IoReadOperationsPerSecond|I/O-läsningsåtgärder per sekund|BytesPerSecond|Totalt|I/O-läsningsåtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/O-skrivåtgärder per sekund|BytesPerSecond|Totalt|I/O-skrivåtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|I/O övriga åtgärder per sekund|BytesPerSecond|Totalt|I/O övriga åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Förfrågningar i programkö|Count|Average|Förfrågningar i programkö|Instans|
|CurrentAssemblies|Aktuellt antal sammansättningar|Count|Average|Aktuellt antal sammansättningar|Instans|
|TotalAppDomains|Totalt antal appdomäner|Count|Average|Totalt antal appdomäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal appdomäner som inte lästs in|Count|Average|Totalt antal appdomäner som inte lästs in|Instans|
|Gen0Collections|Generation 0-skräpinsamlingar|Count|Totalt|Generation 0-skräpinsamlingar|Instans|
|Gen1Collections|Generation 1-skräpinsamlingar|Count|Totalt|Generation 1-skräpinsamlingar|Instans|
|Gen2Collections|Generation 2-skräpinsamlingar|Count|Totalt|Generation 2-skräpinsamlingar|Instans|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Count|Totalt|Begäranden|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Count|Totalt|Http 101|Instans|
|Http2xx|HTTP 2xx|Count|Totalt|HTTP 2xx|Instans|
|Http3xx|HTTP 3xx|Count|Totalt|HTTP 3xx|Instans|
|Http401|HTTP 401|Count|Totalt|HTTP 401|Instans|
|Http403|Http 403|Count|Totalt|Http 403|Instans|
|Http404|Http 404|Count|Totalt|Http 404|Instans|
|Http406|Http 406|Count|Totalt|Http 406|Instans|
|Http4xx|Http 4xx|Count|Totalt|Http 4xx|Instans|
|Http5xx|Http-serverfel|Count|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Average|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Average|Genomsnittligt arbetsminne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Average|Genomsnittlig svarstid|Instans|
|FunctionExecutionUnits|Funktionskörningsenheter|Count|Totalt|Funktionskörningsenheter|Instans|
|FunctionExecutionCount|Funktionen Antal körningar|Count|Totalt|Funktionen Antal körningar|Instans|
|AppConnections|Anslutningar|Count|Average|Anslutningar|Instans|
|Handtag|Antal referenser|Count|Average|Antal referenser|Instans|
|Trådar|Antal trådar|Count|Average|Antal trådar|Instans|
|PrivateBytes|Privata byte|Byte|Average|Privata byte|Instans|
|IoReadBytesPerSecond|I/O lästa byte per sekund|BytesPerSecond|Totalt|I/O lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|I/O skrivna byte per sekund|BytesPerSecond|Totalt|I/O skrivna byte per sekund|Instans|
|IoOtherBytesPerSecond|I/O övrigt byte per sekund|BytesPerSecond|Totalt|I/O övrigt byte per sekund|Instans|
|IoReadOperationsPerSecond|I/O-läsningsåtgärder per sekund|BytesPerSecond|Totalt|I/O-läsningsåtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/O-skrivåtgärder per sekund|BytesPerSecond|Totalt|I/O-skrivåtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|I/O övriga åtgärder per sekund|BytesPerSecond|Totalt|I/O övriga åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Förfrågningar i programkö|Count|Average|Förfrågningar i programkö|Instans|
|CurrentAssemblies|Aktuellt antal sammansättningar|Count|Average|Aktuellt antal sammansättningar|Instans|
|TotalAppDomains|Totalt antal appdomäner|Count|Average|Totalt antal appdomäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal appdomäner som inte lästs in|Count|Average|Totalt antal appdomäner som inte lästs in|Instans|
|Gen0Collections|Generation 0-skräpinsamlingar|Count|Totalt|Generation 0-skräpinsamlingar|Instans|
|Gen1Collections|Generation 1-skräpinsamlingar|Count|Totalt|Generation 1-skräpinsamlingar|Instans|
|Gen2Collections|Generation 2-skräpinsamlingar|Count|Totalt|Generation 2-skräpinsamlingar|Instans|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Count|Totalt|Begäranden|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Count|Totalt|Http 101|Instans|
|Http2xx|HTTP 2xx|Count|Totalt|HTTP 2xx|Instans|
|Http3xx|HTTP 3xx|Count|Totalt|HTTP 3xx|Instans|
|Http401|HTTP 401|Count|Totalt|HTTP 401|Instans|
|Http403|Http 403|Count|Totalt|Http 403|Instans|
|Http404|Http 404|Count|Totalt|Http 404|Instans|
|Http406|Http 406|Count|Totalt|Http 406|Instans|
|Http4xx|Http 4xx|Count|Totalt|Http 4xx|Instans|
|Http5xx|Http-serverfel|Count|Totalt|Http-serverfel|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Average|Genomsnittlig svarstid|Instans|
|CpuPercentage|CPU-procent|Percent|Average|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Percent|Average|Minnesprocent|Instans|
|DiskQueueLength|Diskkölängd|Count|Average|Diskkölängd|Instans|
|HttpQueueLength|Http-kölängd|Count|Average|Http-kölängd|Instans|
|ActiveRequests|Aktiva förfrågningar|Count|Totalt|Aktiva förfrågningar|Instans|
|TotalFrontEnds|Totalt antal klienter|Count|Average|Totalt antal klienter|Inga dimensioner|
|SmallAppServicePlanInstances|App Service-plan för arbeten (låg användning)|Count|Average|App Service-plan för arbeten (låg användning)|Inga dimensioner|
|MediumAppServicePlanInstances|App Service-plan för arbeten (medelhög användning)|Count|Average|App Service-plan för arbeten (medelhög användning)|Inga dimensioner|
|LargeAppServicePlanInstances|App Service-plan för arbeten (hög användning)|Count|Average|App Service-plan för arbeten (hög användning)|Inga dimensioner|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbeten|Count|Average|Totalt antal arbeten|Inga dimensioner|
|WorkersAvailable|Tillgängliga arbeten|Count|Average|Tillgängliga arbeten|Inga dimensioner|
|WorkersUsed|Använda arbeten|Count|Average|Använda arbeten|Inga dimensioner|
|CpuPercentage|CPU-procent|Percent|Average|CPU-procent|Instans|
|MemoryPercentage|Minnesprocent|Percent|Average|Minnesprocent|Instans|

## <a name="next-steps"></a>Nästa steg
* [Läs om mått i Azure Monitor](data-platform.md)
* [Skapa aviseringar för mått](alerts-overview.md)
* [Exportera mått till lagring, Event Hub eller Log Analytics](diagnostic-logs-overview.md)
