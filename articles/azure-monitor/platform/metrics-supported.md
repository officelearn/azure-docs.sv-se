---
title: Azure Monitor mått som stöds efter resurs typ
description: Lista över mått som är tillgängliga för varje resurs typ med Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 06/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: ffdfeacad2fcfa7f77f3bcb55e8b1edaea865202
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145166"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor

> [!NOTE]
> Den här listan genereras i stort sett automatiskt från Azure Monitor mått REST API. Alla ändringar som görs i den här listan via GitHub kan skrivas över utan varning. Kontakta författaren till den här artikeln om du vill ha mer information om hur du gör permanenta uppdateringar.

Azure Monitor tillhandahåller flera olika sätt att interagera med mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av PowerShell eller CLI. 

Den här artikeln är en fullständig lista över alla plattformar (dvs. automatiskt insamlade) mått som för närvarande är tillgängliga med Azure Monitor den konsoliderade mått pipelinen. Listan senast uppdaterades mars 27 2020. Mått som har ändrats eller lagts till efter det här datumet får inte visas nedan. Om du vill fråga efter och komma åt listan över mått program mässigt kan du använda [2018-01-01 API-versionen](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Andra mått som inte finns med i listan kan finnas tillgängliga i portalen eller med äldre API: er.

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
|qpu_metric|QPU|Antal|Medelvärde|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|memory_metric|Minne|Byte|Medelvärde|Memory. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|private_bytes_metric|Privata byte|Byte|Medelvärde|Privata byte.|ServerResourceType|
|virtual_bytes_metric|Virtuella byte|Byte|Medelvärde|Virtuella byte.|ServerResourceType|
|TotalConnectionRequests|Totalt antal anslutnings begär Anden|Antal|Medelvärde|Totalt antal anslutnings begär Anden. Dessa är mottagna.|ServerResourceType|
|SuccessfullConnectionsPerSec|Lyckade anslutningar per sekund|CountPerSecond|Medelvärde|Antal slutförda slut för ande av anslutningar.|ServerResourceType|
|TotalConnectionFailures|Totalt antal anslutnings problem|Antal|Medelvärde|Totalt antal misslyckade anslutnings försök.|ServerResourceType|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medelvärde|Aktuellt antal användarsessioner som har upprättats.|ServerResourceType|
|QueryPoolBusyThreads|Upptagna trådar för frågeprocessorn|Antal|Medelvärde|Antalet upptagna trådar i trådpoolen.|ServerResourceType|
|CommandPoolJobQueueLength|Kölängd för kommando bassäng|Antal|Medelvärde|Antal jobb i kön för kommando tråds poolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Bearbetar Kölängd för poolen|Antal|Medelvärde|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|CurrentConnections|Anslutning: aktuella anslutningar|Antal|Medelvärde|Aktuellt antal upprättade klient anslutningar.|ServerResourceType|
|CleanerCurrentPrice|Minne: aktuellt pris för renare|Antal|Medelvärde|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Minne: krympnings utrymme för rensning|Byte|Medelvärde|Mängden minne i byte som kan rensas av bakgrunds rensaren.|ServerResourceType|
|CleanerMemoryNonshrinkable|Minne: det går inte att krympa rengörings minnet|Byte|Medelvärde|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|ServerResourceType|
|MemoryUsage|Minne: minnes användning|Byte|Medelvärde|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar minne som har mappats eller allokerats av xVelocity i Memory Analytics Engine (VertiPaq) utöver minnes gränsen för xVelocity-motorn.|ServerResourceType|
|MemoryLimitHard|Minne: minnes gräns hårt|Byte|Medelvärde|Hård minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitHigh|Minne: minnes gräns hög|Byte|Medelvärde|Hög minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitLow|Minne: minnes gräns låg|Byte|Medelvärde|Låg minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitVertiPaq|Minne: minnes gräns VertiPaq|Byte|Medelvärde|Minnes intern gräns, från konfigurations filen.|ServerResourceType|
|Kvot|Minne: kvot|Byte|Medelvärde|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|ServerResourceType|
|QuotaBlocked|Minne: kvot blockerad|Antal|Medelvärde|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|ServerResourceType|
|VertiPaqNonpaged|Minne: VertiPaq som inte är växlat|Byte|Medelvärde|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|ServerResourceType|
|VertiPaqPaged|Minne: VertiPaq växlat|Byte|Medelvärde|Byte av växlings Bart minne som används för minnes intern data.|ServerResourceType|
|RowsReadPerSec|Bearbetar: rader lästa per sekund|CountPerSecond|Medelvärde|Antalet rader som läses från alla Relations databaser.|ServerResourceType|
|RowsConvertedPerSec|Bearbetar: rader konverterade per sekund|CountPerSecond|Medelvärde|Antal rader som konverterats under bearbetning.|ServerResourceType|
|RowsWrittenPerSec|Bearbetar: rader skrivna per sekund|CountPerSecond|Medelvärde|Antal rader som skrivs under bearbetningen.|ServerResourceType|
|CommandPoolBusyThreads|Trådar: pool med upptagna kommando trådar|Antal|Medelvärde|Antalet upptagna trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolIdleThreads|Trådar: inaktiva trådar för kommando pool|Antal|Medelvärde|Antal inaktiva trådar i kommando tråds-poolen.|ServerResourceType|
|LongParsingBusyThreads|Trådar: lång parsning av upptagna trådar|Antal|Medelvärde|Antalet upptagna trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingIdleThreads|Trådar: inaktiva trådar för lång parsning|Antal|Medelvärde|Antalet inaktiva trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingJobQueueLength|Trådar: lång parsning av jobb Kölängd|Antal|Medelvärde|Antal jobb i kön för den långa parsande trådpoolen.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Trådar: bearbetning av pool upptagna I/O-jobb trådar|Antal|Medelvärde|Antal trådar som kör i/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Trådar: bearbetning av pool upptagna icke-I/O-trådar|Antal|Medelvärde|Antal trådar som kör icke-I/O-jobb i bearbetningen av trådpoolen.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Trådar: bearbeta pool I/O-jobb Kölängd|Antal|Medelvärde|Antalet I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Trådar: behandlar inaktiva I/O-jobb trådar för poolen|Antal|Medelvärde|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Trådar: behandlar inaktiva icke-I/O-trådar som behandlar poolen|Antal|Medelvärde|Antalet inaktiva trådar i bearbetnings trådens pool som är dedikerad till icke-I/O-jobb.|ServerResourceType|
|QueryPoolIdleThreads|Trådar: inaktiva trådar i lagringspoolen|Antal|Medelvärde|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|QueryPoolJobQueueLength|Trådar: jobbkölängd för jobbkö|Antal|Medelvärde|Antal jobb i kön för trådpoolen för Query.|ServerResourceType|
|ShortParsingBusyThreads|Trådar: kort parsning upptagna trådar|Antal|Medelvärde|Antalet upptagna trådar i den kort parsar trådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Trådar: kort parsning inaktiva trådar|Antal|Medelvärde|Antal inaktiva trådar i kort parsar trådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Trådar: kort parsning av jobb Kölängd|Antal|Medelvärde|Antal jobb i kö för kort parsar trådpoolen.|ServerResourceType|
|memory_thrashing_metric|Minnesförslöing|Procent|Medelvärde|Genomsnittligt minne nedskräpning.|ServerResourceType|
|mashup_engine_qpu_metric|M-motor QPU|Antal|Medelvärde|QPU användning av kombinations motor processer|ServerResourceType|
|mashup_engine_memory_metric|M motor minne|Byte|Medelvärde|Minnes användning per kombinations motor processer|ServerResourceType|
|mashup_engine_private_bytes_metric|M motor privata byte|Byte|Medelvärde|Användning av privata byte med kombinations motor processer.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Virtuella byte i M-motor|Byte|Medelvärde|Virtuella byte som används av kombinations motor processer.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalRequests|Totalt antal Gateway-begäranden (inaktuella)|Antal|Totalt|Antal Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|SuccessfulRequests|Lyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal lyckade Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|UnauthorizedRequests|Obehöriga Gateway-begäranden (inaktuella)|Antal|Totalt|Antal otillåtna Gateway-begäranden – Använd dimensions mått för flera dimensioner med GatewayResponseCodeCategory dimension i stället|Plats, värdnamn|
|FailedRequests|Misslyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal misslyckade Gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|OtherRequests|Andra gateway-begäranden (inaktuella)|Antal|Totalt|Antal andra gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|Varaktighet|Total varaktighet för gateway-begäranden|Millisekunder|Medelvärde|Total varaktighet för gateway-begäranden i millisekunder|Plats, värdnamn|
|BackendDuration|Varaktighet för backend-begäranden|Millisekunder|Medelvärde|Varaktighet för backend-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Kapacitet|Procent|Medelvärde|Användnings mått för API Management-tjänsten|Plats|
|EventHubTotalEvents|Totalt antal EventHub-händelser|Antal|Totalt|Antal händelser som skickats till EventHub|Plats|
|EventHubSuccessfulEvents|Lyckade EventHub-händelser|Antal|Totalt|Antal lyckade EventHub-händelser|Plats|
|EventHubTotalFailedEvents|Misslyckade EventHub-händelser|Antal|Totalt|Antal misslyckade EventHub-händelser|Plats|
|EventHubRejectedEvents|Avvisade EventHub-händelser|Antal|Totalt|Antal avvisade EventHub-händelser (felaktig konfiguration eller otillåten)|Plats|
|EventHubThrottledEvents|Begränsade EventHub-händelser|Antal|Totalt|Antal begränsade EventHub-händelser|Plats|
|EventHubTimedoutEvents|Tids gränsen nåddes för EventHub-händelser|Antal|Totalt|Antal inaktuella EventHub-händelser|Plats|
|EventHubDroppedEvents|Ignorerade EventHub-händelser|Antal|Totalt|Antalet händelser som hoppades över på grund av att gränsen för kös Tor lek har uppnåtts|Plats|
|EventHubTotalBytesSent|Storlek på EventHub-händelser|Byte|Totalt|Total storlek för EventHub-händelser i byte|Plats|
|Begäranden|Begäranden|Antal|Totalt|Mått för gateway-begäran med flera dimensioner|Plats, värdnamn, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|NetworkConnectivity|Status för resurs för nätverks anslutning (förhands granskning)|Antal|Totalt|Nätverks anslutnings status för beroende resurs typer från API Management-tjänsten|Plats, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Antal|Antal|Totalt antal inkommande HTTP-begäranden.|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Antal|Medelvärde|Svars tid på en http-begäran.|StatusCode|


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
|GCPauseTotalCount|Antal pauser för GC|Antal|Totalt|Antal pauser för GC|AppName, Pod|
|GCPauseTotalTime|Total tid för GC-paus|Millisekunder|Totalt|Total tid för GC-paus|AppName, Pod|
|TomcatSentBytes|Totalt antal skickade byte i Tomcat|Byte|Totalt|Totalt antal skickade byte i Tomcat|AppName, Pod|
|TomcatReceivedBytes|Totalt antal mottagna byte i Tomcat|Byte|Totalt|Totalt antal mottagna byte i Tomcat|AppName, Pod|
|TomcatRequestTotalTime|Totalt antal gånger för Tomcat-begäran|Millisekunder|Totalt|Totalt antal gånger för Tomcat-begäran|AppName, Pod|
|TomcatRequestTotalCount|Totalt antal Tomcat-begäranden|Antal|Totalt|Totalt antal Tomcat-begäranden|AppName, Pod|
|TomcatResponseAvgTime|Genomsnittlig tid för Tomcat-begäran|Millisekunder|Medelvärde|Genomsnittlig tid för Tomcat-begäran|AppName, Pod|
|TomcatRequestMaxTime|Max tid för Tomcat-begäran|Millisekunder|Maximal|Max tid för Tomcat-begäran|AppName, Pod|
|TomcatErrorCount|Tomcat globalt fel|Antal|Totalt|Tomcat globalt fel|AppName, Pod|
|TomcatSessionActiveMaxCount|Antal aktiva Tomcat-sessioner|Antal|Totalt|Antal aktiva Tomcat-sessioner|AppName, Pod|
|TomcatSessionAliveMaxTime|Maximal Alive-tid för Tomcat-session|Millisekunder|Maximal|Maximal Alive-tid för Tomcat-session|AppName, Pod|
|TomcatSessionActiveCurrentCount|Tomcat-session Alive-antal|Antal|Totalt|Tomcat-session Alive-antal|AppName, Pod|
|TomcatSessionCreatedCount|Antal skapade Tomcat-sessioner|Antal|Totalt|Antal skapade Tomcat-sessioner|AppName, Pod|
|TomcatSessionExpiredCount|Antal utgångna Tomcat-sessioner|Antal|Totalt|Antal utgångna Tomcat-sessioner|AppName, Pod|
|TomcatSessionRejectedCount|Antal nekade Tomcat-sessioner|Antal|Totalt|Antal nekade Tomcat-sessioner|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Runbook, status|
|TotalUpdateDeploymentRuns|Antal körningar av uppdateringsdistributionen|Antal|Totalt|Totalt antal körningar av program uppdaterings distribution|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Antal datorspecifika körningar av uppdateringsdistributionen|Antal|Totalt|Total distributions dator för program uppdatering körs i en körning av program uppdaterings distribution|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
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
|TaskStartEvent|Aktivitetens start händelser|Antal|Totalt|Totalt antal aktiviteter som har startat|poolId, jobId|
|TaskCompleteEvent|Uppgift slutförda händelser|Antal|Totalt|Totalt antal slutförda uppgifter|poolId, jobId|
|TaskFailEvent|Aktivitet, misslyckade händelser|Antal|Totalt|Totalt antal uppgifter som har slutförts i felaktigt tillstånd|poolId, jobId|
|PoolCreateEvent|Skapa händelser för pool|Antal|Totalt|Totalt antal pooler som har skapats|poolId|
|PoolResizeStartEvent|Starta händelser för att ändra storlek på poolen|Antal|Totalt|Totalt antal storleks ändringar för pooler som har startat|poolId|
|PoolResizeCompleteEvent|Slutför händelser för storleks ändring av pool|Antal|Totalt|Totalt antal ändrade pooler som har slutförts|poolId|
|PoolDeleteStartEvent|Start händelser för borttagning av pool|Antal|Totalt|Totalt antal borttagningar av pooler som har startat|poolId|
|PoolDeleteCompleteEvent|Slutför händelse för borttagning av pool|Antal|Totalt|Totalt antal borttagna pooler som har slutförts|poolId|
|JobDeleteCompleteEvent|Slutför händelser för borttagning av jobb|Antal|Totalt|Totalt antal jobb som har tagits bort.|jobId|
|JobDeleteStartEvent|Start händelser för jobb borttagning|Antal|Totalt|Totalt antal jobb som har begärts att tas bort.|jobId|
|JobDisableCompleteEvent|Jobb för att inaktivera slutförda händelser|Antal|Totalt|Totalt antal jobb som har inaktiverats.|jobId|
|JobDisableStartEvent|Jobb inaktivera start händelser|Antal|Totalt|Totalt antal jobb som har begärts att inaktive ras.|jobId|
|JobStartEvent|Jobb start händelser|Antal|Totalt|Totalt antal jobb som har startats.|jobId|
|JobTerminateCompleteEvent|Slutför händelser för avsluta jobb|Antal|Totalt|Totalt antal jobb som har avslut ATS.|jobId|
|JobTerminateStartEvent|Jobb som avslutar start händelser|Antal|Totalt|Totalt antal jobb som har begärts att avslutas.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Jobb skickat|Jobb skickat|Antal|Totalt|Antal skickade jobb|Scenario, kluster namn|
|Jobbet har slutförts|Jobbet har slutförts|Antal|Totalt|Antal slutförda jobb|Scenario, kluster namn, ResultType|
|Totalt antal noder|Totalt antal noder|Antal|Medelvärde|Antal totala noder|Scenario, kluster namn|
|Aktiva noder|Aktiva noder|Antal|Medelvärde|Antal noder som körs|Scenario, kluster namn|
|Inaktiva noder|Inaktiva noder|Antal|Medelvärde|Antal inaktiva noder|Scenario, kluster namn|
|Oanvändbara noder|Oanvändbara noder|Antal|Medelvärde|Antal oanvändbara noder|Scenario, kluster namn|
|Misslyckade noder|Misslyckade noder|Antal|Medelvärde|Antal misslyckade noder|Scenario, kluster namn|
|Lämnar noder|Lämnar noder|Antal|Medelvärde|Antal lämnar noder|Scenario, kluster namn|
|Totalt antal kärnor|Totalt antal kärnor|Antal|Medelvärde|Antal total kärnor|Scenario, kluster namn|
|Aktiva kärnor|Aktiva kärnor|Antal|Medelvärde|Antal aktiva kärnor|Scenario, kluster namn|
|Inaktiva kärnor|Inaktiva kärnor|Antal|Medelvärde|Antal inaktiva kärnor|Scenario, kluster namn|
|Oanvändbara kärnor|Oanvändbara kärnor|Antal|Medelvärde|Antal oanvändbara kärnor|Scenario, kluster namn|
|Blockerade kärnor|Blockerade kärnor|Antal|Medelvärde|Antal blockerade kärnor|Scenario, kluster namn|
|Lämnar kärnor|Lämnar kärnor|Antal|Medelvärde|Antal lämnar kärnor|Scenario, kluster namn|
|Kvot användning i procent|Kvot användning i procent|Antal|Medelvärde|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|

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
|ConnectionAccepted|Godkända anslutningar|Antal|Totalt|Godkända anslutningar|Nod|
|ConnectionHandled|Hanterade anslutningar|Antal|Totalt|Hanterade anslutningar|Nod|
|ConnectionActive|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Nod|
|RequestHandled|Hanterade begär Anden|Antal|Totalt|Hanterade begär Anden|Nod|
|ProcessedBlocks|Bearbetade block|Antal|Totalt|Bearbetade block|Nod|
|ProcessedTransactions|Bearbetade transaktioner|Antal|Totalt|Bearbetade transaktioner|Nod|
|PendingTransactions|Väntande transaktioner|Antal|Medelvärde|Väntande transaktioner|Nod|
|QueuedTransactions|Köade transaktioner|Antal|Medelvärde|Köade transaktioner|Nod|



## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedclients|Anslutna klienter|Antal|Maximal||ShardId|
|totalcommandsprocessed|Totalt antal åtgärder|Antal|Totalt||ShardId|
|cachehits|Cacheträffar|Antal|Totalt||ShardId|
|cachemisses|Cachemissar|Antal|Totalt||ShardId|
|cachemissrate|Missar i cache|Procent|cachemissrate||ShardId|
|getcommands|Hämtningar|Antal|Totalt||ShardId|
|setcommands|Uppsättningar|Antal|Totalt||ShardId|
|operationsPerSecond|Åtgärder per sekund|Antal|Maximal||ShardId|
|evictedkeys|Avlägsnade nycklar|Antal|Totalt||ShardId|
|totalkeys|Totalt antal nycklar|Antal|Maximal||ShardId|
|expiredkeys|Utgångna nycklar|Antal|Totalt||ShardId|
|usedmemory|Använt minne|Byte|Maximal||ShardId|
|usedmemorypercentage|Använt minne i procent|Procent|Maximal||ShardId|
|usedmemoryRss|RSS för använt minne|Byte|Maximal||ShardId|
|serverLoad|Serverbelastning|Procent|Maximal||ShardId|
|cacheWrite|Cacheskrivning|BytesPerSecond|Maximal||ShardId|
|cacheRead|Cacheläsning|BytesPerSecond|Maximal||ShardId|
|percentProcessorTime|Processor|Procent|Maximal||ShardId|
|cacheLatency|Mikrosekunder för cache-fördröjning (för hands version)|Antal|Medelvärde||ShardId|
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

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|Inget|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|Inget|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|Inget|
|Disk-lästa byte/s|Disk läsning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|Inget|
|Disk-skrivna byte/s|Disk skrivning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|Inget|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS.|Inget|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk.|Inget|


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
|UsedCapacity|Använd kapacitet|Byte|Medelvärde|Kapacitet som används av konto|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|BLOB-antal|Antal|Medelvärde|Antalet BLOB i lagrings kontots Blob Service.|BlobType,-nivå|
|ContainerCount|Antal BLOB-behållare|Antal|Medelvärde|Antalet behållare i lagrings kontots Blob Service.|Inget|
|IndexCapacity|Index kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabell kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Table service i byte.|Inget|
|TableCount|Antal tabeller|Antal|Medelvärde|Antalet tabeller i lagrings kontots Table service.|Inget|
|TableEntityCount|Antal tabell enheter|Antal|Medelvärde|Antalet tabell enheter i lagrings kontots Table service.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Fil kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|FileShare|
|FileCount|Antal filer|Antal|Medelvärde|Antalet filer i lagrings kontots fil tjänst.|FileShare|
|FileShareCount|Antal fil resurser|Antal|Medelvärde|Antalet fil resurser i lagrings kontots fil tjänst.|Inget|
|FileShareSnapshotCount|Antal ögonblicks bilder av fil resurs|Antal|Medelvärde|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|Storlek på fil resursens ögonblicks bild|Byte|Medelvärde|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|FileShareQuota|Fil resursens kvot storlek|Byte|Medelvärde|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kös Kap kap.|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Kötjänst i byte.|Inget|
|QueueCount|Antal köer|Antal|Medelvärde|Antalet köer i lagrings kontots Kötjänst.|Inget|
|QueueMessageCount|Antal meddelanden i kö|Antal|Medelvärde|Ungefärligt antal köa meddelanden i lagrings kontots Kötjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalCalls|Totalt antal anrop|Antal|Totalt|Totalt antal anrop.|ApiName, OperationName, region|
|SuccessfulCalls|Lyckade anrop|Antal|Totalt|Antal lyckade anrop.|ApiName, OperationName, region|
|TotalErrors|Totalt antal fel|Antal|Totalt|Totalt antal anrop med fel svar (HTTP-svarskod 4xx eller 5xx).|ApiName, OperationName, region|
|BlockedCalls|Blockerade anrop|Antal|Totalt|Antal anrop som har överskridit frekvensen eller kvot gränsen.|ApiName, OperationName, region|
|ServerErrors|Server fel|Antal|Totalt|Antal anrop med internt tjänst fel (HTTP-svarskod 5xx).|ApiName, OperationName, region|
|ClientErrors|Klient fel|Antal|Totalt|Antal anrop med fel på klient sidan (HTTP-svarskod 4xx).|ApiName, OperationName, region|
|Datain|Data i|Byte|Totalt|Storlek på inkommande data i byte.|ApiName, OperationName, region|
|Data|Data ut|Byte|Totalt|Storlek på utgående data i byte.|ApiName, OperationName, region|
|Svarstid|Svarstid|Millisekunder|Medelvärde|Svars tid i millisekunder.|ApiName, OperationName, region|
|TotalTokenCalls|Totalt antal token-anrop|Antal|Totalt|Totalt antal token-anrop.|ApiName, OperationName, region|
|CharactersTranslated|Översatta tecken|Antal|Totalt|Totalt antal tecken i begäran om inkommande text.|ApiName, OperationName, region|
|CharactersTrained|Upptränade tecken|Antal|Totalt|Totalt antal tecken som har tränats.|ApiName, OperationName, region|
|SpeechSessionDuration|Tal sessionens varaktighet|Sekunder|Totalt|Total varaktighet för talläget på några sekunder.|ApiName, OperationName, region|
|TotalTransactions|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner.|Inget|
|ProcessedImages|Bearbetade bilder|Antal|Totalt| Antal transaktioner för bild bearbetning.|ApiName, FeatureName, kanal, region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inget|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inget|
|Nätverk – utgående|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inget|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inget|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inget|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS|Inget|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk|Inget|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medelvärde|Totalt antal krediter som är tillgängliga för burst|Inget|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medelvärde|Totalt antal krediter som konsumeras av den virtuella datorn|Inget|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk [(inaktuellt)](portal-disk-metrics-deprecation.md)] (portal-disk-Metrics-deprecation.MD)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|KÖDJUP för operativ system per disk|OS-KÖDJUP [(inaktuellt)](portal-disk-metrics-deprecation.md)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Ködjup för datadisk|Data disk Queue djup (för hands version)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Inkommande flöden|Inkommande flöden|Antal|Medelvärde|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inget|
|Utgående flöden|Utgående flöden|Antal|Medelvärde|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inget|
|Högsta skapande frekvens för inkommande flöden|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Medelvärde|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inget|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden|CountPerSecond|Medelvärde|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inget|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medelvärde|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medelvärde|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Läsning av Premium OS-diskcachen|Inget|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Read missar i Premium OS-diskcachen|Inget|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inget|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inget|


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
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medelvärde|Totalt antal krediter som är tillgängliga för burst|Inget|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medelvärde|Totalt antal krediter som konsumeras av den virtuella datorn|Inget|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk [(inaktuellt)](portal-disk-metrics-deprecation.md)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|KÖDJUP för operativ system per disk|OS-KÖDJUP [(inaktuellt)](portal-disk-metrics-deprecation.md)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|LUN, VMName|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|LUN, VMName|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Ködjup för datadisk|Data disk Queue djup (för hands version)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|LUN, VMName|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|VMName|
|Inkommande flöden|Inkommande flöden|Antal|Medelvärde|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|VMName|
|Utgående flöden|Utgående flöden|Antal|Medelvärde|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|VMName|
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
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inget|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inget|
|Nätverk – utgående|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inget|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inget|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inget|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS|Inget|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk|Inget|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medelvärde|Totalt antal krediter som är tillgängliga för burst|Inget|
|Förbrukade CPU-krediter|Förbrukade CPU-krediter|Antal|Medelvärde|Totalt antal krediter som konsumeras av den virtuella datorn|Inget|
|Lästa byte per disk/SEK|Lästa byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Skrivna byte på datadisk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Läs åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Skriv åtgärder för data disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Per disk KÖDJUP|KÖDJUP för data disk [(inaktuellt)](portal-disk-metrics-deprecation.md)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per operativ system disk/SEK|Lästa byte för OS-disk/SEK [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system per disk/SEK|Läs åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system per disk/SEK|Skriv åtgärder för operativ system disk/SEK [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|KÖDJUP för operativ system per disk|OS-KÖDJUP [(inaktuellt)](portal-disk-metrics-deprecation.md)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Lästa byte på datadisk/SEK|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Skrivna byte på datadisk/SEK|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Ködjup för datadisk|Data disk Queue djup (för hands version)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte för OS-disk/SEK|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skrivna byte per operativ system disk/SEK|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Läs åtgärder för operativ system disk/SEK|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Skriv åtgärder för operativ system disk/SEK|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inget|
|Ködjup för OS-disk|Ködjup för OS-disk (för hands version)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inget|
|Inkommande flöden|Inkommande flöden|Antal|Medelvärde|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inget|
|Utgående flöden|Utgående flöden|Antal|Medelvärde|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inget|
|Högsta skapande frekvens för inkommande flöden|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Medelvärde|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inget|
|Längsta skapande frekvens för utgående flöden|Längsta skapande frekvens för utgående flöden|CountPerSecond|Medelvärde|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inget|
|Läsning av Premium data disk-cache|Läsning av Premium data disk-cache (för hands version)|Procent|Medelvärde|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Read missar i Premium data disk cache (för hands version)|Procent|Medelvärde|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Läsning av Premium OS-diskcachen|Inget|
|Read missar i Premium OS-diskcachen|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Read missar i Premium OS-diskcachen|Inget|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inget|
|Totalt nätverk|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inget|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|Processoranvändning|Antal|Medelvärde|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Minnesanvändning|Byte|Medelvärde|Total minnes användning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Mottagna nätverks byte per sekund|Byte|Medelvärde|Mottagna nätverks byte per sekund.|Inget|
|NetworkBytesTransmittedPerSecond|Överförda nätverks byte per sekund|Byte|Medelvärde|Överförda nätverks byte per sekund.|Inget|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalPullCount|Totalt antal hämtningar|Antal|Medelvärde|Totalt antal bild hämtningar|Inget|
|SuccessfulPullCount|Antal lyckade pull-överföringar|Antal|Medelvärde|Antal lyckade bild hämtningar|Inget|
|TotalPushCount|Totalt antal push-meddelanden|Antal|Medelvärde|Totalt antal push-meddelanden för avbildningar|Inget|
|SuccessfulPushCount|Antal lyckade push-meddelanden|Antal|Medelvärde|Antal lyckade avbildnings push-meddelanden|Inget|
|RunDuration|Körnings tid|Millisekunder|Totalt|Körnings tid i millisekunder|Inget|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Antal|Medelvärde|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Inget|
|kube_node_status_allocatable_memory_bytes|Total mängd tillgängligt minne i ett hanterat kluster|Byte|Medelvärde|Total mängd tillgängligt minne i ett hanterat kluster|Inget|
|kube_pod_status_ready|Antal poddar i klart läge|Antal|Medelvärde|Antal poddar i klart läge|namnrymd, Pod|
|kube_node_status_condition|Status för olika nod villkor|Antal|Medelvärde|Status för olika nod villkor|villkor, status, status2, nod|
|kube_pod_status_phase|Antal poddar per fas|Antal|Medelvärde|Antal poddar per fas|fas, namnrymd, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfullRequests|Slutförda förfrågningar|Antal|Totalt|Lyckade förfrågningar gjorda av den anpassade providern|HttpMethod, CallPath, StatusCode|
|FailedRequests|Misslyckade begäranden|Antal|Totalt|Hämtar de tillgängliga loggarna för anpassade resurs leverantörer|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|NICReadThroughput|Läs data flöde (nätverk)|BytesPerSecond|Medelvärde|Läs data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|NICWriteThroughput|Skriv data flöde (nätverk)|BytesPerSecond|Medelvärde|Skriv data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|CloudReadThroughputPerShare|Data flöde för moln hämtning (resurs)|BytesPerSecond|Medelvärde|Ladda ned data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|CloudUploadThroughputPerShare|Moln överförings data flöde (resurs)|BytesPerSecond|Medelvärde|Ladda upp data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|BytesUploadedToCloudPerShare|Överförda moln byte (resurs)|Byte|Medelvärde|Det totala antalet byte som överförs till Azure från en resurs under rapporterings perioden.|Dela|
|Enhet|Total kapacitet|Byte|Medelvärde|Total kapacitet|Inget|
|Availablecapacity;)|Tillgänglig kapacitet|Byte|Medelvärde|Tillgänglig kapacitet i byte under rapporterings perioden.|Inget|
|CloudUploadThroughput|Moln överförings data flöde|BytesPerSecond|Medelvärde|Moln överförings data flödet till Azure under rapporterings perioden.|Inget|
|CloudReadThroughput|Data flöde för moln hämtning|BytesPerSecond|Medelvärde|Molnet Ladda ned data flödet till Azure under rapporterings perioden.|Inget|
|BytesUploadedToCloud|Överförda moln byte (enhet)|Byte|Medelvärde|Det totala antalet byte som överförs till Azure från en enhet under rapporterings perioden.|Inget|
|HyperVVirtualProcessorUtilization|Edge Compute-procent CPU|Procent|Medelvärde|CPU-användning i procent|InstanceName|
|HyperVMemoryUtilization|Edge Compute-minnes användning|Procent|Medelvärde|Mängden RAM-minne som används|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft. DataCatalog/datacatalogs

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Till gångs distribution efter klassificering|Antal|Totalt|Anger antalet till gångar med en viss klassificering som tilldelas, d.v.s. de klassificeras med den etiketten.|Klassificering, källa|
|AssetDistributionByStorageType|Till gångs distribution efter lagrings typ|Antal|Totalt|Anger antalet till gångar med en viss lagrings typ.|StorageType|
|NumberOfAssetsWithClassifications|Antal till gångar med minst en klassificering|Antal|Medelvärde|Anger antalet till gångar med minst en etikett klassificering.|Inget|
|ScanCancelled|Skanningen avbröts|Antal|Totalt|Anger antalet avsökningar som har avbrutits.|Inget|
|ScanCompleted|Sökningen är klar|Antal|Totalt|Anger hur många genomsökningar som har slutförts.|Inget|
|ScanFailed|Sökningen misslyckades|Antal|Totalt|Anger antalet misslyckade genomsökningar.|Inget|
|ScanTimeTaken|Tids åtgång för genomsökning|Sekunder|Totalt|Anger den totala genomsöknings tiden i sekunder.|Inget|
|CatalogActiveUsers|Dagliga aktiva användare|Antal|Totalt|Antal aktiva användare varje dag|Inget|
|CatalogUsage|Användnings distribution per åtgärd|Antal|Totalt|Ange antalet åtgärds användare som gör till katalogen, t. ex. åtkomst, sökning, ord lista.|Åtgärd|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FailedRuns|Misslyckade körningar|Antal|Totalt||pipelineName, activityName|
|SuccessfulRuns|Lyckade körningar|Antal|Totalt||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
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
|IntegrationRuntimeCpuPercentage|PROCESSOR användning för integration runtime|Procent|Medelvärde||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAvailableMemory|Tillgängligt minne för integration runtime|Byte|Medelvärde||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAverageTaskPickupDelay|Varaktighet för integration runtime-kö|Sekunder|Medelvärde||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Kölängd för integration runtime|Antal|Medelvärde||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Antal tillgängliga noder för integration runtime|Antal|Medelvärde||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maximalt antal tillåtna entiteter|Antal|Maximal||Inget|
|MaxAllowedFactorySizeInGbUnits|Högsta tillåtna fabriks storlek (GB enhet)|Antal|Maximal||Inget|
|ResourceCount|Totalt antal entiteter|Antal|Maximal||Inget|
|FactorySizeInGbUnits|Total fabriks storlek (GB enhet)|Antal|Maximal||Inget|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobEndedSuccess|Slutförda jobb|Antal|Totalt|Antal lyckade jobb.|Inget|
|JobEndedFailure|Misslyckade jobb|Antal|Totalt|Antal misslyckade jobb.|Inget|
|JobEndedCancelled|Avbrutna jobb|Antal|Totalt|Antal avbrutna jobb.|Inget|
|JobAUEndedSuccess|Lyckad AU-tid|Sekunder|Totalt|Total AU-tid för lyckade jobb.|Inget|
|JobAUEndedFailure|Misslyckad AU-tid|Sekunder|Totalt|Total AU-tid för misslyckade jobb.|Inget|
|JobAUEndedCancelled|Tid för avbruten AU|Sekunder|Totalt|Total AU-tid för avbrutna jobb.|Inget|
|JobStage|Jobb i steg|Antal|Totalt|Antal jobb i varje steg.|Inget|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Byte|Maximal|Den totala mängden data som lagras i kontot.|Inget|
|DataWritten|Skrivna data|Byte|Totalt|Total mängd data som skrivs till kontot.|Inget|
|DataRead|Lästa data|Byte|Totalt|Total mängd data som lästs från kontot.|Inget|
|WriteRequests|Skriv förfrågningar|Antal|Totalt|Antal data skrivnings begär anden till kontot.|Inget|
|ReadRequests|Läs begär Anden|Antal|Totalt|Antal data läsnings begär anden till kontot.|Inget|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ShareCount|Skickade resurser|Antal|Maximal|Antal skickade resurser i kontot|Resurs|
|ShareSubscriptionCount|Mottagna resurser|Antal|Maximal|Antal mottagna resurser i kontot|ShareSubscriptionName|
|SucceededShareSynchronizations|Slutförda ögonblicks bilder av skickade resurser|Antal|Antal|Antal skickade delade resurser i kontot|Inget|
|FailedShareSynchronizations|Misslyckade ögonblicks bilder av skickade resurser|Antal|Antal|Antal skickade resurs misslyckade ögonblicks bilder i kontot|Inget|
|SucceededShareSubscriptionSynchronizations|Mottagna delade ögonblicks bilder|Antal|Antal|Antal mottagna delade ögonblicks bilder i kontot|Inget|
|FailedShareSubscriptionSynchronizations|Mottagna ögonblicks bilder av resurs misslyckades|Antal|Antal|Antal mottagna resurs misslyckade ögonblicks bilder i kontot|Inget|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inget|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inget|
|io_consumption_percent|I/o procent|Procent|Medelvärde|I/o procent|Inget|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inget|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inget|
|storage_limit|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inget|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medelvärde|Server logg lagrings procent|Inget|
|serverlog_storage_usage|Server logg lagring används|Byte|Medelvärde|Server logg lagring används|Inget|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Medelvärde|Server logg lagrings gräns|Inget|
|active_connections|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inget|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Medelvärde|Lagring av säkerhets kopior som används|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inget|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inget|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inget|
|io_consumption_percent|I/o procent|Procent|Medelvärde|I/o procent|Inget|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inget|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inget|
|storage_limit|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inget|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medelvärde|Server logg lagrings procent|Inget|
|serverlog_storage_usage|Server logg lagring används|Byte|Medelvärde|Server logg lagring används|Inget|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inget|
|active_connections|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inget|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Medelvärde|Lagring av säkerhets kopior som används|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inget|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inget|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inget|
|io_consumption_percent|I/o procent|Procent|Medelvärde|I/o procent|Inget|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inget|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inget|
|storage_limit|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inget|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Medelvärde|Server logg lagrings procent|Inget|
|serverlog_storage_usage|Server logg lagring används|Byte|Medelvärde|Server logg lagring används|Inget|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inget|
|active_connections|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Medelvärde|Lagring av säkerhets kopior som används|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inget|
|pg_replica_log_delay_in_seconds|Replik fördröjning|Sekunder|Maximal|Replik fördröjning i sekunder|Inget|
|pg_replica_log_delay_in_bytes|Maximal fördröjning mellan repliker|Byte|Maximal|Fördröjning i byte för den flesta isolerings repliken|Inget|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inget|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inget|
|IOPS|IOPS|Antal|Medelvärde|I/o-åtgärder per sekund|Inget|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inget|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inget|
|active_connections|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inget|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inget|
|memory_percent|Minnes procent|Procent|Medelvärde|Minnes procent|Inget|
|IOPS|IOPS|Antal|Medelvärde|I/o-åtgärder per sekund|Inget|
|storage_percent|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inget|
|storage_used|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inget|
|active_connections|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|connections_succeeded|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inget|
|maximum_used_transactionIDs|Högsta antal använda transaktions-ID: n|Antal|Medelvärde|Högsta antal använda transaktions-ID: n|Inget|





## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|D2C. telemetri. ingress. allProtocol|Skicka försök för telemetri|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inget|
|D2C. telemetri. ingress. lyckades|Meddelande om telemetri|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inget|
|C2D. commands. utgående. Complete. lyckades|C2D meddelande leveranser har slutförts|Antal|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Inget|
|C2D. commands. utgående. Abandon. lyckades|Övergivna C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Inget|
|C2D. commands. rekasta. Success|Avvisade C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inget|
|C2DMessagesExpired|C2D meddelanden har förfallit (förhands granskning)|Antal|Totalt|Antal utgångna meddelanden från moln till enhet|Inget|
|enheter. totalDevices|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inget|
|Devices. connectedDevices. allProtocol|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inget|
|D2C. telemetri. utgående. lyckades|Routning: telemetri meddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inget|
|D2C. telemetri. utgående.|Routning: telemetri ignoreras |Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inget|
|D2C. telemetri. utgående. överblivna|Routning: telemetri-meddelanden har överblivna |Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inget|
|D2C. telemetri. utgående. ogiltig|Routning: telemetri-meddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inget|
|D2C. telemetri. utgående. fallback|Routning: meddelanden levererade till reserv|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inget|
|D2C. endpoints. utgående. eventHubs|Routning: meddelanden levererade till Händelsehubben|Antal|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inget|
|D2C. endpoints. latens. eventHubs|Routning: meddelande fördröjning för Event Hub|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inget|
|D2C. endpoints. utgående. serviceBusQueues|Routning: meddelanden levererade till Service Bus kö|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inget|
|D2C. endpoints. latens. serviceBusQueues|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inget|
|D2C. endpoints. utgående. serviceBusTopics|Routning: meddelanden levererade till Service Bus ämnet|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inget|
|D2C. endpoints. latens. serviceBusTopics|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inget|
|D2C. endpoints. utgående. Builtity. events|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser).|Inget|
|D2C. endpoints. latens. Builtin. events|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser).|Inget|
|D2C. endpoints. utgående. Storage|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inget|
|D2C. endpoints. svars tid. Storage|Routning: meddelande fördröjning för lagring|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inget|
|D2C. endpoints. utgående. Storage. byte|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inget|
|D2C. endpoints. utgående. Storage. blob|Routning: blobbar levererade till lagring|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inget|
|EventGridDeliveries|Event Grid leveranser (för hands version)|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen ( https://aka.ms/ioteventgrid) .|ResourceId, resultat, EventType|
|EventGridLatency|Event Grid svars tid (för hands version)|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|ResourceId, EventType|
|RoutingDeliveries|Dirigera leveranser (för hands version)|Millisekunder|Totalt|Antalet gånger IoT Hub försökte leverera meddelanden till alla slut punkter med hjälp av routning. Använd resultat dimensionen för att se antalet lyckade eller misslyckade försök. Om du vill se orsaken till fel, som ogiltig, släppt eller föräldralös, använder du FailureReasonCategory-dimensionen. Du kan också använda dimensionerna EndpointName och EndpointType för att förstå hur många meddelanden som levererats till dina olika slut punkter. Mått värdet ökar med ett för varje leverans försök, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|ResourceId, EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Leverans svars tid för routning (för hands version)|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att förstå svars tiden för dina olika slut punkter.|ResourceId, EndpointType, EndpointName, RoutingSource|
|D2C. delad. lyckades|Lyckades dubbla läsningar från enheter|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inget|
|D2C. delad.|Misslyckade dubbla läsningar från enheter|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inget|
|D2C., delad. storlek|Svars storlek för dubbla läsningar från enheter|Byte|Medelvärde|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inget|
|D2C. delad. Update. lyckades|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|D2C. delad. Update. Failure|Misslyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|D2C. dubbla. Update. size|Storlek på dubbla uppdateringar från enheter|Byte|Medelvärde|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|C2D. Methods. Success|Direkta metod anrop|Antal|Totalt|Antalet lyckade direkta metod anrop.|Inget|
|C2D. Methods. Failure|Misslyckade direkta metod anrop|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Inget|
|C2D. Methods. requestSize|Begär ande storlek för direkta metod anrop|Byte|Medelvärde|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inget|
|C2D. Methods. responseSize|Svars storlek för direkta metod anrop|Byte|Medelvärde|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inget|
|C2D. delad. lyckades|Lyckades dubbla läspaket från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inget|
|C2D. delad.|Det gick inte att dubbla läsningar från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inget|
|C2D., delad. storlek|Svars storlek för dubbla läsningar från Server delen|Byte|Medelvärde|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inget|
|C2D. delad. Update. lyckades|Lyckades dubbla uppdateringar från Server delen|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inget|
|C2D. delad. Update. Failure|Misslyckade dubbla uppdateringar från Server delen|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inget|
|C2D. dubbla. Update. size|Storlek på dubbla uppdateringar från Server delen|Byte|Medelvärde|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inget|
|twinQueries. lyckades|Lyckades dubbla frågor|Antal|Totalt|Antalet lyckade dubbla frågor.|Inget|
|twinQueries. Failure|Misslyckade dubbla frågor|Antal|Totalt|Antalet misslyckade dubbla frågor.|Inget|
|twinQueries.resultSize|Resultat storlek för dubbla frågor|Byte|Medelvärde|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inget|
|Jobs. createTwinUpdateJob. lyckades|Skapandet av dubbla uppdaterings jobb lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inget|
|Jobs. createTwinUpdateJob. Failure|Det gick inte att skapa dubbla uppdaterings jobb|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inget|
|Jobs. createDirectMethodJob. lyckades|Lyckade skapande av metod anrops jobb|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inget|
|Jobs. createDirectMethodJob. Failure|Det gick inte att skapa metod anrops jobb|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inget|
|Jobs. listJobs. lyckades|Lyckade anrop till List jobb|Antal|Totalt|Antalet lyckade anrop till List jobb.|Inget|
|Jobs. listJobs. Failure|Misslyckade anrop till List jobb|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Inget|
|Jobs. cancelJob. lyckades|Slutförda jobb avbokningar|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inget|
|Jobs. cancelJob. Failure|Misslyckade jobb-annulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inget|
|Jobs. queryJobs. lyckades|Slutförda jobb frågor|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inget|
|Jobs. queryJobs. Failure|Misslyckade jobb frågor|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inget|
|jobb. slutfört|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inget|
|jobb. misslyckades|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inget|
|D2C. telemetri. ingress. sendThrottle|Antal begränsnings fel|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inget|
|dailyMessageQuotaUsed|Totalt antal meddelanden som används|Antal|Medelvärde|Antal sammanlagt antal meddelanden som använts idag|Inget|
|deviceDataUsage|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inget|
|deviceDataUsageV2|Total användning av enhets data (för hands version)|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inget|
|totalDeviceCount|Totalt antal enheter (förhands granskning)|Antal|Medelvärde|Antal enheter som har registrerats för din IoT-hubb|Inget|
|connectedDeviceCount|Anslutna enheter (förhands granskning)|Antal|Medelvärde|Antal enheter som är anslutna till din IoT-hubb|Inget|
|konfigurationer|Konfigurations mått|Antal|Totalt|Mått för konfigurations åtgärder|Inget|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Registrerings försök|Antal|Totalt|Antal enhets registreringar som försökts|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Tilldelade enheter|Antal|Totalt|Antal enheter som har tilldelats till en IoT-hubb|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Attesterings försök|Antal|Totalt|Antal försök till enhets attestering|ProvisioningServiceName, status, protokoll|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AddRegion|Region tillagt|Antal|Antal|Region tillagt|Region|
|AvailableStorage|Tillgängligt lagringsutrymme|Byte|Totalt|Totalt tillgängligt lagrings utrymme som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|CassandraConnectionClosures|Cassandra-anslutningens stängningar|Antal|Totalt|Antalet Cassandra-anslutningar som stängdes, rapporteras med en 1 minuts kornig het|APIType, region, ClosureReason|
|CassandraKeyspaceDelete|Cassandra-tecken utrymme borttaget|Antal|Antal|Cassandra-tecken utrymme borttaget|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraKeyspaceThroughputUpdate|Cassandra-dataflöde har uppdaterats|Antal|Antal|Cassandra-dataflöde har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra-disk utrymme uppdaterat|Antal|Antal|Cassandra-disk utrymme uppdaterat|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraRequestCharges|Avgifter för Cassandra-begäran|Antal|Totalt|Ru: er förbrukat för Cassandra begär Anden|APIType, DatabaseName, samlings region, OperationType, ResourceType|
|CassandraRequests|Cassandra-begäranden|Antal|Antal|Antal Cassandra-begäranden som gjorts|APIType, DatabaseName, samlings region, OperationType, ResourceType, ErrorCode|
|CassandraTableDelete|Cassandra-tabellen har tagits bort|Antal|Antal|Cassandra-tabellen har tagits bort|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|Cassandra tabell data flöde har uppdaterats|Antal|Antal|Cassandra tabell data flöde har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Cassandra-tabellen har uppdaterats|Antal|Antal|Cassandra-tabellen har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CreateAccount|Kontot har skapats|Antal|Antal|Kontot har skapats|Inget|
|DataUsage|Dataanvändning|Byte|Totalt|Total data användning rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|DeleteAccount|Kontot har tagits bort|Antal|Antal|Kontot har tagits bort|Inget|
|DocumentCount|Antal dokument|Antal|Totalt|Totalt antal dokument som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentQuota|Dokument kvot|Byte|Totalt|Total lagrings kvot som rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|GremlinDatabaseDelete|Gremlin-databasen har tagits bort|Antal|Antal|Gremlin-databasen har tagits bort|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinDatabaseThroughputUpdate|Gremlin Database-genomflöde har uppdaterats|Antal|Antal|Gremlin Database-genomflöde har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Gremlin-databasen har uppdaterats|Antal|Antal|Gremlin-databasen har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphDelete|Gremlin Graph borttagen|Antal|Antal|Gremlin Graph borttagen|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|Gremlin Graph-genomflöde har uppdaterats|Antal|Antal|Gremlin Graph-genomflöde har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Gremlin-diagrammet har uppdaterats|Antal|Antal|Gremlin-diagrammet har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|Indexanvändning|Byte|Totalt|Total användning av index rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|MetadataRequests|Begär Anden om metadata|Antal|Antal|Antal metadata-begäranden. Cosmos DB behåller systemets metadata-samling för varje konto, vilket gör att du kan räkna upp samlingar, databaser osv och deras konfigurationer kostnads fritt.|DatabaseName, samlings namn, region, StatusCode, roll|
|MongoCollectionDelete|Mongo-samlingen har tagits bort|Antal|Antal|Mongo-samlingen har tagits bort|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|Mongo Collection-genomflöde har uppdaterats|Antal|Antal|Mongo Collection-genomflöde har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Mongo-samlingen har uppdaterats|Antal|Antal|Mongo-samlingen har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Mongo-databasen har uppdaterats|Antal|Antal|Mongo-databasen har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDatabaseDelete|Mongo-databasen har tagits bort|Antal|Antal|Mongo-databasen har tagits bort|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoDatabaseThroughputUpdate|Mongo Database-genomflöde har uppdaterats|Antal|Antal|Mongo Database-genomflöde har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoRequestCharge|Mongo begär ande avgift|Antal|Totalt|Mongo enheter för förbrukad begäran|DatabaseName, samlings namn, region, CommandName, ErrorCode, status|
|MongoRequests|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden som gjorts|DatabaseName, samlings namn, region, CommandName, ErrorCode, status|
|MongoRequestsCount|Mongo begär ande frekvens|CountPerSecond|Medelvärde|Antal Mongo begär Anden per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsDelete|Mongo ta bort begär ande frekvens|CountPerSecond|Medelvärde|Mongo ta bort begäran per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsInsert|Mongo infoga begär ande frekvens|CountPerSecond|Medelvärde|Antal Mongo infogningar per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsQuery|Mongo för förfrågningar|CountPerSecond|Medelvärde|Mongo-förfrågan per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|MongoRequestsUpdate|Frekvens för mongo uppdaterings begär Anden|CountPerSecond|Medelvärde|Mongo för begär ande uppdatering per sekund|DatabaseName, samlings namn, region, CommandName, ErrorCode|
|NormalizedRUConsumption|Normaliserad RU-förbrukning|Procent|Maximal|Max procent per minut för RU-förbrukning|Samlings namn, DatabaseName, region|
|ProvisionedThroughput|Etablerat dataflöde|Antal|Maximal|Etablerat dataflöde|DatabaseName, samlings namn|
|RegionFailover|Regionen har redundansväxlats|Antal|Antal|Regionen har redundansväxlats|Inget|
|RemoveRegion|Region borttagen|Antal|Antal|Region borttagen|Region|
|ReplicationLatency|Fördröjning för P99-replikering|Millisekunder|Medelvärde|P99 för replikering i käll-och mål regioner för geo-aktiverat konto|SourceRegion,TargetRegion|
|ServerSideLatency|Svars tid på Server Sidan|Millisekunder|Medelvärde|Svars tid på Server Sidan|DatabaseName, samlings namn, region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Tjänst tillgänglighet|Procent|Medelvärde|Konto begär tillgänglighet på en timme, dag eller månads kornig het|Inget|
|SqlContainerDelete|SQL-behållare borttagen|Antal|Antal|SQL-behållare borttagen|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate|SQL container data flöde uppdaterat|Antal|Antal|SQL container data flöde uppdaterat|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|SQL-behållare har uppdaterats|Antal|Antal|SQL-behållare har uppdaterats|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete|SQL-databas borttagen|Antal|Antal|SQL-databas borttagen|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlDatabaseThroughputUpdate|SQL Database-dataflöde uppdaterat|Antal|Antal|SQL Database-dataflöde uppdaterat|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|SQL-databas uppdaterad|Antal|Antal|SQL-databas uppdaterad|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableDelete|AzureTable-tabellen har tagits bort|Antal|Antal|AzureTable-tabellen har tagits bort|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|TableTableThroughputUpdate|AzureTable tabell data flöde har uppdaterats|Antal|Antal|AzureTable tabell data flöde har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|AzureTable-tabellen har uppdaterats|Antal|Antal|AzureTable-tabellen har uppdaterats|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TotalRequestUnits|Totalt antal enheter för programbegäran|Antal|Totalt|Förbrukade enheter för begär Ande|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|TotalRequests|Totalt antal förfrågningar|Antal|Antal|Antal begär Anden som gjorts|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|UpdateAccountKeys|Konto nycklar har uppdaterats|Antal|Antal|Konto nycklar har uppdaterats|KeyType|
|UpdateAccountNetworkSettings|Kontots nätverks inställningar har uppdaterats|Antal|Antal|Kontots nätverks inställningar har uppdaterats|Inget|
|UpdateAccountReplicationSettings|Kontots replikeringsinställningar har uppdaterats|Antal|Antal|Kontots replikeringsinställningar har uppdaterats|Inget|
|UpdateDiagnosticsSettings|Inställningarna för konto diagnostik har uppdaterats|Antal|Antal|Inställningarna för konto diagnostik har uppdaterats|DiagnosticSettingsName, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/Services

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TransactionCount|Antal transaktioner|Antal|Antal|Totalt antal transaktioner|TransactionCount|
|SuccessCount|Antal lyckade|Antal|Antal|Antal genomförda transaktioner|SuccessCount|
|FailureCount|Antal misslyckade|Antal|Antal|Antal misslyckade transaktioner|FailureCount|
|SuccessLatency|Svars tid|Millisekunder|Medelvärde|Svars tid för lyckade transaktioner|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Avsnitt|
|PublishFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|Ämne, ErrorType, fel|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inget|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inget|
|PublishFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inget|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inget|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason,EventSubscriptionName|
|DeadLetteredCount|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inget|
|PublishFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inget|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inget|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason,EventSubscriptionName|
|DeadLetteredCount|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inget|
|DeliveryAttemptFailCount|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inget|
|DestinationProcessingDurationInMs|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|Inget|
|DroppedEventCount|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|DeadLetteredCount|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inget|
|PublishFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|UnmatchedEventCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inget|
|PublishSuccessLatencyInMs|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inget|




## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Slutförda förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|ServerErrors|Serverfel.|Antal|Totalt|Server fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|UserErrors|Användarfel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|QuotaExceededErrors|Fel på grund av överskriden kvot.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|ThrottledRequests|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|IncomingRequests|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Entitetsnamnet|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|OutgoingMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Entitetsnamnet|
|OutgoingBytes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Entitetsnamnet|
|ActiveConnections|ActiveConnections|Antal|Medelvärde|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inget|
|ConnectionsOpened|Öppna anslutningar.|Antal|Medelvärde|Anslutningar som har öppnats för Microsoft. EventHub.|Entitetsnamnet|
|ConnectionsClosed|Stängda anslutningar.|Antal|Medelvärde|Stängda anslutningar för Microsoft. EventHub.|Entitetsnamnet|
|CaptureBacklog|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Entitetsnamnet|
|CapturedMessages|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|CapturedBytes|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Entitetsnamnet|
|Storlek|Storlek|Byte|Medelvärde|Storlek på en EventHub i byte.|Entitetsnamnet|
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

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/Clusters

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Slutförda förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|OperationResult under pågående|
|ServerErrors|Serverfel.|Antal|Totalt|Server fel för Microsoft. EventHub.|OperationResult under pågående|
|UserErrors|Användarfel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|OperationResult under pågående|
|QuotaExceededErrors|Fel på grund av överskriden kvot.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|OperationResult under pågående|
|ThrottledRequests|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|OperationResult under pågående|
|IncomingRequests|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Inget|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Inget|
|OutgoingMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|Inget|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Inget|
|OutgoingBytes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Inget|
|ActiveConnections|ActiveConnections|Antal|Medelvärde|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inget|
|ConnectionsOpened|Öppna anslutningar.|Antal|Medelvärde|Anslutningar som har öppnats för Microsoft. EventHub.|Inget|
|ConnectionsClosed|Stängda anslutningar.|Antal|Medelvärde|Stängda anslutningar för Microsoft. EventHub.|Inget|
|CaptureBacklog|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Inget|
|CapturedMessages|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|Inget|
|CapturedBytes|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Inget|
|Processor|Processor|Procent|Maximal|CPU-användning för Event Hub-klustret i procent|Role|
|AvailableMemory|Tillgängligt minne|Procent|Maximal|Tillgängligt minne för Event Hub-klustret som en procent andel av det totala minnet.|Role|
|Storlek|Storlek på en EventHub i byte.|Byte|Medelvärde|Storlek på en EventHub i byte.|Role|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kluster

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden|HttpStatus|
|CategorizedGatewayRequests|Kategoriserade Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden per kategori (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Antal aktiva arbetare|Antal|Maximal|Antal aktiva arbetare|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observerat mått värde|Antal|Medelvärde|Det värde som beräknas genom autoskalning vid körning|MetricTriggerSource|
|MetricThreshold|Mätnings tröskel|Antal|Medelvärde|Det konfigurerade tröskelvärdet för autoskalning vid autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Observerad kapacitet|Antal|Medelvärde|Kapaciteten som rapporteras till autoskalning när den kördes.|Inget|
|ScaleActionsInitiated|Initierade skalnings åtgärder|Antal|Totalt|Riktningen för skalnings åtgärden.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Tillgänglighet|Procent|Medelvärde|Procent slutförda tillgänglighets test har slutförts|availabilityResult/namn, availabilityResult/plats|
|availabilityResults/antal|Tillgänglighetstester|Antal|Antal|Antal tillgänglighets test|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|availabilityResults/varaktighet|Tillgänglighets testets varaktighet|Millisekunder|Medelvärde|Tillgänglighets testets varaktighet|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|browserTimings/networkDuration|Nätverks anslutnings tid för sid inläsning|Millisekunder|Medelvärde|Tid mellan användar förfrågan och nätverks anslutning. Inkluderar DNS-sökning och transport anslutning.|Inget|
|browserTimings/processingDuration|Klient bearbetnings tid|Millisekunder|Medelvärde|Tiden mellan att ta emot sista byten i ett dokument tills DOM har lästs in. Asynkrona begär Anden kan fortfarande bearbetas.|Inget|
|browserTimings/receiveDuration|Tar emot svars tid|Millisekunder|Medelvärde|Tiden mellan den första och sista byten, eller till från koppling.|Inget|
|browserTimings/sendDuration|Tid för att skicka begäran|Millisekunder|Medelvärde|Tiden mellan nätverks anslutning och mottagande av den första byten.|Inget|
|browserTimings/totalDuration|Sid inläsnings tid för webbläsare|Millisekunder|Medelvärde|Tid från användar förfrågan tills DOM, formatmallar, skript och bilder har lästs in.|Inget|
|beroenden/antal|Beroende anrop|Antal|Antal|Antal anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/varaktighet|Beroende varaktighet|Millisekunder|Medelvärde|Varaktigheten för anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/misslyckades|Beroende anrops problem|Antal|Antal|Antal misslyckade beroende anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|pageViews/antal|Sid visningar|Antal|Antal|Antal sid visningar.|drift/syntetisk, Cloud/roleName|
|pageViews/varaktighet|Inläsnings tid för sid visning|Millisekunder|Medelvärde|Inläsnings tid för sid visning|drift/syntetisk, Cloud/roleName|
|performanceCounters/requestExecutionTime|Körnings tid för HTTP-begäran|Millisekunder|Medelvärde|Körnings tid för den senaste begäran.|Cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-begäranden i program kön|Antal|Medelvärde|Längden på program begär ande kön.|Cloud/roleInstance|
|performanceCounters/requestsPerSecond|Hastighet för HTTP-begäran|CountPerSecond|Medelvärde|Takten för alla förfrågningar till programmet per sekund från ASP.NET.|Cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Undantags frekvens|CountPerSecond|Medelvärde|Antal hanterade och ohanterade undantag som rapporter ATS till Windows, inklusive .NET-undantag och ohanterade undantag som konverterats till .NET-undantag.|Cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process-IO-hastighet|BytesPerSecond|Medelvärde|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|Cloud/roleInstance|
|performanceCounters/processCpuPercentage|Processor-CPU|Procent|Medelvärde|Procent andelen av förfluten tid som alla process trådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger enbart prestanda för W3wp-processen.|Cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor tid|Procent|Medelvärde|Den procent andel av tiden som processorn ägnat åt icke-inaktiva trådar.|Cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Tillgängligt minne|Byte|Medelvärde|Fysiskt minne som är omedelbart tillgängligt för allokering till en process eller för system användning.|Cloud/roleInstance|
|performanceCounters/processPrivateBytes|Privata byte för process|Byte|Medelvärde|Minne som tilldelats exklusivt för de övervakade program processerna.|Cloud/roleInstance|
|begär Anden/varaktighet|Server svars tid|Millisekunder|Medelvärde|Tiden mellan att ta emot en HTTP-förfrågan och avsluta sändningen av svaret.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/antal|Server begär Anden|Antal|Antal|Antal slutförda HTTP-förfrågningar.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/misslyckade|Misslyckade förfrågningar|Antal|Antal|Antal HTTP-begäranden som marker ATS som misslyckade. I de flesta fall är dessa förfrågningar med svars kod >= 400 och inte lika med 401.|begäran/performanceBucket, begäran/resultCode, begäran/framgång, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|begär Anden/pris|Server begär ande frekvens|CountPerSecond|Medelvärde|Antal server begär Anden per sekund|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|undantag/antal|Undantag|Antal|Antal|Sammanlagt antal undantag som inte har fångats.|Cloud/roleName, Cloud/roleInstance, Client/Type|
|undantag/webbläsare|Webbläsarundantag|Antal|Antal|Antal ej fångade undantag som har utlösts i webbläsaren.|klient-isServer, Cloud/roleName|
|undantag/Server|Server undantag|Antal|Antal|Antal ej fångade undantag som har utlösts i serverprogrammet.|klient-isServer, Cloud/roleName, Cloud/roleInstance|
|spårning/antal|Spårningar|Antal|Antal|Spårnings dokument antal|Trace/severityLevel, operation/syntetisk, Cloud/roleName, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedDeviceCount|Totalt antal anslutna enheter|Antal|Medelvärde|Antal enheter som är anslutna till IoT Central|Inget|
|C2D. Property. Read. lyckades|Lyckad enhets egenskaps läsning från IoT Central|Antal|Totalt|Antalet lyckade egenskaps läsningar som initierats från IoT Central|Inget|
|C2D. Property. Read. Failure|Det gick inte att läsa enhets egenskapen från IoT Central|Antal|Totalt|Antal misslyckade egenskaps läsningar som initierats från IoT Central|Inget|
|D2C. Property. Read. lyckades|Lyckad enhets egenskap läser från enheter|Antal|Totalt|Antalet lyckade egenskaps läsningar som initierats från enheter|Inget|
|D2C. Property. Read. Failure|Det gick inte att läsa enhets egenskapen från enheterna|Antal|Totalt|Antalet misslyckade egenskaps läsningar som initierats från enheter|Inget|
|C2D. Property. Update. lyckades|Lyckade uppdateringar av enhets egenskapen från IoT Central|Antal|Totalt|Antalet lyckade egenskaps uppdateringar som initierats från IoT Central|Inget|
|C2D. Property. Update. Failure|Det gick inte att uppdatera enhets egenskapen från IoT Central|Antal|Totalt|Antalet misslyckade egenskaps uppdateringar som initierats från IoT Central|Inget|
|D2C. Property. Update. lyckades|Lyckade uppdateringar av enhets egenskapen från enheter|Antal|Totalt|Antalet lyckade egenskaps uppdateringar som initierats från enheter|Inget|
|D2C. Property. Update. Failure|Misslyckade uppdateringar av enhets egenskapen från enheter|Antal|Totalt|Antalet misslyckade egenskaps uppdateringar som initierats från enheter|Inget|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Totalt antal tjänst-API-träffar|Antal|Antal|Antal totala service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Övergripande service API-latens|Millisekunder|Medelvärde|Övergripande svars tid för service API-begäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Totalt antal service API-resultat|Antal|Antal|Antal totala service API-resultat|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Övergripande valv beläggning|Procent|Medelvärde|Valv kapacitet som används|ActivityType, ActivityName, TransactionType|
|Tillgänglighet|Övergripande valv tillgänglighet|Procent|Medelvärde|Tillgänglighet för valv begär Anden|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CacheUtilization|Användning av cache|Procent|Medelvärde|Användnings nivå i kluster omfånget|Inget|
|QueryDuration|Frågans varaktighet|Millisekunder|Medelvärde|Frågornas varaktighet i sekunder|QueryStatus|
|IngestionUtilization|Förbruknings användning|Procent|Medelvärde|Förhållandet mellan använda inmatnings platser i klustret|Inget|
|KeepAlive|Behåll Alive|Antal|Medelvärde|Sanity check anger att klustret svarar på frågor|Inget|
|IngestionVolumeInMB|Inmatnings volym (i MB)|Antal|Totalt|Total mängd inmatade data till klustret (i MB)|Databasen|
|IngestionLatencyInSeconds|Inmatnings svars tid (i sekunder)|Sekunder|Medelvärde|Hämtnings tiden från källan (t. ex. meddelandet är i EventHub) till klustret på några sekunder|Inget|
|EventsProcessedForEventHubs|Bearbetade händelser (för Event/IoT-hubbar)|Antal|Totalt|Antal händelser som bearbetas av klustret vid inmatning från händelse/IoT Hub|EventStatus|
|IngestionResult|Inmatnings resultat|Antal|Antal|Antal inmatnings åtgärder|IngestionResultDetails|
|Processor|Processor|Procent|Medelvärde|PROCESSOR användnings nivå|Inget|
|ContinuousExportNumOfRecordsExported|Kontinuerlig export – antal exporterade poster|Antal|Totalt|Antal exporterade poster, utlöst för varje lagrings artefakt som skrivits under export åtgärden|ContinuousExportName, databas|
|ExportUtilization|Exportanvändning|Procent|Maximal|Exportera användning|Inget|
|ContinuousExportPendingCount|Antal väntande pågående export|Antal|Maximal|Antal väntande kontinuerliga export jobb som är klara för körning|Inget|
|ContinuousExportMaxLatenessMinutes|Kontinuerlig export, maximal försening|Antal|Maximal|Försenad (i minuter) som rapporteras av de kontinuerliga export jobben i klustret|Inget|
|ContinuousExportResult|Resultat av kontinuerlig export|Antal|Antal|Anger om kontinuerlig export lyckades eller misslyckades|ContinuousExportName, resultat, databas|
|StreamingIngestDuration|Hämtnings tid för strömning|Millisekunder|Medelvärde|Hämtnings tid för strömning i millisekunder|Inget|
|StreamingIngestDataRate|Data hastighet för strömnings intag|Antal|Medelvärde|Strömnings data hastighet (MB per sekund)|Inget|
|SteamingIngestRequestRate|Begär ande frekvens för strömning|Antal|RateRequestsPerSecond|Hastighet för strömnings inmatnings begäran (begär Anden per sekund)|Inget|
|StreamingIngestResults|Resultat av strömnings inmatning|Antal|Medelvärde|Resultat av strömnings inmatning|Resultat|
|TotalNumberOfConcurrentQueries|Totalt antal samtidiga frågor|Antal|Totalt|Totalt antal samtidiga frågor|Inget|
|TotalNumberOfThrottledQueries|Totalt antal begränsade frågor|Antal|Totalt|Totalt antal begränsade frågor|Inget|
|TotalNumberOfThrottledCommands|Totalt antal begränsade kommandon|Antal|Totalt|Totalt antal begränsade kommandon|CommandType|
|TotalNumberOfExtents|Totalt antal omfattningar|Antal|Totalt|Totalt antal data omfattningar|Inget|
|InstanceCount|Antal instanser|Antal|Medelvärde|Totalt antal instanser|Inget|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inget|
|RunsCompleted|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inget|
|RunsSucceeded|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inget|
|RunsFailed|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inget|
|RunsCancelled|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inget|
|RunLatency|Körnings fördröjning|Sekunder|Medelvärde|Svars tiden för slutförda arbets flödes körningar.|Inget|
|RunSuccessLatency|Kör svars tid|Sekunder|Medelvärde|Svars tiden för slutfört arbets flöde körs.|Inget|
|RunThrottledEvents|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inget|
|RunStartThrottledEvents|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inget|
|RunFailurePercentage|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inget|
|ActionsStarted|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inget|
|ActionsCompleted|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inget|
|ActionsSucceeded|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inget|
|ActionsFailed|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inget|
|ActionsSkipped|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inget|
|ActionLatency|Åtgärds svars tid |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inget|
|ActionSuccessLatency|Svars tid för åtgärd |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inget|
|ActionThrottledEvents|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inget|
|TriggersStarted|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inget|
|TriggersCompleted|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inget|
|TriggersSucceeded|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inget|
|TriggersFailed|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inget|
|TriggersSkipped|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inget|
|TriggersFired|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inget|
|TriggerLatency|Utlös fördröjning |Sekunder|Medelvärde|Latens för slutförda arbets flödes utlösare.|Inget|
|TriggerFireLatency|Utlös brand fördröjning |Sekunder|Medelvärde|Fördröjning för utlösta arbets flödes utlösare.|Inget|
|TriggerSuccessLatency|Utlös svars tid för lyckade |Sekunder|Medelvärde|Svars tid för lyckade arbets flödes utlösare.|Inget|
|TriggerThrottledEvents|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inget|
|BillableActionExecutions|Fakturerbara åtgärds körningar|Antal|Totalt|Antal körningar av arbets flödes åtgärder som faktureras.|Inget|
|BillableTriggerExecutions|Fakturerbara Utlösar-körningar|Antal|Totalt|Antal körningar av arbets flödes utlösare som faktureras.|Inget|
|TotalBillableExecutions|Totalt antal fakturerbara körningar|Antal|Totalt|Antal arbets flödes körningar som faktureras.|Inget|
|BillingUsageNativeOperation|Fakturerings användning för intern åtgärds körningar|Antal|Totalt|Antal körningar av interna åtgärder som debiteras.|Inget|
|BillingUsageStandardConnector|Fakturerings användning för standard kopplings körningar|Antal|Totalt|Antal standard kopplings körningar som faktureras.|Inget|
|BillingUsageStorageConsumption|Fakturerings användning för lagrings förbruknings körningar|Antal|Totalt|Antal lagrings förbruknings körningar som faktureras.|Inget|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inget|
|RunsCompleted|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inget|
|RunsSucceeded|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inget|
|RunsFailed|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inget|
|RunsCancelled|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inget|
|RunLatency|Körnings fördröjning|Sekunder|Medelvärde|Svars tiden för slutförda arbets flödes körningar.|Inget|
|RunSuccessLatency|Kör svars tid|Sekunder|Medelvärde|Svars tiden för slutfört arbets flöde körs.|Inget|
|RunThrottledEvents|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inget|
|RunStartThrottledEvents|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inget|
|RunFailurePercentage|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inget|
|ActionsStarted|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inget|
|ActionsCompleted|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inget|
|ActionsSucceeded|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inget|
|ActionsFailed|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inget|
|ActionsSkipped|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inget|
|ActionLatency|Åtgärds svars tid |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inget|
|ActionSuccessLatency|Svars tid för åtgärd |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inget|
|ActionThrottledEvents|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inget|
|TriggersStarted|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inget|
|TriggersCompleted|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inget|
|TriggersSucceeded|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inget|
|TriggersFailed|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inget|
|TriggersSkipped|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inget|
|TriggersFired|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inget|
|TriggerLatency|Utlös fördröjning |Sekunder|Medelvärde|Latens för slutförda arbets flödes utlösare.|Inget|
|TriggerFireLatency|Utlös brand fördröjning |Sekunder|Medelvärde|Fördröjning för utlösta arbets flödes utlösare.|Inget|
|TriggerSuccessLatency|Utlös svars tid för lyckade |Sekunder|Medelvärde|Svars tid för lyckade arbets flödes utlösare.|Inget|
|TriggerThrottledEvents|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inget|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Användning av arbets flödes processor för Integration Service Environment|Procent|Medelvärde|Användning av arbets flödes processor för integrerings tjänst miljö.|Inget|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Minnes användning för arbets flöde för Integration Service Environment|Procent|Medelvärde|Arbets flödets minnes användning för integrerings tjänst miljön.|Inget|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Anslutnings processor användning för Integration Service Environment|Procent|Medelvärde|Anslutnings processor användning för integrerings tjänst miljön.|Inget|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Anslutnings minnes användning för Integration Service Environment|Procent|Medelvärde|Anslutnings minnes användning för integrerings tjänst miljön.|Inget|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Avbrutna körningar|Avbrutna körningar|Antal|Totalt|Antal körningar som avbrutits för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Avbryt begärda körningar|Avbryt begärda körningar|Antal|Totalt|Antal körningar där Cancel begärdes för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Slutförda körningar|Slutförda körningar|Antal|Totalt|Antal körningar som har slutförts för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Misslyckade körningar|Misslyckade körningar|Antal|Totalt|Antalet körningar som misslyckades för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Slutför körningar|Slutför körningar|Antal|Totalt|Antal körningar som har angetts för avslutande tillstånd för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Svarar inte körningar|Svarar inte körningar|Antal|Totalt|Antal körningar som inte svarar för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Inte startade körningar|Inte startade körningar|Antal|Totalt|Antal körningar i läget har inte startats för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Förbereder körningar|Förbereder körningar|Antal|Totalt|Antal körningar som förbereds för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Etablerings körningar|Etablerings körningar|Antal|Totalt|Antal körningar som har etablering för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Köade körningar|Köade körningar|Antal|Totalt|Antal körningar som har placerats i kö för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Startade körningar|Startade körningar|Antal|Totalt|Antal körningar som har startats för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Startar körningar|Startar körningar|Antal|Totalt|Antal körningar som har startats för den här arbets ytan|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Fel|Fel|Antal|Totalt|Antal körnings fel i den här arbets ytan|Scenario|
|Varningar|Varningar|Antal|Totalt|Antal körnings varningar på den här arbets ytan|Scenario|
|Modell registreringen har slutförts|Modell registreringen har slutförts|Antal|Totalt|Antal modell registreringar som har slutförts på den här arbets ytan|Scenario|
|Modell registreringen misslyckades|Modell registreringen misslyckades|Antal|Totalt|Antal modell registreringar som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Modelldistribution Startad|Modelldistribution Startad|Antal|Totalt|Antal modell distributioner som startats på den här arbets ytan|Scenario|
|Modelldistribution lyckades|Modelldistribution lyckades|Antal|Totalt|Antal modell distributioner som har slutförts på den här arbets ytan|Scenario|
|Modelldistribution misslyckades|Modelldistribution misslyckades|Antal|Totalt|Antal modell distributioner som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Totalt antal noder|Totalt antal noder|Antal|Medelvärde|Totalt antal noder. Den här summan innehåller några aktiva noder, inaktiva noder, oanvändbara noder, Premepted noder, lämnar noder|Scenario, kluster namn|
|Aktiva noder|Aktiva noder|Antal|Medelvärde|Antal Active-noder. Detta är de noder som aktivt kör ett jobb.|Scenario, kluster namn|
|Inaktiva noder|Inaktiva noder|Antal|Medelvärde|Antal inaktiva noder. Inaktiva noder är noder som inte kör några jobb, men som kan ta emot nya jobb om de är tillgängliga.|Scenario, kluster namn|
|Oanvändbara noder|Oanvändbara noder|Antal|Medelvärde|Antal oanvändbara noder. Oanvändbara noder fungerar inte på grund av ett problem som inte kan matchas. De här noderna återanvänds av Azure.|Scenario, kluster namn|
|Misslyckade noder|Misslyckade noder|Antal|Medelvärde|Antal noder som har åsidosatts. De här noderna är de noder med låg prioritet som tas bort från den tillgängliga Node-poolen.|Scenario, kluster namn|
|Lämnar noder|Lämnar noder|Antal|Medelvärde|Antal noder som lämnar. Att lämna noder är de noder som precis har avslutat bearbetningen av ett jobb och kommer att gå in i inaktivt läge.|Scenario, kluster namn|
|Totalt antal kärnor|Totalt antal kärnor|Antal|Medelvärde|Antal total kärnor|Scenario, kluster namn|
|Aktiva kärnor|Aktiva kärnor|Antal|Medelvärde|Antal aktiva kärnor|Scenario, kluster namn|
|Inaktiva kärnor|Inaktiva kärnor|Antal|Medelvärde|Antal inaktiva kärnor|Scenario, kluster namn|
|Oanvändbara kärnor|Oanvändbara kärnor|Antal|Medelvärde|Antal oanvändbara kärnor|Scenario, kluster namn|
|Blockerade kärnor|Blockerade kärnor|Antal|Medelvärde|Antal blockerade kärnor|Scenario, kluster namn|
|Lämnar kärnor|Lämnar kärnor|Antal|Medelvärde|Antal lämnar kärnor|Scenario, kluster namn|
|Kvot användning i procent|Kvot användning i procent|Antal|Medelvärde|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Antal|Medelvärde|PROCESSOR (för hands version)|Scenario, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Antal|Medelvärde|GPU (för hands version)|Scenario, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Antal|Antal|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Tillgänglighet för API: erna|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Media Services/strömnings slut punkter

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte.|OutputFormat|
|SuccessE2ELatency|Slutför svars tid för slut punkt till slut punkt|Millisekunder|Medelvärde|Genomsnittlig svars tid för lyckade begär anden i millisekunder.|OutputFormat|
|Begäranden|Begäranden|Antal|Totalt|Begär anden till en slut punkt för direkt uppspelning.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AssetQuota|Till gångs kvot|Antal|Medelvärde|Hur många till gångar som tillåts för det aktuella Media Service-kontot|Inget|
|AssetCount|Antal till gångar|Antal|Medelvärde|Hur många till gångar som redan har skapats i det aktuella medie tjänst kontot|Inget|
|AssetQuotaUsedPercentage|Använd procent andel till till gångs kvot|Procent|Medelvärde|Procent andel till gång som används i det aktuella medie tjänst kontot|Inget|
|ContentKeyPolicyQuota|Kvot för innehålls nyckel princip|Antal|Medelvärde|Hur många innehålls nyckel principer som tillåts för det aktuella Media Service-kontot|Inget|
|ContentKeyPolicyCount|Antal nyckel principer för innehåll|Antal|Medelvärde|Hur många innehålls nyckel principer som redan har skapats i det aktuella medie tjänst kontot|Inget|
|ContentKeyPolicyQuotaUsedPercentage|Procent andel av nyckel princip för innehåll|Procent|Medelvärde|Procent andel av nyckel princip för innehåll i det aktuella medie tjänst kontot|Inget|
|StreamingPolicyQuota|Kvot för strömmande princip|Antal|Medelvärde|Hur många strömmande principer som tillåts för det aktuella Media Service-kontot|Inget|
|StreamingPolicyCount|Antal strömmande principer|Antal|Medelvärde|Hur många strömmande principer som redan har skapats i det aktuella medie tjänst kontot|Inget|
|StreamingPolicyQuotaUsedPercentage|Använd procent andel av princip för strömning|Procent|Medelvärde|Procentuell strömnings princip som används i det aktuella medie tjänst kontot|Inget|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AssetsConverted|Konverterade till gångar|Antal|Totalt|Totalt antal konverterade till gångar|AppId, ResourceId, SDKVersion|
|ActiveRenderingSessions|Aktiva åter givnings sessioner|Antal|Totalt|Totalt antal aktiva åter givnings sessioner|AppId, ResourceId, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageReadLatency|Genomsnittlig Läs fördröjning|Millisekunder|Medelvärde|Genomsnittlig Läs fördröjning i millisekunder per åtgärd|Inget|
|AverageWriteLatency|Genomsnittlig Skriv fördröjning|Millisekunder|Medelvärde|Genomsnittlig Skriv fördröjning i millisekunder per åtgärd|Inget|
|VolumeLogicalSize|Storlek på förbrukad volym|Byte|Medelvärde|Den logiska storleken på volymen (använda byte)|Inget|
|VolumeSnapshotSize|Storlek på volym ögonblicks bild|Byte|Medelvärde|Storlek på alla ögonblicks bilder i volymen|Inget|
|ReadIops|Läs IOPS|CountPerSecond|Medelvärde|Läs-/ut-åtgärder per sekund|Inget|
|WriteIops|Skriv IOPS|CountPerSecond|Medelvärde|Skriv in/ut-åtgärder per sekund|Inget|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Pool allokerad till volym storlek|Byte|Medelvärde|Allokerad använt storlek på poolen|Inget|
|VolumePoolTotalLogicalSize|Förbrukad pool storlek|Byte|Medelvärde|Summan av den logiska storleken för alla volymer som tillhör poolen|Inget|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesSentRate|Skickade byte|Byte|Totalt|Antal byte som nätverks gränssnittet har skickats|Inget|
|BytesReceivedRate|Mottagna byte|Byte|Totalt|Antal byte som nätverks gränssnittet har tagits emot|Inget|
|PacketsSentRate|Skickade paket|Antal|Totalt|Antal paket som nätverks gränssnittet har skickats|Inget|
|PacketsReceivedRate|Mottagna paket|Antal|Totalt|Antal paket som nätverks gränssnittet har fått|Inget|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|Tillgänglighet för data Sök väg|Antal|Medelvärde|Genomsnittlig tillgänglighet för Load Balancer data Sök väg per tids längd|FrontendIPAddress,FrontendPort|
|DipAvailability|Status för hälso avsökning|Antal|Medelvärde|Genomsnittlig status för Load Balancer hälso avsökning per tids period|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som skickats inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tids perioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allokerade SNAT-portar (förhands granskning)|Antal|Totalt|Totalt antal SNAT-portar som allokerats inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Använda SNAT-portar (förhands granskning)|Antal|Totalt|Totalt antal SNAT-portar som använts inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Fråga volym|Antal|Totalt|Antal frågor som hanteras för en DNS-zon|Inget|
|RecordSetCount|Antal post uppsättningar|Antal|Maximal|Antal post uppsättningar i en DNS-zon|Inget|
|RecordSetCapacityUtilization|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en DNS-zon|Inget|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS för inkommande paket|CountPerSecond|Maximal|DDoS för inkommande paket|Inget|
|PacketsDroppedDDoS|Inkommande paket som släppts DDoS|CountPerSecond|Maximal|Inkommande paket som släppts DDoS|Inget|
|PacketsForwardedDDoS|Vidarebefordrade inkommande paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande paket DDoS|Inget|
|TCPPacketsInDDoS|DDoS inkommande TCP-paket|CountPerSecond|Maximal|DDoS inkommande TCP-paket|Inget|
|TCPPacketsDroppedDDoS|Inkommande TCP-paket ignorerade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket ignorerade DDoS|Inget|
|TCPPacketsForwardedDDoS|Inkommande TCP-paket, vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket, vidarebefordrade DDoS|Inget|
|UDPPacketsInDDoS|DDoS för inkommande UDP-paket|CountPerSecond|Maximal|DDoS för inkommande UDP-paket|Inget|
|UDPPacketsDroppedDDoS|Ignorerade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Ignorerade inkommande UDP-paket DDoS|Inget|
|UDPPacketsForwardedDDoS|Vidarebefordrade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande UDP-paket DDoS|Inget|
|BytesInDDoS|DDoS för inkommande byte|BytesPerSecond|Maximal|DDoS för inkommande byte|Inget|
|BytesDroppedDDoS|Ignorerade inkommande byte DDoS|BytesPerSecond|Maximal|Ignorerade inkommande byte DDoS|Inget|
|BytesForwardedDDoS|Inkommande byte, vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande byte, vidarebefordrade DDoS|Inget|
|TCPBytesInDDoS|DDoS för inkommande TCP-byte|BytesPerSecond|Maximal|DDoS för inkommande TCP-byte|Inget|
|TCPBytesDroppedDDoS|Inkommande TCP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte utelämnade DDoS|Inget|
|TCPBytesForwardedDDoS|Inkommande TCP byte-vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande TCP byte-vidarebefordrade DDoS|Inget|
|UDPBytesInDDoS|DDoS för inkommande UDP-byte|BytesPerSecond|Maximal|DDoS för inkommande UDP-byte|Inget|
|UDPBytesDroppedDDoS|Inkommande UDP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte utelämnade DDoS|Inget|
|UDPBytesForwardedDDoS|Inkommande UDP byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande UDP byte vidarebefordrade DDoS|Inget|
|IfUnderDDoSAttack|Under DDoS-attack eller inte|Antal|Maximal|Under DDoS-attack eller inte|Inget|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-minskning|Inget|
|DDoSTriggerUDPPackets|Ingående UDP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Ingående UDP-paket för att utlösa DDoS-minskning|Inget|
|DDoSTriggerSYNPackets|Inkommande SYN paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande SYN paket för att utlösa DDoS-minskning|Inget|
|VipAvailability|Tillgänglighet för data Sök väg|Antal|Medelvärde|Genomsnittlig tillgänglighet för IP-adress per tids längd|Port|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal byte som skickats inom tids perioden|Port, riktning|
|PacketCount|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|Port, riktning|
|SynCount|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|Port, riktning|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Fördröjning för ping till en virtuell dator|Millisekunder|Medelvärde|Tur och retur-tid för pingar som skickas till en virtuell måldator|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Misslyckade pingar till en virtuell dator|Procent|Medelvärde|Procent av antalet misslyckade pingar till totalt antal skickade pingar för en virtuell måldator|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ApplicationRuleHit|Antal träffar för program regler|Antal|Totalt|Antal gånger som program regler träffades|Status, orsak, protokoll|
|NetworkRuleHit|Antal träffar för nätverks regler|Antal|Totalt|Antal gånger som nätverks regler träffades|Status, orsak, protokoll|
|FirewallHealth|Hälso tillstånd för brand vägg|Procent|Medelvärde|Hälso tillstånd för brand vägg|Status, orsak|
|DataProcessed|Bearbetade data|Byte|Totalt|Total mängd data som bearbetas av brand väggen|Inget|
|SNATPortUtilization|SNAT-port användning|Procent|Medelvärde|SNAT-port användning|Inget|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Medelvärde|Antal byte per sekund som Application Gateway har betjänat|Inget|
|UnhealthyHostCount|Antal felaktiga värdar|Antal|Medelvärde|Antal värdar för skadade Server delar|BackendSettingsPool|
|HealthyHostCount|Antal felfria värdar|Antal|Medelvärde|Antal felfria Server dels värdar|BackendSettingsPool|
|TotalRequests|Totalt antal förfrågningar|Antal|Totalt|Antal lyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Begär Anden per minut per felfri värd|Antal|Medelvärde|Genomsnittligt antal begär Anden per minut per felfri backend-värd i en pool|BackendSettingsPool|
|FailedRequests|Misslyckade begäranden|Antal|Totalt|Antal misslyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|ResponseStatus|Svars status|Antal|Totalt|Http-svarets status returnerades av Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inget|
|NewConnectionsPerSecond|Nya anslutningar per sekund|CountPerSecond|Medelvärde|Nya anslutningar per sekund som upprättats med Application Gateway|Inget|
|CpuUtilization|CPU-användning|Procent|Medelvärde|Aktuell processor användning för Application Gateway|Inget|
|CapacityUnits|Aktuella kapacitets enheter|Antal|Medelvärde|Förbrukade kapacitets enheter|Inget|
|FixedBillableCapacityUnits|Fasta fakturerbara kapacitetsenheter|Antal|Medelvärde|Lägsta kapacitets enhet som ska debiteras|Inget|
|EstimatedBilledCapacityUnits|Uppskattade enheter för fakturerings kapacitet|Antal|Medelvärde|Uppskattade kapacitets enheter som ska debiteras|Inget|
|ComputeUnits|Aktuella beräknings enheter|Antal|Medelvärde|Förbrukade beräknings enheter|Inget|
|BackendResponseStatus|Svars status för Server del|Antal|Totalt|Antalet HTTP-svars koder som genereras av Server dels medlemmar. Detta omfattar inte några svars koder som genereras av Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Klientens TLS-protokoll|Antal|Totalt|Antalet TLS-och icke-TLS-begäranden som initieras av klienten som upprättade anslutningen till Application Gateway. Om du vill visa TLS-protokollets distribution filtrerar du efter dimension TLS-protokollet.|Lyssnare, TlsProtocol|
|Bytes sent|Skickade byte|Byte|Totalt|Det totala antalet byte som har skickats av Application Gateway till klienterna|Lyssnare|
|BytesReceived|Mottagna byte|Byte|Totalt|Det totala antalet byte som tagits emot av Application Gateway från klienterna|Lyssnare|
|ClientRtt|Klient-/klient|Millisekunder|Medelvärde|Genomsnittlig fördröjning mellan klienter och Application Gateway. Det här måttet anger hur lång tid det tar att upprätta anslutningar och retur bekräftelser|Lyssnare|
|ApplicationGatewayTotalTime|Application Gateway total tid|Millisekunder|Medelvärde|Genomsnittlig tid det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som genomsnitt av intervallet från den tid då Application Gateway tar emot den första byten av en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att detta vanligt vis omfattar Application Gateway bearbetnings tid, tid då paket för begäran och svar överförs över nätverket och hur lång tid det tog att svara på backend-servern.|Lyssnare|
|BackendConnectTime|Server dels anslutnings tid|Millisekunder|Medelvärde|Åtgången tid för att upprätta en anslutning till en backend-server|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|Svars tid för första byte för Server del|Millisekunder|Medelvärde|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den första byten i svars huvudet, ungefär bearbetnings tiden för backend-servern|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|Svars tid för senaste byte för Server delen|Millisekunder|Medelvärde|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den sista byten i svars texten|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|MatchedCount|Brand vägg för webbaserade program v1 totalt regel distribution|Antal|Totalt|Brand vägg för webbaserade program v1 total regel distribution för inkommande trafik|RuleGroup, RuleId|
|BlockedCount|Distribution av brand vägg för webbaserade program v1 blockerade begär Anden|Antal|Totalt|Distribution av brand vägg för webbaserade program v1 blockerade begär Anden|RuleGroup, RuleId|
|BlockedReqCount|Antal blockerade begär Anden för brand vägg för webbaserade program|Antal|Totalt|Antal blockerade begär Anden för brand vägg för webbaserade program|Inget|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageBandwidth|Gatewayens S2S-bandbredd|BytesPerSecond|Medelvärde|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inget|
|P2SBandwidth|Gateway P2S-bandbredd|BytesPerSecond|Medelvärde|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inget|
|P2SConnectionCount|Antal P2S-anslutningar|Antal|Maximal|Antal anslutningar från punkt-till-plats för en gateway|Protokoll|
|TunnelAverageBandwidth|Tunnelbandbredd|BytesPerSecond|Medelvärde|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Utgående byte för tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Inkommande byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Utgående tunnelpaket|Antal|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Inkommande tunnel paket|Antal|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Utgående ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal utgående ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Inkommande ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal inkommande ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Antal|Medelvärde|RX ljus nivå i dBm|Länk, Lane|
|TxLightLevel|TxLightLevel|Antal|Medelvärde|TX-ljusnivå i dBm|Länk, Lane|
|AdminState|AdminState|Antal|Medelvärde|Administratörs tillstånd för porten|Länk|
|LineProtocol|LineProtocol|Antal|Medelvärde|Status för linje protokoll för porten|Länk|
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
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Medelvärde|Ingress BITS av data som ignoreras per sekund|Inget|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Medelvärde|Utgående bitar med data som ignoreras per sekund|Inget|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|Inget|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|Inget|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|Inget|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|Inget|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Returnerade frågor efter slut punkt|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den aktuella tids ramen|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Slut punkts status efter slut punkt|Antal|Maximal|1 om avsöknings status för en slut punkt är "aktive rad", annars 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Avsökningar misslyckades|Procent|Medelvärde|% av anslutnings övervaknings avsökningarna misslyckades|Inget|
|AverageRoundtripMs|Genomsnittlig tid för fördröjning (MS)|Millisekunder|Medelvärde|Genomsnittlig tid för nätverks fördröjning (MS) för anslutnings övervaknings avsökningar som skickas mellan källa och mål|Inget|
|ChecksFailedPercent|Misslyckade kontroller i procent (för hands version)|Procent|Medelvärde|% av kontrollerna för anslutnings övervakning misslyckades|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Tur och retur tid (MS) (för hands version)|Millisekunder|Medelvärde|Svars tid i millisekunder för kontrollerna för anslutnings övervakning|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RequestCount|Antal begäranden|Antal|Totalt|Antalet klient förfrågningar som hanteras av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Begär ande storlek|Byte|Totalt|Antalet byte som har skickats som begär Anden från klienter till HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Svars storlek|Byte|Totalt|Antalet byte som skickats som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Fakturerbart svars storlek|Byte|Totalt|Antalet fakturerbara byte (minsta 2KB per begäran) som skickats som svar från HTTP/S-proxy till klienter.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Antal Server dels begär Anden|Antal|Totalt|Antalet förfrågningar som skickats från HTTP/S-proxyn till Server delar|HttpStatus, HttpStatusGroup, Server del|
|BackendRequestLatency|Svars tid för Server del|Millisekunder|Medelvärde|Tiden som beräknas från när begäran skickades av HTTP/S-proxyn till Server delen tills HTTP/S-proxyn fick den senaste svars byten från Server delen|Serverdel|
|TotalLatency|Total svars tid|Millisekunder|Medelvärde|Den tid som beräknas från när klientbegäran togs emot av HTTP/S-proxy tills klienten bekräftade den senaste svars byten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Server delens hälso procent|Procent|Medelvärde|Procent andelen lyckade hälso avsökningar från HTTP/S-proxyn till Server delar|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Fråga volym|Antal|Totalt|Antal frågor som hanteras för en Privat DNS zon|Inget|
|RecordSetCount|Antal post uppsättningar|Antal|Maximal|Antal post uppsättningar i en Privat DNS zon|Inget|
|RecordSetCapacityUtilization|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en Privat DNS zon|Inget|
|VirtualNetworkLinkCount|Antal Virtual Network länkar|Antal|Maximal|Antal virtuella nätverk som är länkade till en Privat DNS zon|Inget|
|VirtualNetworkLinkCapacityUtilization|Virtual Network länka kapacitets användning|Procent|Maximal|Procent andel Virtual Network länk kapacitet som används av en Privat DNS zon|Inget|
|VirtualNetworkWithRegistrationLinkCount|Antal Virtual Network registrerings länkar|Antal|Maximal|Antal virtuella nätverk som är länkade till en Privat DNS zon med automatisk registrering aktive rad|Inget|
|VirtualNetworkWithRegistrationCapacityUtilization|Virtual Network kapacitets användning för registrerings länk|Procent|Maximal|Procent av Virtual Network-länk med automatisk registrerings kapacitet som används av en Privat DNS zon|Inget|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/Namespaces/NotificationHubs

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|registrering. alla|Registrerings åtgärder|Antal|Totalt|Antalet lyckade registrerings åtgärder (skapar uppdateringar av frågor och borttagningar). |Inget|
|registrering. skapa|Skapa åtgärder för registrering|Antal|Totalt|Antalet lyckade registreringar som skapas.|Inget|
|registrering. Update|Registrera uppdaterings åtgärder|Antal|Totalt|Antalet lyckade registrerings uppdateringar.|Inget|
|registrering. Hämta|Läs åtgärder för registrering|Antal|Totalt|Antalet lyckade registrerings frågor.|Inget|
|registrering. Delete|Åtgärder för att ta bort registrering|Antal|Totalt|Antalet lyckade registrerings borttagningar.|Inget|
|inkommande|Inkommande meddelanden|Antal|Totalt|Antalet lyckade sändnings-API-anrop. |Inget|
|inkommande. schemalagd|Schemalagda push-meddelanden har skickats|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inget|
|inkommande. schemalagd. Avbryt|Schemalagda push-meddelanden har avbrutits|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inget|
|schemalagt. väntar|Väntande schemalagda meddelanden|Antal|Totalt|Väntande schemalagda meddelanden|Inget|
|installation. alla|Installations hanterings åtgärder|Antal|Totalt|Installations hanterings åtgärder|Inget|
|installation. get|Hämta installations åtgärder|Antal|Totalt|Hämta installations åtgärder|Inget|
|installation. upsert|Skapa eller uppdatera installations åtgärder|Antal|Totalt|Skapa eller uppdatera installations åtgärder|Inget|
|installation. patch|Installation av korrigerings åtgärder|Antal|Totalt|Installation av korrigerings åtgärder|Inget|
|installation. Delete|Ta bort installations åtgärder|Antal|Totalt|Ta bort installations åtgärder|Inget|
|utgående. allpns. lyckades|Lyckade aviseringar|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|utgående. allpns. invalidpayload|Nytto Last fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS returnerade ett felaktigt nytto Last fel.|Inget|
|utgående. allpns. pnserror|Fel i externt meddelande system|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av ett problem med att kommunicera med PNS (exkluderar autentiseringsproblem).|Inget|
|utgående. allpns. channelerror|Kanal fel|Antal|Totalt|Antal push-meddelanden som inte har misslyckats på grund av att kanalen var ogiltig och som inte är associerad med rätt app-begränsning eller upphörde att gälla.|Inget|
|utgående. allpns. badorexpiredchannel|Dåliga eller utgångna kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom kanal/token/registrationId i registreringen har upphört att gälla eller är ogiltiga.|Inget|
|utgående. WNS. lyckades|WNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|utgående. WNS. invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras. (Windows Live känner inte igen autentiseringsuppgifterna).|Inget|
|utgående. WNS. badchannel|WNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inget|
|utgående. WNS. expiredchannel|WNS utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 borta).|Inget|
|utgående. WNS. begränsad|WNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom WNS begränsar den här appen (WNS-status: 406 är inte acceptabel).|Inget|
|utgående. WNS. tokenproviderunreachable|WNS-auktoriseringsfel (kan inte kontaktas)|Antal|Totalt|Windows Live är inte tillgängligt.|Inget|
|utgående. WNS. invalidtoken|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Den token som angetts för WNS är inte giltig (WNS-status: 401 otillåten).|Inget|
|utgående. WNS. wrongtoken|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS-status: 403 förbjuden). Detta kan inträffa om ChannelURI i registreringen är kopplad till en annan app. Kontrol lera att klient programmet är associerat med samma app vars autentiseringsuppgifter finns i Notification Hub.|Inget|
|utgående. WNS. invalidnotificationformat|WNS ogiltigt meddelande format|Antal|Totalt|Meddelandets format är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nytto laster.|Inget|
|utgående. WNS. invalidnotificationsize|WNS ogiltig meddelande storleks fel|Antal|Totalt|Meddelande nytto lasten är för stor (WNS-status: 413).|Inget|
|utgående. WNS. channelthrottled|WNS-kanalen är begränsad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-NotificationStatus: channelThrottled).|Inget|
|utgående. WNS. channeldisconnected|WNS-kanalen är frånkopplad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inget|
|utgående. WNS. släppt|WNS borttagna meddelanden|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: släppt men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inget|
|utgående. WNS. pnserror|WNS-fel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med WNS.|Inget|
|utgående. WNS. authenticationerror|WNS-autentiseringsfel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med ogiltiga Windows Live-autentiseringsuppgifter eller felaktigt token.|Inget|
|utgående. APN. lyckades|APN-lyckade meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|utgående. APN. invalidcredentials|APN-auktoriseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inget|
|utgående. APN. badchannel|APN dåligt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att token är ogiltig (APN-status kod: 8).|Inget|
|utgående. APN. expiredchannel|APN utgånget i kanal fel|Antal|Totalt|Antalet token som ogiltig förklarades av APNS-feedback-kanalen.|Inget|
|utgående. APN. invalidnotificationsize|APN ogiltigt meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (APN-status kod: 7).|Inget|
|utgående. APN. pnserror|APN-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med APN.|Inget|
|utgående. GCM. lyckades|GCM slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|utgående. GCM. invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inget|
|utgående. GCM. badchannel|GCM felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM-resultat: ogiltig registrering).|Inget|
|utgående. GCM. expiredchannel|GCM utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inget|
|utgående. GCM. begränsad|GCM-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom GCM begränsade till den här appen (GCM-status kod: 501-599 eller resultat: ej tillgängligt).|Inget|
|utgående. GCM. invalidnotificationformat|GCM ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten inte formaterades korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inget|
|utgående. GCM. invalidnotificationsize|GCM ogiltig meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (GCM-resultat: MessageTooBig).|Inget|
|utgående. GCM. wrongchannel|GCM fel kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte är kopplad till den aktuella appen (GCM result: InvalidPackageName).|Inget|
|utgående. GCM. pnserror|GCM-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med GCM.|Inget|
|utgående. GCM. authenticationerror|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna som autentiseringsuppgifterna är blockerade eller om SenderId inte har kon figurer ATS korrekt i appen (GCM result: MismatchedSenderId).|Inget|
|utgående. MPNS. lyckades|MPNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inget|
|utgående. MPNS. invalidcredentials|MPNS ogiltiga autentiseringsuppgifter|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inget|
|utgående. MPNS. badchannel|MPNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inget|
|utgående. MPNS. begränsad|MPNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom MPNS begränsar den här appen (WNS MPNS: 406 är inte acceptabel).|Inget|
|utgående. MPNS. invalidnotificationformat|MPNS ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten för meddelandet var för stor.|Inget|
|utgående. MPNS. channeldisconnected|MPNS-kanalen är frånkopplad|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att ChannelURI i registreringen var frånkopplad (MPNS status: 412 hittades inte).|Inget|
|utgående. MPNS. släppt|MPNS borttagna meddelanden|Antal|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svars huvud: X-NotificationStatus: QueueFull eller undertrycks).|Inget|
|utgående. MPNS. pnserror|MPNS-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med MPNS.|Inget|
|utgående. MPNS. authenticationerror|MPNS-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inget|
|notificationhub. push-meddelanden|Alla utgående meddelanden|Antal|Totalt|Alla utgående aviseringar för Notification Hub|Inget|
|inkommande. alla. förfrågningar|Alla inkommande begär Anden|Antal|Totalt|Totalt antal inkommande begär Anden för en Notification Hub|Inget|
|inkommande. alla. failedrequests|Alla inkommande misslyckade förfrågningar|Antal|Totalt|Totalt antal inkommande misslyckade begär Anden för en Notification Hub|Inget|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_ kostnads fri noder i procent|Kostnads fri noder i procent|Antal|Medelvärde|Average_ kostnads fri noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledigt utrymme i procent|Ledigt utrymme i procent|Antal|Medelvärde|Average_ ledigt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Använd noder i procent|% Använda noder i procent|Antal|Medelvärde|Average_% Använd noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt utrymme i procent|Använt utrymme i procent|Antal|Medelvärde|Average_ använt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/s|Disk-lästa byte/s|Antal|Medelvärde|Average_Disk lästa byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Disk läsningar/SEK|Antal|Medelvärde|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Disk överföringar/SEK|Antal|Medelvärde|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/s|Disk-skrivna byte/s|Antal|Medelvärde|Average_Disk skrivna byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Disk skrivningar/SEK|Antal|Medelvärde|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Lediga megabyte|Antal|Medelvärde|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk-byte/s|Logisk disk byte/SEK|Antal|Medelvärde|Average_Logical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt minne|Tillgängligt minne i procent|Antal|Medelvärde|Average_% tillgängligt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt växlings utrymme|Tillgängligt växlings utrymme i procent|Antal|Medelvärde|Average_% tillgängligt växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt minne|Använt minne i procent|Antal|Medelvärde|Average_% använt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt växlings utrymme i procent|Använt växlings utrymme i procent|Antal|Medelvärde|Average_ använt växlings utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB minne|Tillgängligt minne i megabyte|Antal|Medelvärde|Average_Available MB minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Växling vid Average_Available megabyte|Tillgängliga megabyte växlings utrymme|Antal|Medelvärde|Växling vid Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page läsningar/s|Sid läsningar/s|Antal|Medelvärde|Average_Page läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page skrivningar/SEK|Sid skrivningar/SEK|Antal|Medelvärde|Average_Page skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per sekund|Sidor/s|Antal|Medelvärde|Average_Pages per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlings utrymme|Använt megabyte växlings utrymme|Antal|Medelvärde|Average_Used megabyte växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i MB|Använt minne i MB|Antal|Medelvärde|Average_Used minne i MB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total byte|Totalt antal överförda byte|Antal|Medelvärde|Skickade Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna byte|Totalt antal mottagna byte|Antal|Medelvärde|Average_Total mottagna byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Totalt antal byte|Antal|Medelvärde|Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total paket|Totalt antal överförda paket|Antal|Medelvärde|Skickade Average_Total paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna paket|Totalt antal mottagna paket|Antal|Medelvärde|Average_Total mottagna paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total RX-fel|Totalt antal mottagna mottagnings fel|Antal|Medelvärde|Average_Total RX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-fel|Totalt antal TX-fel|Antal|Medelvärde|Average_Total TX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Totalt antal kollisioner|Antal|Medelvärde|Average_Total kollisioner|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/läsning|Medel s/disk läsning|Antal|Medelvärde|Average_Avg. Disk s/läsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/överföring|Medel s/disk överföring|Antal|Medelvärde|Average_Avg. Disk s/överföring|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/skrivning|Medel s/disk skrivning|Antal|Medelvärde|Average_Avg. Disk s/skrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk-byte/s|Fysisk disk-byte/SEK|Antal|Medelvärde|Average_Physical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegie rad tid|PCT privilegie rad tid|Antal|Medelvärde|Average_Pct privilegie rad tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användar tid|PCT-användar tid|Antal|Medelvärde|Average_Pct användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i KB|Använt minne i KB|Antal|Medelvärde|Average_Used minne i KB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Virtuellt delat minne|Antal|Medelvärde|Average_Virtual delat minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tid|DPC-tid i procent|Antal|Medelvärde|Average_% DPC-tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledig tid i procent|Inaktivitetstid i procent|Antal|Medelvärde|Average_ ledig tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Avbrotts tid i procent för Average_%|% Avbrotts tid|Antal|Medelvärde|Avbrotts tid i procent för Average_%|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% i/o-vänte tid|% I/o-vänte tid|Antal|Medelvärde|Average_% i/o-vänte tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% bra tid|% Trevligt tid|Antal|Medelvärde|Average_% bra tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ privilegie rad tid i procent|Privilegie rad tid i procent|Antal|Medelvärde|Average_ privilegie rad tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|% processortid|Antal|Medelvärde|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% användar tid|Användar tid i procent|Antal|Medelvärde|Average_% användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Ledigt fysiskt minne|Antal|Medelvärde|Average_Free fysiskt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Ledigt utrymme i växlingsfiler|Antal|Medelvärde|Average_Free utrymme i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Ledigt virtuellt minne|Antal|Medelvärde|Average_Free virtuellt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processer|Antal|Medelvärde|Average_Processes|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagras i växlingsfiler|Storlek lagrad i växlingsfiler|Antal|Medelvärde|Average_Size lagras i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Drift tid|Antal|Medelvärde|Average_Uptime|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Användare|Antal|Medelvärde|Average_Users|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Aktuell diskkölängd|Antal|Medelvärde|Average_Current diskkölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte|Tillgängliga megabyte|Antal|Medelvärde|Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% allokerade byte som används|% Allokerade byte som används|Antal|Medelvärde|Average_% allokerade byte som används|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes mottagna/SEK|Mottagna byte/SEK|Antal|Medelvärde|Average_Bytes mottagna/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Bytes per sekund|Skickade byte/SEK|Antal|Medelvärde|Skickade Average_Bytes per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/SEK|Totalt antal byte/s|Antal|Medelvärde|Average_Bytes totalt/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Kölängd|Kölängd för processor|Antal|Medelvärde|Average_Processor Kölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pulsslag|Pulsslag|Antal|Totalt|Pulsslag|Dator, OSType, version, SourceComputerId|
|Uppdatera|Uppdatera|Antal|Medelvärde|Uppdatera|Dator, produkt, klassificering, UpdateState, valfri, godkänd|
|Händelse|Händelse|Antal|Medelvärde|Händelse|Källa, EventLog, dator, EventCategory, EventLevel, EventLevelName, EventID|

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
|QueryPoolJobQueueLength|Trådar: Kölängd för jobbkö|Antal|Medelvärde|Antal jobb i kön för trådpoolen för Query.|Inga dimensioner|
|qpu_high_utilization_metric|Hög användning av QPU|Antal|Totalt|QPU hög användning under den senaste minuten, 1 för hög QPU användning, annars 0|Inga dimensioner|
|memory_metric|Minne|Byte|Medelvärde|Memory. Intervallet 0-3 GB för a1, 0-5 GB för a2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Minnesförslöing|Procent|Medelvärde|Genomsnittligt minne nedskräpning.|Inga dimensioner|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/konton

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Till gångs distribution efter klassificering|Antal|Totalt|Anger antalet till gångar med en viss klassificering som tilldelas, d.v.s. de klassificeras med den etiketten.|Klassificering, källa, ResourceId|
|AssetDistributionByStorageType|Till gångs distribution efter lagrings typ|Antal|Totalt|Anger antalet till gångar med en viss lagrings typ.|StorageType, ResourceId|
|CatalogActiveUsers|Dagliga aktiva användare|Antal|Totalt|Antal aktiva användare varje dag|ResourceId|
|CatalogUsage|Användnings distribution per åtgärd|Antal|Totalt|Ange antalet åtgärds användare som gör till katalogen, t. ex. åtkomst, sökning, ord lista.|Åtgärd, ResourceId|
|NumberOfAssetsWithClassifications|Antal till gångar med minst en klassificering|Antal|Medelvärde|Anger antalet till gångar med minst en etikett klassificering.|ResourceId|
|ScanCancelled|Skanningen avbröts|Antal|Totalt|Anger antalet avsökningar som har avbrutits.|ResourceId|
|ScanCompleted|Sökningen är klar|Antal|Totalt|Anger hur många genomsökningar som har slutförts.|ResourceId|
|ScanFailed|Sökningen misslyckades|Antal|Totalt|Anger antalet misslyckade genomsökningar.|ResourceId|
|ScanTimeTaken|Tids åtgång för genomsökning|Sekunder|Totalt|Anger den totala genomsöknings tiden i sekunder.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ListenerConnections – lyckad|ListenerConnections – lyckad|Antal|Totalt|Lyckade ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections – ClientError|ListenerConnections – ClientError|Antal|Totalt|ClientError på ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections – ServerError|ListenerConnections – ServerError|Antal|Totalt|ServerError på ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections – lyckad|SenderConnections – lyckad|Antal|Totalt|Lyckade SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections – ClientError|SenderConnections – ClientError|Antal|Totalt|ClientError på SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections – ServerError|SenderConnections – ServerError|Antal|Totalt|ServerError på SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections – TotalRequests|ListenerConnections – TotalRequests|Antal|Totalt|Totalt ListenerConnections för Microsoft. Relay.|Entitetsnamnet|
|SenderConnections – TotalRequests|SenderConnections – TotalRequests|Antal|Totalt|Totalt antal SenderConnections-begäranden för Microsoft. Relay.|Entitetsnamnet|
|ActiveConnections|ActiveConnections|Antal|Totalt|Totalt ActiveConnections för Microsoft. Relay.|Entitetsnamnet|
|ActiveListeners|ActiveListeners|Antal|Totalt|Totalt ActiveListeners för Microsoft. Relay.|Entitetsnamnet|
|BytesTransferred|BytesTransferred|Antal|Totalt|Totalt BytesTransferred för Microsoft. Relay.|Entitetsnamnet|
|ListenerDisconnects|ListenerDisconnects|Antal|Totalt|Totalt ListenerDisconnects för Microsoft. Relay.|Entitetsnamnet|
|SenderDisconnects|SenderDisconnects|Antal|Totalt|Totalt SenderDisconnects för Microsoft. Relay.|Entitetsnamnet|


## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SearchLatency|Sök svars tid|Sekunder|Medelvärde|Genomsnittlig Sök fördröjning för Sök tjänsten|Inget|
|SearchQueriesPerSecond|Sök frågor per sekund|CountPerSecond|Medelvärde|Sök efter frågor per sekund för Sök tjänsten|Inget|
|ThrottledSearchQueriesPercentage|Begränsade Sök frågor i procent|Procent|Medelvärde|Procent andel Sök frågor som har begränsats för Sök tjänsten|Inget|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Slutförda förfrågningar|Antal|Totalt|Totalt antal slutförda begär Anden för ett namn område|EntityName, OperationResult under pågående|
|ServerErrors|Serverfel.|Antal|Totalt|Server fel för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|UserErrors|Användarfel.|Antal|Totalt|Användar fel för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|ThrottledRequests|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|IncomingRequests|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. Service Bus.|Entitetsnamnet|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|OutgoingMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|ActiveConnections|ActiveConnections|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft. Service Bus.|Inget|
|ConnectionsOpened|Öppna anslutningar.|Antal|Medelvärde|Anslutningar som öppnats för Microsoft. Service Bus.|Entitetsnamnet|
|ConnectionsClosed|Stängda anslutningar.|Antal|Medelvärde|Stängda anslutningar för Microsoft. Service Bus.|Entitetsnamnet|
|Storlek|Storlek|Byte|Medelvärde|Storlek på en kö/ett ämne i byte.|Entitetsnamnet|
|Meddelanden|Antal meddelanden i kö/ämne.|Antal|Medelvärde|Antal meddelanden i kö/ämne.|Entitetsnamnet|
|ActiveMessages|Antal aktiva meddelanden i en kö/ett ämne.|Antal|Medelvärde|Antal aktiva meddelanden i en kö/ett ämne.|Entitetsnamnet|
|DeadletteredMessages|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Antal|Medelvärde|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Entitetsnamnet|
|ScheduledMessages|Antal schemalagda meddelanden i en kö/ett ämne.|Antal|Medelvärde|Antal schemalagda meddelanden i en kö/ett ämne.|Entitetsnamnet|
|NamespaceCpuUsage|Processor|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd.|Replik|
|NamespaceMemoryUsage|Minnesanvändning|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd.|Replik|
|CPUXNS|PROCESSOR (inaktuell)|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd. Det här måttet är inaktuell. Använd processor måttet (NamespaceCpuUsage) i stället.|Replik|
|WSXNS|Minnes användning (inaktuell)|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd. Måttet är föråldrat. Använd minnes användnings måttet (NamespaceMemoryUsage) i stället.|Replik|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/program

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Antal|Medelvärde|CPU allokerad till den här behållaren i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Byte|Medelvärde|Minne som allokerats till den här behållaren i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Antal|Medelvärde|Faktisk processor användning i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Byte|Medelvärde|Faktisk minnes användning i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Medelvärde|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Medelvärde|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Antal|Medelvärde|Status för Service Fabric nätprogram|ApplicationName, status|
|ServiceStatus|ServiceStatus|Antal|Medelvärde|Hälso status för en tjänst i Service Fabric nätprogram|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Antal|Medelvärde|Hälso status för en tjänst replik i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName|
|Container status|Container status|Antal|Medelvärde|Status för behållaren i Service Fabric nätprogram|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Antal|Medelvärde|Antal omstarter för en behållare i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ConnectionCount|Antal anslutningar|Antal|Maximal|Mängden användar anslutning.|Slutpunkt|
|MessageCount|Antal meddelanden|Antal|Totalt|Den totala mängden meddelanden.|Inget|
|InboundTraffic|Inkommande trafik|Byte|Totalt|Inkommande trafik trafik för tjänsten|Inget|
|OutboundTraffic|Utgående trafik|Byte|Totalt|Utgående trafik för tjänsten|Inget|
|UserErrors|Användar fel|Procent|Maximal|Procent andel användar fel|Inget|
|SystemErrors|Systemfel|Procent|Maximal|Procent andelen system fel|Inget|



## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inget|
|physical_data_read_percent|Data IO-procent|Procent|Medelvärde|Data IO-procent|Inget|
|log_write_percent|Logg IO-procent|Procent|Medelvärde|Logg IO-procent. Ej tillämpligt för data lager.|Inget|
|dtu_consumption_percent|DTU-procent|Procent|Medelvärde|DTU-procent. Gäller för DTU-baserade databaser.|Inget|
|storage|Använt data utrymme|Byte|Maximal|Använt data utrymme. Ej tillämpligt för data lager.|Inget|
|connection_successful|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inget|
|connection_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|blocked_by_firewall|Blockerad av brand väggen|Antal|Totalt|Blockerad av brand väggen|Inget|
|hamn|Låsningar|Antal|Totalt|Låsningar. Ej tillämpligt för data lager.|Inget|
|storage_percent|Använt data utrymme i procent|Procent|Maximal|Data utrymme som används i procent. Ej tillämpligt för data lager eller storskaliga databaser.|Inget|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Procent|Medelvärde|Minnes intern OLTP-lagring i procent. Ej tillämpligt för data lager.|Inget|
|workers_percent|Arbetare i procent|Procent|Medelvärde|Arbetare i procent. Ej tillämpligt för data lager.|Inget|
|sessions_percent|Sessioner i procent|Procent|Medelvärde|Sessioner i procent. Ej tillämpligt för data lager.|Inget|
|dtu_limit|DTU-gräns|Antal|Medelvärde|DTU-gräns. Gäller för DTU-baserade databaser.|Inget|
|dtu_used|Använt DTU|Antal|Medelvärde|DTU används. Gäller för DTU-baserade databaser.|Inget|
|cpu_limit|PROCESSOR gräns|Antal|Medelvärde|PROCESSOR gräns. Gäller för vCore-baserade databaser.|Inget|
|cpu_used|Använd CPU|Antal|Medelvärde|Använd CPU. Gäller för vCore-baserade databaser.|Inget|
|dwu_limit|DWU-gräns|Antal|Maximal|DWU-gräns. Gäller endast för data lager.|Inget|
|dwu_consumption_percent|DWU procent|Procent|Maximal|DWU procent. Gäller endast för data lager.|Inget|
|dwu_used|DWU som används|Antal|Maximal|DWU som används. Gäller endast för data lager.|Inget|
|cache_hit_percent|Procent andel cacheträffar|Procent|Maximal|Procent av cacheträffar. Gäller endast för data lager.|Inget|
|cache_used_percent|Procent andel som används|Procent|Maximal|Procent andel som används. Gäller endast för data lager.|Inget|
|sqlserver_process_core_percent<sup>1</sup> |SQL Server process kärn procent|Procent|Maximal|PROCESSOR användning i procent för SQL Server processen, mätt av operativ systemet.|Inget|
|sqlserver_process_memory_percent<sup>1</sup> |SQL Server process minne i procent|Procent|Maximal|Minnes användnings procent för SQL Server processen, mätt av operativ systemet.|Inget|
|tempdb_data_size<sup>1</sup> |Data fil storlek i tempdb i KB|Antal|Maximal|Data fil storlek för tempdb i KB.|Inget|
|tempdb_log_size<sup>1</sup> |TempDB-logg fils storlek kilobyte|Antal|Maximal|TempDB-logg fils storlek kilobyte.|Inget|
|tempdb_log_used_percent<sup>1</sup> |Procent använt tempdb-logg|Procent|Maximal|TempDB procent logg används.|Inget|
|local_tempdb_usage_percent|Lokal tempdb-procent|Procent|Medelvärde|Lokal tempdb-procent. Gäller endast för data lager.|Inget|
|app_cpu_billed|App-CPU fakturerad|Antal|Totalt|App-CPU fakturerad. Gäller databaser utan server.|Inget|
|app_cpu_percent|CPU-procent för app|Procent|Medelvärde|CPU-procent för app. Gäller databaser utan server.|Inget|
|app_memory_percent|Minnes procent för appar|Procent|Medelvärde|Minnes procent för appar. Gäller databaser utan server.|Inget|
|allocated_data_storage|Allokerat data utrymme|Byte|Medelvärde|Allokerad data lagring. Ej tillämpligt för data lager.|Inget|
|memory_usage_percent|Minnes procent|Procent|Maximal|Minnes procent. Gäller endast för data lager.|Inget|
|dw_backup_size_gb|Data lagrings storlek|Antal|Totalt|Data lagrings storleken består av storleken på dina data och transaktions loggen. Måttet räknas till lagrings delen av din faktura. Gäller endast för data lager.|Inget|
|dw_snapshot_size_gb|Storlek för ögonblicks bild lagring|Antal|Totalt|Storlek för ögonblicks bild lagring är storleken på de stegvisa ändringar som fångas av ögonblicks bilder för att skapa användardefinierade och automatiska återställnings punkter. Måttet räknas till lagrings delen av din faktura. Gäller endast för data lager.|Inget|
|dw_geosnapshot_size_gb|Lagrings storlek för katastrof återställning|Antal|Totalt|Lagrings storleken för haveri beredskap visas som "haveri beredskaps lagring" på din faktura. Gäller endast för data lager.|Inget|
|wlg_allocation_relative_to_system_percent|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till hela systemet per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Allokering av arbets belastnings grupp per Cap-resurs procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till de angivna Cap-resurserna per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Aktiva frågor för arbets belastnings grupp|Antal|Totalt|Aktiva frågor inom arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Köade frågor i arbets belastnings gruppen|Antal|Totalt|Köade frågor i arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|active_queries|Aktiva frågor|Antal|Totalt|Aktiva frågor för alla arbets belastnings grupper. Gäller endast för data lager.|Inget|
|queued_queries|Köade frågor|Antal|Totalt|Köade frågor över alla arbets belastnings grupper. Gäller endast för data lager.|Inget|
|wlg_active_queries_timeouts|Fråge tids gränser för arbets belastnings grupp|Antal|Totalt|Frågor som har nått tids gränsen för arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Effektiv minsta resurs procent|Procent|Maximal|Lägsta procent andel av resurser som är reserverade och isolerade för arbets belastnings gruppen, med hänsyn till den lägsta Service nivån. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Effektiv tak resurs procent|Procent|Maximal|En hård gräns för procent andelen av resurser som tillåts för arbets belastnings gruppen, med hänsyn till den lägsta resurs procent som allokerats för andra arbets belastnings grupper. Gäller endast för data lager.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Lagrings storlek för fullständig säkerhets kopia|Byte|Maximal|Ackumulerad lagrings storlek för fullständig säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inget|
|diff_backup_size_bytes|Lagrings storlek för differentiell säkerhets kopia|Byte|Maximal|Kumulativ lagrings storlek för differentiell säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inget|
|log_backup_size_bytes|Logg lagrings storlek för logg säkerhets kopia|Byte|Maximal|Sammanlagd lagrings storlek för logg säkerhets kopia. Gäller för vCore-baserade och storskaliga databaser.|Inget|
|snapshot_backup_size_bytes|Lagrings storlek för ögonblicks bild säkerhets kopia|Byte|Maximal|Lagrings storlek för kumulativ ögonblicks bild. Gäller för storskaliga databaser.|Inget|
|base_blob_size_bytes|Bas för Blob Storage-storlek|Byte|Maximal|Bas för Blob Storage-storlek. Gäller för storskaliga databaser.|Inget|

<sup>1</sup> det här måttet är tillgängligt för databaser som använder vCore inköps modell med 2 virtuella kärnor och högre, eller 200 DTU och högre för DTU-baserade inköps modeller. 

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/Servers/elasticPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|Inget|
|database_cpu_percent|CPU-procent|Procent|Medelvärde|CPU-procent|DatabaseResourceId|
|physical_data_read_percent|Data IO-procent|Procent|Medelvärde|Data IO-procent|Inget|
|database_physical_data_read_percent|Data IO-procent|Procent|Medelvärde|Data IO-procent|DatabaseResourceId|
|log_write_percent|Logg IO-procent|Procent|Medelvärde|Logg IO-procent|Inget|
|database_log_write_percent|Logg IO-procent|Procent|Medelvärde|Logg IO-procent|DatabaseResourceId|
|dtu_consumption_percent|DTU-procent|Procent|Medelvärde|DTU-procent. Gäller för DTU-baserade elastiska pooler.|Inget|
|database_dtu_consumption_percent|DTU-procent|Procent|Medelvärde|DTU-procent|DatabaseResourceId|
|storage_percent|Använt data utrymme i procent|Procent|Medelvärde|Använt data utrymme i procent|Inget|
|workers_percent|Arbetare i procent|Procent|Medelvärde|Arbetare i procent|Inget|
|database_workers_percent|Arbetare i procent|Procent|Medelvärde|Arbetare i procent|DatabaseResourceId|
|sessions_percent|Sessioner i procent|Procent|Medelvärde|Sessioner i procent|Inget|
|database_sessions_percent|Sessioner i procent|Procent|Medelvärde|Sessioner i procent|DatabaseResourceId|
|eDTU_limit|eDTU-gräns|Antal|Medelvärde|eDTU-gräns. Gäller för DTU-baserade elastiska pooler.|Inget|
|storage_limit|Max storlek för data|Byte|Medelvärde|Max storlek för data|Inget|
|eDTU_used|eDTU använt|Antal|Medelvärde|eDTU används. Gäller för DTU-baserade elastiska pooler.|Inget|
|database_eDTU_used|eDTU använt|Antal|Medelvärde|eDTU använt|DatabaseResourceId|
|storage_used|Använt data utrymme|Byte|Medelvärde|Använt data utrymme|Inget|
|database_storage_used|Använt data utrymme|Byte|Medelvärde|Använt data utrymme|DatabaseResourceId|
|xtp_storage_percent|Minnes intern OLTP-lagring i procent|Procent|Medelvärde|Minnes intern OLTP-lagring i procent|Inget|
|cpu_limit|PROCESSOR gräns|Antal|Medelvärde|PROCESSOR gräns. Gäller vCore-baserade elastiska pooler.|Inget|
|database_cpu_limit|PROCESSOR gräns|Antal|Medelvärde|PROCESSOR gräns|DatabaseResourceId|
|cpu_used|Använd CPU|Antal|Medelvärde|Använd CPU. Gäller vCore-baserade elastiska pooler.|Inget|
|database_cpu_used|Använd CPU|Antal|Medelvärde|Använd CPU|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|SQL Server process kärn procent|Procent|Maximal|PROCESSOR användning i procent för SQL Server processen, mätt av operativ systemet. Gäller elastiska pooler. |Inget|
|sqlserver_process_memory_percent<sup>1</sup>|SQL Server process minne i procent|Procent|Maximal|Minnes användnings procent för SQL Server processen, mätt av operativ systemet. Gäller elastiska pooler. |Inget|
|tempdb_data_size<sup>1</sup>|Data fil storlek i tempdb i KB|Antal|Maximal|Data fil storlek för tempdb i KB.|Inget|
|tempdb_log_size<sup>1</sup>|TempDB-logg fils storlek kilobyte|Antal|Maximal|TempDB-logg fils storlek kilobyte. |Inget|
|tempdb_log_used_percent<sup>1</sup>|Procent använt tempdb-logg|Procent|Maximal|TempDB procent logg används.|Inget|
|allocated_data_storage|Allokerat data utrymme|Byte|Medelvärde|Allokerat data utrymme|Inget|
|database_allocated_data_storage|Allokerat data utrymme|Byte|Medelvärde|Allokerat data utrymme|DatabaseResourceId|
|allocated_data_storage_percent|Allokerat data utrymme i procent|Procent|Maximal|Allokerat data utrymme i procent|Inget|

<sup>1</sup> det här måttet är tillgängligt för databaser som använder vCore inköps modell med 2 virtuella kärnor och högre, eller 200 DTU och högre för DTU-baserade inköps modeller. 

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|virtual_core_count|Antal virtuella kärnor|Antal|Medelvärde|Antal virtuella kärnor|Inget|
|avg_cpu_percent|Genomsnittlig CPU-procent|Procent|Medelvärde|Genomsnittlig CPU-procent|Inget|
|reserved_storage_mb|Reserverat lagrings utrymme|Antal|Medelvärde|Reserverat lagrings utrymme|Inget|
|storage_space_used_mb|Använt lagrings utrymme|Antal|Medelvärde|Använt lagrings utrymme|Inget|
|io_requests|Antal IO-begäranden|Antal|Medelvärde|Antal IO-begäranden|Inget|
|io_bytes_read|Lästa IO-byte|Byte|Medelvärde|Lästa IO-byte|Inget|
|io_bytes_written|Skrivna IO-byte|Byte|Medelvärde|Skrivna IO-byte|Inget|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Använd kapacitet|Byte|Medelvärde|Kapacitet som används av konto|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|BLOB-antal|Antal|Medelvärde|Antalet BLOB i lagrings kontots Blob Service.|BlobType,-nivå|
|ContainerCount|Antal BLOB-behållare|Antal|Medelvärde|Antalet behållare i lagrings kontots Blob Service.|Inget|
|IndexCapacity|Index kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Tabell kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Table service i byte.|Inget|
|TableCount|Antal tabeller|Antal|Medelvärde|Antalet tabeller i lagrings kontots Table service.|Inget|
|TableEntityCount|Antal tabell enheter|Antal|Medelvärde|Antalet tabell enheter i lagrings kontots Table service.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|Fil kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|FileShare|
|FileCount|Antal filer|Antal|Medelvärde|Antalet filer i lagrings kontots fil tjänst.|FileShare|
|FileShareCount|Antal fil resurser|Antal|Medelvärde|Antalet fil resurser i lagrings kontots fil tjänst.|Inget|
|FileShareSnapshotCount|Antal ögonblicks bilder av fil resurs|Antal|Medelvärde|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|Storlek på fil resursens ögonblicks bild|Byte|Medelvärde|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|FileShareQuota|Fil resursens kvot storlek|Byte|Medelvärde|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Kös Kap kap.|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Kötjänst i byte.|Inget|
|QueueCount|Antal köer|Antal|Medelvärde|Antalet köer i lagrings kontots Kötjänst.|Inget|
|QueueMessageCount|Antal meddelanden i kö|Antal|Medelvärde|Ungefärligt antal köa meddelanden i lagrings kontots Kötjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/cacheminnen

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ClientIOPS|Totalt antal klient-IOPS|Antal|Medelvärde|Frekvensen av klient fil åtgärder som bearbetas av cachen.|Inget|
|ClientLatency|Genomsnittlig klient latens|Millisekunder|Medelvärde|Genomsnittlig svars tid för klient fil åtgärder till Storage cache.|Inget|
|ClientReadIOPS|Klient läsnings-IOPS|CountPerSecond|Medelvärde|Klient Läs åtgärder per sekund.|Inget|
|ClientReadThroughput|Genomsnittligt cache-läst data flöde|BytesPerSecond|Medelvärde|Överföringshastighet för klient läsnings data.|Inget|
|ClientWriteIOPS|Klient skrivnings-IOPS|CountPerSecond|Medelvärde|Klient skrivnings åtgärder per sekund.|Inget|
|ClientWriteThroughput|Genomsnittligt cacheminne Skriv data flöde|BytesPerSecond|Medelvärde|Data överförings takt för klient skrivning.|Inget|
|ClientMetadataReadIOPS|Lästa IOPS för klientens metadata|CountPerSecond|Medelvärde|Frekvensen av klient fil åtgärder som skickas till cachen, exklusive data läsningar som inte ändrar beständigt tillstånd.|Inget|
|ClientMetadataWriteIOPS|Klientens metadata Skriv IOPS|CountPerSecond|Medelvärde|Frekvensen av klient fil åtgärder som skickas till cachen, exklusive data skrivningar, som ändrar beständigt tillstånd.|Inget|
|ClientLockIOPS|Klient lås IOPS|CountPerSecond|Medelvärde|Klient fil låsnings åtgärder per sekund.|Inget|
|StorageTargetHealth|Lagrings mål hälsa|Antal|Medelvärde|Booleska resultat för anslutnings test mellan cache-och lagrings målen.|Inget|
|Drift tid|Drift tid|Antal|Medelvärde|Booleska resultat för anslutnings test mellan cache-och övervaknings systemet.|Inget|
|StorageTargetIOPS|Totalt antal StorageTarget IOPS|Antal|Medelvärde|Frekvensen för alla fil åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget Skriv IOPS|Antal|Medelvärde|Hastigheten för fil skrivnings åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
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
|ServerSyncSessionResult|Resultat av Sync-session|Antal|Medelvärde|Mått som loggar värdet 1 varje gång Server slut punkten slutför en Sync-session med moln slut punkten|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Återställnings storlek för moln nivå|Byte|Totalt|Storlek på data som återkallas|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Återkalla data flöde för moln nivå|BytesPerSecond|Medelvärde|Storlek på data återkallande data flöde|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Återställnings storlek för moln skikt per program|Byte|Totalt|Storlek på data som återkallas av program|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerHeartbeat|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ResourceUtilization|SU%-användning|Procent|Maximal|SU%-användning|LogicalName, PartitionId|
|InputEvents|Inmatade händelser|Antal|Totalt|Inmatade händelser|LogicalName, PartitionId|
|InputEventBytes|Inkommande händelse-byte|Byte|Totalt|Inkommande händelse-byte|LogicalName, PartitionId|
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

## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/arbets ytor

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|Pipelinen körs avslutad|Antal|Totalt|Antal Dirigerings pipelines som lyckades, misslyckades eller avbröts|Resultat, FailureType, pipeline|
|OrchestrationActivityRunsEnded|Aktivitets körningar avslutad|Antal|Totalt|Antalet Orchestration-aktiviteter som lyckades, misslyckades eller avbröts|Resultat, FailureType, aktivitet, ActivityType, pipeline|
|OrchestrationTriggersEnded|Slutförda utlösare|Antal|Totalt|Antal Dirigerings utlösare som lyckades, misslyckades eller avbröts|Resultat, FailureType, utlösare|
|SQLOnDemandLoginAttempts|Inloggnings försök|Antal|Totalt|Antal inloggnings försök som distribuerats eller misslyckats|Resultat|
|SQLOnDemandQueriesEnded|Avslutade frågor|Antal|Totalt|Antal frågor som har lyckats, misslyckats eller avbrutits|Resultat|
|SQLOnDemandQueryProcessedBytes|Bearbetade data|Byte|Totalt|Mängden data som bearbetas av frågor|Inget|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/arbets ytor/bigDataPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SparkJobsEnded|Avslutade program|Antal|Totalt|Antal avslutade program|JobType, JobResult|
|CoresCapacity|Kärnor kapacitet|Antal|Maximal|Kärnor kapacitet|Inget|
|MemoryCapacityGB|Minnes kapacitet (GB)|Antal|Maximal|Minnes kapacitet (GB)|Inget|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/arbets ytor/sqlPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DWULimit|DWU-gräns|Antal|Maximal|SQL-poolens service nivå mål|Inget|
|DWUUsed|DWU som används|Antal|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Uppmätt av DWU-gränsen * DWU procent|Inget|
|DWUUsedPercent|Procent Använd DWU|Procent|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Mäts genom att dra max värdet mellan CPU-procent och data IO-procent|Inget|
|ConnectionsBlockedByFirewall|Anslutningar blockerade av brand väggen|Antal|Totalt|Antal anslutningar blockeras av brand Väggs regler. Gå tillbaka till principer för åtkomst kontroll för SQL-poolen och övervaka dessa anslutningar om antalet är högt|Inget|
|AdaptiveCacheHitPercent|Procentuellt antal träffar i adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med värdet för cache träff i procent för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inget|
|AdaptiveCacheUsedPercent|Procentuell användning av adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med den cachelagrade procentens mått för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inget|
|LocalTempDBUsedPercent|Lokal tempdb Använd procent|Procent|Maximal|Lokal tempdb-användning över alla Compute-noder – värden genereras var femte minut|Inget|
|MemoryUsedPercent|Använt minne i procent|Procent|Maximal|Minnes användning för alla noder i SQL-poolen|Inget|
|Anslutningar|Anslutningar|Antal|Totalt|Totalt antal inloggningar till SQL-poolen|Resultat|
|WLGActiveQueries|Aktiva frågor för arbets belastnings grupp|Antal|Totalt|Aktiva frågor i arbets belastnings gruppen. Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet|IsUserDefined,WorkloadGroup|
|WLGActiveQueriesTimeouts|Fråge tids gränser för arbets belastnings grupp|Antal|Totalt|Frågor för arbets belastnings gruppen som har nått tids gränsen. Tids gränser för frågor som rapporteras av detta mått sker bara när frågan har börjat köras (den innehåller inte vänte tid på grund av låsning eller väntande resurs avbrott)|IsUserDefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Procent beläggningen av resurser i förhållande till hela systemet|IsUserDefined,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Allokering av arbets belastnings grupper efter max resurs procent|Procent|Maximal|Visar procent tilldelningen av resurser i förhållande till den effektiva Kap resurs procenten per arbets belastnings grupp. Detta mått ger arbets belastnings gruppens effektiva användning|IsUserDefined,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Effektiv tak resurs procent|Procent|Maximal|Den effektiva Kap resurs procenten för arbets belastnings gruppen. Om det finns andra arbets belastnings grupper med min_percentage_resource > 0 sänks effective_cap_percentage_resource proportionellt|IsUserDefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Effektiv minsta resurs procent|Procent|Minimum|Inställningen för lägsta procent andel av resursen som tillåts överväger Service nivån och inställningarna för arbets belastnings gruppen. Effektiv min_percentage_resource kan justeras högre på lägre service nivåer|IsUserDefined,WorkloadGroup|
|WLGQueuedQueries|Köade frågor i arbets belastnings gruppen|Antal|Totalt|Antal sammanställda begär anden i kö efter att max gränsen för samtidighet nåddes|IsUserDefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/miljöer

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inget|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inget|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från alla händelse källor|Inget|
|IngressStoredBytes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inget|
|IngressStoredEvents|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inget|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inget|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Medelvärde|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inget|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inget|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inget|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/miljöer/eventsources

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från händelse källan|Inget|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från händelse källan|Inget|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från händelse källan|Inget|
|IngressStoredBytes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inget|
|IngressStoredEvents|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inget|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inget|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Medelvärde|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inget|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inget|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inget|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk-lästa byte/s|BytesPerSecond|Medelvärde|Genomsnittligt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inget|
|DiskWriteBytesPerSecond|Disk-skrivna byte/s|BytesPerSecond|Medelvärde|Genomsnittligt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inget|
|Lästa byte på disk|Lästa byte på disk|Byte|Totalt|Totalt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inget|
|Disk-skrivna byte|Disk-skrivna byte|Byte|Totalt|Totalt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inget|
|DiskReadOperations|Disk Läs åtgärder|Antal|Totalt|Antalet Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inget|
|DiskWriteOperations|Disk skrivnings åtgärder|Antal|Totalt|Antalet i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inget|
|Disk Läs åtgärder/SEK|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Genomsnittligt antal Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inget|
|Disk skrivnings åtgärder/SEK|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Genomsnittligt antal i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inget|
|DiskReadLatency|Läs fördröjning för disk|Millisekunder|Medelvärde|Total Läs fördröjning. Summan av Läs fördröjningen för enheten och kärnan.|Inget|
|DiskWriteLatency|Skriv fördröjning för disk|Millisekunder|Medelvärde|Total Skriv fördröjning. Summan av Skriv fördröjningarna för enheten och kärnan.|Inget|
|NetworkInBytesPerSecond|Nätverk i byte/s|BytesPerSecond|Medelvärde|Genomsnittligt nätverks data flöde för mottagen trafik.|Inget|
|NetworkOutBytesPerSecond|Nätverks-out-byte/SEK|BytesPerSecond|Medelvärde|Genomsnittligt nätverks data flöde för överförd trafik.|Inget|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Totalt nätverks data flöde för mottagen trafik.|Inget|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Totalt nätverks data flöde för överförd trafik.|Inget|
|MemoryUsed|Använt minne|Byte|Medelvärde|Mängden dator minne som används av den virtuella datorn.|Inget|
|MemoryGranted|Beviljat minne|Byte|Medelvärde|Mängden minne som har beviljats för den virtuella datorn av värden. Minnet beviljas inte för värden förrän den är påslagen en gång och beviljat minne kan växlas ut eller visas borta om VMkernel behöver minnet.|Inget|
|MemoryActive|Aktivt minne|Byte|Medelvärde|Mängden minne som används av den virtuella datorn i det tidigare lilla tids fönstret. Detta är "true"-antalet hur mycket minne som den virtuella datorn för närvarande behöver. Ytterligare, oanvänt minne kan växlas ut eller visas utan att påverka gästens prestanda.|Inget|
|Processorprocentandel|Processorprocentandel|Procent|Medelvärde|PROCESSOR användningen. Det här värdet rapporteras med 100% som representerar alla processor kärnor i systemet. Till exempel är en 2-vägs virtuell dator som använder 50% av ett system med fyra kärnor fullständigt med två kärnor.|Inget|
|PercentageCpuReady|Procent andel CPU klar|Millisekunder|Totalt|Ready Time är den tid som väntar på att CPU: er ska bli tillgängliga under det tidigare uppdaterings intervallet.|Inget|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/Server grupper

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Procent|Medelvärde|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medelvärde|Minnes procent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Medelvärde|Diskkölängd|Instans|
|HttpQueueLength|Längd på http-kö|Antal|Medelvärde|Längd på http-kö|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|TcpSynSent|TCP-syn har skickats|Antal|Medelvärde|TCP-syn har skickats|Instans|
|TcpSynReceived|TCP-syn mottagen|Antal|Medelvärde|TCP-syn mottagen|Instans|
|TcpEstablished|TCP upprättad|Antal|Medelvärde|TCP upprättad|Instans|
|TcpFinWait1|TCP räntetrans wait 1|Antal|Medelvärde|TCP räntetrans wait 1|Instans|
|TcpFinWait2|TCP räntetrans wait 2|Antal|Medelvärde|TCP räntetrans wait 2|Instans|
|TcpClosing|TCP-stängning|Antal|Medelvärde|TCP-stängning|Instans|
|TcpCloseWait|Väntan på TCP-stängning|Antal|Medelvärde|Väntan på TCP-stängning|Instans|
|TcpLastAck|TCP-senaste ack|Antal|Medelvärde|TCP-senaste ack|Instans|
|TcpTimeWait|Väntan på TCP-tid|Antal|Medelvärde|Väntan på TCP-tid|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (exklusive funktioner) 

> [!NOTE]
> **Användning av fil system** är ett nytt mått som distribueras globalt, inga data förväntas om du inte har varit vit listas för privat för hands version.

> [!IMPORTANT]
> Den **genomsnittliga svars tiden** blir föråldrad för att undvika förvirring med mått agg regeringar. Använd **svars tid** som ersättning.

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
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
|MemoryWorkingSet|Minnes arbets mängd|Byte|Medelvärde|Minnes arbets mängd|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Byte|Medelvärde|Genomsnittlig arbets mängd för minne|Instans|
|HttpResponseTime|Svars tid|Sekunder|Totalt|Svars tid|Instans|
|AverageResponseTime|Genomsnittlig svars tid (inaktuell)|Sekunder|Medelvärde|Genomsnittlig svars tid|Instans|
|AppConnections|Anslutningar|Antal|Medelvärde|Anslutningar|Instans|
|Konsolindataobjekt|Antal referenser|Antal|Medelvärde|Antal referenser|Instans|
|Konversation|Antal trådar|Antal|Medelvärde|Antal trådar|Instans|
|PrivateBytes|Privata byte|Byte|Medelvärde|Privata byte|Instans|
|IoReadBytesPerSecond|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoOtherBytesPerSecond|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Begär anden i program kön|Antal|Medelvärde|Begär anden i program kön|Instans|
|CurrentAssemblies|Aktuella sammansättningar|Antal|Medelvärde|Aktuella sammansättningar|Instans|
|TotalAppDomains|Totalt antal app-domäner|Antal|Medelvärde|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal app-domäner som har inaktiverats|Antal|Medelvärde|Totalt antal app-domäner som har inaktiverats|Instans|
|Gen0Collections|Skräp insamling för gen 0|Antal|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Skräp insamling för gen 1|Antal|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Gen 2 skräp insamling|Antal|Totalt|Gen 2 skräp insamling|Instans|
|HealthCheckStatus|Hälso kontroll status|Antal|Medelvärde|Hälso kontroll status|Instans|
|FileSystemUsage|Fil system användning|Byte|Medelvärde|Fil system användning|Inget|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (Functions)

> [!NOTE]
> **Användning av fil system** är ett nytt mått som distribueras globalt, inga data förväntas om du inte har varit vit listas för privat för hands version.

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Data ut|Byte|Totalt|Data ut|Instans|
|Http5xx|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Minnes arbets mängd|Byte|Medelvärde|Minnes arbets mängd|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Byte|Medelvärde|Genomsnittlig arbets mängd för minne|Instans|
|FunctionExecutionUnits|Funktions körnings enheter|MB/millisekunder|Totalt|[Funktions körnings enheter](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instans|
|FunctionExecutionCount|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|PrivateBytes|Privata byte|Byte|Medelvärde|Privata byte|Instans|
|IoReadBytesPerSecond|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoOtherBytesPerSecond|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Begär anden i program kön|Antal|Medelvärde|Begär anden i program kön|Instans|
|CurrentAssemblies|Aktuella sammansättningar|Antal|Medelvärde|Aktuella sammansättningar|Instans|
|TotalAppDomains|Totalt antal app-domäner|Antal|Medelvärde|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal app-domäner som har inaktiverats|Antal|Medelvärde|Totalt antal app-domäner som har inaktiverats|Instans|
|Gen0Collections|Skräp insamling för gen 0|Antal|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Skräp insamling för gen 1|Antal|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Gen 2 skräp insamling|Antal|Totalt|Gen 2 skräp insamling|Instans|
|HealthCheckStatus|Hälso kontroll status|Antal|Medelvärde|Hälso kontroll status|Instans|
|FileSystemUsage|Fil system användning|Byte|Medelvärde|Fil system användning|Inget|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
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
|MemoryWorkingSet|Minnes arbets mängd|Byte|Medelvärde|Minnes arbets mängd|Instans|
|AverageMemoryWorkingSet|Genomsnittlig arbets mängd för minne|Byte|Medelvärde|Genomsnittlig arbets mängd för minne|Instans|
|AverageResponseTime|Genomsnittlig svars tid|Sekunder|Medelvärde|Genomsnittlig svars tid|Instans|
|HttpResponseTime|Svars tid|Sekunder|Medelvärde|Svars tid|Instans|
|FunctionExecutionUnits|Funktions körnings enheter|Antal|Totalt|Funktions körnings enheter|Instans|
|FunctionExecutionCount|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|AppConnections|Anslutningar|Antal|Medelvärde|Anslutningar|Instans|
|Konsolindataobjekt|Antal referenser|Antal|Medelvärde|Antal referenser|Instans|
|Konversation|Antal trådar|Antal|Medelvärde|Antal trådar|Instans|
|PrivateBytes|Privata byte|Byte|Medelvärde|Privata byte|Instans|
|IoReadBytesPerSecond|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoWriteBytesPerSecond|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoOtherBytesPerSecond|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteOperationsPerSecond|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|IoOtherOperationsPerSecond|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|RequestsInApplicationQueue|Begär anden i program kön|Antal|Medelvärde|Begär anden i program kön|Instans|
|CurrentAssemblies|Aktuella sammansättningar|Antal|Medelvärde|Aktuella sammansättningar|Instans|
|TotalAppDomains|Totalt antal app-domäner|Antal|Medelvärde|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Totalt antal app-domäner som har inaktiverats|Antal|Medelvärde|Totalt antal app-domäner som har inaktiverats|Instans|
|Gen0Collections|Skräp insamling för gen 0|Antal|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Skräp insamling för gen 1|Antal|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Gen 2 skräp insamling|Antal|Totalt|Gen 2 skräp insamling|Instans|
|HealthCheckStatus|Hälso kontroll status|Antal|Medelvärde|Hälso kontroll status|Instans|
|FileSystemUsage|Fil system användning|Byte|Medelvärde|Fil system användning|Inget|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data i|Byte|Totalt|Data i|Instans|
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
|AverageResponseTime|Genomsnittlig svars tid|Sekunder|Medelvärde|Genomsnittlig svars tid|Instans|
|CpuPercentage|CPU-procent|Procent|Medelvärde|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medelvärde|Minnes procent|Instans|
|DiskQueueLength|Diskkölängd|Antal|Medelvärde|Diskkölängd|Instans|
|HttpQueueLength|Längd på http-kö|Antal|Medelvärde|Längd på http-kö|Instans|
|ActiveRequests|Aktiva begär Anden|Antal|Totalt|Aktiva begär Anden|Instans|
|TotalFrontEnds|Totalt antal klient delar|Antal|Medelvärde|Totalt antal klient delar|Inget|
|SmallAppServicePlanInstances|Små App Service planera arbetare|Antal|Medelvärde|Små App Service planera arbetare|Inget|
|MediumAppServicePlanInstances|Medel App Service planera arbetare|Antal|Medelvärde|Medel App Service planera arbetare|Inget|
|LargeAppServicePlanInstances|Stora App Service planera arbetare|Antal|Medelvärde|Stora App Service planera arbetare|Inget|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Totalt antal arbetare|Antal|Medelvärde|Totalt antal arbetare|Inget|
|WorkersAvailable|Tillgängliga arbetare|Antal|Medelvärde|Tillgängliga arbetare|Inget|
|WorkersUsed|Använda arbetare|Antal|Medelvärde|Använda arbetare|Inget|
|CpuPercentage|CPU-procent|Procent|Medelvärde|CPU-procent|Instans|
|MemoryPercentage|Minnes procent|Procent|Medelvärde|Minnes procent|Instans|
## <a name="next-steps"></a>Nästa steg
* [Läs om mått i Azure Monitor](data-platform.md)
* [Skapa aviseringar för mått](alerts-overview.md)
* [Exportera mått till lagring, Event Hub eller Log Analytics](platform-logs-overview.md)

