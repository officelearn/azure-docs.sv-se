---
title: Azure Monitor mått som stöds efter resurs typ
description: Lista över mått som är tillgängliga för varje resurs typ med Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 06/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: e8bae2062051156d6de378e54bc354b3f785e403
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515469"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor

> [!NOTE]
> Den här listan genereras i stort sett automatiskt från Azure Monitor mått REST API. Alla ändringar som görs i den här listan via GitHub kan skrivas över utan varning. Kontakta författaren till den här artikeln om du vill ha mer information om hur du gör permanenta uppdateringar.

Azure Monitor tillhandahåller flera olika sätt att interagera med mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av PowerShell eller CLI. 

Den här artikeln är en fullständig lista över alla plattformar (dvs. automatiskt insamlade) mått som för närvarande är tillgängliga med Azure Monitor den konsoliderade mått pipelinen. Listan senast uppdaterades mars 27 2020. Mått som har ändrats eller lagts till efter det här datumet får inte visas nedan. Om du vill fråga efter och komma åt listan över mått program mässigt kan du använda [2018-01-01 API-versionen](/rest/api/monitor/metricdefinitions). Andra mått som inte finns med i listan kan finnas tillgängliga i portalen eller med äldre API: er.

Måtten är ordnade efter resurs leverantörer och resurs typ. En lista över tjänster och resurs leverantörer som hör till dem finns i [Resource providers för Azure-tjänster](../../azure-resource-manager/management/azure-services-resource-providers.md). 


## <a name="guest-os-metrics"></a>Gäst operativ systemets mått

Mått för gäst operativ systemet (gäst operativ system) som körs i Azure Virtual Machines, Service Fabric och Cloud Services visas **inte** här. I stället måste prestanda måtten för gäst operativ systemet samlas in via en eller flera agenter som körs på eller som en del av gäst operativ systemet.  Gäst operativ systemets mått inkluderar prestanda räknare som spårar gäst processor procent eller minnes användning, som båda används ofta för automatisk skalning eller avisering.  Med [Azure-diagnostik-tillägget](diagnostics-extension-overview.md)kan du skicka gäst operativ systemets prestanda mått till samma databas där plattforms måtten lagras. Den dirigerar gäst operativ systemets mått genom API: et för [anpassade mått](metrics-custom-overview.md) . Sedan kan du Diagrama, Varna och annars använda gäst operativ systemets mått som plattforms mått. Mer information finns i [Översikt över övervaknings agenter](agents-overview.md).    

## <a name="routing-platform-metrics-to-other-locations"></a>Routa plattforms mått till andra platser

Du kan använda [diagnostikinställningar](diagnostic-settings.md) för att dirigera plattforms mått till Azure Storage, Azure Monitor loggar (och därmed Log Analytics) och Event Hub.  

Det finns vissa begränsningar i vad som kan vidarebefordras och formuläret där de lagras. 
- Det går inte att exportera alla mått till andra platser. En lista över plattforms mått som kan exporteras via diagnostikinställningar finns i [den här artikeln](metrics-supported-export-diagnostic-settings.md).

- Det finns för närvarande inte stöd för att skicka flerdimensionella mått till andra platser via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
*Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Medelvärde|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|memory_metric|Minne|Byte|Medelvärde|Memory. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|private_bytes_metric|Privata byte|Byte|Medelvärde|Privata byte.|ServerResourceType|
|virtual_bytes_metric|Virtuella byte|Byte|Medelvärde|Virtuella byte.|ServerResourceType|
|TotalConnectionRequests|Totalt antal anslutnings begär Anden|Count|Medelvärde|Totalt antal anslutnings begär Anden. Dessa är mottagna.|ServerResourceType|
|SuccessfullConnectionsPerSec|Lyckade anslutningar per sekund|CountPerSecond|Medelvärde|Antal slutförda slut för ande av anslutningar.|ServerResourceType|
|TotalConnectionFailures|Totalt antal anslutnings problem|Count|Medelvärde|Totalt antal misslyckade anslutnings försök.|ServerResourceType|
|CurrentUserSessions|Aktuella användarsessioner|Count|Medelvärde|Aktuellt antal användarsessioner som har upprättats.|ServerResourceType|
|QueryPoolBusyThreads|Upptagna trådar för frågeprocessorn|Count|Medelvärde|Antalet upptagna trådar i trådpoolen.|ServerResourceType|
|CommandPoolJobQueueLength|Kölängd för kommando bassäng|Count|Medelvärde|Antal jobb i kön för kommando tråds poolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Bearbetar Kölängd för poolen|Count|Medelvärde|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|CurrentConnections|Anslutning: aktuella anslutningar|Count|Medelvärde|Aktuellt antal upprättade klient anslutningar.|ServerResourceType|
|CleanerCurrentPrice|Minne: aktuellt pris för renare|Count|Medelvärde|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Minne: krympnings utrymme för rensning|Byte|Medelvärde|Mängden minne i byte som kan rensas av bakgrunds rensaren.|ServerResourceType|
|CleanerMemoryNonshrinkable|Minne: det går inte att krympa rengörings minnet|Byte|Medelvärde|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|ServerResourceType|
|MemoryUsage|Minne: minnes användning|Byte|Medelvärde|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar minne som har mappats eller allokerats av xVelocity i Memory Analytics Engine (VertiPaq) utöver minnes gränsen för xVelocity-motorn.|ServerResourceType|
|MemoryLimitHard|Minne: minnes gräns hårt|Byte|Medelvärde|Hård minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitHigh|Minne: minnes gräns hög|Byte|Medelvärde|Hög minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitLow|Minne: minnes gräns låg|Byte|Medelvärde|Låg minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitVertiPaq|Minne: minnes gräns VertiPaq|Byte|Medelvärde|Minnes intern gräns, från konfigurations filen.|ServerResourceType|
|Kvot|Minne: kvot|Byte|Medelvärde|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|ServerResourceType|
|QuotaBlocked|Minne: kvot blockerad|Count|Medelvärde|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|ServerResourceType|
|VertiPaqNonpaged|Minne: VertiPaq som inte är växlat|Byte|Medelvärde|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|ServerResourceType|
|VertiPaqPaged|Minne: VertiPaq växlat|Byte|Medelvärde|Byte av växlings Bart minne som används för minnes intern data.|ServerResourceType|
|RowsReadPerSec|Bearbetar: rader lästa per sekund|CountPerSecond|Medelvärde|Antalet rader som läses från alla Relations databaser.|ServerResourceType|
|RowsConvertedPerSec|Bearbetar: rader konverterade per sekund|CountPerSecond|Medelvärde|Antal rader som konverterats under bearbetning.|ServerResourceType|
|RowsWrittenPerSec|Bearbetar: rader skrivna per sekund|CountPerSecond|Medelvärde|Antal rader som skrivs under bearbetningen.|ServerResourceType|
|CommandPoolBusyThreads|Trådar: pool med upptagna kommando trådar|Count|Medelvärde|Antalet upptagna trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolIdleThreads|Trådar: inaktiva trådar för kommando pool|Count|Medelvärde|Antal inaktiva trådar i kommando tråds-poolen.|ServerResourceType|
|LongParsingBusyThreads|Trådar: lång parsning av upptagna trådar|Count|Medelvärde|Antalet upptagna trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingIdleThreads|Trådar: inaktiva trådar för lång parsning|Count|Medelvärde|Antalet inaktiva trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingJobQueueLength|Trådar: lång parsning av jobb Kölängd|Count|Medelvärde|Antal jobb i kön för den långa parsande trådpoolen.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Trådar: bearbetning av pool upptagna I/O-jobb trådar|Count|Medelvärde|Antal trådar som kör i/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Trådar: bearbetning av pool upptagna icke-I/O-trådar|Count|Medelvärde|Antal trådar som kör icke-I/O-jobb i bearbetningen av trådpoolen.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Trådar: bearbeta pool I/O-jobb Kölängd|Count|Medelvärde|Antalet I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Trådar: behandlar inaktiva I/O-jobb trådar för poolen|Count|Medelvärde|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Trådar: behandlar inaktiva icke-I/O-trådar som behandlar poolen|Count|Medelvärde|Antalet inaktiva trådar i bearbetnings trådens pool som är dedikerad till icke-I/O-jobb.|ServerResourceType|
|QueryPoolIdleThreads|Trådar: inaktiva trådar i lagringspoolen|Count|Medelvärde|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|QueryPoolJobQueueLength|Trådar: jobbkölängd för jobbkö|Count|Medelvärde|Antal jobb i kön för trådpoolen för Query.|ServerResourceType|
|ShortParsingBusyThreads|Trådar: kort parsning upptagna trådar|Count|Medelvärde|Antalet upptagna trådar i den kort parsar trådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Trådar: kort parsning inaktiva trådar|Count|Medelvärde|Antal inaktiva trådar i kort parsar trådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Trådar: kort parsning av jobb Kölängd|Count|Medelvärde|Antal jobb i kö för kort parsar trådpoolen.|ServerResourceType|
|memory_thrashing_metric|Minnesförslöing|Procent|Medelvärde|Genomsnittligt minne nedskräpning.|ServerResourceType|
|mashup_engine_qpu_metric|M-motor QPU|Count|Medelvärde|QPU användning av kombinations motor processer|ServerResourceType|
|mashup_engine_memory_metric|M motor minne|Byte|Medelvärde|Minnes användning per kombinations motor processer|ServerResourceType|
|mashup_engine_private_bytes_metric|M motor privata byte|Byte|Medelvärde|Användning av privata byte med kombinations motor processer.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Virtuella byte i M-motor|Byte|Medelvärde|Virtuella byte som används av kombinations motor processer.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalRequests|Totalt antal Gateway-begäranden (inaktuella)|Count|Totalt|Antal Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|SuccessfulRequests|Lyckade Gateway-begäranden (inaktuella)|Count|Totalt|Antal lyckade Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|UnauthorizedRequests|Obehöriga Gateway-begäranden (inaktuella)|Count|Totalt|Antal otillåtna Gateway-begäranden – Använd dimensions mått för flera dimensioner med GatewayResponseCodeCategory dimension i stället|Plats, värdnamn|
|FailedRequests|Misslyckade Gateway-begäranden (inaktuella)|Count|Totalt|Antal misslyckade Gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|OtherRequests|Andra gateway-begäranden (inaktuella)|Count|Totalt|Antal andra gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|Varaktighet|Total varaktighet för gateway-begäranden|Millisekunder|Medelvärde|Total varaktighet för gateway-begäranden i millisekunder|Plats, värdnamn|
|BackendDuration|Varaktighet för backend-begäranden|Millisekunder|Medelvärde|Varaktighet för backend-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Kapacitet|Procent|Medelvärde|Användnings mått för API Management-tjänsten|Position|
|EventHubTotalEvents|Totalt antal EventHub-händelser|Count|Totalt|Antal händelser som skickats till EventHub|Position|
|EventHubSuccessfulEvents|Lyckade EventHub-händelser|Count|Totalt|Antal lyckade EventHub-händelser|Position|
|EventHubTotalFailedEvents|Misslyckade EventHub-händelser|Count|Totalt|Antal misslyckade EventHub-händelser|Position|
|EventHubRejectedEvents|Avvisade EventHub-händelser|Count|Totalt|Antal avvisade EventHub-händelser (felaktig konfiguration eller otillåten)|Position|
|EventHubThrottledEvents|Begränsade EventHub-händelser|Count|Totalt|Antal begränsade EventHub-händelser|Position|
|EventHubTimedoutEvents|Tids gränsen nåddes för EventHub-händelser|Count|Totalt|Antal inaktuella EventHub-händelser|Position|
|EventHubDroppedEvents|Ignorerade EventHub-händelser|Count|Totalt|Antalet händelser som hoppades över på grund av att gränsen för kös Tor lek har uppnåtts|Position|
|EventHubTotalBytesSent|Storlek på EventHub-händelser|Byte|Totalt|Total storlek för EventHub-händelser i byte|Position|
|Begäranden|Begäranden|Count|Totalt|Mått för gateway-begäran med flera dimensioner|Plats, värdnamn, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|NetworkConnectivity|Status för resurs för nätverks anslutning (förhands granskning)|Count|Totalt|Nätverks anslutnings status för beroende resurs typer från API Management-tjänsten|Plats, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Count|Count|Totalt antal inkommande HTTP-begäranden.|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Count|Medelvärde|Svars tid på en http-begäran.|StatusCode|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/våren

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Procent andel system CPU-användning|Procent|Medelvärde|Senaste CPU-användning för hela systemet|AppName, Pod|
|AppCpuUsagePercentage|Procent andel CPU-användning för app|Procent|Medelvärde|JVM CPU-användning i procent|AppName, Pod|
|AppMemoryCommitted|Tilldelad app-minne|Byte|Medelvärde|Minne som tilldelats JVM i byte|AppName, Pod|
|AppMemoryUsed|Använt app-minne|Byte|Medelvärde|App-minne som används i byte|AppName, Pod|
|AppMemoryMax|Max för app-minne|Byte|Maximal|Maximal mängd minne i byte som kan användas för minnes hantering|AppName, Pod|
|MaxOldGenMemoryPoolBytes|Maximal tillgänglig gammal generations data storlek|Byte|Medelvärde|Max storlek på den gamla generationens minnesbuffert|AppName, Pod|
|OldGenMemoryPoolBytes|Gammal generations data storlek|Byte|Medelvärde|Storlek på den gamla generationens lagringspool efter en fullständig GC|AppName, Pod|
|OldGenPromotedBytes|Befordra till gammal generations data storlek|Byte|Maximal|Antalet positiva ökningar i storleken på den gamla generationens minnesbuffert innan GC till efter GC|AppName, Pod|
|YoungGenPromotedBytes|Höj data storlek för unga generationer|Byte|Maximal|Ökas för en ökning av storleken på den unga generationens minnesbuffert efter en GC till före nästa|AppName, Pod|
|GCPauseTotalCount|Antal pauser för GC|Count|Totalt|Antal pauser för GC|AppName, Pod|
|GCPauseTotalTime|Total tid för GC-paus|Millisekunder|Totalt|Total tid för GC-paus|AppName, Pod|
|TomcatSentBytes|Totalt antal skickade byte i Tomcat|Byte|Totalt|Totalt antal skickade byte i Tomcat|AppName, Pod|
|TomcatReceivedBytes|Totalt antal mottagna byte i Tomcat|Byte|Totalt|Totalt antal mottagna byte i Tomcat|AppName, Pod|
|TomcatRequestTotalTime|Totalt antal gånger för Tomcat-begäran|Millisekunder|Totalt|Totalt antal gånger för Tomcat-begäran|AppName, Pod|
|TomcatRequestTotalCount|Totalt antal Tomcat-begäranden|Count|Totalt|Totalt antal Tomcat-begäranden|AppName, Pod|
|TomcatResponseAvgTime|Genomsnittlig tid för Tomcat-begäran|Millisekunder|Medelvärde|Genomsnittlig tid för Tomcat-begäran|AppName, Pod|
|TomcatRequestMaxTime|Max tid för Tomcat-begäran|Millisekunder|Maximal|Max tid för Tomcat-begäran|AppName, Pod|
|TomcatErrorCount|Tomcat globalt fel|Count|Totalt|Tomcat globalt fel|AppName, Pod|
|TomcatSessionActiveMaxCount|Antal aktiva Tomcat-sessioner|Count|Totalt|Antal aktiva Tomcat-sessioner|AppName, Pod|
|TomcatSessionAliveMaxTime|Maximal Alive-tid för Tomcat-session|Millisekunder|Maximal|Maximal Alive-tid för Tomcat-session|AppName, Pod|
|TomcatSessionActiveCurrentCount|Tomcat-session Alive-antal|Count|Totalt|Tomcat-session Alive-antal|AppName, Pod|
|TomcatSessionCreatedCount|Antal skapade Tomcat-sessioner|Count|Totalt|Antal skapade Tomcat-sessioner|AppName, Pod|
|TomcatSessionExpiredCount|Antal utgångna Tomcat-sessioner|Count|Totalt|Antal utgångna Tomcat-sessioner|AppName, Pod|
|TomcatSessionRejectedCount|Antal nekade Tomcat-sessioner|Count|Totalt|Antal nekade Tomcat-sessioner|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Count|Totalt|Det totala antalet jobb|Runbook, status|
|TotalUpdateDeploymentRuns|Antal körningar av uppdateringsdistributionen|Count|Totalt|Totalt antal körningar av program uppdaterings distribution|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Antal datorspecifika körningar av uppdateringsdistributionen|Count|Totalt|Total distributions dator för program uppdatering körs i en körning av program uppdaterings distribution|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CoreCount|Dedikerat antal kärnor|Count|Totalt|Totalt antal dedikerade kärnor i batch-kontot|Inga|
|TotalNodeCount|Antal dedikerade noder|Count|Totalt|Totalt antal dedikerade noder i batch-kontot|Inga|
|LowPriorityCoreCount|Antal LowPriority kärnor|Count|Totalt|Totalt antal låg prioritets kärnor i batch-kontot|Inga|
|TotalLowPriorityNodeCount|Antal noder med låg prioritet|Count|Totalt|Totalt antal noder med låg prioritet i batch-kontot|Inga|
|CreatingNodeCount|Antal noder skapas|Count|Totalt|Antal noder som skapas|Inga|
|StartingNodeCount|Antalet noder startas|Count|Totalt|Antal noder som börjar|Inga|
|WaitingForStartTaskNodeCount|Väntar på att starta aktiviteter antal noder|Count|Totalt|Antal noder som väntar på att start aktiviteten ska slutföras|Inga|
|StartTaskFailedNodeCount|Starta aktivitet antal misslyckade noder|Count|Totalt|Antal noder där start aktiviteten misslyckades|Inga|
|IdleNodeCount|Antal inaktiva noder|Count|Totalt|Antal inaktiva noder|Inga|
|OfflineNodeCount|Antal offline-noder|Count|Totalt|Antal offline-noder|Inga|
|RebootingNodeCount|Antalet noder startas om|Count|Totalt|Antal omstarter av noder|Inga|
|ReimagingNodeCount|Antal noder för avbildning|Count|Totalt|Antal avbildnings noder|Inga|
|RunningNodeCount|Antal noder som körs|Count|Totalt|Antal noder som körs|Inga|
|LeavingPoolNodeCount|Antal noder för att lämna pooler|Count|Totalt|Antal noder som lämnar poolen|Inga|
|UnusableNodeCount|Antal noder som inte kan användas|Count|Totalt|Antal oanvändbara noder|Inga|
|PreemptedNodeCount|Antal misslyckade noder|Count|Totalt|Antal misslyckade noder|Inga|
|TaskStartEvent|Aktivitetens start händelser|Count|Totalt|Totalt antal aktiviteter som har startat|poolId, jobId|
|TaskCompleteEvent|Uppgift slutförda händelser|Count|Totalt|Totalt antal slutförda uppgifter|poolId, jobId|
|TaskFailEvent|Aktivitet, misslyckade händelser|Count|Totalt|Totalt antal uppgifter som har slutförts i felaktigt tillstånd|poolId, jobId|
|PoolCreateEvent|Skapa händelser för pool|Count|Totalt|Totalt antal pooler som har skapats|poolId|
|PoolResizeStartEvent|Starta händelser för att ändra storlek på poolen|Count|Totalt|Totalt antal storleks ändringar för pooler som har startat|poolId|
|PoolResizeCompleteEvent|Slutför händelser för storleks ändring av pool|Count|Totalt|Totalt antal ändrade pooler som har slutförts|poolId|
|PoolDeleteStartEvent|Start händelser för borttagning av pool|Count|Totalt|Totalt antal borttagningar av pooler som har startat|poolId|
|PoolDeleteCompleteEvent|Slutför händelse för borttagning av pool|Count|Totalt|Totalt antal borttagna pooler som har slutförts|poolId|
|JobDeleteCompleteEvent|Slutför händelser för borttagning av jobb|Count|Totalt|Totalt antal jobb som har tagits bort.|jobId|
|JobDeleteStartEvent|Start händelser för jobb borttagning|Count|Totalt|Totalt antal jobb som har begärts att tas bort.|jobId|
|JobDisableCompleteEvent|Jobb för att inaktivera slutförda händelser|Count|Totalt|Totalt antal jobb som har inaktiverats.|jobId|
|JobDisableStartEvent|Jobb inaktivera start händelser|Count|Totalt|Totalt antal jobb som har begärts att inaktive ras.|jobId|
|JobStartEvent|Jobb start händelser|Count|Totalt|Totalt antal jobb som har startats.|jobId|
|JobTerminateCompleteEvent|Slutför händelser för avsluta jobb|Count|Totalt|Totalt antal jobb som har avslut ATS.|jobId|
|JobTerminateStartEvent|Jobb som avslutar start händelser|Count|Totalt|Totalt antal jobb som har begärts att avslutas.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Jobb skickat|Jobb skickat|Count|Totalt|Antal skickade jobb|Scenario, kluster namn|
|Jobbet har slutförts|Jobbet har slutförts|Count|Totalt|Antal slutförda jobb|Scenario, kluster namn, ResultType|
|Totalt antal noder|Totalt antal noder|Count|Medelvärde|Antal totala noder|Scenario, kluster namn|
|Aktiva noder|Aktiva noder|Count|Medelvärde|Antal noder som körs|Scenario, kluster namn|
|Inaktiva noder|Inaktiva noder|Count|Medelvärde|Antal inaktiva noder|Scenario, kluster namn|
|Oanvändbara noder|Oanvändbara noder|Count|Medelvärde|Antal oanvändbara noder|Scenario, kluster namn|
|Misslyckade noder|Misslyckade noder|Count|Medelvärde|Antal misslyckade noder|Scenario, kluster namn|
|Lämnar noder|Lämnar noder|Count|Medelvärde|Antal lämnar noder|Scenario, kluster namn|
|Totalt antal kärnor|Totalt antal kärnor|Count|Medelvärde|Antal total kärnor|Scenario, kluster namn|
|Aktiva kärnor|Aktiva kärnor|Count|Medelvärde|Antal aktiva kärnor|Scenario, kluster namn|
|Inaktiva kärnor|Inaktiva kärnor|Count|Medelvärde|Antal inaktiva kärnor|Scenario, kluster namn|
|Oanvändbara kärnor|Oanvändbara kärnor|Count|Medelvärde|Antal oanvändbara kärnor|Scenario, kluster namn|
|Blockerade kärnor|Blockerade kärnor|Count|Medelvärde|Antal blockerade kärnor|Scenario, kluster namn|
|Lämnar kärnor|Lämnar kärnor|Count|Medelvärde|Antal lämnar kärnor|Scenario, kluster namn|
|Kvot användning i procent|Kvot användning i procent|Count|Medelvärde|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Procent andel CPU-användning|Procent|Maximal|Procent andel CPU-användning|Nod|
|MemoryUsage|Minnesanvändning|Byte|Medelvärde|Minnesanvändning|Nod|
|MemoryLimit|Minnes gräns|Byte|Medelvärde|Minnes gräns|Nod|
|MemoryUsagePercentageInDouble|Minnes användnings procent|Procent|Medelvärde|Minnes användnings procent|Nod|
|StorageUsage|Lagrings användning|Byte|Medelvärde|Lagrings användning|Nod|
|IOReadBytes|Lästa byte i IO|Byte|Totalt|Lästa byte i IO|Nod|
|IOWriteBytes|Skrivna byte i IO|Byte|Totalt|Skrivna byte i IO|Nod|
|ConnectionAccepted|Godkända anslutningar|Count|Totalt|Godkända anslutningar|Nod|
|ConnectionHandled|Hanterade anslutningar|Count|Totalt|Hanterade anslutningar|Nod|
|ConnectionActive|Aktiva anslutningar|Count|Medelvärde|Aktiva anslutningar|Nod|
|RequestHandled|Hanterade begär Anden|Count|Totalt|Hanterade begär Anden|Nod|
|ProcessedBlocks|Bearbetade block|Count|Totalt|Bearbetade block|Nod|
|ProcessedTransactions|Bearbetade transaktioner|Count|Totalt|Bearbetade transaktioner|Nod|
|PendingTransactions|Väntande transaktioner|Count|Medelvärde|Väntande transaktioner|Nod|
|QueuedTransactions|Köade transaktioner|Count|Medelvärde|Köade transaktioner|Nod|



## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedclients|Anslutna klienter|Count|Maximal||ShardId|
|totalcommandsprocessed|Totalt antal åtgärder|Count|Totalt||ShardId|
|cachehits|Cacheträffar|Count|Totalt||ShardId|
|cachemisses|Cachemissar|Count|Totalt||ShardId|
|cachemissrate|Missar i cache|Procent|cachemissrate||ShardId|
|getcommands|Hämtningar|Count|Totalt||ShardId|
|setcommands|Inställningar|Count|Totalt||ShardId|
|operationsPerSecond|Åtgärder per sekund|Count|Maximal||ShardId|
|evictedkeys|Avlägsnade nycklar|Count|Totalt||ShardId|
|totalkeys|Totalt antal nycklar|Count|Maximal||ShardId|
|expiredkeys|Utgångna nycklar|Count|Totalt||ShardId|
|usedmemory|Använt minne|Byte|Maximal||ShardId|
|usedmemorypercentage|Använt minne i procent|Procent|Maximal||ShardId|
|usedmemoryRss|RSS för använt minne|Byte|Maximal||ShardId|
|serverLoad|Serverbelastning|Procent|Maximal||ShardId|
|cacheWrite|Cacheskrivning|BytesPerSecond|Maximal||ShardId|
|cacheRead|Cacheläsning|BytesPerSecond|Maximal||ShardId|
|percentProcessorTime|Processor|Procent|Maximal||ShardId|
|cacheLatency|Mikrosekunder för cache-fördröjning (för hands version)|Count|Medelvärde||ShardId|
|fel|Fel|Count|Maximal||ShardId,ErrorType|
|connectedclients0|Anslutna klienter (Shard 0)|Count|Maximal||Inga|
|totalcommandsprocessed0|Totalt antal åtgärder (Shard 0)|Count|Totalt||Inga|
|cachehits0|Cacheträffar (Shard 0)|Count|Totalt||Inga|
|cachemisses0|Cachemissar (Shard 0)|Count|Totalt||Inga|
|getcommands0|Hämtar (Shard 0)|Count|Totalt||Inga|
|setcommands0|Uppsättningar (Shard 0)|Count|Totalt||Inga|
|operationsPerSecond0|Åtgärder per sekund (Shard 0)|Count|Maximal||Inga|
|evictedkeys0|Avlägsnade nycklar (Shard 0)|Count|Totalt||Inga|
|totalkeys0|Totalt antal nycklar (Shard 0)|Count|Maximal||Inga|
|expiredkeys0|Utgångna nycklar (Shard 0)|Count|Totalt||Inga|
|usedmemory0|Använt minne (Shard 0)|Byte|Maximal||Inga|
|usedmemoryRss0|Använt minne RSS (Shard 0)|Byte|Maximal||Inga|
|serverLoad0|Server belastning (Shard 0)|Procent|Maximal||Inga|
|cacheWrite0|Skrivcache (Shard 0)|BytesPerSecond|Maximal||Inga|
|cacheRead0|Läsning av cache (Shard 0)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime0|PROCESSOR (Shard 0)|Procent|Maximal||Inga|
|connectedclients1|Anslutna klienter (Shard 1)|Count|Maximal||Inga|
|totalcommandsprocessed1|Totalt antal åtgärder (Shard 1)|Count|Totalt||Inga|
|cachehits1|Cacheträffar (Shard 1)|Count|Totalt||Inga|
|cachemisses1|Cachemissar (Shard 1)|Count|Totalt||Inga|
|getcommands1|Hämtar (Shard 1)|Count|Totalt||Inga|
|setcommands1|Uppsättningar (Shard 1)|Count|Totalt||Inga|
|operationsPerSecond1|Åtgärder per sekund (Shard 1)|Count|Maximal||Inga|
|evictedkeys1|Avlägsnade nycklar (Shard 1)|Count|Totalt||Inga|
|totalkeys1|Totalt antal nycklar (Shard 1)|Count|Maximal||Inga|
|expiredkeys1|Utgångna nycklar (Shard 1)|Count|Totalt||Inga|
|usedmemory1|Använt minne (Shard 1)|Byte|Maximal||Inga|
|usedmemoryRss1|RSS för använt minne (Shard 1)|Byte|Maximal||Inga|
|serverLoad1|Server belastning (Shard 1)|Procent|Maximal||Inga|
|cacheWrite1|Skrivcache (Shard 1)|BytesPerSecond|Maximal||Inga|
|cacheRead1|Läsning av cache (Shard 1)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime1|PROCESSOR (Shard 1)|Procent|Maximal||Inga|
|connectedclients2|Anslutna klienter (Shard 2)|Count|Maximal||Inga|
|totalcommandsprocessed2|Totalt antal åtgärder (Shard 2)|Count|Totalt||Inga|
|cachehits2|Cacheträffar (Shard 2)|Count|Totalt||Inga|
|cachemisses2|Cachemissar (Shard 2)|Count|Totalt||Inga|
|getcommands2|Hämtar (Shard 2)|Count|Totalt||Inga|
|setcommands2|Uppsättningar (Shard 2)|Count|Totalt||Inga|
|operationsPerSecond2|Åtgärder per sekund (Shard 2)|Count|Maximal||Inga|
|evictedkeys2|Avlägsnade nycklar (Shard 2)|Count|Totalt||Inga|
|totalkeys2|Totalt antal nycklar (Shard 2)|Count|Maximal||Inga|
|expiredkeys2|Utgångna nycklar (Shard 2)|Count|Totalt||Inga|
|usedmemory2|Använt minne (Shard 2)|Byte|Maximal||Inga|
|usedmemoryRss2|Använt minne RSS (Shard 2)|Byte|Maximal||Inga|
|serverLoad2|Server belastning (Shard 2)|Procent|Maximal||Inga|
|cacheWrite2|Skrivcache (Shard 2)|BytesPerSecond|Maximal||Inga|
|cacheRead2|Läsning av cache (Shard 2)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime2|PROCESSOR (Shard 2)|Procent|Maximal||Inga|
|connectedclients3|Anslutna klienter (Shard 3)|Count|Maximal||Inga|
|totalcommandsprocessed3|Totalt antal åtgärder (Shard 3)|Count|Totalt||Inga|
|cachehits3|Cacheträffar (Shard 3)|Count|Totalt||Inga|
|cachemisses3|Cachemissar (Shard 3)|Count|Totalt||Inga|
|getcommands3|Hämtar (Shard 3)|Count|Totalt||Inga|
|setcommands3|Uppsättningar (Shard 3)|Count|Totalt||Inga|
|operationsPerSecond3|Åtgärder per sekund (Shard 3)|Count|Maximal||Inga|
|evictedkeys3|Avlägsnade nycklar (Shard 3)|Count|Totalt||Inga|
|totalkeys3|Totalt antal nycklar (Shard 3)|Count|Maximal||Inga|
|expiredkeys3|Utgångna nycklar (Shard 3)|Count|Totalt||Inga|
|usedmemory3|Använt minne (Shard 3)|Byte|Maximal||Inga|
|usedmemoryRss3|Använt minne RSS (Shard 3)|Byte|Maximal||Inga|
|serverLoad3|Server belastning (Shard 3)|Procent|Maximal||Inga|
|cacheWrite3|Skrivcache (Shard 3)|BytesPerSecond|Maximal||Inga|
|cacheRead3|Läsning av cacheminne (Shard 3)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime3|PROCESSOR (Shard 3)|Procent|Maximal||Inga|
|connectedclients4|Anslutna klienter (Shard 4)|Count|Maximal||Inga|
|totalcommandsprocessed4|Totalt antal åtgärder (Shard 4)|Count|Totalt||Inga|
|cachehits4|Cacheträffar (Shard 4)|Count|Totalt||Inga|
|cachemisses4|Cachemissar (Shard 4)|Count|Totalt||Inga|
|getcommands4|Hämtar (Shard 4)|Count|Totalt||Inga|
|setcommands4|Uppsättningar (Shard 4)|Count|Totalt||Inga|
|operationsPerSecond4|Åtgärder per sekund (Shard 4)|Count|Maximal||Inga|
|evictedkeys4|Avlägsnade nycklar (Shard 4)|Count|Totalt||Inga|
|totalkeys4|Totalt antal nycklar (Shard 4)|Count|Maximal||Inga|
|expiredkeys4|Utgångna nycklar (Shard 4)|Count|Totalt||Inga|
|usedmemory4|Använt minne (Shard 4)|Byte|Maximal||Inga|
|usedmemoryRss4|Använt minne RSS (Shard 4)|Byte|Maximal||Inga|
|serverLoad4|Server belastning (Shard 4)|Procent|Maximal||Inga|
|cacheWrite4|Skrivcache (Shard 4)|BytesPerSecond|Maximal||Inga|
|cacheRead4|Läsning av cacheminne (Shard 4)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime4|PROCESSOR (Shard 4)|Procent|Maximal||Inga|
|connectedclients5|Anslutna klienter (Shard 5)|Count|Maximal||Inga|
|totalcommandsprocessed5|Totalt antal åtgärder (Shard 5)|Count|Totalt||Inga|
|cachehits5|Cacheträffar (Shard 5)|Count|Totalt||Inga|
|cachemisses5|Cachemissar (Shard 5)|Count|Totalt||Inga|
|getcommands5|Hämtar (Shard 5)|Count|Totalt||Inga|
|setcommands5|Uppsättningar (Shard 5)|Count|Totalt||Inga|
|operationsPerSecond5|Åtgärder per sekund (Shard 5)|Count|Maximal||Inga|
|evictedkeys5|Avlägsnade nycklar (Shard 5)|Count|Totalt||Inga|
|totalkeys5|Totalt antal nycklar (Shard 5)|Count|Maximal||Inga|
|expiredkeys5|Utgångna nycklar (Shard 5)|Count|Totalt||Inga|
|usedmemory5|Använt minne (Shard 5)|Byte|Maximal||Inga|
|usedmemoryRss5|Använt minne RSS (Shard 5)|Byte|Maximal||Inga|
|serverLoad5|Server belastning (Shard 5)|Procent|Maximal||Inga|
|cacheWrite5|Skrivcache (Shard 5)|BytesPerSecond|Maximal||Inga|
|cacheRead5|Läsning av cacheminne (Shard 5)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime5|PROCESSOR (Shard 5)|Procent|Maximal||Inga|
|connectedclients6|Anslutna klienter (Shard 6)|Count|Maximal||Inga|
|totalcommandsprocessed6|Totalt antal åtgärder (Shard 6)|Count|Totalt||Inga|
|cachehits6|Cacheträffar (Shard 6)|Count|Totalt||Inga|
|cachemisses6|Cachemissar (Shard 6)|Count|Totalt||Inga|
|getcommands6|Hämtar (Shard 6)|Count|Totalt||Inga|
|setcommands6|Uppsättningar (Shard 6)|Count|Totalt||Inga|
|operationsPerSecond6|Åtgärder per sekund (Shard 6)|Count|Maximal||Inga|
|evictedkeys6|Avlägsnade nycklar (Shard 6)|Count|Totalt||Inga|
|totalkeys6|Totalt antal nycklar (Shard 6)|Count|Maximal||Inga|
|expiredkeys6|Utgångna nycklar (Shard 6)|Count|Totalt||Inga|
|usedmemory6|Använt minne (Shard 6)|Byte|Maximal||Inga|
|usedmemoryRss6|Använt minne RSS (Shard 6)|Byte|Maximal||Inga|
|serverLoad6|Server belastning (Shard 6)|Procent|Maximal||Inga|
|cacheWrite6|Skrivcache (Shard 6)|BytesPerSecond|Maximal||Inga|
|cacheRead6|Läsning av cache (Shard 6)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime6|PROCESSOR (Shard 6)|Procent|Maximal||Inga|
|connectedclients7|Anslutna klienter (Shard 7)|Count|Maximal||Inga|
|totalcommandsprocessed7|Totalt antal åtgärder (Shard 7)|Count|Totalt||Inga|
|cachehits7|Cacheträffar (Shard 7)|Count|Totalt||Inga|
|cachemisses7|Cachemissar (Shard 7)|Count|Totalt||Inga|
|getcommands7|Hämtar (Shard 7)|Count|Totalt||Inga|
|setcommands7|Uppsättningar (Shard 7)|Count|Totalt||Inga|
|operationsPerSecond7|Åtgärder per sekund (Shard 7)|Count|Maximal||Inga|
|evictedkeys7|Avlägsnade nycklar (Shard 7)|Count|Totalt||Inga|
|totalkeys7|Totalt antal nycklar (Shard 7)|Count|Maximal||Inga|
|expiredkeys7|Utgångna nycklar (Shard 7)|Count|Totalt||Inga|
|usedmemory7|Använt minne (Shard 7)|Byte|Maximal||Inga|
|usedmemoryRss7|Använt minne RSS (Shard 7)|Byte|Maximal||Inga|
|serverLoad7|Server belastning (Shard 7)|Procent|Maximal||Inga|
|cacheWrite7|Skrivcache (Shard 7)|BytesPerSecond|Maximal||Inga|
|cacheRead7|Läsning av cache (Shard 7)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime7|PROCESSOR (Shard 7)|Procent|Maximal||Inga|
|connectedclients8|Anslutna klienter (Shard 8)|Count|Maximal||Inga|
|totalcommandsprocessed8|Totalt antal åtgärder (Shard 8)|Count|Totalt||Inga|
|cachehits8|Cacheträffar (Shard 8)|Count|Totalt||Inga|
|cachemisses8|Cachemissar (Shard 8)|Count|Totalt||Inga|
|getcommands8|Hämtar (Shard 8)|Count|Totalt||Inga|
|setcommands8|Uppsättningar (Shard 8)|Count|Totalt||Inga|
|operationsPerSecond8|Åtgärder per sekund (Shard 8)|Count|Maximal||Inga|
|evictedkeys8|Avlägsnade nycklar (Shard 8)|Count|Totalt||Inga|
|totalkeys8|Totalt antal nycklar (Shard 8)|Count|Maximal||Inga|
|expiredkeys8|Utgångna nycklar (Shard 8)|Count|Totalt||Inga|
|usedmemory8|Använt minne (Shard 8)|Byte|Maximal||Inga|
|usedmemoryRss8|Använt minne RSS (Shard 8)|Byte|Maximal||Inga|
|serverLoad8|Server belastning (Shard 8)|Procent|Maximal||Inga|
|cacheWrite8|Skrivcache (Shard 8)|BytesPerSecond|Maximal||Inga|
|cacheRead8|Läsning av cache (Shard 8)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime8|PROCESSOR (Shard 8)|Procent|Maximal||Inga|
|connectedclients9|Anslutna klienter (Shard 9)|Count|Maximal||Inga|
|totalcommandsprocessed9|Totalt antal åtgärder (Shard 9)|Count|Totalt||Inga|
|cachehits9|Cacheträffar (Shard 9)|Count|Totalt||Inga|
|cachemisses9|Cachemissar (Shard 9)|Count|Totalt||Inga|
|getcommands9|Hämtar (Shard 9)|Count|Totalt||Inga|
|setcommands9|Uppsättningar (Shard 9)|Count|Totalt||Inga|
|operationsPerSecond9|Åtgärder per sekund (Shard 9)|Count|Maximal||Inga|
|evictedkeys9|Avlägsnade nycklar (Shard 9)|Count|Totalt||Inga|
|totalkeys9|Totalt antal nycklar (Shard 9)|Count|Maximal||Inga|
|expiredkeys9|Utgångna nycklar (Shard 9)|Count|Totalt||Inga|
|usedmemory9|Använt minne (Shard 9)|Byte|Maximal||Inga|
|usedmemoryRss9|Använt minne RSS (Shard 9)|Byte|Maximal||Inga|
|serverLoad9|Server belastning (Shard 9)|Procent|Maximal||Inga|
|cacheWrite9|Skrivcache (Shard 9)|BytesPerSecond|Maximal||Inga|
|cacheRead9|Läsning av cacheminne (Shard 9)|BytesPerSecond|Maximal||Inga|
|percentProcessorTime9|PROCESSOR (Shard 9)|Procent|Maximal||Inga|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Antal begär Anden om webb programs brand vägg|Count|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|Inga|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|Inga|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|Inga|
|Disk-lästa byte/s|Disk läsning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|Inga|
|Disk-skrivna byte/s|Disk skrivning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|Inga|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS.|Inga|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk.|Inga|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domän namn/platser/roller

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|RoleInstanceId|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|RoleInstanceId|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|RoleInstanceId|
|Disk-lästa byte/s|Disk läsning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|RoleInstanceId|
|Disk-skrivna byte/s|Disk skrivning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|RoleInstanceId|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS.|RoleInstanceId|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Medelvärde|Kapacitet som används av konto|Inga|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|BLOB-antal|Count|Medelvärde|Antalet BLOB i lagrings kontots Blob Service.|BlobType,-nivå|
|ContainerCount|Antal BLOB-behållare|Count|Medelvärde|Antalet behållare i lagrings kontots Blob Service.|Inga|
|IndexCapacity|Index kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inga|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabell kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Table service i byte.|Inga|
|TableCount|Antal tabeller|Count|Medelvärde|Antalet tabeller i lagrings kontots Table service.|Inga|
|TableEntityCount|Antal tabell enheter|Count|Medelvärde|Antalet tabell enheter i lagrings kontots Table service.|Inga|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Fil kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|FileShare|
|FileCount|Antal filer|Count|Medelvärde|Antalet filer i lagrings kontots fil tjänst.|FileShare|
|FileShareCount|Antal fil resurser|Count|Medelvärde|Antalet fil resurser i lagrings kontots fil tjänst.|Inga|
|FileShareSnapshotCount|Antal ögonblicks bilder av fil resurs|Count|Medelvärde|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|Storlek på fil resursens ögonblicks bild|Byte|Medelvärde|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|FileShareCapacityQuota|Fil resursens kvot storlek|Byte|Medelvärde|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kös Kap kap.|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Kötjänst i byte.|Inga|
|QueueCount|Antal köer|Count|Medelvärde|Antalet köer i lagrings kontots Kötjänst.|Inga|
|QueueMessageCount|Antal meddelanden i kö|Count|Medelvärde|Ungefärligt antal köa meddelanden i lagrings kontots Kötjänst.|Inga|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalCalls|Totalt antal anrop|Count|Totalt|Totalt antal anrop.|ApiName, OperationName, region|
|SuccessfulCalls|Lyckade anrop|Count|Totalt|Antal lyckade anrop.|ApiName, OperationName, region|
|TotalErrors|Totalt antal fel|Count|Totalt|Totalt antal anrop med fel svar (HTTP-svarskod 4xx eller 5xx).|ApiName, OperationName, region|
|BlockedCalls|Blockerade anrop|Count|Totalt|Antal anrop som har överskridit frekvensen eller kvot gränsen.|ApiName, OperationName, region|
|ServerErrors|Server fel|Count|Totalt|Antal anrop med internt tjänst fel (HTTP-svarskod 5xx).|ApiName, OperationName, region|
|ClientErrors|Klient fel|Count|Totalt|Antal anrop med fel på klient sidan (HTTP-svarskod 4xx).|ApiName, OperationName, region|
|Datain|Data i|Byte|Totalt|Storlek på inkommande data i byte.|ApiName, OperationName, region|
|Data|Data ut|Byte|Totalt|Storlek på utgående data i byte.|ApiName, OperationName, region|
|Svarstid|Svarstid|Millisekunder|Medelvärde|Svars tid i millisekunder.|ApiName, OperationName, region|
|TotalTokenCalls|Totalt antal token-anrop|Count|Totalt|Totalt antal token-anrop.|ApiName, OperationName, region|
|CharactersTranslated|Översatta tecken|Count|Totalt|Totalt antal tecken i begäran om inkommande text.|ApiName, OperationName, region|
|CharactersTrained|Upptränade tecken|Count|Totalt|Totalt antal tecken som har tränats.|ApiName, OperationName, region|
|SpeechSessionDuration|Tal sessionens varaktighet|Sekunder|Totalt|Total varaktighet för talläget på några sekunder.|ApiName, OperationName, region|
|TotalTransactions|Totalt antal transaktioner|Count|Totalt|Totalt antal transaktioner.|Inga|
|ProcessedImages|Bearbetade bilder|Count|Totalt| Antal transaktioner för bild bearbetning.|ApiName, FeatureName, kanal, region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inga|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inga|
|Nätverk – utgående|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inga|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inga|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inga|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS|Inga|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk|Inga|
|Återstående CPU-krediter|Återstående CPU-krediter|Count|Medelvärde|Totalt antal krediter som är tillgängliga för burst|Inga|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Count|Medelvärde|Totalt antal krediter som konsumeras av den virtuella datorn|Inga|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk [(inaktuellt)](portal-disk-metrics-deprecation.md)] (portal-disk-Metrics-deprecation.MD)|Count|Medelvärde|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|KÖDJUP för operativ system per disk|OS-KÖDJUP [(inaktuellt)](portal-disk-metrics-deprecation.md)|Count|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Ködjup för datadisk|Data disk Queue djup (för hands version)|Count|Medelvärde|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Count|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga|
|Inkommande flöden|Inkommande flöden|Count|Medelvärde|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inga|
|Utgående flöden|Utgående flöden|Count|Medelvärde|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga|
|Högsta skapande frekvens för inkommande flöden|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Medelvärde|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inga|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden|CountPerSecond|Medelvärde|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inga|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medelvärde|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medelvärde|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Läsning av Premium OS-diskcachen|Inga|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Read missar i Premium OS-diskcachen|Inga|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|VMName|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|VMName|
|Nätverk – utgående|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|VMName|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|VMName|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|VMName|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS|VMName|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk|VMName|
|Återstående CPU-krediter|Återstående CPU-krediter|Count|Medelvärde|Totalt antal krediter som är tillgängliga för burst|Inga|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Count|Medelvärde|Totalt antal krediter som konsumeras av den virtuella datorn|Inga|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk [(inaktuellt)](portal-disk-metrics-deprecation.md)|Count|Medelvärde|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|KÖDJUP för operativ system per disk|OS-KÖDJUP [(inaktuellt)](portal-disk-metrics-deprecation.md)|Count|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|LUN, VMName|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|LUN, VMName|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Ködjup för datadisk|Data disk Queue djup (för hands version)|Count|Medelvärde|Data diskens ködjup (eller Kölängd)|LUN, VMName|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Count|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|VMName|
|Inkommande flöden|Inkommande flöden|Count|Medelvärde|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|VMName|
|Utgående flöden|Utgående flöden|Count|Medelvärde|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|VMName|
|Högsta skapande frekvens för inkommande flöden|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Medelvärde|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|VMName|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden|CountPerSecond|Medelvärde|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|VMName|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medelvärde|Läsning av Premium data disk-cache|LUN, VMName|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medelvärde|Read missar i Premium data-diskcachen|LUN, VMName|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Läsning av Premium OS-diskcachen|VMName|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Read missar i Premium OS-diskcachen|VMName|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|VMName|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. Compute/virtualMachineScaleSets/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inga|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inga|
|Nätverk – utgående|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inga|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inga|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inga|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS|Inga|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk|Inga|
|Återstående CPU-krediter|Återstående CPU-krediter|Count|Medelvärde|Totalt antal krediter som är tillgängliga för burst|Inga|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Count|Medelvärde|Totalt antal krediter som konsumeras av den virtuella datorn|Inga|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk [(inaktuellt)](portal-disk-metrics-deprecation.md)|Count|Medelvärde|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|KÖDJUP för operativ system per disk|OS-KÖDJUP [(inaktuellt)](portal-disk-metrics-deprecation.md)|Count|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Ködjup för datadisk|Data disk Queue djup (för hands version)|Count|Medelvärde|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Count|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga|
|Inkommande flöden|Inkommande flöden|Count|Medelvärde|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inga|
|Utgående flöden|Utgående flöden|Count|Medelvärde|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga|
|Högsta skapande frekvens för inkommande flöden|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Medelvärde|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inga|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden|CountPerSecond|Medelvärde|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inga|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medelvärde|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medelvärde|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Läsning av Premium OS-diskcachen|Inga|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Read missar i Premium OS-diskcachen|Inga|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|Processoranvändning|Count|Medelvärde|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Minnesanvändning|Byte|Medelvärde|Total minnes användning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Mottagna nätverks byte per sekund|Byte|Medelvärde|Mottagna nätverks byte per sekund.|Inga|
|NetworkBytesTransmittedPerSecond|Överförda nätverks byte per sekund|Byte|Medelvärde|Överförda nätverks byte per sekund.|Inga|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalPullCount|Totalt antal hämtningar|Count|Medelvärde|Totalt antal bild hämtningar|Inga|
|SuccessfulPullCount|Antal lyckade pull-överföringar|Count|Medelvärde|Antal lyckade bild hämtningar|Inga|
|TotalPushCount|Totalt antal push-meddelanden|Count|Medelvärde|Totalt antal push-meddelanden för avbildningar|Inga|
|SuccessfulPushCount|Antal lyckade push-meddelanden|Count|Medelvärde|Antal lyckade avbildnings push-meddelanden|Inga|
|RunDuration|Körnings tid|Millisekunder|Totalt|Körnings tid i millisekunder|Inga|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Count|Medelvärde|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Inga|
|kube_node_status_allocatable_memory_bytes|Total mängd tillgängligt minne i ett hanterat kluster|Byte|Medelvärde|Total mängd tillgängligt minne i ett hanterat kluster|Inga|
|kube_pod_status_ready|Antal poddar i klart läge|Count|Medelvärde|Antal poddar i klart läge|namnrymd, Pod|
|kube_node_status_condition|Status för olika nod villkor|Count|Medelvärde|Status för olika nod villkor|villkor, status, status2, nod|
|kube_pod_status_phase|Antal poddar per fas|Count|Medelvärde|Antal poddar per fas|fas, namnrymd, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfullRequests|Slutförda förfrågningar|Count|Totalt|Lyckade förfrågningar gjorda av den anpassade providern|HttpMethod, CallPath, StatusCode|
|FailedRequests|Misslyckade begäranden|Count|Totalt|Hämtar de tillgängliga loggarna för anpassade resurs leverantörer|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|NICReadThroughput|Läs data flöde (nätverk)|BytesPerSecond|Medelvärde|Läs data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|NICWriteThroughput|Skriv data flöde (nätverk)|BytesPerSecond|Medelvärde|Skriv data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|CloudReadThroughputPerShare|Data flöde för moln hämtning (resurs)|BytesPerSecond|Medelvärde|Ladda ned data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|CloudUploadThroughputPerShare|Moln överförings data flöde (resurs)|BytesPerSecond|Medelvärde|Ladda upp data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|BytesUploadedToCloudPerShare|Överförda moln byte (resurs)|Byte|Medelvärde|Det totala antalet byte som överförs till Azure från en resurs under rapporterings perioden.|Dela|
|Enhet|Total kapacitet|Byte|Medelvärde|Total kapacitet|Inga|
|Availablecapacity;)|Tillgänglig kapacitet|Byte|Medelvärde|Tillgänglig kapacitet i byte under rapporterings perioden.|Inga|
|CloudUploadThroughput|Moln överförings data flöde|BytesPerSecond|Medelvärde|Moln överförings data flödet till Azure under rapporterings perioden.|Inga|
|CloudReadThroughput|Data flöde för moln hämtning|BytesPerSecond|Medelvärde|Molnet Ladda ned data flödet till Azure under rapporterings perioden.|Inga|
|BytesUploadedToCloud|Överförda moln byte (enhet)|Byte|Medelvärde|Det totala antalet byte som överförs till Azure från en enhet under rapporterings perioden.|Inga|
|HyperVVirtualProcessorUtilization|Edge Compute-procent CPU|Procent|Medelvärde|CPU-användning i procent|InstanceName|
|HyperVMemoryUtilization|Edge Compute-minnes användning|Procent|Medelvärde|Mängden RAM-minne som används|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FailedRuns|Misslyckade körningar|Count|Totalt||pipelineName, activityName|
|SuccessfulRuns|Lyckade körningar|Count|Totalt||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PipelineFailedRuns|Misslyckad pipeline kör mått|Count|Totalt||FailureType, namn|
|PipelineSucceededRuns|Slutförd pipeline kör mått|Count|Totalt||FailureType, namn|
|PipelineCancelledRuns|Avbruten pipeline kör mått|Count|Totalt||FailureType, namn|
|ActivityFailedRuns|Misslyckad aktivitet kör mått|Count|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Genomförd aktivitet kör mått|Count|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivityCancelledRuns|Avbrutna aktiviteter kör mått|Count|Totalt||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Misslyckad utlösare kör mått|Count|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Lyckade utlösare kör mått|Count|Totalt||Namn, FailureType|
|TriggerCancelledRuns|Avbrutna utlösare kör mått|Count|Totalt||Namn, FailureType|
|IntegrationRuntimeCpuPercentage|PROCESSOR användning för integration runtime|Procent|Medelvärde||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAvailableMemory|Tillgängligt minne för integration runtime|Byte|Medelvärde||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAverageTaskPickupDelay|Varaktighet för integration runtime-kö|Sekunder|Medelvärde||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Kölängd för integration runtime|Count|Medelvärde||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Antal tillgängliga noder för integration runtime|Count|Medelvärde||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maximalt antal tillåtna entiteter|Count|Maximal||Inga|
|MaxAllowedFactorySizeInGbUnits|Högsta tillåtna fabriks storlek (GB enhet)|Count|Maximal||Inga|
|ResourceCount|Totalt antal entiteter|Count|Maximal||Inga|
|FactorySizeInGbUnits|Total fabriks storlek (GB enhet)|Count|Maximal||Inga|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobEndedSuccess|Slutförda jobb|Count|Totalt|Antal lyckade jobb.|Inga|
|JobEndedFailure|Misslyckade jobb|Count|Totalt|Antal misslyckade jobb.|Inga|
|JobEndedCancelled|Avbrutna jobb|Count|Totalt|Antal avbrutna jobb.|Inga|
|JobAUEndedSuccess|Lyckad AU-tid|Sekunder|Totalt|Total AU-tid för lyckade jobb.|Inga|
|JobAUEndedFailure|Misslyckad AU-tid|Sekunder|Totalt|Total AU-tid för misslyckade jobb.|Inga|
|JobAUEndedCancelled|Tid för avbruten AU|Sekunder|Totalt|Total AU-tid för avbrutna jobb.|Inga|
|JobStage|Jobb i steg|Count|Totalt|Antal jobb i varje steg.|Inga|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Byte|Maximal|Den totala mängden data som lagras i kontot.|Inga|
|DataWritten|Skrivna data|Byte|Totalt|Total mängd data som skrivs till kontot.|Inga|
|DataRead|Lästa data|Byte|Totalt|Total mängd data som lästs från kontot.|Inga|
|WriteRequests|Skriv förfrågningar|Count|Totalt|Antal data skrivnings begär anden till kontot.|Inga|
|ReadRequests|Läs begär Anden|Count|Totalt|Antal data läsnings begär anden till kontot.|Inga|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ShareCount|Skickade resurser|Count|Maximal|Antal skickade resurser i kontot|Resurs|
|ShareSubscriptionCount|Mottagna resurser|Count|Maximal|Antal mottagna resurser i kontot|ShareSubscriptionName|
|SucceededShareSynchronizations|Slutförda ögonblicks bilder av skickade resurser|Count|Count|Antal skickade delade resurser i kontot|Inga|
|FailedShareSynchronizations|Misslyckade ögonblicks bilder av skickade resurser|Count|Count|Antal skickade resurs misslyckade ögonblicks bilder i kontot|Inga|
|SucceededShareSubscriptionSynchronizations|Mottagna delade ögonblicks bilder|Count|Count|Antal mottagna delade ögonblicks bilder i kontot|Inga|
|FailedShareSubscriptionSynchronizations|Mottagna ögonblicks bilder av resurs misslyckades|Count|Count|Antal mottagna resurs misslyckade ögonblicks bilder i kontot|Inga|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inga|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inga|
|io_consumption_percent|I/o procent|Procent|Medelvärde|I/o procent|Inga|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga|
|storage_limit|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medelvärde|Server logg lagrings procent|Inga|
|serverlog_storage_usage|Server logg lagring används|Byte|Medelvärde|Server logg lagring används|Inga|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Medelvärde|Server logg lagrings gräns|Inga|
|active_connections|Aktiva anslutningar|Count|Medelvärde|Aktiva anslutningar|Inga|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Count|Maximal|Fördröjning för replikering på några sekunder|Inga|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Medelvärde|Lagring av säkerhets kopior som används|Inga|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inga|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inga|
|io_consumption_percent|I/o procent|Procent|Medelvärde|I/o procent|Inga|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga|
|storage_limit|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medelvärde|Server logg lagrings procent|Inga|
|serverlog_storage_usage|Server logg lagring används|Byte|Medelvärde|Server logg lagring används|Inga|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inga|
|active_connections|Aktiva anslutningar|Count|Medelvärde|Aktiva anslutningar|Inga|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Count|Maximal|Fördröjning för replikering på några sekunder|Inga|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Medelvärde|Lagring av säkerhets kopior som används|Inga|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inga|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inga|
|io_consumption_percent|I/o procent|Procent|Medelvärde|I/o procent|Inga|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga|
|storage_limit|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medelvärde|Server logg lagrings procent|Inga|
|serverlog_storage_usage|Server logg lagring används|Byte|Medelvärde|Server logg lagring används|Inga|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inga|
|active_connections|Aktiva anslutningar|Count|Medelvärde|Aktiva anslutningar|Inga|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Medelvärde|Lagring av säkerhets kopior som används|Inga|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga|
|pg_replica_log_delay_in_seconds|Replik fördröjning|Sekunder|Maximal|Replik fördröjning i sekunder|Inga|
|pg_replica_log_delay_in_bytes|Maximal fördröjning mellan repliker|Byte|Maximal|Fördröjning i byte för den flesta isolerings repliken|Inga|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inga|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inga|
|IOPS|IOPS|Count|Medelvärde|I/o-åtgärder per sekund|Inga|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga|
|active_connections|Aktiva anslutningar|Count|Medelvärde|Aktiva anslutningar|Inga|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inga|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inga|
|IOPS|IOPS|Count|Medelvärde|I/o-åtgärder per sekund|Inga|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga|
|active_connections|Aktiva anslutningar|Count|Medelvärde|Aktiva anslutningar|Inga|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga|
|connections_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga|
|connections_succeeded|Lyckade anslutningar|Count|Totalt|Lyckade anslutningar|Inga|
|maximum_used_transactionIDs|Högsta antal använda transaktions-ID: n|Count|Medelvärde|Högsta antal använda transaktions-ID: n|Inga|





## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|D2C. telemetri. ingress. allProtocol|Skicka försök för telemetri|Count|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inga|
|D2C. telemetri. ingress. lyckades|Meddelande om telemetri|Count|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inga|
|C2D. commands. utgående. Complete. lyckades|C2D meddelande leveranser har slutförts|Count|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Inga|
|C2D. commands. utgående. Abandon. lyckades|Övergivna C2D-meddelanden|Count|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Inga|
|C2D. commands. rekasta. Success|Avvisade C2D-meddelanden|Count|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inga|
|C2DMessagesExpired|C2D meddelanden har förfallit (förhands granskning)|Count|Totalt|Antal utgångna meddelanden från moln till enhet|Inga|
|enheter. totalDevices|Totalt antal enheter (inaktuella)|Count|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inga|
|Devices. connectedDevices. allProtocol|Anslutna enheter (inaktuella) |Count|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga|
|D2C. telemetri. utgående. lyckades|Routning: telemetri meddelanden levereras|Count|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inga|
|D2C. telemetri. utgående.|Routning: telemetri ignoreras |Count|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inga|
|D2C. telemetri. utgående. överblivna|Routning: telemetri-meddelanden har överblivna |Count|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inga|
|D2C. telemetri. utgående. ogiltig|Routning: telemetri-meddelanden är inkompatibla|Count|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inga|
|D2C. telemetri. utgående. fallback|Routning: meddelanden levererade till reserv|Count|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inga|
|D2C. endpoints. utgående. eventHubs|Routning: meddelanden levererade till Händelsehubben|Count|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inga|
|D2C. endpoints. latens. eventHubs|Routning: meddelande fördröjning för Event Hub|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inga|
|D2C. endpoints. utgående. serviceBusQueues|Routning: meddelanden levererade till Service Bus kö|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inga|
|D2C. endpoints. latens. serviceBusQueues|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inga|
|D2C. endpoints. utgående. serviceBusTopics|Routning: meddelanden levererade till Service Bus ämnet|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inga|
|D2C. endpoints. latens. serviceBusTopics|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inga|
|D2C. endpoints. utgående. Builtity. events|Routning: meddelanden som levereras till meddelanden/händelser|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser).|Inga|
|D2C. endpoints. latens. Builtin. events|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser).|Inga|
|D2C. endpoints. utgående. Storage|Routning: meddelanden som levereras till lagring|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inga|
|D2C. endpoints. svars tid. Storage|Routning: meddelande fördröjning för lagring|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inga|
|D2C. endpoints. utgående. Storage. byte|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inga|
|D2C. endpoints. utgående. Storage. blob|Routning: blobbar levererade till lagring|Count|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inga|
|EventGridDeliveries|Event Grid leveranser (för hands version)|Count|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen ( https://aka.ms/ioteventgrid) .|ResourceId, resultat, EventType|
|EventGridLatency|Event Grid svars tid (för hands version)|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|ResourceId, EventType|
|RoutingDeliveries|Dirigera leveranser (för hands version)|Millisekunder|Totalt|Antalet gånger IoT Hub försökte leverera meddelanden till alla slut punkter med hjälp av routning. Använd resultat dimensionen för att se antalet lyckade eller misslyckade försök. Om du vill se orsaken till fel, som ogiltig, släppt eller föräldralös, använder du FailureReasonCategory-dimensionen. Du kan också använda dimensionerna EndpointName och EndpointType för att förstå hur många meddelanden som levererats till dina olika slut punkter. Mått värdet ökar med ett för varje leverans försök, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|ResourceId, EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Leverans svars tid för routning (för hands version)|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att förstå svars tiden för dina olika slut punkter.|ResourceId, EndpointType, EndpointName, RoutingSource|
|D2C. delad. lyckades|Lyckades dubbla läsningar från enheter|Count|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inga|
|D2C. delad.|Misslyckade dubbla läsningar från enheter|Count|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inga|
|D2C., delad. storlek|Svars storlek för dubbla läsningar från enheter|Byte|Medelvärde|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inga|
|D2C. delad. Update. lyckades|Lyckade dubbla uppdateringar från enheter|Count|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inga|
|D2C. delad. Update. Failure|Misslyckade dubbla uppdateringar från enheter|Count|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inga|
|D2C. dubbla. Update. size|Storlek på dubbla uppdateringar från enheter|Byte|Medelvärde|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inga|
|C2D. Methods. Success|Direkta metod anrop|Count|Totalt|Antalet lyckade direkta metod anrop.|Inga|
|C2D. Methods. Failure|Misslyckade direkta metod anrop|Count|Totalt|Antalet misslyckade direkta metod anrop.|Inga|
|C2D. Methods. requestSize|Begär ande storlek för direkta metod anrop|Byte|Medelvärde|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inga|
|C2D. Methods. responseSize|Svars storlek för direkta metod anrop|Byte|Medelvärde|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inga|
|C2D. delad. lyckades|Lyckades dubbla läspaket från Server delen|Count|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inga|
|C2D. delad.|Det gick inte att dubbla läsningar från Server delen|Count|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inga|
|C2D., delad. storlek|Svars storlek för dubbla läsningar från Server delen|Byte|Medelvärde|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inga|
|C2D. delad. Update. lyckades|Lyckades dubbla uppdateringar från Server delen|Count|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inga|
|C2D. delad. Update. Failure|Misslyckade dubbla uppdateringar från Server delen|Count|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inga|
|C2D. dubbla. Update. size|Storlek på dubbla uppdateringar från Server delen|Byte|Medelvärde|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inga|
|twinQueries. lyckades|Lyckades dubbla frågor|Count|Totalt|Antalet lyckade dubbla frågor.|Inga|
|twinQueries. Failure|Misslyckade dubbla frågor|Count|Totalt|Antalet misslyckade dubbla frågor.|Inga|
|twinQueries.resultSize|Resultat storlek för dubbla frågor|Byte|Medelvärde|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inga|
|Jobs. createTwinUpdateJob. lyckades|Skapandet av dubbla uppdaterings jobb lyckades|Count|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inga|
|Jobs. createTwinUpdateJob. Failure|Det gick inte att skapa dubbla uppdaterings jobb|Count|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inga|
|Jobs. createDirectMethodJob. lyckades|Lyckade skapande av metod anrops jobb|Count|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inga|
|Jobs. createDirectMethodJob. Failure|Det gick inte att skapa metod anrops jobb|Count|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inga|
|Jobs. listJobs. lyckades|Lyckade anrop till List jobb|Count|Totalt|Antalet lyckade anrop till List jobb.|Inga|
|Jobs. listJobs. Failure|Misslyckade anrop till List jobb|Count|Totalt|Antalet misslyckade anrop till List jobb.|Inga|
|Jobs. cancelJob. lyckades|Slutförda jobb avbokningar|Count|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga|
|Jobs. cancelJob. Failure|Misslyckade jobb-annulleringar|Count|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga|
|Jobs. queryJobs. lyckades|Slutförda jobb frågor|Count|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inga|
|Jobs. queryJobs. Failure|Misslyckade jobb frågor|Count|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inga|
|jobb. slutfört|Slutförda jobb|Count|Totalt|Antalet slutförda jobb.|Inga|
|jobb. misslyckades|Misslyckade jobb|Count|Totalt|Antalet misslyckade jobb.|Inga|
|D2C. telemetri. ingress. sendThrottle|Antal begränsnings fel|Count|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inga|
|dailyMessageQuotaUsed|Totalt antal meddelanden som används|Count|Medelvärde|Antal sammanlagt antal meddelanden som använts idag|Inga|
|deviceDataUsage|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga|
|deviceDataUsageV2|Total användning av enhets data (för hands version)|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga|
|totalDeviceCount|Totalt antal enheter (förhands granskning)|Count|Medelvärde|Antal enheter som har registrerats för din IoT-hubb|Inga|
|connectedDeviceCount|Anslutna enheter (förhands granskning)|Count|Medelvärde|Antal enheter som är anslutna till din IoT-hubb|Inga|
|konfigurationer|Konfigurations mått|Count|Totalt|Mått för konfigurations åtgärder|Inga|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Registrerings försök|Count|Totalt|Antal enhets registreringar som försökts|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Tilldelade enheter|Count|Totalt|Antal enheter som har tilldelats till en IoT-hubb|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Attesterings försök|Count|Totalt|Antal försök till enhets attestering|ProvisioningServiceName, status, protokoll|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AddRegion|Region tillagt|Count|Count|Region tillagt|Region|
|AvailableStorage|Tillgängligt lagringsutrymme|Byte|Totalt|Totalt tillgängligt lagrings utrymme som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|CassandraConnectionClosures|Cassandra-anslutningens stängningar|Count|Totalt|Antalet Cassandra-anslutningar som stängdes, rapporteras med en 1 minuts kornig het|APIType, region, ClosureReason|
|CassandraKeyspaceDelete|Cassandra-tecken utrymme borttaget|Count|Count|Cassandra-tecken utrymme borttaget|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraKeyspaceThroughputUpdate|Cassandra-dataflöde har uppdaterats|Count|Count|Cassandra-dataflöde har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra-disk utrymme uppdaterat|Count|Count|Cassandra-disk utrymme uppdaterat|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraRequestCharges|Avgifter för Cassandra-begäran|Count|Totalt|Ru: er förbrukat för Cassandra begär Anden|APIType, DatabaseName, samlings region, OperationType, ResourceType|
|CassandraRequests|Cassandra-begäranden|Count|Count|Antal Cassandra-begäranden som gjorts|APIType, DatabaseName, samlings region, OperationType, ResourceType, ErrorCode|
|CassandraTableDelete|Cassandra-tabellen har tagits bort|Count|Count|Cassandra-tabellen har tagits bort|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|Cassandra tabell data flöde har uppdaterats|Count|Count|Cassandra tabell data flöde har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Cassandra-tabellen har uppdaterats|Count|Count|Cassandra-tabellen har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CreateAccount|Kontot har skapats|Count|Count|Kontot har skapats|Inga|
|DataUsage|Dataanvändning|Byte|Totalt|Total data användning rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|DeleteAccount|Kontot har tagits bort|Count|Count|Kontot har tagits bort|Inga|
|DocumentCount|Antal dokument|Count|Totalt|Totalt antal dokument som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentQuota|Dokument kvot|Byte|Totalt|Total lagrings kvot som rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|GremlinDatabaseDelete|Gremlin-databasen har tagits bort|Count|Count|Gremlin-databasen har tagits bort|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinDatabaseThroughputUpdate|Gremlin Database-genomflöde har uppdaterats|Count|Count|Gremlin Database-genomflöde har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Gremlin-databasen har uppdaterats|Count|Count|Gremlin-databasen har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphDelete|Gremlin Graph borttagen|Count|Count|Gremlin Graph borttagen|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|Gremlin Graph-genomflöde har uppdaterats|Count|Count|Gremlin Graph-genomflöde har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Gremlin-diagrammet har uppdaterats|Count|Count|Gremlin-diagrammet har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|Indexanvändning|Byte|Totalt|Total användning av index rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|MetadataRequests|Begär Anden om metadata|Count|Count|Antal metadata-begäranden. Cosmos DB behåller systemets metadata-samling för varje konto, vilket gör att du kan räkna upp samlingar, databaser osv och deras konfigurationer kostnads fritt.|DatabaseName, samlings namn, region, StatusCode, roll|
|MongoCollectionDelete|Mongo-samlingen har tagits bort|Count|Count|Mongo-samlingen har tagits bort|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|Mongo Collection-genomflöde har uppdaterats|Count|Count|Mongo Collection-genomflöde har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Mongo-samlingen har uppdaterats|Count|Count|Mongo-samlingen har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Mongo-databasen har uppdaterats|Count|Count|Mongo-databasen har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDatabaseDelete|Mongo-databasen har tagits bort|Count|Count|Mongo-databasen har tagits bort|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoDatabaseThroughputUpdate|Mongo Database-genomflöde har uppdaterats|Count|Count|Mongo Database-genomflöde har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoRequestCharge|Mongo begär ande avgift|Count|Totalt|Mongo enheter för förbrukad begäran|DatabaseName, samlings namn, region, CommandName, ErrorCode, status|
|MongoRequests|Mongo-begäranden|Count|Count|Antal Mongo-begäranden som gjorts|DatabaseName, samlings namn, region, CommandName, ErrorCode, status|
|MongoRequestsCount|Mongo begär ande frekvens|CountPerSecond|Medelvärde|Antal Mongo begär Anden per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsDelete|Mongo ta bort begär ande frekvens|CountPerSecond|Medelvärde|Mongo ta bort begäran per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsInsert|Mongo infoga begär ande frekvens|CountPerSecond|Medelvärde|Antal Mongo infogningar per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsQuery|Mongo för förfrågningar|CountPerSecond|Medelvärde|Mongo-förfrågan per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsUpdate|Frekvens för mongo uppdaterings begär Anden|CountPerSecond|Medelvärde|Mongo för begär ande uppdatering per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|NormalizedRUConsumption|Normaliserad RU-förbrukning|Procent|Maximal|Max procent per minut för RU-förbrukning|Samlings namn, DatabaseName, region|
|ProvisionedThroughput|Etablerat dataflöde|Count|Maximal|Etablerat dataflöde|DatabaseName, samlings namn|
|RegionFailover|Regionen har redundansväxlats|Count|Count|Regionen har redundansväxlats|Inga|
|RemoveRegion|Region borttagen|Count|Count|Region borttagen|Region|
|ReplicationLatency|Fördröjning för P99-replikering|Millisekunder|Medelvärde|P99 för replikering i käll-och mål regioner för geo-aktiverat konto|SourceRegion,TargetRegion|
|ServerSideLatency|Svars tid på Server Sidan|Millisekunder|Medelvärde|Svars tid på Server Sidan|DatabaseName, samlings namn, region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Tjänst tillgänglighet|Procent|Medelvärde|Konto begär tillgänglighet på en timme, dag eller månads kornig het|Inga|
|SqlContainerDelete|SQL-behållare borttagen|Count|Count|SQL-behållare borttagen|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate|SQL container data flöde uppdaterat|Count|Count|SQL container data flöde uppdaterat|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|SQL-behållare har uppdaterats|Count|Count|SQL-behållare har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete|SQL-databas borttagen|Count|Count|SQL-databas borttagen|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlDatabaseThroughputUpdate|SQL Database-dataflöde uppdaterat|Count|Count|SQL Database-dataflöde uppdaterat|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|SQL-databas uppdaterad|Count|Count|SQL-databas uppdaterad|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableDelete|AzureTable-tabellen har tagits bort|Count|Count|AzureTable-tabellen har tagits bort|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|TableTableThroughputUpdate|AzureTable tabell data flöde har uppdaterats|Count|Count|AzureTable tabell data flöde har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|AzureTable-tabellen har uppdaterats|Count|Count|AzureTable-tabellen har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TotalRequestUnits|Totalt antal enheter för programbegäran|Count|Totalt|Förbrukade enheter för begär Ande|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|TotalRequests|Totalt antal förfrågningar|Count|Count|Antal begär Anden som gjorts|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|UpdateAccountKeys|Konto nycklar har uppdaterats|Count|Count|Konto nycklar har uppdaterats|KeyType|
|UpdateAccountNetworkSettings|Kontots nätverks inställningar har uppdaterats|Count|Count|Kontots nätverks inställningar har uppdaterats|Inga|
|UpdateAccountReplicationSettings|Kontots replikeringsinställningar har uppdaterats|Count|Count|Kontots replikeringsinställningar har uppdaterats|Inga|
|UpdateDiagnosticsSettings|Inställningarna för konto diagnostik har uppdaterats|Count|Count|Inställningarna för konto diagnostik har uppdaterats|DiagnosticSettingsName, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/Services

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TransactionCount|Antal transaktioner|Count|Count|Totalt antal transaktioner|TransactionCount|
|SuccessCount|Antal lyckade|Count|Count|Antal genomförda transaktioner|SuccessCount|
|FailureCount|Antal misslyckade|Count|Count|Antal misslyckade transaktioner|FailureCount|
|SuccessLatency|Svars tid|Millisekunder|Medelvärde|Svars tid för lyckade transaktioner|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Count|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Avsnitt|
|PublishFailCount|Publicera misslyckade händelser|Count|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|Ämne, ErrorType, fel|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga|
|MatchedEventCount|Matchade händelser|Count|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Count|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Count|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Ignorerade händelser|Count|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Obeställbara, Brevade händelser|Count|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Count|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga|
|PublishFailCount|Publicera misslyckade händelser|Count|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Count|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga|
|MatchedEventCount|Matchade händelser|Count|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Count|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Levererade händelser|Count|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ignorerade händelser|Count|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason,EventSubscriptionName|
|DeadLetteredCount|Obeställbara, Brevade händelser|Count|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Count|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga|
|PublishFailCount|Publicera misslyckade händelser|Count|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Count|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga|
|MatchedEventCount|Matchade händelser|Count|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Count|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Levererade händelser|Count|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ignorerade händelser|Count|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason,EventSubscriptionName|
|DeadLetteredCount|Obeställbara, Brevade händelser|Count|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MatchedEventCount|Matchade händelser|Count|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inga|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Count|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Count|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inga|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|Inga|
|DroppedEventCount|Ignorerade händelser|Count|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|DeadLetteredCount|Obeställbara, Brevade händelser|Count|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Count|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga|
|PublishFailCount|Publicera misslyckade händelser|Count|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Count|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga|




## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Slutförda förfrågningar|Count|Totalt|Slutförda begär Anden för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|ServerErrors|Serverfel.|Count|Totalt|Server fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|UserErrors|Användarfel.|Count|Totalt|Användar fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|QuotaExceededErrors|Fel på grund av överskriden kvot.|Count|Totalt|Kvoten överskred fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|ThrottledRequests|Begränsade förfrågningar.|Count|Totalt|Begränsade begär Anden för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|IncomingRequests|Inkommande förfrågningar|Count|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Entitetsnamnet|
|IncomingMessages|Inkommande meddelanden|Count|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|OutgoingMessages|Utgående meddelanden|Count|Totalt|Utgående meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Entitetsnamnet|
|OutgoingBytes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Entitetsnamnet|
|ActiveConnections|ActiveConnections|Count|Medelvärde|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inga|
|ConnectionsOpened|Öppna anslutningar.|Count|Medelvärde|Anslutningar som har öppnats för Microsoft. EventHub.|Entitetsnamnet|
|ConnectionsClosed|Stängda anslutningar.|Count|Medelvärde|Stängda anslutningar för Microsoft. EventHub.|Entitetsnamnet|
|CaptureBacklog|Samla in efter släpning.|Count|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Entitetsnamnet|
|CapturedMessages|Fångade meddelanden.|Count|Totalt|Fångade meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|CapturedBytes|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Entitetsnamnet|
|Storlek|Storlek|Byte|Medelvärde|Storlek på en EventHub i byte.|Entitetsnamnet|
|INREQS|Inkommande begär Anden (inaktuellt)|Count|Totalt|Totalt antal inkommande sändnings begär Anden för en namnrymd (inaktuell)|Inga|
|SUCCREQ|Lyckade förfrågningar (inaktuellt)|Count|Totalt|Totalt antal slutförda begär Anden för en namnrymd (inaktuell)|Inga|
|FAILREQ|Misslyckade förfrågningar (inaktuellt)|Count|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga|
|SVRBSY|Serverns upptaget fel (inaktuellt)|Count|Totalt|Totalt antal upptagen server-fel för en namnrymd (inaktuell)|Inga|
|MELLAN|Interna Server fel (inaktuellt)|Count|Totalt|Totalt antal interna Server fel för en namnrymd (inaktuell)|Inga|
|MISCERR|Andra fel (inaktuellt)|Count|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga|
|INMSGS|Inkommande meddelanden (inaktuella)|Count|Totalt|Totalt antal inkommande meddelanden för ett namn område. Måttet är föråldrat. Använd måtten inkommande e-postmeddelanden i stället (inaktuellt)|Inga|
|EHINMSGS|Inkommande meddelanden (inaktuellt)|Count|Totalt|Totalt antal inkommande meddelanden för en namnrymd (inaktuell)|Inga|
|OUTMSGS|Utgående meddelanden (inaktuella)|Count|Totalt|Totalt antal utgående meddelanden för ett namn område. Måttet är föråldrat. Använd utgående meddelande mått i stället (inaktuellt)|Inga|
|EHOUTMSGS|Utgående meddelanden (inaktuellt)|Count|Totalt|Totalt antal utgående meddelanden för en namnrymd (inaktuell)|Inga|
|EHINMBS|Inkommande byte (inaktuell) (inaktuell)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för ett namn område. Måttet är föråldrat. Använd måttet inkommande byte i stället (inaktuellt)|Inga|
|EHINBYTES|Inkommande byte (inaktuellt)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för en namnrymd (inaktuell)|Inga|
|EHOUTMBS|Utgående byte (inaktuell) (inaktuell)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd. Måttet är föråldrat. Använd utgående byte-mått i stället (inaktuellt)|Inga|
|EHOUTBYTES|Utgående byte (inaktuellt)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd (inaktuell)|Inga|
|EHABL|Arkivera efter släpning meddelanden (inaktuellt)|Count|Totalt|Event Hub arkivera meddelanden i efter släpning för en namnrymd (inaktuell)|Inga|
|EHAMSGS|Arkivera meddelanden (inaktuellt)|Count|Totalt|Arkiverade meddelanden i Event Hub i ett namn område (inaktuellt)|Inga|
|EHAMBS|Arkiv meddelande genom strömning (inaktuellt)|Byte|Totalt|Det arkiverade meddelande flödet i Event Hub i ett namn område (inaktuellt)|Inga|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/Clusters

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Slutförda förfrågningar|Count|Totalt|Slutförda begär Anden för Microsoft. EventHub.|OperationResult under pågående|
|ServerErrors|Serverfel.|Count|Totalt|Server fel för Microsoft. EventHub.|OperationResult under pågående|
|UserErrors|Användarfel.|Count|Totalt|Användar fel för Microsoft. EventHub.|OperationResult under pågående|
|QuotaExceededErrors|Fel på grund av överskriden kvot.|Count|Totalt|Kvoten överskred fel för Microsoft. EventHub.|OperationResult under pågående|
|ThrottledRequests|Begränsade förfrågningar.|Count|Totalt|Begränsade begär Anden för Microsoft. EventHub.|OperationResult under pågående|
|IncomingRequests|Inkommande förfrågningar|Count|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Inga|
|IncomingMessages|Inkommande meddelanden|Count|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Inga|
|OutgoingMessages|Utgående meddelanden|Count|Totalt|Utgående meddelanden för Microsoft. EventHub.|Inga|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Inga|
|OutgoingBytes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Inga|
|ActiveConnections|ActiveConnections|Count|Medelvärde|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inga|
|ConnectionsOpened|Öppna anslutningar.|Count|Medelvärde|Anslutningar som har öppnats för Microsoft. EventHub.|Inga|
|ConnectionsClosed|Stängda anslutningar.|Count|Medelvärde|Stängda anslutningar för Microsoft. EventHub.|Inga|
|CaptureBacklog|Samla in efter släpning.|Count|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Inga|
|CapturedMessages|Fångade meddelanden.|Count|Totalt|Fångade meddelanden för Microsoft. EventHub.|Inga|
|CapturedBytes|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Inga|
|Processor|Processor|Procent|Maximal|CPU-användning för Event Hub-klustret i procent|Roll|
|AvailableMemory|Tillgängligt minne|Procent|Maximal|Tillgängligt minne för Event Hub-klustret som en procent andel av det totala minnet.|Roll|
|Storlek|Storlek på en EventHub i byte.|Byte|Medelvärde|Storlek på en EventHub i byte.|Roll|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kluster

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway-begäranden|Count|Totalt|Antal Gateway-begäranden|HttpStatus|
|CategorizedGatewayRequests|Kategoriserade Gateway-begäranden|Count|Totalt|Antal Gateway-begäranden per kategori (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Antal aktiva arbetare|Count|Maximal|Antal aktiva arbetare|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerat mått värde|Count|Medelvärde|Det värde som beräknas genom autoskalning vid körning|MetricTriggerSource|
|MetricThreshold|Mätnings tröskel|Count|Medelvärde|Det konfigurerade tröskelvärdet för autoskalning vid autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Observerad kapacitet|Count|Medelvärde|Kapaciteten som rapporteras till autoskalning när den kördes.|Inga|
|ScaleActionsInitiated|Initierade skalnings åtgärder|Count|Totalt|Riktningen för skalnings åtgärden.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Tillgänglighet|Procent|Medelvärde|Procent slutförda tillgänglighets test har slutförts|availabilityResult/namn, availabilityResult/plats|
|availabilityResults/antal|Tillgänglighetstester|Count|Count|Antal tillgänglighets test|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|availabilityResults/varaktighet|Tillgänglighets testets varaktighet|Millisekunder|Medelvärde|Tillgänglighets testets varaktighet|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|browserTimings/networkDuration|Nätverks anslutnings tid för sid inläsning|Millisekunder|Medelvärde|Tid mellan användar förfrågan och nätverks anslutning. Inkluderar DNS-sökning och transport anslutning.|Inga|
|browserTimings/processingDuration|Klient bearbetnings tid|Millisekunder|Medelvärde|Tiden mellan att ta emot sista byten i ett dokument tills DOM har lästs in. Asynkrona begär Anden kan fortfarande bearbetas.|Inga|
|browserTimings/receiveDuration|Tar emot svars tid|Millisekunder|Medelvärde|Tiden mellan den första och sista byten, eller till från koppling.|Inga|
|browserTimings/sendDuration|Tid för att skicka begäran|Millisekunder|Medelvärde|Tiden mellan nätverks anslutning och mottagande av den första byten.|Inga|
|browserTimings/totalDuration|Sid inläsnings tid för webbläsare|Millisekunder|Medelvärde|Tid från användar förfrågan tills DOM, formatmallar, skript och bilder har lästs in.|Inga|
|beroenden/antal|Beroende anrop|Count|Count|Antal anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/varaktighet|Beroende varaktighet|Millisekunder|Medelvärde|Varaktigheten för anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/misslyckades|Beroende anrops problem|Count|Count|Antal misslyckade beroende anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|pageViews/antal|Sid visningar|Count|Count|Antal sid visningar.|drift/syntetisk, Cloud/roleName|
|pageViews/varaktighet|Inläsnings tid för sid visning|Millisekunder|Medelvärde|Inläsnings tid för sid visning|drift/syntetisk, Cloud/roleName|
|performanceCounters/requestExecutionTime|Körnings tid för HTTP-begäran|Millisekunder|Medelvärde|Körnings tid för den senaste begäran.|Cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-begäranden i program kön|Count|Medelvärde|Längden på program begär ande kön.|Cloud/roleInstance|
|performanceCounters/requestsPerSecond|Hastighet för HTTP-begäran|CountPerSecond|Medelvärde|Takten för alla förfrågningar till programmet per sekund från ASP.NET.|Cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Undantags frekvens|CountPerSecond|Medelvärde|Antal hanterade och ohanterade undantag som rapporter ATS till Windows, inklusive .NET-undantag och ohanterade undantag som konverterats till .NET-undantag.|Cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process-IO-hastighet|BytesPerSecond|Medelvärde|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|Cloud/roleInstance|
|performanceCounters/processCpuPercentage|Processor-CPU|Procent|Medelvärde|Procent andelen av förfluten tid som alla process trådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger enbart prestanda för W3wp-processen.|Cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor tid|Procent|Medelvärde|Den procent andel av tiden som processorn ägnat åt icke-inaktiva trådar.|Cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Tillgängligt minne|Byte|Medelvärde|Fysiskt minne som är omedelbart tillgängligt för allokering till en process eller för system användning.|Cloud/roleInstance|
|performanceCounters/processPrivateBytes|Privata byte för process|Byte|Medelvärde|Minne som tilldelats exklusivt för de övervakade program processerna.|Cloud/roleInstance|
|begär Anden/varaktighet|Server svars tid|Millisekunder|Medelvärde|Tiden mellan att ta emot en HTTP-förfrågan och avsluta sändningen av svaret.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/antal|Server begär Anden|Count|Count|Antal slutförda HTTP-förfrågningar.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/misslyckade|Misslyckade förfrågningar|Count|Count|Antal HTTP-begäranden som marker ATS som misslyckade. I de flesta fall är dessa förfrågningar med svars kod >= 400 och inte lika med 401.|begäran/performanceBucket, begäran/resultCode, begäran/framgång, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|begär Anden/pris|Server begär ande frekvens|CountPerSecond|Medelvärde|Antal server begär Anden per sekund|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|undantag/antal|Undantag|Count|Count|Sammanlagt antal undantag som inte har fångats.|Cloud/roleName, Cloud/roleInstance, Client/Type|
|undantag/webbläsare|Webbläsarundantag|Count|Count|Antal ej fångade undantag som har utlösts i webbläsaren.|klient-isServer, Cloud/roleName|
|undantag/Server|Server undantag|Count|Count|Antal ej fångade undantag som har utlösts i serverprogrammet.|klient-isServer, Cloud/roleName, Cloud/roleInstance|
|spårning/antal|Spårningar|Count|Count|Spårnings dokument antal|Trace/severityLevel, operation/syntetisk, Cloud/roleName, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedDeviceCount|Totalt antal anslutna enheter|Count|Medelvärde|Antal enheter som är anslutna till IoT Central|Inga|
|C2D. Property. Read. lyckades|Lyckad enhets egenskaps läsning från IoT Central|Count|Totalt|Antalet lyckade egenskaps läsningar som initierats från IoT Central|Inga|
|C2D. Property. Read. Failure|Det gick inte att läsa enhets egenskapen från IoT Central|Count|Totalt|Antal misslyckade egenskaps läsningar som initierats från IoT Central|Inga|
|D2C. Property. Read. lyckades|Lyckad enhets egenskap läser från enheter|Count|Totalt|Antalet lyckade egenskaps läsningar som initierats från enheter|Inga|
|D2C. Property. Read. Failure|Det gick inte att läsa enhets egenskapen från enheterna|Count|Totalt|Antalet misslyckade egenskaps läsningar som initierats från enheter|Inga|
|C2D. Property. Update. lyckades|Lyckade uppdateringar av enhets egenskapen från IoT Central|Count|Totalt|Antalet lyckade egenskaps uppdateringar som initierats från IoT Central|Inga|
|C2D. Property. Update. Failure|Det gick inte att uppdatera enhets egenskapen från IoT Central|Count|Totalt|Antalet misslyckade egenskaps uppdateringar som initierats från IoT Central|Inga|
|D2C. Property. Update. lyckades|Lyckade uppdateringar av enhets egenskapen från enheter|Count|Totalt|Antalet lyckade egenskaps uppdateringar som initierats från enheter|Inga|
|D2C. Property. Update. Failure|Misslyckade uppdateringar av enhets egenskapen från enheter|Count|Totalt|Antalet misslyckade egenskaps uppdateringar som initierats från enheter|Inga|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Totalt antal tjänst-API-träffar|Count|Count|Antal totala service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Övergripande service API-latens|Millisekunder|Medelvärde|Övergripande svars tid för service API-begäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Totalt antal service API-resultat|Count|Count|Antal totala service API-resultat|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Övergripande valv beläggning|Procent|Medelvärde|Valv kapacitet som används|ActivityType, ActivityName, TransactionType|
|Tillgänglighet|Övergripande valv tillgänglighet|Procent|Medelvärde|Tillgänglighet för valv begär Anden|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CacheUtilization|Användning av cache|Procent|Medelvärde|Användnings nivå i kluster omfånget|Inga|
|QueryDuration|Frågans varaktighet|Millisekunder|Medelvärde|Frågornas varaktighet i sekunder|QueryStatus|
|IngestionUtilization|Förbruknings användning|Procent|Medelvärde|Förhållandet mellan använda inmatnings platser i klustret|Inga|
|KeepAlive|Behåll Alive|Count|Medelvärde|Sanity check anger att klustret svarar på frågor|Inga|
|IngestionVolumeInMB|Inmatnings volym (i MB)|Count|Totalt|Total mängd inmatade data till klustret (i MB)|Databas|
|IngestionLatencyInSeconds|Inmatnings svars tid (i sekunder)|Sekunder|Medelvärde|Hämtnings tiden från källan (t. ex. meddelandet är i EventHub) till klustret på några sekunder|Inga|
|EventsProcessedForEventHubs|Bearbetade händelser (för Event/IoT-hubbar)|Count|Totalt|Antal händelser som bearbetas av klustret vid inmatning från händelse/IoT Hub|EventStatus|
|IngestionResult|Inmatnings resultat|Count|Count|Antal inmatnings åtgärder|IngestionResultDetails|
|Processor|Processor|Procent|Medelvärde|PROCESSOR användnings nivå|Inga|
|ContinuousExportNumOfRecordsExported|Kontinuerlig export – antal exporterade poster|Count|Totalt|Antal exporterade poster, utlöst för varje lagrings artefakt som skrivits under export åtgärden|ContinuousExportName, databas|
|ExportUtilization|Exportanvändning|Procent|Maximal|Exportera användning|Inga|
|ContinuousExportPendingCount|Antal väntande pågående export|Count|Maximal|Antal väntande kontinuerliga export jobb som är klara för körning|Inga|
|ContinuousExportMaxLatenessMinutes|Kontinuerlig export, maximal försening|Count|Maximal|Försenad (i minuter) som rapporteras av de kontinuerliga export jobben i klustret|Inga|
|ContinuousExportResult|Resultat av kontinuerlig export|Count|Count|Anger om kontinuerlig export lyckades eller misslyckades|ContinuousExportName, resultat, databas|
|StreamingIngestDuration|Hämtnings tid för strömning|Millisekunder|Medelvärde|Hämtnings tid för strömning i millisekunder|Inga|
|StreamingIngestDataRate|Data hastighet för strömnings intag|Count|Medelvärde|Strömnings data hastighet (MB per sekund)|Inga|
|SteamingIngestRequestRate|Begär ande frekvens för strömning|Count|RateRequestsPerSecond|Hastighet för strömnings inmatnings begäran (begär Anden per sekund)|Inga|
|StreamingIngestResults|Resultat av strömnings inmatning|Count|Medelvärde|Resultat av strömnings inmatning|Resultat|
|TotalNumberOfConcurrentQueries|Totalt antal samtidiga frågor|Count|Totalt|Totalt antal samtidiga frågor|Inga|
|TotalNumberOfThrottledQueries|Totalt antal begränsade frågor|Count|Totalt|Totalt antal begränsade frågor|Inga|
|TotalNumberOfThrottledCommands|Totalt antal begränsade kommandon|Count|Totalt|Totalt antal begränsade kommandon|CommandType|
|TotalNumberOfExtents|Totalt antal omfattningar|Count|Totalt|Totalt antal data omfattningar|Inga|
|InstanceCount|Antal instanser|Count|Medelvärde|Totalt antal instanser|Inga|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Count|Totalt|Antal startade arbets flödes körningar.|Inga|
|RunsCompleted|Slutförda körningar|Count|Totalt|Antal slutförda arbets flödes körningar.|Inga|
|RunsSucceeded|Lyckade körningar|Count|Totalt|Antal slutförda arbets flödes körningar.|Inga|
|RunsFailed|Misslyckade körningar|Count|Totalt|Antal misslyckade arbets flödes körningar.|Inga|
|RunsCancelled|Avbrutna körningar|Count|Totalt|Antal avbrutna arbets flödes körningar.|Inga|
|RunLatency|Körnings fördröjning|Sekunder|Medelvärde|Svars tiden för slutförda arbets flödes körningar.|Inga|
|RunSuccessLatency|Kör svars tid|Sekunder|Medelvärde|Svars tiden för slutfört arbets flöde körs.|Inga|
|RunThrottledEvents|Kör begränsade händelser|Count|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga|
|RunStartThrottledEvents|Kör starta begränsade händelser|Count|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inga|
|RunFailurePercentage|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga|
|ActionsStarted|Startade åtgärder |Count|Totalt|Antal startade arbets flödes åtgärder.|Inga|
|ActionsCompleted|Slutförda åtgärder |Count|Totalt|Antal slutförda arbets flödes åtgärder.|Inga|
|ActionsSucceeded|Genomförda åtgärder |Count|Totalt|Antal slutförda arbets flödes åtgärder.|Inga|
|ActionsFailed|Misslyckade åtgärder |Count|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga|
|ActionsSkipped|Åtgärder hoppades över |Count|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga|
|ActionLatency|Åtgärds svars tid |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inga|
|ActionSuccessLatency|Svars tid för åtgärd |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inga|
|ActionThrottledEvents|Åtgärds begränsade händelser|Count|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga|
|TriggersStarted|Startade utlösare |Count|Totalt|Antal startade arbets flödes utlösare.|Inga|
|TriggersCompleted|Slutförda utlösare |Count|Totalt|Antal slutförda arbets flödes utlösare.|Inga|
|TriggersSucceeded|Lyckade utlösare |Count|Totalt|Antal slutförda arbets flödes utlösare.|Inga|
|TriggersFailed|Misslyckade utlösare |Count|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga|
|TriggersSkipped|Ignorerade utlösare|Count|Totalt|Antal överhoppade arbets flödes utlösare.|Inga|
|TriggersFired|Utlöst utlösare |Count|Totalt|Antal utlösta arbets flödes utlösare.|Inga|
|TriggerLatency|Utlös fördröjning |Sekunder|Medelvärde|Latens för slutförda arbets flödes utlösare.|Inga|
|TriggerFireLatency|Utlös brand fördröjning |Sekunder|Medelvärde|Fördröjning för utlösta arbets flödes utlösare.|Inga|
|TriggerSuccessLatency|Utlös svars tid för lyckade |Sekunder|Medelvärde|Svars tid för lyckade arbets flödes utlösare.|Inga|
|TriggerThrottledEvents|Utlös begränsade händelser|Count|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga|
|BillableActionExecutions|Fakturerbara åtgärds körningar|Count|Totalt|Antal körningar av arbets flödes åtgärder som faktureras.|Inga|
|BillableTriggerExecutions|Fakturerbara Utlösar-körningar|Count|Totalt|Antal körningar av arbets flödes utlösare som faktureras.|Inga|
|TotalBillableExecutions|Totalt antal fakturerbara körningar|Count|Totalt|Antal arbets flödes körningar som faktureras.|Inga|
|BillingUsageNativeOperation|Fakturerings användning för intern åtgärds körningar|Count|Totalt|Antal körningar av interna åtgärder som debiteras.|Inga|
|BillingUsageStandardConnector|Fakturerings användning för standard kopplings körningar|Count|Totalt|Antal standard kopplings körningar som faktureras.|Inga|
|BillingUsageStorageConsumption|Fakturerings användning för lagrings förbruknings körningar|Count|Totalt|Antal lagrings förbruknings körningar som faktureras.|Inga|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Count|Totalt|Antal startade arbets flödes körningar.|Inga|
|RunsCompleted|Slutförda körningar|Count|Totalt|Antal slutförda arbets flödes körningar.|Inga|
|RunsSucceeded|Lyckade körningar|Count|Totalt|Antal slutförda arbets flödes körningar.|Inga|
|RunsFailed|Misslyckade körningar|Count|Totalt|Antal misslyckade arbets flödes körningar.|Inga|
|RunsCancelled|Avbrutna körningar|Count|Totalt|Antal avbrutna arbets flödes körningar.|Inga|
|RunLatency|Körnings fördröjning|Sekunder|Medelvärde|Svars tiden för slutförda arbets flödes körningar.|Inga|
|RunSuccessLatency|Kör svars tid|Sekunder|Medelvärde|Svars tiden för slutfört arbets flöde körs.|Inga|
|RunThrottledEvents|Kör begränsade händelser|Count|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga|
|RunStartThrottledEvents|Kör starta begränsade händelser|Count|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inga|
|RunFailurePercentage|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga|
|ActionsStarted|Startade åtgärder |Count|Totalt|Antal startade arbets flödes åtgärder.|Inga|
|ActionsCompleted|Slutförda åtgärder |Count|Totalt|Antal slutförda arbets flödes åtgärder.|Inga|
|ActionsSucceeded|Genomförda åtgärder |Count|Totalt|Antal slutförda arbets flödes åtgärder.|Inga|
|ActionsFailed|Misslyckade åtgärder |Count|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga|
|ActionsSkipped|Åtgärder hoppades över |Count|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga|
|ActionLatency|Åtgärds svars tid |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inga|
|ActionSuccessLatency|Svars tid för åtgärd |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inga|
|ActionThrottledEvents|Åtgärds begränsade händelser|Count|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga|
|TriggersStarted|Startade utlösare |Count|Totalt|Antal startade arbets flödes utlösare.|Inga|
|TriggersCompleted|Slutförda utlösare |Count|Totalt|Antal slutförda arbets flödes utlösare.|Inga|
|TriggersSucceeded|Lyckade utlösare |Count|Totalt|Antal slutförda arbets flödes utlösare.|Inga|
|TriggersFailed|Misslyckade utlösare |Count|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga|
|TriggersSkipped|Ignorerade utlösare|Count|Totalt|Antal överhoppade arbets flödes utlösare.|Inga|
|TriggersFired|Utlöst utlösare |Count|Totalt|Antal utlösta arbets flödes utlösare.|Inga|
|TriggerLatency|Utlös fördröjning |Sekunder|Medelvärde|Latens för slutförda arbets flödes utlösare.|Inga|
|TriggerFireLatency|Utlös brand fördröjning |Sekunder|Medelvärde|Fördröjning för utlösta arbets flödes utlösare.|Inga|
|TriggerSuccessLatency|Utlös svars tid för lyckade |Sekunder|Medelvärde|Svars tid för lyckade arbets flödes utlösare.|Inga|
|TriggerThrottledEvents|Utlös begränsade händelser|Count|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Användning av arbets flödes processor för Integration Service Environment|Procent|Medelvärde|Användning av arbets flödes processor för integrerings tjänst miljö.|Inga|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Minnes användning för arbets flöde för Integration Service Environment|Procent|Medelvärde|Arbets flödets minnes användning för integrerings tjänst miljön.|Inga|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Anslutnings processor användning för Integration Service Environment|Procent|Medelvärde|Anslutnings processor användning för integrerings tjänst miljön.|Inga|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Anslutnings minnes användning för Integration Service Environment|Procent|Medelvärde|Anslutnings minnes användning för integrerings tjänst miljön.|Inga|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Avbrutna körningar|Avbrutna körningar|Count|Totalt|Antal körningar som avbrutits för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Avbryt begärda körningar|Avbryt begärda körningar|Count|Totalt|Antal körningar där Cancel begärdes för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Slutförda körningar|Slutförda körningar|Count|Totalt|Antal körningar som har slutförts för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Misslyckade körningar|Misslyckade körningar|Count|Totalt|Antalet körningar som misslyckades för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Slutför körningar|Slutför körningar|Count|Totalt|Antal körningar som har angetts för avslutande tillstånd för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Svarar inte körningar|Svarar inte körningar|Count|Totalt|Antal körningar som inte svarar för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Inte startade körningar|Inte startade körningar|Count|Totalt|Antal körningar i läget har inte startats för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Förbereder körningar|Förbereder körningar|Count|Totalt|Antal körningar som förbereds för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Etablerings körningar|Etablerings körningar|Count|Totalt|Antal körningar som har etablering för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Köade körningar|Köade körningar|Count|Totalt|Antal körningar som har placerats i kö för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Startade körningar|Startade körningar|Count|Totalt|Antal körningar som har startats för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Startar körningar|Startar körningar|Count|Totalt|Antal körningar som har startats för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Fel|Fel|Count|Totalt|Antal körnings fel i den här arbets ytan|Scenario|
|Varningar|Varningar|Count|Totalt|Antal körnings varningar på den här arbets ytan|Scenario|
|Modell registreringen har slutförts|Modell registreringen har slutförts|Count|Totalt|Antal modell registreringar som har slutförts på den här arbets ytan|Scenario|
|Modell registreringen misslyckades|Modell registreringen misslyckades|Count|Totalt|Antal modell registreringar som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Modelldistribution Startad|Modelldistribution Startad|Count|Totalt|Antal modell distributioner som startats på den här arbets ytan|Scenario|
|Modelldistribution lyckades|Modelldistribution lyckades|Count|Totalt|Antal modell distributioner som har slutförts på den här arbets ytan|Scenario|
|Modelldistribution misslyckades|Modelldistribution misslyckades|Count|Totalt|Antal modell distributioner som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Totalt antal noder|Totalt antal noder|Count|Medelvärde|Totalt antal noder. Den här summan innehåller några aktiva noder, inaktiva noder, oanvändbara noder, Premepted noder, lämnar noder|Scenario, kluster namn|
|Aktiva noder|Aktiva noder|Count|Medelvärde|Antal Active-noder. Detta är de noder som aktivt kör ett jobb.|Scenario, kluster namn|
|Inaktiva noder|Inaktiva noder|Count|Medelvärde|Antal inaktiva noder. Inaktiva noder är noder som inte kör några jobb, men som kan ta emot nya jobb om de är tillgängliga.|Scenario, kluster namn|
|Oanvändbara noder|Oanvändbara noder|Count|Medelvärde|Antal oanvändbara noder. Oanvändbara noder fungerar inte på grund av ett problem som inte kan matchas. De här noderna återanvänds av Azure.|Scenario, kluster namn|
|Misslyckade noder|Misslyckade noder|Count|Medelvärde|Antal noder som har åsidosatts. De här noderna är de noder med låg prioritet som tas bort från den tillgängliga Node-poolen.|Scenario, kluster namn|
|Lämnar noder|Lämnar noder|Count|Medelvärde|Antal noder som lämnar. Att lämna noder är de noder som precis har avslutat bearbetningen av ett jobb och kommer att gå in i inaktivt läge.|Scenario, kluster namn|
|Totalt antal kärnor|Totalt antal kärnor|Count|Medelvärde|Antal total kärnor|Scenario, kluster namn|
|Aktiva kärnor|Aktiva kärnor|Count|Medelvärde|Antal aktiva kärnor|Scenario, kluster namn|
|Inaktiva kärnor|Inaktiva kärnor|Count|Medelvärde|Antal inaktiva kärnor|Scenario, kluster namn|
|Oanvändbara kärnor|Oanvändbara kärnor|Count|Medelvärde|Antal oanvändbara kärnor|Scenario, kluster namn|
|Blockerade kärnor|Blockerade kärnor|Count|Medelvärde|Antal blockerade kärnor|Scenario, kluster namn|
|Lämnar kärnor|Lämnar kärnor|Count|Medelvärde|Antal lämnar kärnor|Scenario, kluster namn|
|Kvot användning i procent|Kvot användning i procent|Count|Medelvärde|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Count|Medelvärde|PROCESSOR (för hands version)|Scenario, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Count|Medelvärde|GPU (för hands version)|Scenario, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Count|Count|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Tillgänglighet för API: erna|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Media Services/strömnings slut punkter

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte.|OutputFormat|
|SuccessE2ELatency|Slutför svars tid för slut punkt till slut punkt|Millisekunder|Medelvärde|Genomsnittlig svars tid för lyckade begär anden i millisekunder.|OutputFormat|
|Begäranden|Begäranden|Count|Totalt|Begär anden till en slut punkt för direkt uppspelning.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AssetQuota|Till gångs kvot|Count|Medelvärde|Hur många till gångar som tillåts för det aktuella Media Service-kontot|Inga|
|AssetCount|Antal till gångar|Count|Medelvärde|Hur många till gångar som redan har skapats i det aktuella medie tjänst kontot|Inga|
|AssetQuotaUsedPercentage|Använd procent andel till till gångs kvot|Procent|Medelvärde|Procent andel till gång som används i det aktuella medie tjänst kontot|Inga|
|ContentKeyPolicyQuota|Kvot för innehålls nyckel princip|Count|Medelvärde|Hur många innehålls nyckel principer som tillåts för det aktuella Media Service-kontot|Inga|
|ContentKeyPolicyCount|Antal nyckel principer för innehåll|Count|Medelvärde|Hur många innehålls nyckel principer som redan har skapats i det aktuella medie tjänst kontot|Inga|
|ContentKeyPolicyQuotaUsedPercentage|Procent andel av nyckel princip för innehåll|Procent|Medelvärde|Procent andel av nyckel princip för innehåll i det aktuella medie tjänst kontot|Inga|
|StreamingPolicyQuota|Kvot för strömmande princip|Count|Medelvärde|Hur många strömmande principer som tillåts för det aktuella Media Service-kontot|Inga|
|StreamingPolicyCount|Antal strömmande principer|Count|Medelvärde|Hur många strömmande principer som redan har skapats i det aktuella medie tjänst kontot|Inga|
|StreamingPolicyQuotaUsedPercentage|Använd procent andel av princip för strömning|Procent|Medelvärde|Procentuell strömnings princip som används i det aktuella medie tjänst kontot|Inga|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AssetsConverted|Konverterade till gångar|Count|Totalt|Totalt antal konverterade till gångar|AppId, ResourceId, SDKVersion|
|ActiveRenderingSessions|Aktiva åter givnings sessioner|Count|Totalt|Totalt antal aktiva åter givnings sessioner|AppId, ResourceId, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageReadLatency|Genomsnittlig Läs fördröjning|Millisekunder|Medelvärde|Genomsnittlig Läs fördröjning i millisekunder per åtgärd|Inga|
|AverageWriteLatency|Genomsnittlig Skriv fördröjning|Millisekunder|Medelvärde|Genomsnittlig Skriv fördröjning i millisekunder per åtgärd|Inga|
|VolumeLogicalSize|Storlek på förbrukad volym|Byte|Medelvärde|Den logiska storleken på volymen (använda byte)|Inga|
|VolumeSnapshotSize|Storlek på volym ögonblicks bild|Byte|Medelvärde|Storlek på alla ögonblicks bilder i volymen|Inga|
|ReadIops|Läs IOPS|CountPerSecond|Medelvärde|Läs-/ut-åtgärder per sekund|Inga|
|WriteIops|Skriv IOPS|CountPerSecond|Medelvärde|Skriv in/ut-åtgärder per sekund|Inga|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Pool allokerad till volym storlek|Byte|Medelvärde|Allokerad använt storlek på poolen|Inga|
|VolumePoolTotalLogicalSize|Förbrukad pool storlek|Byte|Medelvärde|Summan av den logiska storleken för alla volymer som tillhör poolen|Inga|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesSentRate|Skickade byte|Byte|Totalt|Antal byte som nätverks gränssnittet har skickats|Inga|
|BytesReceivedRate|Mottagna byte|Byte|Totalt|Antal byte som nätverks gränssnittet har tagits emot|Inga|
|PacketsSentRate|Skickade paket|Count|Totalt|Antal paket som nätverks gränssnittet har skickats|Inga|
|PacketsReceivedRate|Mottagna paket|Count|Totalt|Antal paket som nätverks gränssnittet har fått|Inga|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|Tillgänglighet för datasökvägar|Count|Medelvärde|Genomsnittlig tillgänglighet för Load Balancer data Sök väg per tids längd|FrontendIPAddress,FrontendPort|
|DipAvailability|Status för hälsoavsökningen|Count|Medelvärde|Genomsnittlig status för Load Balancer hälso avsökning per tids period|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Antal byte|Count|Totalt|Totalt antal byte som skickats inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Antal paket|Count|Totalt|Totalt antal överförda paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Antal SYN|Count|Totalt|Totalt antal skickade SYN paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Antal SNAT-anslutningar|Count|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tids perioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allokerade SNAT-portar (förhands granskning)|Count|Totalt|Totalt antal SNAT-portar som allokerats inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Använda SNAT-portar (förhands granskning)|Count|Totalt|Totalt antal SNAT-portar som använts inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Fråga volym|Count|Totalt|Antal frågor som hanteras för en DNS-zon|Inga|
|RecordSetCount|Antal post uppsättningar|Count|Maximal|Antal post uppsättningar i en DNS-zon|Inga|
|RecordSetCapacityUtilization|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en DNS-zon|Inga|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS för inkommande paket|CountPerSecond|Maximal|DDoS för inkommande paket|Inga|
|PacketsDroppedDDoS|Inkommande paket som släppts DDoS|CountPerSecond|Maximal|Inkommande paket som släppts DDoS|Inga|
|PacketsForwardedDDoS|Vidarebefordrade inkommande paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande paket DDoS|Inga|
|TCPPacketsInDDoS|DDoS inkommande TCP-paket|CountPerSecond|Maximal|DDoS inkommande TCP-paket|Inga|
|TCPPacketsDroppedDDoS|Inkommande TCP-paket ignorerade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket ignorerade DDoS|Inga|
|TCPPacketsForwardedDDoS|Inkommande TCP-paket, vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket, vidarebefordrade DDoS|Inga|
|UDPPacketsInDDoS|DDoS för inkommande UDP-paket|CountPerSecond|Maximal|DDoS för inkommande UDP-paket|Inga|
|UDPPacketsDroppedDDoS|Ignorerade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Ignorerade inkommande UDP-paket DDoS|Inga|
|UDPPacketsForwardedDDoS|Vidarebefordrade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande UDP-paket DDoS|Inga|
|BytesInDDoS|DDoS för inkommande byte|BytesPerSecond|Maximal|DDoS för inkommande byte|Inga|
|BytesDroppedDDoS|Ignorerade inkommande byte DDoS|BytesPerSecond|Maximal|Ignorerade inkommande byte DDoS|Inga|
|BytesForwardedDDoS|Inkommande byte, vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande byte, vidarebefordrade DDoS|Inga|
|TCPBytesInDDoS|DDoS för inkommande TCP-byte|BytesPerSecond|Maximal|DDoS för inkommande TCP-byte|Inga|
|TCPBytesDroppedDDoS|Inkommande TCP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte utelämnade DDoS|Inga|
|TCPBytesForwardedDDoS|Inkommande TCP byte-vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande TCP byte-vidarebefordrade DDoS|Inga|
|UDPBytesInDDoS|DDoS för inkommande UDP-byte|BytesPerSecond|Maximal|DDoS för inkommande UDP-byte|Inga|
|UDPBytesDroppedDDoS|Inkommande UDP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte utelämnade DDoS|Inga|
|UDPBytesForwardedDDoS|Inkommande UDP byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande UDP byte vidarebefordrade DDoS|Inga|
|IfUnderDDoSAttack|Under DDoS-attack eller inte|Count|Maximal|Under DDoS-attack eller inte|Inga|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-minskning|Inga|
|DDoSTriggerUDPPackets|Ingående UDP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Ingående UDP-paket för att utlösa DDoS-minskning|Inga|
|DDoSTriggerSYNPackets|Inkommande SYN paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande SYN paket för att utlösa DDoS-minskning|Inga|
|VipAvailability|Tillgänglighet för datasökvägar|Count|Medelvärde|Genomsnittlig tillgänglighet för IP-adress per tids längd|Port|
|ByteCount|Antal byte|Count|Totalt|Totalt antal byte som skickats inom tids perioden|Port, riktning|
|PacketCount|Antal paket|Count|Totalt|Totalt antal överförda paket inom tids perioden|Port, riktning|
|SynCount|Antal SYN|Count|Totalt|Totalt antal skickade SYN paket inom tids perioden|Port, riktning|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Fördröjning för ping till en virtuell dator|Millisekunder|Medelvärde|Tur och retur-tid för pingar som skickas till en virtuell måldator|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Misslyckade pingar till en virtuell dator|Procent|Medelvärde|Procent av antalet misslyckade pingar till totalt antal skickade pingar för en virtuell måldator|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ApplicationRuleHit|Antal träffar för program regler|Count|Totalt|Antal gånger som program regler träffades|Status, orsak, protokoll|
|NetworkRuleHit|Antal träffar för nätverks regler|Count|Totalt|Antal gånger som nätverks regler träffades|Status, orsak, protokoll|
|FirewallHealth|Hälso tillstånd för brand vägg|Procent|Medelvärde|Hälso tillstånd för brand vägg|Status, orsak|
|DataProcessed|Bearbetade data|Byte|Totalt|Total mängd data som bearbetas av brand väggen|Inga|
|SNATPortUtilization|SNAT-port användning|Procent|Medelvärde|SNAT-port användning|Inga|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Medelvärde|Antal byte per sekund som Application Gateway har betjänat|Inga|
|UnhealthyHostCount|Antal felaktiga värdar|Count|Medelvärde|Antal värdar för skadade Server delar|BackendSettingsPool|
|HealthyHostCount|Antal felfria värdar|Count|Medelvärde|Antal felfria Server dels värdar|BackendSettingsPool|
|TotalRequests|Totalt antal förfrågningar|Count|Totalt|Antal lyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Begär Anden per minut per felfri värd|Count|Medelvärde|Genomsnittligt antal begär Anden per minut per felfri backend-värd i en pool|BackendSettingsPool|
|FailedRequests|Misslyckade begäranden|Count|Totalt|Antal misslyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|ResponseStatus|Svars status|Count|Totalt|Http-svarets status returnerades av Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuella anslutningar|Count|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inga|
|NewConnectionsPerSecond|Nya anslutningar per sekund|CountPerSecond|Medelvärde|Nya anslutningar per sekund som upprättats med Application Gateway|Inga|
|CpuUtilization|CPU-användning|Procent|Medelvärde|Aktuell processor användning för Application Gateway|Inga|
|CapacityUnits|Aktuella kapacitets enheter|Count|Medelvärde|Förbrukade kapacitets enheter|Inga|
|FixedBillableCapacityUnits|Fasta fakturerbara kapacitetsenheter|Count|Medelvärde|Lägsta kapacitets enhet som ska debiteras|Inga|
|EstimatedBilledCapacityUnits|Uppskattade enheter för fakturerings kapacitet|Count|Medelvärde|Uppskattade kapacitets enheter som ska debiteras|Inga|
|ComputeUnits|Aktuella beräknings enheter|Count|Medelvärde|Förbrukade beräknings enheter|Inga|
|BackendResponseStatus|Svars status för Server del|Count|Totalt|Antalet HTTP-svars koder som genereras av Server dels medlemmar. Detta omfattar inte några svars koder som genereras av Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Klientens TLS-protokoll|Count|Totalt|Antalet TLS-och icke-TLS-begäranden som initieras av klienten som upprättade anslutningen till Application Gateway. Om du vill visa TLS-protokollets distribution filtrerar du efter dimension TLS-protokollet.|Lyssnare, TlsProtocol|
|Bytes sent|Skickade byte|Byte|Totalt|Det totala antalet byte som har skickats av Application Gateway till klienterna|Lyssnare|
|BytesReceived|Mottagna byte|Byte|Totalt|Det totala antalet byte som tagits emot av Application Gateway från klienterna|Lyssnare|
|ClientRtt|Klient-/klient|Millisekunder|Medelvärde|Genomsnittlig fördröjning mellan klienter och Application Gateway. Det här måttet anger hur lång tid det tar att upprätta anslutningar och retur bekräftelser|Lyssnare|
|ApplicationGatewayTotalTime|Application Gateway total tid|Millisekunder|Medelvärde|Genomsnittlig tid det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som genomsnitt av intervallet från den tid då Application Gateway tar emot den första byten av en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att detta vanligt vis omfattar Application Gateway bearbetnings tid, tid då paket för begäran och svar överförs över nätverket och hur lång tid det tog att svara på backend-servern.|Lyssnare|
|BackendConnectTime|Server dels anslutnings tid|Millisekunder|Medelvärde|Åtgången tid för att upprätta en anslutning till en backend-server|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|Svars tid för första byte för Server del|Millisekunder|Medelvärde|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den första byten i svars huvudet, ungefär bearbetnings tiden för backend-servern|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|Svars tid för senaste byte för Server delen|Millisekunder|Medelvärde|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den sista byten i svars texten|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|MatchedCount|Brand vägg för webbaserade program v1 totalt regel distribution|Count|Totalt|Brand vägg för webbaserade program v1 total regel distribution för inkommande trafik|RuleGroup, RuleId|
|BlockedCount|Distribution av brand vägg för webbaserade program v1 blockerade begär Anden|Count|Totalt|Distribution av brand vägg för webbaserade program v1 blockerade begär Anden|RuleGroup, RuleId|
|BlockedReqCount|Antal blockerade begär Anden för brand vägg för webbaserade program|Count|Totalt|Antal blockerade begär Anden för brand vägg för webbaserade program|Inga|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageBandwidth|Gatewayens S2S-bandbredd|BytesPerSecond|Medelvärde|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inga|
|P2SBandwidth|Gateway P2S-bandbredd|BytesPerSecond|Medelvärde|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inga|
|P2SConnectionCount|Antal P2S-anslutningar|Count|Maximal|Antal anslutningar från punkt-till-plats för en gateway|Protokoll|
|TunnelAverageBandwidth|Tunnelbandbredd|BytesPerSecond|Medelvärde|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Utgående byte för tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Inkommande byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Utgående tunnelpaket|Count|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Inkommande tunnel paket|Count|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Utgående ignorerade TS-paket med matchningsfel för tunnel|Count|Totalt|Antal utgående ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Inkommande ignorerade TS-paket med matchningsfel för tunnel|Count|Totalt|Antal inkommande ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Count|Medelvärde|RX ljus nivå i dBm|Länk, Lane|
|TxLightLevel|TxLightLevel|Count|Medelvärde|TX-ljusnivå i dBm|Länk, Lane|
|AdminState|AdminState|Count|Medelvärde|Administratörs tillstånd för porten|Länk|
|LineProtocol|LineProtocol|Count|Medelvärde|Status för linje protokoll för porten|Länk|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|Länk|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|Länk|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|PeeredCircuitSKey|
|BgpAvailability|BGP-tillgänglighet|Procent|Medelvärde|BGP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|ArpAvailability|ARP-tillgänglighet|Procent|Medelvärde|ARP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Medelvärde|Ingress BITS av data som ignoreras per sekund|Inga|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Medelvärde|Utgående bitar med data som ignoreras per sekund|Inga|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|Inga|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|Inga|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|Inga|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|Inga|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Returnerade frågor efter slut punkt|Count|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den aktuella tids ramen|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Slut punkts status efter slut punkt|Count|Maximal|1 om avsöknings status för en slut punkt är "aktive rad", annars 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Avsökningar misslyckades|Procent|Medelvärde|% av anslutnings övervaknings avsökningarna misslyckades|Inga|
|AverageRoundtripMs|Genomsnittlig tid för fördröjning (MS)|Millisekunder|Medelvärde|Genomsnittlig tid för nätverks fördröjning (MS) för anslutnings övervaknings avsökningar som skickas mellan källa och mål|Inga|
|ChecksFailedPercent|Misslyckade kontroller i procent (för hands version)|Procent|Medelvärde|% av kontrollerna för anslutnings övervakning misslyckades|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Tur och retur tid (MS) (för hands version)|Millisekunder|Medelvärde|Svars tid i millisekunder för kontrollerna för anslutnings övervakning|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RequestCount|Antal begäranden|Count|Totalt|Antalet klient förfrågningar som hanteras av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Begär ande storlek|Byte|Totalt|Antalet byte som har skickats som begär Anden från klienter till HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Svars storlek|Byte|Totalt|Antalet byte som skickats som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Fakturerbart svars storlek|Byte|Totalt|Antalet fakturerbara byte (minsta 2KB per begäran) som skickats som svar från HTTP/S-proxy till klienter.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Antal Server dels begär Anden|Count|Totalt|Antalet förfrågningar som skickats från HTTP/S-proxyn till Server delar|HttpStatus, HttpStatusGroup, Server del|
|BackendRequestLatency|Svars tid för Server del|Millisekunder|Medelvärde|Tiden som beräknas från när begäran skickades av HTTP/S-proxyn till Server delen tills HTTP/S-proxyn fick den senaste svars byten från Server delen|Serverdel|
|TotalLatency|Total svars tid|Millisekunder|Medelvärde|Den tid som beräknas från när klientbegäran togs emot av HTTP/S-proxy tills klienten bekräftade den senaste svars byten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Server delens hälso procent|Procent|Medelvärde|Procent andelen lyckade hälso avsökningar från HTTP/S-proxyn till Server delar|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Antal begär Anden om webb programs brand vägg|Count|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Fråga volym|Count|Totalt|Antal frågor som hanteras för en Privat DNS zon|Inga|
|RecordSetCount|Antal post uppsättningar|Count|Maximal|Antal post uppsättningar i en Privat DNS zon|Inga|
|RecordSetCapacityUtilization|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en Privat DNS zon|Inga|
|VirtualNetworkLinkCount|Antal Virtual Network länkar|Count|Maximal|Antal virtuella nätverk som är länkade till en Privat DNS zon|Inga|
|VirtualNetworkLinkCapacityUtilization|Virtual Network länka kapacitets användning|Procent|Maximal|Procent andel Virtual Network länk kapacitet som används av en Privat DNS zon|Inga|
|VirtualNetworkWithRegistrationLinkCount|Antal Virtual Network registrerings länkar|Count|Maximal|Antal virtuella nätverk som är länkade till en Privat DNS zon med automatisk registrering aktive rad|Inga|
|VirtualNetworkWithRegistrationCapacityUtilization|Virtual Network kapacitets användning för registrerings länk|Procent|Maximal|Procent av Virtual Network-länk med automatisk registrerings kapacitet som används av en Privat DNS zon|Inga|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/Namespaces/NotificationHubs

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|registrering. alla|Registrerings åtgärder|Count|Totalt|Antalet lyckade registrerings åtgärder (skapar uppdateringar av frågor och borttagningar). |Inga|
|registrering. skapa|Skapa åtgärder för registrering|Count|Totalt|Antalet lyckade registreringar som skapas.|Inga|
|registrering. Update|Registrera uppdaterings åtgärder|Count|Totalt|Antalet lyckade registrerings uppdateringar.|Inga|
|registrering. Hämta|Läs åtgärder för registrering|Count|Totalt|Antalet lyckade registrerings frågor.|Inga|
|registrering. Delete|Åtgärder för att ta bort registrering|Count|Totalt|Antalet lyckade registrerings borttagningar.|Inga|
|inkommande|Inkommande meddelanden|Count|Totalt|Antalet lyckade sändnings-API-anrop. |Inga|
|inkommande. schemalagd|Schemalagda push-meddelanden har skickats|Count|Totalt|Schemalagda push-meddelanden har avbrutits|Inga|
|inkommande. schemalagd. Avbryt|Schemalagda push-meddelanden har avbrutits|Count|Totalt|Schemalagda push-meddelanden har avbrutits|Inga|
|schemalagt. väntar|Väntande schemalagda meddelanden|Count|Totalt|Väntande schemalagda meddelanden|Inga|
|installation. alla|Installations hanterings åtgärder|Count|Totalt|Installations hanterings åtgärder|Inga|
|installation. get|Hämta installations åtgärder|Count|Totalt|Hämta installations åtgärder|Inga|
|installation. upsert|Skapa eller uppdatera installations åtgärder|Count|Totalt|Skapa eller uppdatera installations åtgärder|Inga|
|installation. patch|Installation av korrigerings åtgärder|Count|Totalt|Installation av korrigerings åtgärder|Inga|
|installation. Delete|Ta bort installations åtgärder|Count|Totalt|Ta bort installations åtgärder|Inga|
|utgående. allpns. lyckades|Lyckade aviseringar|Count|Totalt|Antalet lyckade aviseringar.|Inga|
|utgående. allpns. invalidpayload|Nytto Last fel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS returnerade ett felaktigt nytto Last fel.|Inga|
|utgående. allpns. pnserror|Fel i externt meddelande system|Count|Totalt|Antal push-meddelanden som misslyckades på grund av ett problem med att kommunicera med PNS (exkluderar autentiseringsproblem).|Inga|
|utgående. allpns. channelerror|Kanal fel|Count|Totalt|Antal push-meddelanden som inte har misslyckats på grund av att kanalen var ogiltig och som inte är associerad med rätt app-begränsning eller upphörde att gälla.|Inga|
|utgående. allpns. badorexpiredchannel|Dåliga eller utgångna kanal fel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom kanal/token/registrationId i registreringen har upphört att gälla eller är ogiltiga.|Inga|
|utgående. WNS. lyckades|WNS slutförda meddelanden|Count|Totalt|Antalet lyckade aviseringar.|Inga|
|utgående. WNS. invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras. (Windows Live känner inte igen autentiseringsuppgifterna).|Inga|
|utgående. WNS. badchannel|WNS felaktigt kanal fel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inga|
|utgående. WNS. expiredchannel|WNS utgången kanal fel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 borta).|Inga|
|utgående. WNS. begränsad|WNS-begränsade meddelanden|Count|Totalt|Antal push-meddelanden som misslyckades eftersom WNS begränsar den här appen (WNS-status: 406 är inte acceptabel).|Inga|
|utgående. WNS. tokenproviderunreachable|WNS-auktoriseringsfel (kan inte kontaktas)|Count|Totalt|Windows Live är inte tillgängligt.|Inga|
|utgående. WNS. invalidtoken|WNS-auktoriseringsfel (ogiltig token)|Count|Totalt|Den token som angetts för WNS är inte giltig (WNS-status: 401 otillåten).|Inga|
|utgående. WNS. wrongtoken|WNS-auktoriseringsfel (fel token)|Count|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS-status: 403 förbjuden). Detta kan inträffa om ChannelURI i registreringen är kopplad till en annan app. Kontrol lera att klient programmet är associerat med samma app vars autentiseringsuppgifter finns i Notification Hub.|Inga|
|utgående. WNS. invalidnotificationformat|WNS ogiltigt meddelande format|Count|Totalt|Meddelandets format är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nytto laster.|Inga|
|utgående. WNS. invalidnotificationsize|WNS ogiltig meddelande storleks fel|Count|Totalt|Meddelande nytto lasten är för stor (WNS-status: 413).|Inga|
|utgående. WNS. channelthrottled|WNS-kanalen är begränsad|Count|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-NotificationStatus: channelThrottled).|Inga|
|utgående. WNS. channeldisconnected|WNS-kanalen är frånkopplad|Count|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inga|
|utgående. WNS. släppt|WNS borttagna meddelanden|Count|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: släppt men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga|
|utgående. WNS. pnserror|WNS-fel|Count|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med WNS.|Inga|
|utgående. WNS. authenticationerror|WNS-autentiseringsfel|Count|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med ogiltiga Windows Live-autentiseringsuppgifter eller felaktigt token.|Inga|
|utgående. APN. lyckades|APN-lyckade meddelanden|Count|Totalt|Antalet lyckade aviseringar.|Inga|
|utgående. APN. invalidcredentials|APN-auktoriseringsfel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga|
|utgående. APN. badchannel|APN dåligt kanal fel|Count|Totalt|Antal push-meddelanden som misslyckades på grund av att token är ogiltig (APN-status kod: 8).|Inga|
|utgående. APN. expiredchannel|APN utgånget i kanal fel|Count|Totalt|Antalet token som ogiltig förklarades av APNS-feedback-kanalen.|Inga|
|utgående. APN. invalidnotificationsize|APN ogiltigt meddelande storleks fel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (APN-status kod: 7).|Inga|
|utgående. APN. pnserror|APN-fel|Count|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med APN.|Inga|
|utgående. GCM. lyckades|GCM slutförda meddelanden|Count|Totalt|Antalet lyckade aviseringar.|Inga|
|utgående. GCM. invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga|
|utgående. GCM. badchannel|GCM felaktigt kanal fel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM-resultat: ogiltig registrering).|Inga|
|utgående. GCM. expiredchannel|GCM utgången kanal fel|Count|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga|
|utgående. GCM. begränsad|GCM-begränsade meddelanden|Count|Totalt|Antal push-meddelanden som misslyckades eftersom GCM begränsade till den här appen (GCM-status kod: 501-599 eller resultat: ej tillgängligt).|Inga|
|utgående. GCM. invalidnotificationformat|GCM ogiltigt meddelande format|Count|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten inte formaterades korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga|
|utgående. GCM. invalidnotificationsize|GCM ogiltig meddelande storleks fel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (GCM-resultat: MessageTooBig).|Inga|
|utgående. GCM. wrongchannel|GCM fel kanal fel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte är kopplad till den aktuella appen (GCM result: InvalidPackageName).|Inga|
|utgående. GCM. pnserror|GCM-fel|Count|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med GCM.|Inga|
|utgående. GCM. authenticationerror|GCM-autentiseringsfel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna som autentiseringsuppgifterna är blockerade eller om SenderId inte har kon figurer ATS korrekt i appen (GCM result: MismatchedSenderId).|Inga|
|utgående. MPNS. lyckades|MPNS slutförda meddelanden|Count|Totalt|Antalet lyckade aviseringar.|Inga|
|utgående. MPNS. invalidcredentials|MPNS ogiltiga autentiseringsuppgifter|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga|
|utgående. MPNS. badchannel|MPNS felaktigt kanal fel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inga|
|utgående. MPNS. begränsad|MPNS-begränsade meddelanden|Count|Totalt|Antal push-meddelanden som misslyckades eftersom MPNS begränsar den här appen (WNS MPNS: 406 är inte acceptabel).|Inga|
|utgående. MPNS. invalidnotificationformat|MPNS ogiltigt meddelande format|Count|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten för meddelandet var för stor.|Inga|
|utgående. MPNS. channeldisconnected|MPNS-kanalen är frånkopplad|Count|Totalt|Antal push-meddelanden som misslyckades på grund av att ChannelURI i registreringen var frånkopplad (MPNS status: 412 hittades inte).|Inga|
|utgående. MPNS. släppt|MPNS borttagna meddelanden|Count|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svars huvud: X-NotificationStatus: QueueFull eller undertrycks).|Inga|
|utgående. MPNS. pnserror|MPNS-fel|Count|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med MPNS.|Inga|
|utgående. MPNS. authenticationerror|MPNS-autentiseringsfel|Count|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga|
|notificationhub. push-meddelanden|Alla utgående meddelanden|Count|Totalt|Alla utgående aviseringar för Notification Hub|Inga|
|inkommande. alla. förfrågningar|Alla inkommande begär Anden|Count|Totalt|Totalt antal inkommande begär Anden för en Notification Hub|Inga|
|inkommande. alla. failedrequests|Alla inkommande misslyckade förfrågningar|Count|Totalt|Totalt antal inkommande misslyckade begär Anden för en Notification Hub|Inga|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_ kostnads fri noder i procent|Kostnads fri noder i procent|Count|Medelvärde|Average_ kostnads fri noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledigt utrymme i procent|Ledigt utrymme i procent|Count|Medelvärde|Average_ ledigt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Använd noder i procent|% Använda noder i procent|Count|Medelvärde|Average_% Använd noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt utrymme i procent|Använt utrymme i procent|Count|Medelvärde|Average_ använt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/s|Disk-lästa byte/s|Count|Medelvärde|Average_Disk lästa byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Disk läsningar/SEK|Count|Medelvärde|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Disk överföringar/SEK|Count|Medelvärde|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/s|Disk-skrivna byte/s|Count|Medelvärde|Average_Disk skrivna byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Disk skrivningar/SEK|Count|Medelvärde|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Lediga megabyte|Count|Medelvärde|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk-byte/s|Logisk disk byte/SEK|Count|Medelvärde|Average_Logical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt minne|Tillgängligt minne i procent|Count|Medelvärde|Average_% tillgängligt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt växlings utrymme|Tillgängligt växlings utrymme i procent|Count|Medelvärde|Average_% tillgängligt växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt minne|Använt minne i procent|Count|Medelvärde|Average_% använt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt växlings utrymme i procent|Använt växlings utrymme i procent|Count|Medelvärde|Average_ använt växlings utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB minne|Tillgängligt minne i megabyte|Count|Medelvärde|Average_Available MB minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Växling vid Average_Available megabyte|Tillgängliga megabyte växlings utrymme|Count|Medelvärde|Växling vid Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page läsningar/s|Sid läsningar/s|Count|Medelvärde|Average_Page läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page skrivningar/SEK|Sid skrivningar/SEK|Count|Medelvärde|Average_Page skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per sekund|Sidor/s|Count|Medelvärde|Average_Pages per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlings utrymme|Använt megabyte växlings utrymme|Count|Medelvärde|Average_Used megabyte växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i MB|Använt minne i MB|Count|Medelvärde|Average_Used minne i MB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total byte|Totalt antal överförda byte|Count|Medelvärde|Skickade Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna byte|Totalt antal mottagna byte|Count|Medelvärde|Average_Total mottagna byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Totalt antal byte|Count|Medelvärde|Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total paket|Totalt antal överförda paket|Count|Medelvärde|Skickade Average_Total paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna paket|Totalt antal mottagna paket|Count|Medelvärde|Average_Total mottagna paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total RX-fel|Totalt antal mottagna mottagnings fel|Count|Medelvärde|Average_Total RX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-fel|Totalt antal TX-fel|Count|Medelvärde|Average_Total TX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Totalt antal kollisioner|Count|Medelvärde|Average_Total kollisioner|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/läsning|Medel s/disk läsning|Count|Medelvärde|Average_Avg. Disk s/läsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/överföring|Medel s/disk överföring|Count|Medelvärde|Average_Avg. Disk s/överföring|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/skrivning|Medel s/disk skrivning|Count|Medelvärde|Average_Avg. Disk s/skrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk-byte/s|Fysisk disk-byte/SEK|Count|Medelvärde|Average_Physical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegie rad tid|PCT privilegie rad tid|Count|Medelvärde|Average_Pct privilegie rad tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användar tid|PCT-användar tid|Count|Medelvärde|Average_Pct användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i KB|Använt minne i KB|Count|Medelvärde|Average_Used minne i KB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Virtuellt delat minne|Count|Medelvärde|Average_Virtual delat minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tid|DPC-tid i procent|Count|Medelvärde|Average_% DPC-tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledig tid i procent|Inaktivitetstid i procent|Count|Medelvärde|Average_ ledig tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Avbrotts tid i procent för Average_%|% Avbrotts tid|Count|Medelvärde|Avbrotts tid i procent för Average_%|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% i/o-vänte tid|% I/o-vänte tid|Count|Medelvärde|Average_% i/o-vänte tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% bra tid|% Trevligt tid|Count|Medelvärde|Average_% bra tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ privilegie rad tid i procent|Privilegie rad tid i procent|Count|Medelvärde|Average_ privilegie rad tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|% processortid|Count|Medelvärde|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% användar tid|Användar tid i procent|Count|Medelvärde|Average_% användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Ledigt fysiskt minne|Count|Medelvärde|Average_Free fysiskt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Ledigt utrymme i växlingsfiler|Count|Medelvärde|Average_Free utrymme i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Ledigt virtuellt minne|Count|Medelvärde|Average_Free virtuellt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processer|Count|Medelvärde|Average_Processes|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagras i växlingsfiler|Storlek lagrad i växlingsfiler|Count|Medelvärde|Average_Size lagras i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Drift tid|Count|Medelvärde|Average_Uptime|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Användare|Count|Medelvärde|Average_Users|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Aktuell diskkölängd|Count|Medelvärde|Average_Current diskkölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte|Tillgängliga megabyte|Count|Medelvärde|Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% allokerade byte som används|% Allokerade byte som används|Count|Medelvärde|Average_% allokerade byte som används|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes mottagna/SEK|Mottagna byte/SEK|Count|Medelvärde|Average_Bytes mottagna/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Bytes per sekund|Skickade byte/SEK|Count|Medelvärde|Skickade Average_Bytes per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/SEK|Totalt antal byte/s|Count|Medelvärde|Average_Bytes totalt/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Kölängd|Kölängd för processor|Count|Medelvärde|Average_Processor Kölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pulsslag|Pulsslag|Count|Totalt|Pulsslag|Dator, OSType, version, SourceComputerId|
|Uppdatera|Uppdatera|Count|Medelvärde|Uppdatera|Dator, produkt, klassificering, UpdateState, valfri, godkänd|
|Händelse|Händelse|Count|Medelvärde|Händelse|Källa, EventLog, dator, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PrefixLatency|Prefix svars tid|Millisekunder|Medelvärde|Prefixlängd för median|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Tillgänglighets-v4 för session|Procent|Medelvärde|Tillgänglighet för v4-sessionen|ConnectionId|
|SessionAvailabilityV6|V6 för sessions tillgänglighet|Procent|Medelvärde|Tillgängligheten för V6-sessionen|ConnectionId|
|IngressTrafficRate|Inkommande trafik hastighet|BitsPerSecond|Medelvärde|Inkommande trafik hastighet i bitar per sekund|ConnectionId|
|EgressTrafficRate|Utgående trafik hastighet|BitsPerSecond|Medelvärde|Utgående trafik hastighet i bitar per sekund|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryDuration|Frågevaraktighet|Millisekunder|Medelvärde|DAX-frågans varaktighet i det sista intervallet|Inga dimensioner|
|QueryPoolJobQueueLength|Trådar: Kölängd för jobbkö|Count|Medelvärde|Antal jobb i kön för trådpoolen för Query.|Inga dimensioner|
|qpu_high_utilization_metric|Hög användning av QPU|Count|Totalt|QPU hög användning under den senaste minuten, 1 för hög QPU användning, annars 0|Inga dimensioner|
|memory_metric|Minne|Byte|Medelvärde|Memory. Intervallet 0-3 GB för a1, 0-5 GB för a2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Minnesförslöing|Procent|Medelvärde|Genomsnittligt minne nedskräpning.|Inga dimensioner|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Till gångs distribution efter klassificering|Count|Totalt|Anger antalet till gångar med en viss klassificering som tilldelas, d.v.s. de klassificeras med den etiketten.|Klassificering, källa, ResourceId|
|AssetDistributionByStorageType|Till gångs distribution efter lagrings typ|Count|Totalt|Anger antalet till gångar med en viss lagrings typ.|StorageType, ResourceId|
|CatalogActiveUsers|Dagliga aktiva användare|Count|Totalt|Antal aktiva användare varje dag|ResourceId|
|CatalogUsage|Användnings distribution per åtgärd|Count|Totalt|Ange antalet åtgärds användare som gör till katalogen, t. ex. åtkomst, sökning, ord lista.|Åtgärd, ResourceId|
|NumberOfAssetsWithClassifications|Antal till gångar med minst en klassificering|Count|Medelvärde|Anger antalet till gångar med minst en etikett klassificering.|ResourceId|
|ScanCancelled|Skanningen avbröts|Count|Totalt|Anger antalet avsökningar som har avbrutits.|ResourceId|
|ScanCompleted|Sökningen är klar|Count|Totalt|Anger hur många genomsökningar som har slutförts.|ResourceId|
|ScanFailed|Sökningen misslyckades|Count|Totalt|Anger antalet misslyckade genomsökningar.|ResourceId|
|ScanTimeTaken|Tids åtgång för genomsökning|Sekunder|Totalt|Anger den totala genomsöknings tiden i sekunder.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ListenerConnections – lyckad|ListenerConnections – lyckad|Count|Totalt|Lyckade ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections – ClientError|ListenerConnections – ClientError|Count|Totalt|ClientError på ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections – ServerError|ListenerConnections – ServerError|Count|Totalt|ServerError på ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections – lyckad|SenderConnections – lyckad|Count|Totalt|Lyckade SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections – ClientError|SenderConnections – ClientError|Count|Totalt|ClientError på SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections – ServerError|SenderConnections – ServerError|Count|Totalt|ServerError på SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections – TotalRequests|ListenerConnections – TotalRequests|Count|Totalt|Totalt ListenerConnections för Microsoft. Relay.|Entitetsnamnet|
|SenderConnections – TotalRequests|SenderConnections – TotalRequests|Count|Totalt|Totalt antal SenderConnections-begäranden för Microsoft. Relay.|Entitetsnamnet|
|ActiveConnections|ActiveConnections|Count|Totalt|Totalt ActiveConnections för Microsoft. Relay.|Entitetsnamnet|
|ActiveListeners|ActiveListeners|Count|Totalt|Totalt ActiveListeners för Microsoft. Relay.|Entitetsnamnet|
|BytesTransferred|BytesTransferred|Count|Totalt|Totalt BytesTransferred för Microsoft. Relay.|Entitetsnamnet|
|ListenerDisconnects|ListenerDisconnects|Count|Totalt|Totalt ListenerDisconnects för Microsoft. Relay.|Entitetsnamnet|
|SenderDisconnects|SenderDisconnects|Count|Totalt|Totalt SenderDisconnects för Microsoft. Relay.|Entitetsnamnet|


## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SearchLatency|Sök svars tid|Sekunder|Medelvärde|Genomsnittlig Sök fördröjning för Sök tjänsten|Inga|
|SearchQueriesPerSecond|Sök frågor per sekund|CountPerSecond|Medelvärde|Sök efter frågor per sekund för Sök tjänsten|Inga|
|ThrottledSearchQueriesPercentage|Begränsade Sök frågor i procent|Procent|Medelvärde|Procent andel Sök frågor som har begränsats för Sök tjänsten|Inga|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Slutförda förfrågningar|Count|Totalt|Totalt antal slutförda begär Anden för ett namn område|EntityName, OperationResult under pågående|
|ServerErrors|Serverfel.|Count|Totalt|Server fel för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|UserErrors|Användarfel.|Count|Totalt|Användar fel för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|ThrottledRequests|Begränsade förfrågningar.|Count|Totalt|Begränsade begär Anden för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|IncomingRequests|Inkommande förfrågningar|Count|Totalt|Inkommande begär Anden för Microsoft. Service Bus.|Entitetsnamnet|
|IncomingMessages|Inkommande meddelanden|Count|Totalt|Inkommande meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|OutgoingMessages|Utgående meddelanden|Count|Totalt|Utgående meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|ActiveConnections|ActiveConnections|Count|Totalt|Totalt antal aktiva anslutningar för Microsoft. Service Bus.|Inga|
|ConnectionsOpened|Öppna anslutningar.|Count|Medelvärde|Anslutningar som öppnats för Microsoft. Service Bus.|Entitetsnamnet|
|ConnectionsClosed|Stängda anslutningar.|Count|Medelvärde|Stängda anslutningar för Microsoft. Service Bus.|Entitetsnamnet|
|Storlek|Storlek|Byte|Medelvärde|Storlek på en kö/ett ämne i byte.|Entitetsnamnet|
|Meddelanden|Antal meddelanden i kö/ämne.|Count|Medelvärde|Antal meddelanden i kö/ämne.|Entitetsnamnet|
|ActiveMessages|Antal aktiva meddelanden i en kö/ett ämne.|Count|Medelvärde|Antal aktiva meddelanden i en kö/ett ämne.|Entitetsnamnet|
|DeadletteredMessages|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Count|Medelvärde|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Entitetsnamnet|
|ScheduledMessages|Antal schemalagda meddelanden i en kö/ett ämne.|Count|Medelvärde|Antal schemalagda meddelanden i en kö/ett ämne.|Entitetsnamnet|
|NamespaceCpuUsage|Processor|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd.|Replik|
|NamespaceMemoryUsage|Minnesanvändning|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd.|Replik|
|CPUXNS|PROCESSOR (inaktuell)|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd. Det här måttet är inaktuell. Använd processor måttet (NamespaceCpuUsage) i stället.|Replik|
|WSXNS|Minnes användning (inaktuell)|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd. Måttet är föråldrat. Använd minnes användnings måttet (NamespaceMemoryUsage) i stället.|Replik|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/program

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Medelvärde|CPU allokerad till den här behållaren i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Byte|Medelvärde|Minne som allokerats till den här behållaren i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Medelvärde|Faktisk processor användning i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Byte|Medelvärde|Faktisk minnes användning i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Medelvärde|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Medelvärde|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Medelvärde|Status för Service Fabric nätprogram|ApplicationName, status|
|ServiceStatus|ServiceStatus|Count|Medelvärde|Hälso status för en tjänst i Service Fabric nätprogram|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Medelvärde|Hälso status för en tjänst replik i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName|
|Container status|Container status|Count|Medelvärde|Status för behållaren i Service Fabric nätprogram|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Count|Medelvärde|Antal omstarter för en behållare i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ConnectionCount|Antal anslutningar|Count|Maximal|Mängden användar anslutning.|Slutpunkt|
|MessageCount|Antal meddelanden|Count|Totalt|Den totala mängden meddelanden.|Inga|
|InboundTraffic|Inkommande trafik|Byte|Totalt|Inkommande trafik trafik för tjänsten|Inga|
|OutboundTraffic|Utgående trafik|Byte|Totalt|Utgående trafik för tjänsten|Inga|
|UserErrors|Användar fel|Procent|Maximal|Procent andel användar fel|Inga|
|SystemErrors|Systemfel|Procent|Maximal|Procent andelen system fel|Inga|



## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inga|
|physical_data_read_percent|Data IO-procent|Procent|Medelvärde|Data IO-procent|Inga|
|log_write_percent|Logg IO-procent|Procent|Medelvärde|Logg IO-procent. Ej tillämpligt för data lager.|Inga|
|dtu_consumption_percent|DTU-procent|Procent|Medelvärde|DTU-procent. Gäller för DTU-baserade databaser.|Inga|
|storage|Använt data utrymme|Byte|Maximal|Använt data utrymme. Ej tillämpligt för data lager.|Inga|
|connection_successful|Lyckade anslutningar|Count|Totalt|Lyckade anslutningar|Inga|
|connection_failed|Misslyckade anslutningar|Count|Totalt|Misslyckade anslutningar|Inga|
|blocked_by_firewall|Blockerad av brand väggen|Count|Totalt|Blockerad av brand väggen|Inga|
|hamn|Låsningar|Count|Totalt|Låsningar. Ej tillämpligt för data lager.|Inga|
|storage_percent|Använt data utrymme i procent|Procent|Maximal|Data utrymme som används i procent. Ej tillämpligt för data lager eller storskaliga databaser.|Inga|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Procent|Medelvärde|Minnes intern OLTP-lagring i procent. Ej tillämpligt för data lager.|Inga|
|workers_percent|Arbetare i procent|Procent|Medelvärde|Arbetare i procent. Ej tillämpligt för data lager.|Inga|
|sessions_percent|Sessioner i procent|Procent|Medelvärde|Sessioner i procent. Ej tillämpligt för data lager.|Inga|
|dtu_limit|DTU-gräns|Count|Medelvärde|DTU-gräns. Gäller för DTU-baserade databaser.|Inga|
|dtu_used|Använt DTU|Count|Medelvärde|DTU används. Gäller för DTU-baserade databaser.|Inga|
|cpu_limit|PROCESSOR gräns|Count|Medelvärde|PROCESSOR gräns. Gäller för vCore-baserade databaser.|Inga|
|cpu_used|Använd CPU|Count|Medelvärde|Använd CPU. Gäller för vCore-baserade databaser.|Inga|
|dwu_limit|DWU-gräns|Count|Maximal|DWU-gräns. Gäller endast för data lager.|Inga|
|dwu_consumption_percent|DWU procent|Procent|Maximal|DWU procent. Gäller endast för data lager.|Inga|
|dwu_used|DWU som används|Count|Maximal|DWU som används. Gäller endast för data lager.|Inga|
|cache_hit_percent|Procent andel cacheträffar|Procent|Maximal|Procent av cacheträffar. Gäller endast för data lager.|Inga|
|cache_used_percent|Procent andel som används|Procent|Maximal|Procent andel som används. Gäller endast för data lager.|Inga|
|sqlserver_process_core_percent<sup>1</sup> |SQL Server process kärn procent|Procent|Maximal|PROCESSOR användning i procent för SQL Server processen, mätt av operativ systemet.|Inga|
|sqlserver_process_memory_percent<sup>1</sup> |SQL Server process minne i procent|Procent|Maximal|Minnes användnings procent för SQL Server processen, mätt av operativ systemet.|Inga|
|tempdb_data_size<sup>1</sup> |Data fil storlek i tempdb i KB|Count|Maximal|Data fil storlek för tempdb i KB.|Inga|
|tempdb_log_size<sup>1</sup> |TempDB-logg fils storlek kilobyte|Count|Maximal|TempDB-logg fils storlek kilobyte.|Inga|
|tempdb_log_used_percent<sup>1</sup> |Procent använt tempdb-logg|Procent|Maximal|TempDB procent logg används.|Inga|
|local_tempdb_usage_percent|Lokal tempdb-procent|Procent|Medelvärde|Lokal tempdb-procent. Gäller endast för data lager.|Inga|
|app_cpu_billed|App-CPU fakturerad|Count|Totalt|App-CPU fakturerad. Gäller databaser utan server.|Inga|
|app_cpu_percent|CPU-procent för app|Procent|Medelvärde|CPU-procent för app. Gäller databaser utan server.|Inga|
|app_memory_percent|Minnes procent för appar|Procent|Medelvärde|Minnes procent för appar. Gäller databaser utan server.|Inga|
|allocated_data_storage|Allokerat data utrymme|Byte|Medelvärde|Allokerad data lagring. Ej tillämpligt för data lager.|Inga|
|memory_usage_percent|Minnes procent|Procent|Maximal|Minnes procent. Gäller endast för data lager.|Inga|
|dw_backup_size_gb|Data lagrings storlek|Count|Totalt|Data lagrings storleken består av storleken på dina data och transaktions loggen. Måttet räknas till lagrings delen av din faktura. Gäller endast för data lager.|Inga|
|dw_snapshot_size_gb|Storlek för ögonblicks bild lagring|Count|Totalt|Storlek för ögonblicks bild lagring är storleken på de stegvisa ändringar som fångas av ögonblicks bilder för att skapa användardefinierade och automatiska återställnings punkter. Måttet räknas till lagrings delen av din faktura. Gäller endast för data lager.|Inga|
|dw_geosnapshot_size_gb|Lagrings storlek för katastrof återställning|Count|Totalt|Lagrings storleken för haveri beredskap visas som "haveri beredskaps lagring" på din faktura. Gäller endast för data lager.|Inga|
|wlg_allocation_relative_to_system_percent|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till hela systemet per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Allokering av arbets belastnings grupp per Cap-resurs procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till de angivna Cap-resurserna per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Aktiva frågor för arbets belastnings grupp|Count|Totalt|Aktiva frågor inom arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Köade frågor i arbets belastnings gruppen|Count|Totalt|Köade frågor i arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|active_queries|Aktiva frågor|Count|Totalt|Aktiva frågor för alla arbets belastnings grupper. Gäller endast för data lager.|Inga|
|queued_queries|Köade frågor|Count|Totalt|Köade frågor över alla arbets belastnings grupper. Gäller endast för data lager.|Inga|
|wlg_active_queries_timeouts|Fråge tids gränser för arbets belastnings grupp|Count|Totalt|Frågor som har nått tids gränsen för arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Effektiv minsta resurs procent|Procent|Maximal|Lägsta procent andel av resurser som är reserverade och isolerade för arbets belastnings gruppen, med hänsyn till den lägsta Service nivån. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Effektiv tak resurs procent|Procent|Maximal|En hård gräns för procent andelen av resurser som tillåts för arbets belastnings gruppen, med hänsyn till den lägsta resurs procent som allokerats för andra arbets belastnings grupper. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Lagrings storlek för fullständig säkerhets kopia|Byte|Maximal|Ackumulerad lagrings storlek för fullständig säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inga|
|diff_backup_size_bytes|Lagrings storlek för differentiell säkerhets kopia|Byte|Maximal|Kumulativ lagrings storlek för differentiell säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inga|
|log_backup_size_bytes|Logg lagrings storlek för logg säkerhets kopia|Byte|Maximal|Sammanlagd lagrings storlek för logg säkerhets kopia. Gäller för vCore-baserade och storskaliga databaser.|Inga|
|snapshot_backup_size_bytes|Lagrings storlek för ögonblicks bild säkerhets kopia|Byte|Maximal|Lagrings storlek för kumulativ ögonblicks bild. Gäller för storskaliga databaser.|Inga|
|base_blob_size_bytes|Bas för Blob Storage-storlek|Byte|Maximal|Bas för Blob Storage-storlek. Gäller för storskaliga databaser.|Inga|

<sup>1</sup> det här måttet är tillgängligt för databaser som använder vCore inköps modell med 2 virtuella kärnor och högre, eller 200 DTU och högre för DTU-baserade inköps modeller. 

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/Servers/elasticPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inga|
|database_cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|DatabaseResourceId|
|physical_data_read_percent|Data IO-procent|Procent|Medelvärde|Data IO-procent|Inga|
|database_physical_data_read_percent|Data IO-procent|Procent|Medelvärde|Data IO-procent|DatabaseResourceId|
|log_write_percent|Logg IO-procent|Procent|Medelvärde|Logg IO-procent|Inga|
|database_log_write_percent|Logg IO-procent|Procent|Medelvärde|Logg IO-procent|DatabaseResourceId|
|dtu_consumption_percent|DTU-procent|Procent|Medelvärde|DTU-procent. Gäller för DTU-baserade elastiska pooler.|Inga|
|database_dtu_consumption_percent|DTU-procent|Procent|Medelvärde|DTU-procent|DatabaseResourceId|
|storage_percent|Använt data utrymme i procent|Procent|Medelvärde|Använt data utrymme i procent|Inga|
|workers_percent|Arbetare i procent|Procent|Medelvärde|Arbetare i procent|Inga|
|database_workers_percent|Arbetare i procent|Procent|Medelvärde|Arbetare i procent|DatabaseResourceId|
|sessions_percent|Sessioner i procent|Procent|Medelvärde|Sessioner i procent|Inga|
|database_sessions_percent|Sessioner i procent|Procent|Medelvärde|Sessioner i procent|DatabaseResourceId|
|eDTU_limit|eDTU-gräns|Count|Medelvärde|eDTU-gräns. Gäller för DTU-baserade elastiska pooler.|Inga|
|storage_limit|Max storlek för data|Byte|Medelvärde|Max storlek för data|Inga|
|eDTU_used|eDTU använt|Count|Medelvärde|eDTU används. Gäller för DTU-baserade elastiska pooler.|Inga|
|database_eDTU_used|eDTU använt|Count|Medelvärde|eDTU använt|DatabaseResourceId|
|storage_used|Använt data utrymme|Byte|Medelvärde|Använt data utrymme|Inga|
|database_storage_used|Använt data utrymme|Byte|Medelvärde|Använt data utrymme|DatabaseResourceId|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Procent|Medelvärde|Minnes intern OLTP-lagring i procent|Inga|
|cpu_limit|PROCESSOR gräns|Count|Medelvärde|PROCESSOR gräns. Gäller vCore-baserade elastiska pooler.|Inga|
|database_cpu_limit|PROCESSOR gräns|Count|Medelvärde|PROCESSOR gräns|DatabaseResourceId|
|cpu_used|Använd CPU|Count|Medelvärde|Använd CPU. Gäller vCore-baserade elastiska pooler.|Inga|
|database_cpu_used|Använd CPU|Count|Medelvärde|Använd CPU|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|SQL Server process kärn procent|Procent|Maximal|PROCESSOR användning i procent för SQL Server processen, mätt av operativ systemet. Gäller elastiska pooler. |Inga|
|sqlserver_process_memory_percent<sup>1</sup>|SQL Server process minne i procent|Procent|Maximal|Minnes användnings procent för SQL Server processen, mätt av operativ systemet. Gäller elastiska pooler. |Inga|
|tempdb_data_size<sup>1</sup>|Data fil storlek i tempdb i KB|Count|Maximal|Data fil storlek för tempdb i KB.|Inga|
|tempdb_log_size<sup>1</sup>|TempDB-logg fils storlek kilobyte|Count|Maximal|TempDB-logg fils storlek kilobyte. |Inga|
|tempdb_log_used_percent<sup>1</sup>|Procent använt tempdb-logg|Procent|Maximal|TempDB procent logg används.|Inga|
|allocated_data_storage|Allokerat data utrymme|Byte|Medelvärde|Allokerat data utrymme|Inga|
|database_allocated_data_storage|Allokerat data utrymme|Byte|Medelvärde|Allokerat data utrymme|DatabaseResourceId|
|allocated_data_storage_percent|Allokerat data utrymme i procent|Procent|Maximal|Allokerat data utrymme i procent|Inga|

<sup>1</sup> det här måttet är tillgängligt för databaser som använder vCore inköps modell med 2 virtuella kärnor och högre, eller 200 DTU och högre för DTU-baserade inköps modeller. 

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|virtual_core_count|Antal virtuella kärnor|Count|Medelvärde|Antal virtuella kärnor|Inga|
|avg_cpu_percent|Genomsnittlig CPU-procent|Procent|Medelvärde|Genomsnittlig CPU-procent|Inga|
|reserved_storage_mb|Reserverat lagrings utrymme|Count|Medelvärde|Reserverat lagrings utrymme|Inga|
|storage_space_used_mb|Använt lagrings utrymme|Count|Medelvärde|Använt lagrings utrymme|Inga|
|io_requests|Antal IO-begäranden|Count|Medelvärde|Antal IO-begäranden|Inga|
|io_bytes_read|Lästa IO-byte|Byte|Medelvärde|Lästa IO-byte|Inga|
|io_bytes_written|Skrivna IO-byte|Byte|Medelvärde|Skrivna IO-byte|Inga|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Medelvärde|Kapacitet som används av konto|Inga|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|BLOB-antal|Count|Medelvärde|Antalet BLOB i lagrings kontots Blob Service.|BlobType,-nivå|
|ContainerCount|Antal BLOB-behållare|Count|Medelvärde|Antalet behållare i lagrings kontots Blob Service.|Inga|
|IndexCapacity|Index kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inga|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabell kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Table service i byte.|Inga|
|TableCount|Antal tabeller|Count|Medelvärde|Antalet tabeller i lagrings kontots Table service.|Inga|
|TableEntityCount|Antal tabell enheter|Count|Medelvärde|Antalet tabell enheter i lagrings kontots Table service.|Inga|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Fil kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|FileShare|
|FileCount|Antal filer|Count|Medelvärde|Antalet filer i lagrings kontots fil tjänst.|FileShare|
|FileShareCount|Antal fil resurser|Count|Medelvärde|Antalet fil resurser i lagrings kontots fil tjänst.|Inga|
|FileShareSnapshotCount|Antal ögonblicks bilder av fil resurs|Count|Medelvärde|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|Storlek på fil resursens ögonblicks bild|Byte|Medelvärde|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|FileShareCapacityQuota|Fil resursens kvot storlek|Byte|Medelvärde|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kös Kap kap.|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Kötjänst i byte.|Inga|
|QueueCount|Antal köer|Count|Medelvärde|Antalet köer i lagrings kontots Kötjänst.|Inga|
|QueueMessageCount|Antal meddelanden i kö|Count|Medelvärde|Ungefärligt antal köa meddelanden i lagrings kontots Kötjänst.|Inga|
|Transaktioner|Transaktioner|Count|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/cacheminnen

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ClientIOPS|Totalt antal klient-IOPS|Count|Medelvärde|Frekvensen av klient fil åtgärder som bearbetas av cachen.|Inga|
|ClientLatency|Genomsnittlig klient latens|Millisekunder|Medelvärde|Genomsnittlig svars tid för klient fil åtgärder till Storage cache.|Inga|
|ClientReadIOPS|Klient läsnings-IOPS|CountPerSecond|Medelvärde|Klient Läs åtgärder per sekund.|Inga|
|ClientReadThroughput|Genomsnittligt cache-läst data flöde|BytesPerSecond|Medelvärde|Överföringshastighet för klient läsnings data.|Inga|
|ClientWriteIOPS|Klient skrivnings-IOPS|CountPerSecond|Medelvärde|Klient skrivnings åtgärder per sekund.|Inga|
|ClientWriteThroughput|Genomsnittligt cacheminne Skriv data flöde|BytesPerSecond|Medelvärde|Data överförings takt för klient skrivning.|Inga|
|ClientMetadataReadIOPS|Lästa IOPS för klientens metadata|CountPerSecond|Medelvärde|Frekvensen av klient fil åtgärder som skickas till cachen, exklusive data läsningar som inte ändrar beständigt tillstånd.|Inga|
|ClientMetadataWriteIOPS|Klientens metadata Skriv IOPS|CountPerSecond|Medelvärde|Frekvensen av klient fil åtgärder som skickas till cachen, exklusive data skrivningar, som ändrar beständigt tillstånd.|Inga|
|ClientLockIOPS|Klient lås IOPS|CountPerSecond|Medelvärde|Klient fil låsnings åtgärder per sekund.|Inga|
|StorageTargetHealth|Lagrings mål hälsa|Count|Medelvärde|Booleska resultat för anslutnings test mellan cache-och lagrings målen.|Inga|
|Drift tid|Drift tid|Count|Medelvärde|Booleska resultat för anslutnings test mellan cache-och övervaknings systemet.|Inga|
|StorageTargetIOPS|Totalt antal StorageTarget IOPS|Count|Medelvärde|Frekvensen för alla fil åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget Skriv IOPS|Count|Medelvärde|Hastigheten för fil skrivnings åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget asynkron skrivning av data flöde|BytesPerSecond|Medelvärde|Hastigheten cachen skriver data asynkront till en viss StorageTarget. Detta är Opportunistic skrivningar som inte gör att klienter blockerar.|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget synkront Skriv data flöde|BytesPerSecond|Medelvärde|Hastigheten cachen skriver data synkront till en viss StorageTarget. Detta är skrivningar som gör att klienter blockerar.|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget totalt Skriv data flöde|BytesPerSecond|Medelvärde|Den totala taxan som cachen skriver data till en viss StorageTarget.|StorageTarget|
|StorageTargetLatency|StorageTarget-svars tid|Millisekunder|Medelvärde|Genomsnittlig fördröjning för fördröjning av alla fil åtgärder som cachen skickar till en partricular-StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget metadata Läs IOPS|CountPerSecond|Medelvärde|Frekvensen för fil åtgärder som inte ändrar beständigt tillstånd, och som utesluter Läs åtgärden, som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget metadata Skriv IOPS|CountPerSecond|Medelvärde|Frekvensen för fil åtgärder som ändrar beständigt tillstånd och exklusive Skriv åtgärden, som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget läsa IOPS|CountPerSecond|Medelvärde|Frekvensen för fil läsnings åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget Read i förväg genom strömning|BytesPerSecond|Medelvärde|Den hastighet med vilken cache-opportunisticly läser data från StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|StorageTarget Fill-genomflöde|BytesPerSecond|Medelvärde|Hastigheten som cachen läser data från StorageTarget för att hantera ett cache-missar.|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget total Read-genomflöde|BytesPerSecond|Medelvärde|Den totala taxan som cachen läser data från en viss StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultat av Sync-session|Count|Medelvärde|Mått som loggar värdet 1 varje gång Server slut punkten slutför en Sync-session med moln slut punkten|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synkroniserade filer|Count|Totalt|Antal filer som har synkroniserats|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Count|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Status för server online|Count|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Återställnings storlek för moln nivå|Byte|Totalt|Storlek på data som återkallas|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Återkalla data flöde för moln nivå|BytesPerSecond|Medelvärde|Storlek på data återkallande data flöde|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Återställnings storlek för moln skikt per program|Byte|Totalt|Storlek på data som återkallas av program|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Synkroniserade filer|Count|Totalt|Antal filer som har synkroniserats|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Count|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Synkroniserade filer|Count|Totalt|Antal filer som har synkroniserats|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Count|Totalt|Antal filer som inte kunde synkroniseras|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerHeartbeat|Status för server online|Count|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ResourceUtilization|SU%-användning|Procent|Maximal|SU%-användning|LogicalName, PartitionId|
|InputEvents|Inmatade händelser|Count|Totalt|Inmatade händelser|LogicalName, PartitionId|
|InputEventBytes|Inkommande händelse-byte|Byte|Totalt|Inkommande händelse-byte|LogicalName, PartitionId|
|LateInputEvents|Sena ingångs händelser|Count|Totalt|Sena ingångs händelser|LogicalName, PartitionId|
|OutputEvents|Utgående händelser|Count|Totalt|Utgående händelser|LogicalName, PartitionId|
|ConversionErrors|Data konverterings fel|Count|Totalt|Data konverterings fel|LogicalName, PartitionId|
|Fel|Körnings fel|Count|Totalt|Körnings fel|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Händelser som inte är i ordning|Count|Totalt|Händelser som inte är i ordning|LogicalName, PartitionId|
|AMLCalloutRequests|Funktions begär Anden|Count|Totalt|Funktions begär Anden|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Misslyckade funktions begär Anden|Count|Totalt|Misslyckade funktions begär Anden|LogicalName, PartitionId|
|AMLCalloutInputEvents|Funktions händelser|Count|Totalt|Funktions händelser|LogicalName, PartitionId|
|DeserializationError|Fel vid deserialisering av indataport|Count|Totalt|Fel vid deserialisering av indataport|LogicalName, PartitionId|
|EarlyInputEvents|Tidiga ingångs händelser|Count|Totalt|Tidiga ingångs händelser|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Fördröjning för vattenstämpel|Sekunder|Maximal|Fördröjning för vattenstämpel|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Eftersläpande inloggade ingångs händelser|Count|Maximal|Eftersläpande inloggade ingångs händelser|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Mottagna inmatade källor|Count|Totalt|Mottagna inmatade källor|LogicalName, PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|Pipelinen körs avslutad|Count|Totalt|Antal Dirigerings pipelines som lyckades, misslyckades eller avbröts|Resultat, FailureType, pipeline|
|OrchestrationActivityRunsEnded|Aktivitets körningar avslutad|Count|Totalt|Antalet Orchestration-aktiviteter som lyckades, misslyckades eller avbröts|Resultat, FailureType, aktivitet, ActivityType, pipeline|
|OrchestrationTriggersEnded|Slutförda utlösare|Count|Totalt|Antal Dirigerings utlösare som lyckades, misslyckades eller avbröts|Resultat, FailureType, utlösare|
|SQLOnDemandLoginAttempts|Inloggnings försök|Count|Totalt|Antal inloggnings försök som distribuerats eller misslyckats|Resultat|
|SQLOnDemandQueriesEnded|Avslutade frågor|Count|Totalt|Antal frågor som har lyckats, misslyckats eller avbrutits|Resultat|
|SQLOnDemandQueryProcessedBytes|Bearbetade data|Byte|Totalt|Mängden data som bearbetas av frågor|Inga|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/arbets ytor/bigDataPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SparkJobsEnded|Avslutade program|Count|Totalt|Antal avslutade program|JobType, JobResult|
|CoresCapacity|Kärnor kapacitet|Count|Maximal|Kärnor kapacitet|Inga|
|MemoryCapacityGB|Minnes kapacitet (GB)|Count|Maximal|Minnes kapacitet (GB)|Inga|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/arbets ytor/sqlPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DWULimit|DWU-gräns|Count|Maximal|SQL-poolens service nivå mål|Inga|
|DWUUsed|DWU som används|Count|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Uppmätt av DWU-gränsen * DWU procent|Inga|
|DWUUsedPercent|Procent Använd DWU|Procent|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Mäts genom att dra max värdet mellan CPU-procent och data IO-procent|Inga|
|ConnectionsBlockedByFirewall|Anslutningar blockerade av brand väggen|Count|Totalt|Antal anslutningar blockeras av brand Väggs regler. Gå tillbaka till principer för åtkomst kontroll för SQL-poolen och övervaka dessa anslutningar om antalet är högt|Inga|
|AdaptiveCacheHitPercent|Procentuellt antal träffar i adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med värdet för cache träff i procent för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inga|
|AdaptiveCacheUsedPercent|Procentuell användning av adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med den cachelagrade procentens mått för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inga|
|LocalTempDBUsedPercent|Lokal tempdb Använd procent|Procent|Maximal|Lokal tempdb-användning över alla Compute-noder – värden genereras var femte minut|Inga|
|MemoryUsedPercent|Använt minne i procent|Procent|Maximal|Minnes användning för alla noder i SQL-poolen|Inga|
|Anslutningar|Anslutningar|Count|Totalt|Totalt antal inloggningar till SQL-poolen|Resultat|
|WLGActiveQueries|Aktiva frågor för arbets belastnings grupp|Count|Totalt|Aktiva frågor i arbets belastnings gruppen. Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet|IsUserDefined,WorkloadGroup|
|WLGActiveQueriesTimeouts|Fråge tids gränser för arbets belastnings grupp|Count|Totalt|Frågor för arbets belastnings gruppen som har nått tids gränsen. Tids gränser för frågor som rapporteras av detta mått sker bara när frågan har börjat köras (den innehåller inte vänte tid på grund av låsning eller väntande resurs avbrott)|IsUserDefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Procent beläggningen av resurser i förhållande till hela systemet|IsUserDefined,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Allokering av arbets belastnings grupper efter max resurs procent|Procent|Maximal|Visar procent tilldelningen av resurser i förhållande till den effektiva Kap resurs procenten per arbets belastnings grupp. Detta mått ger arbets belastnings gruppens effektiva användning|IsUserDefined,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Effektiv tak resurs procent|Procent|Maximal|Den effektiva Kap resurs procenten för arbets belastnings gruppen. Om det finns andra arbets belastnings grupper med min_percentage_resource > 0 sänks effective_cap_percentage_resource proportionellt|IsUserDefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Effektiv minsta resurs procent|Procent|Minimum|Inställningen för lägsta procent andel av resursen som tillåts överväger Service nivån och inställningarna för arbets belastnings gruppen. Effektiv min_percentage_resource kan justeras högre på lägre service nivåer|IsUserDefined,WorkloadGroup|
|WLGQueuedQueries|Köade frågor i arbets belastnings gruppen|Count|Totalt|Antal sammanställda begär anden i kö efter att max gränsen för samtidighet nåddes|IsUserDefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/miljöer

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress mottagna meddelanden|Count|Totalt|Antal meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Count|Totalt|Antal ogiltiga meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från alla händelse källor|Inga|
|IngressStoredBytes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga|
|IngressStoredEvents|Ingress lagrade händelser|Count|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Count|Medelvärde|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Count|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Count|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/miljöer/eventsources

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress mottagna meddelanden|Count|Totalt|Antal meddelanden som lästs från händelse källan|Inga|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Count|Totalt|Antal ogiltiga meddelanden som lästs från händelse källan|Inga|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från händelse källan|Inga|
|IngressStoredBytes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga|
|IngressStoredEvents|Ingress lagrade händelser|Count|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Count|Medelvärde|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Count|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Count|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk-lästa byte/s|BytesPerSecond|Medelvärde|Genomsnittligt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga|
|DiskWriteBytesPerSecond|Disk-skrivna byte/s|BytesPerSecond|Medelvärde|Genomsnittligt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Totalt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga|
|DiskReadOperations|Disk Läs åtgärder|Count|Totalt|Antalet Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga|
|DiskWriteOperations|Disk skrivnings åtgärder|Count|Totalt|Antalet i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Genomsnittligt antal Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Genomsnittligt antal i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga|
|DiskReadLatency|Läs fördröjning för disk|Millisekunder|Medelvärde|Total Läs fördröjning. Summan av Läs fördröjningen för enheten och kärnan.|Inga|
|DiskWriteLatency|Skriv fördröjning för disk|Millisekunder|Medelvärde|Total Skriv fördröjning. Summan av Skriv fördröjningarna för enheten och kärnan.|Inga|
|NetworkInBytesPerSecond|Nätverk i byte/s|BytesPerSecond|Medelvärde|Genomsnittligt nätverks data flöde för mottagen trafik.|Inga|
|NetworkOutBytesPerSecond|Nätverks-out-byte/SEK|BytesPerSecond|Medelvärde|Genomsnittligt nätverks data flöde för överförd trafik.|Inga|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Totalt nätverks data flöde för mottagen trafik.|Inga|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Totalt nätverks data flöde för överförd trafik.|Inga|
|MemoryUsed|Använt minne|Byte|Medelvärde|Mängden dator minne som används av den virtuella datorn.|Inga|
|MemoryGranted|Beviljat minne|Byte|Medelvärde|Mängden minne som har beviljats för den virtuella datorn av värden. Minnet beviljas inte för värden förrän den är påslagen en gång och beviljat minne kan växlas ut eller visas borta om VMkernel behöver minnet.|Inga|
|MemoryActive|Aktivt minne|Byte|Medelvärde|Mängden minne som används av den virtuella datorn i det tidigare lilla tids fönstret. Detta är "true"-antalet hur mycket minne som den virtuella datorn för närvarande behöver. Ytterligare, oanvänt minne kan växlas ut eller visas utan att påverka gästens prestanda.|Inga|
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|PROCESSOR användningen. Det här värdet rapporteras med 100% som representerar alla processor kärnor i systemet. Till exempel är en 2-vägs virtuell dator som använder 50% av ett system med fyra kärnor fullständigt med två kärnor.|Inga|
|PercentageCpuReady|Procent andel CPU klar|Millisekunder|Totalt|Ready Time är den tid som väntar på att CPU: er ska bli tillgängliga under det tidigare uppdaterings intervallet.|Inga|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/Server grupper

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Procent|Medelvärde|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medelvärde|Minnes procent|Instans|
|DiskQueueLength|Diskkölängd|Count|Medelvärde|Diskkölängd|Instans|
|HttpQueueLength|Längd på http-kö|Count|Medelvärde|Längd på http-kö|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|TcpSynSent|TCP-syn har skickats|Count|Medelvärde|TCP-syn har skickats|Instans|
|TcpSynReceived|TCP-syn mottagen|Count|Medelvärde|TCP-syn mottagen|Instans|
|TcpEstablished|TCP upprättad|Count|Medelvärde|TCP upprättad|Instans|
|TcpFinWait1|TCP räntetrans wait 1|Count|Medelvärde|TCP räntetrans wait 1|Instans|
|TcpFinWait2|TCP räntetrans wait 2|Count|Medelvärde|TCP räntetrans wait 2|Instans|
|TcpClosing|TCP-stängning|Count|Medelvärde|TCP-stängning|Instans|
|TcpCloseWait|Väntan på TCP-stängning|Count|Medelvärde|Väntan på TCP-stängning|Instans|
|TcpLastAck|TCP-senaste ack|Count|Medelvärde|TCP-senaste ack|Instans|
|TcpTimeWait|Väntan på TCP-tid|Count|Medelvärde|Väntan på TCP-tid|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (exklusive funktioner) 

> [!NOTE]
> **Användning av fil system** är ett nytt mått som distribueras globalt, inga data förväntas om du inte har varit vit listas för privat för hands version.

> [!IMPORTANT]
> Den **genomsnittliga svars tiden** blir föråldrad för att undvika förvirring med mått agg regeringar. Använd **svars tid** som ersättning.

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Count|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Count|Totalt|Http 101|Instans|
|Http2xx|Http-2xx|Count|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Count|Totalt|Http-3xx|Instans|
|Http401|Http 401|Count|Totalt|Http 401|Instans|
|Http403|Http 403|Count|Totalt|Http 403|Instans|
|Http404|Http 404|Count|Totalt|Http 404|Instans|
|Http406|Http 406|Count|Totalt|Http 406|Instans|
|Http4xx|Http-4xx|Count|Totalt|Http-4xx|Instans|
|Http5xx|Http-serverfel|Count|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Minnes arbets mängd|Byte|Medelvärde|Minnes arbets mängd|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Byte|Medelvärde|Genomsnittlig arbets mängd för minne|Instans|
|HttpResponseTime|Svars tid|Sekunder|Totalt|Svars tid|Instans|
|AverageResponseTime|Genomsnittlig svars tid (inaktuell)|Sekunder|Medelvärde|Genomsnittlig svars tid|Instans|
|AppConnections|Anslutningar|Count|Medelvärde|Anslutningar|Instans|
|Konsolindataobjekt|Antal referenser|Count|Medelvärde|Antal referenser|Instans|
|Konversation|Antal trådar|Count|Medelvärde|Antal trådar|Instans|
|PrivateBytes|Privata byte|Byte|Medelvärde|Privata byte|Instans|
|IoReadBytesPerSecond|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoOtherBytesPerSecond|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Begär anden i program kön|Count|Medelvärde|Begär anden i program kön|Instans|
|CurrentAssemblies|Aktuella sammansättningar|Count|Medelvärde|Aktuella sammansättningar|Instans|
|TotalAppDomains|Totalt antal app-domäner|Count|Medelvärde|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal app-domäner som har inaktiverats|Count|Medelvärde|Totalt antal app-domäner som har inaktiverats|Instans|
|Gen0Collections|Skräp insamling för gen 0|Count|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Skräp insamling för gen 1|Count|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Gen 2 skräp insamling|Count|Totalt|Gen 2 skräp insamling|Instans|
|HealthCheckStatus|Hälso kontroll status|Count|Medelvärde|Hälso kontroll status|Instans|
|FileSystemUsage|Fil system användning|Byte|Medelvärde|Fil system användning|Inga|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (Functions)

> [!NOTE]
> **Användning av fil system** är ett nytt mått som distribueras globalt, inga data förväntas om du inte har varit vit listas för privat för hands version.

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http5xx|Http-serverfel|Count|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Minnes arbets mängd|Byte|Medelvärde|Minnes arbets mängd|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Byte|Medelvärde|Genomsnittlig arbets mängd för minne|Instans|
|FunctionExecutionUnits|Funktions körnings enheter|MB/millisekunder|Totalt|[Funktions körnings enheter](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instans|
|FunctionExecutionCount|Funktions körnings antal|Count|Totalt|Funktions körnings antal|Instans|
|PrivateBytes|Privata byte|Byte|Medelvärde|Privata byte|Instans|
|IoReadBytesPerSecond|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoOtherBytesPerSecond|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Begär anden i program kön|Count|Medelvärde|Begär anden i program kön|Instans|
|CurrentAssemblies|Aktuella sammansättningar|Count|Medelvärde|Aktuella sammansättningar|Instans|
|TotalAppDomains|Totalt antal app-domäner|Count|Medelvärde|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal app-domäner som har inaktiverats|Count|Medelvärde|Totalt antal app-domäner som har inaktiverats|Instans|
|Gen0Collections|Skräp insamling för gen 0|Count|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Skräp insamling för gen 1|Count|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Gen 2 skräp insamling|Count|Totalt|Gen 2 skräp insamling|Instans|
|HealthCheckStatus|Hälso kontroll status|Count|Medelvärde|Hälso kontroll status|Instans|
|FileSystemUsage|Fil system användning|Byte|Medelvärde|Fil system användning|Inga|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Count|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Count|Totalt|Http 101|Instans|
|Http2xx|Http-2xx|Count|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Count|Totalt|Http-3xx|Instans|
|Http401|Http 401|Count|Totalt|Http 401|Instans|
|Http403|Http 403|Count|Totalt|Http 403|Instans|
|Http404|Http 404|Count|Totalt|Http 404|Instans|
|Http406|Http 406|Count|Totalt|Http 406|Instans|
|Http4xx|Http-4xx|Count|Totalt|Http-4xx|Instans|
|Http5xx|Http-serverfel|Count|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Minnes arbets mängd|Byte|Medelvärde|Minnes arbets mängd|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Byte|Medelvärde|Genomsnittlig arbets mängd för minne|Instans|
|AverageResponseTime|Genomsnittlig svars tid|Sekunder|Medelvärde|Genomsnittlig svars tid|Instans|
|HttpResponseTime|Svars tid|Sekunder|Medelvärde|Svars tid|Instans|
|FunctionExecutionUnits|Funktions körnings enheter|Count|Totalt|Funktions körnings enheter|Instans|
|FunctionExecutionCount|Funktions körnings antal|Count|Totalt|Funktions körnings antal|Instans|
|AppConnections|Anslutningar|Count|Medelvärde|Anslutningar|Instans|
|Konsolindataobjekt|Antal referenser|Count|Medelvärde|Antal referenser|Instans|
|Konversation|Antal trådar|Count|Medelvärde|Antal trådar|Instans|
|PrivateBytes|Privata byte|Byte|Medelvärde|Privata byte|Instans|
|IoReadBytesPerSecond|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoOtherBytesPerSecond|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Begär anden i program kön|Count|Medelvärde|Begär anden i program kön|Instans|
|CurrentAssemblies|Aktuella sammansättningar|Count|Medelvärde|Aktuella sammansättningar|Instans|
|TotalAppDomains|Totalt antal app-domäner|Count|Medelvärde|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal app-domäner som har inaktiverats|Count|Medelvärde|Totalt antal app-domäner som har inaktiverats|Instans|
|Gen0Collections|Skräp insamling för gen 0|Count|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Skräp insamling för gen 1|Count|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Gen 2 skräp insamling|Count|Totalt|Gen 2 skräp insamling|Instans|
|HealthCheckStatus|Hälso kontroll status|Count|Medelvärde|Hälso kontroll status|Instans|
|FileSystemUsage|Fil system användning|Byte|Medelvärde|Fil system användning|Inga|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Count|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101|Http 101|Count|Totalt|Http 101|Instans|
|Http2xx|Http-2xx|Count|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Count|Totalt|Http-3xx|Instans|
|Http401|Http 401|Count|Totalt|Http 401|Instans|
|Http403|Http 403|Count|Totalt|Http 403|Instans|
|Http404|Http 404|Count|Totalt|Http 404|Instans|
|Http406|Http 406|Count|Totalt|Http 406|Instans|
|Http4xx|Http-4xx|Count|Totalt|Http-4xx|Instans|
|Http5xx|Http-serverfel|Count|Totalt|Http-serverfel|Instans|
|AverageResponseTime|Genomsnittlig svars tid|Sekunder|Medelvärde|Genomsnittlig svars tid|Instans|
|CpuPercentage|CPU-procent|Procent|Medelvärde|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medelvärde|Minnes procent|Instans|
|DiskQueueLength|Diskkölängd|Count|Medelvärde|Diskkölängd|Instans|
|HttpQueueLength|Längd på http-kö|Count|Medelvärde|Längd på http-kö|Instans|
|ActiveRequests|Aktiva begär Anden|Count|Totalt|Aktiva begär Anden|Instans|
|TotalFrontEnds|Totalt antal klient delar|Count|Medelvärde|Totalt antal klient delar|Inga|
|SmallAppServicePlanInstances|Små App Service planera arbetare|Count|Medelvärde|Små App Service planera arbetare|Inga|
|MediumAppServicePlanInstances|Medel App Service planera arbetare|Count|Medelvärde|Medel App Service planera arbetare|Inga|
|LargeAppServicePlanInstances|Stora App Service planera arbetare|Count|Medelvärde|Stora App Service planera arbetare|Inga|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbetare|Count|Medelvärde|Totalt antal arbetare|Inga|
|WorkersAvailable|Tillgängliga arbetare|Count|Medelvärde|Tillgängliga arbetare|Inga|
|WorkersUsed|Använda arbetare|Count|Medelvärde|Använda arbetare|Inga|
|CpuPercentage|CPU-procent|Procent|Medelvärde|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medelvärde|Minnes procent|Instans|
## <a name="next-steps"></a>Nästa steg
* [Läs om mått i Azure Monitor](data-platform.md)
* [Skapa aviseringar för mått](alerts-overview.md)
* [Exportera mått till lagring, Event Hub eller Log Analytics](platform-logs-overview.md)
