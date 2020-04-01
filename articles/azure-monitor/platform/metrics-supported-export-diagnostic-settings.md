---
title: Azure Monitor-plattformsmått som kan exporteras via diagnostikinställningar
description: Lista över tillgängliga mått för varje resurstyp med Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 6be8cb1b7e74301d16a1174f5ca2b774334dac3f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422114"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor-plattformsmått som kan exporteras via diagnostikinställningar

Azure Monitor tillhandahåller [plattformsmått](data-platform-metrics.md) som standard utan konfiguration. Det ger flera sätt att interagera med plattformsmått, inklusive att kartlägga dem i portalen, komma åt dem via REST API eller fråga dem med PowerShell eller CLI. Se [mått som stöds](metrics-supported.md) för en fullständig lista över plattformsmått som för närvarande är tillgängliga med Azure Monitors konsoliderade måttpipeline. Om du vill fråga efter och komma åt dessa mått använder du [api-versionen 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Andra mått kan vara tillgängliga i portalen eller med hjälp av äldre API:er.

Du kan exportera plattformsmåtten från Azure-övervakarpipelinen till andra platser på ett av två sätt.
1. Använda [diagnostikinställningar för](diagnostic-settings.md) att skicka till Log Analytics, Event Hubs eller Azure Storage.
2. Använda [REST API-API:erna för mått](https://docs.microsoft.com/rest/api/monitor/metrics/list)

På grund av krångligheter i Azure Monitor-backend kan inte alla mått exporteras med hjälp av diagnostikinställningar. Tabellen nedan visar vilka som kan och inte kan exporteras med hjälp av diagnostikinställningar.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Ändra till beteende för NULLs och Nollvärden 
 
För plattformsmått som kan exporteras via diagnostikinställningar finns det några mått som Azure Monitor tolkar 0:0 som "Nulls". Detta har orsakat viss förvirring mellan verkliga "0s" (som avges av resurs) och tolkade "0s" (Nulls). Snart kommer en förändring att ske och plattformsmått som exporteras via diagnostikinställningar exporterar inte längre 0:0 om de inte verkligen har avgetts av den underliggande resursen. Ändringen var planerad till den 1 april 2020, men har försenats på grund av prioriterade skift på grund av COVID-19. 

Observera följande:

1.  Om du tar bort en resursgrupp eller en viss resurs skickas inte längre måttdata från de påverkade resurserna till diagnostikinställningsexportdestinationer. Det vill än, det kommer inte längre att visas i Event Hubs, Storage Accounts och Log Analytics Workspaces.
2.  Denna förbättring kommer att vara tillgänglig i alla offentliga och privata moln.
3.  Den här ändringen påverkar inte beteendet för någon av följande upplevelser: 
   - Plattformsresursloggar exporteras via diagnostikinställningar
   - Mått som kartlägger i Statistikutforskaren
   - Avisering om plattformsmått
 
## <a name="metrics-exportable-table"></a>Exportbar tabell för mätvärden 

Tabellen innehåller följande kolumner. 
- Kan du exportera via diagnostikinställningar? 
- Verkställs av NULL / 0 
- ResourceType 
- Mått 
- MetricDisplayName
- Enhet 
- Aggregeringstyp


> [!NOTE]
> Tabellen nedan kan ha en vågrät rullningslist längst ned. Om du tror att du saknar information kontrollerar du att rullningslisten är hela vägen till vänster.  


| Kan du exportera via diagnostikinställningar?  | Redan avger NULLs |  ResourceType  |  Mått  |  MetricDisplayName  |  Enhet  |  Aggregeringstyp | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Ja****  | Inga |  Microsoft.AnalysisServices/servrar  |  CleanerCurrentPrice  |  Minne: Renare nuvarande pris  |  Antal  |  Medel | 
| Ja****  | Inga |  Microsoft.AnalysisServices/servrar  |  CleanerMemoryNonshrinkable  |  Minne: Renare minne nonshrinkable  |  Byte  |  Medel | 
| Ja****  | Inga |  Microsoft.AnalysisServices/servrar  |  CleanerMemoryShrinkable  |  Minne: Renare minne krympbar  |  Byte  |  Medel | 
| Ja****  | Inga |  Microsoft.AnalysisServices/servrar  |  CommandPoolBusyTråds  |  Trådar: Kommandopool upptagen trådar  |  Antal  |  Medel | 
| Ja****  | Inga |  Microsoft.AnalysisServices/servrar  |  CommandPoolIdleThreads  |  Trådar: Kommandopool inaktiva trådar  |  Antal  |  Medel | 
| Ja****  | Inga |  Microsoft.AnalysisServices/servrar  |  KommandoPoolJobQueueLength  |  Längd på jobbköen för kommandopool  |  Antal  |  Medel | 
| Ja****  | Inga |  Microsoft.AnalysisServices/servrar  |  CurrentConnections  |  Anslutning: Aktuella anslutningar  |  Antal  |  Medel | 
| Ja****  | Inga |  Microsoft.AnalysisServices/servrar  |  CurrentUserSessions  |  Aktuella användarsessioner  |  Antal  |  Medel | 
| Ja****  | Inga |  Microsoft.AnalysisServices/servrar  |  LongParsingBusyTråds  |  Trådar: Lång tolkning upptagen trådar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  LongParsingIdleThreads  |  Trådar: Lång parsning inaktiva trådar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  LångParsingJobQueueLength  |  Trådar: Lång parsning av jobbkölängd  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  mashup_engine_memory_metric  |  M Motorminne  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  mashup_engine_private_bytes_metric  |  M Motor privata byte  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  mashup_engine_qpu_metric  |  M Motor QPU  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  mashup_engine_virtual_bytes_metric  |  Virtuella byte för M-motor  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  memory_metric  |  Minne  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  memory_thrashing_metric  |  Minnesförslöing  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  MemoryLimitHard  |  Minne: Minnesgräns hårt  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  MinneLimitHög  |  Minne: Minnesgränsen hög  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  MemoryLimitLow  |  Minne: Minnesgränsen låg  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  MinneLimitVertiPaq  |  Minne: Minnesgräns VertiPaq  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  Minnesupptring  |  Minne: Minnesanvändning  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  private_bytes_metric  |  Privata byte  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  ProcessingPoolBusyIOJobTrådar  |  Trådar: Bearbetning pool upptagen I / O-jobb trådar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  ProcessingPoolBusyNonIOTråds  |  Trådar: Bearbetning pool upptagen icke-I /O trådar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  ProcessingPoolIdleIOJobTrådar  |  Trådar: Bearbetning av tomgångstrådar för i/o-jobb för poolen  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  ProcessingPoolIdleNonIOTrådar  |  Trådar: Bearbetning av pool inaktiv icke-I/O-trådar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  BearbetningPoolIOJobQueueLength  |  Trådar: Bearbetning av i/o-jobbkölängd för pool  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  BearbetningPoolJobQueueLength  |  Köns längd för bearbetning av pooljobb  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  qpu_metric  |  QPU (på andra sätt)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  QueryPoolBusyTråds  |  Upptagna trådar för frågepool  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  QueryPoolIdleTrådar  |  Trådar: Frågepool inaktiva trådar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  FrågaPoolJobQueueLength  |  Trådar: Kölängd för frågepooljobb  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  Kvot  |  Minne: Kvot  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  Kvotblockerad  |  Minne: Kvot blockerad  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  RaderKonverteradPerSec  |  Bearbetning: Rader konverterade per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  RadernaReadPerSec  |  Bearbetning: Rader som läses per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  RaderSkrivnaPerSec  |  Bearbetning: Rader skrivna per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  ShortParsingBusyTråds  |  Trådar: Kort tolkning upptagen trådar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  ShortParsingIdleThreads  |  Trådar: Korta parsning inaktiva trådar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  KortParsingJobQueueLength  |  Trådar: Kort parsning av jobbkölängd  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  Lyckade anslutningarPerSec  |  Lyckade anslutningar per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  TotalAnslutningsylar  |  Totalt antal anslutningsfel  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  Totalt Antal anslutningsanknlöningar  |  Totalt antal anslutningsbegäranden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  VertiPaqNonpaged  |  Minne: VertiPaq Ej sidiga  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  VertiPaqSidad  |  Minne: VertiPaq Sökte  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AnalysisServices/servrar  |  virtual_bytes_metric  |  Virtuella byte  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Varaktighet för begäranden för bakåtsträvningar  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ApiManagement/service  |  Kapacitet  |  Kapacitet  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Varaktighet  |  Den totala varaktigheten för gateway-begäranden  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Händelser som tagits bort i Händelsehub  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Avvisade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Lyckade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Begränsade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Tidsinnade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Storleken på EventHub-händelser  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Totalt antal EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Misslyckade EventHub-händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Misslyckade begärande  |  Misslyckade gateway-begäranden (inaktuell)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Övriga Upplysningar  |  Andra gateway-begäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Lyckade Begärande  |  Lyckade gateway-begäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Totalt antal gatewaybegäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Obehöriga begärande  |  Obehöriga gateway-begäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  AppCpuUsagePercentage  |  Procentandel av appens CPU-användning  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  AppMemoryCommitted  |  Appminne tilldelas  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  AppMemoryMax (1993)  |  Max för appminne  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  AppMemoryUsed  |  Appminne används  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  GCPauseTotalCount  |  Antal GC-pauser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  GCPauseTotalTime  |  GC Paus total tid  |  Millisekunder  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  MaxOldGenMemoryPoolBytes  |  Max tillgänglig datastorlek för gammal generation  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  OldGenMemoryPoolBytes  |  Datastorlek för gammal generering  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  OldGenPromotedBytes  |  Befordra till datastorlek för gammal generation  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  SystemCpuUsagePercentage  |  Procentandel av användningen av system-PROCESSOR  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatErrorCount  |  Tomcat globalt fel  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatReceivedBytes  |  Tomcat totalt mottagna byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatRequestMaxTime  |  Tomcat begäran max tid  |  Millisekunder  |  Maximal | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatRequestTotalCount  |  Totalt antal antal för Tomcat-begäran  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatRequestTotalTime  |  Totalt antal gånger för Tomcat-begäran  |  Millisekunder  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatResponseAvgTime  |  Genomsnittlig tid för Tomcat-begäran  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatSentBytes  |  Tomcat Totalt antal skickade byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatSessionActiveCurrentCount  |  Tomcat session levande räkna  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatSessionActiveMaxCount  |  Tomcat-session Max aktivt antal  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatSessionAliveMaxTime  |  Tomcat Session Max Alive Tid  |  Millisekunder  |  Maximal | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatSessionCreatedCount  |  Tomcat-session skapad räkning  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatSessionExpiredCount  |  Tomcat-session har upphört att gälla  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  TomcatSessionRejectedCount  |  Tomcat-session avvisad räkning  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.AppPlattform/Fjäder  |  YoungGenPromotedBytes  |  Främja till young generation datastorlek  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Automation/automationKonton  |  TotaltJob  |  Totalt antal jobb  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Automation/automationKonton  |  TotalUpdateDeploymentMachineRuns  |  Antal datorspecifika körningar av uppdateringsdistributionen  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Automation/automationKonton  |  TotalUpdateDeploymentRuns  |  Antal körningar av uppdateringsdistributionen  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  CoreCount (olikartade)  |  Dedikerat kärnantal  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  Skapanädland  |  Skapa antal noder  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  IdleNodeCount  |  Antal inaktiv nod  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  JobDeleteCompleteEvent  |  Slutföra händelser för jobbborttagning  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  JobDeleteStartEvent  |  Starthändelser för jobbborttagning  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  JobDisableCompleteEvent  |  Jobb inaktiverar slutförd händelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  JobbDisableStartevent  |  Jobb inaktivera starthändelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  JobbstartEvent  |  Jobbstarthändelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  JobbtermineraKompileEvent  |  Slutföra händelser för jobb avsluta  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  JobbtermineraStartEvent  |  Starthändelser för jobb avsluta  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  LämnarPoolNodeCount  |  Lämna antalet poolnoder  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  LågpriorityCoreCount  |  Lågt prioriterat kärnantal  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  OfflineNodeCount  |  Antal offlinenoder  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  PoolCreateEvent  |  Skapa händelser för pool  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  PoolDeleteCompleteEvent  |  Pool Ta bort slutförda händelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  PoolDeleteStartEvent  |  Starta starthändelser för poolborttagning  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  PoolResizeCompleteEvent  |  Pool Ändra storlek på slutförda händelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  PoolResizeStartEvent  |  Pool Ändra storlek på starthändelser  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  FöregripenNodeCount  |  Antal föregripna nod  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  OmstartNodeCount  |  Omstart av antal noder  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  ReimagingNodeCount  |  Återskapa nodantal  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  KörNodeCount  |  Köra nodantal  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  StartNodeCount  |  Antal startnoder  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  StartTaskFailedNodeCount  |  Antal misslyckade noder för start av aktivitet  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  TaskCompleteEvent  |  Aktiviteter som slutförts  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  TaskFailEvent  |  Händelser för att misslyckas med aktivitet  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Batch/batchKonton  |  TaskStartEvent  |  Aktivitetsstarthändelser  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  TotaltLågpriorityNodeCount  |  Antal nodrad med låg prioritet  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  Totaltnr  |  Antal dedikerade nodar  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  OanvändarbartNodeCount  |  Oersättlig nodantal  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Batch/batchKonton  |  Väntar påStartTaskNodeCount  |  Väntar på antal start-aktivitetsnoder  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Aktiva kärnor  |  Aktiva kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Aktiva noder  |  Aktiva noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Inaktiva kärnor  |  Inaktiva kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Inaktiva noder  |  Inaktiva noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Jobbet har slutförts  |  Jobbet har slutförts  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Jobbet har skickats in  |  Jobbet har skickats in  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Lämna kärnor  |  Lämna kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Lämna noder  |  Lämna noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Föregripna kärnor  |  Föregripna kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Föregripna noder  |  Föregripna noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Procent av kvotutnyttjande  |  Procent av kvotutnyttjande  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Totalt kärnor  |  Totalt kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Totalt antal noder  |  Totalt antal noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Oersättliga kärnor  |  Oersättliga kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.BatchAI/arbetsytor  |  Oersättliga noder  |  Oersättliga noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  AnslutningAccepterad  |  Godkända anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  AnslutningActive  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  Anslutningshanterat  |  Hanterade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  CpuUsagePercentageDouble  |  Procentandel av CPU-användningen  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  IOReadBytes  |  IO läs byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  IOWriteBytes  |  IO skriv byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  MemoryLimit (minne)  |  Minnesgräns  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  Minnesupptring  |  Minnesanvändning  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  MinneAnvändarePercentageInDouble  |  Procentandel av minnesanvändningen  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  Väntande transaktioner  |  Väntande transaktioner  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  Bearbetade Blockeringar  |  Bearbetade block  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  Bearbetade transaktioner  |  Bearbetade transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  Kötransaktioner  |  Transaktioner i kö  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  RequestHandled  |  Hanterade förfrågningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Blockchain/blockchainMedlem  |  LagringAnvändare  |  Användning av lagring  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits  |  Cache träffar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits0  |  Cache träffar (fragment 0)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits1  |  Cache träffar (fragment 1)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits2  |  Cache träffar (fragment 2)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits3  |  Cache träffar (fragment 3)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits4  |  Cache träffar (fragment 4)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits5  |  Cache träffar (fragment 5)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits6  |  Cache träffar (fragment 6)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits7  |  Cache träffar (fragment 7)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits8  |  Cache träffar (fragment 8)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachehits9  |  Cache träffar (fragment 9)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLatens  |  Mikrosekonsekter för cachefördröjning (förhandsgranskning)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemissar  |  Cache missar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses0  |  Cache missar (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses1  |  Cache missar (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses2  |  Cache missar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses3  |  Cache missar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses4  |  Cache missar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses5  |  Cache missar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses6  |  Cache missar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses7  |  Cache missar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses8  |  Cache missar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cachemisses9  |  Cache missar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLäs  |  Cache läsa  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheRead0  |  Cache läsa (Shard 0)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLäs1  |  Cache läsa (Shard 1)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLäs2  |  Cache läsa (Shard 2)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLäs3  |  Cache läsning (Shard 3)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLäs4  |  Cache läsa (Shard 4)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLäs5  |  Cache läsa (Shard 5)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheRead6  |  Cache läsa (Shard 6)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLäs7  |  Cache läsa (Shard 7)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLäs8  |  Cache läsa (Shard 8)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheLäs9  |  Cache läsa (Shard 9)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheSkriva  |  Cache skriva  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite0  |  Cache skriva (fragment 0)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite1  |  Cache skriva (fragment 1)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite2  |  Cache skriva (fragment 2)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite3  |  Cache skriva (fragment 3)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite4  |  Cache skriva (Shard 4)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite5  |  Cache skriva (Shard 5)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite6  |  Cache skriva (Shard 6)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite7  |  Cache skriva (Shard 7)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite8  |  Cache skriva (fragment 8)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  cacheWrite9  |  Cache skriva (fragment 9)  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter  |  Anslutna klienter  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter0  |  Anslutna klienter (fragment 0)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter1  |  Anslutna klienter (fragment 1)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter2  |  Anslutna klienter (fragment 2)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter3  |  Anslutna klienter (Shard 3)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter4  |  Anslutna klienter (Shard 4)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter5  |  Anslutna klienter (Shard 5)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter6  |  Anslutna klienter (Shard 6)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter7  |  Anslutna klienter (Shard 7)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter8  |  Anslutna klienter (Shard 8)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  anslutnaklienter9  |  Anslutna klienter (fragment 9)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  fel  |  Fel  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkas nycklar  |  Vräkta nycklar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräka nycklar0  |  Vräkta nycklar (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkas nycklar1  |  Vräkta nycklar (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkas nycklar2  |  Vräkta nycklar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkas nycklar3  |  Vräkta nycklar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkasnycklar4  |  Vräkta nycklar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkasnycklar5  |  Vräkta nycklar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkasnycklar6  |  Vräkta nycklar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkasnycklar7  |  Vräkta nycklar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkasnycklar8  |  Vräkta nycklar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  vräkas nycklar9  |  Vräkta nycklar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångna nycklar  |  Utgångna nycklar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångna nycklar0  |  Utgångna nycklar (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångnanycklar1  |  Utgångna nycklar (fragment 1)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångna nycklar2  |  Utgångna nycklar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångnanycklar3  |  Utgångna nycklar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångna nycklar4  |  Utgångna nycklar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångna nycklar5  |  Utgångna nycklar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångna nycklar6  |  Utgångna nycklar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångna nycklar7  |  Utgångna nycklar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångna nycklar8  |  Utgångna nycklar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  utgångna nycklar9  |  Utgångna nycklar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands (fåcommands)  |  Får  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands0  |  Gets (Skärva 0)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands1  |  Gets (Skärva 1)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands2  |  Gets (Skärva 2)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands3  |  Gets (Skärva 3)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands4  |  Gets (Skärva 4)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands5  |  Gets (Skärva 5)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands6  |  Gets (Skärva 6)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands7  |  Gets (Skärva 7)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands8  |  Gets (Skärva 8)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  getcommands9  |  Gets (Skärva 9)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond  |  Operationer per sekund  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond0  |  Åtgärder per sekund (Shard 0)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond1  |  Åtgärder per sekund (Shard 1)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond2  |  Åtgärder per sekund (Shard 2)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond3  |  Åtgärder per sekund (Shard 3)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond4  |  Åtgärder per sekund (Shard 4)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond5  |  Åtgärder per sekund (Shard 5)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond6  |  Åtgärder per sekund (Shard 6)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond7  |  Åtgärder per sekund (Shard 7)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond8  |  Åtgärder per sekund (Shard 8)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  operationerPerSecond9  |  Åtgärder per sekund (Shard 9)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime  |  Processor  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime0  |  CPU (Fragment 0)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime1  |  CPU (Fragment 1)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime2  |  CPU (Fragment 2)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime3  |  CPU (Shard 3)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime4  |  CPU (Shard 4)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime5  |  CPU (Shard 5)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime6  |  CPU (Shard 6)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime7  |  CPU (Shard 7)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime8  |  CPU (Shard 8)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  procentProcessorTime9  |  CPU (Shard 9)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad (serverLoad)  |  Serverinläsning  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad0  |  Serverbelastning (Shard 0)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad1  |  Serverbelastning (Shard 1)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad2  |  Serverbelastning (Shard 2)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad3  |  Serverbelastning (Shard 3)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad4  |  Serverbelastning (Shard 4)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad5  |  Serverbelastning (Shard 5)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad6  |  Serverbelastning (Shard 6)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad7  |  Serverbelastning (Shard 7)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad8  |  Serverbelastning (Shard 8)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  serverLoad9  |  Serverbelastning (Shard 9)  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands (setcommands)  |  Uppsättningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands0  |  Uppsättningar (Fragment 0)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands1 (setcommands1)  |  Uppsättningar (fragment 1)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands2 (setcommands2)  |  Uppsättningar (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands3 (setcommands3)  |  Uppsättningar (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands4 (setcommands4)  |  Uppsättningar (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands5 (setcommands5)  |  Uppsättningar (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands6 (setcommands6)  |  Uppsättningar (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands7 (setcommands7)  |  Uppsättningar (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands8 (setcommands8)  |  Uppsättningar (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  setcommands9 (setcommands9)  |  Uppsättningar (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Totala operationer  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Totalt antal operationer (Shard 0)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Totalt antal operationer (Shard 1)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Totalt antal operationer (Shard 2)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Totalt antal operationer (Shard 3)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Totalt antal operationer (Shard 4)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Totalt antal operationer (Shard 5)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Totalt antal operationer (Shard 6)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Totalt antal operationer (Shard 7)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Totalt antal operationer (Shard 8)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Totalt antal operationer (Shard 9)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar  |  Totalt antal nycklar  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar0  |  Totalt antal nycklar (Shard 0)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar1  |  Totalt antal nycklar (Shard 1)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar2  |  Totalt antal nycklar (Shard 2)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar3  |  Totalt antal nycklar (Shard 3)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar4  |  Totalt antal nycklar (Shard 4)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar5  |  Totalt antal nycklar (Shard 5)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar6  |  Totalt antal nycklar (Shard 6)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar7  |  Totalt antal nycklar (Shard 7)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar8  |  Totalt antal nycklar (Shard 8)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  totalnycklar9  |  Totalt antal nycklar (Shard 9)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  användsminne  |  Använt minne  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemory0  |  Använt minne (fragment 0)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemory1  |  Använt minne (fragment 1)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemory2  |  Använt minne (fragment 2)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemory3  |  Använt minne (fragment 3)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemory4  |  Använt minne (fragment 4)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemory5  |  Använt minne (fragment 5)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemory6  |  Använt minne (fragment 6)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemory7  |  Använt minne (fragment 7)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemory8  |  Använt minne (fragment 8)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  användsminne9  |  Använt minne (fragment 9)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemorypercentage  |  Använd minnesprocent  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss  |  Använt minne RSS  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Använt minne RSS (Shard 0)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss1  |  Använt minne RSS (Shard 1)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Använt minne RSS (Shard 2)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Använt minne RSS (Shard 3)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Använt minne RSS (Shard 4)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Använt minne RSS (Shard 5)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Använt minne RSS (Shard 6)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Använt minne RSS (Shard 7)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Använt minne RSS (Shard 8)  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Använt minne RSS (Shard 9)  |  Byte  |  Maximal | 
| Inga  | Inga |  Microsoft.ClassicCompute/domainNames/slots/roller  |  Diskläs byte/sek  |  Diskläsning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/domainNames/slots/roller  |  Diskläsningsåtgärder/sek  |  Diskläsningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicCompute/domainNames/slots/roller  |  Byte för diskskrivning/sek  |  Skriva disk  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/domainNames/slots/roller  |  Diskskrivningsåtgärder/sek  |  Diskskrivningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/domainNames/slots/roller  |  Nätverk – inkommande  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/domainNames/slots/roller  |  Nätverk – utgående  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/domainNames/slots/roller  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicCompute/virtualMachines  |  Diskläs byte/sek  |  Diskläsning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/virtualMachines  |  Diskläsningsåtgärder/sek  |  Diskläsningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicCompute/virtualMachines  |  Byte för diskskrivning/sek  |  Skriva disk  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/virtualMachines  |  Diskskrivningsåtgärder/sek  |  Diskskrivningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/virtualMachines  |  Nätverk – inkommande  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/virtualMachines  |  Nätverk – utgående  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicCompute/virtualMachines  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Begagnad kapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Blob kapacitet  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount (BlobCount)  |  Antal blob  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  ContainerCount (ContainerCount)  |  Antal blob-behållare  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  IndexKapacitet  |  Indexkapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  Filkapacitet  |  Filkapacitet  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  FilCount  |  Antal filer  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  Filsharecount  |  Antal filresurser  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  ArkivDelaQuota  |  Kvotstorlek för filresurs  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  FileShareSnapshotCount  |  Antal ögonblicksbilder av filresurs  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  FileShareSnapshotSize  |  Ögonblicksbildstorlek för filresurs  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.ClassicStorage/storageAccounts/fileServices Microsoft.  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Kökapacitet  |  Kökapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCount (QueueCount)  |  Antal köer  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Antal kömeddelanden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TabellKapacitet  |  Tabellkapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TabellCount  |  Antal bord  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Antal entiteter för tabell  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  Blockerade samtal  |  Blockerade samtal  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  TeckenTränade  |  Tecken tränade  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  Teckenöversatta  |  Översatta tecken  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  Kunderrors  |  Klientfel  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  DataIn  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  DataOut (DataOut)  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  Svarstid  |  Svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  ServerErrors  |  Serverfel  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  TalsessionUnderation  |  Varaktighet för talsession  |  Sekunder  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  FramgångsrikaCalls  |  Lyckade samtal  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  Totalt Antal Meddelanden  |  Totalt antal samtal  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  TotalErrors  |  Totalt antal fel  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  TotaltTokenCalls  |  Totalt antal tokenanrop  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.CognitiveServices/konton  |  Totalt antal transaktioner  |  Totalt antal transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Cpu-krediter förbrukas  |  Cpu-krediter förbrukas  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Återstående CPU-krediter  |  Återstående CPU-krediter  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Ködjup för datadisk  |  Djup för datadiskkö (förhandsgranskning)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Läsbyte för datadisk per sekund  |  Läsbyte för datadisk per sekund (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Läsåtgärder för datadiskar/sek  |  Läsåtgärder för datadisk/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Skrivbyte för datadisk per sekund  |  Skrivbyte för datadisk/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Skrivåtgärder för datadisk/sek  |  Datadiskskrivningsåtgärder/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Diskläs byte  |  Diskläs byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Diskläsningsåtgärder/sek  |  Diskläsningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Byte av diskskrivning  |  Byte av diskskrivning  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Diskskrivningsåtgärder/sek  |  Diskskrivningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Inkommande flöden  |  Inkommande flöden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Inkommande flöden Maximal skapandegrad  |  Inkommande flöden Maximal skapandegrad (förhandsversion)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Nätverk – inkommande  |  Nätverk i fakturerbart (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Nätverket totalt  |  Nätverket totalt  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Nätverk – utgående  |  Utbytbart nätverk (föråldrad)  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Network Out Totalt  |  Network Out Totalt  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Ködjup för OS-disk  |  Os-diskködjup (förhandsgranskning)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Os-disklät byte per sek  |  OS-disklät byte/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Diskläsning av os-disk/sek  |  Diskläsning av operativsystem/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  OS-diskskrivningsbyten per sekund  |  OS-diskskrivningsbyten/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  OS-diskskrivningsåtgärder/sek  |  OS-diskskrivningsåtgärder/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  OS Per Disk QD  |  OS-disk QD (föråldrad)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Os per disk läsbyten per sekund  |  Os-disklät byte/sek (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Os per diskläsningsoperationer/sek  |  Diskläsningsåtgärder för OS-disk/sek (inaktuell)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  OS per disk skrivbyten per sek  |  OS-diskskrivningsbyten/sek (inaktuella)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  OS per diskskrivningsoperationer/sek  |  OS-diskskrivningsåtgärder/sek (inaktuella)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Utgående flöden  |  Utgående flöden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Maximal skapandegrad för utgående flöden  |  Maximal genereringshastighet för utgående flöden (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Per Disk QD  |  Datadisk QD (föråldrad)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Läsbyte per disk per sekund  |  Läsbyten för datadisk/sek (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Läsåtgärder per disk/sek  |  Läsåtgärder för datadiskar/sek (inaktuella)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Skrivbyte per disk per sekund  |  Skrivbyte för datadisk/sek (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Skrivåtgärder per disk/sek  |  Datadiskskrivningsoperationer/sek (inaktuell)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Lästräff för premiumdatadisk cache  |  Lästräff för premiumdatadisk cache (förhandsgranskning)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Premium Data Disk Cache Läs Miss  |  Premium Data Disk Cache Läs Miss (Preview)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Premium OS Disk Cache Läs träff  |  Lästräff för Premium OS-diskcachen (förhandsgranskning)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachines  |  Premium OS Disk cache läs miss  |  Premium OS Disk cache läsmiss (Preview)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Cpu-krediter förbrukas  |  Cpu-krediter förbrukas  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Återstående CPU-krediter  |  Återstående CPU-krediter  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Ködjup för datadisk  |  Djup för datadiskkö (förhandsgranskning)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Läsbyte för datadisk per sekund  |  Läsbyte för datadisk per sekund (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Läsåtgärder för datadiskar/sek  |  Läsåtgärder för datadisk/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Skrivbyte för datadisk per sekund  |  Skrivbyte för datadisk/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Skrivåtgärder för datadisk/sek  |  Datadiskskrivningsåtgärder/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Diskläs byte  |  Diskläs byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Diskläsningsåtgärder/sek  |  Diskläsningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Byte av diskskrivning  |  Byte av diskskrivning  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Diskskrivningsåtgärder/sek  |  Diskskrivningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Inkommande flöden  |  Inkommande flöden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Inkommande flöden Maximal skapandegrad  |  Inkommande flöden Maximal skapandegrad (förhandsversion)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Nätverk – inkommande  |  Nätverk i fakturerbart (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Nätverket totalt  |  Nätverket totalt  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Nätverk – utgående  |  Utbytbart nätverk (föråldrad)  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Network Out Totalt  |  Network Out Totalt  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Ködjup för OS-disk  |  Os-diskködjup (förhandsgranskning)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Os-disklät byte per sek  |  OS-disklät byte/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Diskläsning av os-disk/sek  |  Diskläsning av operativsystem/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  OS-diskskrivningsbyten per sekund  |  OS-diskskrivningsbyten/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  OS-diskskrivningsåtgärder/sek  |  OS-diskskrivningsåtgärder/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  OS Per Disk QD  |  OS-disk QD (föråldrad)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Os per disk läsbyten per sekund  |  Os-disklät byte/sek (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Os per diskläsningsoperationer/sek  |  Diskläsningsåtgärder för OS-disk/sek (inaktuell)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  OS per disk skrivbyten per sek  |  OS-diskskrivningsbyten/sek (inaktuella)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  OS per diskskrivningsoperationer/sek  |  OS-diskskrivningsåtgärder/sek (inaktuella)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Utgående flöden  |  Utgående flöden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Maximal skapandegrad för utgående flöden  |  Maximal genereringshastighet för utgående flöden (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Per Disk QD  |  Datadisk QD (föråldrad)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Läsbyte per disk per sekund  |  Läsbyten för datadisk/sek (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Läsåtgärder per disk/sek  |  Läsåtgärder för datadiskar/sek (inaktuella)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Skrivbyte per disk per sekund  |  Skrivbyte för datadisk/sek (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Skrivåtgärder per disk/sek  |  Datadiskskrivningsoperationer/sek (inaktuell)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Lästräff för premiumdatadisk cache  |  Lästräff för premiumdatadisk cache (förhandsgranskning)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Premium Data Disk Cache Läs Miss  |  Premium Data Disk Cache Läs Miss (Preview)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS Disk Cache Läs träff  |  Lästräff för Premium OS-diskcachen (förhandsgranskning)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS Disk cache läs miss  |  Premium OS Disk cache läsmiss (Preview)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Cpu-krediter förbrukas  |  Cpu-krediter förbrukas  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Återstående CPU-krediter  |  Återstående CPU-krediter  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Ködjup för datadisk  |  Djup för datadiskkö (förhandsgranskning)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Läsbyte för datadisk per sekund  |  Läsbyte för datadisk per sekund (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Läsåtgärder för datadiskar/sek  |  Läsåtgärder för datadisk/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Skrivbyte för datadisk per sekund  |  Skrivbyte för datadisk/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Skrivåtgärder för datadisk/sek  |  Datadiskskrivningsåtgärder/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Diskläs byte  |  Diskläs byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Diskläsningsåtgärder/sek  |  Diskläsningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Byte av diskskrivning  |  Byte av diskskrivning  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Diskskrivningsåtgärder/sek  |  Diskskrivningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Inkommande flöden  |  Inkommande flöden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Inkommande flöden Maximal skapandegrad  |  Inkommande flöden Maximal skapandegrad (förhandsversion)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Nätverk – inkommande  |  Nätverk i fakturerbart (inaktuellt)  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Nätverket totalt  |  Nätverket totalt  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Nätverk – utgående  |  Utbytbart nätverk (föråldrad)  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Network Out Totalt  |  Network Out Totalt  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Ködjup för OS-disk  |  Os-diskködjup (förhandsgranskning)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Os-disklät byte per sek  |  OS-disklät byte/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Diskläsning av os-disk/sek  |  Diskläsning av operativsystem/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  OS-diskskrivningsbyten per sekund  |  OS-diskskrivningsbyten/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  OS-diskskrivningsåtgärder/sek  |  OS-diskskrivningsåtgärder/sek (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  OS Per Disk QD  |  OS-disk QD (föråldrad)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Os per disk läsbyten per sekund  |  Os-disklät byte/sek (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Os per diskläsningsoperationer/sek  |  Diskläsningsåtgärder för OS-disk/sek (inaktuell)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  OS per disk skrivbyten per sek  |  OS-diskskrivningsbyten/sek (inaktuella)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  OS per diskskrivningsoperationer/sek  |  OS-diskskrivningsåtgärder/sek (inaktuella)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Utgående flöden  |  Utgående flöden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Maximal skapandegrad för utgående flöden  |  Maximal genereringshastighet för utgående flöden (förhandsgranskning)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Per Disk QD  |  Datadisk QD (föråldrad)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Läsbyte per disk per sekund  |  Läsbyten för datadisk/sek (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Läsåtgärder per disk/sek  |  Läsåtgärder för datadiskar/sek (inaktuella)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Skrivbyte per disk per sekund  |  Skrivbyte för datadisk/sek (inaktuellt)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Skrivåtgärder per disk/sek  |  Datadiskskrivningsoperationer/sek (inaktuell)  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Lästräff för premiumdatadisk cache  |  Lästräff för premiumdatadisk cache (förhandsgranskning)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Premium Data Disk Cache Läs Miss  |  Premium Data Disk Cache Läs Miss (Preview)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Premium OS Disk Cache Läs träff  |  Lästräff för Premium OS-diskcachen (förhandsgranskning)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.Compute/virtualMachineScaleSets/virtualMachines Microsoft.  |  Premium OS Disk cache läs miss  |  Premium OS Disk cache läsmiss (Preview)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  CPU-användning  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.ContainerInstance/containerGroups  |  Minnesupptring  |  Minnesanvändning  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Nätverksbyte som tas emot per sekund  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.ContainerInstance/containerGroups  |  NätverksbyteÖversändadPerSecond  |  Nätverksbyte överförda per sekund  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Container-register/register  |  RunDuration  |  Kör varaktighet  |  Millisekunder  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Container-register/register  |  LyckadPullCount  |  Lyckat pull-antal  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Container-register/register  |  LyckadPushCount  |  Lyckat antal push-ningar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Container-register/register  |  Totalt Antal  |  Totalt antal pull  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Container-register/register  |  TotaltKonto  |  Totalt antal push-tryck  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Totalt antal tillgängliga cpu-kärnor i ett hanterat kluster  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Total mängd tillgängligt minne i ett hanterat kluster  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Status för olika nodvillkor  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Antal poddar efter fas  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Antal poddar i redoläge  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Tillgängligkapacitet  |  Tillgänglig kapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesLaddadeToCloud  |  Molnbyte uppladdade (enhet)  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUppladdadeToCloudPerShare  |  Cloud Bytes uppladdade (dela)  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Dataflöde för molnhämtning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Dataflöde för molnhämtning (delning)  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadDrgenomput  |  Molnuppladdningsdataflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadDroughputPerShare  |  Molnladdningsdataflöde (dela)  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Edge Compute - Minnesanvändning  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Edge Compute - Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Läs dataflöde (nätverk)  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteDetgenomput  |  Skrivdataflöde (nätverk)  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Totalkapacitet  |  Total kapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataFactory/datafakta  |  MisslyckadeRuns  |  Misslyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/datafakta  |  LyckadeRuns  |  Lyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  ActivityCancelledRuns  |  Avbrutna aktivitet kör mått  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  ActivityFailedRuns  |  Misslyckade aktivitet kör mått  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  ActivitySucceededRuns  |  Efterföljande aktivitet kör mått  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  FactorySizeInGbUnits  |  Total fabriksstorlek (GB-enhet)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  IntegrationRuntimeTillgängligt Minne  |  Tillgängligt minne för integreringskörning  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  IntegrationRuntimeAverageTaskPickupDelay  |  Varaktighet för integrationskörningskö  |  Sekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  IntegrationRuntimeCpuPercentage  |  Processoranvändning för integreringskörning  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  IntegrationRuntimeQueueLength  |  Kölängd för integrationskörning  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  MaxTillåtafactorysizeInGbUnits  |  Maximal tillåten fabriksstorlek (GB-enhet)  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  MaxTilldömdResourceCount  |  Antal tillåtna enheter med maximal tillåten enhet  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  PipelineCancelledRuns  |  Avbrutna pipelinekörningsmått  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  PipelineFailedRuns  |  Misslyckade pipelinekörningar mått  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  PipelineSucceedEdRuns  |  Efterföljande pipeline kör mått  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  ResursCount  |  Totalt antal enheter  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  TriggerCancelledRuns  |  Avbruten utlösare kör mått  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  TriggerFailedRuns  |  Misslyckade utlösarkörningsmått  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataFactory/fabriker  |  TriggerSucceededRuns  |  Efterföljande utlösarkörningsmått  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeAnalytics/konton  |  JobbAUEndedKancelled  |  Inställd AU-tid  |  Sekunder  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeAnalytics/konton  |  JobbAUEndedFailure  |  Misslyckad AU-tid  |  Sekunder  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeAnalytics/konton  |  JobbAUEndedSuccess  |  Framgångsrik AU-tid  |  Sekunder  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeAnalytics/konton  |  JobbEndedKancelled  |  Avbrutna jobb  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeAnalytics/konton  |  JobbEndedFailure  |  Misslyckade jobb  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeAnalytics/konton  |  JobbEndedSuccess  |  Framgångsrika jobb  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeStore/konton  |  DataLäs  |  Data läsa  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeStore/konton  |  Dataskrivet  |  Uppgifter skrivna  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeStore/konton  |  ReadRequests (LäsRequests)  |  Läsa begäranden  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DataLakeStore/konton  |  TotalStorage  |  Totalt lagringsutrymme  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DataLakeStore/konton  |  WriteRequests  |  Skriv förfrågningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  active_connections  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  backup_storage_used  |  Lagring för säkerhetskopiering som används  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  connections_failed  |  Misslyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  io_consumption_percent  |  IO procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  memory_percent  |  Minne procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  network_bytes_egress  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  network_bytes_ingress  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  seconds_behind_master  |  Fördröjning för replikering på några sekunder  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  serverlog_storage_limit  |  Lagringsgräns för serverlogg  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  serverlog_storage_percent  |  Lagringsprocent för serverlogg  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  serverlog_storage_usage  |  Serverlogglagring används  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  storage_limit  |  Lagringsgräns  |  Byte  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  storage_percent  |  Lagringsprocent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servrar  |  storage_used  |  Användning av lagring  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  active_connections  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  backup_storage_used  |  Lagring för säkerhetskopiering som används  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  connections_failed  |  Misslyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  io_consumption_percent  |  IO procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  memory_percent  |  Minne procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  network_bytes_egress  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  network_bytes_ingress  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  seconds_behind_master  |  Fördröjning för replikering på några sekunder  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  serverlog_storage_limit  |  Lagringsgräns för serverlogg  |  Byte  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  serverlog_storage_percent  |  Lagringsprocent för serverlogg  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  serverlog_storage_usage  |  Serverlogglagring används  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  storage_limit  |  Lagringsgräns  |  Byte  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  storage_percent  |  Lagringsprocent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servrar  |  storage_used  |  Användning av lagring  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  active_connections  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  backup_storage_used  |  Lagring för säkerhetskopiering som används  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  connections_failed  |  Misslyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  io_consumption_percent  |  IO procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  memory_percent  |  Minne procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  network_bytes_egress  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  network_bytes_ingress  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  pg_replica_log_delay_in_bytes  |  Max fördröjning över repliker  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  pg_replica_log_delay_in_seconds  |  Replik lag  |  Sekunder  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  serverlog_storage_limit  |  Lagringsgräns för serverlogg  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  serverlog_storage_percent  |  Lagringsprocent för serverlogg  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  serverlog_storage_usage  |  Serverlogglagring används  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  storage_limit  |  Lagringsgräns  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  storage_percent  |  Lagringsprocent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/servrar  |  storage_used  |  Användning av lagring  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Aktiva anslutningar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/serversv2  |  Iops  |  IOPS  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Minne procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Lagringsprocent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Användning av lagring  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/konto  |  digitaltwins.telemetry.noder  |  Platshållare för digital twins-nodtelemetri  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  C2D-meddelanden övergivna  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  C2D-meddelandeleveranser har slutförts  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  C2D-meddelanden avvisade  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Misslyckade direkta metodaropcations  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Begär storlek på direkta metodaropcations  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Svarsstorlek för direkta metodutkallelser  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Lyckade direkta metodaropationer  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Misslyckade tvillingläsningar från baksidan  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Svarsstorleken på tvillingavläsningar från den bakre änden  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Lyckad tvilling läser från slutet  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Misslyckade dubbla uppdateringar från backend  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Storleken på dubbla uppdateringar från baksidan  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Lyckade dubbla uppdateringar från slutet  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpirerad  |  C2D-meddelanden har upphört att gälla (förhandsgranskning)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  Konfigurationer  |  Konfigurationsmått  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Anslutna enheter (förhandsgranskning)  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Routning: meddelanden som levereras till meddelanden/händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Routning: meddelanden som levereras till eventhubben  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Routning: meddelanden som levereras till servicebusskö  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Routning: meddelanden som levereras till Service Bus-ämne  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Routning: meddelanden som levereras till lagring  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobbar  |  Routning: blobbar som levereras till lagring  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Routning: data som levereras till lagring  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latens.builtIn.events  |  Routning: meddelandefördröjning för meddelanden/händelser  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latens.eventHubs  |  Routning: meddelandesvarstid för händelsehubben  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Routning: meddelandesvarstid för servicebusskö  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latens.serviceBusTopics  |  Routning: meddelandefördröjning för servicebussavsnitt  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latens.lagring  |  Routning: meddelandefördröjning för lagring  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Routning: telemetrimeddelanden har tagits bort   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetri.egress.reserv  |  Routning: meddelanden som levereras till reserv  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Routning: telemetrimeddelanden är inkompatibla  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned d2c.telemetry.egress.orphaned d2c.telemetry.egress.orphaned d2  |  Routning: överblivna telemetrimeddelanden   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Routning: telemetrimeddelanden levererade  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Skicka försök till telemetri  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Antal begränsningsfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Skickade telemetrimeddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Misslyckade tvillingavläsningar från enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Svarsstorleken för dubbla läsningar från enheter  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Lyckade tvillingläsningar från enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Misslyckade dubbla uppdateringar från enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Storlek på dubbla uppdateringar från enheter  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Lyckade dubbla uppdateringar från enheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Totalt antal meddelanden som används  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Total användning av enhetsdata  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Total enhetsdataanvändning (förhandsgranskning)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Anslutna enheter (inaktuella)   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Totalt antal enheter (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Leveranser av eventrutnät(förhandsgranskning)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Svarstid för händelserutnät (förhandsgranskning)  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Misslyckade avbokningar av jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Lyckade jobbavbokningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobb.slutförda  |  Slutförda jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Misslyckade skapande av metod-anropsjobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Framgångsrika skapande av metod anrop jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Misslyckade skapande av dubbla uppdateringsjobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Framgångsrika skapande av dubbla uppdateringsjobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  Misslyckade jobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Misslyckade anrop till listjobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Lyckade anrop till listjobb  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Misslyckade jobbfrågor  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Lyckade jobbfrågor  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Devices/IotHubs  |  totaltDeviceCount  |  Totalt antal enheter (förhandsgranskning)  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Misslyckade dubbla frågor  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Dubbla frågor resultatstorlek  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.success twinQueries.success twinQueries.success twinQue  |  Lyckade dubbla frågor  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.  |  AttestationAttempts  |  Attesteringsförsök  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.  |  EnhetTilldelningar  |  Enheter som tilldelats  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.  |  RegistreringSanpassa  |  Registreringsförsök  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  TillgängligtStora  |  Tillgänglig lagring  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  CassandraAnslutningAr  |  Cassandra Anslutning Nedläggningar  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  CassandraRequest Laddar  |  Avgifter för begäran om cassandra  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  CassandraRequests  |  Cassandra Förfrågningar  |  Antal  |  Antal | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  DataUpparage  |  Dataanvändning  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.DocumentDB/databasKonton  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Antal  |  Antal | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  DokumentCount  |  Antal dokument  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  DocumentQuota (dokumentquota)  |  Dokumentkvot  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  IndexUsage  |  Indexanvändning  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  MetadataRequests  |  Begäranden om metadata  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databasKonton  |  MongoRequest Ladda  |  Mongo begäran avgift  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databasKonton  |  MongoRequests (  |  Mongo Förfrågningar  |  Antal  |  Antal | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  MongoRequestsCount (Olikartade)  |  Mongo-begärandehastighet  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  MongoRequestsDelete  |  Mångås ta bort begäranden  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  MongoRequestsInsert  |  Begärandehastighet för Mongo-infogning  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  MongoRequestsQuery (Mån)  |  Mångo-frågebegäranden  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  MongoRequestsUppdatera  |  Uppdateringsfrekvens för Mongo  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  EtableradGenomströmning  |  Etablerat dataflöde  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.DocumentDB/databasKonton  |  Replikeringslatens  |  P99 Replikeringssvarstid  |  Millisekunder  |  Medel | 
| Inga  | Inga |  Microsoft.DocumentDB/databasKonton  |  ServiceTillgänglighet  |  Tjänstens tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databasKonton  |  TotalRequests  |  Totalt antal förfrågningar  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databasKonton  |  TotaltRequestUnits  |  Totalt antal enheter för begärande  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EnterpriseKnowledgeGraph/tjänster  |  Felräkning  |  Antal misslyckade  |  Antal  |  Antal | 
| Inga  | Inga |  Microsoft.EnterpriseKnowledgeGraph/tjänster  |  Antal lyckade länder  |  Antal slutförda  |  Antal  |  Antal | 
| Inga  | Inga |  Microsoft.EnterpriseKnowledgeGraph/tjänster  |  FramgångLatens  |  Svarstid för lyckade svar  |  Millisekunder  |  Medel | 
| Inga  | Inga |  Microsoft.EnterpriseKnowledgeGraph/tjänster  |  Transaktionsgräns  |  Antal transaktioner  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domäner  |  DeadLetteredCount (döddött)  |  Dead Lettered Händelser  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventGrid/domäner  |  LeveransAttemptFailCount  |  Misslyckade händelser vid leverans  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domäner  |  LeveransSuccessCount  |  Levererade händelser  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventGrid/domäner  |  MålprocesseraVarningInM  |  Varaktighet för målbearbetning  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domäner  |  DroppedEventCount  |  Tappade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domäner  |  MatchedEventCount  |  Matchade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domäner  |  PubliceraFailCount  |  Publicera misslyckade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domäner  |  PubliceraSuccessCount  |  Publicerade evenemang  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domäner  |  PubliceraUgessLatencyInMs  |  Publicera svarstid för lyckade uppgifter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventAbonnemang  |  DeadLetteredCount (döddött)  |  Dead Lettered Händelser  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventGrid/eventAbonnemang  |  LeveransAttemptFailCount  |  Misslyckade händelser vid leverans  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventAbonnemang  |  LeveransSuccessCount  |  Levererade händelser  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventGrid/eventAbonnemang  |  MålprocesseraVarningInM  |  Varaktighet för målbearbetning  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventAbonnemang  |  DroppedEventCount  |  Tappade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventAbonnemang  |  MatchedEventCount  |  Matchade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionTopics  |  PubliceraFailCount  |  Publicera misslyckade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionTopics  |  PubliceraSuccessCount  |  Publicerade evenemang  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionTopics  |  PubliceraUgessLatencyInMs  |  Publicera svarstid för lyckade uppgifter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionTopics  |  OmatchadHändelseCount  |  Omatchade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Ämnen för Microsoft.EventGrid/Event  |  PubliceraFailCount  |  Publicera misslyckade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Ämnen för Microsoft.EventGrid/Event  |  PubliceraSuccessCount  |  Publicerade evenemang  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Ämnen för Microsoft.EventGrid/Event  |  PubliceraUgessLatencyInMs  |  Publicera svarstid för lyckade uppgifter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Ämnen för Microsoft.EventGrid/Event  |  OmatchadHändelseCount  |  Omatchade händelser  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  ActiveConnections (Aktiva Anslutning)  |  ActiveConnections (Aktiva Anslutning)  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  Tillgängligt Minne  |  Tillgängligt minne  |  Procent  |  Maximal | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  CaptureBacklog  |  Fånga eftersläpning.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  CapturedBytes  |  Fångade byte.  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  CapturedMessages  |  Hämtade meddelanden.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  Anslutningar Slutna  |  Anslutningar stängda.  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  AnslutningarÖppnade  |  Anslutningar öppnade.  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  Processor  |  Processor  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/kluster  |  IncomingBytes  |  Inkommande byte.  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/kluster  |  IncomingMessages  |  Inkommande meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/kluster  |  Inkommande Begäran  |  Inkommande begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/kluster  |  Utgåendebyte  |  Utgående byte.  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/kluster  |  UtgåendeMessages  |  Utgående meddelanden  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  KvotExceedEdErrors  |  Kvoten överskred fel.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  ServerErrors  |  Serverfel.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  Lyckade Begärande  |  Lyckade förfrågningar  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  ThrottledRequests  |  Begränsade begäranden.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/kluster  |  UserErrors  |  Användarfel.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  ActiveConnections (Aktiva Anslutning)  |  ActiveConnections (Aktiva Anslutning)  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  CaptureBacklog  |  Fånga eftersläpning.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  CapturedBytes  |  Fångade byte.  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  CapturedMessages  |  Hämtade meddelanden.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  Anslutningar Slutna  |  Anslutningar stängda.  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  AnslutningarÖppnade  |  Anslutningar öppnade.  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  EHABL (på andra)  |  Arkivera eftersläpande meddelanden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  EHAMBS (på andra sätt)  |  Arkivera meddelandegenomströmning (föråldrad)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  EHAMSGS (på en)  |  Arkivera meddelanden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  EHINBYTES (PÅ ANDRA)  |  Inkommande byte (inaktuell)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  EHINMBS (HINMBS)  |  Inkommande byte (föråldrade) (inaktuella)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  EHINMSGS (PÅ ANDRA SÄTT)  |  Inkommande meddelanden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  EHOUTBYTES (PÅ ANDRA SÄTT)  |  Utgående byte (inaktuella)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  EHOUTMBS (AVS)  |  Utgående byte (föråldrade) (inaktuella)  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  EHOUTMSGS (AVS)  |  Utgående meddelanden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  FAILREQ (FEL)  |  Misslyckade begäranden (inaktuell)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  IncomingBytes  |  Inkommande byte.  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  IncomingMessages  |  Inkommande meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  Inkommande Begäran  |  Inkommande begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  INMSGS (INMSGS)  |  Inkommande meddelanden (föråldrade) (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  INREQS (INREQS)  |  Inkommande begäranden (inaktuell)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  INTERR (INTERR)  |  Interna serverfel (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  MISCERR (FEL)  |  Andra fel (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  Utgåendebyte  |  Utgående byte.  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  UtgåendeMessages  |  Utgående meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  OUTMSGS (UTSM)  |  Utgående meddelanden (föråldrade) (inaktuella)  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  KvotExceedEdErrors  |  Kvoten överskred fel.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  ServerErrors  |  Serverfel.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  Storlek  |  Storlek  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  Lyckade Begärande  |  Lyckade förfrågningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  SUCCREQ (SUCCREQ)  |  Lyckade begäranden (inaktuella)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namnområden  |  SVRBSY  |  Server upptagen fel (föråldrad)  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  ThrottledRequests  |  Begränsade begäranden.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.EventHub/namnområden  |  UserErrors  |  Användarfel.  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.HDInsight/kluster  |  KategoriseradeGatewayRequests  |  Kategoriserade gateway-begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.HDInsight/kluster  |  GatewayRequests  |  Gateway-begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.HDInsight/kluster  |  NumActiveArbetare  |  Antal aktiva arbetstagare  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.HDInsight/kluster  |  SkalningRequests  |  Skala begäranden  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold (metriska)  |  Tröskelvärde för mått  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/AutoscaleSettings  |  Observeradkapacitet  |  Observerad kapacitet  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/AutoscaleSettings  |  ObserveratMetricVärde  |  Observerat metriskt värde  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Skala åtgärder som initierats  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  tillgänglighetResultat/tillgänglighetPercentage  |  Tillgänglighet  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Insights/Components  |  tillgänglighetResultat/antal  |  Tillgänglighetstester  |  Antal  |  Antal | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  tillgänglighetResultat/varaktighet  |  Varaktighet för tillgänglighetstest  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  webbläsareTimings/nätverkUnderydning  |  Anslutningstid för sidbelastning av nätverksanslutning  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  webbläsareTimings/bearbetningUnder behandling  |  Tid för klientbearbetning  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  webbläsareTimings/mottagningUnder  |  Ta emot svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  webbläsareTimings/sendDuration  |  Skicka tid för begäran  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  webbläsareTimings/totalUnderation  |  Inläsningstid för webbläsarsidan  |  Millisekunder  |  Medel | 
| Inga  | Inga |  Microsoft.Insights/Components  |  beroenden/antal  |  Beroendeanrop  |  Antal  |  Antal | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  beroenden/varaktighet  |  Beroendevaraktighet  |  Millisekunder  |  Medel | 
| Inga  | Inga |  Microsoft.Insights/Components  |  beroenden/misslyckades  |  Fel i beroendeanrop  |  Antal  |  Antal | 
| Inga  | Inga |  Microsoft.Insights/Components  |  undantag/webbläsare  |  Webbläsarundantag  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  undantag/antal  |  Undantag  |  Antal  |  Antal | 
| Inga  | Inga |  Microsoft.Insights/Components  |  undantag/server  |  Serverundantag  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  pageVisningar/antal  |  Sidvisningar  |  Antal  |  Antal | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  pageVisningar/varaktighet  |  Inläsningstid för sidvy  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  Undantagsfrekvens  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  performanceCounters/memoryTillgängligaBytes  |  Tillgängligt minne  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  Bearbeta CPU  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  Process-IO-hastighet  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  Processortid  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  Bearbeta privata byte  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  HTTP-körningstid för HTTP-begäran  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  HTTP-begäranden i programkö  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  HTTP-begäran hastighet  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.Insights/Components  |  begäranden/antal  |  Serverbegäranden  |  Antal  |  Antal | 
| **Ja**  | Inga |  Microsoft.Insights/Components  |  begäranden/varaktighet  |  Svarstid för servern  |  Millisekunder  |  Medel | 
| Inga  | Inga |  Microsoft.Insights/Components  |  begäranden/misslyckades  |  Misslyckade förfrågningar  |  Antal  |  Antal | 
| Inga  | Inga |  Microsoft.Insights/Components  |  förfrågningar/ränta  |  Serverbegäranshastighet  |  CountPerSecond  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  spår/antal  |  Traces  |  Antal  |  Antal | 
| **Ja**  | Inga |  Microsoft.KeyVault/valv  |  ServiceApiHit  |  Totalt antal service-api-träffar  |  Antal  |  Antal | 
| **Ja**  | Inga |  Microsoft.KeyVault/valv  |  ServiceApiLatency  |  Svarstid för tjänstens api  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.KeyVault/valv  |  ServiceApiResult  |  Totalt antal api-resultat för tjänsten  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  CacheUtilisering  |  Cacheanvändning  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  Kontinuerligt ExporteraMaxLatenessMinutes  |  Kontinuerlig export Max Lateness Minuter  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  ContinuousExportNumOfRecordsExported  |  Kontinuerlig export - antal exporterade poster  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  Kontinuerligt Exportera väntande konto  |  Kontinuerligt antal väntande export  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  Kontinuerligt ExporteraResult  |  Kontinuerligt exportresultat  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  Processor  |  Processor  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  Händelser BearbetasForEventHubs  |  Händelser som bearbetas (för event/IoT-hubbar)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  ExportUtilisering  |  Exportutnyttjande  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  IntagLatencyInSeconds  |  Svarstid för inmatning (i sekunder)  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  IntagResultat  |  Intag resultat  |  Antal  |  Antal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  IntagUtilisering  |  Utnyttjande av förtäring  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  IntagVolumeInMB  |  Intagsvolym (i MB)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  Keepalive  |  Håll dig vid liv  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  FrågaUnderation  |  Varaktighet för fråga  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  SteamingIngestRequestRate  |  Streaming Ingest begärsthastighet  |  Antal  |  RäntaRequestsPerSecond | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  Strömmandedatatdata  |  Datahastighet för strömmande intning  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  StreamingIngestDuration  |  Varaktighet för strömmande intning  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Kluster  |  StreamingIngestResults  |  Streaming Intest Resultat  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  ActionLatency (ÅtgärdLatens)  |  Svarstid för åtgärder   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  ÅtgärderKomt  |  Slutförda åtgärder   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  ÅtgärderMisslade  |  Åtgärderna misslyckades   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  ÅtgärderSkipade  |  Åtgärder som hoppas över   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  ÅtgärderStartad  |  Åtgärder har startats   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  Åtgärder som  |  Åtgärderna lyckades   |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  ÅtgärdUppsugning  |  Svarstid för åtgärden lyckades   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  ActionThrottledEvents  |  Åtgärder begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  IntegrationServiceMiljöAnslutningarMemoryUsage  |  Anslutningsminnesanvändning för integrationstjänstmiljö  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  IntegrationServiceMiljöAnslutningsprocessorAnvändare  |  Anslutningsprocessoranvändning för integrationstjänstmiljö  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  IntegrationServiceMiljöArbetsflödeMemoryUsage  |  Arbetsflödesminnesanvändning för integrationstjänstmiljö  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  IntegrationServiceMiljöArbetsflödeProcessorUsage  |  Arbetsflödesprocessoranvändning för integrationstjänstmiljö  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  RunFailurePercentage  |  Kör felprocent  |  Procent  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  RunLatency (körl)  |  Kör svarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  RunsKanlade  |  Körs avbruten  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  RunsCompleted  |  Slutförda körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  RunsFailed  |  Det gick inte att köra körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  RunsStarted  |  Startar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  RunsSucceeded  |  Körningar lyckades  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  RunStartThrottledEvents  |  Begränsa antalet begränsade händelser för körning  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  RunSuccessLatency  |  Kör svarstid för lyckade svar  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  RunThrottledEvents  |  Köra begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  TriggerFireLatency  |  Svarstid för utlösande utlösare   |  Sekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  TriggerLatency  |  Svarstid för utlösare   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  TriggersCompleted  |  Utlösare har slutförts   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  TriggersFailed  |  Utlösare misslyckades   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  UtlösareAvfyrade  |  Avfyrade utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  TriggersSkipped  |  Överhoppade utlösare  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  TriggersStarted  |  Utlösare startade   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  TriggersSucceeded  |  Utlösare lyckades   |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/integrationServiceMiljöer  |  TriggerSuccessLatency  |  Svarstid för utlösarlyckades   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceMiljöer  |  TriggerThrottledEvents  |  Utlösarbegränsningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/arbetsflöden  |  ActionLatency (ÅtgärdLatens)  |  Svarstid för åtgärder   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  ÅtgärderKomt  |  Slutförda åtgärder   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  ÅtgärderMisslade  |  Åtgärderna misslyckades   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  ÅtgärderSkipade  |  Åtgärder som hoppas över   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  ÅtgärderStartad  |  Åtgärder har startats   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  Åtgärder som  |  Åtgärderna lyckades   |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/arbetsflöden  |  ÅtgärdUppsugning  |  Svarstid för åtgärden lyckades   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  ActionThrottledEvents  |  Åtgärder begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  Fakturerbara ÅtgärderExecutions  |  Fakturerbara åtgärdskörningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  FakturerbaraTriggerUtskurningar  |  Fakturerbara utlösarkörningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  BillingUsageNativeOperation  |  Faktureringsanvändning för körning i inbyggda operationer  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  BillingUsageNativeOperation  |  Faktureringsanvändning för körning i inbyggda operationer  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  BillingUsageStandardConnector  |  Faktureringsanvändning för standardappkörningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  BillingUsageStandardConnector  |  Faktureringsanvändning för standardappkörningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  FaktureringUsageStorageKonsumtion  |  Faktureringsanvändning för körning av lagringsförbrukning  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  FaktureringUsageStorageKonsumtion  |  Faktureringsanvändning för körning av lagringsförbrukning  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  RunFailurePercentage  |  Kör felprocent  |  Procent  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/arbetsflöden  |  RunLatency (körl)  |  Kör svarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  RunsKanlade  |  Körs avbruten  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  RunsCompleted  |  Slutförda körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  RunsFailed  |  Det gick inte att köra körningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  RunsStarted  |  Startar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  RunsSucceeded  |  Körningar lyckades  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  RunStartThrottledEvents  |  Begränsa antalet begränsade händelser för körning  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/arbetsflöden  |  RunSuccessLatency  |  Kör svarstid för lyckade svar  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  RunThrottledEvents  |  Köra begränsade händelser  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  Totalt FakturerbaraUtredningar  |  Totalt fakturerbara avrättningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/arbetsflöden  |  TriggerFireLatency  |  Svarstid för utlösande utlösare   |  Sekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Logic/arbetsflöden  |  TriggerLatency  |  Svarstid för utlösare   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  TriggersCompleted  |  Utlösare har slutförts   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  TriggersFailed  |  Utlösare misslyckades   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  UtlösareAvfyrade  |  Avfyrade utlösare   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  TriggersSkipped  |  Överhoppade utlösare  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  TriggersStarted  |  Utlösare startade   |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  TriggersSucceeded  |  Utlösare lyckades   |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Logic/arbetsflöden  |  TriggerSuccessLatency  |  Svarstid för utlösarlyckades   |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Logic/arbetsflöden  |  TriggerThrottledEvents  |  Utlösarbegränsningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Aktiva kärnor  |  Aktiva kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Aktiva noder  |  Aktiva noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Slutförda körningar  |  Slutförda körningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Misslyckade körningar  |  Misslyckade körningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Inaktiva kärnor  |  Inaktiva kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Inaktiva noder  |  Inaktiva noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Lämna kärnor  |  Lämna kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Lämna noder  |  Lämna noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Det gick inte att distribuera modeller  |  Det gick inte att distribuera modeller  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Modellut distribuera igång  |  Modellut distribuera igång  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Modellut distribuera lyckades  |  Modellut distribuera lyckades  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Det gick inte att registrera modellregistret  |  Det gick inte att registrera modellregistret  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Modellregistret lyckades  |  Modellregistret lyckades  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Föregripna kärnor  |  Föregripna kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Föregripna noder  |  Föregripna noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Procent av kvotutnyttjande  |  Procent av kvotutnyttjande  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Startade körningar  |  Startade körningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Totalt kärnor  |  Totalt kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Totalt antal noder  |  Totalt antal noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Oersättliga kärnor  |  Oersättliga kärnor  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.MachineLearningServices/arbetsytor  |  Oersättliga noder  |  Oersättliga noder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Maps/konton  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Maps/konton  |  Användning  |  Användning  |  Antal  |  Antal | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices  |  Tillgångsräkning  |  Antal tillgångar  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices  |  TillgångQuota  |  Tillgångskvot  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices  |  TillgångQuotaUsedPercentage  |  Använd procentandel för tillgångskvot  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices  |  InnehållKeyPolicyCount  |  Antal principer för innehållsnyckel  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota  |  Policykvot för innehållsnyckel  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuotaUsedPercentage  |  Procentuell kvot för innehållsnyckelprincip  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices  |  StreamingPolicyCount  |  Antal streamingprinciper  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  Kvot för direktuppspelningsprincip  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Datauppspelningsprincipkvot använd procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices/streamingEndpoints  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices/streamingEndpoints  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Media/mediaservices/streamingEndpoints  |  FramgångE2ELatency  |  Lyckades avsluta svarstiden  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  GCPauseTotalCount  |  Antal GC-pauser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  GCPauseTotalTime  |  GC Paus total tid  |  Millisekunder  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  MaxOldGenMemoryPoolBytes  |  Max tillgänglig datastorlek för gammal generation  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  OldGenMemoryPoolBytes  |  Datastorlek för gammal generering  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  OldGenPromotedBytes  |  Befordra till datastorlek för gammal generation  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  ServiceCpuUsagePercentage  |  Procentandel av tjänstens CPU-användning  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  ServiceMemoryCommitted  |  Tjänstminne tilldelas  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  ServiceMemoryMax  |  Högsta till tjänstminne  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  ServiceMemoryUsed  |  Tjänstminne används  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  SystemCpuUsagePercentage  |  Procentandel av användningen av system-PROCESSOR  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatErrorCount  |  Tomcat globalt fel  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatReceivedBytes  |  Tomcat totalt mottagna byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatRequestMaxTime  |  Tomcat begäran max tid  |  Millisekunder  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatRequestTotalCount  |  Totalt antal antal för Tomcat-begäran  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatRequestTotalTime  |  Totalt antal gånger för Tomcat-begäran  |  Millisekunder  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatResponseAvgTime  |  Genomsnittlig tid för Tomcat-begäran  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatSentBytes  |  Tomcat Totalt antal skickade byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatSessionActiveCurrentCount  |  Tomcat session levande räkna  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatSessionActiveMaxCount  |  Tomcat-session Max aktivt antal  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatSessionAliveMaxTime  |  Tomcat Session Max Alive Tid  |  Millisekunder  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatSessionCreatedCount  |  Tomcat-session skapad räkning  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatSessionExpiredCount  |  Tomcat-session har upphört att gälla  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  TomcatSessionRejectedCount  |  Tomcat-session avvisad räkning  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Microservices4Spring/appKuddar  |  YoungGenPromotedBytes  |  Främja till young generation datastorlek  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Tilldelade volympooler  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Total logisk storlek för volympool  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.NetApp/netAppAccounts/capacityPools/volymer  |  Genomsnittligläsning  |  Genomsnittlig läsfördröjning  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.NetApp/netAppAccounts/capacityPools/volymer  |  AverageWriteLatency  |  Genomsnittlig skrivfördröjning  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.NetApp/netAppAccounts/capacityPools/volymer  |  ReadIops (läs)  |  Läs iops  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.NetApp/netAppAccounts/capacityPools/volymer  |  VolumeLogicalSize  |  Logiskt volymstorlek  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.NetApp/netAppAccounts/capacityPools/volymer  |  VolumeSnapshotSize  |  Storlek på ögonblicksbilder av volym  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.NetApp/netAppAccounts/capacityPools/volymer  |  WriteIops  |  Skriv iops  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Total tid för programgateway  |  Millisekunder  |  Medel | 
| Inga  | Inga |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Begäranden per minut och felfri värd  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Anslutningstid för backend  |  Millisekunder  |  Medel | 
| Inga  | Inga |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Svarstid för serverad första byte  |  Millisekunder  |  Medel | 
| Inga  | Inga |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Serverad senaste byte svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Svarsstatus för backend  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  BlockeradRäkning  |  Regeldistribution av blockerade begäranden för webbprogram  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  BlockeradReqCount  |  Antal blockerade begäranden om webbprogram  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  ByteKom med  |  Mottagna byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  BytesSent  |  Skickade byte  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.Network/applicationGateways  |  KapacitetEnheter  |  Enheter för strömkapacitet  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Network/applicationGateways  |  ClientRtt (klientrt)  |  RTT för klient  |  Millisekunder  |  Medel | 
| Inga  | Inga |  Microsoft.Network/applicationGateways  |  ComputeUnits (beräkningsenheter)  |  Aktuella beräkningsenheter  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Aktuella anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  Misslyckade begärande  |  Misslyckade begäranden  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  FriskaHostCount  |  Friska värden räknas  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Regeldistribution av brandväggssumtal för webbprogram  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  Svarsstatus  |  Svarsstatus  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Network/applicationGateways  |  Dataflöde  |  Dataflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  TlsProtocol (TlsProtocol)  |  TLS-protokoll för klient  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Totalt antal förfrågningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/applicationGateways  |  OhälsosammaHostCount  |  Felfritt antal värden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/azurefirewalls  |  AnsökanRuleHit  |  Antal programregler  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/azurefirewalls  |  Databehandlade  |  Bearbetade data  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/azurefirewalls  |  BrandväggHälsa  |  Hälsotillstånd för brandvägg  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/azurefirewalls  |  NätverkRuleHit  |  Antal nätverksregler  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization (SNATPortUtilization)  |  SNAT-portutnyttjande  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/anslutningar  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/anslutningar  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/dnszones  |  FrågaVolym  |  Fråga volym  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Kapacitetsutnyttjande för postuppsättning  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/dnszones  |  RecordSetCount (RecordSetCount)  |  Antal postuppsättningar  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  Arp Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/expressRouteCircuits  |  BgpTillgänglighet  |  Bgp-tillgänglighet  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.Network/expressRouteGateways  |  ErGatewayAnslutningBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| Inga  | Inga |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/expressRoutePorts  |  AdminState (administrativa)  |  AdminState (administrativa)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/expressRoutePorts  |  LineProtocol (LinjeProtokol)  |  LineProtocol (LinjeProtokol)  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Hälsoprocent för backend  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Antal begäranden efter rygg  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Svarstid för begäran om bakåtsträvning  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  FakturerasResponseSize  |  Fakturerbar svarsstorlek  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  Begäranrring  |  Antal begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  Begäransstorlek  |  Storlek för begäran  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  SvarStorlek  |  Responsstorlek  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Total latens  |  Millisekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Antal begäranden om brandvägg för webbprogram  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Network/loadBalancers  |  AllokeradeSnatPorts  |  Allokerade SNAT-portar (förhandsversion)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/loadBalancers  |  ByteCount  |  Antal byte  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/loadBalancers  |  Diptillgänglighet  |  Status för hälsoavsökning  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/loadBalancers  |  PacketCount (Antal)  |  Antal paket  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Antal SNAT-anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/loadBalancers  |  SYNCount (olikartade)  |  SYN Räkna  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Används SNAT-portar (förhandsversion)  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Tillgänglighet för datasökväg  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/networkInterfaces  |  ByteReceivedRate  |  Mottagna byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Skickade byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/networkInterfaces  |  PaketReceivedRate  |  Mottagna paket  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Skickade paket  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Genomsnittlig tid tur och retur (ms)  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/networkWatchers/connectionMonitors  |  KontrollerFailedPercent  |  Kontroller misslyckades procent (förhandsgranskning)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/networkWatchers/connectionMonitors  |  SonderFailedPercent  |  % avsökningar misslyckades  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeM  |  Tid för tur och retur (ms) (förhandsgranskning)  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  ByteCount  |  Antal byte  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  BytesDroppedDDoS  |  Inkommande byte tappade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  BytesForwardedDDoS  |  Inkommande byte vidarebefordrade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  BytesInDDoS  |  Inkommande byte DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  DDoSTriggerSYNPackets  |  Inkommande SYN-paket för att utlösa DDoS-begränsning  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  DDoSTriggerTCPPackets  |  Inkommande TCP-paket för att utlösa DDoS-begränsning  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  DDoSTriggerUDPPackets  |  Inkommande UDP-paket för att utlösa DDoS-begränsning  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  IfUnderDDosAttack  |  Under DDoS-attack eller inte  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  PacketCount (Antal)  |  Antal paket  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  PacketsDroppedDDoS  |  Inkommande paket tappade DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  PaketForwardadeDDoS  |  Inkommande paket vidarebefordrade DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  PacketsInDDoS  |  Inkommande paket DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  SynCount (olikartade)  |  SYN Räkna  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  TCPBytesDroppedDDoS  |  Inkommande TCP-byte tappade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  TCPBytesForwardedDDoS  |  Inkommande TCP-byte vidarebefordrade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  TCPBytesInDDoS  |  Inkommande TCP-byte DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  TCPPacketsDroppedDDos  |  Inkommande TCP-paket tappade DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  TCPPacketsForwardedDDoS  |  Inkommande TCP-paket vidarebefordrade DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  TCPPacketsInDDoS  |  Inkommande TCP-paket DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  UDPBytesDroppedDDoS  |  Inkommande UDP-byte tappade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  UDPBytesForwardedDDoS  |  Inkommande UDP-byte vidarebefordrade DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  UDPBytesInDDoS  |  Inkommande UDP-byte DDoS  |  BytesPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  UDPPacketsDroppedDDos  |  Inkommande UDP-paket tappade DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  UDPPacketsForwardedDDoS  |  Inkommande UDP-paket vidarebefordrade DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  UDPPacketsInDDoS  |  Inkommande UDP-paket DDoS  |  CountPerSecond  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/publicIPAdresser  |  VipAvailability  |  Tillgänglighet för datasökväg  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Slutpunktsstatus efter slutpunkt  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Frågor efter slutpunkt returneras  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  GenomsnittligtBredd  |  Bandbredd för Gateway S2S  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  P2SBandbredd  |  Gateway P2S-bandbredd  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Antal P2S-anslutningar  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandbredd  |  Tunnel bandbredd  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Tunnel utgående byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDroptSMismatch  |  Tunnel utgående TS matchar inte paketsläpp  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Tunnel utgående paket  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Tunnel inträngning byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDroptSMismatch  |  Tunnel ingående TS matchar inte paketsläpp  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Tunnel inträngningspaket  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Tur och retur för Ping till en virtuell dator  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Misslyckade pingar till en virtuell dator  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Inkommande  |  Inkommande meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.failedrequests  |  Alla inkommande misslyckade begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  Alla inkommande begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  Skickade push-meddelanden för schemalagda push-meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  Schemalagda push-meddelanden har avbrutits  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.alla  |  Åtgärder för installationshantering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Ta bort installationsåtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Hämta installationsåtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  Korrigeringsinstallationsåtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  Skapa eller uppdatera installationsåtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  anmälanhub.pushes  |  Alla utgående meddelanden  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Felaktiga eller utgångna kanalfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Kanalfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Fel i nyttolasten  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Fel i externt meddelandesystem  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Lyckade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  APNS fel på dålig kanal  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  APNS utgångna kanalfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  APNS-auktoriseringsfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnoteificationsize  |  Fel i APNS ogiltiga meddelandestorlek  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  APNS-fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  GODKÄNDA APNS-meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  utgående.gcm.authenticationerror  |  GCM-autentiseringsfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  GCM-fel på dålig kanal  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  GCM-utgångna kanalfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Ogiltigt meddelandeformat för GCM  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidanmälingsstorlek  |  Fel i GCM-ogiltig meddelandestorlek  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  GCM-fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  Lyckade GCM-meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  GCM begränsade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  Fel kanalfel i GCM  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  MPNS-autentiseringsfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  FEL PÅ MPNS-fel i dålig kanal  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  MPNS-kanal frånkopplad  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  MPNS-ignorerade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  Ogiltiga MPNS-autentiseringsuppgifter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Villkor för ogiltigt meddelande om MPNS  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  MPNS-fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  MPNS lyckade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  MPNS-begränsade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  WNS-autentiseringsfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  WNS fel i dålig kanal  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  WNS-kanal frånkopplad  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  WNS-kanal strypt  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  WNS-ignorerade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  WNS-utgångna kanalfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Ogiltigt meddelandeformat för WNS  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnoteificationsize  |  Fel i WNS-ogiltiga meddelandestorlek  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  WNS-auktoriseringsfel (ogiltig token)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  WNS-fel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  WNS lyckade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  WNS-begränsade meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  WNS-auktoriseringsfel (kan inte nås)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  WNS-auktoriseringsfel (fel token)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registrering.alla  |  Registreringsåtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Skapa åtgärder för registrering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete (registrera.ta bort)  |  Borttagning av registreringsåtgärder  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Läsa verksamhet för registrering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update (på/på)44  |  Uppdatering av registrering  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  Väntande schemalagda meddelanden  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% tillgängligt minne  |  % tillgängligt minne  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% tillgängligt växlingsutrymme  |  % tillgängligt växlingsutrymme  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% genomförda byte som används  |  % genomförda byte som används  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% DPC-tid  |  % DPC-tid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% Gratis Inodes  |  % gratis inoder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% ledigt utrymme  |  % ledigt utrymme  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% ledigt utrymme  |  % ledigt utrymme  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% inaktiv tid  |  Inaktivitetstid i procent  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% avbrottstid  |  % avbrottstid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% IO väntetid  |  % väntetid i i/o  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% Trevlig tid  |  % trevlig tid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% privilegierad tid  |  % privilegierad tid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% processortid  |  % processortid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% processortid  |  % processortid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% Begagnade Inoder  |  % använda inoder  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% använt minne  |  % använt minne  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% använt utrymme  |  Använt utrymme i %  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% använt växlingsutrymme  |  % använt växlingsutrymme  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_% användartid  |  % användartid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Available MBytes  |  Tillgängliga MBytes  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Available MBytes-minne  |  Tillgängligt MBytes-minne  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Available MBytes Swap  |  Tillgänglig MBytes Swap  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Avg. Disk s/läsning  |  Genomsnittlig disk sek/läs  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Avg. Disk s/läsning  |  Genomsnittlig disk sek/läs  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Avg. Disk sek/överföring  |  Genomsnittlig disk sek/överföring  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Avg. Disk s/skrivning  |  Genomsnittlig disk sek/skrivning  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Avg. Disk s/skrivning  |  Genomsnittlig disk sek/skrivning  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Bytes mottaget/sek  |  Mottagna byte per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Bytes skickat/sek  |  Skickade byte per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Bytes Totalt/sek  |  Totalt antal byte per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  längden Average_Current diskkö  |  Aktuell diskkölängd  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Disk Läs byte/sek  |  Diskläs byte per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Disk Läser/sek  |  Diskläsningar/sek  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Disk Läser/sek  |  Diskläsningar/sek  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Disk Överföringar/sek  |  Disköverföringar per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Disk Överföringar/sek  |  Disköverföringar per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Disk skrivbyten/sek  |  Byte för diskskrivning/sek  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Disk skriver/sek  |  Diskskrivningar per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Disk skriver/sek  |  Diskskrivningar per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Free Megabyte  |  Gratis Megabyte  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Free Megabyte  |  Gratis Megabyte  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Free fysiskt minne  |  Gratis fysiskt minne  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Free utrymme i växlingsfiler  |  Ledigt utrymme i växlingsfiler  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Free virtuellt minne  |  Gratis virtuellt minne  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Logical diskbyten/sek  |  Logiska diskbyten per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Page Läser/sek  |  Sidan läser/sek  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Page skriver/sek  |  Sidan skriver per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Pages/sek  |  Sidor per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Pct privilegierad tid  |  Pct privilegierad tid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Pct användartid  |  Pct-användartid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Physical diskbyten/sek  |  Fysiska diskbyten per sekund  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Processes  |  Processer  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  kölängd Average_Processor  |  Längden på processorkön  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Size lagras i växlingsfiler  |  Storlek lagras i växlingsfiler  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Total byte  |  Totalt antal byte  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Total mottagna byte  |  Totalt antal mottagna byte  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Total överförda byte  |  Totalt antal överförda byte  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Total kollisioner  |  Totalt kollisioner  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Total mottagna paket  |  Totalt antal mottagna paket  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Total överförda paket  |  Totalt antal överförda paket  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Total Rx-fel  |  Totalt Rx-fel  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Total Tx-fel  |  Totalt antal Tx-fel  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Uptime  |  Upptid  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Used MBytes byta utrymme  |  Använt MBytes-växlingsutrymme  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Used-minne kBytes  |  Använt minne kBytes  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Used-minne MBytes  |  Använt minne MBytes  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Users  |  Användare  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Average_Virtual delat minne  |  Virtuellt delat minne  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Händelse  |  Händelse  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.OperationalInsights/arbetsytor  |  Pulsslag  |  Pulsslag  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.OperationalInsights/arbetsytor  |  Uppdatering  |  Uppdatering  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.PowerBIDedicated/capacity  |  memory_metric  |  Minne  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.PowerBIDedicated/capacity  |  memory_thrashing_metric  |  Minne Stryk (Dataset)  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.PowerBIDedicated/capacity  |  qpu_high_utilization_metric  |  Hög användning av QPU  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.PowerBIDedicated/capacity  |  FrågaUnderation  |  Frågevaraktighet (datauppsättningar)  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.PowerBIDedicated/capacity  |  FrågaPoolJobQueueLength  |  Kölängd för frågepooljobb (datauppsättningar)  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  ActiveConnections (Aktiva Anslutning)  |  ActiveConnections (Aktiva Anslutning)  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  ActiveListeners  |  ActiveListeners  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Relay/namnområden  |  ByteÖverläst  |  ByteÖverläst  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  ListenerConnections-Framgång  |  ListenerConnections-Framgång  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  LyssnarenAnslutningar-TotaltRequests  |  LyssnarenAnslutningar-TotaltRequests  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  LyssnareAnsluter  |  LyssnareAnsluter  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  SenderConnections-Framgång  |  SenderConnections-Framgång  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Relay/namnområden  |  SenderDisconnects  |  SenderDisconnects  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Search/searchServices  |  SökLatens  |  Sök svarstid  |  Sekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Search/searchServices  |  SökQueriesPerSecond  |  Sökfrågor per sekund  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Procent av begränsade sökfrågor  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  ActiveConnections (Aktiva Anslutning)  |  ActiveConnections (Aktiva Anslutning)  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  ActiveMessages  |  Antal aktiva meddelanden i en kö/ett ämne.  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  Anslutningar Slutna  |  Anslutningar stängda.  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  AnslutningarÖppnade  |  Anslutningar öppnade.  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  CPUXNS (PÅ)  |  CPU (föråldrad)  |  Procent  |  Maximal | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  DeadletteredMessages  |  Antal meddelanden med obeställda meddelanden i en kö/ett ämne.  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namnområden  |  IncomingMessages  |  Inkommande meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namnområden  |  Inkommande Begäran  |  Inkommande begäranden  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  Meddelanden  |  Antal meddelanden i en kö/ett ämne.  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  NamnrymdCpuUsage  |  Processor  |  Procent  |  Maximal | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  NamnrymdMemoryUsage  |  Minnesanvändning  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namnområden  |  UtgåendeMessages  |  Utgående meddelanden  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  SchemalagdaMessages  |  Antal schemalagda meddelanden i en kö/ett ämne.  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  ServerErrors  |  Serverfel.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  Storlek  |  Storlek  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  Lyckade Begärande  |  Lyckade förfrågningar  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  ThrottledRequests  |  Begränsade begäranden.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  UserErrors  |  Användarfel.  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.ServiceBus/namnområden  |  WSXNS (WSXNS)  |  Minnesanvändning (föråldrad)  |  Procent  |  Maximal | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  ActualCpu (1990)  |  ActualCpu (1990)  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  FaktiskaMinne  |  FaktiskaMinne  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  AllocatedCpu  |  AllocatedCpu  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  Allokerad minne  |  Allokerad minne  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  ProgramStatus  |  ProgramStatus  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  BehållareStatus  |  BehållareStatus  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  CpuUtilization (CpuUtilization)  |  CpuUtilization (CpuUtilization)  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  Minnesututbyggning  |  Minnesututbyggning  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  RestartCount(RestartCount)  |  RestartCount(RestartCount)  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.ServiceFabricMesh/-program  |  Tjänststatus  |  Tjänststatus  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  Anslutningskonto  |  Antal anslutningar  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  InkommandeTraffic  |  Inkommande trafik  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  MeddelandeCount  |  Antal meddelanden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  UtgåendeTraffic  |  Utgående trafik  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Systemfel  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  Användarfel  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Genomsnittlig CPU-procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  IO byte läsa  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  IO byte skrivna  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/managedInstances  |  io_requests  |  Antal IO-begäranden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Reserverat lagringsutrymme  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Lagringsutrymme som används  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Antal virtuella kärnor  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar  |  database_dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar  |  database_storage_used  |  Datautrymme som används  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar  |  dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar  |  dtu_used  |  DTU används  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar  |  storage_used  |  Datautrymme som används  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  allocated_data_storage  |  Tilldelat datautrymme  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  app_cpu_billed  |  App-processor faktureras  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  app_cpu_percent  |  Procent av appens PROCESSOR  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  app_memory_percent  |  Procentandel av appens minne  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  blocked_by_firewall  |  Blockerad av brandvägg  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  cache_hit_percent  |  Antal träffar i cacheminne  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  cache_used_percent  |  Använd procent av cache  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  connection_failed  |  Misslyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  connection_successful  |  Lyckade anslutningar  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  cpu_limit  |  CPU-gräns  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  cpu_used  |  PROCESSOR som används  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  Dödläge  |  Dödlägen  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  dtu_limit  |  DTU-gräns  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  dtu_used  |  DTU används  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  dwu_consumption_percent  |  DWU-procent  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  dwu_limit  |  DWU-gräns  |  Antal  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  dwu_used  |  DWU används  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  local_tempdb_usage_percent  |  Lokal tempdb-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  log_write_percent  |  Log IO-procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  memory_usage_percent  |  Minnesprocent  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  physical_data_read_percent  |  Data IO-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  sessions_percent  |  Procent av sessioner  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  storage  |  Datautrymme som används  |  Byte  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  storage_percent  |  Datautrymme som används procent  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  tempdb_data_size  |  Tempdb-datafilstorlek Kilobyte  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  tempdb_log_size  |  Tempdb-loggfilstorlek Kilobyte  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/databaser  |  tempdb_log_used_percent  |  Tempdb procent logg används  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  workers_percent  |  Procentandel av arbetstagarna  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/databaser  |  xtp_storage_percent  |  OLTP-lagringsprocent i minnet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  allocated_data_storage  |  Tilldelat datautrymme  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  allocated_data_storage_percent  |  Tilldelat datautrymme i procent  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  cpu_limit  |  CPU-gräns  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/elasticPools  |  cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  cpu_used  |  PROCESSOR som används  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_allocated_data_storage  |  Tilldelat datautrymme  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_cpu_limit  |  CPU-gräns  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_cpu_percent  |  CPU-procent  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_cpu_used  |  PROCESSOR som används  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_eDTU_used  |  eDTU används  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_log_write_percent  |  Log IO-procent  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_physical_data_read_percent  |  Data IO-procent  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_sessions_percent  |  Procent av sessioner  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_storage_used  |  Datautrymme som används  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.Sql/servrar/elasticPools  |  database_workers_percent  |  Procentandel av arbetstagarna  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/elasticPools  |  dtu_consumption_percent  |  DTU-procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  eDTU_limit  |  eDTU-gräns  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/elasticPools  |  eDTU_used  |  eDTU används  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/elasticPools  |  log_write_percent  |  Log IO-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/elasticPools  |  physical_data_read_percent  |  Data IO-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/elasticPools  |  sessions_percent  |  Procent av sessioner  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  storage_limit  |  Data max storlek  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  storage_percent  |  Datautrymme som används procent  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  storage_used  |  Datautrymme som används  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  tempdb_data_size  |  Tempdb-datafilstorlek Kilobyte  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  tempdb_log_size  |  Tempdb-loggfilstorlek Kilobyte  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.Sql/servrar/elasticPools  |  tempdb_log_used_percent  |  Tempdb procent logg används  |  Procent  |  Maximal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/elasticPools  |  workers_percent  |  Procentandel av arbetstagarna  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Sql/servrar/elasticPools  |  xtp_storage_percent  |  OLTP-lagringsprocent i minnet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Begagnad kapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Blob kapacitet  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount (BlobCount)  |  Antal blob  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount (ContainerCount)  |  Antal blob-behållare  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  IndexKapacitet  |  Indexkapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/blobServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  Filkapacitet  |  Filkapacitet  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  FilCount  |  Antal filer  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  Filsharecount  |  Antal filresurser  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  ArkivDelaQuota  |  Kvotstorlek för filresurs  |  Byte  |  Medel | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Antal ögonblicksbilder av filresurs  |  Antal  |  Medel | 
| Inga  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Ögonblicksbildstorlek för filresurs  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/fileServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/queueServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/queueServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/queueServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/queueServices  |  Kökapacitet  |  Kökapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount (QueueCount)  |  Antal köer  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Antal kömeddelanden  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/queueServices  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/queueServices  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/queueServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/tableServices  |  Tillgänglighet  |  Tillgänglighet  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/tableServices  |  Utgående  |  Utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/tableServices  |  Ingress  |  Ingress  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/tableServices  |  FramgångE2ELatency  |  Lyckad E2E-svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/tableServices  |  Lycka Till Svar  |  Lyckad serversvarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/tableServices  |  TabellKapacitet  |  Tabellkapacitet  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/tableServices  |  TabellCount  |  Antal bord  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Antal entiteter för tabell  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.Storage/storageAccounts/tableServices  |  Transaktioner  |  Transaktioner  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  ClientIOPS (Klientiops)  |  Totalt antal IOPS för klient  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  Klientlatens  |  Genomsnittlig klientfördröjning  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  ClientLockIOPS  |  IOPS för klientlås  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  ClientMetadataLästos  |  Klientmetadata läsa IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  ClientMetadataWriteIOPS  |  IOPS för klientmetadata  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  Klientreadiops  |  Klienten läser IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  ClientReadThroughput  |  Genomsnittligt dataflöde för cacheavläsning  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  ClientWriteIOPS  |  IOPS för klientskrivning  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  ClientWriteDetgenomput  |  Genomsnittligt cache-skrivflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  StorageTargetAsyncWriteGenomströmning  |  Lagringsmål asynkron skrivdataflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  LagringsmålFyllUtflöde  |  Fill-dataflöde för lagringsmål  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  LagringTargetHälsa  |  Hälsa för lagringsmål  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  LagringTargetIOPS  |  Totalt antal lagringsutrymmetar iops  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  LagringsmålUppriktat  |  Lagringsriktning svarstid  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  StorageTargetMetadataLästos  |  StorageTarget Metadata Läs IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  StorageTargetMetadataWriteIOPS  |  StorageTarget Metadata Skriv IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  StorageTargetReadAheadGenomströmning  |  StorageTarget Läs framåt-dataflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  LagringsmålläsIOPS  |  StorageTarget Läsa IOPS  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  StorageTargetSyncWriteGenomströmning  |  Synkroniseringsriktigt synkront skrivflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  LagringTargetTotalLäs-Tredykningstal  |  Lagringsrikt totalt läsflöde  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  LagringTargetTotalWriteGenomströmning  |  Totalt skrivflöde för storagetarget  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  LagringTargetWriteIOPS  |  StorageTarget Skriva IOPS  |  Antal  |  Medel | 
| **Ja**  | Inga |  Microsoft.StorageCache/cacheminnen  |  Upptid  |  Upptid  |  Antal  |  Medel | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResultat  |  Resultat för synkronisering av sessions  |  Antal  |  Medel | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Synkroniserade byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Återkallar storlek för molnnivå efter program  |  Byte  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Återkallad storlek för molnnivå  |  Byte  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Återkalla molnnivådelning  |  Byte  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Dataflöde för återkallar molnnivåer  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices  |  LagringssynkroniseringsserverHeartbeat  |  Server onlinestatus  |  Antal  |  Maximal | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices  |  StorageSyncSessionAppliedFilesCount  |  Synkroniserade filer  |  Antal  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Filer som inte synkroniseras  |  Antal  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat (på ett sätt som är)  |  Server onlinestatus  |  Antal  |  Maximal | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Återkalla molnnivådelning  |  Byte  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices/syncGroups microsoft.storagesync/storageSyncServices/syncGroups microsoft.storagesync/storageSyncServices/syncGroups microsoft.  |  SyncGroupBatchTransferredFileBytes  |  Synkroniserade byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices/syncGroups microsoft.storagesync/storageSyncServices/syncGroups microsoft.storagesync/storageSyncServices/syncGroups microsoft.  |  SyncGroupSyncSessionAppliedFilesCount  |  Synkroniserade filer  |  Antal  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices/syncGroups microsoft.storagesync/storageSyncServices/syncGroups microsoft.storagesync/storageSyncServices/syncGroups microsoft.  |  SyncGroupSyncSessionPerItemErrorsCount  |  Filer som inte synkroniseras  |  Antal  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Synkroniserade byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Synkroniserade filer  |  Antal  |  Totalt | 
| **Ja**  | Inga |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Filer som inte synkroniseras  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Misslyckade funktionsbegäranden  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Funktionshändelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Funktionsbegäranden  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Datakonverteringsfel  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Fel vid indataserialisering  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Händelser som inte är i funktion  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Tidiga inmatningshändelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  Fel  |  Körningsfel  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Indatahändelsebyten  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Indatahändelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Eftersläpade indatahändelser  |  Antal  |  Maximal | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Mottagna indatakällor  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  Sena indatahändelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Utdatahändelser  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Fördröjning av vattenstämpel  |  Sekunder  |  Maximal | 
| **Ja**  | Inga |  Microsoft.StreamAnalytics/streamingjobs  |  ResursUtnyttjande  |  SU% utnyttjande  |  Procent  |  Maximal | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  Diskläs byte  |  Diskläs byte  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  Diskläsningsåtgärder/sek  |  Diskläsningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  Byte av diskskrivning  |  Byte av diskskrivning  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  Diskskrivningsåtgärder/sek  |  Diskskrivningsåtgärder/sek  |  CountPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Diskläs byte/sek  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskLäslatens  |  Svarstid för diskläsning  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Diskläsningsåtgärder  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Byte för diskskrivning/sek  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Svarstid för diskskrivning  |  Millisekunder  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Skrivåtgärder för disk  |  Antal  |  Totalt | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Minne aktivt  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  MinneGranterad  |  Minne beviljat  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  Minnesupptredt  |  Minne som används  |  Byte  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  Nätverk – inkommande  |  Nätverk – inkommande  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  Nätverk – utgående  |  Nätverk – utgående  |  Byte  |  Totalt | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  NätverkInBytesPerSecond  |  Nätverk i byte/sek  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Nätverksbyten/sek  |  BytesPerSecond  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  Procent CPU  |  Procent CPU  |  Procent  |  Medel | 
| **Ja**  | Inga |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Procent CPU klar  |  Millisekunder  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  ActiveRequests  |  Aktiva begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Genomsnittligtidstid  |  Genomsnittlig svarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  ByteKom med  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  BytesSent  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  CpuPercentage (processorpercentage)  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  DiskQueueLength  |  Längd på diskkö  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Http101 (på ett sätt)  |  Http 101  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Http2xx (på andra sätt)  |  Http 2xx (på andra sätt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Http3xx (på andra sätt)  |  Http 3xx (på)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Http401 (på ett sätt)  |  Http 401  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Http403 (på)  |  Http 403  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Http404 (på)  |  Http 404  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Http406 (på andra)  |  Http 406  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Http4xx (på sätt och vis)  |  Http 4xx (på)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Http5xx (på sätt och vis)  |  Http-serverfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Mer från HttpQueueLength  |  Http kö längd  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  LargeAppServicePlanInstances  |  Arbetare för stora apptjänstplan  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  MediumAppServicePlanera  |  Arbetare för medelstora apptjänstabonnemang  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  MinnePercentage  |  Minnesprocent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  SmallAppServicePlanInstances  |  Arbetare för serviceplan för små app  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/multiRolePools  |  Totalt Antal direkten  |  Totalt antal främre ändar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/workerPools  |  CpuPercentage (processorpercentage)  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/workerPools  |  MinnePercentage  |  Minnesprocent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/workerPools  |  ArbetareTillgängligt  |  Tillgängliga arbetstagare  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/workerPools  |  ArbetareTotal  |  Totalt antal arbetstagare  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingMiljöer/workerPools  |  ArbetareUsed  |  Begagnade arbetare  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  ByteKom med  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  BytesSent  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  CpuPercentage (processorpercentage)  |  CPU-procent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Längd på diskkö  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  Mer från HttpQueueLength  |  Http kö längd  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  MinnePercentage  |  Minnesprocent  |  Procent  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpCloseWait (på)  |  TCP Stäng Vänta  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpClosing (TcpClosing)  |  TCP Stängning  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpEtableed  |  TCP etablerad  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP Fin Vänta 1  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP Fin Vänta 2  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpLastAck (en)  |  TCP Sista Ack  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  TCP Syn mottagen  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpSynSent (  |  TCP-syn skickad  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpTimeWait (av TcpTimeWait)  |  TCP-tid vänta  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  AppAnslutningar  |  Anslutningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  AverageMemoryWorkingSet  |  Genomsnittligt arbetsminne  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Genomsnittligtidstid  |  Genomsnittlig svarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  ByteKom med  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  BytesSent  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  CpuTime (på andra sätt)  |  CPU-tid  |  Sekunder  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Aktuella sammansättningar  |  Aktuella sammansättningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  FunctionExecutionCount  |  Antal funktionskörningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  FunktionUtställningAr  |  Funktionskörningsenheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Gen0Kolleringar  |  Gen 0 Sophämtningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Gen1Kolleringar  |  Gen 1 Sophämtningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Gen2Kolleringar  |  Gen 2 Sophämtningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Hanterar  |  Antal referenser  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Hälsokontrollerstatus  |  Status för hälsokontroll  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Http101 (på ett sätt)  |  Http 101  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Http2xx (på andra sätt)  |  Http 2xx (på andra sätt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Http3xx (på andra sätt)  |  Http 3xx (på)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Http401 (på ett sätt)  |  Http 401  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Http403 (på)  |  Http 403  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Http404 (på)  |  Http 404  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Http406 (på andra)  |  Http 406  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Http4xx (på sätt och vis)  |  Http 4xx (på)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Http5xx (på sätt och vis)  |  Http-serverfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  HttpResponseTime (På annat sätt)  |  Svarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  IotherBytesPerSecond  |  IO Andra byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  IotherOperationsPerSecond  |  IO Övriga transaktioner per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  IoReadBytesPerSecond  |  IO läs byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  IoReadOperationsPerSecond  |  IO läsa operationer per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  IoWriteBytesPerSecond  |  IO skriv byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  IoWriteOperationsPerSecond  |  IO-skrivåtgärder per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  MemoryWorkingSet  |  Arbetsminne  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  PrivateBytes (PrivateBytes)  |  Privata byte  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  BegärandenInApplicationQueue  |  Begäranden i programkö  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Trådar  |  Antal trådar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  Totalt Antal Händer  |  Totalt antal appdomäner  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.Webbplatser  |  TotaltTillämtsTilldelning  |  Totalt antal appdomäner har tagits bort  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  AppAnslutningar  |  Anslutningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  AverageMemoryWorkingSet  |  Genomsnittligt arbetsminne  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Genomsnittligtidstid  |  Genomsnittlig svarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  ByteKom med  |  Data i  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  BytesSent  |  Data ut  |  Byte  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  CpuTime (på andra sätt)  |  CPU-tid  |  Sekunder  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Aktuella sammansättningar  |  Aktuella sammansättningar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  FunctionExecutionCount  |  Antal funktionskörningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  FunktionUtställningAr  |  Funktionskörningsenheter  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Gen0Kolleringar  |  Gen 0 Sophämtningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Gen1Kolleringar  |  Gen 1 Sophämtningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Gen2Kolleringar  |  Gen 2 Sophämtningar  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Hanterar  |  Antal referenser  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Hälsokontrollerstatus  |  Status för hälsokontroll  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Http101 (på ett sätt)  |  Http 101  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Http2xx (på andra sätt)  |  Http 2xx (på andra sätt)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Http3xx (på andra sätt)  |  Http 3xx (på)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Http401 (på ett sätt)  |  Http 401  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Http403 (på)  |  Http 403  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Http404 (på)  |  Http 404  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Http406 (på andra)  |  Http 406  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Http4xx (på sätt och vis)  |  Http 4xx (på)  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Http5xx (på sätt och vis)  |  Http-serverfel  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  HttpResponseTime (På annat sätt)  |  Svarstid  |  Sekunder  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  IotherBytesPerSecond  |  IO Andra byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  IotherOperationsPerSecond  |  IO Övriga transaktioner per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  IoReadBytesPerSecond  |  IO läs byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  IoReadOperationsPerSecond  |  IO läsa operationer per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  IoWriteBytesPerSecond  |  IO skriv byte per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  IoWriteOperationsPerSecond  |  IO-skrivåtgärder per sekund  |  BytesPerSecond  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  MemoryWorkingSet  |  Arbetsminne  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  PrivateBytes (PrivateBytes)  |  Privata byte  |  Byte  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Begäranden  |  Begäranden  |  Antal  |  Totalt | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  BegärandenInApplicationQueue  |  Begäranden i programkö  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Trådar  |  Antal trådar  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  Totalt Antal Händer  |  Totalt antal appdomäner  |  Antal  |  Medel | 
| **Ja**  | **Ja** |  Microsoft.webbplatser/platser  |  TotaltTillämtsTilldelning  |  Totalt antal appdomäner har tagits bort  |  Antal  |  Medel | 
