---
title: Azure Monitor plattforms mått som kan exporteras via diagnostikinställningar
description: Lista över mått som är tillgängliga för varje resurs typ med Azure Monitor.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: e530663ef4ebfbef9371c48f4c70578b14a63605
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368231"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor plattforms mått som kan exporteras via diagnostikinställningar

Azure Monitor tillhandahåller [plattforms mått](data-platform-metrics.md) som standard utan konfiguration. Det ger dig flera olika sätt att interagera med plattforms mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av PowerShell eller CLI. Se [mått – stöds](metrics-supported.md) för en fullständig lista över plattforms mått som för närvarande är tillgängliga med Azure Monitor den konsoliderade mått pipelinen. Använd [2018-01-01 API-versionen](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)för att fråga efter och komma åt dessa mått. Andra mått kan vara tillgängliga i portalen eller med äldre API: er.

Du kan exportera plattforms måtten från Azure Monitor-pipeline till andra platser på ett av två sätt.
1. Använd [diagnostikinställningar](diagnostic-settings.md) för att skicka till Log Analytics Event Hubs eller Azure Storage.
2. Använd [måtten REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

På grund av erna i Azure Monitor-Dataservern kan inte alla mått exporteras med diagnostiska inställningar. Tabellen nedan visar vilka som kan och inte kan exporteras med diagnostiska inställningar.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Ändra till beteende för NULL-värden och nollvärden 
 
För de plattforms mått som kan exporteras via diagnostikinställningar finns det några mått för vilka Azure Monitor tolkar "0s" som "null-värden". Detta har orsakat viss förvirring mellan Real "0s" (som har spridits av resursen) och tolkat 0s (null-värden). Från **och med den 1 April 2020** kommer de plattforms mått som exporteras via diagnostikinställningar inte längre att exportera ' 0s ', om de inte har spridits av den underliggande resursen. Observera följande:

1.  Om du tar bort en resurs grupp eller en resurs, skickas mått data från de berörda resurserna inte längre till de export destinationer som används. Det innebär att den inte längre visas i Event Hubs, lagrings konton och Log Analytics arbets ytor.
2.  Den här förbättringen är tillgänglig i alla offentliga och privata moln.
3.  Den här ändringen påverkar inte beteendet hos någon av följande upplevelser: 
   - Plattforms resurs loggar som exporter ATS via diagnostikinställningar
   - Mått diagram i Metrics Explorer
   - Avisering om plattforms mått
 
## <a name="metrics-exportable-table"></a>Tabell över mått som kan exporteras 

Tabellen innehåller följande kolumner. 
- Kan exporteras via diagnostikinställningar? 
- Gällande av NULL/0 
- ResourceType 
- Mått 
- MetricDisplayName
- Enhet 
- AggregationType


> [!NOTE]
> Tabellen nedan kan ha en vågrät rullnings List längst ned. Om du tror att du saknar information kontrollerar du att rullnings listen är längst till vänster.  


| Kan exporteras via diagnostikinställningar?  | Avger NULL-värden |  ResourceType  |  Mått  |  MetricDisplayName  |  Enhet  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Ja * * * *  | Nej |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Minne: aktuellt pris för renare  |  Antal  |  Medel | 
| Ja * * * *  | Nej |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Minne: det går inte att krympa rengörings minnet  |  Byte  |  Medel | 
| Ja * * * *  | Nej |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Minne: krympnings utrymme för rensning  |  Byte  |  Medel | 
| Ja * * * *  | Nej |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Trådar: pool med upptagna kommando trådar  |  Antal  |  Medel | 
| Ja * * * *  | Nej |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Trådar: inaktiva trådar för kommando pool  |  Antal  |  Medel | 
| Ja * * * *  | Nej |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Kölängd för kommando bassäng  |  Antal  |  Medel | 
| Ja * * * *  | Nej |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  Anslutning: aktuella anslutningar  |  Antal  |  Medel | 
| Ja * * * *  | Nej |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Aktuella användarsessioner  |  Antal  |  Medel | 
| Ja * * * *  | Nej |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Trådar: lång parsning av upptagna trådar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Trådar: inaktiva trådar för lång parsning  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Trådar: lång parsning av jobb Kölängd  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  M motor minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  M motor privata byte  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  M-motor QPU  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  Virtuella byte i M-motor  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Nedskräpning för minne  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Minne: minnes gräns hårt  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Minne: minnes gräns hög  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Minne: minnes gräns låg  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Minne: minnes gräns VertiPaq  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Minne: minnes användning  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Privata byte  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Trådar: bearbetning av pool upptagna I/O-jobb trådar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Trådar: bearbetning av pool upptagna icke-I/O-trådar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Trådar: behandlar inaktiva I/O-jobb trådar för poolen  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Trådar: behandlar inaktiva icke-I/O-trådar som behandlar poolen  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Trådar: bearbeta pool I/O-jobb Kölängd  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Bearbetar Kölängd för poolen  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Upptagna trådar för frågeprocessorn  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Trådar: inaktiva trådar i lagringspoolen  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Trådar: Kölängd för jobbkö  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  Resurser  |  Minne: kvot  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Minne: kvot blockerad  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Bearbetar: rader konverterade per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Bearbetar: rader lästa per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Bearbetar: rader skrivna per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Trådar: kort parsning upptagna trådar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Trådar: kort parsning inaktiva trådar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Trådar: kort parsning av jobb Kölängd  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Lyckade anslutningar per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Totalt antal anslutnings problem  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Totalt antal anslutnings begär Anden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Minne: VertiPaq som inte är växlat  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Minne: VertiPaq växlat  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Virtuella byte  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Varaktighet för backend-begäranden  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.ApiManagement/service  |  Kapacitet  |  Kapacitet  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Varaktighet  |  Total varaktighet för gateway-begäranden  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Ignorerade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Avvisade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Lyckade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Begränsade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Tids gränsen nåddes för EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Storlek på EventHub-händelser  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Totalt antal EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Misslyckade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Misslyckade Gateway-begäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Andra gateway-begäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Lyckade Gateway-begäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Totalt antal Gateway-begäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Obehöriga Gateway-begäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  AppCpuUsagePercentage  |  Procent andel CPU-användning för app  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  AppMemoryCommitted  |  Tilldelad app-minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  AppMemoryMax  |  Max för app-minne  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  AppMemoryUsed  |  Använt app-minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  GCPauseTotalCount  |  Antal pauser för GC  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  GCPauseTotalTime  |  Total tid för GC-paus  |  Millisekunder  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  MaxOldGenMemoryPoolBytes  |  Maximal tillgänglig gammal generations data storlek  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  OldGenMemoryPoolBytes  |  Gammal generations data storlek  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  OldGenPromotedBytes  |  Befordra till gammal generations data storlek  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  SystemCpuUsagePercentage  |  Procent andel system CPU-användning  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatErrorCount  |  Tomcat globalt fel  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatReceivedBytes  |  Totalt antal mottagna byte i Tomcat  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatRequestMaxTime  |  Max tid för Tomcat-begäran  |  Millisekunder  |  Maximal | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatRequestTotalCount  |  Totalt antal Tomcat-begäranden  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatRequestTotalTime  |  Totalt antal gånger för Tomcat-begäran  |  Millisekunder  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatResponseAvgTime  |  Genomsnittlig tid för Tomcat-begäran  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatSentBytes  |  Totalt antal skickade byte i Tomcat  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatSessionActiveCurrentCount  |  Tomcat-session Alive-antal  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatSessionActiveMaxCount  |  Antal aktiva Tomcat-sessioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatSessionAliveMaxTime  |  Maximal Alive-tid för Tomcat-session  |  Millisekunder  |  Maximal | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatSessionCreatedCount  |  Antal skapade Tomcat-sessioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatSessionExpiredCount  |  Antal utgångna Tomcat-sessioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  TomcatSessionRejectedCount  |  Antal nekade Tomcat-sessioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. AppPlatform/våren  |  YoungGenPromotedBytes  |  Höj data storlek för unga generationer  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Automation/automationAccounts  |  TotalJob  |  Totalt antal jobb  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Den totala uppdaterings distributions datorn körs  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Total uppdaterings distribution körs  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Dedikerat antal kärnor  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Antal noder skapas  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Antal inaktiva noder  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Slutför händelser för borttagning av jobb  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Start händelser för jobb borttagning  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Jobb för att inaktivera slutförda händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Jobb inaktivera start händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Jobb start händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Slutför händelser för avsluta jobb  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  Jobb som avslutar start händelser  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Antal noder för att lämna pooler  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  Antal LowPriority kärnor  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Antal offline-noder  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Skapa händelser för pool  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Slutför händelse för borttagning av pool  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Start händelser för borttagning av pool  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Slutför händelser för storleks ändring av pool  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Starta händelser för att ändra storlek på poolen  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Antal misslyckade noder  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Antalet noder startas om  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Antal noder för avbildning  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Antal noder som körs  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Antalet noder startas  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Starta aktivitet antal misslyckade noder  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Uppgift slutförda händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Aktivitet, misslyckade händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Aktivitetens start händelser  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Antal noder med låg prioritet  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Antal dedikerade noder  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Antal noder som inte kan användas  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Väntar på att starta aktiviteter antal noder  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Aktiva kärnor  |  Aktiva kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Aktiva noder  |  Aktiva noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Inaktiva kärnor  |  Inaktiva kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Inaktiva noder  |  Inaktiva noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Jobbet har slutförts  |  Jobbet har slutförts  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Jobb skickat  |  Jobb skickat  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Lämnar kärnor  |  Lämnar kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Lämnar noder  |  Lämnar noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Blockerade kärnor  |  Blockerade kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Misslyckade noder  |  Misslyckade noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Kvot användning i procent  |  Kvot användning i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Totalt antal kärnor  |  Totalt antal kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Totalt antal noder  |  Totalt antal noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Oanvändbara kärnor  |  Oanvändbara kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. BatchAI/arbets ytor  |  Oanvändbara noder  |  Oanvändbara noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  ConnectionAccepted  |  Godkända anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  ConnectionActive  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  ConnectionHandled  |  Hanterade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Procent andel CPU-användning  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  Lästa byte i IO  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  Skrivna byte i IO  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  MemoryLimit  |  Minnes gräns  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsage  |  Minnesanvändning  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Minnes användnings procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  PendingTransactions  |  Väntande transaktioner  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  ProcessedBlocks  |  Bearbetade block  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  ProcessedTransactions  |  Bearbetade transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  QueuedTransactions  |  Köade transaktioner  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  RequestHandled  |  Hanterade begär Anden  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Blockchain/blockchainMembers  |  StorageUsage  |  Lagrings användning  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits  |  Cacheträffar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits0  |  Cacheträffar (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits1  |  Cacheträffar (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits2  |  Cacheträffar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits3  |  Cacheträffar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits4  |  Cacheträffar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits5  |  Cacheträffar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits6  |  Cacheträffar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits7  |  Cacheträffar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits8  |  Cacheträffar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachehits9  |  Cacheträffar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheLatency  |  Mikrosekunder för cache-fördröjning (för hands version)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses  |  Cachemissar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses0  |  Cachemissar (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses1  |  Cachemissar (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses2  |  Cachemissar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses3  |  Cachemissar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses4  |  Cachemissar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses5  |  Cachemissar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses6  |  Cachemissar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses7  |  Cachemissar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses8  |  Cachemissar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cachemisses9  |  Cachemissar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead  |  Läsning cache  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead0  |  Läsning av cache (Shard 0)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead1  |  Läsning av cache (Shard 1)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead2  |  Läsning av cache (Shard 2)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead3  |  Läsning av cacheminne (Shard 3)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead4  |  Läsning av cacheminne (Shard 4)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead5  |  Läsning av cacheminne (Shard 5)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead6  |  Läsning av cache (Shard 6)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead7  |  Läsning av cache (Shard 7)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead8  |  Läsning av cache (Shard 8)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheRead9  |  Läsning av cacheminne (Shard 9)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite  |  Skrivcache  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite0  |  Skrivcache (Shard 0)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite1  |  Skrivcache (Shard 1)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite2  |  Skrivcache (Shard 2)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite3  |  Skrivcache (Shard 3)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite4  |  Skrivcache (Shard 4)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite5  |  Skrivcache (Shard 5)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite6  |  Skrivcache (Shard 6)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite7  |  Skrivcache (Shard 7)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite8  |  Skrivcache (Shard 8)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  cacheWrite9  |  Skrivcache (Shard 9)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients  |  Anslutna klienter  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients0  |  Anslutna klienter (Shard 0)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients1  |  Anslutna klienter (Shard 1)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients2  |  Anslutna klienter (Shard 2)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients3  |  Anslutna klienter (Shard 3)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients4  |  Anslutna klienter (Shard 4)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients5  |  Anslutna klienter (Shard 5)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients6  |  Anslutna klienter (Shard 6)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients7  |  Anslutna klienter (Shard 7)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients8  |  Anslutna klienter (Shard 8)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  connectedclients9  |  Anslutna klienter (Shard 9)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  fel  |  Fel  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys  |  Avlägsnade nycklar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys0  |  Avlägsnade nycklar (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys1  |  Avlägsnade nycklar (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys2  |  Avlägsnade nycklar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys3  |  Avlägsnade nycklar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys4  |  Avlägsnade nycklar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys5  |  Avlägsnade nycklar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys6  |  Avlägsnade nycklar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys7  |  Avlägsnade nycklar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys8  |  Avlägsnade nycklar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  evictedkeys9  |  Avlägsnade nycklar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys  |  Förfallna nycklar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys0  |  Utgångna nycklar (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys1  |  Utgångna nycklar (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys2  |  Utgångna nycklar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys3  |  Utgångna nycklar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys4  |  Utgångna nycklar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys5  |  Utgångna nycklar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys6  |  Utgångna nycklar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys7  |  Utgångna nycklar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys8  |  Utgångna nycklar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  expiredkeys9  |  Utgångna nycklar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands  |  Sparas  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands0  |  Hämtar (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands1  |  Hämtar (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands2  |  Hämtar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands3  |  Hämtar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands4  |  Hämtar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands5  |  Hämtar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands6  |  Hämtar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands7  |  Hämtar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands8  |  Hämtar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  getcommands9  |  Hämtar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond  |  Åtgärder per sekund  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond0  |  Åtgärder per sekund (Shard 0)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond1  |  Åtgärder per sekund (Shard 1)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond2  |  Åtgärder per sekund (Shard 2)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond3  |  Åtgärder per sekund (Shard 3)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond4  |  Åtgärder per sekund (Shard 4)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond5  |  Åtgärder per sekund (Shard 5)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond6  |  Åtgärder per sekund (Shard 6)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond7  |  Åtgärder per sekund (Shard 7)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond8  |  Åtgärder per sekund (Shard 8)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  operationsPerSecond9  |  Åtgärder per sekund (Shard 9)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime  |  Processor  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime0  |  PROCESSOR (Shard 0)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime1  |  PROCESSOR (Shard 1)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime2  |  PROCESSOR (Shard 2)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime3  |  PROCESSOR (Shard 3)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime4  |  PROCESSOR (Shard 4)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime5  |  PROCESSOR (Shard 5)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime6  |  PROCESSOR (Shard 6)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime7  |  PROCESSOR (Shard 7)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime8  |  PROCESSOR (Shard 8)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  percentProcessorTime9  |  PROCESSOR (Shard 9)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad  |  Server belastning  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad0  |  Server belastning (Shard 0)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad1  |  Server belastning (Shard 1)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad2  |  Server belastning (Shard 2)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad3  |  Server belastning (Shard 3)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad4  |  Server belastning (Shard 4)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad5  |  Server belastning (Shard 5)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad6  |  Server belastning (Shard 6)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad7  |  Server belastning (Shard 7)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad8  |  Server belastning (Shard 8)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  serverLoad9  |  Server belastning (Shard 9)  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands  |  Definierar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands0  |  Uppsättningar (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands1  |  Uppsättningar (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands2  |  Uppsättningar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands3  |  Uppsättningar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands4  |  Uppsättningar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands5  |  Uppsättningar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands6  |  Uppsättningar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands7  |  Uppsättningar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands8  |  Uppsättningar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  setcommands9  |  Uppsättningar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Totalt antal åtgärder  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Totalt antal åtgärder (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Totalt antal åtgärder (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Totalt antal åtgärder (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Totalt antal åtgärder (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Totalt antal åtgärder (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Totalt antal åtgärder (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Totalt antal åtgärder (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Totalt antal åtgärder (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Totalt antal åtgärder (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Totalt antal åtgärder (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys  |  Totalt antal nycklar  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys0  |  Totalt antal nycklar (Shard 0)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys1  |  Totalt antal nycklar (Shard 1)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys2  |  Totalt antal nycklar (Shard 2)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys3  |  Totalt antal nycklar (Shard 3)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys4  |  Totalt antal nycklar (Shard 4)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys5  |  Totalt antal nycklar (Shard 5)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys6  |  Totalt antal nycklar (Shard 6)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys7  |  Totalt antal nycklar (Shard 7)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys8  |  Totalt antal nycklar (Shard 8)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  totalkeys9  |  Totalt antal nycklar (Shard 9)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory  |  Använt minne  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory0  |  Använt minne (Shard 0)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory1  |  Använt minne (Shard 1)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory2  |  Använt minne (Shard 2)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory3  |  Använt minne (Shard 3)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory4  |  Använt minne (Shard 4)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory5  |  Använt minne (Shard 5)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory6  |  Använt minne (Shard 6)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory7  |  Använt minne (Shard 7)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory8  |  Använt minne (Shard 8)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemory9  |  Använt minne (Shard 9)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemorypercentage  |  Använd minnes procent  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss  |  RSS för använt minne  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Använt minne RSS (Shard 0)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss1  |  RSS för använt minne (Shard 1)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Använt minne RSS (Shard 2)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Använt minne RSS (Shard 3)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Använt minne RSS (Shard 4)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Använt minne RSS (Shard 5)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Använt minne RSS (Shard 6)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Använt minne RSS (Shard 7)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Använt minne RSS (Shard 8)  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Använt minne RSS (Shard 9)  |  Byte  |  Maximal | 
| Nej  | Nej |  Microsoft. ClassicCompute/domän namn/platser/roller  |  Disk-lästa byte/s  |  Disk läsning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicCompute/domän namn/platser/roller  |  Disk Läs åtgärder/SEK  |  Disk Läs åtgärder/SEK  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft. ClassicCompute/domän namn/platser/roller  |  Disk-skrivna byte/s  |  Disk skrivning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicCompute/domän namn/platser/roller  |  Disk skrivnings åtgärder/SEK  |  Disk skrivnings åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicCompute/domän namn/platser/roller  |  Nätverk – inkommande  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicCompute/domän namn/platser/roller  |  Nätverk – utgående  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicCompute/domän namn/platser/roller  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.ClassicCompute/virtualMachines  |  Disk-lästa byte/s  |  Disk läsning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.ClassicCompute/virtualMachines  |  Disk Läs åtgärder/SEK  |  Disk Läs åtgärder/SEK  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft.ClassicCompute/virtualMachines  |  Disk-skrivna byte/s  |  Disk skrivning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.ClassicCompute/virtualMachines  |  Disk skrivnings åtgärder/SEK  |  Disk skrivnings åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.ClassicCompute/virtualMachines  |  Nätverk – inkommande  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.ClassicCompute/virtualMachines  |  Nätverk – utgående  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.ClassicCompute/virtualMachines  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.ClassicStorage/storageAccounts  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.ClassicStorage/storageAccounts  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.ClassicStorage/storageAccounts  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.ClassicStorage/storageAccounts  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Använd kapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Blobkapacitet  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Antalet blobar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Antal blobcontainrar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Index kapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileCapacity  |  Fil kapacitet  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileCount  |  Antal filer  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Antal fil resurser  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Fil resursens kvot storlek  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Antal ögonblicks bilder av fil resurs  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Storlek på fil resursens ögonblicks bild  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Kökapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Antal köer  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Antal kömeddelanden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Tabellkapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Antal tabeller  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Antal tabellentiteter  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Blockerade anrop  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  CharactersTrained  |  Upptränade tecken  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Översatta tecken  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  Klient fel  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  Datain  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  Data  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  Svarstid  |  Svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  Server fel  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Tal sessionens varaktighet  |  Sekunder  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  Lyckade anrop  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  Totalt antal anrop  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  Totalt antal fel  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Totalt antal token-anrop  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  Totalt antal transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Förbrukade CPU-krediter  |  Förbrukade CPU-krediter  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Återstående CPU-krediter  |  Återstående CPU-krediter  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Data disk Queue djup  |  Data disk Queue djup (för hands version)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Lästa byte på datadisk/SEK  |  Lästa byte på datadisk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Läs åtgärder för data disk/SEK  |  Läs åtgärder för data disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Skrivna byte på datadisk/SEK  |  Skrivna byte på datadisk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Skriv åtgärder för data disk/SEK  |  Skriv åtgärder för data disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Lästa byte på disk  |  Lästa byte på disk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Disk Läs åtgärder/SEK  |  Disk Läs åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Disk-skrivna byte  |  Disk-skrivna byte  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Disk skrivnings åtgärder/SEK  |  Disk skrivnings åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Inkommande flöden  |  Inkommande flöden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Högsta skapande frekvens för inkommande flöden  |  Maximal skapande frekvens för inkommande flöden (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Nätverk – inkommande  |  Nätverk i fakturerbart (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Totalt nätverk  |  Totalt nätverk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Nätverk – utgående  |  Fakturerbart för nätverk (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Totalt nätverk  |  Totalt nätverk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Ködjup för OS-disk  |  Ködjup för OS-disk (för hands version)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Lästa byte för OS-disk/SEK  |  Lästa byte för operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Läs åtgärder för operativ system disk/SEK  |  Läs åtgärder för operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Skrivna byte per operativ system disk/SEK  |  Skrivna byte per operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Skriv åtgärder för operativ system disk/SEK  |  Skriv åtgärder för operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  KÖDJUP för operativ system per disk  |  OS-KÖDJUP (inaktuellt)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Lästa byte per operativ system disk/SEK  |  Lästa byte för OS-disk/SEK (inaktuell)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Läs åtgärder för operativ system per disk/SEK  |  Läs åtgärder för operativ system disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Skrivna byte per operativ system disk/SEK  |  Skrivna byte per operativ system disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Skriv åtgärder för operativ system per disk/SEK  |  Skriv åtgärder för operativ system disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Utgående flöden  |  Utgående flöden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Längsta skapande frekvens för utgående flöden  |  Längsta skapande frekvens för utgående flöden (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Per disk KÖDJUP  |  KÖDJUP för data disk (inaktuellt)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Lästa byte per disk/SEK  |  Lästa byte på datadisk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Läs åtgärder per disk/SEK  |  Läs åtgärder för data disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Skrivna byte per disk/SEK  |  Skrivna byte på datadisk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Skriv åtgärder per disk/SEK  |  Skriv åtgärder för data disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Läsning av Premium data disk-cache  |  Läsning av Premium data disk-cache (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Read missar i Premium data-diskcachen  |  Read missar i Premium data disk cache (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Läsning av Premium OS-diskcachen  |  Läsning av Premium OS-diskcachen (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachines  |  Read missar i Premium OS-diskcachen  |  Read missar i Premium OS-diskcachen (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Förbrukade CPU-krediter  |  Förbrukade CPU-krediter  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Återstående CPU-krediter  |  Återstående CPU-krediter  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Data disk Queue djup  |  Data disk Queue djup (för hands version)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Lästa byte på datadisk/SEK  |  Lästa byte på datadisk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Läs åtgärder för data disk/SEK  |  Läs åtgärder för data disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Skrivna byte på datadisk/SEK  |  Skrivna byte på datadisk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Skriv åtgärder för data disk/SEK  |  Skriv åtgärder för data disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Lästa byte på disk  |  Lästa byte på disk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Läs åtgärder/SEK  |  Disk Läs åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Disk-skrivna byte  |  Disk-skrivna byte  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Disk skrivnings åtgärder/SEK  |  Disk skrivnings åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Inkommande flöden  |  Inkommande flöden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Högsta skapande frekvens för inkommande flöden  |  Maximal skapande frekvens för inkommande flöden (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Nätverk – inkommande  |  Nätverk i fakturerbart (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Totalt nätverk  |  Totalt nätverk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Nätverk – utgående  |  Fakturerbart för nätverk (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Totalt nätverk  |  Totalt nätverk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Ködjup för OS-disk  |  Ködjup för OS-disk (för hands version)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Lästa byte för OS-disk/SEK  |  Lästa byte för operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Läs åtgärder för operativ system disk/SEK  |  Läs åtgärder för operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Skrivna byte per operativ system disk/SEK  |  Skrivna byte per operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Skriv åtgärder för operativ system disk/SEK  |  Skriv åtgärder för operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  KÖDJUP för operativ system per disk  |  OS-KÖDJUP (inaktuellt)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Lästa byte per operativ system disk/SEK  |  Lästa byte för OS-disk/SEK (inaktuell)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Läs åtgärder för operativ system per disk/SEK  |  Läs åtgärder för operativ system disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Skrivna byte per operativ system disk/SEK  |  Skrivna byte per operativ system disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Skriv åtgärder för operativ system per disk/SEK  |  Skriv åtgärder för operativ system disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Utgående flöden  |  Utgående flöden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Längsta skapande frekvens för utgående flöden  |  Längsta skapande frekvens för utgående flöden (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Per disk KÖDJUP  |  KÖDJUP för data disk (inaktuellt)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Lästa byte per disk/SEK  |  Lästa byte på datadisk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Läs åtgärder per disk/SEK  |  Läs åtgärder för data disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Skrivna byte per disk/SEK  |  Skrivna byte på datadisk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Skriv åtgärder per disk/SEK  |  Skriv åtgärder för data disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Läsning av Premium data disk-cache  |  Läsning av Premium data disk-cache (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Read missar i Premium data-diskcachen  |  Read missar i Premium data disk cache (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Läsning av Premium OS-diskcachen  |  Läsning av Premium OS-diskcachen (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets  |  Read missar i Premium OS-diskcachen  |  Read missar i Premium OS-diskcachen (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Förbrukade CPU-krediter  |  Förbrukade CPU-krediter  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Återstående CPU-krediter  |  Återstående CPU-krediter  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Data disk Queue djup  |  Data disk Queue djup (för hands version)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lästa byte på datadisk/SEK  |  Lästa byte på datadisk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Läs åtgärder för data disk/SEK  |  Läs åtgärder för data disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Skrivna byte på datadisk/SEK  |  Skrivna byte på datadisk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Skriv åtgärder för data disk/SEK  |  Skriv åtgärder för data disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lästa byte på disk  |  Lästa byte på disk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Läs åtgärder/SEK  |  Disk Läs åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk-skrivna byte  |  Disk-skrivna byte  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk skrivnings åtgärder/SEK  |  Disk skrivnings åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Inkommande flöden  |  Inkommande flöden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Högsta skapande frekvens för inkommande flöden  |  Maximal skapande frekvens för inkommande flöden (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Nätverk – inkommande  |  Nätverk i fakturerbart (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Totalt nätverk  |  Totalt nätverk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Nätverk – utgående  |  Fakturerbart för nätverk (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Totalt nätverk  |  Totalt nätverk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Ködjup för OS-disk  |  Ködjup för OS-disk (för hands version)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lästa byte för OS-disk/SEK  |  Lästa byte för operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Läs åtgärder för operativ system disk/SEK  |  Läs åtgärder för operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Skrivna byte per operativ system disk/SEK  |  Skrivna byte per operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Skriv åtgärder för operativ system disk/SEK  |  Skriv åtgärder för operativ system disk/SEK (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  KÖDJUP för operativ system per disk  |  OS-KÖDJUP (inaktuellt)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lästa byte per operativ system disk/SEK  |  Lästa byte för OS-disk/SEK (inaktuell)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Läs åtgärder för operativ system per disk/SEK  |  Läs åtgärder för operativ system disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Skrivna byte per operativ system disk/SEK  |  Skrivna byte per operativ system disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Skriv åtgärder för operativ system per disk/SEK  |  Skriv åtgärder för operativ system disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Utgående flöden  |  Utgående flöden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Längsta skapande frekvens för utgående flöden  |  Längsta skapande frekvens för utgående flöden (för hands version)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per disk KÖDJUP  |  KÖDJUP för data disk (inaktuellt)  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lästa byte per disk/SEK  |  Lästa byte på datadisk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Läs åtgärder per disk/SEK  |  Läs åtgärder för data disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Skrivna byte per disk/SEK  |  Skrivna byte på datadisk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Skriv åtgärder per disk/SEK  |  Skriv åtgärder för data disk/SEK (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Läsning av Premium data disk-cache  |  Läsning av Premium data disk-cache (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Read missar i Premium data-diskcachen  |  Read missar i Premium data disk cache (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Läsning av Premium OS-diskcachen  |  Läsning av Premium OS-diskcachen (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Read missar i Premium OS-diskcachen  |  Read missar i Premium OS-diskcachen (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. ContainerInstance/containerGroups  |  CpuUsage  |  CPU-användning  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ContainerInstance/containerGroups  |  MemoryUsage  |  Minnesanvändning  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Mottagna nätverks byte per sekund  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Överförda nätverks byte per sekund  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. ContainerRegistry/register  |  RunDuration  |  Körnings tid  |  Millisekunder  |  Totalt | 
| **Ja**  | Nej |  Microsoft. ContainerRegistry/register  |  SuccessfulPullCount  |  Antal lyckade pull-överföringar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ContainerRegistry/register  |  SuccessfulPushCount  |  Antal lyckade push-meddelanden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ContainerRegistry/register  |  TotalPullCount  |  Totalt antal hämtningar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. ContainerRegistry/register  |  TotalPushCount  |  Totalt antal push-meddelanden  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft. container service/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Totalt antal tillgängliga processor kärnor i ett hanterat kluster  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft. container service/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Total mängd tillgängligt minne i ett hanterat kluster  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft. container service/managedClusters  |  kube_node_status_condition  |  Status för olika nod villkor  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft. container service/managedClusters  |  kube_pod_status_phase  |  Antal poddar per fas  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft. container service/managedClusters  |  kube_pod_status_ready  |  Antal poddar i klart läge  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  Availablecapacity;)  |  Tillgänglig kapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Överförda moln byte (enhet)  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Överförda moln byte (resurs)  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Data flöde för moln hämtning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Data flöde för moln hämtning (resurs)  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Moln överförings data flöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Moln överförings data flöde (resurs)  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Edge Compute-minnes användning  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Edge Compute-procent CPU  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Läs data flöde (nätverk)  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Skriv data flöde (nätverk)  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  Enhet  |  Total kapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataFactory/datafactories  |  FailedRuns  |  Misslyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/datafactories  |  SuccessfulRuns  |  Lyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  ActivityCancelledRuns  |  Avbrutna aktiviteter kör mått  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  ActivityFailedRuns  |  Misslyckad aktivitet kör mått  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  ActivitySucceededRuns  |  Genomförd aktivitet kör mått  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  FactorySizeInGbUnits  |  Total fabriks storlek (GB enhet)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  IntegrationRuntimeAvailableMemory  |  Tillgängligt minne för integration runtime  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  IntegrationRuntimeAverageTaskPickupDelay  |  Varaktighet för integration runtime-kö  |  Sekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  IntegrationRuntimeCpuPercentage  |  PROCESSOR användning för integration runtime  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  IntegrationRuntimeQueueLength  |  Kölängd för integration runtime  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  MaxAllowedFactorySizeInGbUnits  |  Högsta tillåtna fabriks storlek (GB enhet)  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  MaxAllowedResourceCount  |  Maximalt antal tillåtna entiteter  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  PipelineCancelledRuns  |  Avbruten pipeline kör mått  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  PipelineFailedRuns  |  Misslyckad pipeline kör mått  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  PipelineSucceededRuns  |  Slutförd pipeline kör mått  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  ResourceCount  |  Totalt antal entiteter  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  TriggerCancelledRuns  |  Avbrutna utlösare kör mått  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  TriggerFailedRuns  |  Misslyckad utlösare kör mått  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DataFactory/fabriker  |  TriggerSucceededRuns  |  Lyckade utlösare kör mått  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Tid för avbruten AU  |  Sekunder  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Misslyckad AU-tid  |  Sekunder  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Lyckad AU-tid  |  Sekunder  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Avbrutna jobb  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Misslyckade jobb  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Slutförda jobb  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeStore/accounts  |  DataRead  |  Lästa data  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  Skrivna data  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  Läs begär Anden  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  Totalt lagrings utrymme  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  Skriv förfrågningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Lagring av säkerhets kopior som används  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Misslyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  I/o procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Minnes procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Fördröjning för replikering på några sekunder  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Server logg lagrings gräns  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Server logg lagrings procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Server logg lagring används  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Lagrings gräns  |  Byte  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Lagrings procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Använt lagrings utrymme  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  active_connections  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  backup_storage_used  |  Lagring av säkerhets kopior som används  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  connections_failed  |  Misslyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  io_consumption_percent  |  I/o procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  memory_percent  |  Minnes procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  network_bytes_egress  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  network_bytes_ingress  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  seconds_behind_master  |  Fördröjning för replikering på några sekunder  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  serverlog_storage_limit  |  Server logg lagrings gräns  |  Byte  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  serverlog_storage_percent  |  Server logg lagrings procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  serverlog_storage_usage  |  Server logg lagring används  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  storage_limit  |  Lagrings gräns  |  Byte  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  storage_percent  |  Lagrings procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DBforMySQL/servers  |  storage_used  |  Använt lagrings utrymme  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Lagring av säkerhets kopior som används  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Misslyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  I/o procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Minnes procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Maximal fördröjning mellan repliker  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Replik fördröjning  |  Sekunder  |  Maximal | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Server logg lagrings gräns  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Server logg lagrings procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Server logg lagring används  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Lagrings gräns  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Lagrings procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Använt lagrings utrymme  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/serversv2  |  IOPS  |  IOPS  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Minnes procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Lagrings procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Använt lagrings utrymme  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Devices/Account  |  digitaltwins. telemetri. noder  |  Plats hållare för telemetri för digitala dubbla noder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Övergivna C2D-meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. commands. utgående. Complete. lyckades  |  C2D meddelande leveranser har slutförts  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Avvisade C2D-meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. Methods. Failure  |  Misslyckade direkta metod anrop  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. Methods. requestSize  |  Begär ande storlek för direkta metod anrop  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. Methods. responseSize  |  Svars storlek för direkta metod anrop  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Direkta metod anrop  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Det gick inte att dubbla läsningar från Server delen  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Svars storlek för dubbla läsningar från Server delen  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Lyckades dubbla läspaket från Server delen  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Misslyckade dubbla uppdateringar från Server delen  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Storlek på dubbla uppdateringar från Server delen  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Lyckades dubbla uppdateringar från Server delen  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  C2D meddelanden har förfallit (förhands granskning)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  konfigurationer  |  Konfigurations mått  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Anslutna enheter (förhands granskning)  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Routning: meddelanden som levereras till meddelanden/händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Routning: meddelanden levererade till Händelsehubben  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Routning: meddelanden levererade till Service Bus kö  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Routning: meddelanden levererade till Service Bus ämnet  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  D2C. endpoints. utgående. Storage  |  Routning: meddelanden som levereras till lagring  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  Routning: blobbar levererade till lagring  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  D2C. endpoints. utgående. Storage. byte  |  Routning: data som levereras till lagring  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Routning: meddelande fördröjning för meddelanden/händelser  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Routning: meddelande fördröjning för Event Hub  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Routning: meddelande fördröjning för Service Bus kö  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Routning: meddelande fördröjning för Service Bus ämne  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  Routning: meddelande fördröjning för lagring  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Routning: telemetri ignoreras   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Routning: meddelanden levererade till reserv  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Routning: telemetri-meddelanden är inkompatibla  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Routning: telemetri-meddelanden har överblivna   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Routning: telemetri meddelanden levereras  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Skicka försök för telemetri  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Antal begränsnings fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Meddelande om telemetri  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Misslyckade dubbla läsningar från enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Svars storlek för dubbla läsningar från enheter  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Lyckades dubbla läsningar från enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Misslyckade dubbla uppdateringar från enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Storlek på dubbla uppdateringar från enheter  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Lyckade dubbla uppdateringar från enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Totalt antal meddelanden som används  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Total användning av enhets data  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Total användning av enhets data (för hands version)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Anslutna enheter (inaktuella)   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Totalt antal enheter (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Event Grid leveranser (för hands version)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Event Grid svars tid (för hands version)  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  Jobs. cancelJob. Failure  |  Misslyckade jobb-annulleringar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  Jobs. cancelJob. lyckades  |  Slutförda jobb avbokningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobb. slutfört  |  Slutförda jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Det gick inte att skapa metod anrops jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Lyckade skapande av metod anrops jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Det gick inte att skapa dubbla uppdaterings jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Skapandet av dubbla uppdaterings jobb lyckades  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobb. misslyckades  |  Misslyckade jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  Jobs. listJobs. Failure  |  Misslyckade anrop till List jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Lyckade anrop till List jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  Jobs. queryJobs. Failure  |  Misslyckade jobb frågor  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  Jobs. queryJobs. lyckades  |  Slutförda jobb frågor  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Totalt antal enheter (förhands granskning)  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries. Failure  |  Misslyckade dubbla frågor  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Resultat storlek för dubbla frågor  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries. lyckades  |  Lyckades dubbla frågor  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Attesterings försök  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Tilldelade enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Registrerings försök  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  AvailableStorage  |  Tillgängligt lagringsutrymme  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Cassandra-anslutningens stängningar  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Avgifter för Cassandra-begäran  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  CassandraRequests  |  Cassandra-begäranden  |  Antal  |  Antal | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  DataUsage  |  Dataanvändning  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Antal  |  Antal | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  DocumentCount  |  Antal dokument  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  DocumentQuota  |  Dokument kvot  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  IndexUsage  |  Index användning  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  MetadataRequests  |  Begär Anden om metadata  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Mongo begär ande avgift  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequests  |  Mongo-begäranden  |  Antal  |  Antal | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Mongo begär ande frekvens  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Mongo ta bort begär ande frekvens  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Mongo infoga begär ande frekvens  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Mongo för förfrågningar  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Frekvens för mongo uppdaterings begär Anden  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Etablerat dataflöde  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  ReplicationLatency  |  Fördröjning för P99-replikering  |  Millisekunder  |  Medel | 
| Nej  | Nej |  Microsoft. DocumentDB/databaseAccounts  |  ServiceAvailability  |  Tjänst tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. DocumentDB/databaseAccounts  |  TotalRequests  |  Totalt antal förfrågningar  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft. DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Totalt antal enheter för programbegäran  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft. EnterpriseKnowledgeGraph/Services  |  FailureCount  |  Antal haverier  |  Antal  |  Antal | 
| Nej  | Nej |  Microsoft. EnterpriseKnowledgeGraph/Services  |  SuccessCount  |  Antal lyckade  |  Antal  |  Antal | 
| Nej  | Nej |  Microsoft. EnterpriseKnowledgeGraph/Services  |  SuccessLatency  |  Svars tid  |  Millisekunder  |  Medel | 
| Nej  | Nej |  Microsoft. EnterpriseKnowledgeGraph/Services  |  TransactionCount  |  Antal transaktioner  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/Domains  |  DeadLetteredCount  |  Obeställbara, Brevade händelser  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft. EventGrid/Domains  |  DeliveryAttemptFailCount  |  Misslyckade leverans händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/Domains  |  DeliverySuccessCount  |  Levererade händelser  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft. EventGrid/Domains  |  DestinationProcessingDurationInMs  |  Varaktighet för mål bearbetning  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/Domains  |  DroppedEventCount  |  Ignorerade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/Domains  |  MatchedEventCount  |  Matchade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/Domains  |  PublishFailCount  |  Publicera misslyckade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/Domains  |  PublishSuccessCount  |  Publicerade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/Domains  |  PublishSuccessLatencyInMs  |  Slutför svars tid för publicering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Obeställbara, Brevade händelser  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft. EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Misslyckade leverans händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Levererade händelser  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft. EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Varaktighet för mål bearbetning  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/eventSubscriptions  |  DroppedEventCount  |  Ignorerade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/eventSubscriptions  |  MatchedEventCount  |  Matchade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/extensionTopics  |  PublishFailCount  |  Publicera misslyckade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessCount  |  Publicerade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Slutför svars tid för publicering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/extensionTopics  |  UnmatchedEventCount  |  Omatchade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/ämnen  |  PublishFailCount  |  Publicera misslyckade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/ämnen  |  PublishSuccessCount  |  Publicerade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/ämnen  |  PublishSuccessLatencyInMs  |  Slutför svars tid för publicering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. EventGrid/ämnen  |  UnmatchedEventCount  |  Omatchade händelser  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  AvailableMemory  |  Tillgängligt minne  |  Procent  |  Maximal | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Samla in efter släpning.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Hämtade byte.  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  CapturedMessages  |  Fångade meddelanden.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  ConnectionsClosed  |  Stängda anslutningar.  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Öppna anslutningar.  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  Processor  |  Processor  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Inkommande byte.  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  Inkommande meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  Inkommande begär Anden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Utgående byte.  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  Utgående meddelanden  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  Kvoten överskreds.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  ServerErrors  |  Server fel.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  Lyckade förfrågningar  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Begränsade begär Anden.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/clusters  |  UserErrors  |  Användar fel.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Samla in efter släpning.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Hämtade byte.  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Fångade meddelanden.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  ConnectionsClosed  |  Stängda anslutningar.  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Öppna anslutningar.  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHABL  |  Arkivera efter släpning meddelanden (inaktuellt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Arkiv meddelande genom strömning (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Arkivera meddelanden (inaktuellt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Inkommande byte (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Inkommande byte (inaktuell) (inaktuell)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Inkommande meddelanden (inaktuellt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Utgående byte (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Utgående byte (inaktuell) (inaktuell)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Utgående meddelanden (inaktuellt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Misslyckade förfrågningar (inaktuellt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Inkommande byte.  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Inkommande meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Inkommande begär Anden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Inkommande meddelanden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  INREQS  |  Inkommande begär Anden (inaktuellt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  MELLAN  |  Interna Server fel (inaktuellt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Andra fel (inaktuellt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Utgående byte.  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Utgående meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Utgående meddelanden (inaktuella)  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Kvoten överskreds.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Server fel.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  Storlek  |  Storlek  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Lyckade förfrågningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Lyckade förfrågningar (inaktuellt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Serverns upptaget fel (inaktuellt)  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Begränsade begär Anden.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.EventHub/namespaces  |  UserErrors  |  Användar fel.  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. HDInsight/kluster  |  CategorizedGatewayRequests  |  Kategoriserade Gateway-begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. HDInsight/kluster  |  GatewayRequests  |  Gateway-begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. HDInsight/kluster  |  NumActiveWorkers  |  Antal aktiva arbetare  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft. HDInsight/kluster  |  ScalingRequests  |  Skalnings begär Anden  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Mätnings tröskel  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Observerad kapacitet  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Observerat mått värde  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Initierade skalnings åtgärder  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  availabilityResults/availabilityPercentage  |  Tillgänglighet  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft. Insights/komponenter  |  availabilityResults/antal  |  Tillgänglighetstester  |  Antal  |  Antal | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  availabilityResults/varaktighet  |  Tillgänglighets testets varaktighet  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  browserTimings/networkDuration  |  Nätverks anslutnings tid för sid inläsning  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  browserTimings/processingDuration  |  Klient bearbetnings tid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  browserTimings/receiveDuration  |  Tar emot svars tid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  browserTimings/sendDuration  |  Tid för att skicka begäran  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  browserTimings/totalDuration  |  Sid inläsnings tid för webbläsare  |  Millisekunder  |  Medel | 
| Nej  | Nej |  Microsoft. Insights/komponenter  |  beroenden/antal  |  Beroende anrop  |  Antal  |  Antal | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  beroenden/varaktighet  |  Beroende varaktighet  |  Millisekunder  |  Medel | 
| Nej  | Nej |  Microsoft. Insights/komponenter  |  beroenden/misslyckades  |  Beroende anrops problem  |  Antal  |  Antal | 
| Nej  | Nej |  Microsoft. Insights/komponenter  |  undantag/webbläsare  |  Webbläsarundantag  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft. Insights/komponenter  |  undantag/antal  |  Undantag  |  Antal  |  Antal | 
| Nej  | Nej |  Microsoft. Insights/komponenter  |  undantag/Server  |  Server undantag  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft. Insights/komponenter  |  pageViews/antal  |  Sid visningar  |  Antal  |  Antal | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  pageViews/varaktighet  |  Inläsnings tid för sid visning  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Insights/komponenter  |  performanceCounters/exceptionsPerSecond  |  Undantags frekvens  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  performanceCounters/memoryAvailableBytes  |  Tillgängligt minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  performanceCounters/processCpuPercentage  |  Processor-CPU  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  performanceCounters/processIOBytesPerSecond  |  Process-IO-hastighet  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  performanceCounters/processorCpuPercentage  |  Processor tid  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  performanceCounters/processPrivateBytes  |  Privata byte för process  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  performanceCounters/requestExecutionTime  |  Körnings tid för HTTP-begäran  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Insights/komponenter  |  performanceCounters/requestsInQueue  |  HTTP-begäranden i program kön  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Insights/komponenter  |  performanceCounters/requestsPerSecond  |  Hastighet för HTTP-begäran  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft. Insights/komponenter  |  begär Anden/antal  |  Server begär Anden  |  Antal  |  Antal | 
| **Ja**  | Nej |  Microsoft. Insights/komponenter  |  begär Anden/varaktighet  |  Serversvarstid  |  Millisekunder  |  Medel | 
| Nej  | Nej |  Microsoft. Insights/komponenter  |  begär Anden/misslyckade  |  Misslyckade förfrågningar  |  Antal  |  Antal | 
| Nej  | Nej |  Microsoft. Insights/komponenter  |  begär Anden/pris  |  Server begär ande frekvens  |  CountPerSecond  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Insights/komponenter  |  spårning/antal  |  Anden  |  Antal  |  Antal | 
| **Ja**  | Nej |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Totalt antal tjänst-API-träffar  |  Antal  |  Antal | 
| **Ja**  | Nej |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Övergripande service API-latens  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Totalt antal service API-resultat  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  CacheUtilization  |  Användning av cache  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  ContinuousExportMaxLatenessMinutes  |  Högsta antal förseningar i minuter för kontinuerlig export  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  ContinuousExportNumOfRecordsExported  |  Kontinuerlig export-antal exporterade poster  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  ContinuousExportPendingCount  |  Antal väntande pågående export  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  ContinuousExportResult  |  Resultat av kontinuerlig export  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  Processor  |  Processor  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  EventsProcessedForEventHubs  |  Bearbetade händelser (för Event/IoT-hubbar)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  ExportUtilization  |  Exportera användning  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  IngestionLatencyInSeconds  |  Inmatnings svars tid (i sekunder)  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  IngestionResult  |  Inmatnings resultat  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  IngestionUtilization  |  Förbruknings användning  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  IngestionVolumeInMB  |  Inmatnings volym (i MB)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  KeepAlive  |  Behåll Alive  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  QueryDuration  |  Frågevaraktighet  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  SteamingIngestRequestRate  |  Begär ande frekvens för strömning  |  Antal  |  RateRequestsPerSecond | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  StreamingIngestDataRate  |  Data hastighet för strömnings intag  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  StreamingIngestDuration  |  Hämtnings tid för strömning  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Kusto/kluster  |  StreamingIngestResults  |  Resultat av strömnings inmatning  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency  |  Åtgärds svars tid   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Slutförda åtgärder   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsFailed  |  Misslyckade åtgärder   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Åtgärder hoppades över   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsStarted  |  Startade åtgärder   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Genomförda åtgärder   |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Svars tid för åtgärd   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Åtgärds begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Anslutnings minnes användning för Integration Service Environment  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Anslutnings processor användning för Integration Service Environment  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Minnes användning för arbets flöde för Integration Service Environment  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Användning av arbets flödes processor för Integration Service Environment  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Procent körnings avbrott  |  Procent  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  Körnings fördröjning  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Körningarna har avbrutits  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  Slutförda körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Misslyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Startade körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Lyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Kör starta begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Kör svars tid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Kör begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Utlös brand fördröjning   |  Sekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency  |  Utlös fördröjning   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Slutförda utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFailed  |  Misslyckade utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  Utlöst utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Ignorerade utlösare  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  Startade utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Lyckade utlösare   |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Utlös svars tid för lyckade   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Utlös begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/workflows  |  ActionLatency  |  Åtgärds svars tid   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  Slutförda åtgärder   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsFailed  |  Misslyckade åtgärder   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  Åtgärder hoppades över   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsStarted  |  Startade åtgärder   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  Genomförda åtgärder   |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Svars tid för åtgärd   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  Åtgärds begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  Fakturerbara åtgärds körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  Fakturerbara Utlösar-körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Fakturerings användning för intern åtgärds körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Fakturerings användning för intern åtgärds körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Fakturerings användning för standard kopplings körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Fakturerings användning för standard kopplings körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Fakturerings användning för lagrings förbruknings körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Fakturerings användning för lagrings förbruknings körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Procent körnings avbrott  |  Procent  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/workflows  |  RunLatency  |  Körnings fördröjning  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Körningarna har avbrutits  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Slutförda körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsFailed  |  Misslyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsStarted  |  Startade körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Lyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunStartThrottledEvents  |  Kör starta begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Kör svars tid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Kör begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Totalt antal fakturerbara körningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Utlös brand fördröjning   |  Sekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Logic/workflows  |  TriggerLatency  |  Utlös fördröjning   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  Slutförda utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersFailed  |  Misslyckade utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersFired  |  Utlöst utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  Ignorerade utlösare  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersStarted  |  Startade utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  Lyckade utlösare   |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Utlös svars tid för lyckade   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Utlös begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Aktiva kärnor  |  Aktiva kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Aktiva noder  |  Aktiva noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Slutförda körningar  |  Slutförda körningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Misslyckade körningar  |  Misslyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Inaktiva kärnor  |  Inaktiva kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Inaktiva noder  |  Inaktiva noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Lämnar kärnor  |  Lämnar kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Lämnar noder  |  Lämnar noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Modelldistribution misslyckades  |  Modelldistribution misslyckades  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Modelldistribution Startad  |  Modelldistribution Startad  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Modelldistribution lyckades  |  Modelldistribution lyckades  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Modell registreringen misslyckades  |  Modell registreringen misslyckades  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Modell registreringen har slutförts  |  Modell registreringen har slutförts  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Blockerade kärnor  |  Blockerade kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Misslyckade noder  |  Misslyckade noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Kvot användning i procent  |  Kvot användning i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Startade körningar  |  Startade körningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Totalt antal kärnor  |  Totalt antal kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Totalt antal noder  |  Totalt antal noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Oanvändbara kärnor  |  Oanvändbara kärnor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.MachineLearningServices/workspaces  |  Oanvändbara noder  |  Oanvändbara noder  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Maps/konton  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft. Maps/konton  |  Användning  |  Användning  |  Antal  |  Antal | 
| **Ja**  | Nej |  Microsoft. Media/Media Services  |  AssetCount  |  Antal till gångar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Media/Media Services  |  AssetQuota  |  Till gångs kvot  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Media/Media Services  |  AssetQuotaUsedPercentage  |  Använd procent andel till till gångs kvot  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. Media/Media Services  |  ContentKeyPolicyCount  |  Antal nyckel principer för innehåll  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Media/Media Services  |  ContentKeyPolicyQuota  |  Kvot för innehålls nyckel princip  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Media/Media Services  |  ContentKeyPolicyQuotaUsedPercentage  |  Procent andel av nyckel princip för innehåll  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. Media/Media Services  |  StreamingPolicyCount  |  Antal strömmande principer  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Media/Media Services  |  StreamingPolicyQuota  |  Kvot för strömmande princip  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Media/Media Services  |  StreamingPolicyQuotaUsedPercentage  |  Använd procent andel av princip för strömning  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. Media/Media Services/strömnings slut punkter  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Media/Media Services/strömnings slut punkter  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Media/Media Services/strömnings slut punkter  |  SuccessE2ELatency  |  Slutför svars tid för slut punkt till slut punkt  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Antal pauser för GC  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Total tid för GC-paus  |  Millisekunder  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Maximal tillgänglig gammal generations data storlek  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Gammal generations data storlek  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Befordra till gammal generations data storlek  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Procent andel CPU-användning  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Tilldelat tjänst minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Högsta tjänst minne  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Använt tjänst minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Procent andel system CPU-användning  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatErrorCount  |  Tomcat globalt fel  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Totalt antal mottagna byte i Tomcat  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Max tid för Tomcat-begäran  |  Millisekunder  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Totalt antal Tomcat-begäranden  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Totalt antal gånger för Tomcat-begäran  |  Millisekunder  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Genomsnittlig tid för Tomcat-begäran  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatSentBytes  |  Totalt antal skickade byte i Tomcat  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Tomcat-session Alive-antal  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Antal aktiva Tomcat-sessioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Maximal Alive-tid för Tomcat-session  |  Millisekunder  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Antal skapade Tomcat-sessioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Antal utgångna Tomcat-sessioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Antal nekade Tomcat-sessioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Höj data storlek för unga generationer  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Allokerad volym pool används  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Total logisk storlek i volymprocent  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. NetApp/netAppAccounts/capacityPools/Volumes  |  AverageReadLatency  |  Genomsnittlig Läs fördröjning  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. NetApp/netAppAccounts/capacityPools/Volumes  |  AverageWriteLatency  |  Genomsnittlig Skriv fördröjning  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. NetApp/netAppAccounts/capacityPools/Volumes  |  ReadIops  |  Läs IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. NetApp/netAppAccounts/capacityPools/Volumes  |  VolumeLogicalSize  |  Logisk volym storlek  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. NetApp/netAppAccounts/capacityPools/Volumes  |  VolumeSnapshotSize  |  Storlek på volym ögonblicks bild  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. NetApp/netAppAccounts/capacityPools/Volumes  |  WriteIops  |  Skriv IOPS  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Application Gateway total tid  |  Millisekunder  |  Medel | 
| Nej  | Nej |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Begär Anden per minut per felfri värd  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Server dels anslutnings tid  |  Millisekunder  |  Medel | 
| Nej  | Nej |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Svars tid för första byte för Server del  |  Millisekunder  |  Medel | 
| Nej  | Nej |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Svars tid för senaste byte för Server delen  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Svars status för Server del  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  BlockedCount  |  Webb program brand vägg blockerade begär Anden regel distribution  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Antal blockerade förfrågningar för webb program brand vägg  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Mottagna byte  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  Bytes sent  |  Skickade byte  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft.Network/applicationGateways  |  CapacityUnits  |  Aktuella kapacitets enheter  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Network/applicationGateways  |  ClientRtt  |  Klient-/klient  |  Millisekunder  |  Medel | 
| Nej  | Nej |  Microsoft.Network/applicationGateways  |  ComputeUnits  |  Aktuella beräknings enheter  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Aktuella anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Misslyckade begäranden  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Antal felfria värdar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Brand vägg för total regel distribution i webb program  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  Svars status  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Network/applicationGateways  |  Dataflöde  |  Dataflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  Klientens TLS-protokoll  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Totalt antal förfrågningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Antal felaktiga värdar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  Antal träffar för program regler  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/azurefirewalls  |  DataProcessed  |  Bearbetade data  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  Hälso tillstånd för brand vägg  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Antal träffar för nätverks regler  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization  |  SNAT-port användning  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/Connections  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/Connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/dnszones  |  QueryVolume  |  Fråga volym  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Kapacitets användning för post uppsättning  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Network/dnszones  |  RecordSetCount  |  Antal post uppsättningar  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  ARP-tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/expressRouteCircuits  |  BgpAvailability  |  BGP-tillgänglighet  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/expressRouteCircuits/peering  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/expressRouteCircuits/peering  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| Nej  | Nej |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/expressRoutePorts  |  AdminState  |  AdminState  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Network/frontdoors  |  BackendHealthPercentage  |  Server delens hälso procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Network/frontdoors  |  BackendRequestCount  |  Antal Server dels begär Anden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. Network/frontdoors  |  BackendRequestLatency  |  Svars tid för Server del  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Network/frontdoors  |  BillableResponseSize  |  Fakturerbart svars storlek  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. Network/frontdoors  |  RequestCount  |  Antal begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. Network/frontdoors  |  RequestSize  |  Begär ande storlek  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. Network/frontdoors  |  ResponseSize  |  Svars storlek  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft. Network/frontdoors  |  TotalLatency  |  Total svars tid  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Antal begär Anden om webb programs brand vägg  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Allokerade SNAT-portar (förhands granskning)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/loadBalancers  |  ByteCount  |  Antal byte  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Status för hälso avsökning  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/loadBalancers  |  PacketCount  |  Antal paket  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Antal SNAT-anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/loadBalancers  |  SYNCount  |  Antal SYN  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Använda SNAT-portar (förhands granskning)  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Tillgänglighet för data Sök väg  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Mottagna byte  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Skickade byte  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Mottagna paket  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Skickade paket  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Genomsnittlig tid för fördröjning (MS)  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Misslyckade kontroller i procent (för hands version)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  % Avsökningar misslyckades  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Tur och retur tid (MS) (för hands version)  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  ByteCount  |  Antal byte  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  BytesDroppedDDoS  |  Ignorerade inkommande byte DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  BytesForwardedDDoS  |  Inkommande byte, vidarebefordrade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  BytesInDDoS  |  DDoS för inkommande byte  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Inkommande SYN paket för att utlösa DDoS-minskning  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Inkommande TCP-paket för att utlösa DDoS-minskning  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Ingående UDP-paket för att utlösa DDoS-minskning  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  IfUnderDDoSAttack  |  Under DDoS-attack eller inte  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  PacketCount  |  Antal paket  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  PacketsDroppedDDoS  |  Inkommande paket som släppts DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  PacketsForwardedDDoS  |  Vidarebefordrade inkommande paket DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  PacketsInDDoS  |  DDoS för inkommande paket  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  SynCount  |  Antal SYN  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Inkommande TCP-byte utelämnade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Inkommande TCP byte-vidarebefordrade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  TCPBytesInDDoS  |  DDoS för inkommande TCP-byte  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  Inkommande TCP-paket ignorerade DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Inkommande TCP-paket, vidarebefordrade DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  TCPPacketsInDDoS  |  DDoS inkommande TCP-paket  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Inkommande UDP-byte utelämnade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Inkommande UDP byte vidarebefordrade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  UDPBytesInDDoS  |  DDoS för inkommande UDP-byte  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Ignorerade inkommande UDP-paket DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Vidarebefordrade inkommande UDP-paket DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  UDPPacketsInDDoS  |  DDoS för inkommande UDP-paket  |  CountPerSecond  |  Maximal | 
| **Ja**  | Nej |  Microsoft. Network/publicIPAddresses  |  VipAvailability  |  Tillgänglighet för data Sök väg  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Slut punkts status efter slut punkt  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Returnerade frågor efter slut punkt  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Gatewayens S2S-bandbredd  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Gateway P2S-bandbredd  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Antal P2S-anslutningar  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Tunnel bandbredd  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Utgående byte i tunnel  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Ignorerade TS-matchnings paket för tunnel  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Utgående tunnel paket  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Ingångs byte för tunnel  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Tunnel om ingångs paket för TS-matchningsfel  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Inkommande tunnel paket  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Fördröjning för ping till en virtuell dator  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Misslyckade pingar till en virtuell dator  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  inkommande  |  Inkommande meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.failedrequests  |  Alla inkommande misslyckade förfrågningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  Alla inkommande begär Anden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  Schemalagda push-meddelanden har skickats  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  inkommande. schemalagd. Avbryt  |  Schemalagda push-meddelanden har avbrutits  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  Installations hanterings åtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Ta bort installations åtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Hämta installations åtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  Installation av korrigerings åtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  Skapa eller uppdatera installations åtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub. push-meddelanden  |  Alla utgående meddelanden  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Dåliga eller utgångna kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Nytto Last fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Fel i externt meddelande system  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Lyckade aviseringar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  APN dåligt kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  APN utgånget i kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  APN-auktoriseringsfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  APN ogiltigt meddelande storleks fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  APN-fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  APN-lyckade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  utgående. GCM. authenticationerror  |  GCM-autentiseringsfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  GCM felaktigt kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  GCM utgången kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  GCM ogiltigt meddelande format  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  utgående. GCM. invalidnotificationsize  |  GCM ogiltig meddelande storleks fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  GCM-fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  utgående. GCM. lyckades  |  GCM slutförda meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  utgående. GCM. begränsad  |  GCM-begränsade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  GCM fel kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  MPNS-autentiseringsfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  MPNS felaktigt kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  MPNS-kanalen är frånkopplad  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  MPNS borttagna meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  MPNS ogiltiga autentiseringsuppgifter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  MPNS ogiltigt meddelande format  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  MPNS-fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  MPNS slutförda meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  MPNS-begränsade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  utgående. WNS. authenticationerror  |  WNS-autentiseringsfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  WNS felaktigt kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  WNS-kanalen är frånkopplad  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  WNS-kanalen är begränsad  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  WNS borttagna meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  WNS utgången kanal fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  WNS ogiltigt meddelande format  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  utgående. WNS. invalidnotificationsize  |  WNS ogiltig meddelande storleks fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  WNS-auktoriseringsfel (ogiltig token)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  WNS-fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  utgående. WNS. lyckades  |  WNS slutförda meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  utgående. WNS. begränsad  |  WNS-begränsade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  WNS-auktoriseringsfel (kan inte kontaktas)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  WNS-auktoriseringsfel (fel token)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registrering. alla  |  Registrerings åtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Skapa åtgärder för registrering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  Åtgärder för att ta bort registrering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Läs åtgärder för registrering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Registrera uppdaterings åtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  schemalagt. väntar  |  Väntande schemalagda meddelanden  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% tillgängligt minne  |  Tillgängligt minne i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% tillgängligt växlings utrymme  |  Tillgängligt växlings utrymme i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% allokerade byte som används  |  % Allokerade byte som används  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% DPC-tid  |  DPC-tid i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_ kostnads fri noder i procent  |  Kostnads fri noder i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_ ledigt utrymme i procent  |  % ledigt utrymme  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_ ledigt utrymme i procent  |  % ledigt utrymme  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_ ledig tid i procent  |  Ledig tid i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Avbrotts tid i procent för Average_%  |  % Avbrotts tid  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% i/o-vänte tid  |  % I/o-vänte tid  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% bra tid  |  % Trevligt tid  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_ privilegie rad tid i procent  |  Privilegie rad tid i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% processor tid  |  % processortid  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% processor tid  |  % processortid  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% Använd noder i procent  |  % Använda noder i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% använt minne  |  Använt minne i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_ använt utrymme i procent  |  Använt utrymme i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_ använt växlings utrymme i procent  |  Använt växlings utrymme i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_% användar tid  |  Användar tid i procent  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Available megabyte  |  Tillgängliga megabyte  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Available MB minne  |  Tillgängligt minne i megabyte  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Växling vid Average_Available megabyte  |  Tillgängliga megabyte växlings utrymme  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Avg. Disk s/läsning  |  Medel s/disk läsning  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Avg. Disk s/läsning  |  Medel s/disk läsning  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Avg. disk s/överföring  |  Medel s/disk överföring  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Avg. Disk s/skrivning  |  Medel s/disk skrivning  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Avg. Disk s/skrivning  |  Medel s/disk skrivning  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Bytes mottagna/SEK  |  Mottagna byte/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Skickade Average_Bytes per sekund  |  Skickade byte/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Bytes totalt/SEK  |  Totalt antal byte/s  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Current diskkölängd  |  Aktuell diskkölängd  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Disk lästa byte/s  |  Disk – lästa byte/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Disk läsningar/s  |  Diskläsningar/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Disk läsningar/s  |  Diskläsningar/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Disk överföringar/SEK  |  Disköverföringar/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Disk överföringar/SEK  |  Disköverföringar/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Disk skrivna byte/s  |  Disk – skrivna byte/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Disk skrivningar/SEK  |  Diskskrivningar/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Disk skrivningar/SEK  |  Diskskrivningar/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Free megabyte  |  Ledigt utrymme i MB  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Free megabyte  |  Ledigt utrymme i MB  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Free fysiskt minne  |  Ledigt fysiskt minne  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Free utrymme i växlingsfiler  |  Ledigt utrymme i växlingsfiler  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Free virtuellt minne  |  Ledigt virtuellt minne  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Logical Disk-byte/s  |  Logisk Disk byte/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Page läsningar/s  |  Sidläsningar/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Page skrivningar/SEK  |  Sidskrivningar/sek  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Pages per sekund  |  Sidor/s  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Pct privilegie rad tid  |  PCT privilegie rad tid  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Pct användar tid  |  PCT-användar tid  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Physical Disk-byte/s  |  Fysisk disk-byte/SEK  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Processes  |  Processer  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Processor Kölängd  |  Kölängd för processor  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Size lagras i växlingsfiler  |  Storlek lagrad i växlingsfiler  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Total byte  |  Totalt antal byte  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Total mottagna byte  |  Totalt antal byte mottaget  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Skickade Average_Total byte  |  Totalt antal överförda byte  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Total kollisioner  |  Totalt antal kollisioner  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Total mottagna paket  |  Totalt antal mottagna paket  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Skickade Average_Total paket  |  Totalt antal överförda paket  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Total RX-fel  |  Totalt antal mottagna mottagnings fel  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Total TX-fel  |  Totalt antal TX-fel  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Uptime  |  Drifttid  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Used megabyte växlings utrymme  |  Använt megabyte växlings utrymme  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Used minne i KB  |  Använt minne i KB  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Used minne i MB  |  Använt minne i MB  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Users  |  Användare  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Average_Virtual delat minne  |  Virtuellt delat minne  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Händelse  |  Händelse  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft. OperationalInsights/arbets ytor  |  Pulsslag  |  Pulsslag  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. OperationalInsights/arbets ytor  |  Uppdatera  |  Uppdatera  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. PowerBIDedicated/kapacitet  |  memory_metric  |  Minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft. PowerBIDedicated/kapacitet  |  memory_thrashing_metric  |  Nedskräpning för minne (data uppsättningar)  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft. PowerBIDedicated/kapacitet  |  qpu_high_utilization_metric  |  QPU hög användning  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. PowerBIDedicated/kapacitet  |  QueryDuration  |  Frågans varaktighet (data uppsättningar)  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. PowerBIDedicated/kapacitet  |  QueryPoolJobQueueLength  |  Kölängd för jobbkö (data uppsättningar)  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  ListenerConnections – ClientError  |  ListenerConnections – ClientError  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  ListenerConnections – ServerError  |  ListenerConnections – ServerError  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  ListenerConnections – lyckad  |  ListenerConnections – lyckad  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  SenderConnections – ClientError  |  SenderConnections – ClientError  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  SenderConnections – ServerError  |  SenderConnections – ServerError  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Search/searchServices  |  SearchLatency  |  Sök svars tid  |  Sekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Sök frågor per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Begränsade Sök frågor i procent  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Antal aktiva meddelanden i en kö/ett ämne.  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  ConnectionsClosed  |  Stängda anslutningar.  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Öppna anslutningar.  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  PROCESSOR (inaktuell)  |  Procent  |  Maximal | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  DeadletteredMessages  |  Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Inkommande meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Inkommande begär Anden  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  Meddelanden  |  Antal meddelanden i en kö/ett ämne.  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  Processor  |  Procent  |  Maximal | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUsage  |  Minnesanvändning  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Utgående meddelanden  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  ScheduledMessages  |  Antal schemalagda meddelanden i en kö/ett ämne.  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Server fel.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  Storlek  |  Storlek  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Lyckade förfrågningar  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Begränsade begär Anden.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Användar fel.  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Minnes användning (inaktuell)  |  Procent  |  Maximal | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  ActualCpu  |  ActualCpu  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  ActualMemory  |  ActualMemory  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  AllocatedCpu  |  AllocatedCpu  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  AllocatedMemory  |  AllocatedMemory  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  ApplicationStatus  |  ApplicationStatus  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  Container status  |  Container status  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  CpuUtilization  |  CpuUtilization  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  MemoryUtilization  |  MemoryUtilization  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  RestartCount  |  RestartCount  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.ServiceFabricMesh/applications  |  ServiceStatus  |  ServiceStatus  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Antal anslutningar  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  Inkommande trafik  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  Antal meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  Utgående trafik  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Systemfel  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  Användar fel  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Genomsnittlig CPU-procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Lästa IO-byte  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Skrivna IO-byte  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/managedInstances  |  io_requests  |  Antal IO-begäranden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Reserverat lagrings utrymme  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Använt lagrings utrymme  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Antal virtuella kärnor  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers  |  database_storage_used  |  Använt data utrymme  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers  |  dtu_used  |  Använt DTU  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers  |  storage_used  |  Använt data utrymme  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Allokerat data utrymme  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  App-CPU fakturerad  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  CPU-procent för app  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Minnes procent för appar  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Blockerad av brand väggen  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Procent andel cacheträffar  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Procent andel som används  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Misslyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Lyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  cpu_limit  |  PROCESSOR gräns  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cpu_used  |  Använd CPU  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  hamn  |  Låsningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  dtu_limit  |  DTU-gräns  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dtu_used  |  Använt DTU  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU procent  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  dwu_limit  |  DWU-gräns  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU som används  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Lokal tempdb-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Logg IO-procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Minnes procent  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Data IO-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Sessioner i procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  lagrings  |  Använt data utrymme  |  Byte  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  storage_percent  |  Använt data utrymme i procent  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Data fil storlek i tempdb i KB  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  TempDB-logg fils storlek kilobyte  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Procent använt tempdb-logg  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Arbetare i procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Minnes intern OLTP-lagring i procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Allokerat data utrymme  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Allokerat data utrymme i procent  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  PROCESSOR gräns  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  Använd CPU  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Allokerat data utrymme  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  PROCESSOR gräns  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  Använd CPU  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU använt  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Logg IO-procent  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Data IO-procent  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Sessioner i procent  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Använt data utrymme  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Arbetare i procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU-gräns  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU använt  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Logg IO-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Data IO-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Sessioner i procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Max storlek för data  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Använt data utrymme i procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Använt data utrymme  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Data fil storlek i tempdb i KB  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  TempDB-logg fils storlek kilobyte  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Procent använt tempdb-logg  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Arbetare i procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Minnes intern OLTP-lagring i procent  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Använd kapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Blobkapacitet  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Antalet blobar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Antal blobcontainrar  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Index kapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/blobServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Fil kapacitet  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Antal filer  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Antal fil resurser  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  Fil resursens kvot storlek  |  Byte  |  Medel | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Antal ögonblicks bilder av fil resurs  |  Antal  |  Medel | 
| Nej  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Storlek på fil resursens ögonblicks bild  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/fileServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/queueServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/queueServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/queueServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Kökapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Antal köer  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Antal kömeddelanden  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/queueServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/tableServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/tableServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/tableServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Tabellkapacitet  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Antal tabeller  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Antal tabellentiteter  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft.Storage/storageAccounts/tableServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  ClientIOPS  |  Totalt antal klient-IOPS  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  ClientLatency  |  Genomsnittlig klient latens  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  ClientLockIOPS  |  Klient lås IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  ClientMetadataReadIOPS  |  Lästa IOPS för klientens metadata  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  ClientMetadataWriteIOPS  |  Klientens metadata Skriv IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  ClientReadIOPS  |  Klient läsnings-IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  ClientReadThroughput  |  Genomsnittligt cache-läst data flöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  ClientWriteIOPS  |  Klient skrivnings-IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  ClientWriteThroughput  |  Genomsnittligt cacheminne Skriv data flöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetAsyncWriteThroughput  |  StorageTarget asynkron skrivning av data flöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetFillThroughput  |  StorageTarget Fill-genomflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetHealth  |  Lagrings mål hälsa  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetIOPS  |  Totalt antal StorageTarget IOPS  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetLatency  |  StorageTarget-svars tid  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetMetadataReadIOPS  |  StorageTarget metadata Läs IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetMetadataWriteIOPS  |  StorageTarget metadata Skriv IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetReadAheadThroughput  |  StorageTarget Read i förväg genom strömning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetReadIOPS  |  StorageTarget läsa IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetSyncWriteThroughput  |  StorageTarget synkront Skriv data flöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetTotalReadThroughput  |  StorageTarget total Read-genomflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetTotalWriteThroughput  |  StorageTarget totalt Skriv data flöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  StorageTargetWriteIOPS  |  StorageTarget Skriv IOPS  |  Antal  |  Medel | 
| **Ja**  | Nej |  Microsoft. StorageCache/cacheminnen  |  Drifttid  |  Drifttid  |  Antal  |  Medel | 
| **Ja**  | Nej |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Resultat av Sync-session  |  Antal  |  Medel | 
| **Ja**  | Nej |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Byte som har synkroniserats  |  Byte  |  Totalt | 
| **Ja**  | Nej |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Återställnings storlek för moln skikt per program  |  Byte  |  Totalt | 
| **Ja**  | Nej |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Återställnings storlek för moln nivå  |  Byte  |  Totalt | 
| **Ja**  | Nej |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Återkalla moln nivå  |  Byte  |  Totalt | 
| **Ja**  | Nej |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Återkalla data flöde för moln nivå  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Status för server online  |  Antal  |  Maximal | 
| **Ja**  | Nej |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Synkroniserade filer  |  Antal  |  Totalt | 
| **Ja**  | Nej |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Filer som inte synkroniseras  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat  |  Status för server online  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Återkalla moln nivå  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Byte som har synkroniserats  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Synkroniserade filer  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Filer som inte synkroniseras  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Byte som har synkroniserats  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Synkroniserade filer  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Filer som inte synkroniseras  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Misslyckade funktionsförfrågningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Funktionshändelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Funktionsförfrågningar  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Datakonverteringsfel  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Fel vid deserialisering av indata  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Händelser som inte är i ordning  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Tidiga indatahändelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  Fel  |  Körningsfel  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Indatahändelsebyte  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Indatahändelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Eftersläpande inmatningshändelser  |  Antal  |  Maximal | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Indatakällorna har tagits emot  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  Sena indatahändelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Utdatahändelser  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Vattenstämpelfördröjning  |  Sekunder  |  Maximal | 
| **Ja**  | Nej |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  SU % utnyttjande  |  Procent  |  Maximal | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  Lästa byte på disk  |  Lästa byte på disk  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Läs åtgärder/SEK  |  Disk Läs åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk-skrivna byte  |  Disk-skrivna byte  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk skrivnings åtgärder/SEK  |  Disk skrivnings åtgärder/SEK  |  CountPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Disk-lästa byte/s  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Läs fördröjning för disk  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Disk Läs åtgärder  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Disk-skrivna byte/s  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Skriv fördröjning för disk  |  Millisekunder  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Disk skrivnings åtgärder  |  Antal  |  Totalt | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Aktivt minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Beviljat minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Använt minne  |  Byte  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  Nätverk – inkommande  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  Nätverk – utgående  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Nätverk i byte/s  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Nätverks-out-byte/SEK  |  BytesPerSecond  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Nej |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Procent andel CPU klar  |  Millisekunder  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Aktiva begär Anden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Medelsvarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Bytes sent  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Diskkölängd  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http-2xx  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http-3xx  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  Http 401  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http-4xx  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Http-serverfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Längd på http-kö  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Stora App Service planera arbetare  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Medel App Service planera arbetare  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Minnes procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Små App Service planera arbetare  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Totalt antal klient delar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Minnes procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Tillgängliga arbetare  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Totalt antal arbetare  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Använda arbetare  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  Bytes sent  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Diskkölängd  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Längd på http-kö  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  Minnes procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  Väntan på TCP-stängning  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpClosing  |  TCP-stängning  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  TCP upprättad  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP räntetrans wait 1  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP räntetrans wait 2  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP-senaste ack  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  TCP-syn mottagen  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  TCP-syn har skickats  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  Väntan på TCP-tid  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  AppConnections  |  Anslutningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  Genomsnittlig arbets mängd för minne  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  AverageResponseTime  |  Medelsvarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  BytesReceived  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Bytes sent  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  CpuTime  |  CPU-tid  |  Sekunder  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  CurrentAssemblies  |  Aktuella sammansättningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  Funktions körnings antal  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  Funktions körnings enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gen0Collections  |  Skräp insamling för gen 0  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gen1Collections  |  Skräp insamling för gen 1  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gen2Collections  |  Gen 2 skräp insamling  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Konsolindataobjekt  |  Antal referenser  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  HealthCheckStatus  |  Hälso kontroll status  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http2xx  |  Http-2xx  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http3xx  |  Http-3xx  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http401  |  Http 401  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http403  |  Http 403  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http4xx  |  Http-4xx  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http5xx  |  Http-serverfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  HttpResponseTime  |  Svars tid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  I/o andra byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  Andra i/o-åtgärder per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  IO-lästa byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  IO-Läs åtgärder per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  Skrivna byte i i/o per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  I/o-Skriv åtgärder per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  Minnes arbets mängd  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  PrivateBytes  |  Privata byte  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  Begär anden i program kön  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Konversation  |  Räkning av trådar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  TotalAppDomains  |  Totalt antal app-domäner  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  Totalt antal app-domäner som har inaktiverats  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  AppConnections  |  Anslutningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  Genomsnittlig arbets mängd för minne  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  Medelsvarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Bytes sent  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  CpuTime  |  CPU-tid  |  Sekunder  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Aktuella sammansättningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  Funktions körnings antal  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  Funktions körnings enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Skräp insamling för gen 0  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Skräp insamling för gen 1  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Gen 2 skräp insamling  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Konsolindataobjekt  |  Antal referenser  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  Hälso kontroll status  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http-2xx  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http3xx  |  Http-3xx  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http-4xx  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http5xx  |  Http-serverfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Svars tid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  I/o andra byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  Andra i/o-åtgärder per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  IO-lästa byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  IO-Läs åtgärder per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  Skrivna byte i i/o per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  I/o-Skriv åtgärder per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  Minnes arbets mängd  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Privata byte  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Begär anden i program kön  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Konversation  |  Räkning av trådar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  Totalt antal app-domäner  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  Totalt antal app-domäner som har inaktiverats  |  Antal  |  Medel | 
