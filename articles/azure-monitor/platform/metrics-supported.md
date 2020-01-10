---
title: Azure Monitor mått som stöds efter resurs typ
description: Lista över mått som är tillgängliga för varje resurs typ med Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 67e2675dbf65daf929407a437447f5d977c7a6c3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750409"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor

Azure Monitor tillhandahåller flera olika sätt att interagera med mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av PowerShell eller CLI. Nedan visas en fullständig lista över alla mått som för närvarande är tillgängliga med Azure Monitor mått pipelinen. Andra mått kan vara tillgängliga i portalen eller med äldre API: er. Listan nedan innehåller endast mått som är tillgängliga med den konsoliderade Azure Monitor mått pipelinen. Använd [2018-01-01 API-versionen](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) för att fråga efter och komma åt dessa mått.

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
> En lista över plattforms mått som kan exporteras via diagnostikinställningar finns i [den här artikeln](metrics-supported-export-diagnostic-settings.md).




## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|qpu_metric|QPU|Antal|Medel|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|memory_metric|Minne|Byte|Medel|Memory. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|private_bytes_metric|Privata byte|Byte|Medel|Privata byte.|ServerResourceType|
|virtual_bytes_metric|Virtuella byte|Byte|Medel|Virtuella byte.|ServerResourceType|
|TotalConnectionRequests|Totalt antal anslutnings begär Anden|Antal|Medel|Totalt antal anslutnings begär Anden. Dessa är mottagna.|ServerResourceType|
|SuccessfullConnectionsPerSec|Lyckade anslutningar per sekund|CountPerSecond|Medel|Antal slutförda slut för ande av anslutningar.|ServerResourceType|
|TotalConnectionFailures|Totalt antal anslutnings problem|Antal|Medel|Totalt antal misslyckade anslutnings försök.|ServerResourceType|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medel|Aktuellt antal användarsessioner som har upprättats.|ServerResourceType|
|QueryPoolBusyThreads|Upptagna trådar för frågeprocessorn|Antal|Medel|Antalet upptagna trådar i trådpoolen.|ServerResourceType|
|CommandPoolJobQueueLength|Kölängd för kommando bassäng|Antal|Medel|Antal jobb i kön för kommando tråds poolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Bearbetar Kölängd för poolen|Antal|Medel|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|CurrentConnections|Anslutning: aktuella anslutningar|Antal|Medel|Aktuellt antal upprättade klient anslutningar.|ServerResourceType|
|CleanerCurrentPrice|Minne: aktuellt pris för renare|Antal|Medel|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Minne: krympnings utrymme för rensning|Byte|Medel|Mängden minne i byte som kan rensas av bakgrunds rensaren.|ServerResourceType|
|CleanerMemoryNonshrinkable|Minne: det går inte att krympa rengörings minnet|Byte|Medel|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|ServerResourceType|
|MemoryUsage|Minne: minnes användning|Byte|Medel|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar minne som har mappats eller allokerats av xVelocity i Memory Analytics Engine (VertiPaq) utöver minnes gränsen för xVelocity-motorn.|ServerResourceType|
|MemoryLimitHard|Minne: minnes gräns hårt|Byte|Medel|Hård minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitHigh|Minne: minnes gräns hög|Byte|Medel|Hög minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitLow|Minne: minnes gräns låg|Byte|Medel|Låg minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitVertiPaq|Minne: minnes gräns VertiPaq|Byte|Medel|Minnes intern gräns, från konfigurations filen.|ServerResourceType|
|Kvot|Minne: kvot|Byte|Medel|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|ServerResourceType|
|QuotaBlocked|Minne: kvot blockerad|Antal|Medel|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|ServerResourceType|
|VertiPaqNonpaged|Minne: VertiPaq som inte är växlat|Byte|Medel|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|ServerResourceType|
|VertiPaqPaged|Minne: VertiPaq växlat|Byte|Medel|Byte av växlings Bart minne som används för minnes intern data.|ServerResourceType|
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
|QueryPoolJobQueueLength|Trådar: jobbkölängd för jobbkö|Antal|Medel|Antal jobb i kön för trådpoolen för Query.|ServerResourceType|
|ShortParsingBusyThreads|Trådar: kort parsning upptagna trådar|Antal|Medel|Antalet upptagna trådar i den kort parsar trådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Trådar: kort parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i kort parsar trådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Trådar: kort parsning av jobb Kölängd|Antal|Medel|Antal jobb i kö för kort parsar trådpoolen.|ServerResourceType|
|memory_thrashing_metric|Minnesförslöing|Procent|Medel|Genomsnittligt minne nedskräpning.|ServerResourceType|
|mashup_engine_qpu_metric|M-motor QPU|Antal|Medel|QPU användning av kombinations motor processer|ServerResourceType|
|mashup_engine_memory_metric|M motor minne|Byte|Medel|Minnes användning per kombinations motor processer|ServerResourceType|
|mashup_engine_private_bytes_metric|M motor privata byte|Byte|Medel|Användning av privata byte med kombinations motor processer.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Virtuella byte i M-motor|Byte|Medel|Virtuella byte som används av kombinations motor processer.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalRequests|Totalt antal Gateway-begäranden (inaktuella)|Antal|Totalt|Antal Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|SuccessfulRequests|Lyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal lyckade Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|UnauthorizedRequests|Obehöriga Gateway-begäranden (inaktuella)|Antal|Totalt|Antal otillåtna Gateway-begäranden – Använd dimensions mått för flera dimensioner med GatewayResponseCodeCategory dimension i stället|Plats, värdnamn|
|FailedRequests|Misslyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal misslyckade Gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|OtherRequests|Andra gateway-begäranden (inaktuella)|Antal|Totalt|Antal andra gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|Längd|Total varaktighet för gateway-begäranden|Millisekunder|Medel|Total varaktighet för gateway-begäranden i millisekunder|Plats, värdnamn|
|BackendDuration|Varaktighet för backend-begäranden|Millisekunder|Medel|Varaktighet för backend-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Kapacitet|Procent|Medel|Användnings mått för API Management-tjänsten|Location|
|EventHubTotalEvents|Totalt antal EventHub-händelser|Antal|Totalt|Antal händelser som skickats till EventHub|Location|
|EventHubSuccessfulEvents|Lyckade EventHub-händelser|Antal|Totalt|Antal lyckade EventHub-händelser|Location|
|EventHubTotalFailedEvents|Misslyckade EventHub-händelser|Antal|Totalt|Antal misslyckade EventHub-händelser|Location|
|EventHubRejectedEvents|Avvisade EventHub-händelser|Antal|Totalt|Antal avvisade EventHub-händelser (felaktig konfiguration eller otillåten)|Location|
|EventHubThrottledEvents|Begränsade EventHub-händelser|Antal|Totalt|Antal begränsade EventHub-händelser|Location|
|EventHubTimedoutEvents|Tids gränsen nåddes för EventHub-händelser|Antal|Totalt|Antal inaktuella EventHub-händelser|Location|
|EventHubDroppedEvents|Ignorerade EventHub-händelser|Antal|Totalt|Antalet händelser som hoppades över på grund av att gränsen för kös Tor lek har uppnåtts|Location|
|EventHubTotalBytesSent|Storlek på EventHub-händelser|Byte|Totalt|Total storlek för EventHub-händelser i byte|Location|
|Begäranden|Begäranden|Antal|Totalt|Mått för gateway-begäran med flera dimensioner|Plats, värdnamn, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Antal|Antal|Totalt antal inkommande HTTP-begäranden.|Inget|
|FailedHttpRequestCount|FailedHttpRequestCount|Antal|Antal|Misslyckade HTTP-begäranden.|Inget|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Antal|Medel|Svars tid på en http-begäran.|Inget|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/våren

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Procent andel system CPU-användning|Procent|Medel|Senaste CPU-användning för hela systemet|AppName, Pod|
|AppCpuUsagePercentage|Procent andel CPU-användning för app|Procent|Medel|JVM CPU-användning i procent|AppName, Pod|
|AppMemoryCommitted|Tilldelad app-minne|Byte|Medel|Minne som tilldelats JVM i byte|AppName, Pod|
|AppMemoryUsed|Använt app-minne|Byte|Medel|App-minne som används i byte|AppName, Pod|
|AppMemoryMax|Max för app-minne|Byte|Maximal|Maximal mängd minne i byte som kan användas för minnes hantering|AppName, Pod|
|MaxOldGenMemoryPoolBytes|Maximal tillgänglig gammal generations data storlek|Byte|Medel|Max storlek på den gamla generationens minnesbuffert|AppName, Pod|
|OldGenMemoryPoolBytes|Gammal generations data storlek|Byte|Medel|Storlek på den gamla generationens lagringspool efter en fullständig GC|AppName, Pod|
|OldGenPromotedBytes|Befordra till gammal generations data storlek|Byte|Maximal|Antalet positiva ökningar i storleken på den gamla generationens minnesbuffert innan GC till efter GC|AppName, Pod|
|YoungGenPromotedBytes|Höj data storlek för unga generationer|Byte|Maximal|Ökas för en ökning av storleken på den unga generationens minnesbuffert efter en GC till före nästa|AppName, Pod|
|GCPauseTotalCount|Antal pauser för GC|Antal|Totalt|Antal pauser för GC|AppName, Pod|
|GCPauseTotalTime|Total tid för GC-paus|Millisekunder|Totalt|Total tid för GC-paus|AppName, Pod|
|TomcatSentBytes|Totalt antal skickade byte i Tomcat|Byte|Totalt|Totalt antal skickade byte i Tomcat|AppName, Pod|
|TomcatReceivedBytes|Totalt antal mottagna byte i Tomcat|Byte|Totalt|Totalt antal mottagna byte i Tomcat|AppName, Pod|
|TomcatRequestTotalTime|Totalt antal gånger för Tomcat-begäran|Millisekunder|Totalt|Totalt antal gånger för Tomcat-begäran|AppName, Pod|
|TomcatRequestTotalCount|Totalt antal Tomcat-begäranden|Antal|Totalt|Totalt antal Tomcat-begäranden|AppName, Pod|
|TomcatResponseAvgTime|Genomsnittlig tid för Tomcat-begäran|Millisekunder|Medel|Genomsnittlig tid för Tomcat-begäran|AppName, Pod|
|TomcatRequestMaxTime|Max tid för Tomcat-begäran|Millisekunder|Maximal|Max tid för Tomcat-begäran|AppName, Pod|
|TomcatErrorCount|Tomcat globalt fel|Antal|Totalt|Tomcat globalt fel|AppName, Pod|
|TomcatSessionActiveMaxCount|Antal aktiva Tomcat-sessioner|Antal|Totalt|Antal aktiva Tomcat-sessioner|AppName, Pod|
|TomcatSessionAliveMaxTime|Maximal Alive-tid för Tomcat-session|Millisekunder|Maximal|Maximal Alive-tid för Tomcat-session|AppName, Pod|
|TomcatSessionActiveCurrentCount|Tomcat-session Alive-antal|Antal|Totalt|Tomcat-session Alive-antal|AppName, Pod|
|TomcatSessionCreatedCount|Antal skapade Tomcat-sessioner|Antal|Totalt|Antal skapade Tomcat-sessioner|AppName, Pod|
|TomcatSessionExpiredCount|Antal utgångna Tomcat-sessioner|Antal|Totalt|Antal utgångna Tomcat-sessioner|AppName, Pod|
|TomcatSessionRejectedCount|Antal nekade Tomcat-sessioner|Antal|Totalt|Antal nekade Tomcat-sessioner|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Runbook, status|
|TotalUpdateDeploymentRuns|Total uppdaterings distribution körs|Antal|Totalt|Totalt antal körningar av program uppdaterings distribution|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Den totala uppdaterings distributions datorn körs|Antal|Totalt|Total distributions dator för program uppdatering körs i en körning av program uppdaterings distribution|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CoreCount|Dedikerat antal kärnor|Antal|Totalt|Totalt antal dedikerade kärnor i batch-kontot|Inget|
|TotalNodeCount|Antal dedikerade noder|Antal|Totalt|Totalt antal dedikerade noder i batch-kontot|Inget|
|LowPriorityCoreCount|Antal LowPriority kärnor|Antal|Totalt|Totalt antal låg prioritets kärnor i batch-kontot|Inget|
|TotalLowPriorityNodeCount|Antal noder med låg prioritet|Antal|Totalt|Totalt antal noder med låg prioritet i batch-kontot|Inget|
|CreatingNodeCount|Antal noder skapas|Antal|Totalt|Antal noder som skapas|Inget|
|StartingNodeCount|Antalet noder startas|Antal|Totalt|Antal noder som börjar|Inget|
|WaitingForStartTaskNodeCount|Väntar på att starta aktiviteter antal noder|Antal|Totalt|Antal noder som väntar på att start aktiviteten ska slutföras|Inget|
|StartTaskFailedNodeCount|Starta aktivitet antal misslyckade noder|Antal|Totalt|Antal noder där start aktiviteten misslyckades|Inget|
|IdleNodeCount|Antal inaktiva noder|Antal|Totalt|Antal inaktiva noder|Inget|
|OfflineNodeCount|Antal offline-noder|Antal|Totalt|Antal offline-noder|Inget|
|RebootingNodeCount|Antalet noder startas om|Antal|Totalt|Antal omstarter av noder|Inget|
|ReimagingNodeCount|Antal noder för avbildning|Antal|Totalt|Antal avbildnings noder|Inget|
|RunningNodeCount|Antal noder som körs|Antal|Totalt|Antal noder som körs|Inget|
|LeavingPoolNodeCount|Antal noder för att lämna pooler|Antal|Totalt|Antal noder som lämnar poolen|Inget|
|UnusableNodeCount|Antal noder som inte kan användas|Antal|Totalt|Antal oanvändbara noder|Inget|
|PreemptedNodeCount|Antal misslyckade noder|Antal|Totalt|Antal misslyckade noder|Inget|
|TaskStartEvent|Aktivitetens start händelser|Antal|Totalt|Totalt antal aktiviteter som har startat|Inget|
|TaskCompleteEvent|Uppgift slutförda händelser|Antal|Totalt|Totalt antal slutförda uppgifter|Inget|
|TaskFailEvent|Aktivitet, misslyckade händelser|Antal|Totalt|Totalt antal uppgifter som har slutförts i felaktigt tillstånd|Inget|
|PoolCreateEvent|Skapa händelser för pool|Antal|Totalt|Totalt antal pooler som har skapats|Inget|
|PoolResizeStartEvent|Starta händelser för att ändra storlek på poolen|Antal|Totalt|Totalt antal storleks ändringar för pooler som har startat|Inget|
|PoolResizeCompleteEvent|Slutför händelser för storleks ändring av pool|Antal|Totalt|Totalt antal ändrade pooler som har slutförts|Inget|
|PoolDeleteStartEvent|Start händelser för borttagning av pool|Antal|Totalt|Totalt antal borttagningar av pooler som har startat|Inget|
|PoolDeleteCompleteEvent|Slutför händelse för borttagning av pool|Antal|Totalt|Totalt antal borttagna pooler som har slutförts|Inget|
|JobDeleteCompleteEvent|Slutför händelser för borttagning av jobb|Antal|Totalt|Totalt antal jobb som har tagits bort.|Inget|
|JobDeleteStartEvent|Start händelser för jobb borttagning|Antal|Totalt|Totalt antal jobb som har begärts att tas bort.|Inget|
|JobDisableCompleteEvent|Jobb för att inaktivera slutförda händelser|Antal|Totalt|Totalt antal jobb som har inaktiverats.|Inget|
|JobDisableStartEvent|Jobb inaktivera start händelser|Antal|Totalt|Totalt antal jobb som har begärts att inaktive ras.|Inget|
|JobStartEvent|Jobb start händelser|Antal|Totalt|Totalt antal jobb som har startats.|Inget|
|JobTerminateCompleteEvent|Slutför händelser för avsluta jobb|Antal|Totalt|Totalt antal jobb som har avslut ATS.|Inget|
|JobTerminateStartEvent|Jobb som avslutar start händelser|Antal|Totalt|Totalt antal jobb som har begärts att avslutas.|Inget|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. BatchAI/arbets ytor

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Jobb skickat|Jobb skickat|Antal|Totalt|Antal skickade jobb|Scenario, kluster namn|
|Jobbet har slutförts|Jobbet har slutförts|Antal|Totalt|Antal slutförda jobb|Scenario, kluster namn, ResultType|
|Totalt antal noder|Totalt antal noder|Antal|Medel|Antal totala noder|Scenario, kluster namn|
|Aktiva noder|Aktiva noder|Antal|Medel|Antal noder som körs|Scenario, kluster namn|
|Inaktiva noder|Inaktiva noder|Antal|Medel|Antal inaktiva noder|Scenario, kluster namn|
|Oanvändbara noder|Oanvändbara noder|Antal|Medel|Antal oanvändbara noder|Scenario, kluster namn|
|Misslyckade noder|Misslyckade noder|Antal|Medel|Antal misslyckade noder|Scenario, kluster namn|
|Lämnar noder|Lämnar noder|Antal|Medel|Antal lämnar noder|Scenario, kluster namn|
|Totalt antal kärnor|Totalt antal kärnor|Antal|Medel|Antal total kärnor|Scenario, kluster namn|
|Aktiva kärnor|Aktiva kärnor|Antal|Medel|Antal aktiva kärnor|Scenario, kluster namn|
|Inaktiva kärnor|Inaktiva kärnor|Antal|Medel|Antal inaktiva kärnor|Scenario, kluster namn|
|Oanvändbara kärnor|Oanvändbara kärnor|Antal|Medel|Antal oanvändbara kärnor|Scenario, kluster namn|
|Blockerade kärnor|Blockerade kärnor|Antal|Medel|Antal blockerade kärnor|Scenario, kluster namn|
|Lämnar kärnor|Lämnar kärnor|Antal|Medel|Antal lämnar kärnor|Scenario, kluster namn|
|Kvot användning i procent|Kvot användning i procent|Antal|Medel|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Procent andel CPU-användning|Procent|Maximal|Procent andel CPU-användning|Nod|
|MemoryUsage|Minnesanvändning|Byte|Medel|Minnesanvändning|Nod|
|MemoryLimit|Minnes gräns|Byte|Medel|Minnes gräns|Nod|
|MemoryUsagePercentageInDouble|Minnes användnings procent|Procent|Medel|Minnes användnings procent|Nod|
|StorageUsage|Lagrings användning|Byte|Medel|Lagrings användning|Nod|
|IOReadBytes|Lästa byte i IO|Byte|Totalt|Lästa byte i IO|Nod|
|IOWriteBytes|Skrivna byte i IO|Byte|Totalt|Skrivna byte i IO|Nod|
|ConnectionAccepted|Godkända anslutningar|Antal|Totalt|Godkända anslutningar|Nod|
|ConnectionHandled|Hanterade anslutningar|Antal|Totalt|Hanterade anslutningar|Nod|
|ConnectionActive|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Nod|
|RequestHandled|Hanterade begär Anden|Antal|Totalt|Hanterade begär Anden|Nod|
|ProcessedBlocks|Bearbetade block|Antal|Totalt|Bearbetade block|Nod|
|ProcessedTransactions|Bearbetade transaktioner|Antal|Totalt|Bearbetade transaktioner|Nod|
|PendingTransactions|Väntande transaktioner|Antal|Medel|Väntande transaktioner|Nod|
|QueuedTransactions|Köade transaktioner|Antal|Medel|Köade transaktioner|Nod|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedclients|Anslutna klienter|Antal|Maximal||ShardId|
|totalcommandsprocessed|Totalt antal åtgärder|Antal|Totalt||ShardId|
|cachehits|Cacheträffar|Antal|Totalt||ShardId|
|cachemisses|Cachemissar|Antal|Totalt||ShardId|
|cachemissrate|Missar i cache|Procent|cachemissrate||ShardId|
|getcommands|Sparas|Antal|Totalt||ShardId|
|setcommands|Uppsättningar|Antal|Totalt||ShardId|
|operationsPerSecond|Åtgärder per sekund|Antal|Maximal||ShardId|
|evictedkeys|Avlägsnade nycklar|Antal|Totalt||ShardId|
|totalkeys|Totalt antal nycklar|Antal|Maximal||ShardId|
|expiredkeys|Förfallna nycklar|Antal|Totalt||ShardId|
|usedmemory|Använt minne|Byte|Maximal||ShardId|
|usedmemorypercentage|Använd minnes procent|Procent|Maximal||ShardId|
|usedmemoryRss|RSS för använt minne|Byte|Maximal||ShardId|
|serverLoad|Server belastning|Procent|Maximal||ShardId|
|cacheWrite|Skrivcache|BytesPerSecond|Maximal||ShardId|
|cacheRead|Läsning cache|BytesPerSecond|Maximal||ShardId|
|percentProcessorTime|Processor|Procent|Maximal||ShardId|
|cacheLatency|Mikrosekunder för cache-fördröjning (för hands version)|Antal|Medel||ShardId|
|fel|Fel|Antal|Maximal||ShardId,ErrorType|
|connectedclients0|Anslutna klienter (Shard 0)|Antal|Maximal||Inget|
|totalcommandsprocessed0|Totalt antal åtgärder (Shard 0)|Antal|Totalt||Inget|
|cachehits0|Cacheträffar (Shard 0)|Antal|Totalt||Inget|
|cachemisses0|Cachemissar (Shard 0)|Antal|Totalt||Inget|
|getcommands0|Hämtar (Shard 0)|Antal|Totalt||Inget|
|setcommands0|Uppsättningar (Shard 0)|Antal|Totalt||Inget|
|operationsPerSecond0|Åtgärder per sekund (Shard 0)|Antal|Maximal||Inget|
|evictedkeys0|Avlägsnade nycklar (Shard 0)|Antal|Totalt||Inget|
|totalkeys0|Totalt antal nycklar (Shard 0)|Antal|Maximal||Inget|
|expiredkeys0|Utgångna nycklar (Shard 0)|Antal|Totalt||Inget|
|usedmemory0|Använt minne (Shard 0)|Byte|Maximal||Inget|
|usedmemoryRss0|Använt minne RSS (Shard 0)|Byte|Maximal||Inget|
|serverLoad0|Server belastning (Shard 0)|Procent|Maximal||Inget|
|cacheWrite0|Skrivcache (Shard 0)|BytesPerSecond|Maximal||Inget|
|cacheRead0|Läsning av cache (Shard 0)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime0|PROCESSOR (Shard 0)|Procent|Maximal||Inget|
|connectedclients1|Anslutna klienter (Shard 1)|Antal|Maximal||Inget|
|totalcommandsprocessed1|Totalt antal åtgärder (Shard 1)|Antal|Totalt||Inget|
|cachehits1|Cacheträffar (Shard 1)|Antal|Totalt||Inget|
|cachemisses1|Cachemissar (Shard 1)|Antal|Totalt||Inget|
|getcommands1|Hämtar (Shard 1)|Antal|Totalt||Inget|
|setcommands1|Uppsättningar (Shard 1)|Antal|Totalt||Inget|
|operationsPerSecond1|Åtgärder per sekund (Shard 1)|Antal|Maximal||Inget|
|evictedkeys1|Avlägsnade nycklar (Shard 1)|Antal|Totalt||Inget|
|totalkeys1|Totalt antal nycklar (Shard 1)|Antal|Maximal||Inget|
|expiredkeys1|Utgångna nycklar (Shard 1)|Antal|Totalt||Inget|
|usedmemory1|Använt minne (Shard 1)|Byte|Maximal||Inget|
|usedmemoryRss1|RSS för använt minne (Shard 1)|Byte|Maximal||Inget|
|serverLoad1|Server belastning (Shard 1)|Procent|Maximal||Inget|
|cacheWrite1|Skrivcache (Shard 1)|BytesPerSecond|Maximal||Inget|
|cacheRead1|Läsning av cache (Shard 1)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime1|PROCESSOR (Shard 1)|Procent|Maximal||Inget|
|connectedclients2|Anslutna klienter (Shard 2)|Antal|Maximal||Inget|
|totalcommandsprocessed2|Totalt antal åtgärder (Shard 2)|Antal|Totalt||Inget|
|cachehits2|Cacheträffar (Shard 2)|Antal|Totalt||Inget|
|cachemisses2|Cachemissar (Shard 2)|Antal|Totalt||Inget|
|getcommands2|Hämtar (Shard 2)|Antal|Totalt||Inget|
|setcommands2|Uppsättningar (Shard 2)|Antal|Totalt||Inget|
|operationsPerSecond2|Åtgärder per sekund (Shard 2)|Antal|Maximal||Inget|
|evictedkeys2|Avlägsnade nycklar (Shard 2)|Antal|Totalt||Inget|
|totalkeys2|Totalt antal nycklar (Shard 2)|Antal|Maximal||Inget|
|expiredkeys2|Utgångna nycklar (Shard 2)|Antal|Totalt||Inget|
|usedmemory2|Använt minne (Shard 2)|Byte|Maximal||Inget|
|usedmemoryRss2|Använt minne RSS (Shard 2)|Byte|Maximal||Inget|
|serverLoad2|Server belastning (Shard 2)|Procent|Maximal||Inget|
|cacheWrite2|Skrivcache (Shard 2)|BytesPerSecond|Maximal||Inget|
|cacheRead2|Läsning av cache (Shard 2)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime2|PROCESSOR (Shard 2)|Procent|Maximal||Inget|
|connectedclients3|Anslutna klienter (Shard 3)|Antal|Maximal||Inget|
|totalcommandsprocessed3|Totalt antal åtgärder (Shard 3)|Antal|Totalt||Inget|
|cachehits3|Cacheträffar (Shard 3)|Antal|Totalt||Inget|
|cachemisses3|Cachemissar (Shard 3)|Antal|Totalt||Inget|
|getcommands3|Hämtar (Shard 3)|Antal|Totalt||Inget|
|setcommands3|Uppsättningar (Shard 3)|Antal|Totalt||Inget|
|operationsPerSecond3|Åtgärder per sekund (Shard 3)|Antal|Maximal||Inget|
|evictedkeys3|Avlägsnade nycklar (Shard 3)|Antal|Totalt||Inget|
|totalkeys3|Totalt antal nycklar (Shard 3)|Antal|Maximal||Inget|
|expiredkeys3|Utgångna nycklar (Shard 3)|Antal|Totalt||Inget|
|usedmemory3|Använt minne (Shard 3)|Byte|Maximal||Inget|
|usedmemoryRss3|Använt minne RSS (Shard 3)|Byte|Maximal||Inget|
|serverLoad3|Server belastning (Shard 3)|Procent|Maximal||Inget|
|cacheWrite3|Skrivcache (Shard 3)|BytesPerSecond|Maximal||Inget|
|cacheRead3|Läsning av cacheminne (Shard 3)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime3|PROCESSOR (Shard 3)|Procent|Maximal||Inget|
|connectedclients4|Anslutna klienter (Shard 4)|Antal|Maximal||Inget|
|totalcommandsprocessed4|Totalt antal åtgärder (Shard 4)|Antal|Totalt||Inget|
|cachehits4|Cacheträffar (Shard 4)|Antal|Totalt||Inget|
|cachemisses4|Cachemissar (Shard 4)|Antal|Totalt||Inget|
|getcommands4|Hämtar (Shard 4)|Antal|Totalt||Inget|
|setcommands4|Uppsättningar (Shard 4)|Antal|Totalt||Inget|
|operationsPerSecond4|Åtgärder per sekund (Shard 4)|Antal|Maximal||Inget|
|evictedkeys4|Avlägsnade nycklar (Shard 4)|Antal|Totalt||Inget|
|totalkeys4|Totalt antal nycklar (Shard 4)|Antal|Maximal||Inget|
|expiredkeys4|Utgångna nycklar (Shard 4)|Antal|Totalt||Inget|
|usedmemory4|Använt minne (Shard 4)|Byte|Maximal||Inget|
|usedmemoryRss4|Använt minne RSS (Shard 4)|Byte|Maximal||Inget|
|serverLoad4|Server belastning (Shard 4)|Procent|Maximal||Inget|
|cacheWrite4|Skrivcache (Shard 4)|BytesPerSecond|Maximal||Inget|
|cacheRead4|Läsning av cacheminne (Shard 4)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime4|PROCESSOR (Shard 4)|Procent|Maximal||Inget|
|connectedclients5|Anslutna klienter (Shard 5)|Antal|Maximal||Inget|
|totalcommandsprocessed5|Totalt antal åtgärder (Shard 5)|Antal|Totalt||Inget|
|cachehits5|Cacheträffar (Shard 5)|Antal|Totalt||Inget|
|cachemisses5|Cachemissar (Shard 5)|Antal|Totalt||Inget|
|getcommands5|Hämtar (Shard 5)|Antal|Totalt||Inget|
|setcommands5|Uppsättningar (Shard 5)|Antal|Totalt||Inget|
|operationsPerSecond5|Åtgärder per sekund (Shard 5)|Antal|Maximal||Inget|
|evictedkeys5|Avlägsnade nycklar (Shard 5)|Antal|Totalt||Inget|
|totalkeys5|Totalt antal nycklar (Shard 5)|Antal|Maximal||Inget|
|expiredkeys5|Utgångna nycklar (Shard 5)|Antal|Totalt||Inget|
|usedmemory5|Använt minne (Shard 5)|Byte|Maximal||Inget|
|usedmemoryRss5|Använt minne RSS (Shard 5)|Byte|Maximal||Inget|
|serverLoad5|Server belastning (Shard 5)|Procent|Maximal||Inget|
|cacheWrite5|Skrivcache (Shard 5)|BytesPerSecond|Maximal||Inget|
|cacheRead5|Läsning av cacheminne (Shard 5)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime5|PROCESSOR (Shard 5)|Procent|Maximal||Inget|
|connectedclients6|Anslutna klienter (Shard 6)|Antal|Maximal||Inget|
|totalcommandsprocessed6|Totalt antal åtgärder (Shard 6)|Antal|Totalt||Inget|
|cachehits6|Cacheträffar (Shard 6)|Antal|Totalt||Inget|
|cachemisses6|Cachemissar (Shard 6)|Antal|Totalt||Inget|
|getcommands6|Hämtar (Shard 6)|Antal|Totalt||Inget|
|setcommands6|Uppsättningar (Shard 6)|Antal|Totalt||Inget|
|operationsPerSecond6|Åtgärder per sekund (Shard 6)|Antal|Maximal||Inget|
|evictedkeys6|Avlägsnade nycklar (Shard 6)|Antal|Totalt||Inget|
|totalkeys6|Totalt antal nycklar (Shard 6)|Antal|Maximal||Inget|
|expiredkeys6|Utgångna nycklar (Shard 6)|Antal|Totalt||Inget|
|usedmemory6|Använt minne (Shard 6)|Byte|Maximal||Inget|
|usedmemoryRss6|Använt minne RSS (Shard 6)|Byte|Maximal||Inget|
|serverLoad6|Server belastning (Shard 6)|Procent|Maximal||Inget|
|cacheWrite6|Skrivcache (Shard 6)|BytesPerSecond|Maximal||Inget|
|cacheRead6|Läsning av cache (Shard 6)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime6|PROCESSOR (Shard 6)|Procent|Maximal||Inget|
|connectedclients7|Anslutna klienter (Shard 7)|Antal|Maximal||Inget|
|totalcommandsprocessed7|Totalt antal åtgärder (Shard 7)|Antal|Totalt||Inget|
|cachehits7|Cacheträffar (Shard 7)|Antal|Totalt||Inget|
|cachemisses7|Cachemissar (Shard 7)|Antal|Totalt||Inget|
|getcommands7|Hämtar (Shard 7)|Antal|Totalt||Inget|
|setcommands7|Uppsättningar (Shard 7)|Antal|Totalt||Inget|
|operationsPerSecond7|Åtgärder per sekund (Shard 7)|Antal|Maximal||Inget|
|evictedkeys7|Avlägsnade nycklar (Shard 7)|Antal|Totalt||Inget|
|totalkeys7|Totalt antal nycklar (Shard 7)|Antal|Maximal||Inget|
|expiredkeys7|Utgångna nycklar (Shard 7)|Antal|Totalt||Inget|
|usedmemory7|Använt minne (Shard 7)|Byte|Maximal||Inget|
|usedmemoryRss7|Använt minne RSS (Shard 7)|Byte|Maximal||Inget|
|serverLoad7|Server belastning (Shard 7)|Procent|Maximal||Inget|
|cacheWrite7|Skrivcache (Shard 7)|BytesPerSecond|Maximal||Inget|
|cacheRead7|Läsning av cache (Shard 7)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime7|PROCESSOR (Shard 7)|Procent|Maximal||Inget|
|connectedclients8|Anslutna klienter (Shard 8)|Antal|Maximal||Inget|
|totalcommandsprocessed8|Totalt antal åtgärder (Shard 8)|Antal|Totalt||Inget|
|cachehits8|Cacheträffar (Shard 8)|Antal|Totalt||Inget|
|cachemisses8|Cachemissar (Shard 8)|Antal|Totalt||Inget|
|getcommands8|Hämtar (Shard 8)|Antal|Totalt||Inget|
|setcommands8|Uppsättningar (Shard 8)|Antal|Totalt||Inget|
|operationsPerSecond8|Åtgärder per sekund (Shard 8)|Antal|Maximal||Inget|
|evictedkeys8|Avlägsnade nycklar (Shard 8)|Antal|Totalt||Inget|
|totalkeys8|Totalt antal nycklar (Shard 8)|Antal|Maximal||Inget|
|expiredkeys8|Utgångna nycklar (Shard 8)|Antal|Totalt||Inget|
|usedmemory8|Använt minne (Shard 8)|Byte|Maximal||Inget|
|usedmemoryRss8|Använt minne RSS (Shard 8)|Byte|Maximal||Inget|
|serverLoad8|Server belastning (Shard 8)|Procent|Maximal||Inget|
|cacheWrite8|Skrivcache (Shard 8)|BytesPerSecond|Maximal||Inget|
|cacheRead8|Läsning av cache (Shard 8)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime8|PROCESSOR (Shard 8)|Procent|Maximal||Inget|
|connectedclients9|Anslutna klienter (Shard 9)|Antal|Maximal||Inget|
|totalcommandsprocessed9|Totalt antal åtgärder (Shard 9)|Antal|Totalt||Inget|
|cachehits9|Cacheträffar (Shard 9)|Antal|Totalt||Inget|
|cachemisses9|Cachemissar (Shard 9)|Antal|Totalt||Inget|
|getcommands9|Hämtar (Shard 9)|Antal|Totalt||Inget|
|setcommands9|Uppsättningar (Shard 9)|Antal|Totalt||Inget|
|operationsPerSecond9|Åtgärder per sekund (Shard 9)|Antal|Maximal||Inget|
|evictedkeys9|Avlägsnade nycklar (Shard 9)|Antal|Totalt||Inget|
|totalkeys9|Totalt antal nycklar (Shard 9)|Antal|Maximal||Inget|
|expiredkeys9|Utgångna nycklar (Shard 9)|Antal|Totalt||Inget|
|usedmemory9|Använt minne (Shard 9)|Byte|Maximal||Inget|
|usedmemoryRss9|Använt minne RSS (Shard 9)|Byte|Maximal||Inget|
|serverLoad9|Server belastning (Shard 9)|Procent|Maximal||Inget|
|cacheWrite9|Skrivcache (Shard 9)|BytesPerSecond|Maximal||Inget|
|cacheRead9|Läsning av cacheminne (Shard 9)|BytesPerSecond|Maximal||Inget|
|percentProcessorTime9|PROCESSOR (Shard 9)|Procent|Maximal||Inget|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|Inget|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|Inget|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|Inget|
|Disk-lästa byte/s|Disk läsning|BytesPerSecond|Medel|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|Inget|
|Disk-skrivna byte/s|Disk skrivning|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|Inget|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Disk-Läs-IOPS.|Inget|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Skriv IOPS för disk.|Inget|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domän namn/platser/roller

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|RoleInstanceId|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|RoleInstanceId|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|RoleInstanceId|
|Disk-lästa byte/s|Disk läsning|BytesPerSecond|Medel|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|RoleInstanceId|
|Disk-skrivna byte/s|Disk skrivning|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|RoleInstanceId|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Disk-Läs-IOPS.|RoleInstanceId|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Skriv IOPS för disk.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Medel|Kapacitet som används av konto|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|Blobkapacitet|Byte|Medel|Mängden lagring som används av lagringskontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|Antalet blobar|Antal|Medel|Antalet blobar i lagringskontots Blob Service.|BlobType,-nivå|
|ContainerCount|Antal blobcontainrar|Antal|Medel|Antalet containrar i lagringskontots Blob Service.|Inget|
|IndexCapacity|Indexkapacitet|Byte|Medel|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabellkapacitet|Byte|Medel|Mängden lagring som används av lagringskontots tabelltjänst i byte.|Inget|
|TableCount|Antal tabeller|Antal|Medel|Antalet tabeller i lagringskontots tabelltjänst.|Inget|
|TableEntityCount|Antal tabellentiteter|Antal|Medel|Antalet tabellentiteter i lagringskontots tabelltjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Fil kapacitet|Byte|Medel|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|FileShare|
|FileCount|Antal filer|Antal|Medel|Antalet filer i lagrings kontots fil tjänst.|FileShare|
|FileShareCount|Antal fil resurser|Antal|Medel|Antalet fil resurser i lagrings kontots fil tjänst.|Inget|
|FileShareSnapshotCount|Antal ögonblicks bilder av fil resurs|Antal|Medel|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|Storlek på fil resursens ögonblicks bild|Byte|Medel|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|FileShareQuota|Fil resursens kvot storlek|Byte|Medel|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kökapacitet|Byte|Medel|Mängden lagring som används av lagringskontots kötjänst i byte.|Inget|
|QueueCount|Antal köer|Antal|Medel|Antalet köer i lagringskontots kötjänst.|Inget|
|QueueMessageCount|Antal kömeddelanden|Antal|Medel|Ungefärligt antal kömeddelanden i lagringskontots kötjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalCalls|Totalt antal anrop|Antal|Totalt|Totalt antal anrop.|ApiName, OperationName, region|
|SuccessfulCalls|Lyckade anrop|Antal|Totalt|Antal lyckade anrop.|ApiName, OperationName, region|
|TotalErrors|Totalt antal fel|Antal|Totalt|Totalt antal anrop med fel svar (HTTP-svarskod 4xx eller 5xx).|ApiName, OperationName, region|
|BlockedCalls|Blockerade anrop|Antal|Totalt|Antal anrop som har överskridit frekvensen eller kvot gränsen.|ApiName, OperationName, region|
|ServerErrors|Server fel|Antal|Totalt|Antal anrop med internt tjänst fel (HTTP-svarskod 5xx).|ApiName, OperationName, region|
|ClientErrors|Klient fel|Antal|Totalt|Antal anrop med fel på klient sidan (HTTP-svarskod 4xx).|ApiName, OperationName, region|
|Datain|Data in|Byte|Totalt|Storlek på inkommande data i byte.|ApiName, OperationName, region|
|Data|Data ut|Byte|Totalt|Storlek på utgående data i byte.|ApiName, OperationName, region|
|Svarstid|Svarstid|Millisekunder|Medel|Svars tid i millisekunder.|ApiName, OperationName, region|
|CharactersTranslated|Översatta tecken|Antal|Totalt|Totalt antal tecken i begäran om inkommande text.|ApiName, OperationName, region|
|CharactersTrained|Upptränade tecken|Antal|Totalt|Totalt antal tecken som har tränats.|ApiName, OperationName, region|
|SpeechSessionDuration|Tal sessionens varaktighet|Sekunder|Totalt|Total varaktighet för talläget på några sekunder.|ApiName, OperationName, region|
|TotalTransactions|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner.|Inget|
|TotalTokenCalls|Totalt antal token-anrop|Antal|Totalt|Totalt antal token-anrop.|ApiName, OperationName, region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inget|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inget|
|Nätverk – utgående|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inget|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inget|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inget|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Disk-Läs-IOPS|Inget|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Skriv IOPS för disk|Inget|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter som är tillgängliga för burst|Inget|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medel|Totalt antal krediter som konsumeras av den virtuella datorn|Inget|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk (inaktuellt)|Antal|Medel|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|KÖDJUP för operativ system per disk|OS-KÖDJUP (inaktuellt)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|LUN|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|LUN|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|LUN|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|LUN|
|Ködjup för datadisk|Data disk Queue djup (för hands version)|Antal|Medel|Data diskens ködjup (eller Kölängd)|LUN|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Inkommande flöden|Inkommande flöden|Antal|Medel|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inget|
|Utgående flöden|Utgående flöden|Antal|Medel|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inget|
|Högsta skapande frekvens för inkommande flöden|Maximal skapande frekvens för inkommande flöden (för hands version)|CountPerSecond|Medel|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inget|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden (för hands version)|CountPerSecond|Medel|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inget|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medel|Läsning av Premium data disk-cache|LUN|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medel|Read missar i Premium data-diskcachen|LUN|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medel|Läsning av Premium OS-diskcachen|Inget|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medel|Read missar i Premium OS-diskcachen|Inget|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inget|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inget|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|VMName|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|VMName|
|Nätverk – utgående|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|VMName|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|VMName|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|VMName|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Disk-Läs-IOPS|VMName|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Skriv IOPS för disk|VMName|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter som är tillgängliga för burst|Inget|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medel|Totalt antal krediter som konsumeras av den virtuella datorn|Inget|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk (inaktuellt)|Antal|Medel|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|KÖDJUP för operativ system per disk|OS-KÖDJUP (inaktuellt)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|LUN, VMName|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|LUN, VMName|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Ködjup för datadisk|Data disk Queue djup (för hands version)|Antal|Medel|Data diskens ködjup (eller Kölängd)|LUN, VMName|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|VMName|
|Inkommande flöden|Inkommande flöden|Antal|Medel|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|VMName|
|Utgående flöden|Utgående flöden|Antal|Medel|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|VMName|
|Högsta skapande frekvens för inkommande flöden|Maximal skapande frekvens för inkommande flöden (för hands version)|CountPerSecond|Medel|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|VMName|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden (för hands version)|CountPerSecond|Medel|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|VMName|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medel|Läsning av Premium data disk-cache|LUN, VMName|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medel|Read missar i Premium data-diskcachen|LUN, VMName|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medel|Läsning av Premium OS-diskcachen|VMName|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medel|Read missar i Premium OS-diskcachen|VMName|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|VMName|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer|Inget|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inget|
|Nätverk – utgående|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inget|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inget|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inget|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Disk-Läs-IOPS|Inget|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Skriv IOPS för disk|Inget|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter som är tillgängliga för burst|Inget|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medel|Totalt antal krediter som konsumeras av den virtuella datorn|Inget|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk (inaktuellt)|Antal|Medel|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|KÖDJUP för operativ system per disk|OS-KÖDJUP (inaktuellt)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden|LUN|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden|LUN|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden|LUN|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden|LUN|
|Ködjup för datadisk|Data disk Queue djup (för hands version)|Antal|Medel|Data diskens ködjup (eller Kölängd)|LUN|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medel|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Antal|Medel|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Inkommande flöden|Inkommande flöden|Antal|Medel|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inget|
|Utgående flöden|Utgående flöden|Antal|Medel|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inget|
|Högsta skapande frekvens för inkommande flöden|Maximal skapande frekvens för inkommande flöden (för hands version)|CountPerSecond|Medel|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inget|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden (för hands version)|CountPerSecond|Medel|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inget|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medel|Läsning av Premium data disk-cache|LUN|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medel|Read missar i Premium data-diskcachen|LUN|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medel|Läsning av Premium OS-diskcachen|Inget|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medel|Read missar i Premium OS-diskcachen|Inget|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inget|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inget|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|CPU-användning|Antal|Medel|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Minnesanvändning|Byte|Medel|Total minnes användning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Mottagna nätverks byte per sekund|Byte|Medel|Mottagna nätverks byte per sekund.|Inget|
|NetworkBytesTransmittedPerSecond|Överförda nätverks byte per sekund|Byte|Medel|Överförda nätverks byte per sekund.|Inget|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalPullCount|Totalt antal hämtningar|Antal|Medel|Totalt antal bild hämtningar|Inget|
|SuccessfulPullCount|Antal lyckade pull-överföringar|Antal|Medel|Antal lyckade bild hämtningar|Inget|
|TotalPushCount|Totalt antal push-meddelanden|Antal|Medel|Totalt antal push-meddelanden för avbildningar|Inget|
|SuccessfulPushCount|Antal lyckade push-meddelanden|Antal|Medel|Antal lyckade avbildnings push-meddelanden|Inget|
|RunDuration|Körnings tid|Millisekunder|Totalt|Körnings tid i millisekunder|Inget|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Antal|Medel|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Inget|
|kube_node_status_allocatable_memory_bytes|Total mängd tillgängligt minne i ett hanterat kluster|Byte|Medel|Total mängd tillgängligt minne i ett hanterat kluster|Inget|
|kube_pod_status_ready|Antal poddar i klart läge|Antal|Medel|Antal poddar i klart läge|namnrymd, Pod|
|kube_node_status_condition|Status för olika nod villkor|Antal|Medel|Status för olika nod villkor|villkor, status, status2, nod|
|kube_pod_status_phase|Antal poddar per fas|Antal|Medel|Antal poddar per fas|fas, namnrymd, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfullRequests|Lyckade förfrågningar|Antal|Totalt|Lyckade förfrågningar gjorda av den anpassade providern|HttpMethod, CallPath, StatusCode|
|FailedRequests|Misslyckade begäranden|Antal|Totalt|Hämtar de tillgängliga loggarna för anpassade resurs leverantörer|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|NICReadThroughput|Läs data flöde (nätverk)|BytesPerSecond|Medel|Läs data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|NICWriteThroughput|Skriv data flöde (nätverk)|BytesPerSecond|Medel|Skriv data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|CloudReadThroughputPerShare|Data flöde för moln hämtning (resurs)|BytesPerSecond|Medel|Ladda ned data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|CloudUploadThroughputPerShare|Moln överförings data flöde (resurs)|BytesPerSecond|Medel|Ladda upp data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|BytesUploadedToCloudPerShare|Överförda moln byte (resurs)|Byte|Medel|Det totala antalet byte som överförs till Azure från en resurs under rapporterings perioden.|Dela|
|Enhet|Total kapacitet|Byte|Medel|Total kapacitet|Inget|
|Availablecapacity;)|Tillgänglig kapacitet|Byte|Medel|Tillgänglig kapacitet i byte under rapporterings perioden.|Inget|
|CloudUploadThroughput|Moln överförings data flöde|BytesPerSecond|Medel|Moln överförings data flödet till Azure under rapporterings perioden.|Inget|
|CloudReadThroughput|Data flöde för moln hämtning|BytesPerSecond|Medel|Molnet Ladda ned data flödet till Azure under rapporterings perioden.|Inget|
|BytesUploadedToCloud|Överförda moln byte (enhet)|Byte|Medel|Det totala antalet byte som överförs till Azure från en enhet under rapporterings perioden.|Inget|
|HyperVVirtualProcessorUtilization|Edge Compute-procent CPU|Procent|Medel|CPU-användning i procent|InstanceName|
|HyperVMemoryUtilization|Edge Compute-minnes användning|Procent|Medel|Mängden RAM-minne som används|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FailedRuns|Misslyckade körningar|Antal|Totalt||pipelineName, activityName|
|SuccessfulRuns|Lyckade körningar|Antal|Totalt||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PipelineFailedRuns|Misslyckad pipeline kör mått|Antal|Totalt||FailureType, namn|
|PipelineSucceededRuns|Slutförd pipeline kör mått|Antal|Totalt||FailureType, namn|
|PipelineCancelledRuns|Avbruten pipeline kör mått|Antal|Totalt||FailureType, namn|
|ActivityFailedRuns|Misslyckad aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Genomförd aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivityCancelledRuns|Avbrutna aktiviteter kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Misslyckad utlösare kör mått|Antal|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Lyckade utlösare kör mått|Antal|Totalt||Namn, FailureType|
|TriggerCancelledRuns|Avbrutna utlösare kör mått|Antal|Totalt||Namn, FailureType|
|IntegrationRuntimeCpuPercentage|PROCESSOR användning för integration runtime|Procent|Medel||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAvailableMemory|Tillgängligt minne för integration runtime|Byte|Medel||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAverageTaskPickupDelay|Varaktighet för integration runtime-kö|Sekunder|Medel||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Kölängd för integration runtime|Antal|Medel||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Antal tillgängliga noder för integration runtime|Antal|Medel||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maximalt antal tillåtna entiteter|Antal|Maximal||Inget|
|MaxAllowedFactorySizeInGbUnits|Högsta tillåtna fabriks storlek (GB enhet)|Antal|Maximal||Inget|
|ResourceCount|Totalt antal entiteter|Antal|Maximal||Inget|
|FactorySizeInGbUnits|Total fabriks storlek (GB enhet)|Antal|Maximal||Inget|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobEndedSuccess|Slutförda jobb|Antal|Totalt|Antal lyckade jobb.|Inget|
|JobEndedFailure|Misslyckade jobb|Antal|Totalt|Antal misslyckade jobb.|Inget|
|JobEndedCancelled|Avbrutna jobb|Antal|Totalt|Antal avbrutna jobb.|Inget|
|JobAUEndedSuccess|Lyckad AU-tid|Sekunder|Totalt|Total AU-tid för lyckade jobb.|Inget|
|JobAUEndedFailure|Misslyckad AU-tid|Sekunder|Totalt|Total AU-tid för misslyckade jobb.|Inget|
|JobAUEndedCancelled|Tid för avbruten AU|Sekunder|Totalt|Total AU-tid för avbrutna jobb.|Inget|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Byte|Maximal|Den totala mängden data som lagras i kontot.|Inget|
|DataWritten|Skrivna data|Byte|Totalt|Total mängd data som skrivs till kontot.|Inget|
|DataRead|Lästa data|Byte|Totalt|Total mängd data som lästs från kontot.|Inget|
|WriteRequests|Skriv förfrågningar|Antal|Totalt|Antal data skrivnings begär anden till kontot.|Inget|
|ReadRequests|Läs begär Anden|Antal|Totalt|Antal data läsnings begär anden till kontot.|Inget|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|memory_percent|Minnes procent|Procent|Medel|Minnes procent|Inget|
|io_consumption_percent|I/o procent|Procent|Medel|I/o procent|Inget|
|storage_percent|Lagrings procent|Procent|Medel|Lagrings procent|Inget|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inget|
|storage_limit|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inget|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medel|Server logg lagrings procent|Inget|
|serverlog_storage_usage|Server logg lagring används|Byte|Medel|Server logg lagring används|Inget|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Medel|Server logg lagrings gräns|Inget|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inget|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Medel|Lagring av säkerhets kopior som används|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inget|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|memory_percent|Minnes procent|Procent|Medel|Minnes procent|Inget|
|io_consumption_percent|I/o procent|Procent|Medel|I/o procent|Inget|
|storage_percent|Lagrings procent|Procent|Medel|Lagrings procent|Inget|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inget|
|storage_limit|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inget|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medel|Server logg lagrings procent|Inget|
|serverlog_storage_usage|Server logg lagring används|Byte|Medel|Server logg lagring används|Inget|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inget|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inget|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Medel|Lagring av säkerhets kopior som används|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inget|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|memory_percent|Minnes procent|Procent|Medel|Minnes procent|Inget|
|io_consumption_percent|I/o procent|Procent|Medel|I/o procent|Inget|
|storage_percent|Lagrings procent|Procent|Medel|Lagrings procent|Inget|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inget|
|storage_limit|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inget|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medel|Server logg lagrings procent|Inget|
|serverlog_storage_usage|Server logg lagring används|Byte|Medel|Server logg lagring används|Inget|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inget|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Medel|Lagring av säkerhets kopior som används|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inget|
|pg_replica_log_delay_in_seconds|Replik fördröjning|Sekunder|Maximal|Replik fördröjning i sekunder|Inget|
|pg_replica_log_delay_in_bytes|Maximal fördröjning mellan repliker|Byte|Maximal|Fördröjning i byte för den flesta isolerings repliken|Inget|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|memory_percent|Minnes procent|Procent|Medel|Minnes procent|Inget|
|IOPS|IOPS|Antal|Medel|I/o-åtgärder per sekund|Inget|
|storage_percent|Lagrings procent|Procent|Medel|Lagrings procent|Inget|
|storage_used|Använt lagringsutrymme|Byte|Medel|Använt lagringsutrymme|Inget|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inget|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Skicka försök för telemetri|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inget|
|d2c.telemetry.ingress.success|Skickade telemetrimeddelanden|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inget|
|C2D. commands. utgående. Complete. lyckades|C2D meddelande leveranser har slutförts|Antal|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Inget|
|c2d.commands.egress.abandon.success|Övergivna C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Inget|
|c2d.commands.egress.reject.success|Avvisade C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inget|
|C2DMessagesExpired|C2D meddelanden har förfallit (förhands granskning)|Antal|Totalt|Antal utgångna meddelanden från moln till enhet|Inget|
|devices.totalDevices|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inget|
|devices.connectedDevices.allProtocol|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inget|
|d2c.telemetry.egress.success|Routning: telemetri meddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inget|
|d2c.telemetry.egress.dropped|Routning: telemetri ignoreras |Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inget|
|d2c.telemetry.egress.orphaned|Routning: telemetri-meddelanden har överblivna |Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inget|
|d2c.telemetry.egress.invalid|Routning: telemetri-meddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inget|
|d2c.telemetry.egress.fallback|Routning: meddelanden levererade till reserv|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inget|
|d2c.endpoints.egress.eventHubs|Routning: meddelanden levererade till Händelsehubben|Antal|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inget|
|d2c.endpoints.latency.eventHubs|Routning: meddelandesvarstid för händelsehubb|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inget|
|d2c.endpoints.egress.serviceBusQueues|Routning: meddelanden levererade till Service Bus kö|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inget|
|d2c.endpoints.latency.serviceBusQueues|Routning: meddelandesvarstid för Service Bus-kö|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inget|
|d2c.endpoints.egress.serviceBusTopics|Routning: meddelanden levererade till Service Bus ämnet|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inget|
|d2c.endpoints.latency.serviceBusTopics|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inget|
|d2c.endpoints.egress.builtIn.events|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser).|Inget|
|d2c.endpoints.latency.builtIn.events|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser).|Inget|
|D2C. endpoints. utgående. Storage|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inget|
|d2c.endpoints.latency.storage|Routning: meddelande fördröjning för lagring|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inget|
|D2C. endpoints. utgående. Storage. byte|Routning: data som levererats till minne|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inget|
|d2c.endpoints.egress.storage.blobs|Routning: blobar som levererats till minne|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inget|
|EventGridDeliveries|Event Grid leveranser (för hands version)|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar typen av händelse (https://aka.ms/ioteventgrid).|ResourceId, resultat, EventType|
|EventGridLatency|Event Grid svars tid (för hands version)|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|ResourceId, EventType|
|d2c.twin.read.success|Lyckades dubbla läsningar från enheter|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inget|
|d2c.twin.read.failure|Misslyckade tvillingläsningar från enheter|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inget|
|d2c.twin.read.size|Svarsstorlek för tvillingläsningar från enheter|Byte|Medel|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inget|
|d2c.twin.update.success|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|d2c.twin.update.failure|Misslyckade tvillinguppdateringar från enheter|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|d2c.twin.update.size|Storlek på dubbla uppdateringar från enheter|Byte|Medel|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|c2d.methods.success|Direkta metod anrop|Antal|Totalt|Antalet lyckade direkta metod anrop.|Inget|
|C2D. Methods. Failure|Misslyckade direktmetodsanrop|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Inget|
|C2D. Methods. requestSize|Begäransstorlek för direktmetodsanrop|Byte|Medel|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inget|
|C2D. Methods. responseSize|Svarsstorlek för direktmetodsanrop|Byte|Medel|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inget|
|c2d.twin.read.success|Lyckades dubbla läspaket från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inget|
|c2d.twin.read.failure|Misslyckade tvillingläsningar från serverdel|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inget|
|c2d.twin.read.size|Svarsstorlek för tvillingläsningar från serverdel|Byte|Medel|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inget|
|c2d.twin.update.success|Lyckade tvillinguppdateringar från serverdel|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inget|
|c2d.twin.update.failure|Misslyckade tvillinguppdateringar från serverdel|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inget|
|c2d.twin.update.size|Storlek på dubbla uppdateringar från Server delen|Byte|Medel|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inget|
|twinQueries. lyckades|Lyckades dubbla frågor|Antal|Totalt|Antalet lyckade dubbla frågor.|Inget|
|twinQueries. Failure|Misslyckade tvillingfrågor|Antal|Totalt|Antalet misslyckade dubbla frågor.|Inget|
|twinQueries.resultSize|Resultat storlek för dubbla frågor|Byte|Medel|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inget|
|jobs.createTwinUpdateJob.success|Skapandet av dubbla uppdaterings jobb lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inget|
|jobs.createTwinUpdateJob.failure|Misslyckat skapande av tvillinguppdateringsjobb|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inget|
|jobs.createDirectMethodJob.success|Lyckade skapande av metod anrops jobb|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inget|
|jobs.createDirectMethodJob.failure|Misslyckat skapande av metodanropsjobb|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inget|
|jobs.listJobs.success|Lyckade anrop till List jobb|Antal|Totalt|Antalet lyckade anrop till List jobb.|Inget|
|Jobs. listJobs. Failure|Misslyckade anrop för att lista jobb|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Inget|
|Jobs. cancelJob. lyckades|Slutförda jobb avbokningar|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inget|
|Jobs. cancelJob. Failure|Misslyckade jobbannulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inget|
|Jobs. queryJobs. lyckades|Slutförda jobb frågor|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inget|
|Jobs. queryJobs. Failure|Misslyckade jobbfrågor|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inget|
|jobb. slutfört|Slutförda arbeten|Antal|Totalt|Antalet slutförda jobb.|Inget|
|jobb. misslyckades|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inget|
|d2c.telemetry.ingress.sendThrottle|Antal begränsningsfel|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inget|
|dailyMessageQuotaUsed|Totalt antal meddelanden som används|Antal|Medel|Antal sammanlagt antal meddelanden som använts idag|Inget|
|deviceDataUsage|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inget|
|deviceDataUsageV2|Total användning av enhets data (för hands version)|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inget|
|totalDeviceCount|Totalt antal enheter (förhands granskning)|Antal|Medel|Antal enheter som har registrerats för din IoT-hubb|Inget|
|connectedDeviceCount|Anslutna enheter (förhandsversion)|Antal|Medel|Antal enheter som är anslutna till din IoT-hubb|Inget|
|konfigurationer|Konfigurationsmått|Antal|Totalt|Mått för konfigurations åtgärder|Inget|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Registrerings försök|Antal|Totalt|Antal enhets registreringar som försökts|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Tilldelade enheter|Antal|Totalt|Antal enheter som har tilldelats till en IoT-hubb|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Attesterings försök|Antal|Totalt|Antal försök till enhets attestering|ProvisioningServiceName, status, protokoll|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AddRegion|Region tillagt|Antal|Antal|Region tillagt|Region|
|AvailableStorage|Tillgängligt lagringsutrymme|Byte|Totalt|Totalt tillgängligt lagrings utrymme som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|CassandraConnectionClosures|Cassandra-anslutningens stängningar|Antal|Totalt|Antalet Cassandra-anslutningar som stängdes, rapporteras med en 1 minuts kornig het|APIType, region, ClosureReason|
|CassandraRequestCharges|Avgifter för Cassandra-begäran|Antal|Totalt|Ru: er förbrukat för Cassandra begär Anden|APIType, DatabaseName, samlings region, OperationType, ResourceType|
|CassandraRequests|Cassandra-begäranden|Antal|Antal|Antal Cassandra-begäranden som gjorts|APIType, DatabaseName, samlings region, OperationType, ResourceType, ErrorCode|
|CreateAccount|Kontot har skapats|Antal|Antal|Kontot har skapats|Inget|
|DataUsage|Dataanvändning|Byte|Totalt|Total data användning rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|DeleteAccount|Kontot har tagits bort|Antal|Antal|Kontot har tagits bort|Inget|
|DocumentCount|Antal dokument|Antal|Totalt|Totalt antal dokument som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentQuota|Dokument kvot|Byte|Totalt|Total lagrings kvot som rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|IndexUsage|Index användning|Byte|Totalt|Total användning av index rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|MetadataRequests|Begär Anden om metadata|Antal|Antal|Antal metadata-begäranden. Cosmos DB behåller systemets metadata-samling för varje konto, vilket gör att du kan räkna upp samlingar, databaser osv och deras konfigurationer kostnads fritt.|DatabaseName, samlings namn, region, StatusCode, roll|
|MongoRequestCharge|Mongo begär ande avgift|Antal|Totalt|Mongo enheter för förbrukad begäran|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequests|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden som gjorts|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsCount|Mongo begär ande frekvens|CountPerSecond|Medel|Antal Mongo begär Anden per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsDelete|Mongo ta bort begär ande frekvens|CountPerSecond|Medel|Mongo ta bort begäran per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsInsert|Mongo infoga begär ande frekvens|CountPerSecond|Medel|Antal Mongo infogningar per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsQuery|Mongo för förfrågningar|CountPerSecond|Medel|Mongo-förfrågan per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsUpdate|Frekvens för mongo uppdaterings begär Anden|CountPerSecond|Medel|Mongo för begär ande uppdatering per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|ProvisionedThroughput|Etablerat dataflöde|Antal|Maximal|Etablerat dataflöde|DatabaseName, samlings namn|
|RegionFailover|Regionen har redundansväxlats|Antal|Antal|Regionen har redundansväxlats|Inget|
|RemoveRegion|Region borttagen|Antal|Antal|Region borttagen|Region|
|ReplicationLatency|Fördröjning för P99-replikering|Millisekunder|Medel|P99 för replikering i käll-och mål regioner för geo-aktiverat konto|SourceRegion,TargetRegion|
|ServiceAvailability|Tjänst tillgänglighet|Procent|Medel|Konto begär tillgänglighet på en timme, dag eller månads kornig het|Inget|
|TotalRequestUnits|Totalt antal enheter för programbegäran|Antal|Totalt|Förbrukade enheter för begär Ande|DatabaseName, samlings namn, region, StatusCode, OperationType|
|TotalRequests|Totalt antal förfrågningar|Antal|Antal|Antal begär Anden som gjorts|DatabaseName, samlings namn, region, StatusCode, OperationType|
|UpdateAccountKeys|Konto nycklar har uppdaterats|Antal|Antal|Konto nycklar har uppdaterats|KeyType|
|UpdateAccountNetworkSettings|Kontots nätverks inställningar har uppdaterats|Antal|Antal|Kontots nätverks inställningar har uppdaterats|Inget|
|UpdateAccountReplicationSettings|Kontots replikeringsinställningar har uppdaterats|Antal|Antal|Kontots replikeringsinställningar har uppdaterats|Inget|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/Services

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TransactionCount|Antal transaktioner|Antal|Antal|Totalt antal transaktioner|TransactionCount|
|SuccessCount|Antal slutförda|Antal|Antal|Antal genomförda transaktioner|SuccessCount|
|FailureCount|Antal misslyckade|Antal|Antal|Antal misslyckade transaktioner|FailureCount|
|SuccessLatency|Svars tid|Millisekunder|Medel|Svars tid för lyckade transaktioner|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Ämne|
|PublishFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|Ämne, ErrorType, fel|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inget|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medel|Varaktighet för mål bearbetning i millisekunder|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inget|
|PublishFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inget|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inget|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medel|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason,EventSubscriptionName|
|DeadLetteredCount|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inget|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inget|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medel|Varaktighet för mål bearbetning i millisekunder|Inget|
|DroppedEventCount|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|DeadLetteredCount|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inget|
|PublishFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inget|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inget|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|ServerErrors|Server fel.|Antal|Totalt|Server fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|UserErrors|Användar fel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|QuotaExceededErrors|Kvoten överskreds.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|ThrottledRequests|Begränsade begär Anden.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|IncomingRequests|Inkommande begär Anden|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|EntityName|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|EntityName|
|OutgoingMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|EntityName|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|EntityName|
|OutgoingBytes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Antal|Medel|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inget|
|ConnectionsOpened|Öppna anslutningar.|Antal|Medel|Anslutningar som har öppnats för Microsoft. EventHub.|EntityName|
|ConnectionsClosed|Stängda anslutningar.|Antal|Medel|Stängda anslutningar för Microsoft. EventHub.|EntityName|
|CaptureBacklog|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|EntityName|
|CapturedMessages|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|EntityName|
|CapturedBytes|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|EntityName|
|Storlek|Storlek|Byte|Medel|Storlek på en EventHub i byte.|EntityName|
|INREQS|Inkommande begär Anden (inaktuellt)|Antal|Totalt|Totalt antal inkommande sändnings begär Anden för en namnrymd (inaktuell)|Inget|
|SUCCREQ|Lyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal slutförda begär Anden för en namnrymd (inaktuell)|Inget|
|FAILREQ|Misslyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inget|
|SVRBSY|Serverns upptaget fel (inaktuellt)|Antal|Totalt|Totalt antal upptagen server-fel för en namnrymd (inaktuell)|Inget|
|MELLAN|Interna Server fel (inaktuellt)|Antal|Totalt|Totalt antal interna Server fel för en namnrymd (inaktuell)|Inget|
|MISCERR|Andra fel (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inget|
|INMSGS|Inkommande meddelanden (inaktuella)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namn område. Måttet är föråldrat. Använd måtten inkommande e-postmeddelanden i stället (inaktuellt)|Inget|
|EHINMSGS|Inkommande meddelanden (inaktuellt)|Antal|Totalt|Totalt antal inkommande meddelanden för en namnrymd (inaktuell)|Inget|
|OUTMSGS|Utgående meddelanden (inaktuella)|Antal|Totalt|Totalt antal utgående meddelanden för ett namn område. Måttet är föråldrat. Använd utgående meddelande mått i stället (inaktuellt)|Inget|
|EHOUTMSGS|Utgående meddelanden (inaktuellt)|Antal|Totalt|Totalt antal utgående meddelanden för en namnrymd (inaktuell)|Inget|
|EHINMBS|Inkommande byte (inaktuell) (inaktuell)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för ett namn område. Måttet är föråldrat. Använd måttet inkommande byte i stället (inaktuellt)|Inget|
|EHINBYTES|Inkommande byte (inaktuellt)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för en namnrymd (inaktuell)|Inget|
|EHOUTMBS|Utgående byte (inaktuell) (inaktuell)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd. Måttet är föråldrat. Använd utgående byte-mått i stället (inaktuellt)|Inget|
|EHOUTBYTES|Utgående byte (inaktuellt)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd (inaktuell)|Inget|
|EHABL|Arkivera efter släpning meddelanden (inaktuellt)|Antal|Totalt|Event Hub arkivera meddelanden i efter släpning för en namnrymd (inaktuell)|Inget|
|EHAMSGS|Arkivera meddelanden (inaktuellt)|Antal|Totalt|Arkiverade meddelanden i Event Hub i ett namn område (inaktuellt)|Inget|
|EHAMBS|Arkiv meddelande genom strömning (inaktuellt)|Byte|Totalt|Det arkiverade meddelande flödet i Event Hub i ett namn område (inaktuellt)|Inget|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|OperationResult under pågående|
|ServerErrors|Server fel.|Antal|Totalt|Server fel för Microsoft. EventHub.|OperationResult under pågående|
|UserErrors|Användar fel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|OperationResult under pågående|
|QuotaExceededErrors|Kvoten överskreds.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|OperationResult under pågående|
|ThrottledRequests|Begränsade begär Anden.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|OperationResult under pågående|
|IncomingRequests|Inkommande begär Anden|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Inget|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Inget|
|OutgoingMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|Inget|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Inget|
|OutgoingBytes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Inget|
|ActiveConnections|ActiveConnections|Antal|Medel|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inget|
|ConnectionsOpened|Öppna anslutningar.|Antal|Medel|Anslutningar som har öppnats för Microsoft. EventHub.|Inget|
|ConnectionsClosed|Stängda anslutningar.|Antal|Medel|Stängda anslutningar för Microsoft. EventHub.|Inget|
|CaptureBacklog|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Inget|
|CapturedMessages|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|Inget|
|CapturedBytes|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Inget|
|Processor|Processor|Procent|Maximal|CPU-användning för Event Hub-klustret i procent|Roll|
|AvailableMemory|Tillgängligt minne|Procent|Maximal|Tillgängligt minne för Event Hub-klustret som en procent andel av det totala minnet.|Roll|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kluster

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden|HttpStatus|
|CategorizedGatewayRequests|Kategoriserade Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden per kategori (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Antal aktiva arbetare|Antal|Maximal|Antal aktiva arbetare|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerat mått värde|Antal|Medel|Det värde som beräknas genom autoskalning vid körning|MetricTriggerSource|
|MetricThreshold|Mätnings tröskel|Antal|Medel|Det konfigurerade tröskelvärdet för autoskalning vid autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Observerad kapacitet|Antal|Medel|Kapaciteten som rapporteras till autoskalning när den kördes.|Inget|
|ScaleActionsInitiated|Initierade skalnings åtgärder|Antal|Totalt|Riktningen för skalnings åtgärden.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Tillgänglighet|Procent|Medel|Procent slutförda tillgänglighets test har slutförts|availabilityResult/namn, availabilityResult/plats|
|availabilityResults/antal|Tillgänglighetstester|Antal|Antal|Antal tillgänglighets test|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|availabilityResults/varaktighet|Tillgänglighets testets varaktighet|Millisekunder|Medel|Tillgänglighets testets varaktighet|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|browserTimings/networkDuration|Nätverks anslutnings tid för sid inläsning|Millisekunder|Medel|Tid mellan användar förfrågan och nätverks anslutning. Inkluderar DNS-sökning och transport anslutning.|Inget|
|browserTimings/processingDuration|Klientbehandlingstid|Millisekunder|Medel|Tiden mellan att ta emot sista byten i ett dokument tills DOM har lästs in. Asynkrona begär Anden kan fortfarande bearbetas.|Inget|
|browserTimings/receiveDuration|Tar emot svars tid|Millisekunder|Medel|Tiden mellan den första och sista byten, eller till från koppling.|Inget|
|browserTimings/sendDuration|Tid för att skicka begäran|Millisekunder|Medel|Tiden mellan nätverks anslutning och mottagande av den första byten.|Inget|
|browserTimings/totalDuration|Sid inläsnings tid för webbläsare|Millisekunder|Medel|Tid från användar förfrågan tills DOM, formatmallar, skript och bilder har lästs in.|Inget|
|beroenden/antal|Beroende anrop|Antal|Antal|Antal anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, operation/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/varaktighet|Beroende varaktighet|Millisekunder|Medel|Varaktigheten för anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, operation/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/misslyckades|Beroende anrops problem|Antal|Antal|Antal misslyckade beroende anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, operation/syntetisk, Cloud/roleInstance, Cloud/roleName|
|pageViews/antal|Sid visningar|Antal|Antal|Antal sid visningar.|drift/syntetisk, Cloud/roleName|
|pageViews/varaktighet|Inläsnings tid för sid visning|Millisekunder|Medel|Inläsnings tid för sid visning|drift/syntetisk, Cloud/roleName|
|performanceCounters/requestExecutionTime|Körnings tid för HTTP-begäran|Millisekunder|Medel|Körnings tid för den senaste begäran.|Cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-begäranden i program kön|Antal|Medel|Längden på program begär ande kön.|Cloud/roleInstance|
|performanceCounters/requestsPerSecond|Hastighet för HTTP-begäran|CountPerSecond|Medel|Takten för alla förfrågningar till programmet per sekund från ASP.NET.|Cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Undantags frekvens|CountPerSecond|Medel|Antal hanterade och ohanterade undantag som rapporter ATS till Windows, inklusive .NET-undantag och ohanterade undantag som konverterats till .NET-undantag.|Cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process-IO-hastighet|BytesPerSecond|Medel|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|Cloud/roleInstance|
|performanceCounters/processCpuPercentage|Processor-CPU|Procent|Medel|Procent andelen av förfluten tid som alla process trådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger enbart prestanda för W3wp-processen.|Cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor tid|Procent|Medel|Den procent andel av tiden som processorn ägnat åt icke-inaktiva trådar.|Cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Tillgängligt minne|Byte|Medel|Fysiskt minne som är omedelbart tillgängligt för allokering till en process eller för system användning.|Cloud/roleInstance|
|performanceCounters/processPrivateBytes|Privata byte för process|Byte|Medel|Minne som tilldelats exklusivt för de övervakade program processerna.|Cloud/roleInstance|
|begär Anden/varaktighet|Serversvarstid|Millisekunder|Medel|Tiden mellan att ta emot en HTTP-förfrågan och avsluta sändningen av svaret.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/antal|Server begär Anden|Antal|Antal|Antal slutförda HTTP-förfrågningar.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/misslyckade|Misslyckade förfrågningar|Antal|Antal|Antal HTTP-begäranden som marker ATS som misslyckade. I de flesta fall är dessa förfrågningar med svars kod > = 400 och inte lika med 401.|begäran/performanceBucket, begäran/resultCode, begäran/framgång, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|begär Anden/pris|Server begär ande frekvens|CountPerSecond|Medel|Antal server begär Anden per sekund|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|undantag/antal|Undantag|Antal|Antal|Sammanlagt antal undantag som inte har fångats.|Cloud/roleName, Cloud/roleInstance, Client/Type|
|undantag/webbläsare|Webbläsarundantag|Antal|Antal|Antal ej fångade undantag som har utlösts i webbläsaren.|klient-isServer, Cloud/roleName|
|undantag/Server|Server undantag|Antal|Antal|Antal ej fångade undantag som har utlösts i serverprogrammet.|klient-isServer, Cloud/roleName, Cloud/roleInstance|
|spårning/antal|Spårningar|Antal|Antal|Spårnings dokument antal|Trace/severityLevel, operation/syntetisk, Cloud/roleName, Cloud/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Totalt antal tjänst-API-träffar|Antal|Antal|Antal totala service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Övergripande service API-latens|Millisekunder|Medel|Övergripande svars tid för service API-begäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Totalt antal service API-resultat|Antal|Antal|Antal totala service API-resultat|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Övergripande valv beläggning|Procent|Medel|Valv kapacitet som används|ActivityType, ActivityName, TransactionType|
|Tillgänglighet|Övergripande valv tillgänglighet|Procent|Medel|Tillgänglighet för valv begär Anden|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CacheUtilization|Användning av cache|Procent|Medel|Användnings nivå i kluster omfånget|Inget|
|QueryDuration|Frågevaraktighet|Millisekunder|Medel|Frågornas varaktighet i sekunder|QueryStatus|
|IngestionUtilization|Förbruknings användning|Procent|Medel|Förhållandet mellan använda inmatnings platser i klustret|Inget|
|KeepAlive|Behåll Alive|Antal|Medel|Sanity check anger att klustret svarar på frågor|Inget|
|IngestionVolumeInMB|Inmatnings volym (i MB)|Antal|Totalt|Total mängd inmatade data till klustret (i MB)|Databas|
|IngestionLatencyInSeconds|Inmatnings svars tid (i sekunder)|Sekunder|Medel|Hämtnings tiden från källan (t. ex. meddelandet är i EventHub) till klustret på några sekunder|Inget|
|EventsProcessedForEventHubs|Bearbetade händelser (för Event/IoT-hubbar)|Antal|Totalt|Antal händelser som bearbetas av klustret vid inmatning från händelse/IoT Hub|EventStatus|
|IngestionResult|Inmatnings resultat|Antal|Antal|Antal inmatnings åtgärder|IngestionResultDetails|
|Processor|Processor|Procent|Medel|PROCESSOR användnings nivå|Inget|
|ContinuousExportNumOfRecordsExported|Kontinuerlig export – antal exporterade poster|Antal|Totalt|Antal exporterade poster, utlöst för varje lagrings artefakt som skrivits under export åtgärden|Inget|
|ExportUtilization|Exportera användning|Procent|Maximal|Exportera användning|Inget|
|ContinuousExportPendingCount|Antal väntande pågående export|Antal|Maximal|Antal väntande kontinuerliga export jobb som är klara för körning|Inget|
|ContinuousExportMaxLatenessMinutes|Högsta antal förseningar i minuter för kontinuerlig export|Antal|Maximal|Maximal försening i minuter för alla kontinuerliga exporter som väntar och är redo att köras|Inget|
|ContinuousExportResult|Resultat av kontinuerlig export|Antal|Antal|Anger om kontinuerlig export lyckades eller misslyckades|ContinuousExportName, resultat, databas|
|StreamingIngestDuration|Hämtnings tid för strömning|Millisekunder|Medel|Hämtnings tid för strömning i millisekunder|Inget|
|StreamingIngestDataRate|Data hastighet för strömnings intag|Antal|Medel|Strömnings data hastighet (MB per sekund)|Inget|
|SteamingIngestRequestRate|Begär ande frekvens för strömning|Antal|RateRequestsPerSecond|Hastighet för strömnings inmatnings begäran (begär Anden per sekund)|Inget|
|StreamingIngestResults|Resultat av strömnings inmatning|Antal|Medel|Resultat av strömnings inmatning|Resultat|
|TotalNumberOfConcurrentQueries|Totalt antal samtidiga frågor|Antal|Totalt|Totalt antal samtidiga frågor|Inget|
|TotalNumberOfThrottledQueries|Totalt antal begränsade frågor|Antal|Totalt|Totalt antal begränsade frågor|Inget|
|TotalNumberOfThrottledCommands|Totalt antal begränsade kommandon|Antal|Totalt|Totalt antal begränsade kommandon|CommandType|
|TotalNumberOfExtents|Totalt antal omfattningar|Antal|Totalt|Totalt antal data omfattningar|Inget|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inget|
|RunsCompleted|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inget|
|RunsSucceeded|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inget|
|RunsFailed|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inget|
|RunsCancelled|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inget|
|RunLatency|Körnings fördröjning|Sekunder|Medel|Svars tiden för slutförda arbets flödes körningar.|Inget|
|RunSuccessLatency|Kör svars tid|Sekunder|Medel|Svars tiden för slutfört arbets flöde körs.|Inget|
|RunThrottledEvents|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inget|
|RunStartThrottledEvents|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inget|
|RunFailurePercentage|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inget|
|ActionsStarted|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inget|
|ActionsCompleted|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inget|
|ActionsSucceeded|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inget|
|ActionsFailed|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inget|
|ActionsSkipped|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inget|
|ActionLatency|Åtgärds svars tid |Sekunder|Medel|Svars tid för slutförda arbets flödes åtgärder.|Inget|
|ActionSuccessLatency|Svars tid för åtgärd |Sekunder|Medel|Svars tid för slutförda arbets flödes åtgärder.|Inget|
|ActionThrottledEvents|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inget|
|TriggersStarted|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inget|
|TriggersCompleted|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inget|
|TriggersSucceeded|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inget|
|TriggersFailed|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inget|
|TriggersSkipped|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inget|
|TriggersFired|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inget|
|TriggerLatency|Utlös fördröjning |Sekunder|Medel|Latens för slutförda arbets flödes utlösare.|Inget|
|TriggerFireLatency|Utlös brand fördröjning |Sekunder|Medel|Fördröjning för utlösta arbets flödes utlösare.|Inget|
|TriggerSuccessLatency|Utlös svars tid för lyckade |Sekunder|Medel|Svars tid för lyckade arbets flödes utlösare.|Inget|
|TriggerThrottledEvents|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inget|
|BillableActionExecutions|Fakturerbara åtgärds körningar|Antal|Totalt|Antal körningar av arbets flödes åtgärder som faktureras.|Inget|
|BillableTriggerExecutions|Fakturerbara Utlösar-körningar|Antal|Totalt|Antal körningar av arbets flödes utlösare som faktureras.|Inget|
|TotalBillableExecutions|Totalt antal fakturerbara körningar|Antal|Totalt|Antal arbets flödes körningar som faktureras.|Inget|
|BillingUsageNativeOperation|Fakturerings användning för intern åtgärds körningar|Antal|Totalt|Antal körningar av interna åtgärder som debiteras.|Inget|
|BillingUsageStandardConnector|Fakturerings användning för standard kopplings körningar|Antal|Totalt|Antal standard kopplings körningar som faktureras.|Inget|
|BillingUsageStorageConsumption|Fakturerings användning för lagrings förbruknings körningar|Antal|Totalt|Antal lagrings förbruknings körningar som faktureras.|Inget|
|BillingUsageNativeOperation|Fakturerings användning för intern åtgärds körningar|Antal|Totalt|Antal körningar av interna åtgärder som debiteras.|Inget|
|BillingUsageStandardConnector|Fakturerings användning för standard kopplings körningar|Antal|Totalt|Antal standard kopplings körningar som faktureras.|Inget|
|BillingUsageStorageConsumption|Fakturerings användning för lagrings förbruknings körningar|Antal|Totalt|Antal lagrings förbruknings körningar som faktureras.|Inget|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inget|
|RunsCompleted|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inget|
|RunsSucceeded|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inget|
|RunsFailed|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inget|
|RunsCancelled|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inget|
|RunLatency|Körnings fördröjning|Sekunder|Medel|Svars tiden för slutförda arbets flödes körningar.|Inget|
|RunSuccessLatency|Kör svars tid|Sekunder|Medel|Svars tiden för slutfört arbets flöde körs.|Inget|
|RunThrottledEvents|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inget|
|RunStartThrottledEvents|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inget|
|RunFailurePercentage|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inget|
|ActionsStarted|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inget|
|ActionsCompleted|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inget|
|ActionsSucceeded|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inget|
|ActionsFailed|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inget|
|ActionsSkipped|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inget|
|ActionLatency|Åtgärds svars tid |Sekunder|Medel|Svars tid för slutförda arbets flödes åtgärder.|Inget|
|ActionSuccessLatency|Svars tid för åtgärd |Sekunder|Medel|Svars tid för slutförda arbets flödes åtgärder.|Inget|
|ActionThrottledEvents|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inget|
|TriggersStarted|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inget|
|TriggersCompleted|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inget|
|TriggersSucceeded|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inget|
|TriggersFailed|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inget|
|TriggersSkipped|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inget|
|TriggersFired|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inget|
|TriggerLatency|Utlös fördröjning |Sekunder|Medel|Latens för slutförda arbets flödes utlösare.|Inget|
|TriggerFireLatency|Utlös brand fördröjning |Sekunder|Medel|Fördröjning för utlösta arbets flödes utlösare.|Inget|
|TriggerSuccessLatency|Utlös svars tid för lyckade |Sekunder|Medel|Svars tid för lyckade arbets flödes utlösare.|Inget|
|TriggerThrottledEvents|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inget|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Användning av arbets flödes processor för Integration Service Environment|Procent|Medel|Användning av arbets flödes processor för integrerings tjänst miljö.|Inget|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Minnes användning för arbets flöde för Integration Service Environment|Procent|Medel|Arbets flödets minnes användning för integrerings tjänst miljön.|Inget|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Anslutnings processor användning för Integration Service Environment|Procent|Medel|Anslutnings processor användning för integrerings tjänst miljön.|Inget|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Anslutnings minnes användning för Integration Service Environment|Procent|Medel|Anslutnings minnes användning för integrerings tjänst miljön.|Inget|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Slutförda körningar|Slutförda körningar|Antal|Totalt|Antal körningar som har slutförts för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Startade körningar|Startade körningar|Antal|Totalt|Antal körningar som har startats för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Misslyckade körningar|Misslyckade körningar|Antal|Totalt|Antalet körningar som misslyckades för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Modell registreringen har slutförts|Modell registreringen har slutförts|Antal|Totalt|Antal modell registreringar som har slutförts på den här arbets ytan|Scenario|
|Modell registreringen misslyckades|Modell registreringen misslyckades|Antal|Totalt|Antal modell registreringar som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Modelldistribution Startad|Modelldistribution Startad|Antal|Totalt|Antal modell distributioner som startats på den här arbets ytan|Scenario|
|Modelldistribution lyckades|Modelldistribution lyckades|Antal|Totalt|Antal modell distributioner som har slutförts på den här arbets ytan|Scenario|
|Modelldistribution misslyckades|Modelldistribution misslyckades|Antal|Totalt|Antal modell distributioner som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Totalt antal noder|Totalt antal noder|Antal|Medel|Totalt antal noder. Den här summan innehåller några aktiva noder, inaktiva noder, oanvändbara noder, Premepted noder, lämnar noder|Scenario, kluster namn|
|Aktiva noder|Aktiva noder|Antal|Medel|Antal Active-noder. Detta är de noder som aktivt kör ett jobb.|Scenario, kluster namn|
|Inaktiva noder|Inaktiva noder|Antal|Medel|Antal inaktiva noder. Inaktiva noder är noder som inte kör några jobb, men som kan ta emot nya jobb om de är tillgängliga.|Scenario, kluster namn|
|Oanvändbara noder|Oanvändbara noder|Antal|Medel|Antal oanvändbara noder. Oanvändbara noder fungerar inte på grund av ett problem som inte kan matchas. De här noderna återanvänds av Azure.|Scenario, kluster namn|
|Misslyckade noder|Misslyckade noder|Antal|Medel|Antal noder som har åsidosatts. De här noderna är de noder med låg prioritet som tas bort från den tillgängliga Node-poolen.|Scenario, kluster namn|
|Lämnar noder|Lämnar noder|Antal|Medel|Antal noder som lämnar. Att lämna noder är de noder som precis har avslutat bearbetningen av ett jobb och kommer att gå in i inaktivt läge.|Scenario, kluster namn|
|Totalt antal kärnor|Totalt antal kärnor|Antal|Medel|Antal total kärnor|Scenario, kluster namn|
|Aktiva kärnor|Aktiva kärnor|Antal|Medel|Antal aktiva kärnor|Scenario, kluster namn|
|Inaktiva kärnor|Inaktiva kärnor|Antal|Medel|Antal inaktiva kärnor|Scenario, kluster namn|
|Oanvändbara kärnor|Oanvändbara kärnor|Antal|Medel|Antal oanvändbara kärnor|Scenario, kluster namn|
|Blockerade kärnor|Blockerade kärnor|Antal|Medel|Antal blockerade kärnor|Scenario, kluster namn|
|Lämnar kärnor|Lämnar kärnor|Antal|Medel|Antal lämnar kärnor|Scenario, kluster namn|
|Kvot användning i procent|Kvot användning i procent|Antal|Medel|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Antal|Medel|PROCESSOR (för hands version)|Scenario, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Antal|Medel|GPU (för hands version)|Scenario, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konton

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Antal|Antal|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Tillgänglighet för API: erna|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Media Services/strömnings slut punkter

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte.|OutputFormat|
|SuccessE2ELatency|Slutför svars tid för slut punkt till slut punkt|Millisekunder|Medel|Genomsnittlig svars tid för lyckade begär anden i millisekunder.|OutputFormat|
|Begäranden|Begäranden|Antal|Totalt|Begär anden till en slut punkt för direkt uppspelning.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AssetQuota|Till gångs kvot|Antal|Medel|Hur många till gångar som tillåts för det aktuella Media Service-kontot|Inget|
|AssetCount|Antal till gångar|Antal|Medel|Hur många till gångar som redan har skapats i det aktuella medie tjänst kontot|Inget|
|AssetQuotaUsedPercentage|Använd procent andel till till gångs kvot|Procent|Medel|Procent andel till gång som används i det aktuella medie tjänst kontot|Inget|
|ContentKeyPolicyQuota|Kvot för innehålls nyckel princip|Antal|Medel|Hur många innehålls nyckel principer som tillåts för det aktuella Media Service-kontot|Inget|
|ContentKeyPolicyCount|Antal nyckel principer för innehåll|Antal|Medel|Hur många innehålls nyckel principer som redan har skapats i det aktuella medie tjänst kontot|Inget|
|ContentKeyPolicyQuotaUsedPercentage|Procent andel av nyckel princip för innehåll|Procent|Medel|Procent andel av nyckel princip för innehåll i det aktuella medie tjänst kontot|Inget|
|StreamingPolicyQuota|Kvot för strömmande princip|Antal|Medel|Hur många strömmande principer som tillåts för det aktuella Media Service-kontot|Inget|
|StreamingPolicyCount|Antal strömmande principer|Antal|Medel|Hur många strömmande principer som redan har skapats i det aktuella medie tjänst kontot|Inget|
|StreamingPolicyQuotaUsedPercentage|Använd procent andel av princip för strömning|Procent|Medel|Procentuell strömnings princip som används i det aktuella medie tjänst kontot|Inget|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageReadLatency|Genomsnittlig Läs fördröjning|Millisekunder|Medel|Genomsnittlig Läs fördröjning i millisekunder per åtgärd|Inget|
|AverageWriteLatency|Genomsnittlig Skriv fördröjning|Millisekunder|Medel|Genomsnittlig Skriv fördröjning i millisekunder per åtgärd|Inget|
|VolumeLogicalSize|Logisk volym storlek|Byte|Medel|Den logiska storleken på volymen (använda byte)|Inget|
|VolumeSnapshotSize|Storlek på volym ögonblicks bild|Byte|Medel|Storlek på alla ögonblicks bilder i volymen|Inget|
|ReadIops|Läs IOPS|CountPerSecond|Medel|Läs-/ut-åtgärder per sekund|Inget|
|WriteIops|Skriv IOPS|CountPerSecond|Medel|Skriv in/ut-åtgärder per sekund|Inget|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Allokerad volym pool används|Byte|Medel|Allokerad använt storlek på poolen|Inget|
|VolumePoolTotalLogicalSize|Total logisk storlek i volymprocent|Byte|Medel|Summan av den logiska storleken för alla volymer som tillhör poolen|Inget|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesSentRate|Skickade byte|Byte|Totalt|Antal byte som nätverks gränssnittet har skickats|Inget|
|BytesReceivedRate|Mottagna byte|Byte|Totalt|Antal byte som nätverks gränssnittet har tagits emot|Inget|
|PacketsSentRate|Skickade paket|Antal|Totalt|Antal paket som nätverks gränssnittet har skickats|Inget|
|PacketsReceivedRate|Mottagna paket|Antal|Totalt|Antal paket som nätverks gränssnittet har fått|Inget|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|Tillgänglighet för data Sök väg|Antal|Medel|Genomsnittlig tillgänglighet för Load Balancer data Sök väg per tids längd|FrontendIPAddress,FrontendPort|
|DipAvailability|Status för hälso avsökning|Antal|Medel|Genomsnittlig status för Load Balancer hälso avsökning per tids period|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som skickats inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tids perioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allokerade SNAT-portar (förhands granskning)|Antal|Totalt|Totalt antal SNAT-portar som allokerats inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Använda SNAT-portar (förhands granskning)|Antal|Totalt|Totalt antal SNAT-portar som använts inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Fråga volym|Antal|Totalt|Antal frågor som hanteras för en DNS-zon|Inget|
|RecordSetCount|Antal post uppsättningar|Antal|Maximal|Antal post uppsättningar i en DNS-zon|Inget|
|RecordSetCapacityUtilization|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en DNS-zon|Inget|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PacketsInDDoS|Inkommande paket DDoS|CountPerSecond|Maximal|Inkommande paket DDoS|Inget|
|PacketsDroppedDDoS|Inkommande paket som förlorats DDoS|CountPerSecond|Maximal|Inkommande paket som förlorats DDoS|Inget|
|PacketsForwardedDDoS|Inkommande paket som vidarekopplats DDoS|CountPerSecond|Maximal|Inkommande paket som vidarekopplats DDoS|Inget|
|TCPPacketsInDDoS|DDoS inkommande TCP-paket|CountPerSecond|Maximal|DDoS inkommande TCP-paket|Inget|
|TCPPacketsDroppedDDoS|Inkommande TCP-paket ignorerade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket ignorerade DDoS|Inget|
|TCPPacketsForwardedDDoS|Inkommande TCP-paket, vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket, vidarebefordrade DDoS|Inget|
|UDPPacketsInDDoS|DDoS för inkommande UDP-paket|CountPerSecond|Maximal|DDoS för inkommande UDP-paket|Inget|
|UDPPacketsDroppedDDoS|Ignorerade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Ignorerade inkommande UDP-paket DDoS|Inget|
|UDPPacketsForwardedDDoS|Vidarebefordrade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande UDP-paket DDoS|Inget|
|BytesInDDoS|Inkommande byte DDoS|BytesPerSecond|Maximal|Inkommande byte DDoS|Inget|
|BytesDroppedDDoS|Inkommande byte som förlorats DDoS|BytesPerSecond|Maximal|Inkommande byte som förlorats DDoS|Inget|
|BytesForwardedDDoS|Inkommande byte som vidarekopplats DDoS|BytesPerSecond|Maximal|Inkommande byte som vidarekopplats DDoS|Inget|
|TCPBytesInDDoS|Inkommande TCP-byte DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte DDoS|Inget|
|TCPBytesDroppedDDoS|Inkommande TCP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte utelämnade DDoS|Inget|
|TCPBytesForwardedDDoS|Inkommande TCP byte-vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande TCP byte-vidarebefordrade DDoS|Inget|
|UDPBytesInDDoS|DDoS för inkommande UDP-byte|BytesPerSecond|Maximal|DDoS för inkommande UDP-byte|Inget|
|UDPBytesDroppedDDoS|Inkommande UDP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte utelämnade DDoS|Inget|
|UDPBytesForwardedDDoS|Inkommande UDP byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande UDP byte vidarebefordrade DDoS|Inget|
|IfUnderDDoSAttack|Under DDoS-attack eller inte|Antal|Maximal|Under DDoS-attack eller inte|Inget|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-minskning|Inget|
|DDoSTriggerUDPPackets|Ingående UDP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Ingående UDP-paket för att utlösa DDoS-minskning|Inget|
|DDoSTriggerSYNPackets|Inkommande SYN-paket för att utlösa DDoS-riskreducering|CountPerSecond|Maximal|Inkommande SYN-paket för att utlösa DDoS-riskreducering|Inget|
|VipAvailability|Tillgänglighet för data Sök väg|Antal|Medel|Genomsnittlig tillgänglighet för IP-adress per tids längd|Port|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som skickats inom tids perioden|Port, riktning|
|PacketCount|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|Port, riktning|
|SynCount|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|Port, riktning|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Fördröjning för ping till en virtuell dator|Millisekunder|Medel|Tur och retur-tid för pingar som skickas till en virtuell måldator|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Misslyckade pingar till en virtuell dator|Procent|Medel|Procent av antalet misslyckade pingar till totalt antal skickade pingar för en virtuell måldator|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ApplicationRuleHit|Antal träffar för program regler|Antal|Totalt|Antal gånger som program regler träffades|Status, orsak, protokoll|
|NetworkRuleHit|Antal träffar för nätverks regler|Antal|Totalt|Antal gånger som nätverks regler träffades|Status, orsak, protokoll|
|FirewallHealth|Hälso tillstånd för brand vägg|Procent|Medel|Hälso tillstånd för brand vägg|Status, orsak|
|DataProcessed|Bearbetade data|Byte|Totalt|Total mängd data som bearbetas av brand väggen|Inget|
|SNATPortUtilization|SNAT-port användning|Procent|Medel|SNAT-port användning|Inget|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Medel|Antal byte per sekund som Application Gateway har betjänat|Inget|
|UnhealthyHostCount|Antal felaktiga värdar|Antal|Medel|Antal värdar för skadade Server delar|BackendSettingsPool|
|HealthyHostCount|Antal felfria värdar|Antal|Medel|Antal felfria Server dels värdar|BackendSettingsPool|
|TotalRequests|Totalt antal förfrågningar|Antal|Totalt|Antal lyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Begär Anden per minut per felfri värd|Antal|Medel|Genomsnittligt antal begär Anden per minut per felfri backend-värd i en pool|BackendSettingsPool|
|FailedRequests|Misslyckade begäranden|Antal|Totalt|Antal misslyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|ResponseStatus|Svars status|Antal|Totalt|Http-svarets status returnerades av Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inget|
|CpuUtilization|PROCESSOR användning|Procent|Medel|Aktuell processor användning för Application Gateway|Inget|
|CapacityUnits|Aktuella kapacitets enheter|Antal|Medel|Förbrukade kapacitets enheter|Inget|
|ComputeUnits|Aktuella beräknings enheter|Antal|Medel|Förbrukade beräknings enheter|Inget|
|BackendResponseStatus|Svars status för Server del|Antal|Totalt|Antalet HTTP-svars koder som genereras av Server dels medlemmar. Detta omfattar inte några svars koder som genereras av Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Klientens TLS-protokoll|Antal|Totalt|Antalet TLS-och icke-TLS-begäranden som initieras av klienten som upprättade anslutningen till Application Gateway. Om du vill visa TLS-protokollets distribution filtrerar du efter dimension TLS-protokollet.|Lyssnare, TlsProtocol|
|Bytes sent|Skickade byte|Byte|Totalt|Det totala antalet byte som har skickats av Application Gateway till klienterna|Lyssnare|
|BytesReceived|Mottagna byte|Byte|Totalt|Det totala antalet byte som tagits emot av Application Gateway från klienterna|Lyssnare|
|ClientRtt|Klient-/klient|Millisekunder|Medel|Genomsnittlig fördröjning mellan klienter och Application Gateway. Det här måttet anger hur lång tid det tar att upprätta anslutningar och retur bekräftelser|Lyssnare|
|ApplicationGatewayTotalTime|Application Gateway total tid|Millisekunder|Medel|Genomsnittlig tid det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som genomsnitt av intervallet från den tid då Application Gateway tar emot den första byten av en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att detta vanligt vis omfattar Application Gateway bearbetnings tid, tid då paket för begäran och svar överförs över nätverket och hur lång tid det tog att svara på backend-servern.|Lyssnare|
|BackendConnectTime|Server dels anslutnings tid|Millisekunder|Medel|Åtgången tid för att upprätta en anslutning till en backend-server|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|Svars tid för första byte för Server del|Millisekunder|Medel|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den första byten i svars huvudet, ungefär bearbetnings tiden för backend-servern|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|Svars tid för senaste byte för Server delen|Millisekunder|Medel|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den sista byten i svars texten|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|MatchedCount|Brand vägg för total regel distribution i webb program|Antal|Totalt|Brand vägg för webb program, total regel distribution för inkommande trafik|RuleGroup, RuleId|
|BlockedCount|Webb program brand vägg blockerade begär Anden regel distribution|Antal|Totalt|Webb program brand vägg blockerade begär Anden regel distribution|RuleGroup, RuleId|
|BlockedReqCount|Antal blockerade förfrågningar för webb program brand vägg|Antal|Totalt|Antal blockerade förfrågningar för webb program brand vägg|Inget|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageBandwidth|Gatewayens S2S-bandbredd|BytesPerSecond|Medel|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inget|
|P2SBandwidth|Gateway P2S-bandbredd|BytesPerSecond|Medel|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inget|
|P2SConnectionCount|Antal P2S-anslutningar|Antal|Maximal|Antal anslutningar punkt-till-plats för en gateway|Protokoll|
|TunnelAverageBandwidth|Tunnel bandbredd|BytesPerSecond|Medel|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Utgående byte i tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Ingångs byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Utgående tunnel paket|Antal|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Inkommande tunnel paket|Antal|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Ignorerade TS-matchnings paket för tunnel|Antal|Totalt|Utgående paket-antal från trafik väljare matchnings fel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel om ingångs paket för TS-matchningsfel|Antal|Totalt|Inkommande paket Drop Count från trafik väljarens matchnings fel i en tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Antal|Medel|RX ljus nivå i dBm|Länk, Lane|
|TxLightLevel|TxLightLevel|Antal|Medel|TX-ljusnivå i dBm|Länk, Lane|
|AdminState|AdminState|Antal|Medel|Administratörs tillstånd för porten|Länk|
|LineProtocol|LineProtocol|Antal|Medel|Status för linje protokoll för porten|Länk|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS inkommande Azure per sekund|Länk|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS utgående Azure per sekund|Länk|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS inkommande Azure per sekund|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS utgående Azure per sekund|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Medel|BITS inkommande Azure per sekund|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Medel|BITS utgående Azure per sekund|PeeredCircuitSKey|
|BgpAvailability|BGP-tillgänglighet|Procent|Medel|BGP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|ArpAvailability|ARP-tillgänglighet|Procent|Medel|ARP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Medel|Ingress BITS av data som ignoreras per sekund|Inget|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Medel|Utgående bitar med data som ignoreras per sekund|Inget|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS inkommande Azure per sekund|Inget|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS utgående Azure per sekund|Inget|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS inkommande Azure per sekund|Inget|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS utgående Azure per sekund|Inget|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|BITS inkommande Azure per sekund|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|BITS utgående Azure per sekund|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Returnerade frågor efter slut punkt|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den aktuella tids ramen|endpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Slut punkts status efter slut punkt|Antal|Maximal|1 om avsöknings status för en slut punkt är "aktive rad", annars 0.|endpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Avsökningar misslyckades|Procent|Medel|% av anslutnings övervaknings avsökningarna misslyckades|Inget|
|AverageRoundtripMs|Genomsnittlig tid för fördröjning (MS)|Millisekunder|Medel|Genomsnittlig tid för nätverks fördröjning (MS) för anslutnings övervaknings avsökningar som skickas mellan källa och mål|Inget|
|ChecksFailedPercent|Misslyckade kontroller i procent (för hands version)|Procent|Medel|% av kontrollerna för anslutnings övervakning misslyckades|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Tur och retur tid (MS) (för hands version)|Millisekunder|Medel|Svars tid i millisekunder för kontrollerna för anslutnings övervakning|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RequestCount|Antal begäranden|Antal|Totalt|Antalet klient förfrågningar som hanteras av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Begär ande storlek|Byte|Totalt|Antalet byte som har skickats som begär Anden från klienter till HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Svars storlek|Byte|Totalt|Antalet byte som skickats som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Fakturerbart svars storlek|Byte|Totalt|Antalet fakturerbara byte (minsta 2KB per begäran) som skickats som svar från HTTP/S-proxy till klienter.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Antal Server dels begär Anden|Antal|Totalt|Antalet förfrågningar som skickats från HTTP/S-proxyn till Server delar|HttpStatus, HttpStatusGroup, Server del|
|BackendRequestLatency|Svars tid för Server del|Millisekunder|Medel|Tiden som beräknas från när begäran skickades av HTTP/S-proxyn till Server delen tills HTTP/S-proxyn fick den senaste svars byten från Server delen|Serverdel|
|TotalLatency|Total svars tid|Millisekunder|Medel|Den tid som beräknas från när klientbegäran togs emot av HTTP/S-proxy tills klienten bekräftade den senaste svars byten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Server delens hälso procent|Procent|Medel|Procent andelen lyckade hälso avsökningar från HTTP/S-proxyn till Server delar|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|registrering. alla|Registrerings åtgärder|Antal|Totalt|Antalet lyckade registrerings åtgärder (skapar uppdateringar av frågor och borttagningar). |Inget|
|registration.create|Skapa åtgärder för registrering|Antal|Totalt|Antalet lyckade registreringar som skapas.|Inget|
|registration.update|Registrera uppdaterings åtgärder|Antal|Totalt|Antalet lyckade registrerings uppdateringar.|Inget|
|registration.get|Läs åtgärder för registrering|Antal|Totalt|Antalet lyckade registrerings frågor.|Inget|
|registration.delete|Åtgärder för att ta bort registrering|Antal|Totalt|Antalet lyckade registrerings borttagningar.|Inget|
|inkommande|Inkommande meddelanden|Antal|Totalt|Antalet lyckade sändnings-API-anrop. |Inget|
|incoming.scheduled|Schemalagda push-meddelanden har skickats|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inget|
|inkommande. schemalagd. Avbryt|Schemalagda push-meddelanden har avbrutits|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inget|
|schemalagt. väntar|Väntande schemalagda meddelanden|Antal|Totalt|Väntande schemalagda meddelanden|Inget|
|installation.all|Installations hanterings åtgärder|Antal|Totalt|Installations hanterings åtgärder|Inget|
|installation.get|Hämta installations åtgärder|Antal|Totalt|Hämta installations åtgärder|Inget|
|installation.upsert|Skapa eller uppdatera installations åtgärder|Antal|Totalt|Skapa eller uppdatera installations åtgärder|Inget|
|installation.patch|Installation av korrigerings åtgärder|Antal|Totalt|Installation av korrigerings åtgärder|Inget|
|installation.delete|Ta bort installations åtgärder|Antal|Totalt|Ta bort installations åtgärder|Inget|
|outgoing.allpns.success|Lyckade aviseringar|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|outgoing.allpns.invalidpayload|Nytto Last fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS returnerade ett felaktigt nytto Last fel.|Inget|
|outgoing.allpns.pnserror|Fel i externt meddelande system|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av ett problem med att kommunicera med PNS (exkluderar autentiseringsproblem).|Inget|
|outgoing.allpns.channelerror|Kanal fel|Antal|Totalt|Antal push-meddelanden som inte har misslyckats på grund av att kanalen var ogiltig och som inte är associerad med rätt app-begränsning eller upphörde att gälla.|Inget|
|outgoing.allpns.badorexpiredchannel|Dåliga eller utgångna kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom kanal/token/registrationId i registreringen har upphört att gälla eller är ogiltiga.|Inget|
|utgående. WNS. lyckades|WNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|outgoing.wns.invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras. (Windows Live känner inte igen autentiseringsuppgifterna).|Inget|
|outgoing.wns.badchannel|WNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inget|
|outgoing.wns.expiredchannel|WNS utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 borta).|Inget|
|utgående. WNS. begränsad|WNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom WNS begränsar den här appen (WNS-status: 406 är inte acceptabel).|Inget|
|outgoing.wns.tokenproviderunreachable|WNS-auktoriseringsfel (kan inte kontaktas)|Antal|Totalt|Windows Live är inte tillgängligt.|Inget|
|outgoing.wns.invalidtoken|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Den token som angetts för WNS är inte giltig (WNS-status: 401 otillåten).|Inget|
|outgoing.wns.wrongtoken|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS-status: 403 förbjuden). Detta kan inträffa om ChannelURI i registreringen är kopplad till en annan app. Kontrol lera att klient programmet är associerat med samma app vars autentiseringsuppgifter finns i Notification Hub.|Inget|
|outgoing.wns.invalidnotificationformat|WNS ogiltigt meddelande format|Antal|Totalt|Meddelandets format är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nytto laster.|Inget|
|utgående. WNS. invalidnotificationsize|WNS ogiltig meddelande storleks fel|Antal|Totalt|Meddelande nytto lasten är för stor (WNS-status: 413).|Inget|
|outgoing.wns.channelthrottled|WNS-kanalen är begränsad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-NotificationStatus: channelThrottled).|Inget|
|outgoing.wns.channeldisconnected|WNS-kanalen är frånkopplad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inget|
|outgoing.wns.dropped|WNS borttagna meddelanden|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: släppt men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inget|
|outgoing.wns.pnserror|WNS-fel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med WNS.|Inget|
|utgående. WNS. authenticationerror|WNS-autentiseringsfel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med ogiltiga Windows Live-autentiseringsuppgifter eller felaktigt token.|Inget|
|outgoing.apns.success|APN-lyckade meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|outgoing.apns.invalidcredentials|APN-auktoriseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inget|
|outgoing.apns.badchannel|APN dåligt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att token är ogiltig (APN-status kod: 8).|Inget|
|outgoing.apns.expiredchannel|APN utgånget i kanal fel|Antal|Totalt|Antalet token som ogiltig förklarades av APNS-feedback-kanalen.|Inget|
|outgoing.apns.invalidnotificationsize|APN ogiltigt meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (APN-status kod: 7).|Inget|
|outgoing.apns.pnserror|APN-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med APN.|Inget|
|utgående. GCM. lyckades|GCM slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|outgoing.gcm.invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inget|
|outgoing.gcm.badchannel|GCM felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM-resultat: ogiltig registrering).|Inget|
|outgoing.gcm.expiredchannel|GCM utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inget|
|utgående. GCM. begränsad|GCM-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom GCM begränsade till den här appen (GCM-status kod: 501-599 eller resultat: ej tillgängligt).|Inget|
|outgoing.gcm.invalidnotificationformat|GCM ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten inte formaterades korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inget|
|utgående. GCM. invalidnotificationsize|GCM ogiltig meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (GCM-resultat: MessageTooBig).|Inget|
|outgoing.gcm.wrongchannel|GCM fel kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte är kopplad till den aktuella appen (GCM result: InvalidPackageName).|Inget|
|outgoing.gcm.pnserror|GCM-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med GCM.|Inget|
|utgående. GCM. authenticationerror|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna som autentiseringsuppgifterna är blockerade eller om SenderId inte har kon figurer ATS korrekt i appen (GCM result: MismatchedSenderId).|Inget|
|outgoing.mpns.success|MPNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|outgoing.mpns.invalidcredentials|MPNS ogiltiga autentiseringsuppgifter|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inget|
|outgoing.mpns.badchannel|MPNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inget|
|outgoing.mpns.throttled|MPNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom MPNS begränsar den här appen (WNS MPNS: 406 är inte acceptabel).|Inget|
|outgoing.mpns.invalidnotificationformat|MPNS ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten för meddelandet var för stor.|Inget|
|outgoing.mpns.channeldisconnected|MPNS-kanalen är frånkopplad|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att ChannelURI i registreringen var frånkopplad (MPNS status: 412 hittades inte).|Inget|
|outgoing.mpns.dropped|MPNS borttagna meddelanden|Antal|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svars huvud: X-NotificationStatus: QueueFull eller undertrycks).|Inget|
|outgoing.mpns.pnserror|MPNS-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med MPNS.|Inget|
|outgoing.mpns.authenticationerror|MPNS-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inget|
|notificationhub. push-meddelanden|Alla utgående meddelanden|Antal|Totalt|Alla utgående aviseringar för Notification Hub|Inget|
|incoming.all.requests|Alla inkommande begär Anden|Antal|Totalt|Totalt antal inkommande begär Anden för en Notification Hub|Inget|
|incoming.all.failedrequests|Alla inkommande misslyckade förfrågningar|Antal|Totalt|Totalt antal inkommande misslyckade begär Anden för en Notification Hub|Inget|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_ kostnads fri noder i procent|Kostnads fri noder i procent|Antal|Medel|Average_ kostnads fri noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledigt utrymme i procent|Ledigt utrymme i procent|Antal|Medel|Average_ ledigt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Använd noder i procent|% Använda noder i procent|Antal|Medel|Average_% Använd noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt utrymme i procent|Använt utrymme i procent|Antal|Medel|Average_ använt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/s|Disk – lästa byte/sek|Antal|Medel|Average_Disk lästa byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Diskläsningar/sek|Antal|Medel|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Disköverföringar/sek|Antal|Medel|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/s|Disk – skrivna byte/sek|Antal|Medel|Average_Disk skrivna byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Diskskrivningar/sek|Antal|Medel|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Ledigt utrymme i MB|Antal|Medel|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk-byte/s|Logisk Disk byte/sek|Antal|Medel|Average_Logical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt minne|Tillgängligt minne i procent|Antal|Medel|Average_% tillgängligt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt växlings utrymme|Tillgängligt växlings utrymme i procent|Antal|Medel|Average_% tillgängligt växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt minne|Använt minne i procent|Antal|Medel|Average_% använt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt växlings utrymme i procent|Använt växlings utrymme i procent|Antal|Medel|Average_ använt växlings utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB minne|Tillgängligt minne i megabyte|Antal|Medel|Average_Available MB minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Växling vid Average_Available megabyte|Tillgängliga megabyte växlings utrymme|Antal|Medel|Växling vid Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page läsningar/s|Sidläsningar/sek|Antal|Medel|Average_Page läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page skrivningar/SEK|Sidskrivningar/sek|Antal|Medel|Average_Page skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per sekund|Sidor/s|Antal|Medel|Average_Pages per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlings utrymme|Använt megabyte växlings utrymme|Antal|Medel|Average_Used megabyte växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i MB|Använt minne i MB|Antal|Medel|Average_Used minne i MB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total byte|Totalt antal överförda byte|Antal|Medel|Skickade Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna byte|Totalt antal byte mottaget|Antal|Medel|Average_Total mottagna byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Totalt antal byte|Antal|Medel|Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total paket|Totalt antal överförda paket|Antal|Medel|Skickade Average_Total paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna paket|Totalt antal mottagna paket|Antal|Medel|Average_Total mottagna paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total RX-fel|Totalt antal mottagna mottagnings fel|Antal|Medel|Average_Total RX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-fel|Totalt antal TX-fel|Antal|Medel|Average_Total TX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Totalt antal kollisioner|Antal|Medel|Average_Total kollisioner|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskläsning|Medel s/disk läsning|Antal|Medel|Average_Avg. S/diskläsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/disköverföring|Medel s/disk överföring|Antal|Medel|Average_Avg. S/disköverföring|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskskrivning|Medel s/disk skrivning|Antal|Medel|Average_Avg. S/diskskrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk-byte/s|Fysisk disk-byte/SEK|Antal|Medel|Average_Physical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegie rad tid|PCT privilegie rad tid|Antal|Medel|Average_Pct privilegie rad tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användar tid|PCT-användar tid|Antal|Medel|Average_Pct användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i KB|Använt minne i KB|Antal|Medel|Average_Used minne i KB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Virtuellt delat minne|Antal|Medel|Average_Virtual delat minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tid|DPC-tid i procent|Antal|Medel|Average_% DPC-tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledig tid i procent|Inaktivitetstid i procent|Antal|Medel|Average_ ledig tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Avbrotts tid i procent för Average_%|% Avbrotts tid|Antal|Medel|Avbrotts tid i procent för Average_%|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% i/o-vänte tid|% I/o-vänte tid|Antal|Medel|Average_% i/o-vänte tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% bra tid|% Trevligt tid|Antal|Medel|Average_% bra tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ privilegie rad tid i procent|Privilegie rad tid i procent|Antal|Medel|Average_ privilegie rad tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|Tid i procent för processor|Antal|Medel|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% användar tid|Användar tid i procent|Antal|Medel|Average_% användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Ledigt fysiskt minne|Antal|Medel|Average_Free fysiskt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Ledigt utrymme i växlingsfiler|Antal|Medel|Average_Free utrymme i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Ledigt virtuellt minne|Antal|Medel|Average_Free virtuellt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processer|Antal|Medel|Average_Processes|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagras i växlingsfiler|Storlek lagrad i växlingsfiler|Antal|Medel|Average_Size lagras i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Drifttid|Antal|Medel|Average_Uptime|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Användare|Antal|Medel|Average_Users|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskläsning|Medel s/disk läsning|Antal|Medel|Average_Avg. S/diskläsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. S/diskskrivning|Medel s/disk skrivning|Antal|Medel|Average_Avg. S/diskskrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Aktuell diskkölängd|Antal|Medel|Average_Current diskkölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Diskläsningar/sek|Antal|Medel|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Disköverföringar/sek|Antal|Medel|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Diskskrivningar/sek|Antal|Medel|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Ledigt utrymme i MB|Antal|Medel|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledigt utrymme i procent|Ledigt utrymme i procent|Antal|Medel|Average_ ledigt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte|Tillgängliga megabyte|Antal|Medel|Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% allokerade byte som används|% Allokerade byte som används|Antal|Medel|Average_% allokerade byte som används|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes mottagna/SEK|Mottagna byte/sek|Antal|Medel|Average_Bytes mottagna/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Bytes per sekund|Skickade byte/sek|Antal|Medel|Skickade Average_Bytes per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/SEK|Totalt antal byte/s|Antal|Medel|Average_Bytes totalt/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|Tid i procent för processor|Antal|Medel|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Kölängd|Kölängd för processor|Antal|Medel|Average_Processor Kölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pulsslag|Pulsslag|Antal|Totalt|Pulsslag|Dator, OSType, version, SourceComputerId|
|Uppdatering|Uppdatering|Antal|Medel|Uppdatering|Dator, produkt, klassificering, UpdateState, valfri, godkänd|
|Händelse|Händelse|Antal|Medel|Händelse|Källa, EventLog, dator, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PrefixLatency|Prefix svars tid|Millisekunder|Medel|Prefixlängd för median|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Tillgänglighets-v4 för session|Procent|Medel|Tillgänglighet för v4-sessionen|ConnectionId|
|SessionAvailabilityV6|V6 för sessions tillgänglighet|Procent|Medel|Tillgängligheten för V6-sessionen|ConnectionId|
|IngressTrafficRate|Inkommande trafik hastighet|BitsPerSecond|Medel|Inkommande trafik hastighet i bitar per sekund|ConnectionId|
|EgressTrafficRate|Utgående trafik hastighet|BitsPerSecond|Medel|Utgående trafik hastighet i bitar per sekund|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryDuration|Frågevaraktighet|Millisekunder|Medel|DAX-frågans varaktighet i det sista intervallet|Inga dimensioner|
|QueryPoolJobQueueLength|Trådar: Kölängd för jobbkö|Antal|Medel|Antal jobb i kön för trådpoolen för Query.|Inga dimensioner|
|qpu_high_utilization_metric|Hög användning av QPU|Antal|Totalt|QPU hög användning under den senaste minuten, 1 för hög QPU användning, annars 0|Inga dimensioner|
|memory_metric|Minne|Byte|Medel|Memory. Intervallet 0-3 GB för a1, 0-5 GB för a2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Minnesförslöing|Procent|Medel|Genomsnittligt minne nedskräpning.|Inga dimensioner|



## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ListenerConnections – lyckad|ListenerConnections – lyckad|Antal|Totalt|Lyckade ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections – ClientError|ListenerConnections – ClientError|Antal|Totalt|ClientError på ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections – ServerError|ListenerConnections – ServerError|Antal|Totalt|ServerError på ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections-Success|SenderConnections-Success|Antal|Totalt|Lyckade SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections – ClientError|SenderConnections – ClientError|Antal|Totalt|ClientError på SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections – ServerError|SenderConnections – ServerError|Antal|Totalt|ServerError på SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Antal|Totalt|Totalt ListenerConnections för Microsoft. Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Antal|Totalt|Totalt antal SenderConnections-begäranden för Microsoft. Relay.|EntityName|
|ActiveConnections|ActiveConnections|Antal|Totalt|Totalt ActiveConnections för Microsoft. Relay.|EntityName|
|ActiveListeners|ActiveListeners|Antal|Totalt|Totalt ActiveListeners för Microsoft. Relay.|EntityName|
|BytesTransferred|BytesTransferred|Antal|Totalt|Totalt BytesTransferred för Microsoft. Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Antal|Totalt|Totalt ListenerDisconnects för Microsoft. Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Antal|Totalt|Totalt SenderDisconnects för Microsoft. Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SearchLatency|Sök svars tid|Sekunder|Medel|Genomsnittlig Sök fördröjning för Sök tjänsten|Inget|
|SearchQueriesPerSecond|Sök frågor per sekund|CountPerSecond|Medel|Sök efter frågor per sekund för Sök tjänsten|Inget|
|ThrottledSearchQueriesPercentage|Begränsade Sök frågor i procent|Procent|Medel|Procent andel Sök frågor som har begränsats för Sök tjänsten|Inget|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Lyckade förfrågningar|Antal|Totalt|Totalt antal slutförda begär Anden för ett namn område|EntityName, OperationResult under pågående|
|ServerErrors|Server fel.|Antal|Totalt|Server fel för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|UserErrors|Användar fel.|Antal|Totalt|Användar fel för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|ThrottledRequests|Begränsade begär Anden.|Antal|Totalt|Begränsade begär Anden för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|IncomingRequests|Inkommande begär Anden|Antal|Totalt|Inkommande begär Anden för Microsoft. Service Bus.|EntityName|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. Service Bus.|EntityName|
|OutgoingMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. Service Bus.|EntityName|
|ActiveConnections|ActiveConnections|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft. Service Bus.|Inget|
|ConnectionsOpened|Öppna anslutningar.|Antal|Medel|Anslutningar som öppnats för Microsoft. Service Bus.|EntityName|
|ConnectionsClosed|Stängda anslutningar.|Antal|Medel|Stängda anslutningar för Microsoft. Service Bus.|EntityName|
|Storlek|Storlek|Byte|Medel|Storlek på en kö/ett ämne i byte.|EntityName|
|Meddelanden|Antal meddelanden i en kö/ett ämne.|Antal|Medel|Antal meddelanden i en kö/ett ämne.|EntityName|
|ActiveMessages|Antal aktiva meddelanden i en kö/ett ämne.|Antal|Medel|Antal aktiva meddelanden i en kö/ett ämne.|EntityName|
|DeadletteredMessages|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Antal|Medel|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|EntityName|
|ScheduledMessages|Antal schemalagda meddelanden i en kö/ett ämne.|Antal|Medel|Antal schemalagda meddelanden i en kö/ett ämne.|EntityName|
|NamespaceCpuUsage|Processor|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd.|Replik|
|NamespaceMemoryUsage|Minnesanvändning|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd.|Replik|
|CPUXNS|PROCESSOR (inaktuell)|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd. Det här måttet är inaktuell. Använd processor måttet (NamespaceCpuUsage) i stället.|Replik|
|WSXNS|Minnes användning (inaktuell)|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd. Måttet är föråldrat. Använd minnes användnings måttet (NamespaceMemoryUsage) i stället.|Replik|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Antal|Medel|CPU allokerad till den här behållaren i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Byte|Medel|Minne som allokerats till den här behållaren i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Antal|Medel|Faktisk processor användning i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Byte|Medel|Faktisk minnes användning i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Medel|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Medel|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Antal|Medel|Status för Service Fabric nätprogram|ApplicationName, status|
|ServiceStatus|ServiceStatus|Antal|Medel|Hälso status för en tjänst i Service Fabric nätprogram|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Antal|Medel|Hälso status för en tjänst replik i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName|
|Container status|Container status|Antal|Medel|Status för behållaren i Service Fabric nätprogram|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Antal|Medel|Antal omstarter för en behållare i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ConnectionCount|Antal anslutningar|Antal|Maximal|Mängden användar anslutning.|Slutpunkt|
|MessageCount|Antal meddelanden|Antal|Totalt|Den totala mängden meddelanden.|Inget|
|InboundTraffic|Inkommande trafik|Byte|Totalt|Inkommande trafik trafik för tjänsten|Inget|
|OutboundTraffic|Utgående trafik|Byte|Totalt|Utgående trafik för tjänsten|Inget|
|UserErrors|Användar fel|Procent|Maximal|Procent andel användar fel|Inget|
|SystemErrors|Systemfel|Procent|Maximal|Procent andelen system fel|Inget|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inget|
|log_write_percent|Logg IO-procent|Procent|Medel|Logg IO-procent. Ej tillämpligt för data lager.|Inget|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent. Gäller för DTU-baserade databaser.|Inget|
|lagring|Använt data utrymme|Byte|Maximal|Använt data utrymme. Ej tillämpligt för data lager.|Inget|
|connection_successful|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inget|
|connection_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|blocked_by_firewall|Blockerad av brand väggen|Antal|Totalt|Blockerad av brand väggen|Inget|
|hamn|Låsningar|Antal|Totalt|Låsningar. Ej tillämpligt för data lager.|Inget|
|storage_percent|Använt data utrymme i procent|Procent|Maximal|Data utrymme som används i procent. Ej tillämpligt för data lager eller storskaliga databaser.|Inget|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Procent|Medel|Minnes intern OLTP-lagring i procent. Ej tillämpligt för data lager.|Inget|
|workers_percent|Arbetare i procent|Procent|Medel|Arbetare i procent. Ej tillämpligt för data lager.|Inget|
|sessions_percent|Sessioner i procent|Procent|Medel|Sessioner i procent. Ej tillämpligt för data lager.|Inget|
|dtu_limit|DTU-gräns|Antal|Medel|DTU-gräns. Gäller för DTU-baserade databaser.|Inget|
|dtu_used|Använt DTU|Antal|Medel|DTU används. Gäller för DTU-baserade databaser.|Inget|
|cpu_limit|PROCESSOR gräns|Antal|Medel|PROCESSOR gräns. Gäller för vCore-baserade databaser.|Inget|
|cpu_used|Använd CPU|Antal|Medel|Använd CPU. Gäller för vCore-baserade databaser.|Inget|
|dwu_limit|DWU-gräns|Antal|Maximal|DWU-gräns. Gäller endast för data lager.|Inget|
|dwu_consumption_percent|DWU procent|Procent|Maximal|DWU procent. Gäller endast för data lager.|Inget|
|dwu_used|DWU som används|Antal|Maximal|DWU som används. Gäller endast för data lager.|Inget|
|cache_hit_percent|Procent andel cacheträffar|Procent|Maximal|Procent av cacheträffar. Gäller endast för data lager.|Inget|
|cache_used_percent|Procent andel som används|Procent|Maximal|Procent andel som används. Gäller endast för data lager.|Inget|
|sqlserver_process_core_percent|SQL Server process kärn procent|Procent|Maximal|CPU-användning som en procent andel av SQL DB-processen. Ej tillämpligt för data lager.|Inget|
|sqlserver_process_memory_percent|SQL Server process minne i procent|Procent|Maximal|Minnes användning som en procent andel av SQL DB-processen. Ej tillämpligt för data lager.|Inget|
|tempdb_data_size|Data fil storlek i tempdb i KB|Antal|Maximal|Data fil storlek för tempdb i KB. Ej tillämpligt för data lager.|Inget|
|tempdb_log_size|TempDB-logg fils storlek kilobyte|Antal|Maximal|TempDB-logg fils storlek kilobyte. Ej tillämpligt för data lager.|Inget|
|tempdb_log_used_percent|Procent använt tempdb-logg|Procent|Maximal|TempDB procent logg används. Ej tillämpligt för data lager.|Inget|
|local_tempdb_usage_percent|Lokal tempdb-procent|Procent|Medel|Lokal tempdb-procent. Gäller endast för data lager.|Inget|
|app_cpu_billed|App-CPU fakturerad|Antal|Totalt|App-CPU fakturerad. Gäller databaser utan server.|Inget|
|app_cpu_percent|CPU-procent för app|Procent|Medel|CPU-procent för app. Gäller databaser utan server.|Inget|
|app_memory_percent|Minnes procent för appar|Procent|Medel|Minnes procent för appar. Gäller databaser utan server.|Inget|
|allocated_data_storage|Allokerat data utrymme|Byte|Medel|Allokerad data lagring. Ej tillämpligt för data lager.|Inget|
|memory_usage_percent|Minnes procent|Procent|Maximal|Minnes procent. Gäller endast för data lager.|Inget|
|full_backup_size_bytes|Lagrings storlek för fullständig säkerhets kopia|Byte|Maximal|Ackumulerad lagrings storlek för fullständig säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inget|
|diff_backup_size_bytes|Lagrings storlek för differentiell säkerhets kopia|Byte|Maximal|Kumulativ lagrings storlek för differentiell säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inget|
|log_backup_size_bytes|Logg lagrings storlek för logg säkerhets kopia|Byte|Maximal|Sammanlagd lagrings storlek för logg säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inget|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|database_cpu_percent|CPU-procent|Procent|Medel|CPU-procent|DatabaseResourceId|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inget|
|database_physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|DatabaseResourceId|
|log_write_percent|Logg IO-procent|Procent|Medel|Logg IO-procent|Inget|
|database_log_write_percent|Logg IO-procent|Procent|Medel|Logg IO-procent|DatabaseResourceId|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent. Gäller för DTU-baserade elastiska pooler.|Inget|
|database_dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|DatabaseResourceId|
|storage_percent|Använt data utrymme i procent|Procent|Medel|Använt data utrymme i procent|Inget|
|workers_percent|Arbetare i procent|Procent|Medel|Arbetare i procent|Inget|
|database_workers_percent|Arbetare i procent|Procent|Medel|Arbetare i procent|DatabaseResourceId|
|sessions_percent|Sessioner i procent|Procent|Medel|Sessioner i procent|Inget|
|database_sessions_percent|Sessioner i procent|Procent|Medel|Sessioner i procent|DatabaseResourceId|
|eDTU_limit|eDTU-gräns|Antal|Medel|eDTU-gräns. Gäller för DTU-baserade elastiska pooler.|Inget|
|storage_limit|Maximal datastorlek|Byte|Medel|Maximal datastorlek|Inget|
|eDTU_used|eDTU använt|Antal|Medel|eDTU används. Gäller för DTU-baserade elastiska pooler.|Inget|
|database_eDTU_used|eDTU använt|Antal|Medel|eDTU använt|DatabaseResourceId|
|storage_used|Använt data utrymme|Byte|Medel|Använt data utrymme|Inget|
|database_storage_used|Använt data utrymme|Byte|Medel|Använt data utrymme|DatabaseResourceId|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Procent|Medel|Minnes intern OLTP-lagring i procent|Inget|
|cpu_limit|PROCESSOR gräns|Antal|Medel|PROCESSOR gräns. Gäller vCore-baserade elastiska pooler.|Inget|
|database_cpu_limit|PROCESSOR gräns|Antal|Medel|PROCESSOR gräns|DatabaseResourceId|
|cpu_used|Använd CPU|Antal|Medel|Använd CPU. Gäller vCore-baserade elastiska pooler.|Inget|
|database_cpu_used|Använd CPU|Antal|Medel|Använd CPU|DatabaseResourceId|
|sqlserver_process_core_percent|SQL Server process kärn procent|Procent|Maximal|CPU-användning som en procent andel av SQL DB-processen. Gäller elastiska pooler.|Inget|
|sqlserver_process_memory_percent|SQL Server process minne i procent|Procent|Maximal|Minnes användning som en procent andel av SQL DB-processen. Gäller elastiska pooler.|Inget|
|tempdb_data_size|Data fil storlek i tempdb i KB|Antal|Maximal|Data fil storlek i tempdb i KB|Inget|
|tempdb_log_size|TempDB-logg fils storlek kilobyte|Antal|Maximal|TempDB-logg fils storlek kilobyte|Inget|
|tempdb_log_used_percent|Procent använt tempdb-logg|Procent|Maximal|Procent använt tempdb-logg|Inget|
|allocated_data_storage|Allokerat data utrymme|Byte|Medel|Allokerat data utrymme|Inget|
|database_allocated_data_storage|Allokerat data utrymme|Byte|Medel|Allokerat data utrymme|DatabaseResourceId|
|allocated_data_storage_percent|Allokerat data utrymme i procent|Procent|Maximal|Allokerat data utrymme i procent|Inget|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Använt data utrymme|Byte|Medel|Använt data utrymme|ElasticPoolResourceId|
|database_storage_used|Använt data utrymme|Byte|Medel|Använt data utrymme|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Använt DTU|Antal|Medel|Använt DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|virtual_core_count|Antal virtuella kärnor|Antal|Medel|Antal virtuella kärnor|Inget|
|avg_cpu_percent|Genomsnittlig CPU-procent|Procent|Medel|Genomsnittlig CPU-procent|Inget|
|reserved_storage_mb|Reserverat lagrings utrymme|Antal|Medel|Reserverat lagrings utrymme|Inget|
|storage_space_used_mb|Använt lagrings utrymme|Antal|Medel|Använt lagrings utrymme|Inget|
|io_requests|Antal IO-begäranden|Antal|Medel|Antal IO-begäranden|Inget|
|io_bytes_read|Lästa IO-byte|Byte|Medel|Lästa IO-byte|Inget|
|io_bytes_written|Skrivna IO-byte|Byte|Medel|Skrivna IO-byte|Inget|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Medel|Kapacitet som används av konto|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|Blobkapacitet|Byte|Medel|Mängden lagring som används av lagringskontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|Antalet blobar|Antal|Medel|Antalet blobar i lagringskontots Blob Service.|BlobType,-nivå|
|ContainerCount|Antal blobcontainrar|Antal|Medel|Antalet containrar i lagringskontots Blob Service.|Inget|
|IndexCapacity|Indexkapacitet|Byte|Medel|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabellkapacitet|Byte|Medel|Mängden lagring som används av lagringskontots tabelltjänst i byte.|Inget|
|TableCount|Antal tabeller|Antal|Medel|Antalet tabeller i lagringskontots tabelltjänst.|Inget|
|TableEntityCount|Antal tabellentiteter|Antal|Medel|Antalet tabellentiteter i lagringskontots tabelltjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Fil kapacitet|Byte|Medel|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|FileShare|
|FileCount|Antal filer|Antal|Medel|Antalet filer i lagrings kontots fil tjänst.|FileShare|
|FileShareCount|Antal fil resurser|Antal|Medel|Antalet fil resurser i lagrings kontots fil tjänst.|Inget|
|FileShareSnapshotCount|Antal ögonblicks bilder av fil resurs|Antal|Medel|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|Storlek på fil resursens ögonblicks bild|Byte|Medel|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|FileShareQuota|Fil resursens kvot storlek|Byte|Medel|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kökapacitet|Byte|Medel|Mängden lagring som används av lagringskontots kötjänst i byte.|Inget|
|QueueCount|Antal köer|Antal|Medel|Antalet köer i lagringskontots kötjänst.|Inget|
|QueueMessageCount|Antal kömeddelanden|Antal|Medel|Ungefärligt antal kömeddelanden i lagringskontots kötjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd måttet responsetype för antalet annan typ av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingångshändelser|Ingångshändelser|Byte|Totalt|Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad serversvarstid|Millisekunder|Medel|Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/cacheminnen

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ClientIOPS|Totalt antal klient-IOPS|Antal|Medel|Frekvensen av klient fil åtgärder som bearbetas av cachen.|Inget|
|ClientLatency|Genomsnittlig klient latens|Millisekunder|Medel|Genomsnittlig svars tid för klient fil åtgärder till Storage cache.|Inget|
|ClientReadIOPS|Klient läsnings-IOPS|CountPerSecond|Medel|Klient Läs åtgärder per sekund.|Inget|
|ClientReadThroughput|Genomsnittligt cache-läst data flöde|BytesPerSecond|Medel|Överföringshastighet för klient läsnings data.|Inget|
|ClientWriteIOPS|Klient skrivnings-IOPS|CountPerSecond|Medel|Klient skrivnings åtgärder per sekund.|Inget|
|ClientWriteThroughput|Genomsnittligt cacheminne Skriv data flöde|BytesPerSecond|Medel|Data överförings takt för klient skrivning.|Inget|
|ClientMetadataReadIOPS|Lästa IOPS för klientens metadata|CountPerSecond|Medel|Frekvensen av klient fil åtgärder som skickas till cachen, exklusive data läsningar som inte ändrar beständigt tillstånd.|Inget|
|ClientMetadataWriteIOPS|Klientens metadata Skriv IOPS|CountPerSecond|Medel|Frekvensen av klient fil åtgärder som skickas till cachen, exklusive data skrivningar, som ändrar beständigt tillstånd.|Inget|
|ClientLockIOPS|Klient lås IOPS|CountPerSecond|Medel|Klient fil låsnings åtgärder per sekund.|Inget|
|StorageTargetHealth|Lagrings mål hälsa|Antal|Medel|Booleska resultat för anslutnings test mellan cache-och lagrings målen.|Inget|
|Drifttid|Drifttid|Antal|Medel|Booleska resultat för anslutnings test mellan cache-och övervaknings systemet.|Inget|
|StorageTargetIOPS|Totalt antal StorageTarget IOPS|Antal|Medel|Frekvensen för alla fil åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget Skriv IOPS|Antal|Medel|Hastigheten för fil skrivnings åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget asynkron skrivning av data flöde|BytesPerSecond|Medel|Hastigheten cachen skriver data asynkront till en viss StorageTarget. Detta är Opportunistic skrivningar som inte gör att klienter blockerar.|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget synkront Skriv data flöde|BytesPerSecond|Medel|Hastigheten cachen skriver data synkront till en viss StorageTarget. Detta är skrivningar som gör att klienter blockerar.|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget totalt Skriv data flöde|BytesPerSecond|Medel|Den totala taxan som cachen skriver data till en viss StorageTarget.|StorageTarget|
|StorageTargetLatency|StorageTarget-svars tid|Millisekunder|Medel|Genomsnittlig fördröjning för fördröjning av alla fil åtgärder som cachen skickar till en partricular-StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget metadata Läs IOPS|CountPerSecond|Medel|Frekvensen för fil åtgärder som inte ändrar beständigt tillstånd, och som utesluter Läs åtgärden, som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget metadata Skriv IOPS|CountPerSecond|Medel|Frekvensen för fil åtgärder som ändrar beständigt tillstånd och exklusive Skriv åtgärden, som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget läsa IOPS|CountPerSecond|Medel|Frekvensen för fil läsnings åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget Read i förväg genom strömning|BytesPerSecond|Medel|Den hastighet med vilken cache-opportunisticly läser data från StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|StorageTarget Fill-genomflöde|BytesPerSecond|Medel|Hastigheten som cachen läser data från StorageTarget för att hantera ett cache-missar.|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget total Read-genomflöde|BytesPerSecond|Medel|Den totala taxan som cachen läser data från en viss StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultat av Sync-session|Antal|Medel|Mått som loggar värdet 1 varje gång Server slut punkten slutför en Sync-session med moln slut punkten|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Återställnings storlek för moln nivå|Byte|Totalt|Storlek på data som återkallas|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Återkalla data flöde för moln nivå|BytesPerSecond|Medel|Storlek på data återkallande data flöde|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Återställnings storlek för moln skikt per program|Byte|Totalt|Storlek på data som återkallas av program|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerHeartbeat|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ResourceUtilization|SU % utnyttjande|Procent|Maximal|SU % utnyttjande|LogicalName, PartitionId|
|InputEvents|Indatahändelser|Antal|Totalt|Indatahändelser|LogicalName, PartitionId|
|InputEventBytes|Indatahändelsebyte|Byte|Totalt|Indatahändelsebyte|LogicalName, PartitionId|
|LateInputEvents|Sena indatahändelser|Antal|Totalt|Sena indatahändelser|LogicalName, PartitionId|
|OutputEvents|Utdatahändelser|Antal|Totalt|Utdatahändelser|LogicalName, PartitionId|
|ConversionErrors|Datakonverteringsfel|Antal|Totalt|Datakonverteringsfel|LogicalName, PartitionId|
|Fel|Körningsfel|Antal|Totalt|Körningsfel|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Händelser som inte är i ordning|Antal|Totalt|Händelser som inte är i ordning|LogicalName, PartitionId|
|AMLCalloutRequests|Funktionsförfrågningar|Antal|Totalt|Funktionsförfrågningar|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Misslyckade funktionsförfrågningar|Antal|Totalt|Misslyckade funktionsförfrågningar|LogicalName, PartitionId|
|AMLCalloutInputEvents|Funktionshändelser|Antal|Totalt|Funktionshändelser|LogicalName, PartitionId|
|DeserializationError|Fel vid deserialisering av indata|Antal|Totalt|Fel vid deserialisering av indata|LogicalName, PartitionId|
|EarlyInputEvents|Tidiga indatahändelser|Antal|Totalt|Tidiga indatahändelser|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Vattenstämpelfördröjning|Sekunder|Maximal|Vattenstämpelfördröjning|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Eftersläpande inmatningshändelser|Antal|Maximal|Eftersläpande inmatningshändelser|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Indatakällorna har tagits emot|Antal|Totalt|Indatakällorna har tagits emot|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inget|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inget|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från alla händelse källor|Inget|
|IngressStoredBytes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inget|
|IngressStoredEvents|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inget|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inget|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Medel|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inget|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inget|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inget|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från händelse källan|Inget|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från händelse källan|Inget|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från händelse källan|Inget|
|IngressStoredBytes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inget|
|IngressStoredEvents|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inget|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inget|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Medel|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inget|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inget|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inget|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk-lästa byte/s|BytesPerSecond|Medel|Genomsnittligt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inget|
|DiskWriteBytesPerSecond|Disk-skrivna byte/s|BytesPerSecond|Medel|Genomsnittligt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inget|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inget|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Totalt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inget|
|DiskReadOperations|Disk Läs åtgärder|Antal|Totalt|Antalet Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inget|
|DiskWriteOperations|Disk skrivnings åtgärder|Antal|Totalt|Antalet i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inget|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medel|Genomsnittligt antal Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inget|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medel|Genomsnittligt antal i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inget|
|DiskReadLatency|Läs fördröjning för disk|Millisekunder|Medel|Total Läs fördröjning. Summan av Läs fördröjningen för enheten och kärnan.|Inget|
|DiskWriteLatency|Skriv fördröjning för disk|Millisekunder|Medel|Total Skriv fördröjning. Summan av Skriv fördröjningarna för enheten och kärnan.|Inget|
|NetworkInBytesPerSecond|Nätverk i byte/s|BytesPerSecond|Medel|Genomsnittligt nätverks data flöde för mottagen trafik.|Inget|
|NetworkOutBytesPerSecond|Nätverks-out-byte/SEK|BytesPerSecond|Medel|Genomsnittligt nätverks data flöde för överförd trafik.|Inget|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Totalt nätverks data flöde för mottagen trafik.|Inget|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Totalt nätverks data flöde för överförd trafik.|Inget|
|MemoryUsed|Använt minne|Byte|Medel|Mängden dator minne som används av den virtuella datorn.|Inget|
|MemoryGranted|Beviljat minne|Byte|Medel|Mängden minne som har beviljats för den virtuella datorn av värden. Minnet beviljas inte för värden förrän den är påslagen en gång och beviljat minne kan växlas ut eller visas borta om VMkernel behöver minnet.|Inget|
|MemoryActive|Aktivt minne|Byte|Medel|Mängden minne som används av den virtuella datorn i det tidigare lilla tids fönstret. Detta är "true"-antalet hur mycket minne som den virtuella datorn för närvarande behöver. Ytterligare, oanvänt minne kan växlas ut eller visas utan att påverka gästens prestanda.|Inget|
|Procent CPU|Procent CPU|Procent|Medel|PROCESSOR användningen. Det här värdet rapporteras med 100% som representerar alla processor kärnor i systemet. Till exempel är en 2-vägs virtuell dator som använder 50% av ett system med fyra kärnor fullständigt med två kärnor.|Inget|
|PercentageCpuReady|Procent andel CPU klar|Millisekunder|Totalt|Ready Time är den tid som väntar på att CPU: er ska bli tillgängliga under det tidigare uppdaterings intervallet.|Inget|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medel|Minnes procent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Medel|Diskkölängd|Instans|
|HttpQueueLength|Längd på http-kö|Antal|Medel|Längd på http-kö|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|TcpSynSent|TCP-syn har skickats|Antal|Medel|TCP-syn har skickats|Instans|
|TcpSynReceived|TCP-syn mottagen|Antal|Medel|TCP-syn mottagen|Instans|
|TcpEstablished|TCP upprättad|Antal|Medel|TCP upprättad|Instans|
|TcpFinWait1|TCP räntetrans wait 1|Antal|Medel|TCP räntetrans wait 1|Instans|
|TcpFinWait2|TCP räntetrans wait 2|Antal|Medel|TCP räntetrans wait 2|Instans|
|TcpClosing|TCP-stängning|Antal|Medel|TCP-stängning|Instans|
|TcpCloseWait|Väntan på TCP-stängning|Antal|Medel|Väntan på TCP-stängning|Instans|
|TcpLastAck|TCP-senaste ack|Antal|Medel|TCP-senaste ack|Instans|
|TcpTimeWait|Väntan på TCP-tid|Antal|Medel|Väntan på TCP-tid|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (exklusive funktioner)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Http-2xx|Antal|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Antal|Totalt|Http-3xx|Instans|
|Http401|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http-4xx|Antal|Totalt|Http-4xx|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Byte|Medel|Genomsnittlig arbets mängd för minne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Medel|Genomsnittlig svarstid|Instans|
|AppConnections|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Konsolindataobjekt|Antal referenser|Antal|Medel|Antal referenser|Instans|
|Trådar|Räkning av trådar|Antal|Medel|Räkning av trådar|Instans|
|PrivateBytes|Privata byte|Byte|Medel|Privata byte|Instans|
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
|HealthCheckStatus|Hälso kontroll status|Antal|Medel|Hälso kontroll status|Instans|
|FileSystemUsage|Fil system användning|Byte|Medel|Fil system användning|Inget|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (Functions)

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Byte|Medel|Genomsnittlig arbets mängd för minne|Instans|
|FunctionExecutionUnits|Funktions körnings enheter|MB/millisekunder|Totalt|[Funktions körnings enheter](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instans|
|FunctionExecutionCount|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|PrivateBytes|Privata byte|Byte|Medel|Privata byte|Instans|
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
|HealthCheckStatus|Hälso kontroll status|Antal|Medel|Hälso kontroll status|Instans|
|FileSystemUsage|Fil system användning|Byte|Medel|Fil system användning|Inget|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Http-2xx|Antal|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Antal|Totalt|Http-3xx|Instans|
|Http401|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http-4xx|Antal|Totalt|Http-4xx|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Byte|Medel|Genomsnittlig arbets mängd för minne|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Medel|Genomsnittlig svarstid|Instans|
|HttpResponseTime|Svars tid|Sekunder|Medel|Svars tid|Instans|
|FunctionExecutionUnits|Funktions körnings enheter|Antal|Totalt|Funktions körnings enheter|Instans|
|FunctionExecutionCount|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|AppConnections|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Konsolindataobjekt|Antal referenser|Antal|Medel|Antal referenser|Instans|
|Trådar|Räkning av trådar|Antal|Medel|Räkning av trådar|Instans|
|PrivateBytes|Privata byte|Byte|Medel|Privata byte|Instans|
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
|HealthCheckStatus|Hälso kontroll status|Antal|Medel|Hälso kontroll status|Instans|
|FileSystemUsage|Fil system användning|Byte|Medel|Fil system användning|Inget|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data in|Byte|Totalt|Data in|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Http-2xx|Antal|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Antal|Totalt|Http-3xx|Instans|
|Http401|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http-4xx|Antal|Totalt|Http-4xx|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|AverageResponseTime|Genomsnittlig svarstid|Sekunder|Medel|Genomsnittlig svarstid|Instans|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medel|Minnes procent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Medel|Diskkölängd|Instans|
|HttpQueueLength|Längd på http-kö|Antal|Medel|Längd på http-kö|Instans|
|ActiveRequests|Aktiva begär Anden|Antal|Totalt|Aktiva begär Anden|Instans|
|TotalFrontEnds|Totalt antal klient delar|Antal|Medel|Totalt antal klient delar|Inget|
|SmallAppServicePlanInstances|Små App Service planera arbetare|Antal|Medel|Små App Service planera arbetare|Inget|
|MediumAppServicePlanInstances|Medel App Service planera arbetare|Antal|Medel|Medel App Service planera arbetare|Inget|
|LargeAppServicePlanInstances|Stora App Service planera arbetare|Antal|Medel|Stora App Service planera arbetare|Inget|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbetare|Antal|Medel|Totalt antal arbetare|Inget|
|WorkersAvailable|Tillgängliga arbetare|Antal|Medel|Tillgängliga arbetare|Inget|
|WorkersUsed|Använda arbetare|Antal|Medel|Använda arbetare|Inget|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medel|Minnes procent|Instans|
## <a name="next-steps"></a>Nästa steg
* [Läs om mått i Azure Monitor](data-platform.md)
* [Skapa aviseringar för mått](alerts-overview.md)
* [Exportera mått till lagring, Event Hub eller Log Analytics](platform-logs-overview.md)
