---
title: Azure Monitor stöds mått efter resurstyp
description: Lista över tillgängliga mått för varje resurstyp med Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 03/17/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 79bae9712cea04425cc36414ec56fdddd4345eab
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586030"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor

> [!NOTE]
> Den här listan genereras till stor del automatiskt från REST-API:et för Azure Monitor Metrics. Alla ändringar som görs i denna lista via Github kan skrivas över utan förvarning. Kontakta författaren till den här artikeln om du vill ha mer information om hur du gör permanenta uppdateringar.

Azure Monitor innehåller flera sätt att interagera med mått, inklusive att kartlägga dem i portalen, komma åt dem via REST API eller fråga dem med PowerShell eller CLI. Nedan finns en fullständig lista över alla mått som för närvarande är tillgängliga med Azure Monitors måttpipeline. Andra mått kan vara tillgängliga i portalen eller med hjälp av äldre API:er. Den här listan nedan innehåller endast mått som är tillgängliga med den konsoliderade Azure Monitor-måttpipelinen. Måtten är ordnade efter namnområde. En lista över tjänster och namnområden som tillhör dem finns i [Resursleverantörer för Azure-tjänster](../../azure-resource-manager/management/azure-services-resource-providers.md). Om du vill fråga efter och komma åt dessa mått programmässigt använder du [api-versionen 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
> En lista över plattformsmått som kan exporteras via diagnostikinställningar finns i [den här artikeln](metrics-supported-export-diagnostic-settings.md).


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servrar

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|qpu_metric|QPU (på andra sätt)|Antal|Medel|QPU. Räckvidd 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|memory_metric|Minne|Byte|Medel|Memory. Räckvidd 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|private_bytes_metric|Privata byte|Byte|Medel|Privata byte.|ServerResourceType|
|virtual_bytes_metric|Virtuella byte|Byte|Medel|Virtuella byte.|ServerResourceType|
|Totalt Antal anslutningsanknlöningar|Totalt antal anslutningsbegäranden|Antal|Medel|Totalt antal anslutningsbegäranden. Det här är ankomster.|ServerResourceType|
|Lyckade anslutningarPerSec|Lyckade anslutningar per sekund|CountPerSecond|Medel|Hastighet för slutförda anslutningar.|ServerResourceType|
|TotalAnslutningsylar|Totalt antal anslutningsfel|Antal|Medel|Totalt antal misslyckade anslutningsförsök.|ServerResourceType|
|CurrentUserSessions|Aktuella användarsessioner|Antal|Medel|Aktuellt antal användarsessioner har upprättats.|ServerResourceType|
|QueryPoolBusyTråds|Upptagna trådar för frågepool|Antal|Medel|Antal upptagna trådar i frågetrådspoolen.|ServerResourceType|
|KommandoPoolJobQueueLength|Längd på jobbköen för kommandopool|Antal|Medel|Antal jobb i kön till kommandotrådspoolen.|ServerResourceType|
|BearbetningPoolJobQueueLength|Köns längd för bearbetning av pooljobb|Antal|Medel|Antal icke-I/O-jobb i kön till bearbetningstrådspoolen.|ServerResourceType|
|CurrentConnections|Anslutning: Aktuella anslutningar|Antal|Medel|Aktuellt antal etablerade klientanslutningar.|ServerResourceType|
|CleanerCurrentPrice|Minne: Renare nuvarande pris|Antal|Medel|Nuvarande pris på minne, $/byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Minne: Renare minne krympbar|Byte|Medel|Mängd minne, i byte, föremål för rensning av bakgrundstvätten.|ServerResourceType|
|CleanerMemoryNonshrinkable|Minne: Renare minne nonshrinkable|Byte|Medel|Mängden minne, i byte, som inte kan rensas av bakgrundstvätten.|ServerResourceType|
|Minnesupptring|Minne: Minnesanvändning|Byte|Medel|Minnesanvändning av serverprocessen som används för att beräkna renare minnespris. Lika med att motverka Process\PrivateBytes plus storleken på minnesmappade data, ignorera allt minne som mappades eller allokerades av xVelocity in-memory analytics engine (VertiPaq) utöver xVelocity-motorn Memory Limit.|ServerResourceType|
|MemoryLimitHard|Minne: Minnesgräns hårt|Byte|Medel|Gräns för hårt minne från konfigurationsfilen.|ServerResourceType|
|MinneLimitHög|Minne: Minnesgränsen hög|Byte|Medel|Hög minnesgräns, från konfigurationsfilen.|ServerResourceType|
|MemoryLimitLow|Minne: Minnesgränsen låg|Byte|Medel|Låg minnesgräns från konfigurationsfilen.|ServerResourceType|
|MinneLimitVertiPaq|Minne: Minnesgräns VertiPaq|Byte|Medel|In-memory limit, från konfigurationsfilen.|ServerResourceType|
|Kvot|Minne: Kvot|Byte|Medel|Aktuell minneskvot, i byte. Minneskvot kallas också minnesbidrag eller minnesreservation.|ServerResourceType|
|Kvotblockerad|Minne: Kvot blockerad|Antal|Medel|Aktuellt antal kvotbegäranden som blockeras tills andra minneskvoter frigörs.|ServerResourceType|
|VertiPaqNonpaged|Minne: VertiPaq Ej sidiga|Byte|Medel|Byte av minne låst i arbetsminnet för användning av minnesmotorn.|ServerResourceType|
|VertiPaqSidad|Minne: VertiPaq Sökte|Byte|Medel|Byte av sökt minne som används för minnesdata.|ServerResourceType|
|RadernaReadPerSec|Bearbetning: Rader som läses per sekund|CountPerSecond|Medel|Radhastigheten som läses från alla relationsdatabaser.|ServerResourceType|
|RaderKonverteradPerSec|Bearbetning: Rader konverterade per sekund|CountPerSecond|Medel|Radhastighet som konverterats under bearbetningen.|ServerResourceType|
|RaderSkrivnaPerSec|Bearbetning: Rader skrivna per sekund|CountPerSecond|Medel|Radhastighet som skrivs under bearbetningen.|ServerResourceType|
|CommandPoolBusyTråds|Trådar: Kommandopool upptagen trådar|Antal|Medel|Antal upptagna trådar i kommandotrådspoolen.|ServerResourceType|
|CommandPoolIdleThreads|Trådar: Kommandopool inaktiva trådar|Antal|Medel|Antal inaktiva trådar i kommandotrådspoolen.|ServerResourceType|
|LongParsingBusyTråds|Trådar: Lång tolkning upptagen trådar|Antal|Medel|Antal upptagna trådar i den långa tolkningstrådspoolen.|ServerResourceType|
|LongParsingIdleThreads|Trådar: Lång parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i den långa tolkningstrådspoolen.|ServerResourceType|
|LångParsingJobQueueLength|Trådar: Lång parsning av jobbkölängd|Antal|Medel|Antal jobb i kön för den långa tolkningstrådspoolen.|ServerResourceType|
|ProcessingPoolBusyIOJobTrådar|Trådar: Bearbetning pool upptagen I / O-jobb trådar|Antal|Medel|Antal trådar som kör I/O-jobb i bearbetningstrådspoolen.|ServerResourceType|
|ProcessingPoolBusyNonIOTråds|Trådar: Bearbetning pool upptagen icke-I /O trådar|Antal|Medel|Antal trådar som kör icke-I/O-jobb i bearbetningstrådspoolen.|ServerResourceType|
|BearbetningPoolIOJobQueueLength|Trådar: Bearbetning av i/o-jobbkölängd för pool|Antal|Medel|Antal I/O-jobb i kön till bearbetningstrådspoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobTrådar|Trådar: Bearbetning av tomgångstrådar för i/o-jobb för poolen|Antal|Medel|Antal inaktiva trådar för I/O-jobb i bearbetningstrådspoolen.|ServerResourceType|
|ProcessingPoolIdleNonIOTrådar|Trådar: Bearbetning av pool inaktiv icke-I/O-trådar|Antal|Medel|Antal inaktiva trådar i bearbetningstrådspoolen som är dedikerad till icke-I/O-jobb.|ServerResourceType|
|QueryPoolIdleTrådar|Trådar: Frågepool inaktiva trådar|Antal|Medel|Antal inaktiva trådar för I/O-jobb i bearbetningstrådspoolen.|ServerResourceType|
|FrågaPoolJobQueueLength|Trådar: Fråga pool jobbkö lengt|Antal|Medel|Antal jobb i kön till frågetrådspoolen.|ServerResourceType|
|ShortParsingBusyTråds|Trådar: Kort tolkning upptagen trådar|Antal|Medel|Antal upptagna trådar i den korta tolkningstrådspoolen.|ServerResourceType|
|ShortParsingIdleThreads|Trådar: Korta parsning inaktiva trådar|Antal|Medel|Antal inaktiva trådar i den korta tolkningstrådspoolen.|ServerResourceType|
|KortParsingJobQueueLength|Trådar: Kort parsning av jobbkölängd|Antal|Medel|Antal jobb i kön till den korta tolkningstrådspoolen.|ServerResourceType|
|memory_thrashing_metric|Minnesförslöing|Procent|Medel|Genomsnittligt minne stryk.|ServerResourceType|
|mashup_engine_qpu_metric|M Motor QPU|Antal|Medel|QPU användning av mashup motor processer|ServerResourceType|
|mashup_engine_memory_metric|M Motorminne|Byte|Medel|Minnesanvändning genom mashup motor processer|ServerResourceType|
|mashup_engine_private_bytes_metric|M Motor privata byte|Byte|Medel|Privat byte användning av mashup motor processer.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Virtuella byte för M-motor|Byte|Medel|Användning av virtuella byte av mashup-motorprocesser.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalRequests|Totalt antal gatewaybegäranden (inaktuella)|Antal|Totalt|Antal gatewaybegäranden - Använd mätvärde för begäran om flera dimensioner med dimensionen GatewayResponseCodeCategory i stället|Plats,Värdnamn|
|Lyckade Begärande|Lyckade gateway-begäranden (inaktuella)|Antal|Totalt|Antal lyckade gatewaybegäranden - Använd mätvärde för begäran om flera dimensioner med dimensionen GatewayResponseCodeCategory i stället|Plats,Värdnamn|
|Obehöriga begärande|Obehöriga gateway-begäranden (inaktuella)|Antal|Totalt|Antal obehöriga gatewaybegäranden - Använd mätvärde för flera dimensioner med dimensionen GatewayResponseCodeCategory i stället|Plats,Värdnamn|
|Misslyckade begärande|Misslyckade gateway-begäranden (inaktuell)|Antal|Totalt|Antal fel i gatewaybegäranden - Använd mätvärde för flera dimensioner med dimensionen GatewayResponseCodeCategory i stället|Plats,Värdnamn|
|Övriga Upplysningar|Andra gateway-begäranden (inaktuella)|Antal|Totalt|Antal andra gateway-begäranden - Använd mätvärde för begäran om flera dimensioner med dimensionen GatewayResponseCodeCategory i stället|Plats,Värdnamn|
|Varaktighet|Den totala varaktigheten för gateway-begäranden|Millisekunder|Medel|Total varaktighet för gateway-begäranden i millisekunder|Plats,Värdnamn|
|BackendDuration|Varaktighet för begäranden för bakåtsträvningar|Millisekunder|Medel|Varaktighet för backend-begäranden i millisekunder|Plats,Värdnamn|
|Kapacitet|Kapacitet|Procent|Medel|Utnyttjandemått för ApiManagement-tjänsten|Location|
|EventHubTotalEvents|Totalt antal EventHub-händelser|Antal|Totalt|Antal händelser som skickats till EventHub|Location|
|EventHubSuccessfulEvents|Lyckade EventHub-händelser|Antal|Totalt|Antal lyckade EventHub-händelser|Location|
|EventHubTotalFailedEvents|Misslyckade EventHub-händelser|Antal|Totalt|Antal misslyckade EventHub-händelser|Location|
|EventHubRejectedEvents|Avvisade EventHub-händelser|Antal|Totalt|Antal avvisade EventHub-händelser (felaktig konfiguration eller obehörig)|Location|
|EventHubThrottledEvents|Begränsade EventHub-händelser|Antal|Totalt|Antal begränsade EventHub-händelser|Location|
|EventHubTimedoutEvents|Tidsinnade EventHub-händelser|Antal|Totalt|Antal tidsinnade EventHub-händelser|Location|
|EventHubDroppedEvents|Händelser som tagits bort i Händelsehub|Antal|Totalt|Antal händelser som hoppas över på grund av att gränsen för köstorlek har uppnåtts|Location|
|EventHubTotalBytesSent|Storleken på EventHub-händelser|Byte|Totalt|Total storlek på EventHub-händelser i byte|Location|
|Begäranden|Begäranden|Antal|Totalt|Mått för gatewaybegäran med flera dimensioner|Plats,Värdnamn,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory|
|Nätverksanslutning|Nätverksanslutningsstatus för resurser (förhandsversion)|Antal|Totalt|Nätverksanslutningsstatus för beroende resurstyper från API Management-tjänsten|Plats,ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppKonfigurering/configurationStores

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Antal|Antal|Totalt antal inkommande http-begäranden.|Statuskod|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Antal|Medel|Svarstid på en http-begäran.|Statuskod|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlattform/Fjäder

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Procentandel av användningen av system-PROCESSOR|Procent|Medel|Den senaste cpu-användningen för hela systemet|AppName,Pod|
|AppCpuUsagePercentage|Procentandel av appens CPU-användning|Procent|Medel|Procentandel av app JVM CPU-användning|AppName,Pod|
|AppMemoryCommitted|Appminne tilldelas|Byte|Medel|Minne som tilldelats JVM i byte|AppName,Pod|
|AppMemoryUsed|Appminne används|Byte|Medel|Appminne som används i byte|AppName,Pod|
|AppMemoryMax (1993)|Max för appminne|Byte|Maximal|Den maximala mängden minne i byte som kan användas för minneshantering|AppName,Pod|
|MaxOldGenMemoryPoolBytes|Max tillgänglig datastorlek för gammal generation|Byte|Medel|Maxstorlek på den gamla generationens minnespool|AppName,Pod|
|OldGenMemoryPoolBytes|Datastorlek för gammal generering|Byte|Medel|Storleken på den gamla generationens minnespool efter en fullständig GC|AppName,Pod|
|OldGenPromotedBytes|Befordra till datastorlek för gammal generation|Byte|Maximal|Antal positiva ökningar i storleken på den gamla generationens minnespool före GC till efter GC|AppName,Pod|
|YoungGenPromotedBytes|Främja till young generation datastorlek|Byte|Maximal|Stegas för en ökning av storleken på den unga generationens minnespool efter en GC till före nästa|AppName,Pod|
|GCPauseTotalCount|Antal GC-pauser|Antal|Totalt|Antal GC-pauser|AppName,Pod|
|GCPauseTotalTime|GC Paus total tid|Millisekunder|Totalt|GC Paus total tid|AppName,Pod|
|TomcatSentBytes|Tomcat Totalt antal skickade byte|Byte|Totalt|Tomcat Totalt antal skickade byte|AppName,Pod|
|TomcatReceivedBytes|Tomcat totalt mottagna byte|Byte|Totalt|Tomcat totalt mottagna byte|AppName,Pod|
|TomcatRequestTotalTime|Totalt antal gånger för Tomcat-begäran|Millisekunder|Totalt|Totalt antal gånger för Tomcat-begäran|AppName,Pod|
|TomcatRequestTotalCount|Totalt antal antal för Tomcat-begäran|Antal|Totalt|Totalt antal antal för Tomcat-begäran|AppName,Pod|
|TomcatResponseAvgTime|Genomsnittlig tid för Tomcat-begäran|Millisekunder|Medel|Genomsnittlig tid för Tomcat-begäran|AppName,Pod|
|TomcatRequestMaxTime|Tomcat begäran max tid|Millisekunder|Maximal|Tomcat begäran max tid|AppName,Pod|
|TomcatErrorCount|Tomcat globalt fel|Antal|Totalt|Tomcat globalt fel|AppName,Pod|
|TomcatSessionActiveMaxCount|Tomcat-session Max aktivt antal|Antal|Totalt|Tomcat-session Max aktivt antal|AppName,Pod|
|TomcatSessionAliveMaxTime|Tomcat Session Max Alive Tid|Millisekunder|Maximal|Tomcat Session Max Alive Tid|AppName,Pod|
|TomcatSessionActiveCurrentCount|Tomcat session levande räkna|Antal|Totalt|Tomcat session levande räkna|AppName,Pod|
|TomcatSessionCreatedCount|Tomcat-session skapad räkning|Antal|Totalt|Tomcat-session skapad räkning|AppName,Pod|
|TomcatSessionExpiredCount|Tomcat-session har upphört att gälla|Antal|Totalt|Tomcat-session har upphört att gälla|AppName,Pod|
|TomcatSessionRejectedCount|Tomcat-session avvisad räkning|Antal|Totalt|Tomcat-session avvisad räkning|AppName,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationKonton

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotaltJob|Totalt antal jobb|Antal|Totalt|Det totala antalet arbetstillfällen|Runbook,Status|
|TotalUpdateDeploymentRuns|Antal körningar av uppdateringsdistributionen|Antal|Totalt|Totalt antal distributionskörningar för programuppdatering|SoftwareUpdateConfigurationName,Status|
|TotalUpdateDeploymentMachineRuns|Antal datorspecifika körningar av uppdateringsdistributionen|Antal|Totalt|Totalt antal distributionsmaskiner för programuppdatering körs i en distributionskörning för programuppdatering|SoftwareUpdateConfigurationName,Status,TargetComputer,SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchKonton

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CoreCount (olikartade)|Dedikerat kärnantal|Antal|Totalt|Totalt antal dedikerade kärnor i batchkontot|Inget|
|Totaltnr|Antal dedikerade nodar|Antal|Totalt|Totalt antal dedikerade noder i batchkontot|Inget|
|LågpriorityCoreCount|Lågt prioriterat kärnantal|Antal|Totalt|Totalt antal lågprioriterade kärnor i batchkontot|Inget|
|TotaltLågpriorityNodeCount|Antal nodrad med låg prioritet|Antal|Totalt|Totalt antal noder med låg prioritet i batchkontot|Inget|
|Skapanädland|Skapa antal noder|Antal|Totalt|Antal noder som skapas|Inget|
|StartNodeCount|Antal startnoder|Antal|Totalt|Antal noder som startar|Inget|
|Väntar påStartTaskNodeCount|Väntar på antal start-aktivitetsnoder|Antal|Totalt|Antal noder som väntar på att startuppgiften ska slutföras|Inget|
|StartTaskFailedNodeCount|Antal misslyckade noder för start av aktivitet|Antal|Totalt|Antal noder där startaktiviteten har misslyckats|Inget|
|IdleNodeCount|Antal inaktiv nod|Antal|Totalt|Antal inaktiva noder|Inget|
|OfflineNodeCount|Antal offlinenoder|Antal|Totalt|Antal offlinenoder|Inget|
|OmstartNodeCount|Omstart av antal noder|Antal|Totalt|Antal omstartsnoder|Inget|
|ReimagingNodeCount|Återskapa nodantal|Antal|Totalt|Antal omimaging noder|Inget|
|KörNodeCount|Köra nodantal|Antal|Totalt|Antal noder som körs|Inget|
|LämnarPoolNodeCount|Lämna antalet poolnoder|Antal|Totalt|Antal noder som lämnar poolen|Inget|
|OanvändarbartNodeCount|Oersättlig nodantal|Antal|Totalt|Antal oanvändbara noder|Inget|
|FöregripenNodeCount|Antal föregripna nod|Antal|Totalt|Antal föregripna noder|Inget|
|TaskStartEvent|Aktivitetsstarthändelser|Antal|Totalt|Totalt antal aktiviteter som har startat|poolId,jobId|
|TaskCompleteEvent|Aktiviteter som slutförts|Antal|Totalt|Totalt antal aktiviteter som har slutförts|poolId,jobId|
|TaskFailEvent|Händelser för att misslyckas med aktivitet|Antal|Totalt|Totalt antal aktiviteter som har slutförts i ett misslyckat tillstånd|poolId,jobId|
|PoolCreateEvent|Skapa händelser för pool|Antal|Totalt|Totalt antal pooler som har skapats|poolId (på)poolId|
|PoolResizeStartEvent|Pool Ändra storlek på starthändelser|Antal|Totalt|Totalt antal ändrar storlek på poolen som har startats|poolId (på)poolId|
|PoolResizeCompleteEvent|Pool Ändra storlek på slutförda händelser|Antal|Totalt|Totalt antal storleksstorlekar för poolen som har slutförts|poolId (på)poolId|
|PoolDeleteStartEvent|Starta starthändelser för poolborttagning|Antal|Totalt|Totalt antal poolborttagningar som har startat|poolId (på)poolId|
|PoolDeleteCompleteEvent|Pool Ta bort slutförda händelser|Antal|Totalt|Totalt antal poolborttagningar som har slutförts|poolId (på)poolId|
|JobDeleteCompleteEvent|Slutföra händelser för jobbborttagning|Antal|Totalt|Totalt antal jobb som har tagits bort.|jobId (jobbId)|
|JobDeleteStartEvent|Starthändelser för jobbborttagning|Antal|Totalt|Totalt antal jobb som har begärts att tas bort.|jobId (jobbId)|
|JobDisableCompleteEvent|Jobb inaktiverar slutförd händelser|Antal|Totalt|Totalt antal jobb som har inaktiverats.|jobId (jobbId)|
|JobbDisableStartevent|Jobb inaktivera starthändelser|Antal|Totalt|Totalt antal jobb som har begärts inaktiveras.|jobId (jobbId)|
|JobbstartEvent|Jobbstarthändelser|Antal|Totalt|Totalt antal jobb som har startats.|jobId (jobbId)|
|JobbtermineraKompileEvent|Slutföra händelser för jobb avsluta|Antal|Totalt|Totalt antal jobb som har avslutats.|jobId (jobbId)|
|JobbtermineraStartEvent|Starthändelser för jobb avsluta|Antal|Totalt|Totalt antal jobb som har begärts att avslutas.|jobId (jobbId)|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbetsytor

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Jobbet har skickats in|Jobbet har skickats in|Antal|Totalt|Antal inskickade jobb|Scenario,ClusterName|
|Jobbet har slutförts|Jobbet har slutförts|Antal|Totalt|Antal slutförda jobb|Scenario,ClusterName,ResultType|
|Totalt antal noder|Totalt antal noder|Antal|Medel|Antal totala noder|Scenario,ClusterName|
|Aktiva noder|Aktiva noder|Antal|Medel|Antal noder som körs|Scenario,ClusterName|
|Inaktiva noder|Inaktiva noder|Antal|Medel|Antal inaktiva noder|Scenario,ClusterName|
|Oersättliga noder|Oersättliga noder|Antal|Medel|Antal oanvändbara noder|Scenario,ClusterName|
|Föregripna noder|Föregripna noder|Antal|Medel|Antal föregripna noder|Scenario,ClusterName|
|Lämna noder|Lämna noder|Antal|Medel|Antal lämnande noder|Scenario,ClusterName|
|Totalt kärnor|Totalt kärnor|Antal|Medel|Antal totala kärnor|Scenario,ClusterName|
|Aktiva kärnor|Aktiva kärnor|Antal|Medel|Antal aktiva kärnor|Scenario,ClusterName|
|Inaktiva kärnor|Inaktiva kärnor|Antal|Medel|Antal inaktiva kärnor|Scenario,ClusterName|
|Oersättliga kärnor|Oersättliga kärnor|Antal|Medel|Antal oanvändbara kärnor|Scenario,ClusterName|
|Föregripna kärnor|Föregripna kärnor|Antal|Medel|Antal föregripna kärnor|Scenario,ClusterName|
|Lämna kärnor|Lämna kärnor|Antal|Medel|Antal lämnar kärnor|Scenario,ClusterName|
|Procent av kvotutnyttjande|Procent av kvotutnyttjande|Antal|Medel|Procent av kvoten utnyttjas|Scenario,ClusterName,VmFamilyName,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMedlem

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsagePercentageDouble|Procentandel av CPU-användningen|Procent|Maximal|Procentandel av CPU-användningen|Node|
|Minnesupptring|Minnesanvändning|Byte|Medel|Minnesanvändning|Node|
|MemoryLimit (minne)|Minnesgräns|Byte|Medel|Minnesgräns|Node|
|MinneAnvändarePercentageInDouble|Procentandel av minnesanvändningen|Procent|Medel|Procentandel av minnesanvändningen|Node|
|LagringAnvändare|Användning av lagring|Byte|Medel|Användning av lagring|Node|
|IOReadBytes|IO läs byte|Byte|Totalt|IO läs byte|Node|
|IOWriteBytes|IO skriv byte|Byte|Totalt|IO skriv byte|Node|
|AnslutningAccepterad|Godkända anslutningar|Antal|Totalt|Godkända anslutningar|Node|
|Anslutningshanterat|Hanterade anslutningar|Antal|Totalt|Hanterade anslutningar|Node|
|AnslutningActive|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Node|
|RequestHandled|Hanterade förfrågningar|Antal|Totalt|Hanterade förfrågningar|Node|
|Bearbetade Blockeringar|Bearbetade block|Antal|Totalt|Bearbetade block|Node|
|Bearbetade transaktioner|Bearbetade transaktioner|Antal|Totalt|Bearbetade transaktioner|Node|
|Väntande transaktioner|Väntande transaktioner|Antal|Medel|Väntande transaktioner|Node|
|Kötransaktioner|Transaktioner i kö|Antal|Medel|Transaktioner i kö|Node|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|anslutnaklienter|Anslutna klienter|Antal|Maximal||ShardId (ShardId)|
|totalcommandsprocessed|Totala operationer|Antal|Totalt||ShardId (ShardId)|
|cachehits|Cache träffar|Antal|Totalt||ShardId (ShardId)|
|cachemissar|Cache missar|Antal|Totalt||ShardId (ShardId)|
|cachemissrate|Cachemisshastighet|Procent|cachemissrate||ShardId (ShardId)|
|getcommands (fåcommands)|Får|Antal|Totalt||ShardId (ShardId)|
|setcommands (setcommands)|Uppsättningar|Antal|Totalt||ShardId (ShardId)|
|operationerPerSecond|Operationer per sekund|Antal|Maximal||ShardId (ShardId)|
|vräkas nycklar|Vräkta nycklar|Antal|Totalt||ShardId (ShardId)|
|totalnycklar|Totalt antal nycklar|Antal|Maximal||ShardId (ShardId)|
|utgångna nycklar|Utgångna nycklar|Antal|Totalt||ShardId (ShardId)|
|användsminne|Använt minne|Byte|Maximal||ShardId (ShardId)|
|usedmemorypercentage|Använd minnesprocent|Procent|Maximal||ShardId (ShardId)|
|usedmemoryRss|Använt minne RSS|Byte|Maximal||ShardId (ShardId)|
|serverLoad (serverLoad)|Serverinläsning|Procent|Maximal||ShardId (ShardId)|
|cacheSkriva|Cache skriva|BytesPerSecond|Maximal||ShardId (ShardId)|
|cacheLäs|Cache läsa|BytesPerSecond|Maximal||ShardId (ShardId)|
|procentProcessorTime|Processor|Procent|Maximal||ShardId (ShardId)|
|cacheLatens|Mikrosekonsekter för cachefördröjning (förhandsgranskning)|Antal|Medel||ShardId (ShardId)|
|fel|Fel|Antal|Maximal||ShardId,ErrorType|
|anslutnaklienter0|Anslutna klienter (fragment 0)|Antal|Maximal||Inget|
|totalcommandsprocessed0|Totalt antal operationer (Shard 0)|Antal|Totalt||Inget|
|cachehits0|Cache träffar (fragment 0)|Antal|Totalt||Inget|
|cachemisses0|Cache missar (Shard 0)|Antal|Totalt||Inget|
|getcommands0|Gets (Skärva 0)|Antal|Totalt||Inget|
|setcommands0|Uppsättningar (Fragment 0)|Antal|Totalt||Inget|
|operationerPerSecond0|Åtgärder per sekund (Shard 0)|Antal|Maximal||Inget|
|vräka nycklar0|Vräkta nycklar (Shard 0)|Antal|Totalt||Inget|
|totalnycklar0|Totalt antal nycklar (Shard 0)|Antal|Maximal||Inget|
|utgångna nycklar0|Utgångna nycklar (Shard 0)|Antal|Totalt||Inget|
|usedmemory0|Använt minne (fragment 0)|Byte|Maximal||Inget|
|usedmemoryRss0|Använt minne RSS (Shard 0)|Byte|Maximal||Inget|
|serverLoad0|Serverbelastning (Shard 0)|Procent|Maximal||Inget|
|cacheWrite0|Cache skriva (fragment 0)|BytesPerSecond|Maximal||Inget|
|cacheRead0|Cache läsa (Shard 0)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime0|CPU (Fragment 0)|Procent|Maximal||Inget|
|anslutnaklienter1|Anslutna klienter (fragment 1)|Antal|Maximal||Inget|
|totalcommandsprocessed1|Totalt antal operationer (Shard 1)|Antal|Totalt||Inget|
|cachehits1|Cache träffar (fragment 1)|Antal|Totalt||Inget|
|cachemisses1|Cache missar (Shard 1)|Antal|Totalt||Inget|
|getcommands1|Gets (Skärva 1)|Antal|Totalt||Inget|
|setcommands1 (setcommands1)|Uppsättningar (fragment 1)|Antal|Totalt||Inget|
|operationerPerSecond1|Åtgärder per sekund (Shard 1)|Antal|Maximal||Inget|
|vräkas nycklar1|Vräkta nycklar (Shard 1)|Antal|Totalt||Inget|
|totalnycklar1|Totalt antal nycklar (Shard 1)|Antal|Maximal||Inget|
|utgångnanycklar1|Utgångna nycklar (fragment 1)|Antal|Totalt||Inget|
|usedmemory1|Använt minne (fragment 1)|Byte|Maximal||Inget|
|usedmemoryRss1|Använt minne RSS (Shard 1)|Byte|Maximal||Inget|
|serverLoad1|Serverbelastning (Shard 1)|Procent|Maximal||Inget|
|cacheWrite1|Cache skriva (fragment 1)|BytesPerSecond|Maximal||Inget|
|cacheLäs1|Cache läsa (Shard 1)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime1|CPU (Fragment 1)|Procent|Maximal||Inget|
|anslutnaklienter2|Anslutna klienter (fragment 2)|Antal|Maximal||Inget|
|totalcommandsprocessed2|Totalt antal operationer (Shard 2)|Antal|Totalt||Inget|
|cachehits2|Cache träffar (fragment 2)|Antal|Totalt||Inget|
|cachemisses2|Cache missar (Shard 2)|Antal|Totalt||Inget|
|getcommands2|Gets (Skärva 2)|Antal|Totalt||Inget|
|setcommands2 (setcommands2)|Uppsättningar (Shard 2)|Antal|Totalt||Inget|
|operationerPerSecond2|Åtgärder per sekund (Shard 2)|Antal|Maximal||Inget|
|vräkas nycklar2|Vräkta nycklar (Shard 2)|Antal|Totalt||Inget|
|totalnycklar2|Totalt antal nycklar (Shard 2)|Antal|Maximal||Inget|
|utgångna nycklar2|Utgångna nycklar (Shard 2)|Antal|Totalt||Inget|
|usedmemory2|Använt minne (fragment 2)|Byte|Maximal||Inget|
|usedmemoryRss2|Använt minne RSS (Shard 2)|Byte|Maximal||Inget|
|serverLoad2|Serverbelastning (Shard 2)|Procent|Maximal||Inget|
|cacheWrite2|Cache skriva (fragment 2)|BytesPerSecond|Maximal||Inget|
|cacheLäs2|Cache läsa (Shard 2)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime2|CPU (Fragment 2)|Procent|Maximal||Inget|
|anslutnaklienter3|Anslutna klienter (Shard 3)|Antal|Maximal||Inget|
|totalcommandsprocessed3|Totalt antal operationer (Shard 3)|Antal|Totalt||Inget|
|cachehits3|Cache träffar (fragment 3)|Antal|Totalt||Inget|
|cachemisses3|Cache missar (Shard 3)|Antal|Totalt||Inget|
|getcommands3|Gets (Skärva 3)|Antal|Totalt||Inget|
|setcommands3 (setcommands3)|Uppsättningar (Shard 3)|Antal|Totalt||Inget|
|operationerPerSecond3|Åtgärder per sekund (Shard 3)|Antal|Maximal||Inget|
|vräkas nycklar3|Vräkta nycklar (Shard 3)|Antal|Totalt||Inget|
|totalnycklar3|Totalt antal nycklar (Shard 3)|Antal|Maximal||Inget|
|utgångnanycklar3|Utgångna nycklar (Shard 3)|Antal|Totalt||Inget|
|usedmemory3|Använt minne (fragment 3)|Byte|Maximal||Inget|
|usedmemoryRss3|Använt minne RSS (Shard 3)|Byte|Maximal||Inget|
|serverLoad3|Serverbelastning (Shard 3)|Procent|Maximal||Inget|
|cacheWrite3|Cache skriva (fragment 3)|BytesPerSecond|Maximal||Inget|
|cacheLäs3|Cache läsning (Shard 3)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime3|CPU (Shard 3)|Procent|Maximal||Inget|
|anslutnaklienter4|Anslutna klienter (Shard 4)|Antal|Maximal||Inget|
|totalcommandsprocessed4|Totalt antal operationer (Shard 4)|Antal|Totalt||Inget|
|cachehits4|Cache träffar (fragment 4)|Antal|Totalt||Inget|
|cachemisses4|Cache missar (Shard 4)|Antal|Totalt||Inget|
|getcommands4|Gets (Skärva 4)|Antal|Totalt||Inget|
|setcommands4 (setcommands4)|Uppsättningar (Shard 4)|Antal|Totalt||Inget|
|operationerPerSecond4|Åtgärder per sekund (Shard 4)|Antal|Maximal||Inget|
|vräkasnycklar4|Vräkta nycklar (Shard 4)|Antal|Totalt||Inget|
|totalnycklar4|Totalt antal nycklar (Shard 4)|Antal|Maximal||Inget|
|utgångna nycklar4|Utgångna nycklar (Shard 4)|Antal|Totalt||Inget|
|usedmemory4|Använt minne (fragment 4)|Byte|Maximal||Inget|
|usedmemoryRss4|Använt minne RSS (Shard 4)|Byte|Maximal||Inget|
|serverLoad4|Serverbelastning (Shard 4)|Procent|Maximal||Inget|
|cacheWrite4|Cache skriva (Shard 4)|BytesPerSecond|Maximal||Inget|
|cacheLäs4|Cache läsa (Shard 4)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime4|CPU (Shard 4)|Procent|Maximal||Inget|
|anslutnaklienter5|Anslutna klienter (Shard 5)|Antal|Maximal||Inget|
|totalcommandsprocessed5|Totalt antal operationer (Shard 5)|Antal|Totalt||Inget|
|cachehits5|Cache träffar (fragment 5)|Antal|Totalt||Inget|
|cachemisses5|Cache missar (Shard 5)|Antal|Totalt||Inget|
|getcommands5|Gets (Skärva 5)|Antal|Totalt||Inget|
|setcommands5 (setcommands5)|Uppsättningar (Shard 5)|Antal|Totalt||Inget|
|operationerPerSecond5|Åtgärder per sekund (Shard 5)|Antal|Maximal||Inget|
|vräkasnycklar5|Vräkta nycklar (Shard 5)|Antal|Totalt||Inget|
|totalnycklar5|Totalt antal nycklar (Shard 5)|Antal|Maximal||Inget|
|utgångna nycklar5|Utgångna nycklar (Shard 5)|Antal|Totalt||Inget|
|usedmemory5|Använt minne (fragment 5)|Byte|Maximal||Inget|
|usedmemoryRss5|Använt minne RSS (Shard 5)|Byte|Maximal||Inget|
|serverLoad5|Serverbelastning (Shard 5)|Procent|Maximal||Inget|
|cacheWrite5|Cache skriva (Shard 5)|BytesPerSecond|Maximal||Inget|
|cacheLäs5|Cache läsa (Shard 5)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime5|CPU (Shard 5)|Procent|Maximal||Inget|
|anslutnaklienter6|Anslutna klienter (Shard 6)|Antal|Maximal||Inget|
|totalcommandsprocessed6|Totalt antal operationer (Shard 6)|Antal|Totalt||Inget|
|cachehits6|Cache träffar (fragment 6)|Antal|Totalt||Inget|
|cachemisses6|Cache missar (Shard 6)|Antal|Totalt||Inget|
|getcommands6|Gets (Skärva 6)|Antal|Totalt||Inget|
|setcommands6 (setcommands6)|Uppsättningar (Shard 6)|Antal|Totalt||Inget|
|operationerPerSecond6|Åtgärder per sekund (Shard 6)|Antal|Maximal||Inget|
|vräkasnycklar6|Vräkta nycklar (Shard 6)|Antal|Totalt||Inget|
|totalnycklar6|Totalt antal nycklar (Shard 6)|Antal|Maximal||Inget|
|utgångna nycklar6|Utgångna nycklar (Shard 6)|Antal|Totalt||Inget|
|usedmemory6|Använt minne (fragment 6)|Byte|Maximal||Inget|
|usedmemoryRss6|Använt minne RSS (Shard 6)|Byte|Maximal||Inget|
|serverLoad6|Serverbelastning (Shard 6)|Procent|Maximal||Inget|
|cacheWrite6|Cache skriva (Shard 6)|BytesPerSecond|Maximal||Inget|
|cacheRead6|Cache läsa (Shard 6)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime6|CPU (Shard 6)|Procent|Maximal||Inget|
|anslutnaklienter7|Anslutna klienter (Shard 7)|Antal|Maximal||Inget|
|totalcommandsprocessed7|Totalt antal operationer (Shard 7)|Antal|Totalt||Inget|
|cachehits7|Cache träffar (fragment 7)|Antal|Totalt||Inget|
|cachemisses7|Cache missar (Shard 7)|Antal|Totalt||Inget|
|getcommands7|Gets (Skärva 7)|Antal|Totalt||Inget|
|setcommands7 (setcommands7)|Uppsättningar (Shard 7)|Antal|Totalt||Inget|
|operationerPerSecond7|Åtgärder per sekund (Shard 7)|Antal|Maximal||Inget|
|vräkasnycklar7|Vräkta nycklar (Shard 7)|Antal|Totalt||Inget|
|totalnycklar7|Totalt antal nycklar (Shard 7)|Antal|Maximal||Inget|
|utgångna nycklar7|Utgångna nycklar (Shard 7)|Antal|Totalt||Inget|
|usedmemory7|Använt minne (fragment 7)|Byte|Maximal||Inget|
|usedmemoryRss7|Använt minne RSS (Shard 7)|Byte|Maximal||Inget|
|serverLoad7|Serverbelastning (Shard 7)|Procent|Maximal||Inget|
|cacheWrite7|Cache skriva (Shard 7)|BytesPerSecond|Maximal||Inget|
|cacheLäs7|Cache läsa (Shard 7)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime7|CPU (Shard 7)|Procent|Maximal||Inget|
|anslutnaklienter8|Anslutna klienter (Shard 8)|Antal|Maximal||Inget|
|totalcommandsprocessed8|Totalt antal operationer (Shard 8)|Antal|Totalt||Inget|
|cachehits8|Cache träffar (fragment 8)|Antal|Totalt||Inget|
|cachemisses8|Cache missar (Shard 8)|Antal|Totalt||Inget|
|getcommands8|Gets (Skärva 8)|Antal|Totalt||Inget|
|setcommands8 (setcommands8)|Uppsättningar (Shard 8)|Antal|Totalt||Inget|
|operationerPerSecond8|Åtgärder per sekund (Shard 8)|Antal|Maximal||Inget|
|vräkasnycklar8|Vräkta nycklar (Shard 8)|Antal|Totalt||Inget|
|totalnycklar8|Totalt antal nycklar (Shard 8)|Antal|Maximal||Inget|
|utgångna nycklar8|Utgångna nycklar (Shard 8)|Antal|Totalt||Inget|
|usedmemory8|Använt minne (fragment 8)|Byte|Maximal||Inget|
|usedmemoryRss8|Använt minne RSS (Shard 8)|Byte|Maximal||Inget|
|serverLoad8|Serverbelastning (Shard 8)|Procent|Maximal||Inget|
|cacheWrite8|Cache skriva (fragment 8)|BytesPerSecond|Maximal||Inget|
|cacheLäs8|Cache läsa (Shard 8)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime8|CPU (Shard 8)|Procent|Maximal||Inget|
|anslutnaklienter9|Anslutna klienter (fragment 9)|Antal|Maximal||Inget|
|totalcommandsprocessed9|Totalt antal operationer (Shard 9)|Antal|Totalt||Inget|
|cachehits9|Cache träffar (fragment 9)|Antal|Totalt||Inget|
|cachemisses9|Cache missar (Shard 9)|Antal|Totalt||Inget|
|getcommands9|Gets (Skärva 9)|Antal|Totalt||Inget|
|setcommands9 (setcommands9)|Uppsättningar (Shard 9)|Antal|Totalt||Inget|
|operationerPerSecond9|Åtgärder per sekund (Shard 9)|Antal|Maximal||Inget|
|vräkas nycklar9|Vräkta nycklar (Shard 9)|Antal|Totalt||Inget|
|totalnycklar9|Totalt antal nycklar (Shard 9)|Antal|Maximal||Inget|
|utgångna nycklar9|Utgångna nycklar (Shard 9)|Antal|Totalt||Inget|
|användsminne9|Använt minne (fragment 9)|Byte|Maximal||Inget|
|usedmemoryRss9|Använt minne RSS (Shard 9)|Byte|Maximal||Inget|
|serverLoad9|Serverbelastning (Shard 9)|Procent|Maximal||Inget|
|cacheWrite9|Cache skriva (fragment 9)|BytesPerSecond|Maximal||Inget|
|cacheLäs9|Cache läsa (Shard 9)|BytesPerSecond|Maximal||Inget|
|procentProcessorTime9|CPU (Shard 9)|Procent|Maximal||Inget|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Antal begäranden om brandvägg för webbprogram|Antal|Totalt|Antalet klientbegäranden som bearbetas av brandväggen för webbprogram|PolicyName,RuleName,Åtgärd|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandelen allokerade beräkningsenheter som för närvarande används av den virtuella datorn(er).|Inget|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av den virtuella datorn (inkommande trafik).|Inget|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Antalet byte som finns i alla nätverksgränssnitt av den eller de utgående datorerna (utgående trafik).|Inget|
|Diskläs byte/sek|Diskläsning|BytesPerSecond|Medel|Genomsnittligt antal byte som läs avstÃ:s från disk under övervakningsperioden.|Inget|
|Byte för diskskrivning/sek|Skriva disk|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivits till disk under övervakningsperioden.|Inget|
|Diskläsningsåtgärder/sek|Diskläsningsåtgärder/sek|CountPerSecond|Medel|Disk läsa IOPS.|Inget|
|Diskskrivningsåtgärder/sek|Diskskrivningsåtgärder/sek|CountPerSecond|Medel|IOPS för diskskrivning.|Inget|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roller

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandelen allokerade beräkningsenheter som för närvarande används av den virtuella datorn(er).|RoleInstanceId|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av den virtuella datorn (inkommande trafik).|RoleInstanceId|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Antalet byte som finns i alla nätverksgränssnitt av den eller de utgående datorerna (utgående trafik).|RoleInstanceId|
|Diskläs byte/sek|Diskläsning|BytesPerSecond|Medel|Genomsnittligt antal byte som läs avstÃ:s från disk under övervakningsperioden.|RoleInstanceId|
|Byte för diskskrivning/sek|Skriva disk|BytesPerSecond|Medel|Genomsnittligt antal byte som skrivits till disk under övervakningsperioden.|RoleInstanceId|
|Diskläsningsåtgärder/sek|Diskläsningsåtgärder/sek|CountPerSecond|Medel|Disk läsa IOPS.|RoleInstanceId|
|Diskskrivningsåtgärder/sek|Diskskrivningsåtgärder/sek|CountPerSecond|Medel|IOPS för diskskrivning.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Begagnad kapacitet|Byte|Medel|Använda kontokapacitet|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType,ApiName,Autentisering|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svarstiden från slutna till slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|Blob kapacitet|Byte|Medel|Mängden lagringsutrymme som används av lagringskontots Blob-tjänst i byte.|BlobType,Nivå|
|BlobCount (BlobCount)|Antal blob|Antal|Medel|Antalet Blob i lagringskontots Blob-tjänst.|BlobType,Nivå|
|ContainerCount (ContainerCount)|Antal blob-behållare|Antal|Medel|Antalet behållare i lagringskontots Blob-tjänst.|Inget|
|IndexKapacitet|Indexkapacitet|Byte|Medel|Mängden lagringsutrymme som används av ADLS Gen2 (Hierarkiskt) Index i byte.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType,ApiName,Autentisering|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svarstiden från slutna till slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TabellKapacitet|Tabellkapacitet|Byte|Medel|Mängden lagringsutrymme som används av lagringskontots tabelltjänst i byte.|Inget|
|TabellCount|Antal bord|Antal|Medel|Antalet tabeller i lagringskontots tabelltjänst.|Inget|
|TableEntityCount|Antal entiteter för tabell|Antal|Medel|Antalet tabellentiteter i lagringskontots tabelltjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType,ApiName,Autentisering|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svarstiden från slutna till slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Filkapacitet|Filkapacitet|Byte|Medel|Mängden lagringsutrymme som används av lagringskontots filtjänst i byte.|Fildelning|
|FilCount|Antal filer|Antal|Medel|Antalet filer i lagringskontots filtjänst.|Fildelning|
|Filsharecount|Antal filresurser|Antal|Medel|Antalet filresurser i lagringskontots filtjänst.|Inget|
|FileShareSnapshotCount|Antal ögonblicksbilder av filresurs|Antal|Medel|Antalet ögonblicksbilder som finns på resursen i lagringskontots filtjänst.|Fildelning|
|FileShareSnapshotSize|Ögonblicksbildstorlek för filresurs|Byte|Medel|Mängden lagringsutrymme som används av ögonblicksbilderna i lagringskontots filtjänst i byte.|Fildelning|
|ArkivDelaQuota|Kvotstorlek för filresurs|Byte|Medel|Den övre gränsen för hur mycket lagringsutrymme som kan användas av Azure Files Service i byte.|Fildelning|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering,FileShare|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering,FileShare|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering,FileShare|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType,ApiName,Autentisering,FileShare|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svarstiden från slutna till slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering,FileShare|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering,FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Kökapacitet|Kökapacitet|Byte|Medel|Mängden lagringsutrymme som används av lagringskontots kötjänst i byte.|Inget|
|QueueCount (QueueCount)|Antal köer|Antal|Medel|Antalet köer i lagringskontots kötjänst.|Inget|
|QueueMessageCount|Antal kömeddelanden|Antal|Medel|Det ungefärliga antalet kömeddelanden i lagringskontots kötjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType,ApiName,Autentisering|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Svarstiden från slutna till slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/konton

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Totalt Antal Meddelanden|Totalt antal samtal|Antal|Totalt|Totalt antal samtal.|ApiName,OperationName,Region|
|FramgångsrikaCalls|Lyckade samtal|Antal|Totalt|Antal lyckade samtal.|ApiName,OperationName,Region|
|TotalErrors|Totalt antal fel|Antal|Totalt|Totalt antal samtal med felsvar (HTTP-svarskod 4xx eller 5xx).|ApiName,OperationName,Region|
|Blockerade samtal|Blockerade samtal|Antal|Totalt|Antal samtal som överskridit kurs- eller kvotgränsen.|ApiName,OperationName,Region|
|ServerErrors|Serverfel|Antal|Totalt|Antal samtal med tjänstens interna fel (HTTP-svarskod 5xx).|ApiName,OperationName,Region|
|Kunderrors|Klientfel|Antal|Totalt|Antal anrop med fel på klientsidan (HTTP-svarskod 4xx).|ApiName,OperationName,Region|
|DataIn|Data i|Byte|Totalt|Storleken på inkommande data i byte.|ApiName,OperationName,Region|
|DataOut (DataOut)|Data ut|Byte|Totalt|Storleken på utgående data i byte.|ApiName,OperationName,Region|
|Svarstid|Svarstid|Millisekunder|Medel|Svarstid i millisekunder.|ApiName,OperationName,Region|
|TotaltTokenCalls|Totalt antal tokenanrop|Antal|Totalt|Totalt antal tokenanrop.|ApiName,OperationName,Region|
|Teckenöversatta|Översatta tecken|Antal|Totalt|Totalt antal tecken i inkommande textbegäran.|ApiName,OperationName,Region|
|TeckenTränade|Tecken tränade|Antal|Totalt|Totalt antal tecken som har tränats.|ApiName,OperationName,Region|
|TalsessionUnderation|Varaktighet för talsession|Sekunder|Totalt|Talsessionens totala varaktighet i sekunder.|ApiName,OperationName,Region|
|Totalt antal transaktioner|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner.|Inget|
|Bearbetadebilder|Bearbetade bilder|Antal|Totalt| Antal transaktioner för bildbehandling.|ApiName,FeatureName,Kanal,Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandelen allokerade beräkningsenheter som för närvarande används av den virtuella datorn(erna)|Inget|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tas emot i alla nätverksgränssnitt av den virtuella datorn (inkommande trafik) (föråldrad)|Inget|
|Nätverk – utgående|Utbytbart nätverk (föråldrad)|Byte|Totalt|Antalet fakturerbara byte som finns på alla nätverksgränssnitt av den virtuella datorn (utgående trafik) (föråldrad)|Inget|
|Diskläs byte|Diskläs byte|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|Inget|
|Byte av diskskrivning|Byte av diskskrivning|Byte|Totalt|Byte som skrivits till disk under övervakningsperioden|Inget|
|Diskläsningsåtgärder/sek|Diskläsningsåtgärder/sek|CountPerSecond|Medel|Disk läsa IOPS|Inget|
|Diskskrivningsåtgärder/sek|Diskskrivningsåtgärder/sek|CountPerSecond|Medel|IOPS för diskskrivning|Inget|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter tillgängliga för burst|Inget|
|Cpu-krediter förbrukas|Cpu-krediter förbrukas|Antal|Medel|Totalt antal krediter som förbrukas av den virtuella datorn|Inget|
|Läsbyte per disk per sekund|Läsbyten för datadisk/sek [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden|SlotId (kortplats)|
|Skrivbyte per disk per sekund|Skrivbyte för datadisk/sek [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden|SlotId (kortplats)|
|Läsåtgärder per disk/sek|Läsåtgärder för datadiskar/sek [(inaktuella)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden|SlotId (kortplats)|
|Skrivåtgärder per disk/sek|Datadiskskrivningsoperationer/sek [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden|SlotId (kortplats)|
|Per Disk QD|Datadisk QD [(föråldrad)](portal-disk-metrics-deprecation.md)](portal-disk-metrics-deprecation.md)|Antal|Medel|Djup för datadiskkö(eller kölängd)|SlotId (kortplats)|
|Os per disk läsbyten per sekund|Os-disklät byte/sek [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS per disk skrivbyten per sek|OS-diskskrivningsbyten/sek [(inaktuella)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden för OS-disk|Inget|
|Os per diskläsningsoperationer/sek|Diskläsningsåtgärder för OS-disk/sek [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS per diskskrivningsoperationer/sek|OS-diskskrivningsåtgärder/sek [(inaktuella)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS Per Disk QD|OS-disk QD [(föråldrad)](portal-disk-metrics-deprecation.md)|Antal|Medel|OS-diskködjup(eller kölängd)|Inget|
|Läsbyte för datadisk per sekund|Läsbyte för datadisk per sekund (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden|Lun|
|Skrivbyte för datadisk per sekund|Skrivbyte för datadisk/sek (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden|Lun|
|Läsåtgärder för datadiskar/sek|Läsåtgärder för datadisk/sek (förhandsgranskning)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden|Lun|
|Skrivåtgärder för datadisk/sek|Datadiskskrivningsåtgärder/sek (förhandsgranskning)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden|Lun|
|Ködjup för datadisk|Djup för datadiskkö (förhandsgranskning)|Antal|Medel|Djup för datadiskkö(eller kölängd)|Lun|
|Os-disklät byte per sek|OS-disklät byte/sek (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS-diskskrivningsbyten per sekund|OS-diskskrivningsbyten/sek (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden för OS-disk|Inget|
|Diskläsning av os-disk/sek|Diskläsning av operativsystem/sek (förhandsgranskning)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS-diskskrivningsåtgärder/sek|OS-diskskrivningsåtgärder/sek (förhandsgranskning)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|Ködjup för OS-disk|Os-diskködjup (förhandsgranskning)|Antal|Medel|OS-diskködjup(eller kölängd)|Inget|
|Inkommande flöden|Inkommande flöden|Antal|Medel|Inkommande flöden är antalet strömflöden i inkommande riktning (trafik som går in i den virtuella datorn)|Inget|
|Utgående flöden|Utgående flöden|Antal|Medel|Utgående flöden är antalet aktuella flöden i utgående riktning (trafik som går ut ur den virtuella datorn)|Inget|
|Inkommande flöden Maximal skapandegrad|Inkommande flöden Maximal skapandegrad|CountPerSecond|Medel|Den maximala skapandehastigheten för inkommande flöden (trafik som går in i den virtuella datorn)|Inget|
|Maximal skapandegrad för utgående flöden|Maximal skapandegrad för utgående flöden|CountPerSecond|Medel|Den maximala skapandehastigheten för utgående flöden (trafik som går ut ur den virtuella datorn)|Inget|
|Lästräff för premiumdatadisk cache|Lästräff för premiumdatadisk cache (förhandsgranskning)|Procent|Medel|Lästräff för premiumdatadisk cache|Lun|
|Premium Data Disk Cache Läs Miss|Premium Data Disk Cache Läs Miss (Preview)|Procent|Medel|Premium Data Disk Cache Läs Miss|Lun|
|Premium OS Disk Cache Läs träff|Lästräff för Premium OS-diskcachen (förhandsgranskning)|Procent|Medel|Premium OS Disk Cache Läs träff|Inget|
|Premium OS Disk cache läs miss|Premium OS Disk cache läsmiss (Preview)|Procent|Medel|Premium OS Disk cache läs miss|Inget|
|Nätverket totalt|Nätverket totalt|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av den virtuella datorn (Inkommande trafik)|Inget|
|Network Out Totalt|Network Out Totalt|Byte|Totalt|Antalet byte som finns på alla nätverksgränssnitt av den virtuella datorn (utgående trafik)|Inget|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandelen allokerade beräkningsenheter som för närvarande används av den virtuella datorn(erna)|VMName|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tas emot i alla nätverksgränssnitt av den virtuella datorn (inkommande trafik) (föråldrad)|VMName|
|Nätverk – utgående|Utbytbart nätverk (föråldrad)|Byte|Totalt|Antalet fakturerbara byte som finns på alla nätverksgränssnitt av den virtuella datorn (utgående trafik) (föråldrad)|VMName|
|Diskläs byte|Diskläs byte|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|VMName|
|Byte av diskskrivning|Byte av diskskrivning|Byte|Totalt|Byte som skrivits till disk under övervakningsperioden|VMName|
|Diskläsningsåtgärder/sek|Diskläsningsåtgärder/sek|CountPerSecond|Medel|Disk läsa IOPS|VMName|
|Diskskrivningsåtgärder/sek|Diskskrivningsåtgärder/sek|CountPerSecond|Medel|IOPS för diskskrivning|VMName|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter tillgängliga för burst|Inget|
|Cpu-krediter förbrukas|Cpu-krediter förbrukas|Antal|Medel|Totalt antal krediter som förbrukas av den virtuella datorn|Inget|
|Läsbyte per disk per sekund|Läsbyten för datadisk/sek [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden|SlotId (kortplats)|
|Skrivbyte per disk per sekund|Skrivbyte för datadisk/sek [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden|SlotId (kortplats)|
|Läsåtgärder per disk/sek|Läsåtgärder för datadiskar/sek [(inaktuella)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden|SlotId (kortplats)|
|Skrivåtgärder per disk/sek|Datadiskskrivningsoperationer/sek [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden|SlotId (kortplats)|
|Per Disk QD|Datadisk QD [(föråldrad)](portal-disk-metrics-deprecation.md)|Antal|Medel|Djup för datadiskkö(eller kölängd)|SlotId (kortplats)|
|Os per disk läsbyten per sekund|Os-disklät byte/sek [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS per disk skrivbyten per sek|OS-diskskrivningsbyten/sek [(inaktuella)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden för OS-disk|Inget|
|Os per diskläsningsoperationer/sek|Diskläsningsåtgärder för OS-disk/sek [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS per diskskrivningsoperationer/sek|OS-diskskrivningsåtgärder/sek [(inaktuella)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS Per Disk QD|OS-disk QD [(föråldrad)](portal-disk-metrics-deprecation.md)|Antal|Medel|OS-diskködjup(eller kölängd)|Inget|
|Läsbyte för datadisk per sekund|Läsbyte för datadisk per sekund (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden|LUN,VMName|
|Skrivbyte för datadisk per sekund|Skrivbyte för datadisk/sek (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden|LUN,VMName|
|Läsåtgärder för datadiskar/sek|Läsåtgärder för datadisk/sek (förhandsgranskning)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden|LUN,VMName|
|Skrivåtgärder för datadisk/sek|Datadiskskrivningsåtgärder/sek (förhandsgranskning)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden|LUN,VMName|
|Ködjup för datadisk|Djup för datadiskkö (förhandsgranskning)|Antal|Medel|Djup för datadiskkö(eller kölängd)|LUN,VMName|
|Os-disklät byte per sek|OS-disklät byte/sek (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden för OS-disk|VMName|
|OS-diskskrivningsbyten per sekund|OS-diskskrivningsbyten/sek (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden för OS-disk|VMName|
|Diskläsning av os-disk/sek|Diskläsning av operativsystem/sek (förhandsgranskning)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden för OS-disk|VMName|
|OS-diskskrivningsåtgärder/sek|OS-diskskrivningsåtgärder/sek (förhandsgranskning)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden för OS-disk|VMName|
|Ködjup för OS-disk|Os-diskködjup (förhandsgranskning)|Antal|Medel|OS-diskködjup(eller kölängd)|VMName|
|Inkommande flöden|Inkommande flöden|Antal|Medel|Inkommande flöden är antalet strömflöden i inkommande riktning (trafik som går in i den virtuella datorn)|VMName|
|Utgående flöden|Utgående flöden|Antal|Medel|Utgående flöden är antalet aktuella flöden i utgående riktning (trafik som går ut ur den virtuella datorn)|VMName|
|Inkommande flöden Maximal skapandegrad|Inkommande flöden Maximal skapandegrad|CountPerSecond|Medel|Den maximala skapandehastigheten för inkommande flöden (trafik som går in i den virtuella datorn)|VMName|
|Maximal skapandegrad för utgående flöden|Maximal skapandegrad för utgående flöden|CountPerSecond|Medel|Den maximala skapandehastigheten för utgående flöden (trafik som går ut ur den virtuella datorn)|VMName|
|Lästräff för premiumdatadisk cache|Lästräff för premiumdatadisk cache (förhandsgranskning)|Procent|Medel|Lästräff för premiumdatadisk cache|LUN,VMName|
|Premium Data Disk Cache Läs Miss|Premium Data Disk Cache Läs Miss (Preview)|Procent|Medel|Premium Data Disk Cache Läs Miss|LUN,VMName|
|Premium OS Disk Cache Läs träff|Lästräff för Premium OS-diskcachen (förhandsgranskning)|Procent|Medel|Premium OS Disk Cache Läs träff|VMName|
|Premium OS Disk cache läs miss|Premium OS Disk cache läsmiss (Preview)|Procent|Medel|Premium OS Disk cache läs miss|VMName|
|Nätverket totalt|Nätverket totalt|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av den virtuella datorn (Inkommande trafik)|VMName|
|Network Out Totalt|Network Out Totalt|Byte|Totalt|Antalet byte som finns på alla nätverksgränssnitt av den virtuella datorn (utgående trafik)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|Procentandelen allokerade beräkningsenheter som för närvarande används av den virtuella datorn(erna)|Inget|
|Nätverk – inkommande|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tas emot i alla nätverksgränssnitt av den virtuella datorn (inkommande trafik) (föråldrad)|Inget|
|Nätverk – utgående|Utbytbart nätverk (föråldrad)|Byte|Totalt|Antalet fakturerbara byte som finns på alla nätverksgränssnitt av den virtuella datorn (utgående trafik) (föråldrad)|Inget|
|Diskläs byte|Diskläs byte|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|Inget|
|Byte av diskskrivning|Byte av diskskrivning|Byte|Totalt|Byte som skrivits till disk under övervakningsperioden|Inget|
|Diskläsningsåtgärder/sek|Diskläsningsåtgärder/sek|CountPerSecond|Medel|Disk läsa IOPS|Inget|
|Diskskrivningsåtgärder/sek|Diskskrivningsåtgärder/sek|CountPerSecond|Medel|IOPS för diskskrivning|Inget|
|Återstående CPU-krediter|Återstående CPU-krediter|Antal|Medel|Totalt antal krediter tillgängliga för burst|Inget|
|Cpu-krediter förbrukas|Cpu-krediter förbrukas|Antal|Medel|Totalt antal krediter som förbrukas av den virtuella datorn|Inget|
|Läsbyte per disk per sekund|Läsbyten för datadisk/sek [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden|SlotId (kortplats)|
|Skrivbyte per disk per sekund|Skrivbyte för datadisk/sek [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden|SlotId (kortplats)|
|Läsåtgärder per disk/sek|Läsåtgärder för datadiskar/sek [(inaktuella)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden|SlotId (kortplats)|
|Skrivåtgärder per disk/sek|Datadiskskrivningsoperationer/sek [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden|SlotId (kortplats)|
|Per Disk QD|Datadisk QD [(föråldrad)](portal-disk-metrics-deprecation.md)|Antal|Medel|Djup för datadiskkö(eller kölängd)|SlotId (kortplats)|
|Os per disk läsbyten per sekund|Os-disklät byte/sek [(inaktuellt)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS per disk skrivbyten per sek|OS-diskskrivningsbyten/sek [(inaktuella)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden för OS-disk|Inget|
|Os per diskläsningsoperationer/sek|Diskläsningsåtgärder för OS-disk/sek [(inaktuell)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS per diskskrivningsoperationer/sek|OS-diskskrivningsåtgärder/sek [(inaktuella)](portal-disk-metrics-deprecation.md)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS Per Disk QD|OS-disk QD [(föråldrad)](portal-disk-metrics-deprecation.md)|Antal|Medel|OS-diskködjup(eller kölängd)|Inget|
|Läsbyte för datadisk per sekund|Läsbyte för datadisk per sekund (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden|Lun|
|Skrivbyte för datadisk per sekund|Skrivbyte för datadisk/sek (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden|Lun|
|Läsåtgärder för datadiskar/sek|Läsåtgärder för datadisk/sek (förhandsgranskning)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden|Lun|
|Skrivåtgärder för datadisk/sek|Datadiskskrivningsåtgärder/sek (förhandsgranskning)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden|Lun|
|Ködjup för datadisk|Djup för datadiskkö (förhandsgranskning)|Antal|Medel|Djup för datadiskkö(eller kölängd)|Lun|
|Os-disklät byte per sek|OS-disklät byte/sek (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek som lästs från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS-diskskrivningsbyten per sekund|OS-diskskrivningsbyten/sek (förhandsgranskning)|CountPerSecond|Medel|Byte/Sek skrivet till en enda disk under övervakningsperioden för OS-disk|Inget|
|Diskläsning av os-disk/sek|Diskläsning av operativsystem/sek (förhandsgranskning)|CountPerSecond|Medel|Läsa IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|OS-diskskrivningsåtgärder/sek|OS-diskskrivningsåtgärder/sek (förhandsgranskning)|CountPerSecond|Medel|Skriva IOPS från en enda disk under övervakningsperioden för OS-disk|Inget|
|Ködjup för OS-disk|Os-diskködjup (förhandsgranskning)|Antal|Medel|OS-diskködjup(eller kölängd)|Inget|
|Inkommande flöden|Inkommande flöden|Antal|Medel|Inkommande flöden är antalet strömflöden i inkommande riktning (trafik som går in i den virtuella datorn)|Inget|
|Utgående flöden|Utgående flöden|Antal|Medel|Utgående flöden är antalet aktuella flöden i utgående riktning (trafik som går ut ur den virtuella datorn)|Inget|
|Inkommande flöden Maximal skapandegrad|Inkommande flöden Maximal skapandegrad|CountPerSecond|Medel|Den maximala skapandehastigheten för inkommande flöden (trafik som går in i den virtuella datorn)|Inget|
|Maximal skapandegrad för utgående flöden|Maximal skapandegrad för utgående flöden|CountPerSecond|Medel|Den maximala skapandehastigheten för utgående flöden (trafik som går ut ur den virtuella datorn)|Inget|
|Lästräff för premiumdatadisk cache|Lästräff för premiumdatadisk cache (förhandsgranskning)|Procent|Medel|Lästräff för premiumdatadisk cache|Lun|
|Premium Data Disk Cache Läs Miss|Premium Data Disk Cache Läs Miss (Preview)|Procent|Medel|Premium Data Disk Cache Läs Miss|Lun|
|Premium OS Disk Cache Läs träff|Lästräff för Premium OS-diskcachen (förhandsgranskning)|Procent|Medel|Premium OS Disk Cache Läs träff|Inget|
|Premium OS Disk cache läs miss|Premium OS Disk cache läsmiss (Preview)|Procent|Medel|Premium OS Disk cache läs miss|Inget|
|Nätverket totalt|Nätverket totalt|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av den virtuella datorn (Inkommande trafik)|Inget|
|Network Out Totalt|Network Out Totalt|Byte|Totalt|Antalet byte som finns på alla nätverksgränssnitt av den virtuella datorn (utgående trafik)|Inget|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|CPU-användning|Antal|Medel|CPU-användning på alla kärnor i millicores.|containerName (containerName)|
|Minnesupptring|Minnesanvändning|Byte|Medel|Total minnesanvändning i byte.|containerName (containerName)|
|NetworkBytesReceivedPerSecond|Nätverksbyte som tas emot per sekund|Byte|Medel|Nätverksbyten som tas emot per sekund.|Inget|
|NätverksbyteÖversändadPerSecond|Nätverksbyte överförda per sekund|Byte|Medel|Nätverksbyten överförs per sekund.|Inget|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.Container-register/register

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Totalt Antal|Totalt antal pull|Antal|Medel|Antal bilddrag totalt|Inget|
|LyckadPullCount|Lyckat pull-antal|Antal|Medel|Antal lyckade bilddrag|Inget|
|TotaltKonto|Totalt antal push-tryck|Antal|Medel|Antal bildtrycker totalt|Inget|
|LyckadPushCount|Lyckat antal push-ningar|Antal|Medel|Antal lyckade bildtryck|Inget|
|RunDuration|Kör varaktighet|Millisekunder|Totalt|Kör varaktighet i millisekunder|Inget|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totalt antal tillgängliga cpu-kärnor i ett hanterat kluster|Antal|Medel|Totalt antal tillgängliga cpu-kärnor i ett hanterat kluster|Inget|
|kube_node_status_allocatable_memory_bytes|Total mängd tillgängligt minne i ett hanterat kluster|Byte|Medel|Total mängd tillgängligt minne i ett hanterat kluster|Inget|
|kube_pod_status_ready|Antal poddar i redoläge|Antal|Medel|Antal poddar i redoläge|namnområde,pod|
|kube_node_status_condition|Status för olika nodvillkor|Antal|Medel|Status för olika nodvillkor|villkor,status,status2,nod|
|kube_pod_status_phase|Antal poddar efter fas|Antal|Medel|Antal poddar efter fas|fas,namnområde,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Framgångsrika 1898|Lyckade förfrågningar|Antal|Totalt|Lyckade förfrågningar från den anpassade leverantören|HttpMethod,CallPath,StatusCode|
|Misslyckade begärande|Misslyckade begäranden|Antal|Totalt|Hämtar tillgängliga loggar för anpassade resursleverantörer|HttpMethod,CallPath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|NICReadThroughput|Läs dataflöde (nätverk)|BytesPerSecond|Medel|Nätverksgränssnittets läsflöde på enheten under rapporteringsperioden för alla volymer i gatewayen.|Instansnamn|
|NICWriteDetgenomput|Skrivdataflöde (nätverk)|BytesPerSecond|Medel|Skrivflödet för nätverksgränssnittet på enheten under rapporteringsperioden för alla volymer i gatewayen.|Instansnamn|
|CloudReadThroughputPerShare|Dataflöde för molnhämtning (delning)|BytesPerSecond|Medel|Hämtningsflödet till Azure från en resurs under rapporteringsperioden.|Dela|
|CloudUploadDroughputPerShare|Molnladdningsdataflöde (dela)|BytesPerSecond|Medel|Överföringsdataflödet till Azure från en resurs under rapporteringsperioden.|Dela|
|BytesUppladdadeToCloudPerShare|Cloud Bytes uppladdade (dela)|Byte|Medel|Det totala antalet byte som överförs till Azure från en resurs under rapporteringsperioden.|Dela|
|Totalkapacitet|Total kapacitet|Byte|Medel|Total kapacitet|Inget|
|Tillgängligkapacitet|Tillgänglig kapacitet|Byte|Medel|Den tillgängliga kapaciteten i byte under rapporteringsperioden.|Inget|
|CloudUploadDrgenomput|Molnuppladdningsdataflöde|BytesPerSecond|Medel|Molnet överför dataflöde till Azure under rapporteringsperioden.|Inget|
|CloudReadThroughput|Dataflöde för molnhämtning|BytesPerSecond|Medel|Molnet hämtar dataflöde till Azure under rapporteringsperioden.|Inget|
|BytesLaddadeToCloud|Molnbyte uppladdade (enhet)|Byte|Medel|Det totala antalet byte som överförs till Azure från en enhet under rapporteringsperioden.|Inget|
|HyperVVirtualProcessorUtilization|Edge Compute - Procent CPU|Procent|Medel|Procent cpu-användning|Instansnamn|
|HyperVMemoryUtilization|Edge Compute - Minnesanvändning|Procent|Medel|Mängden RAM-minne som används|Instansnamn|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datakategoris

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TillgångsfördelningEfterklassificering|Tillgångsfördelning efter klassificering|Antal|Totalt|Anger antalet tillgångar med en viss klassificering som tilldelats, dvs.|Klassificering,Källa|
|TillgångDistributionByStorageType|Tillgångsfördelning efter lagringstyp|Antal|Totalt|Anger antalet tillgångar med en viss lagringstyp.|StorageType (lagringstyp)|
|AntalAvgörandenKlassificifications|Antal tillgångar med minst en klassificering|Antal|Medel|Anger antalet tillgångar med minst en taggklassificering.|Inget|
|ScanCancelled|Genomsökningen har avbrutits|Antal|Totalt|Anger antalet avbrutna genomsökningar.|Inget|
|ScanCom är slutfört|Genomsökningen har slutförts|Antal|Totalt|Anger antalet genomförda genomsökningar.|Inget|
|ScanFailed|Det gick inte att skanna|Antal|Totalt|Anger att antalet genomsökningar misslyckades.|Inget|
|ScanTimeTaken|Sök tid|Sekunder|Totalt|Anger den totala genomsökningstiden i sekunder.|Inget|
|CatalogActiveUsers|Dagliga aktiva användare|Antal|Totalt|Antal aktiva användare dagligen|Inget|
|KatalogUppsätt|Förbrukningsfördelning efter operation|Antal|Totalt|Ange hur många åtgärder användaren gör i katalogen, dvs Access, Sök, Ordlista.|Åtgärd|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafakta

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MisslyckadeRuns|Misslyckade körningar|Antal|Totalt||pipelineName,activityName|
|LyckadeRuns|Lyckade körningar|Antal|Totalt||pipelineName,activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fabriker

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PipelineFailedRuns|Misslyckade pipelinekörningar mått|Antal|Totalt||FailureType,Namn|
|PipelineSucceedEdRuns|Efterföljande pipeline kör mått|Antal|Totalt||FailureType,Namn|
|PipelineCancelledRuns|Avbrutna pipelinekörningsmått|Antal|Totalt||FailureType,Namn|
|ActivityFailedRuns|Misslyckade aktivitet kör mått|Antal|Totalt||ActivityType,PipelineName,FailureType,Namn|
|ActivitySucceededRuns|Efterföljande aktivitet kör mått|Antal|Totalt||ActivityType,PipelineName,FailureType,Namn|
|ActivityCancelledRuns|Avbrutna aktivitet kör mått|Antal|Totalt||ActivityType,PipelineName,FailureType,Namn|
|TriggerFailedRuns|Misslyckade utlösarkörningsmått|Antal|Totalt||Namn,FailureType|
|TriggerSucceededRuns|Efterföljande utlösarkörningsmått|Antal|Totalt||Namn,FailureType|
|TriggerCancelledRuns|Avbruten utlösare kör mått|Antal|Totalt||Namn,FailureType|
|IntegrationRuntimeCpuPercentage|Processoranvändning för integreringskörning|Procent|Medel||IntegrationRuntimeName,NodeName|
|IntegrationRuntimeTillgängligt Minne|Tillgängligt minne för integreringskörning|Byte|Medel||IntegrationRuntimeName,NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Varaktighet för integrationskörningskö|Sekunder|Medel||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Kölängd för integrationskörning|Antal|Medel||IntegrationRuntimeName|
|IntegrationRuntimeTillgänglignnnägs|Antal tillgängliga nodar för integreringskörning|Antal|Medel||IntegrationRuntimeName|
|MaxTilldömdResourceCount|Antal tillåtna enheter med maximal tillåten enhet|Antal|Maximal||Inget|
|MaxTillåtafactorysizeInGbUnits|Maximal tillåten fabriksstorlek (GB-enhet)|Antal|Maximal||Inget|
|ResursCount|Totalt antal enheter|Antal|Maximal||Inget|
|FactorySizeInGbUnits|Total fabriksstorlek (GB-enhet)|Antal|Maximal||Inget|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/konton

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobbEndedSuccess|Framgångsrika jobb|Antal|Totalt|Antal lyckade jobb.|Inget|
|JobbEndedFailure|Misslyckade jobb|Antal|Totalt|Antal misslyckade jobb.|Inget|
|JobbEndedKancelled|Avbrutna jobb|Antal|Totalt|Antal avbrutna jobb.|Inget|
|JobbAUEndedSuccess|Framgångsrik AU-tid|Sekunder|Totalt|Total AU tid för framgångsrika jobb.|Inget|
|JobbAUEndedFailure|Misslyckad AU-tid|Sekunder|Totalt|Total AU-tid för misslyckade jobb.|Inget|
|JobbAUEndedKancelled|Inställd AU-tid|Sekunder|Totalt|Total AU-tid för inställda jobb.|Inget|
|PlatsSteg|Jobb i scenen|Antal|Totalt|Antal jobb i varje steg.|Inget|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/konton

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Byte|Maximal|Totalt antal data som lagras i kontot.|Inget|
|Dataskrivet|Uppgifter skrivna|Byte|Totalt|Totalt antal data som skrivits till kontot.|Inget|
|DataLäs|Data läsa|Byte|Totalt|Totalt antal data som lästs från kontot.|Inget|
|WriteRequests|Skriv förfrågningar|Antal|Totalt|Antal dataskrivningsbegäranden till kontot.|Inget|
|ReadRequests (LäsRequests)|Läsa begäranden|Antal|Totalt|Antal dataläsningsbegäranden till kontot.|Inget|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/konton

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ShareCount (060/|Skickade aktier|Antal|Maximal|Antal skickade aktier på kontot|Resursnamn|
|ShareSubscriptionCount|Mottagna aktier|Antal|Maximal|Antal erhållna aktier på kontot|ShareSubscriptionName|
|EfterföljandeShareSynchronizations|Skickade ögonblicksbilder av lyckades med resurs|Antal|Antal|Antal skickade ögonblicksbilder av den skickade resursen i kontot|Inget|
|Misslyckade Aktiesynkroniseringar|Misslyckade ögonblicksbilder av skickade delnings misslyckades|Antal|Antal|Antal misslyckade ögonblicksbilder av skickade delar i kontot|Inget|
|EfterföljandeShareSubscriptionSynchronizations|Mottagna ögonblicksbilder av lyckades med resurs|Antal|Antal|Antal mottagna resursen lyckades ögonblicksbilder i kontot|Inget|
|MisslyckadeShareSubscriptionSynchronizations|Mottagna ögonblicksbilder av delade misslyckades|Antal|Antal|Antal mottagna ögonblicksbilder av den mottagna resursen i kontot|Inget|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servrar

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|memory_percent|Minne procent|Procent|Medel|Minne procent|Inget|
|io_consumption_percent|IO procent|Procent|Medel|IO procent|Inget|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inget|
|storage_used|Användning av lagring|Byte|Medel|Användning av lagring|Inget|
|storage_limit|Lagringsgräns|Byte|Maximal|Lagringsgräns|Inget|
|serverlog_storage_percent|Lagringsprocent för serverlogg|Procent|Medel|Lagringsprocent för serverlogg|Inget|
|serverlog_storage_usage|Serverlogglagring används|Byte|Medel|Serverlogglagring används|Inget|
|serverlog_storage_limit|Lagringsgräns för serverlogg|Byte|Medel|Lagringsgräns för serverlogg|Inget|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inget|
|backup_storage_used|Lagring för säkerhetskopiering som används|Byte|Medel|Lagring för säkerhetskopiering som används|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i aktiva anslutningar|Inget|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servrar

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|memory_percent|Minne procent|Procent|Medel|Minne procent|Inget|
|io_consumption_percent|IO procent|Procent|Medel|IO procent|Inget|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inget|
|storage_used|Användning av lagring|Byte|Medel|Användning av lagring|Inget|
|storage_limit|Lagringsgräns|Byte|Maximal|Lagringsgräns|Inget|
|serverlog_storage_percent|Lagringsprocent för serverlogg|Procent|Medel|Lagringsprocent för serverlogg|Inget|
|serverlog_storage_usage|Serverlogglagring används|Byte|Medel|Serverlogglagring används|Inget|
|serverlog_storage_limit|Lagringsgräns för serverlogg|Byte|Maximal|Lagringsgräns för serverlogg|Inget|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inget|
|backup_storage_used|Lagring för säkerhetskopiering som används|Byte|Medel|Lagring för säkerhetskopiering som används|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i aktiva anslutningar|Inget|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servrar

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|memory_percent|Minne procent|Procent|Medel|Minne procent|Inget|
|io_consumption_percent|IO procent|Procent|Medel|IO procent|Inget|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inget|
|storage_used|Användning av lagring|Byte|Medel|Användning av lagring|Inget|
|storage_limit|Lagringsgräns|Byte|Maximal|Lagringsgräns|Inget|
|serverlog_storage_percent|Lagringsprocent för serverlogg|Procent|Medel|Lagringsprocent för serverlogg|Inget|
|serverlog_storage_usage|Serverlogglagring används|Byte|Medel|Serverlogglagring används|Inget|
|serverlog_storage_limit|Lagringsgräns för serverlogg|Byte|Maximal|Lagringsgräns för serverlogg|Inget|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|backup_storage_used|Lagring för säkerhetskopiering som används|Byte|Medel|Lagring för säkerhetskopiering som används|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i aktiva anslutningar|Inget|
|pg_replica_log_delay_in_seconds|Replik lag|Sekunder|Maximal|Fördröjning i replik på några sekunder|Inget|
|pg_replica_log_delay_in_bytes|Max fördröjning över repliker|Byte|Maximal|Eftersläpning i byte av de mest eftersläpande replika|Inget|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|memory_percent|Minne procent|Procent|Medel|Minne procent|Inget|
|Iops|IOPS|Antal|Medel|IO-operationer per sekund|Inget|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inget|
|storage_used|Användning av lagring|Byte|Medel|Användning av lagring|Inget|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i aktiva anslutningar|Inget|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|memory_percent|Minne procent|Procent|Medel|Minne procent|Inget|
|Iops|IOPS|Antal|Medel|IO-operationer per sekund|Inget|
|storage_percent|Lagringsprocent|Procent|Medel|Lagringsprocent|Inget|
|storage_used|Användning av lagring|Byte|Medel|Användning av lagring|Inget|
|active_connections|Aktiva anslutningar|Antal|Medel|Aktiva anslutningar|Inget|
|network_bytes_egress|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inget|
|network_bytes_ingress|Nätverk – inkommande|Byte|Totalt|Nätverk i aktiva anslutningar|Inget|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|connections_succeeded|Efterföljande anslutningar|Antal|Totalt|Efterföljande anslutningar|Inget|
|maximum_used_transactionIDs|Maximalt använda transaktions-ID:n|Antal|Medel|Maximalt använda transaktions-ID:n|Inget|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Skicka försök till telemetri|Antal|Totalt|Antal telemetrimeddelanden från enhet till moln som har försökt skickas till din IoT-hubb|Inget|
|d2c.telemetry.ingress.success|Skickade telemetrimeddelanden|Antal|Totalt|Antal telemetrimeddelanden från enhet till moln som skickats till din IoT-hubb|Inget|
|c2d.commands.egress.complete.success|C2D-meddelandeleveranser har slutförts|Antal|Totalt|Antal meddelandeleveranser från molnet till enheten som har slutförts av enheten|Inget|
|c2d.commands.egress.abandon.success|C2D-meddelanden övergivna|Antal|Totalt|Antal meddelanden från molnet till enheten som övergivits av enheten|Inget|
|c2d.commands.egress.reject.success|C2D-meddelanden avvisade|Antal|Totalt|Antal meddelanden från molnet till enheten som avvisats av enheten|Inget|
|C2DMessagesExpirerad|C2D-meddelanden har upphört att gälla (förhandsgranskning)|Antal|Totalt|Antal meddelanden från molnet till enheten som har upphört att gälla|Inget|
|devices.totalDevices|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som är registrerade i din IoT-hubb|Inget|
|devices.connectedDevices.allProtocol|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inget|
|d2c.telemetry.egress.success|Routning: telemetrimeddelanden levererade|Antal|Totalt|Antalet gånger meddelanden har levererats till alla slutpunkter med hjälp av IoT Hub-routning. Om ett meddelande dirigeras till flera slutpunkter ökar det här värdet med en för varje lyckad leverans. Om ett meddelande levereras till samma slutpunkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inget|
|d2c.telemetry.egress.dropped|Routning: telemetrimeddelanden har tagits bort |Antal|Totalt|Antalet gånger meddelanden har tappats av IoT Hub routning på grund av döda slutpunkter. Det här värdet räknar inte meddelanden som levereras till återställningsvägen eftersom ignorerade meddelanden inte levereras där.|Inget|
|d2c.telemetry.egress.orphaned d2c.telemetry.egress.orphaned d2c.telemetry.egress.orphaned d2|Routning: överblivna telemetrimeddelanden |Antal|Totalt|Antalet gånger meddelanden övergavs av IoT Hub-routning eftersom de inte matchade några routningsregler (inklusive reservregeln). |Inget|
|d2c.telemetry.egress.invalid|Routning: telemetrimeddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub-routning misslyckades med att leverera meddelanden på grund av en inkompatibilitet med slutpunkten. Det här värdet inkluderar inte återförsök.|Inget|
|d2c.telemetri.egress.reserv|Routning: meddelanden som levereras till reserv|Antal|Totalt|Antalet gånger IoT Hub-routning levererade meddelanden till slutpunkten som är associerad med återgångsrutten.|Inget|
|d2c.endpoints.egress.eventHubs|Routning: meddelanden som levereras till eventhubben|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till slutpunkter för Event Hub.|Inget|
|d2c.endpoints.latens.eventHubs|Routning: meddelandesvarstid för händelsehubben|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och meddelandeinträngning till en event hub-slutpunkt.|Inget|
|d2c.endpoints.egress.serviceBusQueues|Routning: meddelanden som levereras till servicebusskö|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till servicebussköslutpunkter.|Inget|
|d2c.endpoints.latency.serviceBusQueues|Routning: meddelandesvarstid för servicebusskö|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och telemetrimeddelandet inträder i en tjänstbussköslutpunkt.|Inget|
|d2c.endpoints.egress.serviceBusTopics|Routning: meddelanden som levereras till Service Bus-ämne|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till servicebussämneslutpunkter.|Inget|
|d2c.endpoints.latens.serviceBusTopics|Routning: meddelandefördröjning för servicebussavsnitt|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och telemetrimeddelande ingående till en Service Bus-ämneslutpunkt.|Inget|
|d2c.endpoints.egress.builtIn.events|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till den inbyggda slutpunkten (meddelanden/händelser).|Inget|
|d2c.endpoints.latens.builtIn.events|Routning: meddelandefördröjning för meddelanden/händelser|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och telemetrimeddelande ingående till den inbyggda slutpunkten (meddelanden/händelser).|Inget|
|d2c.endpoints.egress.storage|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till lagringsslutpunkter.|Inget|
|d2c.endpoints.latens.lagring|Routning: meddelandefördröjning för lagring|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och telemetrimeddelande inträngning i en lagringsslutpunkt.|Inget|
|d2c.endpoints.egress.storage.bytes|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub-routning som levereras till lagringsslutpunkter.|Inget|
|d2c.endpoints.egress.storage.blobbar|Routning: blobbar som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub-routning levererade blobbar till lagringsslutpunkter.|Inget|
|EventGridDeliveries|Leveranser av eventrutnät (förhandsgranskning)|Antal|Totalt|Antalet IoT Hub-händelser som publicerats i Event Grid. Använd dimensionen Resultat för antalet lyckade och misslyckade begäranden. EventType-dimensionen visar typenhttps://aka.ms/ioteventgrid)av händelse ( .|ResourceId,Resultat,EventTyp|
|EventGridLatency|Svarstid för händelserutnät (förhandsgranskning)|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) från när Iot Hub-händelsen genererades till när händelsen publicerades i Event Grid. Det här talet är ett genomsnitt mellan alla händelsetyper. Använd dimensionen EventType om du vill se svarstid för en viss typ av händelse.|ResourceId,EventType|
|RoutingDeliveries|Routningsleveranser (förhandsgranskning)|Millisekunder|Totalt|Antalet gånger IoT Hub försökte leverera meddelanden till alla slutpunkter med routning. Om du vill se antalet lyckade eller misslyckade försök använder du dimensionen Resultat. Om du vill se orsaken till fel, till exempel ogiltig, bortfallen eller överbliven, använder du dimensionen FailureReasonCategory. Du kan också använda dimensionerna Slutpunktnamn och Slutpunktstyp för att förstå hur många meddelanden som levererades till dina olika slutpunkter. Måttvärdet ökar med ett för varje leveransförsök, inklusive om meddelandet levereras till flera slutpunkter eller om meddelandet levereras till samma slutpunkt flera gånger.|ResourceId,EndpointType,EndpointName,FailureReasonCategory,Result,RoutingSource|
|RoutingDeliveryLatency|Svarstid för routningsleverans (förhandsgranskning)|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och telemetrimeddelande inträngning till en slutpunkt. Du kan använda dimensionerna Slutpunktnamn och Slutpunktstyp för att förstå svarstiden för dina olika slutpunkter.|ResourceId,EndpointType,EndpointName,RoutingSource|
|d2c.twin.read.success|Lyckade tvillingläsningar från enheter|Antal|Totalt|Antalet lyckade enhetsinitierade tvillingläsningar.|Inget|
|d2c.twin.read.failure|Misslyckade tvillingavläsningar från enheter|Antal|Totalt|Antalet misslyckade enhetsinitierade tvillingläsningar.|Inget|
|d2c.twin.read.size|Svarsstorleken för dubbla läsningar från enheter|Byte|Medel|Medelvärdet, min och max för alla framgångsrika enhetsinitierade tvillingläsningar.|Inget|
|d2c.twin.update.success|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade enhetsinitierade tvillinguppdateringar.|Inget|
|d2c.twin.update.failure|Misslyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet misslyckade enhetsinitierade tvillinguppdateringar.|Inget|
|d2c.twin.update.size|Storlek på dubbla uppdateringar från enheter|Byte|Medel|Medelvärdet, min och maxstorleken för alla lyckade enhetsinitierade tvillinguppdateringar.|Inget|
|c2d.methods.success|Lyckade direkta metodaropationer|Antal|Totalt|Antalet alla lyckade direktmetodanrop.|Inget|
|c2d.methods.failure|Misslyckade direkta metodaropcations|Antal|Totalt|Antalet misslyckade direktmetodanrop.|Inget|
|c2d.methods.requestSize|Begär storlek på direkta metodaropcations|Byte|Medel|Medelvärdet, min och max för alla lyckade direktmetodbegäranden.|Inget|
|c2d.methods.responseSize|Svarsstorlek för direkta metodutkallelser|Byte|Medel|Medelvärdet, min och max för alla framgångsrika direktmetodsvar.|Inget|
|c2d.twin.read.success|Lyckad tvilling läser från slutet|Antal|Totalt|Antalet framgångsrika back-end-initierade tvillingläsningar.|Inget|
|c2d.twin.read.failure|Misslyckade tvillingläsningar från baksidan|Antal|Totalt|Antalet misslyckade backend-initierade tvillingläsningar.|Inget|
|c2d.twin.read.size|Svarsstorleken på tvillingavläsningar från den bakre änden|Byte|Medel|Medelvärdet, min och max för alla framgångsrika back-end-initierade tvillingläsningar.|Inget|
|c2d.twin.update.success|Lyckade dubbla uppdateringar från slutet|Antal|Totalt|Antalet lyckade backend-initierade tvillinguppdateringar.|Inget|
|c2d.twin.update.failure|Misslyckade dubbla uppdateringar från backend|Antal|Totalt|Antalet misslyckade backend-initierade tvillinguppdateringar.|Inget|
|c2d.twin.update.size|Storleken på dubbla uppdateringar från baksidan|Byte|Medel|Medelvärdet, min och maxstorleken för alla lyckade back-end-initierade tvillinguppdateringar.|Inget|
|twinQueries.success twinQueries.success twinQueries.success twinQue|Lyckade dubbla frågor|Antal|Totalt|Antalet lyckade tvillingfrågor.|Inget|
|twinQueries.failure|Misslyckade dubbla frågor|Antal|Totalt|Antalet misslyckade tvillingfrågor.|Inget|
|twinQueries.resultSize|Dubbla frågor resultatstorlek|Byte|Medel|Medelvärdet, min och max för resultatstorleken för alla lyckade tvillingfrågor.|Inget|
|jobs.createTwinUpdateJob.success|Framgångsrika skapande av dubbla uppdateringsjobb|Antal|Totalt|Antalet framgångsrika skapande av dubbla uppdateringsjobb.|Inget|
|jobs.createTwinUpdateJob.failure|Misslyckade skapande av dubbla uppdateringsjobb|Antal|Totalt|Antalet misslyckade skapande av dubbla uppdateringsjobb.|Inget|
|jobs.createDirectMethodJob.success|Framgångsrika skapande av metod anrop jobb|Antal|Totalt|Antalet framgångsrika skapande av direkta metoder åkarop jobb.|Inget|
|jobs.createDirectMethodJob.failure|Misslyckade skapande av metod-anropsjobb|Antal|Totalt|Antalet misslyckade skapande av direkta metod-anropsjobb.|Inget|
|jobs.listJobs.success|Lyckade anrop till listjobb|Antal|Totalt|Antalet lyckade anrop till listjobb.|Inget|
|jobs.listJobs.failure|Misslyckade anrop till listjobb|Antal|Totalt|Antalet misslyckade anrop till listjobb.|Inget|
|jobs.cancelJob.success|Lyckade jobbavbokningar|Antal|Totalt|Antalet lyckade samtal för att avbryta ett jobb.|Inget|
|jobs.cancelJob.failure|Misslyckade avbokningar av jobb|Antal|Totalt|Antalet misslyckade samtal för att avbryta ett jobb.|Inget|
|jobs.queryJobs.success|Lyckade jobbfrågor|Antal|Totalt|Antalet lyckade anrop till frågejobb.|Inget|
|jobs.queryJobs.failure|Misslyckade jobbfrågor|Antal|Totalt|Antalet misslyckade anrop till frågejobb.|Inget|
|jobb.slutförda|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inget|
|jobs.failed|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inget|
|d2c.telemetry.ingress.sendThrottle|Antal begränsningsfel|Antal|Totalt|Antal begränsningsfel på grund av enhetsdataflödesbegränsningar|Inget|
|dailyMessageQuotaUsed|Totalt antal meddelanden som används|Antal|Medel|Antal totala meddelanden som används idag|Inget|
|deviceDataUsage|Total användning av enhetsdata|Byte|Totalt|Byte som överförts till och från alla enheter som är anslutna till IotHub|Inget|
|deviceDataUsageV2|Total enhetsdataanvändning (förhandsgranskning)|Byte|Totalt|Byte som överförts till och från alla enheter som är anslutna till IotHub|Inget|
|totaltDeviceCount|Totalt antal enheter (förhandsgranskning)|Antal|Medel|Antal enheter som är registrerade i din IoT-hubb|Inget|
|connectedDeviceCount|Anslutna enheter (förhandsgranskning)|Antal|Medel|Antal enheter som är anslutna till din IoT-hubb|Inget|
|Konfigurationer|Konfigurationsmått|Antal|Totalt|Mått för konfigurationsåtgärder|Inget|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistreringSanpassa|Registreringsförsök|Antal|Totalt|Antal försök att registrera enheter|EtableringServiceName,IotHubName,Status|
|EnhetTilldelningar|Enheter som tilldelats|Antal|Totalt|Antal enheter som tilldelats en IoT-hubb|EtableringServiceName,IotHubName|
|AttestationAttempts|Attesteringsförsök|Antal|Totalt|Antal försök att intyga enheter|EtableringServiceName,Status,Protokoll|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databasKonton

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Lägg till region|Region Tillagd|Antal|Antal|Region Tillagd|Region|
|TillgängligtStora|Tillgänglig lagring|Byte|Totalt|Totalt tillgängligt lagringsutrymme rapporteras vid 5 minuters granularitet|Samlingsnamn,Databasnamn,Region|
|CassandraAnslutningAr|Cassandra Anslutning Nedläggningar|Antal|Totalt|Antal Cassandra-anslutningar som stängdes, rapporterade vid en 1 minuts granularitet|APIType,region,ClosureReason|
|CassandraKeyspaceDelete|Cassandra Keyspace borttaget|Antal|Antal|Cassandra Keyspace borttaget|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraKeyspaceDetgenomputUppdera|Cassandra Keyspace-dataflöde uppdaterat|Antal|Antal|Cassandra Keyspace-dataflöde uppdaterat|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraKeyspaceUppdå|Cassandra Keyspace Uppdaterad|Antal|Antal|Cassandra Keyspace Uppdaterad|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraRequest Laddar|Avgifter för begäran om cassandra|Antal|Totalt|Ru:er som förbrukas för Cassandra-begäranden som gjorts|APIType,Databasnamn,samlingsnamn,region,OperationsType,ResourceType|
|CassandraRequests|Cassandra Förfrågningar|Antal|Antal|Antal cassandra-förfrågningar som gjorts|APIType,Databasnamn,samlingsnamn,region,OperationsType,ResourceType,ErrorCode|
|CassandraTableDelete|Cassandra-tabell har tagits bort|Antal|Antal|Cassandra-tabell har tagits bort|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraTableThroughputUppdera|Cassandra-tabellgenomströmning uppdaterad|Antal|Antal|Cassandra-tabellgenomströmning uppdaterad|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraTableUppdera|Cassandra-tabellen uppdaterad|Antal|Antal|Cassandra-tabellen uppdaterad|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Skapakonto|Skapat konto|Antal|Antal|Skapat konto|Inget|
|DataUpparage|Dataanvändning|Byte|Totalt|Total dataanvändning som rapporteras vid 5 minuters granularitet|Samlingsnamn,Databasnamn,Region|
|Ta bortkonto|Kontot har tagits bort|Antal|Antal|Kontot har tagits bort|Inget|
|DokumentCount|Antal dokument|Antal|Totalt|Totalt antal dokument som rapporterats vid 5 minuters granularitet|Samlingsnamn,Databasnamn,Region|
|DocumentQuota (dokumentquota)|Dokumentkvot|Byte|Totalt|Total lagringskvot som rapporterats vid 5 minuters granularitet|Samlingsnamn,Databasnamn,Region|
|GremlinDatabaseDelete|Gremlin-databasen har tagits bort|Antal|Antal|Gremlin-databasen har tagits bort|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|GremlinDatabaseGenomgenomputUppdera|Gremlin-databasdataflöde uppdaterat|Antal|Antal|Gremlin-databasdataflöde uppdaterat|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinDatabasupdate|Gremlin-databasen uppdaterad|Antal|Antal|Gremlin-databasen uppdaterad|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphDelete|Gremlin Graph borttagen|Antal|Antal|Gremlin Graph borttagen|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|GremlinGraphThroughputUppdera|Gremlin Graph Genomströmning Uppdaterad|Antal|Antal|Gremlin Graph Genomströmning Uppdaterad|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGrafUppda|Gremlin Graph Uppdaterad|Antal|Antal|Gremlin Graph Uppdaterad|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|IndexUsage|Indexanvändning|Byte|Totalt|Total indexanvändning som rapporteras vid 5 minuters granularitet|Samlingsnamn,Databasnamn,Region|
|MetadataRequests|Begäranden om metadata|Antal|Antal|Antal metadatabegäranden. Cosmos DB underhåller systemmetadatasamling för varje konto, som gör att du kan räkna upp samlingar, databaser, etc, och deras konfigurationer, gratis.|Databasnamn,samlingsnamn,region,statuskod,roll|
|MongoCollectionDelete|Mongo-samling har tagits bort|Antal|Antal|Mongo-samling har tagits bort|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoCollectionThroughputUppdera|Mongo Collection Dataflöde Uppdaterad|Antal|Antal|Mongo Collection Dataflöde Uppdaterad|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoCollectionUppdera|Mongo-samlingen uppdaterad|Antal|Antal|Mongo-samlingen uppdaterad|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDBDatabaseUppbygga|Mongo-databasen uppdaterad|Antal|Antal|Mongo-databasen uppdaterad|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDatabaseDelete|Mongo-databasen har tagits bort|Antal|Antal|Mongo-databasen har tagits bort|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoDatabaseGenomströmning|Mongo-databasdataflöde uppdaterat|Antal|Antal|Mongo-databasdataflöde uppdaterat|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoRequest Ladda|Mongo begäran avgift|Antal|Totalt|Mongo-begärandeenheter förbrukas|Databasnamn,samlingsnamn,region,kommandonamn,felkod,status|
|MongoRequests (|Mongo Förfrågningar|Antal|Antal|Antal Mongo-begäranden|Databasnamn,samlingsnamn,region,kommandonamn,felkod,status|
|MongoRequestsCount (Olikartade)|Mongo-begärandehastighet|CountPerSecond|Medel|Antal Mongo-begäranden per sekund|Databasnamn,samlingsnamn,region,kommandonamn,felkod|
|MongoRequestsDelete|Mångås ta bort begäranden|CountPerSecond|Medel|Mongo Delete-begäran per sekund|Databasnamn,samlingsnamn,region,kommandonamn,felkod|
|MongoRequestsInsert|Begärandehastighet för Mongo-infogning|CountPerSecond|Medel|Antal Mongo Insert per sekund|Databasnamn,samlingsnamn,region,kommandonamn,felkod|
|MongoRequestsQuery (Mån)|Mångo-frågebegäranden|CountPerSecond|Medel|Begäran om Mongo-fråga per sekund|Databasnamn,samlingsnamn,region,kommandonamn,felkod|
|MongoRequestsUppdatera|Uppdateringsfrekvens för Mongo|CountPerSecond|Medel|Begäran om Mongo-uppdatering per sekund|Databasnamn,samlingsnamn,region,kommandonamn,felkod|
|NormaliseradRUKonsumtion|Normaliserad RU-förbrukning|Procent|Maximal|Max RU-förbrukningsprocent per minut|Samlingsnamn,Databasnamn,Region|
|EtableradGenomströmning|Etablerat dataflöde|Antal|Maximal|Etablerat dataflöde|Databasnamn,Samlingsnamn|
|RegionFailover|Regionen gick över|Antal|Antal|Regionen gick över|Inget|
|Ta bort Region|Region borttagen|Antal|Antal|Region borttagen|Region|
|Replikeringslatens|P99 Replikeringssvarstid|Millisekunder|Medel|P99 Replikeringsfördröjning över käll- och målområden för geoaktiverat konto|Källregion,Målregion|
|ServerSideLatency|Svarstid på serversidan|Millisekunder|Medel|Svarstid på serversidan|Databasnamn,samlingsnamn,region,connectionmode,operationtyp,PublicAPIType|
|ServiceTillgänglighet|Tjänstens tillgänglighet|Procent|Medel|Kontoförfrågningar tillgänglighet på en timme, dag eller månad granularitet|Inget|
|SqlContainerDelete|Sql-behållare har tagits bort|Antal|Antal|Sql-behållare har tagits bort|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|SqlContainerThroughputUpdate|Sql Container-dataflöde uppdaterat|Antal|Antal|Sql Container-dataflöde uppdaterat|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlContainerUppdera|Sql-behållaren uppdaterad|Antal|Antal|Sql-behållaren uppdaterad|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseDelete|Sql-databas borttagen|Antal|Antal|Sql-databas borttagen|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|SqlDatabaseThroughputUpdate|Sql Database-dataflöde uppdaterat|Antal|Antal|Sql Database-dataflöde uppdaterat|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseUpdate|Sql-databasen har uppdaterats|Antal|Antal|Sql-databasen har uppdaterats|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableDelete|AzureTable-tabell har tagits bort|Antal|Antal|AzureTable-tabell har tagits bort|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|TableTableThroughputUpdate|AzureTable-tabelldataflöde uppdaterat|Antal|Antal|AzureTable-tabelldataflöde uppdaterat|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TabellTabellUppdat|AzureTable-tabellen uppdaterad|Antal|Antal|AzureTable-tabellen uppdaterad|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TotaltRequestUnits|Totalt antal enheter för begärande|Antal|Totalt|Begär enheter som förbrukats|Databasnamn,samlingsnamn,region,statuskod,OperationType,status|
|TotalRequests|Totalt antal förfrågningar|Antal|Antal|Antal inst.|Databasnamn,samlingsnamn,region,statuskod,OperationType,status|
|UpdateAccountKeys|Kontonycklar uppdaterade|Antal|Antal|Kontonycklar uppdaterade|Keytype|
|UpdateAccountNetworkSettings|Nätverksinställningar för konto har uppdaterats|Antal|Antal|Nätverksinställningar för konto har uppdaterats|Inget|
|UpdateAccountReplicationSettings|Inställningar för kontoreplikering har uppdaterats|Antal|Antal|Inställningar för kontoreplikering har uppdaterats|Inget|
|UpdateDiagnosticsSettings|Diagnostikinställningar för kontot har uppdaterats|Antal|Antal|Diagnostikinställningar för kontot har uppdaterats|DiagnosticSettingsName,ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/tjänster

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Transaktionsgräns|Antal transaktioner|Antal|Antal|Totalt antal transaktioner|Transaktionsgräns|
|Antal lyckade länder|Antal slutförda|Antal|Antal|Antal efterföljande transaktioner|Antal lyckade länder|
|Felräkning|Antal misslyckade|Antal|Antal|Antal misslyckade transaktioner|Felräkning|
|FramgångLatens|Svarstid för lyckade svar|Millisekunder|Medel|Svarstid för lyckade transaktioner|Antal lyckade länder|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domäner

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PubliceraSuccessCount|Publicerade evenemang|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Hjälpavsnitt|
|PubliceraFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser kunde inte publiceras i det här avsnittet|ämne,errorType,fel|
|PubliceraUgessLatencyInMs|Publicera svarstid för lyckade uppgifter|Millisekunder|Totalt|Publicera lyckad svarstid i millisekunder|Inget|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchas mot den här händelseprenumerationen|ämne,eventSubscriptionName,DomaineventSubscriptionName|
|LeveransAttemptFailCount|Misslyckade händelser vid leverans|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|ämne,eventSubscriptionName,domaineventSubscriptionName,fel,errorType|
|LeveransSuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser som levererats till den här händelseprenumerationen|ämne,eventSubscriptionName,DomaineventSubscriptionName|
|MålprocesseraVarningInM|Varaktighet för målbearbetning|Millisekunder|Medel|Varaktighet för målbearbetning i millisekunder|ämne,eventSubscriptionName,DomaineventSubscriptionName|
|DroppedEventCount|Tappade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelseprenumerationen|Ämne,eventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount (döddött)|Dead Lettered Händelser|Antal|Totalt|Totalt antal händelser med obeställda bokstäver som matchar den här händelseprenumerationen|Ämne,eventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Ämnen för Microsoft.EventGrid/Event

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PubliceraSuccessCount|Publicerade evenemang|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inget|
|PubliceraFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser kunde inte publiceras i det här avsnittet|ErrorType,Fel|
|OmatchadHändelseCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationerna för det här avsnittet|Inget|
|PubliceraUgessLatencyInMs|Publicera svarstid för lyckade uppgifter|Millisekunder|Totalt|Publicera lyckad svarstid i millisekunder|Inget|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchas mot den här händelseprenumerationen|HändelseBeteckningNamn|
|LeveransAttemptFailCount|Misslyckade händelser vid leverans|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|Fel,errorType,eventSubscriptionName|
|LeveransSuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser som levererats till den här händelseprenumerationen|HändelseBeteckningNamn|
|MålprocesseraVarningInM|Varaktighet för målbearbetning|Millisekunder|Medel|Varaktighet för målbearbetning i millisekunder|HändelseBeteckningNamn|
|DroppedEventCount|Tappade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelseprenumerationen|DropReason,EventSubscriptionName|
|DeadLetteredCount (döddött)|Dead Lettered Händelser|Antal|Totalt|Totalt antal händelser med obeställda bokstäver som matchar den här händelseprenumerationen|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PubliceraSuccessCount|Publicerade evenemang|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inget|
|PubliceraFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser kunde inte publiceras i det här avsnittet|ErrorType,Fel|
|OmatchadHändelseCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationerna för det här avsnittet|Inget|
|PubliceraUgessLatencyInMs|Publicera svarstid för lyckade uppgifter|Millisekunder|Totalt|Publicera lyckad svarstid i millisekunder|Inget|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchas mot den här händelseprenumerationen|HändelseBeteckningNamn|
|LeveransAttemptFailCount|Misslyckade händelser vid leverans|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|Fel,errorType,eventSubscriptionName|
|LeveransSuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser som levererats till den här händelseprenumerationen|HändelseBeteckningNamn|
|MålprocesseraVarningInM|Varaktighet för målbearbetning|Millisekunder|Medel|Varaktighet för målbearbetning i millisekunder|HändelseBeteckningNamn|
|DroppedEventCount|Tappade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelseprenumerationen|DropReason,EventSubscriptionName|
|DeadLetteredCount (döddött)|Dead Lettered Händelser|Antal|Totalt|Totalt antal händelser med obeställda bokstäver som matchar den här händelseprenumerationen|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventAbonnemang

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MatchedEventCount|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchas mot den här händelseprenumerationen|Inget|
|LeveransAttemptFailCount|Misslyckade händelser vid leverans|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|Fel,ErrorType|
|LeveransSuccessCount|Levererade händelser|Antal|Totalt|Totalt antal händelser som levererats till den här händelseprenumerationen|Inget|
|MålprocesseraVarningInM|Varaktighet för målbearbetning|Millisekunder|Medel|Varaktighet för målbearbetning i millisekunder|Inget|
|DroppedEventCount|Tappade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelseprenumerationen|DropReason (av )|
|DeadLetteredCount (döddött)|Dead Lettered Händelser|Antal|Totalt|Totalt antal händelser med obeställda bokstäver som matchar den här händelseprenumerationen|DödBreverReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PubliceraSuccessCount|Publicerade evenemang|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inget|
|PubliceraFailCount|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser kunde inte publiceras i det här avsnittet|ErrorType,Fel|
|OmatchadHändelseCount|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationerna för det här avsnittet|Inget|
|PubliceraUgessLatencyInMs|Publicera svarstid för lyckade uppgifter|Millisekunder|Totalt|Publicera lyckad svarstid i millisekunder|Inget|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namnområden

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Lyckade Begärande|Lyckade förfrågningar|Antal|Totalt|Lyckade begäranden för Microsoft.EventHub.|EntityName,OperationResult|
|ServerErrors|Serverfel.|Antal|Totalt|Serverfel för Microsoft.EventHub.|EntityName,OperationResult|
|UserErrors|Användarfel.|Antal|Totalt|Användarfel för Microsoft.EventHub.|EntityName,OperationResult|
|KvotExceedEdErrors|Kvoten överskred fel.|Antal|Totalt|Kvoten överskred fel för Microsoft.EventHub.|EntityName,OperationResult|
|ThrottledRequests|Begränsade begäranden.|Antal|Totalt|Begränsade begäranden för Microsoft.EventHub.|EntityName,OperationResult|
|Inkommande Begäran|Inkommande begäranden|Antal|Totalt|Inkommande begäranden för Microsoft.EventHub.|Entityname|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft.EventHub.|Entityname|
|UtgåendeMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft.EventHub.|Entityname|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft.EventHub.|Entityname|
|Utgåendebyte|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft.EventHub.|Entityname|
|ActiveConnections (Aktiva Anslutning)|ActiveConnections (Aktiva Anslutning)|Antal|Medel|Totalt antal aktiva anslutningar för Microsoft.EventHub.|Inget|
|AnslutningarÖppnade|Anslutningar öppnade.|Antal|Medel|Anslutningar som öppnats för Microsoft.EventHub.|Entityname|
|Anslutningar Slutna|Anslutningar stängda.|Antal|Medel|Anslutningar stängda för Microsoft.EventHub.|Entityname|
|CaptureBacklog|Fånga eftersläpning.|Antal|Totalt|Fånga eftersläpning för Microsoft.EventHub.|Entityname|
|CapturedMessages|Hämtade meddelanden.|Antal|Totalt|Hämtade meddelanden för Microsoft.EventHub.|Entityname|
|CapturedBytes|Fångade byte.|Byte|Totalt|Hämtade byte för Microsoft.EventHub.|Entityname|
|Storlek|Storlek|Byte|Medel|Storleken på en EventHub i Bytes.|Entityname|
|INREQS (INREQS)|Inkommande begäranden (inaktuell)|Antal|Totalt|Totalt antal inkommande skicka förfrågningar om ett namnområde (inaktuellt)|Inget|
|SUCCREQ (SUCCREQ)|Lyckade begäranden (inaktuella)|Antal|Totalt|Totalt antal lyckade begäranden om ett namnområde (inaktuellt)|Inget|
|FAILREQ (FEL)|Misslyckade begäranden (inaktuell)|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde (inaktuellt)|Inget|
|SVRBSY|Server upptagen fel (föråldrad)|Antal|Totalt|Totalt antal upptagna fel på servern för ett namnområde (inaktuellt)|Inget|
|INTERR (INTERR)|Interna serverfel (inaktuella)|Antal|Totalt|Totalt antal interna serverfel för ett namnområde (inaktuellt)|Inget|
|MISCERR (FEL)|Andra fel (inaktuella)|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde (inaktuellt)|Inget|
|INMSGS (INMSGS)|Inkommande meddelanden (föråldrade) (inaktuella)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde. Det här måttet är inaktuellt. Använd mätvärden för inkommande meddelanden i stället (inaktuellt)|Inget|
|EHINMSGS (PÅ ANDRA SÄTT)|Inkommande meddelanden (inaktuella)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde (inaktuellt)|Inget|
|OUTMSGS (UTSM)|Utgående meddelanden (föråldrade) (inaktuella)|Antal|Totalt|Totalt antal utgående meddelanden för ett namnområde. Det här måttet är inaktuellt. Använd måttet Utgående meddelanden i stället (inaktuellt)|Inget|
|EHOUTMSGS (AVS)|Utgående meddelanden (inaktuella)|Antal|Totalt|Totalt antal utgående meddelanden för ett namnområde (inaktuellt)|Inget|
|EHINMBS (HINMBS)|Inkommande byte (föråldrade) (inaktuella)|Byte|Totalt|Inkommande meddelandeflöde för händelsehubben för ett namnområde. Det här måttet är inaktuellt. Använd mätvärden för inkommande byte i stället (inaktuellt)|Inget|
|EHINBYTES (PÅ ANDRA)|Inkommande byte (inaktuell)|Byte|Totalt|Inkommande meddelandeflöde för händelsehubben för ett namnområde (inaktuellt)|Inget|
|EHOUTMBS (AVS)|Utgående byte (föråldrade) (inaktuella)|Byte|Totalt|Utgående meddelandeflöde för händelsehubben för ett namnområde. Det här måttet är inaktuellt. Använd måttet Utgående byte i stället (inaktuellt)|Inget|
|EHOUTBYTES (PÅ ANDRA SÄTT)|Utgående byte (inaktuella)|Byte|Totalt|Utgående meddelandeflöde för händelsehubben för ett namnområde (inaktuellt)|Inget|
|EHABL (på andra)|Arkivera eftersläpande meddelanden (inaktuella)|Antal|Totalt|Arkivmeddelanden i eventhubb för ett namnområde (inaktuella)|Inget|
|EHAMSGS (på en)|Arkivera meddelanden (inaktuella)|Antal|Totalt|Event Hub arkiverade meddelanden i ett namnområde (inaktuellt)|Inget|
|EHAMBS (på andra sätt)|Arkivera meddelandegenomströmning (föråldrad)|Byte|Totalt|Händelsehubben arkiverat meddelandegenomströmning i ett namnområde (inaktuellt)|Inget|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/kluster

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Lyckade Begärande|Lyckade förfrågningar|Antal|Totalt|Lyckade begäranden för Microsoft.EventHub.|OperationResultat|
|ServerErrors|Serverfel.|Antal|Totalt|Serverfel för Microsoft.EventHub.|OperationResultat|
|UserErrors|Användarfel.|Antal|Totalt|Användarfel för Microsoft.EventHub.|OperationResultat|
|KvotExceedEdErrors|Kvoten överskred fel.|Antal|Totalt|Kvoten överskred fel för Microsoft.EventHub.|OperationResultat|
|ThrottledRequests|Begränsade begäranden.|Antal|Totalt|Begränsade begäranden för Microsoft.EventHub.|OperationResultat|
|Inkommande Begäran|Inkommande begäranden|Antal|Totalt|Inkommande begäranden för Microsoft.EventHub.|Inget|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft.EventHub.|Inget|
|UtgåendeMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft.EventHub.|Inget|
|IncomingBytes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft.EventHub.|Inget|
|Utgåendebyte|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft.EventHub.|Inget|
|ActiveConnections (Aktiva Anslutning)|ActiveConnections (Aktiva Anslutning)|Antal|Medel|Totalt antal aktiva anslutningar för Microsoft.EventHub.|Inget|
|AnslutningarÖppnade|Anslutningar öppnade.|Antal|Medel|Anslutningar som öppnats för Microsoft.EventHub.|Inget|
|Anslutningar Slutna|Anslutningar stängda.|Antal|Medel|Anslutningar stängda för Microsoft.EventHub.|Inget|
|CaptureBacklog|Fånga eftersläpning.|Antal|Totalt|Fånga eftersläpning för Microsoft.EventHub.|Inget|
|CapturedMessages|Hämtade meddelanden.|Antal|Totalt|Hämtade meddelanden för Microsoft.EventHub.|Inget|
|CapturedBytes|Fångade byte.|Byte|Totalt|Hämtade byte för Microsoft.EventHub.|Inget|
|Processor|Processor|Procent|Maximal|CPU-användning för Event Hub-klustret i procent|Roll|
|Tillgängligt Minne|Tillgängligt minne|Procent|Maximal|Tillgängligt minne för Event Hub-klustret i procent av det totala minnet.|Roll|
|Storlek|Storleken på en EventHub i Bytes.|Byte|Medel|Storleken på en EventHub i Bytes.|Roll|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/kluster

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway-begäranden|Antal|Totalt|Antal gateway-begäranden|HttpStatus (HttpStatus)|
|KategoriseradeGatewayRequests|Kategoriserade gateway-begäranden|Antal|Totalt|Antal gateway-begäranden efter kategorier (1xx/2xx/3xx/4xx/5xx)|HttpStatus (HttpStatus)|
|NumActiveArbetare|Antal aktiva arbetstagare|Antal|Maximal|Antal aktiva arbetstagare|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObserveratMetricVärde|Observerat metriskt värde|Antal|Medel|Värdet som beräknas genom automatisk skalning när det körs|MetricTriggerKälla|
|MetricThreshold (metriska)|Tröskelvärde för mått|Antal|Medel|Det konfigurerade tröskelvärdet för automatisk skalning när automatisk skalning kördes.|MetricTriggerRule|
|Observeradkapacitet|Observerad kapacitet|Antal|Medel|Kapaciteten rapporteras till automatisk skalning när den kördes.|Inget|
|ScaleActionsInitiated|Skala åtgärder som initierats|Antal|Totalt|Riktningen på skalningsåtgärden.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|tillgänglighetResultat/tillgänglighetPercentage|Tillgänglighet|Procent|Medel|Procentandel av lyckade tillgänglighetstester|tillgänglighetResultat/namn,tillgänglighetResultat/plats|
|tillgänglighetResultat/antal|Tillgänglighetstester|Antal|Antal|Antal tillgänglighetstester|tillgänglighetResultat/namn,tillgänglighetResultat/plats,tillgänglighetResultat/framgång|
|tillgänglighetResultat/varaktighet|Varaktighet för tillgänglighetstest|Millisekunder|Medel|Varaktighet för tillgänglighetstest|tillgänglighetResultat/namn,tillgänglighetResultat/plats,tillgänglighetResultat/framgång|
|webbläsareTimings/nätverkUnderydning|Anslutningstid för sidbelastning av nätverksanslutning|Millisekunder|Medel|Tid mellan användarbegäran och nätverksanslutning. Inkluderar DNS-sökning och transportanslutning.|Inget|
|webbläsareTimings/bearbetningUnder behandling|Tid för klientbearbetning|Millisekunder|Medel|Tid mellan att ta emot den sista bytet i ett dokument tills DOM läses in. Async-begäranden kan fortfarande bearbetas.|Inget|
|webbläsareTimings/mottagningUnder|Ta emot svarstid|Millisekunder|Medel|Tid mellan den första och sista byte, eller tills frånkoppling.|Inget|
|webbläsareTimings/sendDuration|Skicka tid för begäran|Millisekunder|Medel|Tid mellan nätverksanslutning och mottagande av den första bytet.|Inget|
|webbläsareTimings/totalUnderation|Inläsningstid för webbläsarsidan|Millisekunder|Medel|Tid från användarbegäran till DOM, formatmallar, skript och bilder läses in.|Inget|
|beroenden/antal|Beroendeanrop|Antal|Antal|Antal anrop som gjorts av programmet till externa resurser.|beroende/typ,beroende/performanceBucket,dependency/success,dependency/target,dependency/resultCode,operation/synthetic,cloud/roleInstance,cloud/roleName|
|beroenden/varaktighet|Beroendevaraktighet|Millisekunder|Medel|Varaktigheten av anrop som görs av programmet till externa resurser.|beroende/typ,beroende/performanceBucket,dependency/success,dependency/target,dependency/resultCode,operation/synthetic,cloud/roleInstance,cloud/roleName|
|beroenden/misslyckades|Fel i beroendeanrop|Antal|Antal|Antal misslyckade beroendeanrop som gjorts av programmet till externa resurser.|beroende/typ,beroende/performanceBucket,dependency/success,dependency/target,dependency/resultCode,operation/synthetic,cloud/roleInstance,cloud/roleName|
|pageVisningar/antal|Sidvisningar|Antal|Antal|Antal sidvisningar.|operation/syntetiskt,moln/rollnamn|
|pageVisningar/varaktighet|Inläsningstid för sidvy|Millisekunder|Medel|Inläsningstid för sidvy|operation/syntetiskt,moln/rollnamn|
|performanceCounters/requestExecutionTime|HTTP-körningstid för HTTP-begäran|Millisekunder|Medel|Körningstid för den senaste begäran.|moln/rollIntance|
|performanceCounters/requestsInQueue|HTTP-begäranden i programkö|Antal|Medel|Längden på kön för programbegäran.|moln/rollIntance|
|performanceCounters/requestsPerSecond|HTTP-begäran hastighet|CountPerSecond|Medel|Betygsätt alla begäranden till programmet per sekund från ASP.NET.|moln/rollIntance|
|performanceCounters/exceptionsPerSecond|Undantagsfrekvens|CountPerSecond|Medel|Antal hanterade och ohanterade undantag som rapporteras till fönster, inklusive .NET-undantag och ohanterade undantag som konverteras till .NET-undantag.|moln/rollIntance|
|performanceCounters/processIOBytesPerSecond|Process-IO-hastighet|BytesPerSecond|Medel|Totalt antal byte per sekund som lästs och skrivits till filer, nätverk och enheter.|moln/rollIntance|
|performanceCounters/processCpuPercentage|Bearbeta CPU|Procent|Medel|Den procentandel av förfluten tid som alla processtrådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger prestanda för w3wp-processen ensam.|moln/rollIntance|
|performanceCounters/processorCpuPercentage|Processortid|Procent|Medel|Den procentandel av tiden som processorn tillbringar i trådar som inte är inaktiva.|moln/rollIntance|
|performanceCounters/memoryTillgängligaBytes|Tillgängligt minne|Byte|Medel|Fysiskt minne omedelbart tillgängligt för allokering till en process eller för systemanvändning.|moln/rollIntance|
|performanceCounters/processPrivateBytes|Bearbeta privata byte|Byte|Medel|Minne som uteslutande tilldelats det övervakade programmets processer.|moln/rollIntance|
|begäranden/varaktighet|Svarstid för servern|Millisekunder|Medel|Tid mellan att ta emot en HTTP-begäran och avsluta sändningen av svaret.|begäran/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|begäranden/antal|Serverbegäranden|Antal|Antal|Antal HTTP-begäranden har slutförts.|begäran/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|begäranden/misslyckades|Misslyckade förfrågningar|Antal|Antal|Antalet HTTP-begäranden som har markerats som misslyckade. I de flesta fall är dessa begäranden med en svarskod >= 400 och inte lika med 401.|begäran/performanceBucket,request/resultCode,request/success,operation/synthetic,cloud/roleInstance,cloud/roleName|
|förfrågningar/ränta|Serverbegäranshastighet|CountPerSecond|Medel|Hastighet för serverbegäranden per sekund|begäran/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|undantag/antal|Undantag|Antal|Antal|Kombinerat antal av alla undantag som inte är raddragna.|moln/rollNamn,moln/rollInfance,klient/typ|
|undantag/webbläsare|Webbläsarundantag|Antal|Antal|Antal undantag som inte har genererats i webbläsaren.|klient/ärServer,moln/rollNamn|
|undantag/server|Serverundantag|Antal|Antal|Antal undantag som inte har genererats i serverprogrammet.|klient/ärServer,moln/rollNamn,moln/rollInfance|
|spår/antal|Traces|Antal|Antal|Antal spårdokument|trace/severityLevel,operation/synthetic,cloud/roleName,cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedDeviceCount|Totalt antal anslutna enheter|Antal|Medel|Antal enheter som är anslutna till IoT Central|Inget|
|c2d.property.read.success|Lyckad enhetsegenskap läser från IoT Central|Antal|Totalt|Antalet lyckade egendom läser initierad från IoT Central|Inget|
|c2d.property.read.failure|Misslyckad enhetsegenskap läser från IoT Central|Antal|Totalt|Antalet misslyckade egenskapsläsningar som initierats från IoT Central|Inget|
|d2c.property.read.success|Lyckad enhetsegenskap läser från enheter|Antal|Totalt|Antalet lyckade egenskapsavläsningar som initierats från enheter|Inget|
|d2c.property.read.failure|Misslyckad enhetsegenskap läser från enheter|Antal|Totalt|Antalet misslyckade egenskapsavläsningar som initierats från enheter|Inget|
|c2d.property.update.success|Lyckade enhetsegenskapsuppdateringar från IoT Central|Antal|Totalt|Antalet lyckade egenskapsuppdateringar som initierats från IoT Central|Inget|
|c2d.property.update.failure|Misslyckade enhetsegenskapsuppdateringar från IoT Central|Antal|Totalt|Antalet misslyckade egenskapsuppdateringar som initierats från IoT Central|Inget|
|d2c.property.update.success|Lyckade enhetsegenskapsuppdateringar från enheter|Antal|Totalt|Antalet lyckade egenskapsuppdateringar som initierats från enheter|Inget|
|d2c.property.update.failure|Misslyckade enhetsegenskapsuppdateringar från enheter|Antal|Totalt|Antalet misslyckade egenskapsuppdateringar som initierats från enheter|Inget|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/valv

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Totalt antal service-api-träffar|Antal|Antal|Antal totalt antal service-api-träffar|ActivityType,Aktivitetsnamn|
|ServiceApiLatency|Svarstid för tjänstens api|Millisekunder|Medel|Övergripande svarstid för tjänst api-begäranden|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|ServiceApiResult|Totalt antal api-resultat för tjänsten|Antal|Antal|Antal totala service-api-resultat|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|MättnadSkolåda|Övergripande Vault Mättnad|Procent|Medel|Valvkapacitet som används|ActivityType,ActivityName,TransactionType|
|Tillgänglighet|Övergripande arkivtillgänglighet|Procent|Medel|Arkiv begär tillgänglighet|ActivityType,ActivityName,StatusCode,StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Kluster

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CacheUtilisering|Cacheanvändning|Procent|Medel|Utnyttjandenivå i klusteromfattningen|Inget|
|FrågaUnderation|Varaktighet för fråga|Millisekunder|Medel|Frågornas varaktighet i sekunder|Frågestatus|
|IntagUtilisering|Utnyttjande av förtäring|Procent|Medel|Förhållandet mellan använda intagsplatser i klustret|Inget|
|Keepalive|Håll dig vid liv|Antal|Medel|Sanity-kontrollen anger att klustret svarar på frågor|Inget|
|IntagVolumeInMB|Intagsvolym (i MB)|Antal|Totalt|Total volym intappade data till klustret (i MB)|Databas|
|IntagLatencyInSeconds|Svarstid för inmatning (i sekunder)|Sekunder|Medel|Inmatningstid från källan (t.ex. meddelande finns i EventHub) till klustret på några sekunder|Inget|
|Händelser BearbetasForEventHubs|Händelser som bearbetas (för event/IoT-hubbar)|Antal|Totalt|Antal händelser som bearbetas av klustret vid intag från Event/IoT Hub|EventStatus|
|IntagResultat|Intag resultat|Antal|Antal|Antal intagsoperationer|IntagResultDetaljer|
|Processor|Processor|Procent|Medel|CPU-utnyttjandenivå|Inget|
|ContinuousExportNumOfRecordsExported|Kontinuerlig export – antal exporterade poster|Antal|Totalt|Antal exporterade poster, avfyrade för varje lagringsartefakt som skrivits under exportåtgärden|ContinuousExportName,Databas|
|ExportUtilisering|Exportutnyttjande|Procent|Maximal|Exportutnyttjande|Inget|
|Kontinuerligt Exportera väntande konto|Kontinuerligt antal väntande export|Antal|Maximal|Antalet väntande kontinuerliga exportjobb som är klara för körning|Inget|
|Kontinuerligt ExporteraMaxLatenessMinutes|Kontinuerlig export Max lateness|Antal|Maximal|Förseningen (i minuter) som rapporteras av de kontinuerliga exportjobben i klustret|Inget|
|Kontinuerligt ExporteraResult|Kontinuerligt exportresultat|Antal|Antal|Anger om kontinuerlig export lyckades eller misslyckades|ContinuousExportName,Resultat,Databas|
|StreamingIngestDuration|Varaktighet för strömmande intning|Millisekunder|Medel|Streaming intagstid i millisekunder|Inget|
|Strömmandedatatdata|Datahastighet för strömmande intning|Antal|Medel|Datahastighet för direktuppspelning (MB per sekund)|Inget|
|SteamingIngestRequestRate|Streaming Ingest begärsthastighet|Antal|RäntaRequestsPerSecond|Frekvensen för begäran om direktuppspelning (begäranden per sekund)|Inget|
|StreamingIngestResults|Streaming Intest Resultat|Antal|Medel|Resultat för direktuppspelning|Resultat|
|TotalNumberOfConcurrentQueries|Totalt antal samtidiga frågor|Antal|Totalt|Totalt antal samtidiga frågor|Inget|
|Totalt Antal RuttnadeQueries|Totalt antal begränsade frågor|Antal|Totalt|Totalt antal begränsade frågor|Inget|
|Totalt AntalHundrastrottade Kommendanter|Totalt antal begränsade kommandon|Antal|Totalt|Totalt antal begränsade kommandon|Kommandotyp|
|Totalt AntalExtents|Totalt antal omfattningar|Antal|Totalt|Totalt antal data omfattningar|Inget|
|InstansCount|Antal instanser|Antal|Medel|Totalt antal instanser|Inget|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/arbetsflöden

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startar|Antal|Totalt|Antal arbetsflödeskörningar har startats.|Inget|
|RunsCompleted|Slutförda körningar|Antal|Totalt|Antal slutförda arbetsflödeskörningar.|Inget|
|RunsSucceeded|Körningar lyckades|Antal|Totalt|Antalet arbetsflödeskörningar lyckades.|Inget|
|RunsFailed|Det gick inte att köra körningar|Antal|Totalt|Antalet arbetsflödeskörningar misslyckades.|Inget|
|RunsKanlade|Körs annullerade|Antal|Totalt|Antal arbetsflödeskörningar har avbrutits.|Inget|
|RunLatency (körl)|Kör svarstid|Sekunder|Medel|Svarstiden för slutförda arbetsflödeskörningar.|Inget|
|RunSuccessLatency|Kör svarstid för lyckade svar|Sekunder|Medel|Svarstiden för efterföljande arbetsflödeskörningar.|Inget|
|RunThrottledEvents|Köra begränsade händelser|Antal|Totalt|Antal arbetsflödesåtgärder eller utlösarbegränsningar.|Inget|
|RunStartThrottledEvents|Begränsa antalet begränsade händelser för körning|Antal|Totalt|Antal händelser som körs i arbetsflödeskörningen begränsade.|Inget|
|RunFailurePercentage|Kör felprocent|Procent|Totalt|Det gick inte att köra arbetsflöden.|Inget|
|ÅtgärderStartad|Åtgärder har startats |Antal|Totalt|Antal arbetsflödesåtgärder har startats.|Inget|
|ÅtgärderKomt|Slutförda åtgärder |Antal|Totalt|Antal arbetsflödesåtgärder som slutförts.|Inget|
|Åtgärder som|Åtgärderna lyckades |Antal|Totalt|Antalet arbetsflödesåtgärder lyckades.|Inget|
|ÅtgärderMisslade|Åtgärderna misslyckades |Antal|Totalt|Antalet arbetsflödesåtgärder misslyckades.|Inget|
|ÅtgärderSkipade|Åtgärder som hoppas över |Antal|Totalt|Antal arbetsflödesåtgärder som hoppas över.|Inget|
|ActionLatency (ÅtgärdLatens)|Svarstid för åtgärder |Sekunder|Medel|Svarstid för slutförda arbetsflödesåtgärder.|Inget|
|ÅtgärdUppsugning|Svarstid för åtgärden lyckades |Sekunder|Medel|Svarstid för efterföljande arbetsflödesåtgärder.|Inget|
|ActionThrottledEvents|Åtgärder begränsade händelser|Antal|Totalt|Antal händelser som begränsar arbetsflödesåtgärden..|Inget|
|TriggersStarted|Utlösare startade |Antal|Totalt|Antal arbetsflödesutlösare har startats.|Inget|
|TriggersCompleted|Utlösare har slutförts |Antal|Totalt|Antal arbetsflödesutlösare som slutförts.|Inget|
|TriggersSucceeded|Utlösare lyckades |Antal|Totalt|Antalet arbetsflödesutlösare lyckades.|Inget|
|TriggersFailed|Utlösare misslyckades |Antal|Totalt|Antalet arbetsflödesutlösare misslyckades.|Inget|
|TriggersSkipped|Överhoppade utlösare|Antal|Totalt|Antal arbetsflödesutlösare som hoppas över.|Inget|
|UtlösareAvfyrade|Avfyrade utlösare |Antal|Totalt|Antal avfyrade arbetsflödesutlösare.|Inget|
|TriggerLatency|Svarstid för utlösare |Sekunder|Medel|Svarstid för slutförda arbetsflödesutlösare.|Inget|
|TriggerFireLatency|Svarstid för utlösande utlösare |Sekunder|Medel|Svarstid för avfyrade arbetsflödesutlösare.|Inget|
|TriggerSuccessLatency|Svarstid för utlösarlyckades |Sekunder|Medel|Svarstid för efterföljande arbetsflödesutlösare.|Inget|
|TriggerThrottledEvents|Utlösarbegränsningar|Antal|Totalt|Antal händelser som utlöses av arbetsflödesutlösare.|Inget|
|Fakturerbara ÅtgärderExecutions|Fakturerbara åtgärdskörningar|Antal|Totalt|Antal arbetsflödesåtgärder som faktureras.|Inget|
|FakturerbaraTriggerUtskurningar|Fakturerbara utlösarkörningar|Antal|Totalt|Antal arbetsflödesutlösare som faktureras.|Inget|
|Totalt FakturerbaraUtredningar|Totalt fakturerbara avrättningar|Antal|Totalt|Antal arbetsflödeskörningar som faktureras.|Inget|
|BillingUsageNativeOperation|Faktureringsanvändning för körning i inbyggda operationer|Antal|Totalt|Antal körningar som körs i inbyggda.|Inget|
|BillingUsageStandardConnector|Faktureringsanvändning för standardappkörningar|Antal|Totalt|Antal standardappkörningar som faktureras.|Inget|
|FaktureringUsageStorageKonsumtion|Faktureringsanvändning för körning av lagringsförbrukning|Antal|Totalt|Antal körningar för lagringsförbrukning som faktureras.|Inget|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceMiljöer

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Startar|Antal|Totalt|Antal arbetsflödeskörningar har startats.|Inget|
|RunsCompleted|Slutförda körningar|Antal|Totalt|Antal slutförda arbetsflödeskörningar.|Inget|
|RunsSucceeded|Körningar lyckades|Antal|Totalt|Antalet arbetsflödeskörningar lyckades.|Inget|
|RunsFailed|Det gick inte att köra körningar|Antal|Totalt|Antalet arbetsflödeskörningar misslyckades.|Inget|
|RunsKanlade|Körs annullerade|Antal|Totalt|Antal arbetsflödeskörningar har avbrutits.|Inget|
|RunLatency (körl)|Kör svarstid|Sekunder|Medel|Svarstiden för slutförda arbetsflödeskörningar.|Inget|
|RunSuccessLatency|Kör svarstid för lyckade svar|Sekunder|Medel|Svarstiden för efterföljande arbetsflödeskörningar.|Inget|
|RunThrottledEvents|Köra begränsade händelser|Antal|Totalt|Antal arbetsflödesåtgärder eller utlösarbegränsningar.|Inget|
|RunStartThrottledEvents|Begränsa antalet begränsade händelser för körning|Antal|Totalt|Antal händelser som körs i arbetsflödeskörningen begränsade.|Inget|
|RunFailurePercentage|Kör felprocent|Procent|Totalt|Det gick inte att köra arbetsflöden.|Inget|
|ÅtgärderStartad|Åtgärder har startats |Antal|Totalt|Antal arbetsflödesåtgärder har startats.|Inget|
|ÅtgärderKomt|Slutförda åtgärder |Antal|Totalt|Antal arbetsflödesåtgärder som slutförts.|Inget|
|Åtgärder som|Åtgärderna lyckades |Antal|Totalt|Antalet arbetsflödesåtgärder lyckades.|Inget|
|ÅtgärderMisslade|Åtgärderna misslyckades |Antal|Totalt|Antalet arbetsflödesåtgärder misslyckades.|Inget|
|ÅtgärderSkipade|Åtgärder som hoppas över |Antal|Totalt|Antal arbetsflödesåtgärder som hoppas över.|Inget|
|ActionLatency (ÅtgärdLatens)|Svarstid för åtgärder |Sekunder|Medel|Svarstid för slutförda arbetsflödesåtgärder.|Inget|
|ÅtgärdUppsugning|Svarstid för åtgärden lyckades |Sekunder|Medel|Svarstid för efterföljande arbetsflödesåtgärder.|Inget|
|ActionThrottledEvents|Åtgärder begränsade händelser|Antal|Totalt|Antal händelser som begränsar arbetsflödesåtgärden..|Inget|
|TriggersStarted|Utlösare startade |Antal|Totalt|Antal arbetsflödesutlösare har startats.|Inget|
|TriggersCompleted|Utlösare har slutförts |Antal|Totalt|Antal arbetsflödesutlösare som slutförts.|Inget|
|TriggersSucceeded|Utlösare lyckades |Antal|Totalt|Antalet arbetsflödesutlösare lyckades.|Inget|
|TriggersFailed|Utlösare misslyckades |Antal|Totalt|Antalet arbetsflödesutlösare misslyckades.|Inget|
|TriggersSkipped|Överhoppade utlösare|Antal|Totalt|Antal arbetsflödesutlösare som hoppas över.|Inget|
|UtlösareAvfyrade|Avfyrade utlösare |Antal|Totalt|Antal avfyrade arbetsflödesutlösare.|Inget|
|TriggerLatency|Svarstid för utlösare |Sekunder|Medel|Svarstid för slutförda arbetsflödesutlösare.|Inget|
|TriggerFireLatency|Svarstid för utlösande utlösare |Sekunder|Medel|Svarstid för avfyrade arbetsflödesutlösare.|Inget|
|TriggerSuccessLatency|Svarstid för utlösarlyckades |Sekunder|Medel|Svarstid för efterföljande arbetsflödesutlösare.|Inget|
|TriggerThrottledEvents|Utlösarbegränsningar|Antal|Totalt|Antal händelser som utlöses av arbetsflödesutlösare.|Inget|
|IntegrationServiceMiljöArbetsflödeProcessorUsage|Arbetsflödesprocessoranvändning för integrationstjänstmiljö|Procent|Medel|Arbetsflödesprocessoranvändning för integrationstjänstmiljö.|Inget|
|IntegrationServiceMiljöArbetsflödeMemoryUsage|Arbetsflödesminnesanvändning för integrationstjänstmiljö|Procent|Medel|Arbetsflödesminnesanvändning för integrationstjänstmiljö.|Inget|
|IntegrationServiceMiljöAnslutningsprocessorAnvändare|Anslutningsprocessoranvändning för integrationstjänstmiljö|Procent|Medel|Anslutningsprocessoranvändning för integrationstjänstmiljö.|Inget|
|IntegrationServiceMiljöAnslutningarMemoryUsage|Anslutningsminnesanvändning för integrationstjänstmiljö|Procent|Medel|Anslutningsminnesanvändning för integrationstjänstmiljö.|Inget|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/arbetsytor

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Avbrutna körningar|Avbrutna körningar|Antal|Totalt|Antal avbrutna körningar för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Avbryt begärda körningar|Avbryt begärda körningar|Antal|Totalt|Antal körningar där avbokning begärdes för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Slutförda körningar|Slutförda körningar|Antal|Totalt|Antal slutförda körningar för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Misslyckade körningar|Misslyckade körningar|Antal|Totalt|Antalet körningar misslyckades för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Slutföra körningar|Slutföra körningar|Antal|Totalt|Antal körningar som angetts för att slutföra den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Svarar inte körningar|Svarar inte körningar|Antal|Totalt|Antal körningar som inte svarar för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Startar inte körningar|Startar inte körningar|Antal|Totalt|Antal körningar i inte startat tillstånd för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Förbereda körningar|Förbereda körningar|Antal|Totalt|Antal körningar som förbereder sig för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Etableringskörningar|Etableringskörningar|Antal|Totalt|Antal körningar som etablerar för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Köade körningar|Köade körningar|Antal|Totalt|Antal körningar som står i kö för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Startade körningar|Startade körningar|Antal|Totalt|Antal körningar som startats för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Starta körningar|Starta körningar|Antal|Totalt|Antal körningar som startats för den här arbetsytan|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Fel|Fel|Antal|Totalt|Antal körningsfel på den här arbetsytan|Scenario|
|Varningar|Varningar|Antal|Totalt|Antal körningsvarningar på den här arbetsytan|Scenario|
|Modellregistret lyckades|Modellregistret lyckades|Antal|Totalt|Antal modellregistreringar som lyckades på den här arbetsytan|Scenario|
|Det gick inte att registrera modellregistret|Det gick inte att registrera modellregistret|Antal|Totalt|Antal modellregistreringar som misslyckades på den här arbetsytan|Scenario,StatusCode|
|Modellut distribuera igång|Modellut distribuera igång|Antal|Totalt|Antal modelldistributioner som startats på den här arbetsytan|Scenario|
|Modellut distribuera lyckades|Modellut distribuera lyckades|Antal|Totalt|Antal modelldistributioner som lyckades på den här arbetsytan|Scenario|
|Det gick inte att distribuera modeller|Det gick inte att distribuera modeller|Antal|Totalt|Antal modelldistributioner som misslyckades på den här arbetsytan|Scenario,StatusCode|
|Totalt antal noder|Totalt antal noder|Antal|Medel|Antal totala noder. Den här summan inkluderar några av aktiva noder, inaktiva noder, oanpassade noder, föregripna noder, lämnar noder|Scenario,ClusterName|
|Aktiva noder|Aktiva noder|Antal|Medel|Antal Acitve-noder. Det här är noderna som aktivt kör ett jobb.|Scenario,ClusterName|
|Inaktiva noder|Inaktiva noder|Antal|Medel|Antal inaktiva noder. Inaktiva noder är noder som inte kör några jobb men som kan acceptera nytt jobb om tillgängligt.|Scenario,ClusterName|
|Oersättliga noder|Oersättliga noder|Antal|Medel|Antal oanvändbara noder. Outtämbara noder fungerar inte på grund av något olösligt problem. Azure kommer att återvinna dessa noder.|Scenario,ClusterName|
|Föregripna noder|Föregripna noder|Antal|Medel|Antal föregripna noder. Dessa noder är noder med låg prioritet som tas bort från den tillgängliga nodpoolen.|Scenario,ClusterName|
|Lämna noder|Lämna noder|Antal|Medel|Antal lämnade noder. Att lämna noder är de noder som just avslutat bearbetningen av ett jobb och som går till inaktivt tillstånd.|Scenario,ClusterName|
|Totalt kärnor|Totalt kärnor|Antal|Medel|Antal totala kärnor|Scenario,ClusterName|
|Aktiva kärnor|Aktiva kärnor|Antal|Medel|Antal aktiva kärnor|Scenario,ClusterName|
|Inaktiva kärnor|Inaktiva kärnor|Antal|Medel|Antal inaktiva kärnor|Scenario,ClusterName|
|Oersättliga kärnor|Oersättliga kärnor|Antal|Medel|Antal oanvändbara kärnor|Scenario,ClusterName|
|Föregripna kärnor|Föregripna kärnor|Antal|Medel|Antal föregripna kärnor|Scenario,ClusterName|
|Lämna kärnor|Lämna kärnor|Antal|Medel|Antal lämnar kärnor|Scenario,ClusterName|
|Procent av kvotutnyttjande|Procent av kvotutnyttjande|Antal|Medel|Procent av kvoten utnyttjas|Scenario,ClusterName,VmFamilyName,VmPriority|
|CpuUtilization (CpuUtilization)|CpuUtilization (CpuUtilization)|Antal|Medel|CPU (förhandsgranskning)|Scenario,runId,NodeId,CreatedTime|
|GpuUtilization (GpuUtilization)|GpuUtilization (GpuUtilization)|Antal|Medel|GPU (förhandsgranskning)|Scenario,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/konton

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Antal|Antal|Antal API-anrop|ApiCategory,ApiName,ResultType,ResponseCode|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Api:ernas tillgänglighet|ApiCategory,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Utgående|Utgående|Byte|Totalt|Mängden Egress-data, i byte.|OutputFormat|
|FramgångE2ELatency|Lyckades avsluta svarstiden|Millisekunder|Medel|Den genomsnittliga svarstiden för lyckade begäranden i millisekunder.|OutputFormat|
|Begäranden|Begäranden|Antal|Totalt|Begäranden till en slutpunkt för direktuppspelning.|OutputFormat,httpstatuscode,errorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TillgångQuota|Tillgångskvot|Antal|Medel|Hur många tillgångar som tillåts för kontot för aktuella medietjänster|Inget|
|Tillgångsräkning|Antal tillgångar|Antal|Medel|Hur många tillgångar som redan har skapats i kontot för aktuella medietjänster|Inget|
|TillgångQuotaUsedPercentage|Använd procentandel för tillgångskvot|Procent|Medel|Använd procentandel för använda tillgången i kontot för aktuell medietjänst|Inget|
|ContentKeyPolicyQuota|Policykvot för innehållsnyckel|Antal|Medel|Hur många innehållsnyckelpoliser som tillåts för aktuellt medietjänstkonto|Inget|
|InnehållKeyPolicyCount|Antal principer för innehållsnyckel|Antal|Medel|Hur många innehållsnyckelprinciper som redan har skapats i kontot för aktuella medietjänster|Inget|
|ContentKeyPolicyQuotaUsedPercentage|Procentuell kvot för innehållsnyckelprincip|Procent|Medel|Innehållsnyckelprincip som används procent i kontot för aktuella medietjänster|Inget|
|StreamingPolicyQuota|Kvot för direktuppspelningsprincip|Antal|Medel|Hur många direktuppspelningsprinciper tillåts för aktuellt medietjänstkonto|Inget|
|StreamingPolicyCount|Antal streamingprinciper|Antal|Medel|Hur många direktuppspelningsprinciper som redan har skapats i det aktuella medietjänstkontot|Inget|
|StreamingPolicyQuotaUsedPercentage|Datauppspelningsprincipkvot använd procent|Procent|Medel|Streamingprincip som används procent i aktuellt medietjänstkonto|Inget|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TillgångarKonverterad|Konverterade tillgångar|Antal|Totalt|Totalt antal konverterade tillgångar|AppId,ResourceId,SDKVersion|
|ActiveRenderingSessions|Aktiva renderingssessioner|Antal|Totalt|Totalt antal aktiva renderingssessioner|AppId,ResourceId,SessionType,SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volymer

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Genomsnittligläsning|Genomsnittlig läsfördröjning|Millisekunder|Medel|Genomsnittlig lässvarstid i millisekunder per åtgärd|Inget|
|AverageWriteLatency|Genomsnittlig skrivfördröjning|Millisekunder|Medel|Genomsnittlig skrivsvarstid i millisekunder per åtgärd|Inget|
|VolumeLogicalSize|Förbrukad volymstorlek|Byte|Medel|Logiskt storlek på volymen (använda byte)|Inget|
|VolumeSnapshotSize|Storlek på ögonblicksbilder av volym|Byte|Medel|Storlek på alla ögonblicksbilder i volym|Inget|
|ReadIops (läs)|Läs iops|CountPerSecond|Medel|Läs in/ut-åtgärder per sekund|Inget|
|WriteIops|Skriv iops|CountPerSecond|Medel|Skriv in/ut-åtgärder per sekund|Inget|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Pool allokerad till volymstorlek|Byte|Medel|Allokerad använd storlek på poolen|Inget|
|VolumePoolTotalLogicalSize|Förbrukad poolstorlek|Byte|Medel|Summan av den logiska storleken på alla volymer som tillhör poolen|Inget|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesSentRate|Skickade byte|Byte|Totalt|Antal byte som nätverksgränssnittet har skickat|Inget|
|ByteReceivedRate|Mottagna byte|Byte|Totalt|Antal byte som nätverksgränssnittet har fått|Inget|
|PacketsSentRate|Skickade paket|Antal|Totalt|Antal paket som nätverksgränssnittet har skickat|Inget|
|PaketReceivedRate|Mottagna paket|Antal|Totalt|Antal paket som nätverksgränssnittet har tagit emot|Inget|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|Tillgänglighet för datasökväg|Antal|Medel|Genomsnittlig tillgänglighet för datasökväg för belastningsutjämnare per varaktighet|FrontendIPAddress,FrontendPort|
|Diptillgänglighet|Status för hälsoavsökning|Antal|Medel|Genomsnittlig belastningsutjämnad hälsoavsökningsstatus per varaktighet för tid|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal överförda byte inom tidsperioden|FrontendIPAdress,FrontendPort,Riktning|
|PacketCount (Antal)|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tidsperioden|FrontendIPAdress,FrontendPort,Riktning|
|SYNCount (olikartade)|SYN Räkna|Antal|Totalt|Totalt antal SYN-paket som överförts inom tidsperioden|FrontendIPAdress,FrontendPort,Riktning|
|SnatConnectionCount|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tidsperioden|FrontendIPAdress,BackendIPAddress,ConnectionState|
|AllokeradeSnatPorts|Allokerade SNAT-portar (förhandsversion)|Antal|Totalt|Totalt antal SNAT-portar som tilldelats inom tidsperioden|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|UsedSnatPorts|Används SNAT-portar (förhandsversion)|Antal|Totalt|Totalt antal SNAT-portar som används inom tidsperioden|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FrågaVolym|Fråga volym|Antal|Totalt|Antal frågor som har serverats för en DNS-zon|Inget|
|RecordSetCount (RecordSetCount)|Antal postuppsättningar|Antal|Maximal|Antal postuppsättningar i en DNS-zon|Inget|
|RecordSetCapacityUtilization|Kapacitetsutnyttjande för postuppsättning|Procent|Maximal|Procent av postuppsättningskapaciteten som används av en DNS-zon|Inget|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAdresser

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PacketsInDDoS|Inkommande paket DDoS|CountPerSecond|Maximal|Inkommande paket DDoS|Inget|
|PacketsDroppedDDoS|Inkommande paket tappade DDoS|CountPerSecond|Maximal|Inkommande paket tappade DDoS|Inget|
|PaketForwardadeDDoS|Inkommande paket vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande paket vidarebefordrade DDoS|Inget|
|TCPPacketsInDDoS|Inkommande TCP-paket DDoS|CountPerSecond|Maximal|Inkommande TCP-paket DDoS|Inget|
|TCPPacketsDroppedDDos|Inkommande TCP-paket tappade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket tappade DDoS|Inget|
|TCPPacketsForwardedDDoS|Inkommande TCP-paket vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket vidarebefordrade DDoS|Inget|
|UDPPacketsInDDoS|Inkommande UDP-paket DDoS|CountPerSecond|Maximal|Inkommande UDP-paket DDoS|Inget|
|UDPPacketsDroppedDDos|Inkommande UDP-paket tappade DDoS|CountPerSecond|Maximal|Inkommande UDP-paket tappade DDoS|Inget|
|UDPPacketsForwardedDDoS|Inkommande UDP-paket vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande UDP-paket vidarebefordrade DDoS|Inget|
|BytesInDDoS|Inkommande byte DDoS|BytesPerSecond|Maximal|Inkommande byte DDoS|Inget|
|BytesDroppedDDoS|Inkommande byte tappade DDoS|BytesPerSecond|Maximal|Inkommande byte tappade DDoS|Inget|
|BytesForwardedDDoS|Inkommande byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande byte vidarebefordrade DDoS|Inget|
|TCPBytesInDDoS|Inkommande TCP-byte DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte DDoS|Inget|
|TCPBytesDroppedDDoS|Inkommande TCP-byte tappade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte tappade DDoS|Inget|
|TCPBytesForwardedDDoS|Inkommande TCP-byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte vidarebefordrade DDoS|Inget|
|UDPBytesInDDoS|Inkommande UDP-byte DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte DDoS|Inget|
|UDPBytesDroppedDDoS|Inkommande UDP-byte tappade DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte tappade DDoS|Inget|
|UDPBytesForwardedDDoS|Inkommande UDP-byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte vidarebefordrade DDoS|Inget|
|IfUnderDDosAttack|Under DDoS-attack eller inte|Antal|Maximal|Under DDoS-attack eller inte|Inget|
|DDoSTriggerTCPPackets|Inkommande TCP-paket för att utlösa DDoS-begränsning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-begränsning|Inget|
|DDoSTriggerUDPPackets|Inkommande UDP-paket för att utlösa DDoS-begränsning|CountPerSecond|Maximal|Inkommande UDP-paket för att utlösa DDoS-begränsning|Inget|
|DDoSTriggerSYNPackets|Inkommande SYN-paket för att utlösa DDoS-begränsning|CountPerSecond|Maximal|Inkommande SYN-paket för att utlösa DDoS-begränsning|Inget|
|VipAvailability|Tillgänglighet för datasökväg|Antal|Medel|Genomsnittlig IP-adresstillgänglighet per varaktighet|Port|
|ByteCount|Antal byte|Antal|Totalt|Totalt antal överförda byte inom tidsperioden|Port,Riktning|
|PacketCount (Antal)|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tidsperioden|Port,Riktning|
|SynCount (olikartade)|SYN Räkna|Antal|Totalt|Totalt antal SYN-paket som överförts inom tidsperioden|Port,Riktning|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tur och retur för Ping till en virtuell dator|Millisekunder|Medel|Tur- och returtid för Pings som skickas till en virtuell destination|KällaKundadress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Misslyckade pingar till en virtuell dator|Procent|Medel|Procent av antalet misslyckade pingar till totalt skickade pingar för en mål-VM|KällaKundadress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AnsökanRuleHit|Antal programregler|Antal|Totalt|Antal gånger Tillämpningsreglerna har träffats|Status,orsak,protokoll|
|NätverkRuleHit|Antal nätverksregler|Antal|Totalt|Antal gånger Nätverksreglerna träffades|Status,orsak,protokoll|
|BrandväggHälsa|Hälsotillstånd för brandvägg|Procent|Medel|Hälsotillstånd för brandvägg|Status,Orsak|
|Databehandlade|Bearbetade data|Byte|Totalt|Totalt antal data som bearbetas av brandväggen|Inget|
|SNATPortUtilization (SNATPortUtilization)|SNAT-portutnyttjande|Procent|Medel|SNAT-portutnyttjande|Inget|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Medel|Antal byte per sekund som Application Gateway har tjänat|Inget|
|OhälsosammaHostCount|Felfritt antal värden|Antal|Medel|Antal felaktiga serverdvärdar|BackendSettingsPool|
|FriskaHostCount|Friska värden räknas|Antal|Medel|Antal friska serverdvärdar|BackendSettingsPool|
|TotalRequests|Totalt antal förfrågningar|Antal|Totalt|Antal lyckade begäranden som Application Gateway har tjänat|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Begäranden per minut och felfri värd|Antal|Medel|Genomsnittligt antal begäranden per minut och värd för felfri backend i en pool|BackendSettingsPool|
|Misslyckade begärande|Misslyckade begäranden|Antal|Totalt|Antal misslyckade begäranden som Application Gateway har tjänat|BackendSettingsPool|
|Svarsstatus|Svarsstatus|Antal|Totalt|Http-svarsstatus returnerad av Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inget|
|NewConnectionsPerSecond|Nya anslutningar per sekund|CountPerSecond|Medel|Nya anslutningar per sekund som upprättats med Application Gateway|Inget|
|CpuUtilization (CpuUtilization)|CPU-användning|Procent|Medel|Nuvarande CPU-användning av Application Gateway|Inget|
|KapacitetEnheter|Enheter för strömkapacitet|Antal|Medel|Kapacitetsenheter förbrukas|Inget|
|FixedBillableCapacityUnits|Enheter med fast utbytbar kapacitet|Antal|Medel|Minsta kapacitetsenheter som kommer att debiteras|Inget|
|UppskattadeFakturerade ArbetsopacitetUnits|Uppskattade fakturerade kapacitetsenheter|Antal|Medel|Uppskattade kapacitetsenheter som kommer att debiteras|Inget|
|ComputeUnits (beräkningsenheter)|Aktuella beräkningsenheter|Antal|Medel|Beräkningsenheter förbrukas|Inget|
|BackendResponseStatus|Svarsstatus för backend|Antal|Totalt|Antalet HTTP-svarskoder som genereras av backend-medlemmarna. Detta inkluderar inte några svarskoder som genereras av Application Gateway.|BackendServer,BackendPool,BackendHttpSetting,HttpStatusGroup|
|TlsProtocol (TlsProtocol)|TLS-protokoll för klient|Antal|Totalt|Antalet TLS- och icke-TLS-begäranden som initierats av klienten som upprättade anslutningen till Application Gateway. Om du vill visa TLS-protokolldistribution filtrerar du efter dimensionen TLS Protocol.|Lyssnare,TlsProtocol|
|BytesSent|Skickade byte|Byte|Totalt|Det totala antalet byte som skickas av Application Gateway till klienterna|Lyssnare|
|ByteKom med|Mottagna byte|Byte|Totalt|Det totala antalet byte som tas emot av Application Gateway från klienterna|Lyssnare|
|ClientRtt (klientrt)|RTT för klient|Millisekunder|Medel|Genomsnittlig tur och returtid mellan klienter och Application Gateway. Det här måttet anger hur lång tid det tar att upprätta anslutningar och returberäkningar|Lyssnare|
|ApplicationGatewayTotalTime|Total tid för programgateway|Millisekunder|Medel|Genomsnittlig tid som det tar för en begäran att bearbetas och dess svar skickas. Detta beräknas som medelvärdet av intervallet från den tidpunkt då Application Gateway tar emot den första bytet av en HTTP-begäran till den tidpunkt då svarssändningsåtgärden avslutas. Det är viktigt att notera att detta vanligtvis inkluderar application gateway-bearbetningstiden, tid då begäran och svarspaketen reser över nätverket och den tid då serverdservern tog att svara.|Lyssnare|
|BackendConnectTime|Anslutningstid för backend|Millisekunder|Medel|Tid som lagts ned på att upprätta en anslutning till en server för server för server för server för servergrupp|Lyssnare,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|Svarstid för serverad första byte|Millisekunder|Medel|Tidsintervall mellan starten för att upprätta en anslutning till server för serveråtkomst och ta emot den första bytet i svarshuvudet, vilket approximerar bearbetningstiden för serveråtkomstservern|Lyssnare,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|Serverad senaste byte svarstid|Millisekunder|Medel|Tidsintervall mellan starten för upprättande av en anslutning till serverdelsserver och mottagning av den sista bytet av svarstexten|Lyssnare,BackendServer,BackendPool,BackendHttpSetting|
|MatchedCount|Brandvägg för webbprogram v1 Total regeldistribution|Antal|Totalt|Brandvägg för webbprogram v1 Total regelfördelning för inkommande trafik|Regelgrupp,RuleId|
|BlockeradRäkning|Webbprogram Brandvägg v1 Blockerade begäranden regeldistribution|Antal|Totalt|Brandvägg för webbprogram v1 blockerade begäranden regeldistribution|Regelgrupp,RuleId|
|BlockeradReqCount|Antal blockerade begäranden i webbprogram v1-blockerade begäranden|Antal|Totalt|Antal v1 blockerade begäranden för webbprogram v1|Inget|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GenomsnittligtBredd|Bandbredd för Gateway S2S|BytesPerSecond|Medel|Genomsnittlig bandbredd från plats till plats för en gateway i byte per sekund|Inget|
|P2SBandbredd|Gateway P2S-bandbredd|BytesPerSecond|Medel|Genomsnittlig bandbredd från punkt till plats för en gateway i byte per sekund|Inget|
|P2SConnectionCount|Antal P2S-anslutningar|Antal|Maximal|Antal anslutningar från punkt till plats för en gateway|Protokoll|
|TunnelAverageBandbredd|Tunnel bandbredd|BytesPerSecond|Medel|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName,RemoteIP|
|TunnelEgressBytes|Tunnel utgående byte|Byte|Totalt|Utgående byte i en tunnel|ConnectionName,RemoteIP|
|TunnelIngressBytes|Tunnel inträngning byte|Byte|Totalt|Inkommande byte av en tunnel|ConnectionName,RemoteIP|
|TunnelEgressPackets|Tunnel utgående paket|Antal|Totalt|Antal utgående paket i en tunnel|ConnectionName,RemoteIP|
|TunnelIngressPackets|Tunnel inträngningspaket|Antal|Totalt|Antal inkommande paket i en tunnel|ConnectionName,RemoteIP|
|TunnelEgressPacketDroptSMismatch|Tunnel utgående TS matchar inte paketsläpp|Antal|Totalt|Antal utgående paketsläpp från trafikväljare som inte matchar en tunnel|ConnectionName,RemoteIP|
|TunnelIngressPacketDroptSMismatch|Tunnel ingående TS matchar inte paketsläpp|Antal|Totalt|Antal inkommande paketsläpp från trafikväljaren som inte matchar en tunnel|ConnectionName,RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Antal|Medel|Rx Ljusnivå i dBm|Länk,Körfält|
|TxLightLevel|TxLightLevel|Antal|Medel|Tx ljusnivå i dBm|Länk,Körfält|
|AdminState (administrativa)|AdminState (administrativa)|Antal|Medel|Admin-tillstånd för porten|Länk|
|LineProtocol (LinjeProtokol)|LineProtocol (LinjeProtokol)|Antal|Medel|Linjeprotokollstatus för porten|Länk|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|Bitar som inträder i Azure per sekund|Länk|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|Bitar som utgående azure per sekund|Länk|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|Bitar som inträder i Azure per sekund|PeeringType (peeringtyp)|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|Bitar som utgående azure per sekund|PeeringType (peeringtyp)|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Medel|Bitar som inträder i Azure per sekund|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Medel|Bitar som utgående azure per sekund|PeeredCircuitSKey|
|BgpTillgänglighet|Bgp-tillgänglighet|Procent|Medel|BGP Tillgänglighet från MSEE till alla peer-datorer.|PeeringType,Peer|
|ArpAvailability|Arp Tillgänglighet|Procent|Medel|ARP Tillgänglighet från MSEE mot alla peer-datorer.|PeeringType,Peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Medel|Ingående databitar som har tappats per sekund|Inget|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Medel|Utgående databitar som har tagits bort per sekund|Inget|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|Bitar som inträder i Azure per sekund|Inget|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|Bitar som utgående azure per sekund|Inget|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/anslutningar

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|Bitar som inträder i Azure per sekund|Inget|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|Bitar som utgående azure per sekund|Inget|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ErGatewayAnslutningBitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|Bitar som inträder i Azure per sekund|Anslutningsnamn|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|Bitar som utgående azure per sekund|Anslutningsnamn|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Frågor efter slutpunkt returneras|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt har returnerats under den angivna tidsramen|Slutpunktnamn|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Slutpunktsstatus efter slutpunkt|Antal|Maximal|1 om en slutpunkts avsökningsstatus är "Aktiverad", 0 annars.|Slutpunktnamn|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SonderFailedPercent|% avsökningar misslyckades|Procent|Medel|% av av sonderna för anslutningsövervakning misslyckades|Inget|
|AverageRoundtripMs|Genomsnittlig tid tur och retur (ms)|Millisekunder|Medel|Genomsnittlig nätverksresa (ms) för avsökningar för anslutningsövervakning som skickas mellan källa och mål|Inget|
|KontrollerFailedPercent|Kontroller misslyckades procent (förhandsgranskning)|Procent|Medel|% av kontrollerna för konnektivitetsövervakning misslyckades|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|
|RoundTripTimeM|Tid för tur och retur (ms) (förhandsgranskning)|Millisekunder|Medel|Rundresa i millisekunder för anslutningsövervakningskontroller|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranrring|Antal begäranden|Antal|Totalt|Antalet klientbegäranden som betjänas av HTTP/S-proxyn|httpstatus,httpstatusgrupp,clientregion,clientcountry|
|Begäransstorlek|Storlek för begäran|Byte|Totalt|Antalet byte som skickas som begäranden från klienter till HTTP/S-proxyn|httpstatus,httpstatusgrupp,clientregion,clientcountry|
|SvarStorlek|Responsstorlek|Byte|Totalt|Antalet byte som skickas som svar från HTTP/S-proxy till klienter|httpstatus,httpstatusgrupp,clientregion,clientcountry|
|FakturerasResponseSize|Fakturerbar svarsstorlek|Byte|Totalt|Antalet fakturerbara byte (minst 2KB per begäran) som skickas som svar från HTTP/S-proxy till klienter.|httpstatus,httpstatusgrupp,clientregion,clientcountry|
|BackendRequestCount|Antal begäranden efter rygg|Antal|Totalt|Antalet begäranden som skickas från HTTP/S-proxyn till serverings-|HttpStatus,HttpStatusGroup,Backend|
|BackendRequestLatency|Svarstid för begäran om bakåtsträvning|Millisekunder|Medel|Den tid som beräknats från när begäran skickades av HTTP/S-proxyn till servergången tills HTTP/S-proxyn tog emot den senaste svarsbyten från serveringscentralen|Backend|
|TotalLatency|Total latens|Millisekunder|Medel|Den tid som beräknats från när klientbegäran togs emot av HTTP/S-proxyn tills klienten bekräftade den senaste svarsbytet från HTTP/S-proxyn|httpstatus,httpstatusgrupp,clientregion,clientcountry|
|BackendHealthPercentage|Hälsoprocent för backend|Procent|Medel|Procentandelen lyckade hälsoavsökningar från HTTP/S-proxyn till serverings-|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Antal begäranden om brandvägg för webbprogram|Antal|Totalt|Antalet klientbegäranden som bearbetas av brandväggen för webbprogram|PolicyName,RuleName,Åtgärd|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FrågaVolym|Fråga volym|Antal|Totalt|Antal frågor som har serverats för en privat DNS-zon|Inget|
|RecordSetCount (RecordSetCount)|Antal postuppsättningar|Antal|Maximal|Antal postuppsättningar i en privat DNS-zon|Inget|
|RecordSetCapacityUtilization|Kapacitetsutnyttjande för postuppsättning|Procent|Maximal|Procent av postuppsättningskapacitet som används av en privat DNS-zon|Inget|
|VirtualNetworkLinkCount|Antal länkar till virtuella nätverk|Antal|Maximal|Antal virtuella nätverk som är länkade till en privat DNS-zon|Inget|
|VirtualNetworkLinkCapacityUtilisering|Kapacitetsutnyttjande för virtuell nätverkslänk|Procent|Maximal|Procent av virtual network link-kapacitet som används av en privat DNS-zon|Inget|
|VirtualNetworkMedRegistrationLinkCount|Antal länkar för virtuell nätverksregistrering|Antal|Maximal|Antal virtuella nätverk som är länkade till en privat DNS-zon med automatisk registrering aktiverad|Inget|
|VirtualNetworkMedRegistrationCapacityUtilization|Kapacitetsutnyttjande för virtuell nätverksregistrering|Procent|Maximal|Procent av virtual network link med automatisk registreringskapacitet som används av en privat DNS-zon|Inget|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|registrering.alla|Registreringsåtgärder|Antal|Totalt|Antalet lyckade registreringsåtgärder (skapande uppdaterar frågor och borttagningar). |Inget|
|registration.create|Skapa åtgärder för registrering|Antal|Totalt|Antalet lyckade registreringsskapelser.|Inget|
|registration.update (på/på)44|Uppdatering av registrering|Antal|Totalt|Antalet lyckade registreringsuppdateringar.|Inget|
|registration.get|Läsa verksamhet för registrering|Antal|Totalt|Antalet lyckade registreringsfrågor.|Inget|
|registration.delete (registrera.ta bort)|Borttagning av registreringsåtgärder|Antal|Totalt|Antalet lyckade registreringsborttagningar.|Inget|
|Inkommande|Inkommande meddelanden|Antal|Totalt|Antalet lyckade skicka API-anrop. |Inget|
|incoming.scheduled|Skickade push-meddelanden för schemalagda push-meddelanden|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inget|
|incoming.scheduled.cancel|Schemalagda push-meddelanden har avbrutits|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inget|
|scheduled.pending|Väntande schemalagda meddelanden|Antal|Totalt|Väntande schemalagda meddelanden|Inget|
|installation.alla|Åtgärder för installationshantering|Antal|Totalt|Åtgärder för installationshantering|Inget|
|installation.get|Hämta installationsåtgärder|Antal|Totalt|Hämta installationsåtgärder|Inget|
|installation.upsert|Skapa eller uppdatera installationsåtgärder|Antal|Totalt|Skapa eller uppdatera installationsåtgärder|Inget|
|installation.patch|Korrigeringsinstallationsåtgärder|Antal|Totalt|Korrigeringsinstallationsåtgärder|Inget|
|installation.delete|Ta bort installationsåtgärder|Antal|Totalt|Ta bort installationsåtgärder|Inget|
|outgoing.allpns.success|Lyckade meddelanden|Antal|Totalt|Antalet lyckade meddelanden.|Inget|
|outgoing.allpns.invalidpayload|Fel i nyttolasten|Antal|Totalt|Antalet pushes som misslyckades eftersom PNS returnerade ett dåligt nyttolastfel.|Inget|
|outgoing.allpns.pnserror|Fel i externt meddelandesystem|Antal|Totalt|Antalet pushes som misslyckades eftersom det uppstod ett problem med att kommunicera med PNS (utesluter autentiseringsproblem).|Inget|
|outgoing.allpns.channelerror|Kanalfel|Antal|Totalt|Antalet push-ningar som misslyckades eftersom kanalen var ogiltig inte associerad med rätt app som begränsas eller har upphört att gälla.|Inget|
|outgoing.allpns.badorexpiredchannel|Felaktiga eller utgångna kanalfel|Antal|Totalt|Antalet pushes som misslyckades eftersom kanalen/token/registrationId i registreringen har upphört att gälla eller ogiltigt.|Inget|
|outgoing.wns.success|WNS lyckade meddelanden|Antal|Totalt|Antalet lyckade meddelanden.|Inget|
|outgoing.wns.invalidcredentials|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antalet pushes som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade. (Windows Live känner inte igen autentiseringsuppgifterna).|Inget|
|outgoing.wns.badchannel|WNS fel i dålig kanal|Antal|Totalt|Antalet pushes som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inget|
|outgoing.wns.expiredchannel|WNS-utgångna kanalfel|Antal|Totalt|Antalet pushes som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 Borta).|Inget|
|outgoing.wns.throttled|WNS-begränsade meddelanden|Antal|Totalt|Antalet pushes som misslyckades eftersom WNS är begränsning denna app (WNS-status: 406 Inte acceptabelt).|Inget|
|outgoing.wns.tokenproviderunreachable|WNS-auktoriseringsfel (kan inte nås)|Antal|Totalt|Windows Live kan inte nås.|Inget|
|outgoing.wns.invalidtoken|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Token som tillhandahålls WNS är inte giltig (WNS-status: 401 Obehörig).|Inget|
|outgoing.wns.wrongtoken|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Token som tillhandahålls WNS är giltig men för ett annat program (WNS-status: 403 Förbjudet). Detta kan inträffa om ChannelURI i registreringen är associerad med en annan app. Kontrollera att klientappen är associerad med samma app vars autentiseringsuppgifter finns i meddelandehubben.|Inget|
|outgoing.wns.invalidnotificationformat|Ogiltigt meddelandeformat för WNS|Antal|Totalt|Formatet för meddelandet är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nyttolaster.|Inget|
|outgoing.wns.invalidnoteificationsize|Fel i WNS-ogiltiga meddelandestorlek|Antal|Totalt|Meddelandenyttolasten är för stor (WNS-status: 413).|Inget|
|outgoing.wns.channelthrottled|WNS-kanal strypt|Antal|Totalt|Meddelandet togs bort eftersom ChannelURI i registreringen begränsas (WNS-svarshuvud: X-WNS-NotificationStatus:channelThrottled).|Inget|
|outgoing.wns.channeldisconnected|WNS-kanal frånkopplad|Antal|Totalt|Meddelandet avbröts eftersom ChannelURI i registreringen begränsas (WNS-svarshuvud: X-WNS-DeviceConnectionStatus: frånkopplad).|Inget|
|outgoing.wns.dropped|WNS-ignorerade meddelanden|Antal|Totalt|Meddelandet avbröts eftersom ChannelURI i registreringen begränsas (X-WNS-NotificationStatus: tappade men inte X-WNS-DeviceConnectionStatus: frånkopplad).|Inget|
|outgoing.wns.pnserror|WNS-fel|Antal|Totalt|Meddelandet har inte levererats på grund av fel som kommunicerar med WNS.|Inget|
|outgoing.wns.authenticationerror|WNS-autentiseringsfel|Antal|Totalt|Meddelandet levererades inte på grund av fel som kommunicerar med Windows Live ogiltiga autentiseringsuppgifter eller fel token.|Inget|
|outgoing.apns.success|GODKÄNDA APNS-meddelanden|Antal|Totalt|Antalet lyckade meddelanden.|Inget|
|outgoing.apns.invalidcredentials|APNS-auktoriseringsfel|Antal|Totalt|Antalet pushes som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade.|Inget|
|outgoing.apns.badchannel|APNS fel på dålig kanal|Antal|Totalt|Antalet pushes som misslyckades eftersom token är ogiltig (APNS-statuskod: 8).|Inget|
|outgoing.apns.expiredchannel|APNS utgångna kanalfel|Antal|Totalt|Antalet token som ogiltigförklarades av APNS-återkopplingskanalen.|Inget|
|outgoing.apns.invalidnoteificationsize|Fel i APNS ogiltiga meddelandestorlek|Antal|Totalt|Antalet pushes som misslyckades eftersom nyttolasten var för stor (APNS-statuskod: 7).|Inget|
|outgoing.apns.pnserror|APNS-fel|Antal|Totalt|Antalet pushes som misslyckades på grund av fel som kommunicerar med APNS.|Inget|
|outgoing.gcm.success|Lyckade GCM-meddelanden|Antal|Totalt|Antalet lyckade meddelanden.|Inget|
|outgoing.gcm.invalidcredentials|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antalet pushes som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade.|Inget|
|outgoing.gcm.badchannel|GCM-fel på dålig kanal|Antal|Totalt|Antalet pushes som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM-resultat: Ogiltig registrering).|Inget|
|outgoing.gcm.expiredchannel|GCM-utgångna kanalfel|Antal|Totalt|Antalet pushes som misslyckades eftersom registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inget|
|outgoing.gcm.throttled|GCM begränsade meddelanden|Antal|Totalt|Antalet pushes som misslyckades eftersom GCM begränsade den här appen (GCM-statuskod: 501-599 eller resultat:Ej tillgänglig).|Inget|
|outgoing.gcm.invalidnotificationformat|Ogiltigt meddelandeformat för GCM|Antal|Totalt|Antalet pushes som misslyckades eftersom nyttolasten inte formaterades korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inget|
|outgoing.gcm.invalidanmälingsstorlek|Fel i GCM-ogiltig meddelandestorlek|Antal|Totalt|Antalet pushes som misslyckades eftersom nyttolasten var för stor (GCM-resultat: MessageTooBig).|Inget|
|outgoing.gcm.wrongchannel|Fel kanalfel i GCM|Antal|Totalt|Antalet pushes som misslyckades eftersom registrationId i registreringen inte är associerad med den aktuella appen (GCM-resultat: InvalidPackageName).|Inget|
|outgoing.gcm.pnserror|GCM-fel|Antal|Totalt|Antalet pushes som misslyckades på grund av fel som kommunicerar med GCM.|Inget|
|utgående.gcm.authenticationerror|GCM-autentiseringsfel|Antal|Totalt|Antalet pushes som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna autentiseringsuppgifterna är blockerade eller SenderId inte är korrekt konfigurerad i appen (GCM-resultat: MismatchedSenderId).|Inget|
|outgoing.mpns.success|MPNS lyckade meddelanden|Antal|Totalt|Antalet lyckade meddelanden.|Inget|
|outgoing.mpns.invalidcredentials|Ogiltiga MPNS-autentiseringsuppgifter|Antal|Totalt|Antalet pushes som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade.|Inget|
|outgoing.mpns.badchannel|FEL PÅ MPNS-fel i dålig kanal|Antal|Totalt|Antalet pushes som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inget|
|outgoing.mpns.throttled|MPNS-begränsade meddelanden|Antal|Totalt|Antalet pushes som misslyckades eftersom MPNS är strypning denna app (WNS MPNS: 406 Inte acceptabelt).|Inget|
|outgoing.mpns.invalidnotificationformat|Villkor för ogiltigt meddelande om MPNS|Antal|Totalt|Antalet pushes som misslyckades eftersom nyttolasten för meddelandet var för stor.|Inget|
|outgoing.mpns.channeldisconnected|MPNS-kanal frånkopplad|Antal|Totalt|Antalet pushes som misslyckades eftersom ChannelURI i registreringen kopplades från (MPNS-status: 412 hittades inte).|Inget|
|outgoing.mpns.dropped|MPNS-ignorerade meddelanden|Antal|Totalt|Antalet pushes som har tagits bort av MPNS (MPNS-svarshuvud: X-NotificationStatus: QueueFull eller Undertryckt).|Inget|
|outgoing.mpns.pnserror|MPNS-fel|Antal|Totalt|Antalet pushes som misslyckades på grund av fel som kommunicerar med MPNS.|Inget|
|outgoing.mpns.authenticationerror|MPNS-autentiseringsfel|Antal|Totalt|Antalet pushes som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna är blockerade.|Inget|
|anmälanhub.pushes|Alla utgående meddelanden|Antal|Totalt|Alla utgående meddelanden i meddelandehubben|Inget|
|incoming.all.requests|Alla inkommande begäranden|Antal|Totalt|Totalt antal inkommande begäranden för en meddelandehubb|Inget|
|incoming.all.failedrequests|Alla inkommande misslyckade begäranden|Antal|Totalt|Totalt antal inkommande misslyckade begäranden för en meddelandehubb|Inget|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/arbetsytor

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_% Gratis Inodes|% gratis inoder|Antal|Medel|Average_% Gratis Inodes|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% ledigt utrymme|% ledigt utrymme|Antal|Medel|Average_% ledigt utrymme|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Begagnade Inoder|% använda inoder|Antal|Medel|Average_% Begagnade Inoder|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% använt utrymme|Använt utrymme i %|Antal|Medel|Average_% använt utrymme|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Läs byte/sek|Diskläs byte per sekund|Antal|Medel|Average_Disk Läs byte/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Läser/sek|Diskläsningar/sek|Antal|Medel|Average_Disk Läser/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Överföringar/sek|Disköverföringar per sekund|Antal|Medel|Average_Disk Överföringar/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk skrivbyten/sek|Byte för diskskrivning/sek|Antal|Medel|Average_Disk skrivbyten/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk skriver/sek|Diskskrivningar per sekund|Antal|Medel|Average_Disk skriver/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Megabyte|Gratis Megabyte|Antal|Medel|Average_Free Megabyte|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Logical diskbyten/sek|Logiska diskbyten per sekund|Antal|Medel|Average_Logical diskbyten/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% tillgängligt minne|% tillgängligt minne|Antal|Medel|Average_% tillgängligt minne|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% tillgängligt växlingsutrymme|% tillgängligt växlingsutrymme|Antal|Medel|Average_% tillgängligt växlingsutrymme|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% använt minne|% använt minne|Antal|Medel|Average_% använt minne|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% använt växlingsutrymme|% använt växlingsutrymme|Antal|Medel|Average_% använt växlingsutrymme|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes-minne|Tillgängligt MBytes-minne|Antal|Medel|Average_Available MBytes-minne|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes Swap|Tillgänglig MBytes Swap|Antal|Medel|Average_Available MBytes Swap|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Page Läser/sek|Sidan läser/sek|Antal|Medel|Average_Page Läser/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Page skriver/sek|Sidan skriver per sekund|Antal|Medel|Average_Page skriver/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pages/sek|Sidor per sekund|Antal|Medel|Average_Pages/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used MBytes byta utrymme|Använt MBytes-växlingsutrymme|Antal|Medel|Average_Used MBytes byta utrymme|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used-minne MBytes|Använt minne MBytes|Antal|Medel|Average_Used-minne MBytes|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total överförda byte|Totalt antal överförda byte|Antal|Medel|Average_Total överförda byte|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total mottagna byte|Totalt antal mottagna byte|Antal|Medel|Average_Total mottagna byte|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total byte|Totalt antal byte|Antal|Medel|Average_Total byte|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total överförda paket|Totalt antal överförda paket|Antal|Medel|Average_Total överförda paket|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total mottagna paket|Totalt antal mottagna paket|Antal|Medel|Average_Total mottagna paket|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Rx-fel|Totalt Rx-fel|Antal|Medel|Average_Total Rx-fel|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Tx-fel|Totalt antal Tx-fel|Antal|Medel|Average_Total Tx-fel|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total kollisioner|Totalt kollisioner|Antal|Medel|Average_Total kollisioner|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. Disk s/läsning|Genomsnittlig disk sek/läs|Antal|Medel|Average_Avg. Disk s/läsning|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. Disk sek/överföring|Genomsnittlig disk sek/överföring|Antal|Medel|Average_Avg. Disk sek/överföring|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. Disk s/skrivning|Genomsnittlig disk sek/skrivning|Antal|Medel|Average_Avg. Disk s/skrivning|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Physical diskbyten/sek|Fysiska diskbyten per sekund|Antal|Medel|Average_Physical diskbyten/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pct privilegierad tid|Pct privilegierad tid|Antal|Medel|Average_Pct privilegierad tid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pct användartid|Pct-användartid|Antal|Medel|Average_Pct användartid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used-minne kBytes|Använt minne kBytes|Antal|Medel|Average_Used-minne kBytes|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Virtual delat minne|Virtuellt delat minne|Antal|Medel|Average_Virtual delat minne|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% DPC-tid|% DPC-tid|Antal|Medel|Average_% DPC-tid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% inaktiv tid|Inaktivitetstid i procent|Antal|Medel|Average_% inaktiv tid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% avbrottstid|% avbrottstid|Antal|Medel|Average_% avbrottstid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% IO väntetid|% väntetid i i/o|Antal|Medel|Average_% IO väntetid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Trevlig tid|% trevlig tid|Antal|Medel|Average_% Trevlig tid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% privilegierad tid|% privilegierad tid|Antal|Medel|Average_% privilegierad tid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% processortid|% processortid|Antal|Medel|Average_% processortid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% användartid|% användartid|Antal|Medel|Average_% användartid|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free fysiskt minne|Gratis fysiskt minne|Antal|Medel|Average_Free fysiskt minne|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free utrymme i växlingsfiler|Ledigt utrymme i växlingsfiler|Antal|Medel|Average_Free utrymme i växlingsfiler|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free virtuellt minne|Gratis virtuellt minne|Antal|Medel|Average_Free virtuellt minne|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Processes|Processer|Antal|Medel|Average_Processes|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Size lagras i växlingsfiler|Storlek lagras i växlingsfiler|Antal|Medel|Average_Size lagras i växlingsfiler|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Uptime|Upptid|Antal|Medel|Average_Uptime|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Users|Användare|Antal|Medel|Average_Users|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|längden Average_Current diskkö|Aktuell diskkölängd|Antal|Medel|längden Average_Current diskkö|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes|Tillgängliga MBytes|Antal|Medel|Average_Available MBytes|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% genomförda byte som används|% genomförda byte som används|Antal|Medel|Average_% genomförda byte som används|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes mottaget/sek|Mottagna byte per sekund|Antal|Medel|Average_Bytes mottaget/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes skickat/sek|Skickade byte per sekund|Antal|Medel|Average_Bytes skickat/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Totalt/sek|Totalt antal byte per sekund|Antal|Medel|Average_Bytes Totalt/sek|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|kölängd Average_Processor|Längden på processorkön|Antal|Medel|kölängd Average_Processor|Dator,ObjectName,InstanceName,CounterPath,SourceSystem|
|Pulsslag|Pulsslag|Antal|Totalt|Pulsslag|Dator,OSType,Version,SourceComputerId|
|Uppdatering|Uppdatering|Antal|Medel|Uppdatering|Dator,Produkt,Klassificering,UpdateState,Valfritt,Godkänd|
|Händelse|Händelse|Antal|Medel|Händelse|Källa,EventLog,Dator,EventKategori,EventLevel,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PrefixLatency|Svarstid för prefix|Millisekunder|Medel|Medianförbehåll för prefixfördröjning|PrefixNamn|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SessionTillgänglighetV4|Anslutningstillgänglighet V4|Procent|Medel|Tillgänglighet för V4-sessionen|ConnectionId (på)-anslutning)|
|SessionTillgänglighetV6|Session Tillgänglighet V6|Procent|Medel|Tillgänglighet för V6-sessionen|ConnectionId (på)-anslutning)|
|IngåendeTrafficRate|Ingående trafikhastighet|BitsPerSecond|Medel|Inträngningstrafik i bitar per sekund|ConnectionId (på)-anslutning)|
|EgressTrafficRate|Utgående trafikhastighet|BitsPerSecond|Medel|Utgående trafikhastighet i bitar per sekund|ConnectionId (på)-anslutning)|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacity

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FrågaUnderation|Frågevaraktighet|Millisekunder|Medel|VARAKTIGHET FÖR DAX-frågor i det senaste intervallet|Inga dimensioner|
|FrågaPoolJobQueueLength|Trådar: Kölängd för frågepooljobb|Antal|Medel|Antal jobb i kön till frågetrådspoolen.|Inga dimensioner|
|qpu_high_utilization_metric|Hög användning av QPU|Antal|Totalt|QPU högt utnyttjande i sista minuten, 1 för hög QPU utnyttjande, annars 0|Inga dimensioner|
|memory_metric|Minne|Byte|Medel|Memory. Räckvidd 0-3 GB för A1, 0-5 GB för A2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Minnesförslöing|Procent|Medel|Genomsnittligt minne stryk.|Inga dimensioner|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/konton

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TillgångsfördelningEfterklassificering|Tillgångsfördelning efter klassificering|Antal|Totalt|Anger antalet tillgångar med en viss klassificering som tilldelats, dvs.|Klassificering,Källa,ResourceId|
|TillgångDistributionByStorageType|Tillgångsfördelning efter lagringstyp|Antal|Totalt|Anger antalet tillgångar med en viss lagringstyp.|StorageType,ResourceId|
|CatalogActiveUsers|Dagliga aktiva användare|Antal|Totalt|Antal aktiva användare dagligen|ResourceId|
|KatalogUppsätt|Förbrukningsfördelning efter operation|Antal|Totalt|Ange hur många åtgärder användaren gör i katalogen, dvs Access, Sök, Ordlista.|Åtgärd,ResourceId|
|AntalAvgörandenKlassificifications|Antal tillgångar med minst en klassificering|Antal|Medel|Anger antalet tillgångar med minst en taggklassificering.|ResourceId|
|ScanCancelled|Genomsökningen har avbrutits|Antal|Totalt|Anger antalet avbrutna genomsökningar.|ResourceId|
|ScanCom är slutfört|Genomsökningen har slutförts|Antal|Totalt|Anger antalet genomförda genomsökningar.|ResourceId|
|ScanFailed|Det gick inte att skanna|Antal|Totalt|Anger att antalet genomsökningar misslyckades.|ResourceId|
|ScanTimeTaken|Sök tid|Sekunder|Totalt|Anger den totala genomsökningstiden i sekunder.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namnområden

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ListenerConnections-Framgång|ListenerConnections-Framgång|Antal|Totalt|Lyckade ListenerConnections för Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Antal|Totalt|ClientError på ListenerConnections för Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Antal|Totalt|ServerError på ListenerConnections för Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-Framgång|SenderConnections-Framgång|Antal|Totalt|Lyckade SenderConnections för Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Antal|Totalt|ClientError på SenderConnections för Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Antal|Totalt|ServerError på SenderConnections för Microsoft.Relay.|EntityName,OperationResult|
|LyssnarenAnslutningar-TotaltRequests|LyssnarenAnslutningar-TotaltRequests|Antal|Totalt|Totalt lyssnaranslutningar för Microsoft.Relay.|Entityname|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Antal|Totalt|Totalt antal SenderConnections-begäranden för Microsoft.Relay.|Entityname|
|ActiveConnections (Aktiva Anslutning)|ActiveConnections (Aktiva Anslutning)|Antal|Totalt|Totalt antal ActiveConnections för Microsoft.Relay.|Entityname|
|ActiveListeners|ActiveListeners|Antal|Totalt|Totalt ActiveListeners för Microsoft.Relay.|Entityname|
|ByteÖverläst|ByteÖverläst|Antal|Totalt|Totalt antal antalöversatt för Microsoft.Relay.|Entityname|
|LyssnareAnsluter|LyssnareAnsluter|Antal|Totalt|Totalt lyssnareAnsluter för Microsoft.Relay.|Entityname|
|SenderDisconnects|SenderDisconnects|Antal|Totalt|Totalt antal SenderDisconnects för Microsoft.Relay.|Entityname|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SökLatens|Sök svarstid|Sekunder|Medel|Genomsnittlig sökfördröjning för söktjänsten|Inget|
|SökQueriesPerSecond|Sökfrågor per sekund|CountPerSecond|Medel|Sökfrågor per sekund för söktjänsten|Inget|
|ThrottledSearchQueriesPercentage|Procent av begränsade sökfrågor|Procent|Medel|Procentandel sökfrågor som begränsades för söktjänsten|Inget|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namnområden

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Lyckade Begärande|Lyckade förfrågningar|Antal|Totalt|Totalt antal lyckade begäranden för ett namnområde|EntityName,OperationResult|
|ServerErrors|Serverfel.|Antal|Totalt|Serverfel för Microsoft.ServiceBus.|EntityName,OperationResult|
|UserErrors|Användarfel.|Antal|Totalt|Användarfel för Microsoft.ServiceBus.|EntityName,OperationResult|
|ThrottledRequests|Begränsade begäranden.|Antal|Totalt|Begränsade begäranden för Microsoft.ServiceBus.|EntityName,OperationResult|
|Inkommande Begäran|Inkommande begäranden|Antal|Totalt|Inkommande begäranden för Microsoft.ServiceBus.|Entityname|
|IncomingMessages|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft.ServiceBus.|Entityname|
|UtgåendeMessages|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft.ServiceBus.|Entityname|
|ActiveConnections (Aktiva Anslutning)|ActiveConnections (Aktiva Anslutning)|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft.ServiceBus.|Inget|
|AnslutningarÖppnade|Anslutningar öppnade.|Antal|Medel|Anslutningar som öppnats för Microsoft.ServiceBus.|Entityname|
|Anslutningar Slutna|Anslutningar stängda.|Antal|Medel|Anslutningar stängda för Microsoft.ServiceBus.|Entityname|
|Storlek|Storlek|Byte|Medel|Storleken på en kö/ett ämne i byte.|Entityname|
|Meddelanden|Antal meddelanden i en kö/ett ämne.|Antal|Medel|Antal meddelanden i en kö/ett ämne.|Entityname|
|ActiveMessages|Antal aktiva meddelanden i en kö/ett ämne.|Antal|Medel|Antal aktiva meddelanden i en kö/ett ämne.|Entityname|
|DeadletteredMessages|Antal meddelanden med obeställda meddelanden i en kö/ett ämne.|Antal|Medel|Antal meddelanden med obeställda meddelanden i en kö/ett ämne.|Entityname|
|SchemalagdaMessages|Antal schemalagda meddelanden i en kö/ett ämne.|Antal|Medel|Antal schemalagda meddelanden i en kö/ett ämne.|Entityname|
|NamnrymdCpuUsage|Processor|Procent|Maximal|Service buss premium namnområde CPU-användningsmått.|Replik|
|NamnrymdMemoryUsage|Minnesanvändning|Procent|Maximal|Service buss premium namnområde minne användningsmått.|Replik|
|CPUXNS (PÅ)|CPU (föråldrad)|Procent|Maximal|Service buss premium namnområde CPU-användningsmått. Det här måttet är utpricated. Använd cpu-måttet (NamespaceCpuUsage) i stället.|Replik|
|WSXNS (WSXNS)|Minnesanvändning (föråldrad)|Procent|Maximal|Service buss premium namnområde minne användningsmått. Det här måttet är inaktuellt. Använd måttet Minnesanvändning (NamespaceMemoryUsage) i stället.|Replik|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/-program

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Antal|Medel|Cpu tilldelas denna behållare i milli kärnor|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|Allokerad minne|Allokerad minne|Byte|Medel|Minne som allokerats till den här behållaren i MB|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|ActualCpu (1990)|ActualCpu (1990)|Antal|Medel|Faktisk CPU-användning i milli kärnor|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|FaktiskaMinne|FaktiskaMinne|Byte|Medel|Faktisk minnesanvändning i MB|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|CpuUtilization (CpuUtilization)|CpuUtilization (CpuUtilization)|Procent|Medel|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|Minnesututbyggning|Minnesututbyggning|Procent|Medel|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|ProgramStatus|ProgramStatus|Antal|Medel|Status för programmet Service Fabric Mesh|ApplicationName,Status|
|Tjänststatus|Tjänststatus|Antal|Medel|Hälsostatus för en tjänst i Programmet Service Fabric Mesh|ApplicationName,Status,ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Antal|Medel|Hälsostatus för en tjänstreplik i Programmet Service Fabric Mesh|ApplicationName,Status,ServiceName,ServiceReplicaName|
|BehållareStatus|BehållareStatus|Antal|Medel|Status för behållaren i Programmet Service Fabric Mesh|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName,Status|
|RestartCount(RestartCount)|RestartCount(RestartCount)|Antal|Medel|Antal omstarter av en behållare i Programmet Service Fabric Mesh|ApplicationName,Status,ServiceName,ServiceReplicaName,CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Anslutningskonto|Antal anslutningar|Antal|Maximal|Mängden användaranslutning.|Slutpunkt|
|MeddelandeCount|Antal meddelanden|Antal|Totalt|Den totala mängden meddelanden.|Inget|
|InkommandeTraffic|Inkommande trafik|Byte|Totalt|Den inkommande trafiken av tjänsten|Inget|
|UtgåendeTraffic|Utgående trafik|Byte|Totalt|Den utgående trafiken av tjänsten|Inget|
|UserErrors|Användarfel|Procent|Maximal|Procentandelen användarfel|Inget|
|SystemErrors|Systemfel|Procent|Maximal|Procentandelen systemfel|Inget|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servrar/databaser

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inget|
|log_write_percent|Log IO-procent|Procent|Medel|Logga IO-procent. Gäller inte för informationslager.|Inget|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent. Gäller för DTU-baserade databaser.|Inget|
|storage|Datautrymme som används|Byte|Maximal|Datautrymme används. Gäller inte för informationslager.|Inget|
|connection_successful|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inget|
|connection_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inget|
|blocked_by_firewall|Blockerad av brandvägg|Antal|Totalt|Blockerad av brandvägg|Inget|
|Dödläge|Dödlägen|Antal|Totalt|Dödlägen. Gäller inte för informationslager.|Inget|
|storage_percent|Datautrymme som används procent|Procent|Maximal|Datautrymme används procent. Inte tillämplig på informationslager eller hyperskala databaser.|Inget|
|xtp_storage_percent|OLTP-lagringsprocent i minnet|Procent|Medel|OLTP-lagringsprocent i minnet. Gäller inte för informationslager.|Inget|
|workers_percent|Procentandel av arbetstagarna|Procent|Medel|Procent av arbetare. Gäller inte för informationslager.|Inget|
|sessions_percent|Procent av sessioner|Procent|Medel|Antal sessioner. Gäller inte för informationslager.|Inget|
|dtu_limit|DTU-gräns|Antal|Medel|DTU-gränsen. Gäller för DTU-baserade databaser.|Inget|
|dtu_used|DTU används|Antal|Medel|DTU används. Gäller för DTU-baserade databaser.|Inget|
|cpu_limit|CPU-gräns|Antal|Medel|CPU-gräns. Gäller för vCore-baserade databaser.|Inget|
|cpu_used|PROCESSOR som används|Antal|Medel|PROCESSOR som används. Gäller för vCore-baserade databaser.|Inget|
|dwu_limit|DWU-gräns|Antal|Maximal|DWU-gränsen. Gäller endast för informationslager.|Inget|
|dwu_consumption_percent|DWU-procent|Procent|Maximal|DWU-procent. Gäller endast för informationslager.|Inget|
|dwu_used|DWU används|Antal|Maximal|DWU används. Gäller endast för informationslager.|Inget|
|cache_hit_percent|Antal träffar i cacheminne|Procent|Maximal|Cache träff procent. Gäller endast för informationslager.|Inget|
|cache_used_percent|Använd procent av cache|Procent|Maximal|Cache använd procentsats. Gäller endast för informationslager.|Inget|
|sqlserver_process_core_percent|SQL Server-processkärna procent|Procent|Maximal|CPU-användningsprocent för SQL Server-processen, mätt med operativsystemet.|Inget|
|sqlserver_process_memory_percent|Procent av SQL Server-processminne|Procent|Maximal|Procentandel av minnesanvändningen för SQL Server-processen, mätt med operativsystemet.|Inget|
|tempdb_data_size|Tempdb-datafilstorlek Kilobyte|Antal|Maximal|Tempdb Data filstorlek Kilobytes. Gäller inte för informationslager.|Inget|
|tempdb_log_size|Tempdb-loggfilstorlek Kilobyte|Antal|Maximal|Tempdb Loggfil storlek Kilobytes. Gäller inte för informationslager.|Inget|
|tempdb_log_used_percent|Tempdb procent logg används|Procent|Maximal|Tempdb procent logg används. Gäller inte för informationslager.|Inget|
|local_tempdb_usage_percent|Lokal tempdb-procent|Procent|Medel|Lokal tempdb procent. Gäller endast för informationslager.|Inget|
|app_cpu_billed|App-processor faktureras|Antal|Totalt|App CPU faktureras. Gäller för serverlösa databaser.|Inget|
|app_cpu_percent|Procent av appens PROCESSOR|Procent|Medel|Procent av appens PROCESSOR. Gäller för serverlösa databaser.|Inget|
|app_memory_percent|Procentandel av appens minne|Procent|Medel|Procent av appens minne. Gäller för serverlösa databaser.|Inget|
|allocated_data_storage|Tilldelat datautrymme|Byte|Medel|Allokerad datalagring. Gäller inte för informationslager.|Inget|
|memory_usage_percent|Minnesprocent|Procent|Maximal|Minnesprocent. Gäller endast för informationslager.|Inget|
|dw_backup_size_gb|Storlek för datalagring|Antal|Totalt|Datalagringsstorleken består av storleken på dina data och transaktionsloggen. Måttet räknas in i lagringsdelen av fakturan. Gäller endast för informationslager.|Inget|
|dw_snapshot_size_gb|Lagringsstorlek för ögonblicksbilder|Antal|Totalt|Snapshot Storage Size är storleken på de inkrementella ändringar som fångas upp av ögonblicksbilder för att skapa användardefinierade och automatiska återställningspunkter. Måttet räknas in i lagringsdelen av fakturan. Gäller endast för informationslager.|Inget|
|dw_geosnapshot_size_gb|Lagringsstorlek för haveriberedskap|Antal|Totalt|Lagringsstorlek för haveriberedskap återspeglas som "Lagring av haveriberedskap" i din faktura. Gäller endast för informationslager.|Inget|
|wlg_allocation_relative_to_system_percent|Arbetsbelastningsgruppallokering efter systemprocent|Procent|Maximal|Allokerad procentandel av resurser i förhållande till hela systemet per arbetsbelastningsgrupp. Gäller endast för informationslager.|WorkloadGroupName,IsUserDedefinierad|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Arbetsbelastningsgruppallokering efter takresurs procent|Procent|Maximal|Allokerad procentandel av resurser i förhållande till de angivna takresurserna per arbetsbelastningsgrupp. Gäller endast för informationslager.|WorkloadGroupName,IsUserDedefinierad|
|wlg_active_queries|Aktiva frågor för arbetsbelastningsgrupp|Antal|Totalt|Aktiva frågor inom arbetsbelastningsgruppen. Gäller endast för informationslager.|WorkloadGroupName,IsUserDedefinierad|
|wlg_queued_queries|Arbetsbelastningsgrupp köade frågor|Antal|Totalt|Köade frågor inom arbetsbelastningsgruppen. Gäller endast för informationslager.|WorkloadGroupName,IsUserDedefinierad|
|active_queries|Aktiva frågor|Antal|Totalt|Aktiva frågor i alla arbetsbelastningsgrupper. Gäller endast för informationslager.|Inget|
|queued_queries|Köade frågor|Antal|Totalt|Köade frågor i alla arbetsbelastningsgrupper. Gäller endast för informationslager.|Inget|
|wlg_active_queries_timeouts|Timeout för arbetsbelastningsgruppfråga|Antal|Totalt|Frågor som har tagit time out för arbetsbelastningsgruppen. Gäller endast för informationslager.|WorkloadGroupName,IsUserDedefinierad|
|wlg_effective_min_resource_percent|Effektiv minresursprocent|Procent|Maximal|Minsta procentandel av de resurser som reserverats och isolerats för arbetsbelastningsgruppen, med beaktande av lägsta servicenivå. Gäller endast för informationslager.|WorkloadGroupName,IsUserDedefinierad|
|wlg_effective_cap_resource_percent|Effektiv takresurs procent|Procent|Maximal|En hård gräns för procentandelen resurser som tillåts för arbetsbelastningsgruppen, med beaktande av effektiv minresursprocent som allokerats för andra arbetsbelastningsgrupper. Gäller endast för informationslager.|WorkloadGroupName,IsUserDedefinierad|
|full_backup_size_bytes|Fullständig lagringsstorlek för säkerhetskopiering|Byte|Maximal|Kumulativ lagringsstorlek för hela säkerhetskopian. Gäller för vCore-baserade databaser. Gäller inte hyperskaladatabaser.|Inget|
|diff_backup_size_bytes|Differentiell lagringsstorlek för säkerhetskopiering|Byte|Maximal|Kumulativ differentiell lagringsstorlek för säkerhetskopiering. Gäller för vCore-baserade databaser. Gäller inte hyperskaladatabaser.|Inget|
|log_backup_size_bytes|Logga lagringsstorlek för säkerhetskopiering|Byte|Maximal|Ackumulerad lagringsstorlek för säkerhetskopiering av loggar. Gäller för vCore-baserade och hyperskalliga databaser.|Inget|
|snapshot_backup_size_bytes|Lagringsstorlek för säkerhetskopiering av ögonblicksbilder|Byte|Maximal|Kumulativ lagringsstorlek för säkerhetskopiering av ögonblicksbilder. Gäller hyperskaladatabaser.|Inget|
|base_blob_size_bytes|Lagringsstorlek för basblobb|Byte|Maximal|Basblolob lagring storlek. Gäller hyperskaladatabaser.|Inget|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servrar/elasticPools

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|Inget|
|database_cpu_percent|CPU-procent|Procent|Medel|CPU-procent|DatabasResourceId|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|Inget|
|database_physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|DatabasResourceId|
|log_write_percent|Log IO-procent|Procent|Medel|Log IO-procent|Inget|
|database_log_write_percent|Log IO-procent|Procent|Medel|Log IO-procent|DatabasResourceId|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent. Gäller för DTU-baserade elastiska pooler.|Inget|
|database_dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|DatabasResourceId|
|storage_percent|Datautrymme som används procent|Procent|Medel|Datautrymme som används procent|Inget|
|workers_percent|Procentandel av arbetstagarna|Procent|Medel|Procentandel av arbetstagarna|Inget|
|database_workers_percent|Procentandel av arbetstagarna|Procent|Medel|Procentandel av arbetstagarna|DatabasResourceId|
|sessions_percent|Procent av sessioner|Procent|Medel|Procent av sessioner|Inget|
|database_sessions_percent|Procent av sessioner|Procent|Medel|Procent av sessioner|DatabasResourceId|
|eDTU_limit|eDTU-gräns|Antal|Medel|eDTU-gräns. Gäller för DTU-baserade elastiska pooler.|Inget|
|storage_limit|Data max storlek|Byte|Medel|Data max storlek|Inget|
|eDTU_used|eDTU används|Antal|Medel|eDTU används. Gäller för DTU-baserade elastiska pooler.|Inget|
|database_eDTU_used|eDTU används|Antal|Medel|eDTU används|DatabasResourceId|
|storage_used|Datautrymme som används|Byte|Medel|Datautrymme som används|Inget|
|database_storage_used|Datautrymme som används|Byte|Medel|Datautrymme som används|DatabasResourceId|
|xtp_storage_percent|OLTP-lagringsprocent i minnet|Procent|Medel|OLTP-lagringsprocent i minnet|Inget|
|cpu_limit|CPU-gräns|Antal|Medel|CPU-gräns. Gäller för vCore-baserade elastiska pooler.|Inget|
|database_cpu_limit|CPU-gräns|Antal|Medel|CPU-gräns|DatabasResourceId|
|cpu_used|PROCESSOR som används|Antal|Medel|PROCESSOR som används. Gäller för vCore-baserade elastiska pooler.|Inget|
|database_cpu_used|PROCESSOR som används|Antal|Medel|PROCESSOR som används|DatabasResourceId|
|sqlserver_process_core_percent|SQL Server-processkärna procent|Procent|Maximal|CPU-användningsprocent för SQL Server-processen, mätt med operativsystemet. Gäller elastiska pooler.|Inget|
|sqlserver_process_memory_percent|Procent av SQL Server-processminne|Procent|Maximal|Procentandel av minnesanvändningen för SQL Server-processen, mätt med operativsystemet. Gäller elastiska pooler.|Inget|
|tempdb_data_size|Tempdb-datafilstorlek Kilobyte|Antal|Maximal|Tempdb-datafilstorlek Kilobyte|Inget|
|tempdb_log_size|Tempdb-loggfilstorlek Kilobyte|Antal|Maximal|Tempdb-loggfilstorlek Kilobyte|Inget|
|tempdb_log_used_percent|Tempdb procent logg används|Procent|Maximal|Tempdb procent logg används|Inget|
|allocated_data_storage|Tilldelat datautrymme|Byte|Medel|Tilldelat datautrymme|Inget|
|database_allocated_data_storage|Tilldelat datautrymme|Byte|Medel|Tilldelat datautrymme|DatabasResourceId|
|allocated_data_storage_percent|Tilldelat datautrymme i procent|Procent|Maximal|Tilldelat datautrymme i procent|Inget|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servrar

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-procent|Procent|Medel|DTU-procent|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Datautrymme som används|Byte|Medel|Datautrymme som används|ElasticPoolResourceId|
|database_storage_used|Datautrymme som används|Byte|Medel|Datautrymme som används|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|DTU används|Antal|Medel|DTU används|DatabasResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|virtual_core_count|Antal virtuella kärnor|Antal|Medel|Antal virtuella kärnor|Inget|
|avg_cpu_percent|Genomsnittlig CPU-procent|Procent|Medel|Genomsnittlig CPU-procent|Inget|
|reserved_storage_mb|Reserverat lagringsutrymme|Antal|Medel|Reserverat lagringsutrymme|Inget|
|storage_space_used_mb|Lagringsutrymme som används|Antal|Medel|Lagringsutrymme som används|Inget|
|io_requests|Antal IO-begäranden|Antal|Medel|Antal IO-begäranden|Inget|
|io_bytes_read|IO byte läsa|Byte|Medel|IO byte läsa|Inget|
|io_bytes_written|IO byte skrivna|Byte|Medel|IO byte skrivna|Inget|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Begagnad kapacitet|Byte|Medel|Använda kontokapacitet|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Den genomsnittliga svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType,ApiName,Autentisering|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden från till på slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|Blob kapacitet|Byte|Medel|Mängden lagringsutrymme som används av lagringskontots Blob-tjänst i byte.|BlobType,Nivå|
|BlobCount (BlobCount)|Antal blob|Antal|Medel|Antalet Blob i lagringskontots Blob-tjänst.|BlobType,Nivå|
|ContainerCount (ContainerCount)|Antal blob-behållare|Antal|Medel|Antalet behållare i lagringskontots Blob-tjänst.|Inget|
|IndexKapacitet|Indexkapacitet|Byte|Medel|Mängden lagringsutrymme som används av ADLS Gen2 (Hierarkiskt) Index i byte.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Den genomsnittliga svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType,ApiName,Autentisering|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden från till på slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TabellKapacitet|Tabellkapacitet|Byte|Medel|Mängden lagringsutrymme som används av lagringskontots tabelltjänst i byte.|Inget|
|TabellCount|Antal bord|Antal|Medel|Antalet tabeller i lagringskontots tabelltjänst.|Inget|
|TableEntityCount|Antal entiteter för tabell|Antal|Medel|Antalet tabellentiteter i lagringskontots tabelltjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Den genomsnittliga svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType,ApiName,Autentisering|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden från till på slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Filkapacitet|Filkapacitet|Byte|Medel|Mängden lagringsutrymme som används av lagringskontots filtjänst i byte.|Fildelning|
|FilCount|Antal filer|Antal|Medel|Antalet filer i lagringskontots filtjänst.|Fildelning|
|Filsharecount|Antal filresurser|Antal|Medel|Antalet filresurser i lagringskontots filtjänst.|Inget|
|FileShareSnapshotCount|Antal ögonblicksbilder av filresurs|Antal|Medel|Antalet ögonblicksbilder som finns på resursen i lagringskontots filtjänst.|Fildelning|
|FileShareSnapshotSize|Ögonblicksbildstorlek för filresurs|Byte|Medel|Mängden lagringsutrymme som används av ögonblicksbilderna i lagringskontots filtjänst i byte.|Fildelning|
|ArkivDelaQuota|Kvotstorlek för filresurs|Byte|Medel|Den övre gränsen för hur mycket lagringsutrymme som kan användas av Azure Files Service i byte.|Fildelning|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering,FileShare|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering,FileShare|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering,FileShare|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Den genomsnittliga svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType,ApiName,Autentisering,FileShare|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden från till på slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering,FileShare|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering,FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Kökapacitet|Kökapacitet|Byte|Medel|Mängden lagringsutrymme som används av lagringskontots kötjänst i byte.|Inget|
|QueueCount (QueueCount)|Antal köer|Antal|Medel|Antalet köer i lagringskontots kötjänst.|Inget|
|QueueMessageCount|Antal kömeddelanden|Antal|Medel|Det ungefärliga antalet kömeddelanden i lagringskontots kötjänst.|Inget|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType,GeoType,ApiName,Autentisering|
|Ingress|Ingress|Byte|Totalt|Mängden inträngningsdata, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType,ApiName,Autentisering|
|Utgående|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType,ApiName,Autentisering|
|Lycka Till Svar|Lyckad serversvarstid|Millisekunder|Medel|Den genomsnittliga svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType,ApiName,Autentisering|
|FramgångE2ELatency|Lyckad E2E-svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden från till på slutna dagar för lyckade begäranden som gjorts till en lagringstjänst eller den angivna API-åtgärden i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType,ApiName,Autentisering|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType,ApiName,Autentisering|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/cacheminnen

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ClientIOPS (Klientiops)|Totalt antal IOPS för klient|Antal|Medel|Hastigheten för klientfiler som bearbetas av cachen.|Inget|
|Klientlatens|Genomsnittlig klientfördröjning|Millisekunder|Medel|Genomsnittlig svarstid för klientfilsåtgärder till lagringscachen.|Inget|
|Klientreadiops|Klienten läser IOPS|CountPerSecond|Medel|Klienten läser åtgärder per sekund.|Inget|
|ClientReadThroughput|Genomsnittligt dataflöde för cacheavläsning|BytesPerSecond|Medel|Klienten läs dataöverföringshastighet.|Inget|
|ClientWriteIOPS|IOPS för klientskrivning|CountPerSecond|Medel|Klientskrivningsåtgärder per sekund.|Inget|
|ClientWriteDetgenomput|Genomsnittligt cache-skrivflöde|BytesPerSecond|Medel|Överföringshastighet för klientskrivningsdata.|Inget|
|ClientMetadataLästos|Klientmetadata läsa IOPS|CountPerSecond|Medel|Hastigheten för klientfilåtgärder som skickas till cacheminnet, exklusive dataläsningar, som inte ändrar beständigt tillstånd.|Inget|
|ClientMetadataWriteIOPS|IOPS för klientmetadata|CountPerSecond|Medel|Hastigheten för klientfilåtgärder som skickas till cacheminnet, exklusive dataskrivningar, som ändrar beständigt tillstånd.|Inget|
|ClientLockIOPS|IOPS för klientlås|CountPerSecond|Medel|Låsningsåtgärder för klientfiler per sekund.|Inget|
|LagringTargetHälsa|Hälsa för lagringsmål|Antal|Medel|Booleska resultat av anslutningstest mellan cache- och lagringsmålen.|Inget|
|Upptid|Upptid|Antal|Medel|Booleska resultat av anslutningstest mellan cache- och övervakningssystemet.|Inget|
|LagringTargetIOPS|Totalt antal lagringsutrymmetar iops|Antal|Medel|Hastigheten för alla filåtgärder som cachen skickar till ett visst StorageTarget.|Lagringsmål|
|LagringTargetWriteIOPS|StorageTarget Skriva IOPS|Antal|Medel|Hastigheten för de filskrivningsåtgärder som cachen skickar till ett visst StorageTarget.|Lagringsmål|
|StorageTargetAsyncWriteGenomströmning|Lagringsmål asynkron skrivdataflöde|BytesPerSecond|Medel|Hastigheten som cacheminnet asynkront skriver data till ett visst StorageTarget. Dessa är opportunistiska skrivningar som inte orsakar klienter att blockera.|Lagringsmål|
|StorageTargetSyncWriteGenomströmning|Synkroniseringsriktigt synkront skrivflöde|BytesPerSecond|Medel|Hastigheten som cacheminnet synkront skriver data till ett visst StorageTarget. Det här är skrivningar som gör att klienter blockerar.|Lagringsmål|
|LagringTargetTotalWriteGenomströmning|Totalt skrivflöde för storagetarget|BytesPerSecond|Medel|Den totala hastigheten som cachen skriver data till ett visst StorageTarget.|Lagringsmål|
|LagringsmålUppriktat|Lagringsriktning svarstid|Millisekunder|Medel|Den genomsnittliga svarstiden för tur och retur för alla filåtgärder som cachen skickar till en partricular StorageTarget.|Lagringsmål|
|StorageTargetMetadataLästos|StorageTarget Metadata Läs IOPS|CountPerSecond|Medel|Hastigheten för filåtgärder som inte ändrar beständigt tillstånd och exklusive läsåtgärden som cachen skickar till ett visst StorageTarget.|Lagringsmål|
|StorageTargetMetadataWriteIOPS|StorageTarget Metadata Skriv IOPS|CountPerSecond|Medel|Hastigheten för filåtgärder som ändrar beständigt tillstånd och exklusive skrivåtgärden, som cachen skickar till ett visst StorageTarget.|Lagringsmål|
|LagringsmålläsIOPS|StorageTarget Läsa IOPS|CountPerSecond|Medel|Hastigheten för filläsningsåtgärder som cachen skickar till ett visst StorageTarget.|Lagringsmål|
|StorageTargetReadAheadGenomströmning|StorageTarget Läs framåt-dataflöde|BytesPerSecond|Medel|Den hastighet som cacheminnet opportunistiskt läser data från StorageTarget.|Lagringsmål|
|LagringsmålFyllUtflöde|Fill-dataflöde för lagringsmål|BytesPerSecond|Medel|Hastigheten som cacheminnet läser data från StorageTarget för att hantera en cachemiss.|Lagringsmål|
|LagringTargetTotalLäs-Tredykningstal|Lagringsrikt totalt läsflöde|BytesPerSecond|Medel|Den totala hastigheten som cachen läser data från ett visst StorageTarget.|Lagringsmål|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerSyncSessionResultat|Resultat för synkronisering av sessions|Antal|Medel|Mått som loggar ett värde på 1 varje gång serverslutpunkten slutförs en synkroniseringssession med cloud-slutpunkten|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal synkroniserade filer|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antalet filer kunde inte synkroniseras|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Synkroniserade byte|Byte|Totalt|Total filstorlek som överförts för synkroniseringssessioner|SyncGroupName,ServerEndpointName,SyncDirection|
|LagringssynkroniseringsserverHeartbeat|Server onlinestatus|Antal|Maximal|Mått som loggar ett värde på 1 varje gång den resigtered servern framgångsrikt registrerar ett pulsslag med Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Återkalla molnnivådelning|Byte|Totalt|Total storlek på data som återkallas av servern|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Återkallad storlek för molnnivå|Byte|Totalt|Storleken på återkallade data|SyncGroupName,ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Dataflöde för återkallar molnnivåer|BytesPerSecond|Medel|Storlek på dataåterkallningsdataflöde|SyncGroupName,ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Återkallar storlek för molnnivå efter program|Byte|Totalt|Storleken på data som återkallas av programmet|SyncGroupName,ServerName,ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups microsoft.storagesync/storageSyncServices/syncGroups microsoft.storagesync/storageSyncServices/syncGroups microsoft.

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal synkroniserade filer|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antalet filer kunde inte synkroniseras|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Synkroniserade byte|Byte|Totalt|Total filstorlek som överförts för synkroniseringssessioner|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Synkroniserade filer|Antal|Totalt|Antal synkroniserade filer|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Filer som inte synkroniseras|Antal|Totalt|Antalet filer kunde inte synkroniseras|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Synkroniserade byte|Byte|Totalt|Total filstorlek som överförts för synkroniseringssessioner|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerHeartbeat (på ett sätt som är)|Server onlinestatus|Antal|Maximal|Mått som loggar ett värde på 1 varje gång den resigtered servern framgångsrikt registrerar ett pulsslag med Cloud Endpoint|ServerResourceId,ServerName|
|ServerRecallIOTotalSizeBytes|Återkalla molnnivådelning|Byte|Totalt|Total storlek på data som återkallas av servern|ServerResourceId,ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ResursUtnyttjande|SU% utnyttjande|Procent|Maximal|SU% utnyttjande|Logisknamn,PartitionId|
|InputEvents|Indatahändelser|Antal|Totalt|Indatahändelser|Logisknamn,PartitionId|
|InputEventBytes|Indatahändelsebyten|Byte|Totalt|Indatahändelsebyten|Logisknamn,PartitionId|
|LateInputEvents|Sena indatahändelser|Antal|Totalt|Sena indatahändelser|Logisknamn,PartitionId|
|OutputEvents|Utdatahändelser|Antal|Totalt|Utdatahändelser|Logisknamn,PartitionId|
|ConversionErrors|Datakonverteringsfel|Antal|Totalt|Datakonverteringsfel|Logisknamn,PartitionId|
|Fel|Körningsfel|Antal|Totalt|Körningsfel|Logisknamn,PartitionId|
|DroppedOrAdjustedEvents|Händelser som inte är i funktion|Antal|Totalt|Händelser som inte är i funktion|Logisknamn,PartitionId|
|AMLCalloutRequests|Funktionsbegäranden|Antal|Totalt|Funktionsbegäranden|Logisknamn,PartitionId|
|AMLCalloutFailedRequests|Misslyckade funktionsbegäranden|Antal|Totalt|Misslyckade funktionsbegäranden|Logisknamn,PartitionId|
|AMLCalloutInputEvents|Funktionshändelser|Antal|Totalt|Funktionshändelser|Logisknamn,PartitionId|
|DeserializationError|Fel vid indataserialisering|Antal|Totalt|Fel vid indataserialisering|Logisknamn,PartitionId|
|EarlyInputEvents|Tidiga inmatningshändelser|Antal|Totalt|Tidiga inmatningshändelser|Logisknamn,PartitionId|
|OutputWatermarkDelaySeconds|Fördröjning av vattenstämpel|Sekunder|Maximal|Fördröjning av vattenstämpel|Logisknamn,PartitionId|
|InputEventsSourcesBacklogged|Eftersläpade indatahändelser|Antal|Maximal|Eftersläpade indatahändelser|Logisknamn,PartitionId|
|InputEventsSourcesPerSecond|Mottagna indatakällor|Antal|Totalt|Mottagna indatakällor|Logisknamn,PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/arbetsytor

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|Pipeline-körningar har avslutats|Antal|Totalt|Antal orchestration pipeline körningar som lyckades, misslyckades eller avbröts|Resultat,FailureType,Pipeline|
|OrchestrationActivityRunsEnded|Aktivitetskörningar har avslutats|Antal|Totalt|Antal orkestreringsaktiviteter som lyckades, misslyckades eller avbröts|Resultat,FailureType,Aktivitet,ActivityType,Pipeline|
|OrkestreringTriggersEnded|Utlösare har avslutats|Antal|Totalt|Antal orchestration-utlösare som lyckades, misslyckades eller avbröts|Resultat,FailureType,utlösare|
|SQLOnDemandLoginAttempts|Inloggningsförsök|Antal|Totalt|Antal inloggningsförsök som lyckades eller misslyckades|Resultat|
|SQLOnDemandQueriesEnded|Frågor har avslutats|Antal|Totalt|Antal frågor som lyckades, misslyckades eller avbröts|Resultat|
|SQLOnDemandQueryProcessedBytes|Bearbetade data|Byte|Totalt|Mängden data som bearbetas av frågor|Inget|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/arbetsytor/bigDataPools

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SparkJobsEnded|Avslutade program|Antal|Totalt|Antal avslutade ansökningar|JobType,JobbResultat|
|KärnorKapacitet|Kärnor kapacitet|Antal|Maximal|Kärnor kapacitet|Inget|
|MemoryCapacityGB|Minneskapacitet (GB)|Antal|Maximal|Minneskapacitet (GB)|Inget|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/arbetsytor/sqlPools

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DWULimit (DWULimit)|DWU-gräns|Antal|Maximal|Sql-poolens mål på servicenivå|Inget|
|DWUUsed|DWU används|Antal|Maximal|Representerar en representation på hög nivå av användning i SQL-poolen. Mätt med DWU-gräns * DWU-procent|Inget|
|DWUUsedPercent|DWU används procent|Procent|Maximal|Representerar en representation på hög nivå av användning i SQL-poolen. Mätt genom att ta det maximala mellan CPU-procent och Data IO-procent|Inget|
|AnslutningarBlockedByFirewall|Anslutningar blockerade av brandvägg|Antal|Totalt|Antal anslutningar som blockerats av brandväggsregler. Se över principer för åtkomstkontroll för DIN SQL-pool och övervaka dessa anslutningar om antalet är högt|Inget|
|AdaptiveCacheHitPercent|Träffprocent för adaptiv cache|Procent|Maximal|Mäter hur väl arbetsbelastningar använder den adaptiva cachen. Använd det här måttet med cachehitprocentmåttet för att avgöra om du vill skala efter ytterligare kapacitet eller köra arbetsbelastningar igen för att hydratisera cachen|Inget|
|AdaptivCacheUsedPercent|Anpassad cache använd procentandel|Procent|Maximal|Mäter hur väl arbetsbelastningar använder den adaptiva cachen. Använd det här måttet med det cacheminne som används procentmått för att avgöra om du vill skala efter ytterligare kapacitet eller köra arbetsbelastningar igen för att hydratisera cachen|Inget|
|LocalTempDBUsedPercent|Lokal tempdb använd procentandel|Procent|Maximal|Lokal tempdb-användning över alla beräkningsnoder - värden avges var femte minut|Inget|
|MinneAnvändarePercent|Använt minne procent|Procent|Maximal|Minnesanvändning för alla noder i SQL-poolen|Inget|
|Anslutningar|Anslutningar|Antal|Totalt|Antal totalt antal inloggningar till SQL-poolen|Resultat|
|WLGActiveQueries|Aktiva frågor för arbetsbelastningsgrupp|Antal|Totalt|De aktiva frågorna inom arbetsbelastningsgruppen. Om du använder det här måttet ofiltrerat och ospliterat visas alla aktiva frågor som körs på systemet|IsUserDedefinierad,WorkloadGroup|
|WLGActiveQueriesTimeouts|Timeout för arbetsbelastningsgruppfråga|Antal|Totalt|Frågor för arbetsbelastningsgruppen som har time(out. Frågetidsutgångar som rapporteras av det här måttet är först när frågan har startat körs (den inkluderar inte väntetid på grund av låsning eller resursväntar)|IsUserDedefinierad,WorkloadGroup|
|WLGAllocationBySystemPercent|Arbetsbelastningsgruppallokering efter systemprocent|Procent|Maximal|Den procentuella fördelningen av resurser i förhållande till hela systemet|IsUserDedefinierad,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Arbetsbelastningsgruppallokering efter max resursprocent|Procent|Maximal|Visar den procentuella fördelningen av resurser i förhållande till resursprocenten För gällande tak per arbetsbelastningsgrupp. Det här måttet ger ett effektivt utnyttjande av arbetsbelastningsgruppen|IsUserDedefinierad,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Effektiv takresurs procent|Procent|Maximal|Den effektiva takresursprocenten för arbetsbelastningsgruppen. Om det finns andra arbetsbelastningsgrupper med min_percentage_resource > 0 sänks effective_cap_percentage_resource proportionellt|IsUserDedefinierad,WorkloadGroup|
|wlg_effective_min_resource_percent|Effektiv minresursprocent|Procent|Minimum|Den effektiva minsta resursprocentinställningen tillåts med tanke på servicenivån och arbetsbelastningsgruppinställningarna. Den effektiva min_percentage_resource kan justeras högre på lägre servicenivåer|IsUserDedefinierad,WorkloadGroup|
|WLGQueuedQueries|Arbetsbelastningsgrupp köade frågor|Antal|Totalt|Ackumulerat antal begäranden i kö efter att max samtidighetsgränsen har uppnåtts|IsUserDedefinierad,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/miljöer

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Inkommande mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från alla händelsehubben eller IoT-hubbhändelsekällor|Inget|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från alla händelsehubben eller IoT-hubbhändelsekällor|Inget|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal antal byte som lästs från alla händelsekällor|Inget|
|IngressStoredBytes|Ingående lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och varit tillgängliga för frågor|Inget|
|IngressStoredEvents|Inträngning lagrade händelser|Antal|Totalt|Antal tillplattade händelser har bearbetats och varit tillgängligt för frågor|Inget|
|IngressReceivedMessagesTimeLag|Ingress mottagna meddelanden Tidsfördröjning|Sekunder|Maximal|Skillnad mellan den tid som meddelandet är köat i händelsekällan och den tid det bearbetas i Ingress|Inget|
|IngressReceivedMessagesCountLag|Ingress mottagna meddelanden Count Lag|Antal|Medel|Skillnaden mellan sekvensnumret för det senast köade meddelandet i händelsekällpartitionen och sekvensnumret för meddelanden som bearbetas i Ingress|Inget|
|WarmStorageMaxProperties|Maxegenskaper för varm lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av Warm Store för PAYG SKU|Inget|
|WarmStorageUsedProperties|Egenskaper för varm lagring används |Antal|Maximal|Antal egenskaper som används av miljön för S1/S2 SKU och antal egenskaper som används av Warm Store för PAYG SKU|Inget|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/miljöer/eventsources

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Inkommande mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från händelsekällan|Inget|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från händelsekällan|Inget|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från händelsekällan|Inget|
|IngressStoredBytes|Ingående lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och varit tillgängliga för frågor|Inget|
|IngressStoredEvents|Inträngning lagrade händelser|Antal|Totalt|Antal tillplattade händelser har bearbetats och varit tillgängligt för frågor|Inget|
|IngressReceivedMessagesTimeLag|Ingress mottagna meddelanden Tidsfördröjning|Sekunder|Maximal|Skillnad mellan den tid som meddelandet är köat i händelsekällan och den tid det bearbetas i Ingress|Inget|
|IngressReceivedMessagesCountLag|Ingress mottagna meddelanden Count Lag|Antal|Medel|Skillnaden mellan sekvensnumret för det senast köade meddelandet i händelsekällpartitionen och sekvensnumret för meddelanden som bearbetas i Ingress|Inget|
|WarmStorageMaxProperties|Maxegenskaper för varm lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av Warm Store för PAYG SKU|Inget|
|WarmStorageUsedProperties|Egenskaper för varm lagring används |Antal|Maximal|Antal egenskaper som används av miljön för S1/S2 SKU och antal egenskaper som används av Warm Store för PAYG SKU|Inget|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Diskläs byte/sek|BytesPerSecond|Medel|Genomsnittligt diskdataflöde på grund av läsåtgärder under exempelperioden.|Inget|
|DiskWriteBytesPerSecond|Byte för diskskrivning/sek|BytesPerSecond|Medel|Genomsnittligt diskdataflöde på grund av skrivåtgärder under exempelperioden.|Inget|
|Diskläs byte|Diskläs byte|Byte|Totalt|Totalt diskflöde på grund av läsåtgärder under exempelperioden.|Inget|
|Byte av diskskrivning|Byte av diskskrivning|Byte|Totalt|Totalt diskflöde på grund av skrivåtgärder under exempelperioden.|Inget|
|DiskReadOperations|Diskläsningsåtgärder|Antal|Totalt|Antalet IO-avläsningsåtgärder under föregående urvalsperiod. Observera att dessa åtgärder kan vara variabelstorlek.|Inget|
|DiskWriteOperations|Skrivåtgärder för disk|Antal|Totalt|Antalet IO-skrivåtgärder under föregående urvalsperiod. Observera att dessa åtgärder kan vara variabelstorlek.|Inget|
|Diskläsningsåtgärder/sek|Diskläsningsåtgärder/sek|CountPerSecond|Medel|Genomsnittligt antal IO-avläsningsåtgärder under föregående urvalsperiod. Observera att dessa åtgärder kan vara variabelstorlek.|Inget|
|Diskskrivningsåtgärder/sek|Diskskrivningsåtgärder/sek|CountPerSecond|Medel|Genomsnittligt antal IO-skrivåtgärder under föregående urvalsperiod. Observera att dessa åtgärder kan vara variabelstorlek.|Inget|
|DiskLäslatens|Svarstid för diskläsning|Millisekunder|Medel|Total lässvarstid. Summan av enheten och kernel läsa latens.|Inget|
|DiskWriteLatency|Svarstid för diskskrivning|Millisekunder|Medel|Total skrivfördröjning. Summan av enheten och kärnskrivningsspärrar.|Inget|
|NätverkInBytesPerSecond|Nätverk i byte/sek|BytesPerSecond|Medel|Genomsnittligt nätverksflöde för mottagen trafik.|Inget|
|NetworkOutBytesPerSecond|Nätverksbyten/sek|BytesPerSecond|Medel|Genomsnittligt nätverksflöde för överförd trafik.|Inget|
|Nätverk – inkommande|Nätverk – inkommande|Byte|Totalt|Totalt nätverksflöde för mottagen trafik.|Inget|
|Nätverk – utgående|Nätverk – utgående|Byte|Totalt|Totalt nätverksflöde för överförd trafik.|Inget|
|Minnesupptredt|Minne som används|Byte|Medel|Mängden datorminne som används av den virtuella datorn.|Inget|
|MinneGranterad|Minne beviljat|Byte|Medel|Mängden minne som beviljades den virtuella datorn av värden. Minne beviljas inte till värden förrän det berörs en gång och beviljat minne kan bytas ut eller ballongförbrännas om VMkernel behöver minnet.|Inget|
|MemoryActive|Minne aktivt|Byte|Medel|Mängden minne som används av den virtuella datorn i det senaste lilla tidsfönstret. Detta är det "sanna" antalet hur mycket minne den virtuella datorn för närvarande har behov av. Ytterligare, oanvänt minne kan bytas ut eller ballongtryckas utan påverkan på gästens prestation.|Inget|
|Procent CPU|Procent CPU|Procent|Medel|CPU-användningen. Det här värdet rapporteras med 100 % som representerar alla processorkärnor i systemet. Som ett exempel använder en tvåvägsd-VM med 50 % av ett system med fyra kärnor helt och hållet med två kärnor.|Inget|
|PercentageCpuReady|Procent CPU klar|Millisekunder|Totalt|Klar tid är den tid som väntar på att processorer ska bli tillgängliga i det senaste uppdateringsintervallet.|Inget|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage (processorpercentage)|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MinnePercentage|Minnesprocent|Procent|Medel|Minnesprocent|Instans|
|DiskQueueLength|Längd på diskkö|Antal|Medel|Längd på diskkö|Instans|
|Mer från HttpQueueLength|Http kö längd|Antal|Medel|Http kö längd|Instans|
|ByteKom med|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|TcpSynSent (|TCP-syn skickad|Antal|Medel|TCP-syn skickad|Instans|
|TcpSynReceived|TCP Syn mottagen|Antal|Medel|TCP Syn mottagen|Instans|
|TcpEtableed|TCP etablerad|Antal|Medel|TCP etablerad|Instans|
|TcpFinWait1|TCP Fin Vänta 1|Antal|Medel|TCP Fin Vänta 1|Instans|
|TcpFinWait2|TCP Fin Vänta 2|Antal|Medel|TCP Fin Vänta 2|Instans|
|TcpClosing (TcpClosing)|TCP Stängning|Antal|Medel|TCP Stängning|Instans|
|TcpCloseWait (på)|TCP Stäng Vänta|Antal|Medel|TCP Stäng Vänta|Instans|
|TcpLastAck (en)|TCP Sista Ack|Antal|Medel|TCP Sista Ack|Instans|
|TcpTimeWait (av TcpTimeWait)|TCP-tid vänta|Antal|Medel|TCP-tid vänta|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Webbplatser (exklusive funktioner) 

> [!NOTE]
> **Filsystemanvändning** är ett nytt mått som distribueras globalt, inga data förväntas om du inte har vitlistats för privat förhandsgranskning.

> [!IMPORTANT]
> **Genomsnittlig svarstid** kommer att inaktuell för att undvika förväxling med måttaggregeringar. Använd **svarstid** som ersättning.

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime (på andra sätt)|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|ByteKom med|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101 (på ett sätt)|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx (på andra sätt)|Http 2xx (på andra sätt)|Antal|Totalt|Http 2xx (på andra sätt)|Instans|
|Http3xx (på andra sätt)|Http 3xx (på)|Antal|Totalt|Http 3xx (på)|Instans|
|Http401 (på ett sätt)|Http 401|Antal|Totalt|Http 401|Instans|
|Http403 (på)|Http 403|Antal|Totalt|Http 403|Instans|
|Http404 (på)|Http 404|Antal|Totalt|Http 404|Instans|
|Http406 (på andra)|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx (på sätt och vis)|Http 4xx (på)|Antal|Totalt|Http 4xx (på)|Instans|
|Http5xx (på sätt och vis)|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Medel|Genomsnittligt arbetsminne|Instans|
|Svarstid|Svarstid|Sekunder|Totalt|Svarstid|Instans|
|Genomsnittligtidstid|Genomsnittlig svarstid (föråldrad)|Sekunder|Medel|Genomsnittlig svarstid|Instans|
|AppAnslutningar|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Hanterar|Antal referenser|Antal|Medel|Antal referenser|Instans|
|Trådar|Antal trådar|Antal|Medel|Antal trådar|Instans|
|PrivateBytes (PrivateBytes)|Privata byte|Byte|Medel|Privata byte|Instans|
|IoReadBytesPerSecond|IO läs byte per sekund|BytesPerSecond|Totalt|IO läs byte per sekund|Instans|
|IoWriteBytesPerSecond|IO skriv byte per sekund|BytesPerSecond|Totalt|IO skriv byte per sekund|Instans|
|IotherBytesPerSecond|IO Andra byte per sekund|BytesPerSecond|Totalt|IO Andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO läsa operationer per sekund|BytesPerSecond|Totalt|IO läsa operationer per sekund|Instans|
|IoWriteOperationsPerSecond|IO-skrivåtgärder per sekund|BytesPerSecond|Totalt|IO-skrivåtgärder per sekund|Instans|
|IotherOperationsPerSecond|IO Övriga transaktioner per sekund|BytesPerSecond|Totalt|IO Övriga transaktioner per sekund|Instans|
|BegärandenInApplicationQueue|Begäranden i programkö|Antal|Medel|Begäranden i programkö|Instans|
|Aktuella sammansättningar|Aktuella sammansättningar|Antal|Medel|Aktuella sammansättningar|Instans|
|Totalt Antal Händer|Totalt antal appdomäner|Antal|Medel|Totalt antal appdomäner|Instans|
|TotaltTillämtsTilldelning|Totalt antal appdomäner har tagits bort|Antal|Medel|Totalt antal appdomäner har tagits bort|Instans|
|Gen0Kolleringar|Gen 0 Sophämtningar|Antal|Totalt|Gen 0 Sophämtningar|Instans|
|Gen1Kolleringar|Gen 1 Sophämtningar|Antal|Totalt|Gen 1 Sophämtningar|Instans|
|Gen2Kolleringar|Gen 2 Sophämtningar|Antal|Totalt|Gen 2 Sophämtningar|Instans|
|Hälsokontrollerstatus|Status för hälsokontroll|Antal|Medel|Status för hälsokontroll|Instans|
|FileSystemUsage|Användning av filsystem|Byte|Medel|Användning av filsystem|Inget|

## <a name="microsoftwebsites-functions"></a>Microsoft.Webbplatser (funktioner)

> [!NOTE]
> **Filsystemanvändning** är ett nytt mått som distribueras globalt, inga data förväntas om du inte har vitlistats för privat förhandsgranskning.

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ByteKom med|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http5xx (på sätt och vis)|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Medel|Genomsnittligt arbetsminne|Instans|
|FunktionUtställningAr|Funktionskörningsenheter|MB / Millisekunder|Totalt|[Funktionskörningsenheter](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instans|
|FunctionExecutionCount|Antal funktionskörningar|Antal|Totalt|Antal funktionskörningar|Instans|
|PrivateBytes (PrivateBytes)|Privata byte|Byte|Medel|Privata byte|Instans|
|IoReadBytesPerSecond|IO läs byte per sekund|BytesPerSecond|Totalt|IO läs byte per sekund|Instans|
|IoWriteBytesPerSecond|IO skriv byte per sekund|BytesPerSecond|Totalt|IO skriv byte per sekund|Instans|
|IotherBytesPerSecond|IO Andra byte per sekund|BytesPerSecond|Totalt|IO Andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO läsa operationer per sekund|BytesPerSecond|Totalt|IO läsa operationer per sekund|Instans|
|IoWriteOperationsPerSecond|IO-skrivåtgärder per sekund|BytesPerSecond|Totalt|IO-skrivåtgärder per sekund|Instans|
|IotherOperationsPerSecond|IO Övriga transaktioner per sekund|BytesPerSecond|Totalt|IO Övriga transaktioner per sekund|Instans|
|BegärandenInApplicationQueue|Begäranden i programkö|Antal|Medel|Begäranden i programkö|Instans|
|Aktuella sammansättningar|Aktuella sammansättningar|Antal|Medel|Aktuella sammansättningar|Instans|
|Totalt Antal Händer|Totalt antal appdomäner|Antal|Medel|Totalt antal appdomäner|Instans|
|TotaltTillämtsTilldelning|Totalt antal appdomäner har tagits bort|Antal|Medel|Totalt antal appdomäner har tagits bort|Instans|
|Gen0Kolleringar|Gen 0 Sophämtningar|Antal|Totalt|Gen 0 Sophämtningar|Instans|
|Gen1Kolleringar|Gen 1 Sophämtningar|Antal|Totalt|Gen 1 Sophämtningar|Instans|
|Gen2Kolleringar|Gen 2 Sophämtningar|Antal|Totalt|Gen 2 Sophämtningar|Instans|
|Hälsokontrollerstatus|Status för hälsokontroll|Antal|Medel|Status för hälsokontroll|Instans|
|FileSystemUsage|Användning av filsystem|Byte|Medel|Användning av filsystem|Inget|

## <a name="microsoftwebsitesslots"></a>Microsoft.webbplatser/platser

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime (på andra sätt)|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|ByteKom med|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101 (på ett sätt)|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx (på andra sätt)|Http 2xx (på andra sätt)|Antal|Totalt|Http 2xx (på andra sätt)|Instans|
|Http3xx (på andra sätt)|Http 3xx (på)|Antal|Totalt|Http 3xx (på)|Instans|
|Http401 (på ett sätt)|Http 401|Antal|Totalt|Http 401|Instans|
|Http403 (på)|Http 403|Antal|Totalt|Http 403|Instans|
|Http404 (på)|Http 404|Antal|Totalt|Http 404|Instans|
|Http406 (på andra)|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx (på sätt och vis)|Http 4xx (på)|Antal|Totalt|Http 4xx (på)|Instans|
|Http5xx (på sätt och vis)|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|MemoryWorkingSet|Arbetsminne|Byte|Medel|Arbetsminne|Instans|
|AverageMemoryWorkingSet|Genomsnittligt arbetsminne|Byte|Medel|Genomsnittligt arbetsminne|Instans|
|Genomsnittligtidstid|Genomsnittlig svarstid|Sekunder|Medel|Genomsnittlig svarstid|Instans|
|HttpResponseTime (På annat sätt)|Svarstid|Sekunder|Medel|Svarstid|Instans|
|FunktionUtställningAr|Funktionskörningsenheter|Antal|Totalt|Funktionskörningsenheter|Instans|
|FunctionExecutionCount|Antal funktionskörningar|Antal|Totalt|Antal funktionskörningar|Instans|
|AppAnslutningar|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Hanterar|Antal referenser|Antal|Medel|Antal referenser|Instans|
|Trådar|Antal trådar|Antal|Medel|Antal trådar|Instans|
|PrivateBytes (PrivateBytes)|Privata byte|Byte|Medel|Privata byte|Instans|
|IoReadBytesPerSecond|IO läs byte per sekund|BytesPerSecond|Totalt|IO läs byte per sekund|Instans|
|IoWriteBytesPerSecond|IO skriv byte per sekund|BytesPerSecond|Totalt|IO skriv byte per sekund|Instans|
|IotherBytesPerSecond|IO Andra byte per sekund|BytesPerSecond|Totalt|IO Andra byte per sekund|Instans|
|IoReadOperationsPerSecond|IO läsa operationer per sekund|BytesPerSecond|Totalt|IO läsa operationer per sekund|Instans|
|IoWriteOperationsPerSecond|IO-skrivåtgärder per sekund|BytesPerSecond|Totalt|IO-skrivåtgärder per sekund|Instans|
|IotherOperationsPerSecond|IO Övriga transaktioner per sekund|BytesPerSecond|Totalt|IO Övriga transaktioner per sekund|Instans|
|BegärandenInApplicationQueue|Begäranden i programkö|Antal|Medel|Begäranden i programkö|Instans|
|Aktuella sammansättningar|Aktuella sammansättningar|Antal|Medel|Aktuella sammansättningar|Instans|
|Totalt Antal Händer|Totalt antal appdomäner|Antal|Medel|Totalt antal appdomäner|Instans|
|TotaltTillämtsTilldelning|Totalt antal appdomäner har tagits bort|Antal|Medel|Totalt antal appdomäner har tagits bort|Instans|
|Gen0Kolleringar|Gen 0 Sophämtningar|Antal|Totalt|Gen 0 Sophämtningar|Instans|
|Gen1Kolleringar|Gen 1 Sophämtningar|Antal|Totalt|Gen 1 Sophämtningar|Instans|
|Gen2Kolleringar|Gen 2 Sophämtningar|Antal|Totalt|Gen 2 Sophämtningar|Instans|
|Hälsokontrollerstatus|Status för hälsokontroll|Antal|Medel|Status för hälsokontroll|Instans|
|FileSystemUsage|Användning av filsystem|Byte|Medel|Användning av filsystem|Inget|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingMiljöer/multiRolePools

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|ByteKom med|Data i|Byte|Totalt|Data i|Instans|
|BytesSent|Data ut|Byte|Totalt|Data ut|Instans|
|Http101 (på ett sätt)|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx (på andra sätt)|Http 2xx (på andra sätt)|Antal|Totalt|Http 2xx (på andra sätt)|Instans|
|Http3xx (på andra sätt)|Http 3xx (på)|Antal|Totalt|Http 3xx (på)|Instans|
|Http401 (på ett sätt)|Http 401|Antal|Totalt|Http 401|Instans|
|Http403 (på)|Http 403|Antal|Totalt|Http 403|Instans|
|Http404 (på)|Http 404|Antal|Totalt|Http 404|Instans|
|Http406 (på andra)|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx (på sätt och vis)|Http 4xx (på)|Antal|Totalt|Http 4xx (på)|Instans|
|Http5xx (på sätt och vis)|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|Genomsnittligtidstid|Genomsnittlig svarstid|Sekunder|Medel|Genomsnittlig svarstid|Instans|
|CpuPercentage (processorpercentage)|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MinnePercentage|Minnesprocent|Procent|Medel|Minnesprocent|Instans|
|DiskQueueLength|Längd på diskkö|Antal|Medel|Längd på diskkö|Instans|
|Mer från HttpQueueLength|Http kö längd|Antal|Medel|Http kö längd|Instans|
|ActiveRequests|Aktiva begäranden|Antal|Totalt|Aktiva begäranden|Instans|
|Totalt Antal direkten|Totalt antal främre ändar|Antal|Medel|Totalt antal främre ändar|Inget|
|SmallAppServicePlanInstances|Arbetare för serviceplan för små app|Antal|Medel|Arbetare för serviceplan för små app|Inget|
|MediumAppServicePlanera|Arbetare för medelstora apptjänstabonnemang|Antal|Medel|Arbetare för medelstora apptjänstabonnemang|Inget|
|LargeAppServicePlanInstances|Arbetare för stora apptjänstplan|Antal|Medel|Arbetare för stora apptjänstplan|Inget|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingMiljöer/workerPools

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ArbetareTotal|Totalt antal arbetstagare|Antal|Medel|Totalt antal arbetstagare|Inget|
|ArbetareTillgängligt|Tillgängliga arbetstagare|Antal|Medel|Tillgängliga arbetstagare|Inget|
|ArbetareUsed|Begagnade arbetare|Antal|Medel|Begagnade arbetare|Inget|
|CpuPercentage (processorpercentage)|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MinnePercentage|Minnesprocent|Procent|Medel|Minnesprocent|Instans|
## <a name="next-steps"></a>Nästa steg
* [Läs om mått i Azure Monitor](data-platform.md)
* [Skapa aviseringar för mått](alerts-overview.md)
* [Exportera mått till lagring, händelsehubb eller logganalys](platform-logs-overview.md)

