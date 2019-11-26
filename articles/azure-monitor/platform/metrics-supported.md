---
title: Azure Monitor supported metrics by resource type
description: List of metrics available for each resource type with Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 30e2b880f32f896098778942deb67d7ced9f5c2d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484220"
---
# <a name="supported-metrics-with-azure-monitor"></a>Supported metrics with Azure Monitor

Azure Monitor provides several ways to interact with metrics, including charting them in the portal, accessing them through the REST API, or querying them using PowerShell or CLI. Below is a complete list of all metrics currently available with Azure Monitor's metric pipeline. Other metrics may be available in the portal or using legacy APIs. This list below only includes metrics available using the consolidated Azure Monitor metric pipeline. To query for and access these metrics please use the [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|qpu_metric|QPU|Antal|Medel|QPU. Range 0-100 for S1, 0-200 for S2 and 0-400 for S4|ServerResourceType|
|memory_metric|Minne|Bytes|Medel|Memory. Range 0-25 GB for S1, 0-50 GB for S2 and 0-100 GB for S4|ServerResourceType|
|private_bytes_metric|Private Bytes |Bytes|Medel|The total amount of memory the Analysis Services engine process and Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes |Bytes|Medel|The current size of the virtual address space that Analysis Services engine process and Mashup container processes are using.|ServerResourceType|
|TotalConnectionRequests|Total Connection Requests|Antal|Medel|Total connection requests. These are arrivals.|ServerResourceType|
|SuccessfullConnectionsPerSec|Successful Connections Per Sec|CountPerSecond|Medel|Rate of successful connection completions.|ServerResourceType|
|TotalConnectionFailures|Total Connection Failures|Antal|Medel|Total failed connection attempts.|ServerResourceType|
|CurrentUserSessions|Current User Sessions|Antal|Medel|Current number of user sessions established.|ServerResourceType|
|QueryPoolBusyThreads|Query Pool Busy Threads|Antal|Medel|Number of busy threads in the query thread pool.|ServerResourceType|
|CommandPoolJobQueueLength|Command Pool Job Queue Length|Antal|Medel|Number of jobs in the queue of the command thread pool.|ServerResourceType|
|ProcessingPoolJobQueueLength|Processing Pool Job Queue Length|Antal|Medel|Number of non-I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|CurrentConnections|Connection: Current connections|Antal|Medel|Current number of client connections established.|ServerResourceType|
|CleanerCurrentPrice|Memory: Cleaner Current Price|Antal|Medel|Current price of memory, $/byte/time, normalized to 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memory: Cleaner Memory shrinkable|Bytes|Medel|Amount of memory, in bytes, subject to purging by the background cleaner.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memory: Cleaner Memory nonshrinkable|Bytes|Medel|Amount of memory, in bytes, not subject to purging by the background cleaner.|ServerResourceType|
|MemoryUsage|Memory: Memory Usage|Bytes|Medel|Memory usage of the server process as used in calculating cleaner memory price. Equal to counter Process\PrivateBytes plus the size of memory-mapped data, ignoring any memory which was mapped or allocated by the xVelocity in-memory analytics engine (VertiPaq) in excess of the xVelocity engine Memory Limit.|ServerResourceType|
|MemoryLimitHard|Memory: Memory Limit Hard|Bytes|Medel|Hard memory limit, from configuration file.|ServerResourceType|
|MemoryLimitHigh|Memory: Memory Limit High|Bytes|Medel|High memory limit, from configuration file.|ServerResourceType|
|MemoryLimitLow|Memory: Memory Limit Low|Bytes|Medel|Low memory limit, from configuration file.|ServerResourceType|
|MemoryLimitVertiPaq|Memory: Memory Limit VertiPaq|Bytes|Medel|In-memory limit, from configuration file.|ServerResourceType|
|Kvot|Memory: Quota|Bytes|Medel|Current memory quota, in bytes. Memory quota is also known as a memory grant or memory reservation.|ServerResourceType|
|QuotaBlocked|Memory: Quota Blocked|Antal|Medel|Current number of quota requests that are blocked until other memory quotas are freed.|ServerResourceType|
|VertiPaqNonpaged|Memory: VertiPaq Nonpaged|Bytes|Medel|Bytes of memory locked in the working set for use by the in-memory engine.|ServerResourceType|
|VertiPaqPaged|Memory: VertiPaq Paged|Bytes|Medel|Bytes of paged memory in use for in-memory data.|ServerResourceType|
|RowsReadPerSec|Processing: Rows read per sec|CountPerSecond|Medel|Rate of rows read from all relational databases.|ServerResourceType|
|RowsConvertedPerSec|Processing: Rows converted per sec|CountPerSecond|Medel|Rate of rows converted during processing.|ServerResourceType|
|RowsWrittenPerSec|Processing: Rows written per sec|CountPerSecond|Medel|Rate of rows written during processing.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Command pool busy threads|Antal|Medel|Number of busy threads in the command thread pool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Command pool idle threads|Antal|Medel|Number of idle threads in the command thread pool.|ServerResourceType|
|LongParsingBusyThreads|Threads: Long parsing busy threads|Antal|Medel|Number of busy threads in the long parsing thread pool.|ServerResourceType|
|LongParsingIdleThreads|Threads: Long parsing idle threads|Antal|Medel|Number of idle threads in the long parsing thread pool.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Long parsing job queue length|Antal|Medel|Number of jobs in the queue of the long parsing thread pool.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Processing pool busy I/O job threads|Antal|Medel|Number of threads running I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Processing pool busy non-I/O threads|Antal|Medel|Number of threads running non-I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Processing pool I/O job queue length|Antal|Medel|Number of I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Processing pool idle I/O job threads|Antal|Medel|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Processing pool idle non-I/O threads|Antal|Medel|Number of idle threads in the processing thread pool dedicated to non-I/O jobs.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Query pool idle threads|Antal|Medel|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Antal|Medel|Number of jobs in the queue of the query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Short parsing busy threads|Antal|Medel|Number of busy threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Short parsing idle threads|Antal|Medel|Number of idle threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Short parsing job queue length|Antal|Medel|Number of jobs in the queue of the short parsing thread pool.|ServerResourceType|
|memory_thrashing_metric|Memory Thrashing|Procent|Medel|Average memory thrashing.|ServerResourceType|
|mashup_engine_qpu_metric|M Engine QPU|Antal|Medel|QPU usage by mashup engine processes|ServerResourceType|
|mashup_engine_memory_metric|M Engine Memory|Bytes|Medel|Memory usage by mashup engine processes|ServerResourceType|
|mashup_engine_private_bytes_metric|M Engine Private Bytes |Bytes|Medel|The total amount of memory Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Engine Virtual Bytes |Bytes|Medel|The current size of the virtual address space Mashup container processes are using.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Antal|Totalt|The total number of gateway requests in a given period. It can be sliced by various dimensions to help you diagnose issues. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Total Gateway Requests|Antal|Totalt|The total number of gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Antal|Totalt|The total number of successful gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|UnauthorizedRequests|Unauthorized Gateway Requests|Antal|Totalt| The total number of unauthorized gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Antal|Totalt|The total number of failed gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|OtherRequests|Other Gateway Requests|Antal|Totalt|The total number of gateway requests in a given period that do not fall into the successful, unauthorized, or failed categories. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|Längd|Overall Duration of Gateway Requests|Milliseconds|Medel|The time between when API Management receives a request from a client and when it returns a response to the client.|Location, Hostname|
|Kapacitet|Kapacitet|Procent|Medel|Indicator of load on an API Management instance for making informed decisions whether to scale the instance to accommodate more load.|Plats|
|EventHubTotalEvents|Total EventHub Events|Antal|Totalt|The total number of events sent to EventHub from API Management in a given period.|Plats|
|EventHubSuccessfulEvents|Successful EventHub Events|Antal|Totalt|The total number of successful EventHub events in a given period.|Plats|
|EventHubTotalFailedEvents|Failed EventHub Events|Antal|Totalt|The total number of failed EventHub events in a given period.|Plats|
|EventHubRejectedEvents|Rejected EventHub Events|Antal|Totalt|The total number of rejected EventHub events (wrong configuration or unauthorized) in a given period.|Plats|
|EventHubThrottledEvents|Throttled EventHub Events|Antal|Totalt|The total number of throttled EventHub events in a given period.|Plats|
|EventHubTimedoutEvents|Timed Out EventHub Events|Antal|Totalt|The total number of timed out EventHub events in a given period.|Plats|
|EventHubDroppedEvents|Dropped EventHub Events|Antal|Totalt|The total number of events skipped because of queue size limit reached in a given period.|Plats|
|EventHubTotalBytesSent|Size of EventHub Events|Bytes|Totalt|The total size of EventHub events in bytes in a given period.|Plats|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalJob|Total Jobs|Antal|Totalt|The total number of jobs|Runbook, Status|
|TotalUpdateDeploymentRuns|Total Update Deployment Runs|Antal|Totalt|Total software update deployment runs|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Total Update Deployment Machine Runs|Antal|Totalt|Total software update deployment machine runs in a software update deployment run|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Antal|Totalt|Total number of dedicated cores in the batch account|No Dimensions|
|TotalNodeCount|Dedicated Node Count|Antal|Totalt|Total number of dedicated nodes in the batch account|No Dimensions|
|LowPriorityCoreCount|LowPriority Core Count|Antal|Totalt|Total number of low-priority cores in the batch account|No Dimensions|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Antal|Totalt|Total number of low-priority nodes in the batch account|No Dimensions|
|CreatingNodeCount|Creating Node Count|Antal|Totalt|Number of nodes being created|No Dimensions|
|StartingNodeCount|Starting Node Count|Antal|Totalt|Number of nodes starting|No Dimensions|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Antal|Totalt|Number of nodes waiting for the Start Task to complete|No Dimensions|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Antal|Totalt|Number of nodes where the Start Task has failed|No Dimensions|
|IdleNodeCount|Idle Node Count|Antal|Totalt|Number of idle nodes|No Dimensions|
|OfflineNodeCount|Offline Node Count|Antal|Totalt|Number of offline nodes|No Dimensions|
|RebootingNodeCount|Rebooting Node Count|Antal|Totalt|Number of rebooting nodes|No Dimensions|
|ReimagingNodeCount|Reimaging Node Count|Antal|Totalt|Number of reimaging nodes|No Dimensions|
|RunningNodeCount|Running Node Count|Antal|Totalt|Number of running nodes|No Dimensions|
|LeavingPoolNodeCount|Leaving Pool Node Count|Antal|Totalt|Number of nodes leaving the Pool|No Dimensions|
|UnusableNodeCount|Unusable Node Count|Antal|Totalt|Number of unusable nodes|No Dimensions|
|PreemptedNodeCount|Preempted Node Count|Antal|Totalt|Number of preempted nodes|No Dimensions|
|TaskStartEvent|Task Start Events|Antal|Totalt|Total number of tasks that have started|No Dimensions|
|TaskCompleteEvent|Task Complete Events|Antal|Totalt|Total number of tasks that have completed|No Dimensions|
|TaskFailEvent|Task Fail Events|Antal|Totalt|Total number of tasks that have completed in a failed state|No Dimensions|
|PoolCreateEvent|Pool Create Events|Antal|Totalt|Total number of pools that have been created|No Dimensions|
|PoolResizeStartEvent|Pool Resize Start Events|Antal|Totalt|Total number of pool resizes that have started|No Dimensions|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Antal|Totalt|Total number of pool resizes that have completed|No Dimensions|
|PoolDeleteStartEvent|Pool Delete Start Events|Antal|Totalt|Total number of pool deletes that have started|No Dimensions|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Antal|Totalt|Total number of pool deletes that have completed|No Dimensions|
|JobDeleteCompleteEvent|Job Delete Complete Events|Antal|Totalt|Total number of jobs that have been successfully deleted.|No Dimensions|
|JobDeleteStartEvent|Job Delete Start Events|Antal|Totalt|Total number of jobs that have been requested to be deleted.|No Dimensions|
|JobDisableCompleteEvent|Job Disable Complete Events|Antal|Totalt|Total number of jobs that have been successfully disabled.|No Dimensions|
|JobDisableStartEvent|Job Disable Start Events|Antal|Totalt|Total number of jobs that have been requested to be disabled.|No Dimensions|
|JobStartEvent|Job Start Events|Antal|Totalt|Total number of jobs that have been successfully started.|No Dimensions|
|JobTerminateCompleteEvent|Job Terminate Complete Events|Antal|Totalt|Total number of jobs that have been successfully terminated.|No Dimensions|
|JobTerminateStartEvent|Job Terminate Start Events|Antal|Totalt|Total number of jobs that have been requested to be terminated.|No Dimensions|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|connectedclients|Connected Clients|Antal|Maximal||ShardId|
|totalcommandsprocessed|Total Operations|Antal|Totalt||ShardId|
|cachehits|Cache Hits|Antal|Totalt||ShardId|
|cachemisses|Cache Misses|Antal|Totalt||ShardId|
|getcommands|Gets|Antal|Totalt||ShardId|
|setcommands|Sets|Antal|Totalt||ShardId|
|operationsPerSecond|Operations Per Second|Antal|Maximal||ShardId|
|evictedkeys|Evicted Keys|Antal|Totalt||ShardId|
|totalkeys|Total Keys|Antal|Maximal||ShardId|
|expiredkeys|Expired Keys|Antal|Totalt||ShardId|
|usedmemory|Used Memory|Bytes|Maximal||ShardId|
|usedmemorypercentage|Used Memory Percentage|Procent|Maximal||ShardId|
|usedmemoryRss|Used Memory RSS|Bytes|Maximal||ShardId|
|serverLoad|Server Load|Procent|Maximal||ShardId|
|cacheWrite|Cache Write|BytesPerSecond|Maximal||ShardId|
|cacheRead|Cache Read|BytesPerSecond|Maximal||ShardId|
|percentProcessorTime|Processor|Procent|Maximal||ShardId|
|cacheLatency|Cache Latency Microseconds (Preview)|Antal|Medel||ShardId, SampleType|
|fel|Fel|Antal|Maximal||ShardId, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed0|Total Operations (Shard 0)|Antal|Totalt||No Dimensions|
|cachehits0|Cache Hits (Shard 0)|Antal|Totalt||No Dimensions|
|cachemisses0|Cache Misses (Shard 0)|Antal|Totalt||No Dimensions|
|getcommands0|Gets (Shard 0)|Antal|Totalt||No Dimensions|
|setcommands0|Sets (Shard 0)|Antal|Totalt||No Dimensions|
|operationsPerSecond0|Operations Per Second (Shard 0)|Antal|Maximal||No Dimensions|
|evictedkeys0|Evicted Keys (Shard 0)|Antal|Totalt||No Dimensions|
|totalkeys0|Total Keys (Shard 0)|Antal|Maximal||No Dimensions|
|expiredkeys0|Expired Keys (Shard 0)|Antal|Totalt||No Dimensions|
|usedmemory0|Used Memory (Shard 0)|Bytes|Maximal||No Dimensions|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Bytes|Maximal||No Dimensions|
|serverLoad0|Server Load (Shard 0)|Procent|Maximal||No Dimensions|
|cacheWrite0|Cache Write (Shard 0)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead0|Cache Read (Shard 0)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime0|CPU (Shard 0)|Procent|Maximal||No Dimensions|
|connectedclients1|Connected Clients (Shard 1)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed1|Total Operations (Shard 1)|Antal|Totalt||No Dimensions|
|cachehits1|Cache Hits (Shard 1)|Antal|Totalt||No Dimensions|
|cachemisses1|Cache Misses (Shard 1)|Antal|Totalt||No Dimensions|
|getcommands1|Gets (Shard 1)|Antal|Totalt||No Dimensions|
|setcommands1|Sets (Shard 1)|Antal|Totalt||No Dimensions|
|operationsPerSecond1|Operations Per Second (Shard 1)|Antal|Maximal||No Dimensions|
|evictedkeys1|Evicted Keys (Shard 1)|Antal|Totalt||No Dimensions|
|totalkeys1|Total Keys (Shard 1)|Antal|Maximal||No Dimensions|
|expiredkeys1|Expired Keys (Shard 1)|Antal|Totalt||No Dimensions|
|usedmemory1|Used Memory (Shard 1)|Bytes|Maximal||No Dimensions|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Bytes|Maximal||No Dimensions|
|serverLoad1|Server Load (Shard 1)|Procent|Maximal||No Dimensions|
|cacheWrite1|Cache Write (Shard 1)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead1|Cache Read (Shard 1)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime1|CPU (Shard 1)|Procent|Maximal||No Dimensions|
|connectedclients2|Connected Clients (Shard 2)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed2|Total Operations (Shard 2)|Antal|Totalt||No Dimensions|
|cachehits2|Cache Hits (Shard 2)|Antal|Totalt||No Dimensions|
|cachemisses2|Cache Misses (Shard 2)|Antal|Totalt||No Dimensions|
|getcommands2|Gets (Shard 2)|Antal|Totalt||No Dimensions|
|setcommands2|Sets (Shard 2)|Antal|Totalt||No Dimensions|
|operationsPerSecond2|Operations Per Second (Shard 2)|Antal|Maximal||No Dimensions|
|evictedkeys2|Evicted Keys (Shard 2)|Antal|Totalt||No Dimensions|
|totalkeys2|Total Keys (Shard 2)|Antal|Maximal||No Dimensions|
|expiredkeys2|Expired Keys (Shard 2)|Antal|Totalt||No Dimensions|
|usedmemory2|Used Memory (Shard 2)|Bytes|Maximal||No Dimensions|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Bytes|Maximal||No Dimensions|
|serverLoad2|Server Load (Shard 2)|Procent|Maximal||No Dimensions|
|cacheWrite2|Cache Write (Shard 2)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead2|Cache Read (Shard 2)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime2|CPU (Shard 2)|Procent|Maximal||No Dimensions|
|connectedclients3|Connected Clients (Shard 3)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed3|Total Operations (Shard 3)|Antal|Totalt||No Dimensions|
|cachehits3|Cache Hits (Shard 3)|Antal|Totalt||No Dimensions|
|cachemisses3|Cache Misses (Shard 3)|Antal|Totalt||No Dimensions|
|getcommands3|Gets (Shard 3)|Antal|Totalt||No Dimensions|
|setcommands3|Sets (Shard 3)|Antal|Totalt||No Dimensions|
|operationsPerSecond3|Operations Per Second (Shard 3)|Antal|Maximal||No Dimensions|
|evictedkeys3|Evicted Keys (Shard 3)|Antal|Totalt||No Dimensions|
|totalkeys3|Total Keys (Shard 3)|Antal|Maximal||No Dimensions|
|expiredkeys3|Expired Keys (Shard 3)|Antal|Totalt||No Dimensions|
|usedmemory3|Used Memory (Shard 3)|Bytes|Maximal||No Dimensions|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Bytes|Maximal||No Dimensions|
|serverLoad3|Server Load (Shard 3)|Procent|Maximal||No Dimensions|
|cacheWrite3|Cache Write (Shard 3)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead3|Cache Read (Shard 3)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime3|CPU (Shard 3)|Procent|Maximal||No Dimensions|
|connectedclients4|Connected Clients (Shard 4)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed4|Total Operations (Shard 4)|Antal|Totalt||No Dimensions|
|cachehits4|Cache Hits (Shard 4)|Antal|Totalt||No Dimensions|
|cachemisses4|Cache Misses (Shard 4)|Antal|Totalt||No Dimensions|
|getcommands4|Gets (Shard 4)|Antal|Totalt||No Dimensions|
|setcommands4|Sets (Shard 4)|Antal|Totalt||No Dimensions|
|operationsPerSecond4|Operations Per Second (Shard 4)|Antal|Maximal||No Dimensions|
|evictedkeys4|Evicted Keys (Shard 4)|Antal|Totalt||No Dimensions|
|totalkeys4|Total Keys (Shard 4)|Antal|Maximal||No Dimensions|
|expiredkeys4|Expired Keys (Shard 4)|Antal|Totalt||No Dimensions|
|usedmemory4|Used Memory (Shard 4)|Bytes|Maximal||No Dimensions|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Bytes|Maximal||No Dimensions|
|serverLoad4|Server Load (Shard 4)|Procent|Maximal||No Dimensions|
|cacheWrite4|Cache Write (Shard 4)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead4|Cache Read (Shard 4)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime4|CPU (Shard 4)|Procent|Maximal||No Dimensions|
|connectedclients5|Connected Clients (Shard 5)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed5|Total Operations (Shard 5)|Antal|Totalt||No Dimensions|
|cachehits5|Cache Hits (Shard 5)|Antal|Totalt||No Dimensions|
|cachemisses5|Cache Misses (Shard 5)|Antal|Totalt||No Dimensions|
|getcommands5|Gets (Shard 5)|Antal|Totalt||No Dimensions|
|setcommands5|Sets (Shard 5)|Antal|Totalt||No Dimensions|
|operationsPerSecond5|Operations Per Second (Shard 5)|Antal|Maximal||No Dimensions|
|evictedkeys5|Evicted Keys (Shard 5)|Antal|Totalt||No Dimensions|
|totalkeys5|Total Keys (Shard 5)|Antal|Maximal||No Dimensions|
|expiredkeys5|Expired Keys (Shard 5)|Antal|Totalt||No Dimensions|
|usedmemory5|Used Memory (Shard 5)|Bytes|Maximal||No Dimensions|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Bytes|Maximal||No Dimensions|
|serverLoad5|Server Load (Shard 5)|Procent|Maximal||No Dimensions|
|cacheWrite5|Cache Write (Shard 5)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead5|Cache Read (Shard 5)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime5|CPU (Shard 5)|Procent|Maximal||No Dimensions|
|connectedclients6|Connected Clients (Shard 6)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed6|Total Operations (Shard 6)|Antal|Totalt||No Dimensions|
|cachehits6|Cache Hits (Shard 6)|Antal|Totalt||No Dimensions|
|cachemisses6|Cache Misses (Shard 6)|Antal|Totalt||No Dimensions|
|getcommands6|Gets (Shard 6)|Antal|Totalt||No Dimensions|
|setcommands6|Sets (Shard 6)|Antal|Totalt||No Dimensions|
|operationsPerSecond6|Operations Per Second (Shard 6)|Antal|Maximal||No Dimensions|
|evictedkeys6|Evicted Keys (Shard 6)|Antal|Totalt||No Dimensions|
|totalkeys6|Total Keys (Shard 6)|Antal|Maximal||No Dimensions|
|expiredkeys6|Expired Keys (Shard 6)|Antal|Totalt||No Dimensions|
|usedmemory6|Used Memory (Shard 6)|Bytes|Maximal||No Dimensions|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Bytes|Maximal||No Dimensions|
|serverLoad6|Server Load (Shard 6)|Procent|Maximal||No Dimensions|
|cacheWrite6|Cache Write (Shard 6)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead6|Cache Read (Shard 6)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime6|CPU (Shard 6)|Procent|Maximal||No Dimensions|
|connectedclients7|Connected Clients (Shard 7)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed7|Total Operations (Shard 7)|Antal|Totalt||No Dimensions|
|cachehits7|Cache Hits (Shard 7)|Antal|Totalt||No Dimensions|
|cachemisses7|Cache Misses (Shard 7)|Antal|Totalt||No Dimensions|
|getcommands7|Gets (Shard 7)|Antal|Totalt||No Dimensions|
|setcommands7|Sets (Shard 7)|Antal|Totalt||No Dimensions|
|operationsPerSecond7|Operations Per Second (Shard 7)|Antal|Maximal||No Dimensions|
|evictedkeys7|Evicted Keys (Shard 7)|Antal|Totalt||No Dimensions|
|totalkeys7|Total Keys (Shard 7)|Antal|Maximal||No Dimensions|
|expiredkeys7|Expired Keys (Shard 7)|Antal|Totalt||No Dimensions|
|usedmemory7|Used Memory (Shard 7)|Bytes|Maximal||No Dimensions|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Bytes|Maximal||No Dimensions|
|serverLoad7|Server Load (Shard 7)|Procent|Maximal||No Dimensions|
|cacheWrite7|Cache Write (Shard 7)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead7|Cache Read (Shard 7)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime7|CPU (Shard 7)|Procent|Maximal||No Dimensions|
|connectedclients8|Connected Clients (Shard 8)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed8|Total Operations (Shard 8)|Antal|Totalt||No Dimensions|
|cachehits8|Cache Hits (Shard 8)|Antal|Totalt||No Dimensions|
|cachemisses8|Cache Misses (Shard 8)|Antal|Totalt||No Dimensions|
|getcommands8|Gets (Shard 8)|Antal|Totalt||No Dimensions|
|setcommands8|Sets (Shard 8)|Antal|Totalt||No Dimensions|
|operationsPerSecond8|Operations Per Second (Shard 8)|Antal|Maximal||No Dimensions|
|evictedkeys8|Evicted Keys (Shard 8)|Antal|Totalt||No Dimensions|
|totalkeys8|Total Keys (Shard 8)|Antal|Maximal||No Dimensions|
|expiredkeys8|Expired Keys (Shard 8)|Antal|Totalt||No Dimensions|
|usedmemory8|Used Memory (Shard 8)|Bytes|Maximal||No Dimensions|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Bytes|Maximal||No Dimensions|
|serverLoad8|Server Load (Shard 8)|Procent|Maximal||No Dimensions|
|cacheWrite8|Cache Write (Shard 8)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead8|Cache Read (Shard 8)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime8|CPU (Shard 8)|Procent|Maximal||No Dimensions|
|connectedclients9|Connected Clients (Shard 9)|Antal|Maximal||No Dimensions|
|totalcommandsprocessed9|Total Operations (Shard 9)|Antal|Totalt||No Dimensions|
|cachehits9|Cache Hits (Shard 9)|Antal|Totalt||No Dimensions|
|cachemisses9|Cache Misses (Shard 9)|Antal|Totalt||No Dimensions|
|getcommands9|Gets (Shard 9)|Antal|Totalt||No Dimensions|
|setcommands9|Sets (Shard 9)|Antal|Totalt||No Dimensions|
|operationsPerSecond9|Operations Per Second (Shard 9)|Antal|Maximal||No Dimensions|
|evictedkeys9|Evicted Keys (Shard 9)|Antal|Totalt||No Dimensions|
|totalkeys9|Total Keys (Shard 9)|Antal|Maximal||No Dimensions|
|expiredkeys9|Expired Keys (Shard 9)|Antal|Totalt||No Dimensions|
|usedmemory9|Used Memory (Shard 9)|Bytes|Maximal||No Dimensions|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Bytes|Maximal||No Dimensions|
|serverLoad9|Server Load (Shard 9)|Procent|Maximal||No Dimensions|
|cacheWrite9|Cache Write (Shard 9)|BytesPerSecond|Maximal||No Dimensions|
|cacheRead9|Cache Read (Shard 9)|BytesPerSecond|Maximal||No Dimensions|
|percentProcessorTime9|CPU (Shard 9)|Procent|Maximal||No Dimensions|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|No Dimensions|
|Nätverk – inkommande|Nätverk – inkommande|Bytes|Totalt|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|No Dimensions|
|Nätverk – utgående|Nätverk – utgående|Bytes|Totalt|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|No Dimensions|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Medel|Average bytes read from disk during monitoring period.|No Dimensions|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Medel|Average bytes written to disk during monitoring period.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Medel|Disk Read IOPS.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Medel|Disk Write IOPS.|No Dimensions|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|RoleInstanceId|
|Nätverk – inkommande|Nätverk – inkommande|Bytes|Totalt|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|RoleInstanceId|
|Nätverk – utgående|Nätverk – utgående|Bytes|Totalt|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|RoleInstanceId|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Medel|Average bytes read from disk during monitoring period.|RoleInstanceId|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Medel|Average bytes written to disk during monitoring period.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Medel|Disk Read IOPS.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Medel|Disk Write IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Antal|Totalt|Total number of calls.|ApiName, OperationName, Region|
|SuccessfulCalls|Successful Calls|Antal|Totalt|Number of successful calls.|ApiName, OperationName, Region|
|TotalErrors|Total Errors|Antal|Totalt|Total number of calls with error response (HTTP response code 4xx or 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Blocked Calls|Antal|Totalt|Number of calls that exceeded rate or quota limit.|ApiName, OperationName, Region|
|ServerErrors|Server Errors|Antal|Totalt|Number of calls with service internal error (HTTP response code 5xx).|ApiName, OperationName, Region|
|ClientErrors|Client Errors|Antal|Totalt|Number of calls with client side error (HTTP response code 4xx).|ApiName, OperationName, Region|
|DataIn|Data In|Bytes|Totalt|Size of incoming data in bytes.|ApiName, OperationName, Region|
|DataOut|Data Out|Bytes|Totalt|Size of outgoing data in bytes.|ApiName, OperationName, Region|
|Svarstid|Svarstid|MilliSeconds|Medel|Latency in milliseconds.|ApiName, OperationName, Region|
|CharactersTranslated|Characters Translated|Antal|Totalt|Total number of characters in incoming text request.|ApiName, OperationName, Region|
|CharactersTrained|Characters Trained|Antal|Totalt|Total number of characters trained.|ApiName, OperationName, Region|
|SpeechSessionDuration|Speech Session Duration|Sekunder|Totalt|Total duration of speech session in seconds.|ApiName, OperationName, Region|
|TotalTransactions|Total Transactions|Antal|Totalt|Total number of transactions.|No Dimensions|
|TotalTokenCalls|Total Token Calls|Antal|Totalt|Total number of token calls.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|No Dimensions|
|Nätverk – inkommande|Network In Billable|Bytes|Totalt|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Nätverk – utgående|Network Out Billable|Bytes|Totalt|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Totalt|Bytes read from disk during monitoring period|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Totalt|Bytes written to disk during monitoring period|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Medel|Disk Read IOPS|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Medel|Disk Write IOPS|No Dimensions|
|CPU Credits Remaining|CPU Credits Remaining|Antal|Medel|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Antal|Medel|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Medel|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Medel|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Medel|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Medel|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Antal|Medel|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Medel|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Medel|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Medel|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Medel|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Antal|Medel|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Medel|Bytes/Sec read from a single disk during monitoring period|LUN|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Medel|Bytes/Sec written to a single disk during monitoring period|LUN|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Medel|Read IOPS from a single disk during monitoring period|LUN|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Medel|Write IOPS from a single disk during monitoring period|LUN|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Antal|Medel|Data Disk Queue Depth(or Queue Length)|LUN|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Medel|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Medel|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Medel|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Medel|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Antal|Medel|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Inbound Flows|Inbound Flows (Preview)|Antal|Medel|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|No Dimensions|
|Outbound Flows|Outbound Flows (Preview)|Antal|Medel|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|No Dimensions|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Medel|The maximum creation rate of inbound flows (traffic going into the VM)|No Dimensions|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Medel|The maximum creation rate of outbound flows (traffic going out of the VM)|No Dimensions|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Procent|Medel|Premium Data Disk Cache Read Hit|LUN|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Procent|Medel|Premium Data Disk Cache Read Miss|LUN|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Procent|Medel|Premium OS Disk Cache Read Hit|No Dimensions|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Procent|Medel|Premium OS Disk Cache Read Miss|No Dimensions|
|Network In Total|Network In Total|Bytes|Totalt|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Network Out Total|Network Out Total|Bytes|Totalt|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Medel|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|VMName|
|Nätverk – inkommande|Network In Billable|Bytes|Totalt|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Nätverk – utgående|Network Out Billable|Bytes|Totalt|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|
|Disk Read Bytes|Disk Read Bytes|Bytes|Totalt|Bytes read from disk during monitoring period|VMName|
|Disk Write Bytes|Disk Write Bytes|Bytes|Totalt|Bytes written to disk during monitoring period|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Medel|Disk Read IOPS|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Medel|Disk Write IOPS|VMName|
|CPU Credits Remaining|CPU Credits Remaining|Antal|Medel|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Antal|Medel|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Medel|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Medel|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Medel|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Medel|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Antal|Medel|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Medel|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Medel|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Medel|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Medel|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Antal|Medel|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Medel|Bytes/Sec read from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Medel|Bytes/Sec written to a single disk during monitoring period|LUN, VMName|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Medel|Read IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Medel|Write IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Antal|Medel|Data Disk Queue Depth(or Queue Length)|LUN, VMName|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Medel|Bytes/Sec read from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Medel|Bytes/Sec written to a single disk during monitoring period for OS disk|VMName|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Medel|Read IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Medel|Write IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Antal|Medel|OS Disk Queue Depth(or Queue Length)|VMName|
|Inbound Flows|Inbound Flows (Preview)|Antal|Medel|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|VMName|
|Outbound Flows|Outbound Flows (Preview)|Antal|Medel|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|VMName|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Medel|The maximum creation rate of inbound flows (traffic going into the VM)|VMName|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Medel|The maximum creation rate of outbound flows (traffic going out of the VM)|VMName|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Procent|Medel|Premium Data Disk Cache Read Hit|LUN, VMName|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Procent|Medel|Premium Data Disk Cache Read Miss|LUN, VMName|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Procent|Medel|Premium OS Disk Cache Read Hit|VMName|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Procent|Medel|Premium OS Disk Cache Read Miss|VMName|
|Network In Total|Network In Total|Bytes|Totalt|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Network Out Total|Network Out Total|Bytes|Totalt|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuUsage|CPU Usage|Antal|Medel|CPU usage on all cores in millicores.|containerName|
|MemoryUsage|Memory Usage|Bytes|Medel|Total memory usage in byte.|containerName|
|NetworkBytesReceivedPerSecond|Network Bytes Received Per Second|Bytes|Medel|The network bytes received per second.|No Dimensions|
|NetworkBytesTransmittedPerSecond|Network Bytes Transmitted Per Second|Bytes|Medel|The network bytes transmitted per second.|No Dimensions|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalPullCount|Total Pull Count|Antal|Medel|Number of image pulls in total|No Dimensions|
|SuccessfulPullCount|Successful Pull Count|Antal|Medel|Number of successful image pulls|No Dimensions|
|TotalPushCount|Total Push Count|Antal|Medel|Number of image pushes in total|No Dimensions|
|SuccessfulPushCount|Successful Push Count|Antal|Medel|Number of successful image pushes|No Dimensions|
|RunDuration|Run Duration|Milliseconds|Totalt|Run Duration in milliseconds|No Dimensions|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Total number of available cpu cores in a managed cluster|Antal|Totalt|Total number of available cpu cores in a managed cluster|No Dimensions|
|kube_node_status_allocatable_memory_bytes|Total amount of available memory in a managed cluster|Bytes|Totalt|Total amount of available memory in a managed cluster|No Dimensions|
|kube_pod_status_ready|Number of pods in Ready state|Antal|Totalt|Number of pods in Ready state|namespace, pod|
|kube_node_status_condition|Statuses for various node conditions|Antal|Totalt|Statuses for various node conditions|condition, status, status2, node|
|kube_pod_status_phase|Number of pods by phase|Antal|Totalt|Number of pods by phase|phase, namespace, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Antal|Totalt||No Dimensions|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Antal|Totalt||No Dimensions|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Antal|Totalt||No Dimensions|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Antal|Totalt||No Dimensions|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Sekunder|Totalt||No Dimensions|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Antal|Totalt||No Dimensions|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Antal|Totalt||No Dimensions|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Sekunder|Totalt||No Dimensions|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Antal|Totalt||No Dimensions|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Antal|Totalt||No Dimensions|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Sekunder|Totalt||No Dimensions|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Sekunder|Totalt||No Dimensions|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Sekunder|Totalt||No Dimensions|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Sekunder|Totalt||No Dimensions|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Sekunder|Totalt||No Dimensions|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Sekunder|Totalt||No Dimensions|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Sekunder|Totalt||No Dimensions|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Sekunder|Totalt||No Dimensions|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Sekunder|Totalt||No Dimensions|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Sekunder|Totalt||No Dimensions|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Sekunder|Totalt||No Dimensions|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Sekunder|Totalt||No Dimensions|
|ImportASAValuesFailed|Import ASA Values Failed Count|Antal|Totalt||No Dimensions|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Antal|Totalt||No Dimensions|
|DCIProfilesCount|Profile Instance Count|Antal|Last||No Dimensions|
|DCIInteractionsPerMonthCount|Interactions per Month Count|Antal|Last||No Dimensions|
|DCIKpisCount|KPI Count|Antal|Last||No Dimensions|
|DCISegmentsCount|Segment Count|Antal|Last||No Dimensions|
|DCIPredictiveMatchPoliciesCount|Predictive Match Count|Antal|Last||No Dimensions|
|DCIPredictionsCount|Prediction Count|Antal|Last||No Dimensions|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|NICReadThroughput|Read Throughput (Network)|BytesPerSecond|Medel|The read throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|NICWriteThroughput|Write Throughput (Network)|BytesPerSecond|Medel|The write throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|CloudReadThroughputPerShare|Cloud Download Throughput (Share)|BytesPerSecond|Medel|The download throughput to Azure from a share during the reporting period.|Dela|
|CloudUploadThroughputPerShare|Cloud Upload Throughput (Share)|BytesPerSecond|Medel|The upload throughput to Azure from a share during the reporting period.|Dela|
|BytesUploadedToCloudPerShare|Cloud Bytes Uploaded (Share)|Bytes|Medel|The total number of bytes that is uploaded to Azure from a share during the reporting period.|Dela|
|TotalCapacity|Total Capacity|Bytes|Medel|Total Capacity|No Dimensions|
|AvailableCapacity|Available Capacity|Bytes|Medel|The available capacity in bytes during the reporting period.|No Dimensions|
|CloudUploadThroughput|Cloud Upload Throughput|BytesPerSecond|Medel|The cloud upload throughput  to Azure during the reporting period.|No Dimensions|
|CloudReadThroughput|Cloud Download Throughput|BytesPerSecond|Medel|The cloud download throughput to Azure during the reporting period.|No Dimensions|
|BytesUploadedToCloud|Cloud Bytes Uploaded (Device)|Bytes|Medel|The total number of bytes that is uploaded to Azure from a device during the reporting period.|No Dimensions|
|HyperVVirtualProcessorUtilization|Edge Compute - Percentage CPU|Procent|Medel|Percent CPU Usage|InstanceName|
|HyperVMemoryUtilization|Edge Compute - Memory Usage|Procent|Medel|Amount of RAM in Use|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FailedRuns|Failed Runs|Antal|Totalt||pipelineName, activityName|
|SuccessfulRuns|Successful Runs|Antal|Totalt||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics|Antal|Totalt||FailureType, Name|
|PipelineSucceededRuns|Succeeded pipeline runs metrics|Antal|Totalt||FailureType, Name|
|ActivityFailedRuns|Failed activity runs metrics|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Succeeded activity runs metrics|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Failed trigger runs metrics|Antal|Totalt||Name, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics|Antal|Totalt||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Integration runtime CPU utilization|Procent|Medel||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Integration runtime available memory|Bytes|Medel||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Maximum allowed entities count|Antal|Maximal||No Dimensions|
|MaxAllowedFactorySizeInGbUnits|Maximum allowed factory size (GB unit)|Antal|Maximal||No Dimensions|
|ResourceCount|Total entities count|Antal|Maximal||No Dimensions|
|FactorySizeInGbUnits|Total factory size (GB unit)|Antal|Maximal||No Dimensions|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Antal|Totalt|Count of successful jobs.|No Dimensions|
|JobEndedFailure|Failed Jobs|Antal|Totalt|Count of failed jobs.|No Dimensions|
|JobEndedCancelled|Canceled Jobs|Antal|Totalt|Count of canceled jobs.|No Dimensions|
|JobAUEndedSuccess|Successful AU Time|Sekunder|Totalt|Total AU time for successful jobs.|No Dimensions|
|JobAUEndedFailure|Failed AU Time|Sekunder|Totalt|Total AU time for failed jobs.|No Dimensions|
|JobAUEndedCancelled|Canceled AU Time|Sekunder|Totalt|Total AU time for canceled jobs.|No Dimensions|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TotalStorage|Totalt lagringsutrymme|Bytes|Maximal|Total amount of data stored in the account.|No Dimensions|
|DataWritten|Data Written|Bytes|Totalt|Total amount of data written to the account.|No Dimensions|
|DataRead|Data Read|Bytes|Totalt|Total amount of data read from the account.|No Dimensions|
|WriteRequests|Write Requests|Antal|Totalt|Count of data write requests to the account.|No Dimensions|
|ReadRequests|Read Requests|Antal|Totalt|Count of data read requests to the account.|No Dimensions|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Medel|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Medel|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procent|Medel|IO percent|No Dimensions|
|storage_percent|Storage percent|Procent|Medel|Storage percent|No Dimensions|
|storage_used|Använt lagringsutrymme|Bytes|Medel|Använt lagringsutrymme|No Dimensions|
|storage_limit|Storage limit|Bytes|Medel|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procent|Medel|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Medel|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Medel|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Antal|Medel|Active Connections|No Dimensions|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Antal|Medel|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Medel|Backup Storage used|No Dimensions|
|network_bytes_egress|Nätverk – utgående|Bytes|Totalt|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Nätverk – inkommande|Bytes|Totalt|Network In across active connections|No Dimensions|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Medel|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Medel|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procent|Medel|IO percent|No Dimensions|
|storage_percent|Storage percent|Procent|Medel|Storage percent|No Dimensions|
|storage_used|Använt lagringsutrymme|Bytes|Medel|Använt lagringsutrymme|No Dimensions|
|storage_limit|Storage limit|Bytes|Medel|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procent|Medel|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Medel|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Medel|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Antal|Medel|Active Connections|No Dimensions|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Antal|Medel|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Medel|Backup Storage used|No Dimensions|
|network_bytes_egress|Nätverk – utgående|Bytes|Totalt|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Nätverk – inkommande|Bytes|Totalt|Network In across active connections|No Dimensions|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Medel|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Medel|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procent|Medel|IO percent|No Dimensions|
|storage_percent|Storage percent|Procent|Medel|Storage percent|No Dimensions|
|storage_used|Använt lagringsutrymme|Bytes|Medel|Använt lagringsutrymme|No Dimensions|
|storage_limit|Storage limit|Bytes|Medel|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procent|Medel|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Medel|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Medel|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Antal|Medel|Active Connections|No Dimensions|
|connections_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Medel|Backup Storage used|No Dimensions|
|network_bytes_egress|Nätverk – utgående|Bytes|Totalt|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Nätverk – inkommande|Bytes|Totalt|Network In across active connections|No Dimensions|
|pg_replica_log_delay_in_seconds|Replica Lag|Sekunder|Maximal|Replica lag in seconds|No Dimensions|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas|Bytes|Maximal|Lag in bytes of the most lagging replica|No Dimensions|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Medel|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Medel|Memory percent|No Dimensions|
|iops|IOPS|Antal|Medel|IO Operations per second|No Dimensions|
|storage_percent|Storage percent|Procent|Medel|Storage percent|No Dimensions|
|storage_used|Använt lagringsutrymme|Bytes|Medel|Använt lagringsutrymme|No Dimensions|
|active_connections|Active Connections|Antal|Medel|Active Connections|No Dimensions|
|network_bytes_egress|Nätverk – utgående|Bytes|Totalt|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Nätverk – inkommande|Bytes|Totalt|Network In across active connections|No Dimensions|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetry message send attempts|Antal|Totalt|Number of device-to-cloud telemetry messages attempted to be sent to your IoT hub|No Dimensions|
|d2c.telemetry.ingress.success|Telemetry messages sent|Antal|Totalt|Number of device-to-cloud telemetry messages sent successfully to your IoT hub|No Dimensions|
|c2d.commands.egress.complete.success|Commands completed|Antal|Totalt|Number of cloud-to-device commands completed successfully by the device|No Dimensions|
|c2d.commands.egress.abandon.success|Commands abandoned|Antal|Totalt|Number of cloud-to-device commands abandoned by the device|No Dimensions|
|c2d.commands.egress.reject.success|Commands rejected|Antal|Totalt|Number of cloud-to-device commands rejected by the device|No Dimensions|
|devices.totalDevices|Total devices (deprecated)|Antal|Totalt|Number of devices registered to your IoT hub|No Dimensions|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) |Antal|Totalt|Number of devices connected to your IoT hub|No Dimensions|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered|Antal|Totalt|The number of times messages were successfully delivered to all endpoints using IoT Hub routing. If a message is routed to multiple endpoints, this value increases by one for each successful delivery. If a message is delivered to the same endpoint multiple times, this value increases by one for each successful delivery.|No Dimensions|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped |Antal|Totalt|The number of times messages were dropped by IoT Hub routing due to dead endpoints. This value does not count messages delivered to fallback route as dropped messages are not delivered there.|No Dimensions|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned |Antal|Totalt|The number of times messages were orphaned by IoT Hub routing because they didn't match any routing rules (including the fallback rule). |No Dimensions|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible|Antal|Totalt|The number of times IoT Hub routing failed to deliver messages due to an incompatibility with the endpoint. This value does not include retries.|No Dimensions|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback|Antal|Totalt|The number of times IoT Hub routing delivered messages to the endpoint associated with the fallback route.|No Dimensions|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub|Antal|Totalt|The number of times IoT Hub routing successfully delivered messages to Event Hub endpoints.|No Dimensions|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub|Milliseconds|Medel|The average latency (milliseconds) between message ingress to IoT Hub and message ingress into an Event Hub endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue|Antal|Totalt|The number of times IoT Hub routing successfully delivered messages to Service Bus queue endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue|Milliseconds|Medel|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus queue endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic|Antal|Totalt|The number of times IoT Hub routing successfully delivered messages to Service Bus topic endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic|Milliseconds|Medel|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus topic endpoint.|No Dimensions|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events|Antal|Totalt|The number of times IoT Hub routing successfully delivered messages to the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events|Milliseconds|Medel|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.egress.storage|Routing: messages delivered to storage|Antal|Totalt|The number of times IoT Hub routing successfully delivered messages to storage endpoints.|No Dimensions|
|d2c.endpoints.latency.storage|Routing: message latency for storage|Milliseconds|Medel|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a storage endpoint.|No Dimensions|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage|Bytes|Totalt|The amount of data (bytes) IoT Hub routing delivered to storage endpoints.|No Dimensions|
|d2c.endpoints.egress.storage.blobs|Routing: blobs delivered to storage|Antal|Totalt|The number of times IoT Hub routing delivered blobs to storage endpoints.|No Dimensions|
|EventGridDeliveries|Event Grid deliveries (preview)|Antal|Totalt|The number of IoT Hub events published to Event Grid. Use the Result dimension for the number of successful and failed requests. EventType dimension shows the type of event (https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|The average latency (milliseconds) from when the Iot Hub event was generated to when the event was published to Event Grid. This number is an average between all event types. Use the EventType dimension to see latency of a specific type of event.|EventType|
|d2c.twin.read.success|Successful twin reads from devices|Antal|Totalt|The count of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.read.failure|Failed twin reads from devices|Antal|Totalt|The count of all failed device-initiated twin reads.|No Dimensions|
|d2c.twin.read.size|Response size of twin reads from devices|Bytes|Medel|The average, min, and max of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.update.success|Successful twin updates from devices|Antal|Totalt|The count of all successful device-initiated twin updates.|No Dimensions|
|d2c.twin.update.failure|Failed twin updates from devices|Antal|Totalt|The count of all failed device-initiated twin updates.|No Dimensions|
|d2c.twin.update.size|Size of twin updates from devices|Bytes|Medel|The average, min, and max size of all successful device-initiated twin updates.|No Dimensions|
|c2d.methods.success|Successful direct method invocations|Antal|Totalt|The count of all successful direct method calls.|No Dimensions|
|c2d.methods.failure|Failed direct method invocations|Antal|Totalt|The count of all failed direct method calls.|No Dimensions|
|c2d.methods.requestSize|Request size of direct method invocations|Bytes|Medel|The average, min, and max of all successful direct method requests.|No Dimensions|
|c2d.methods.responseSize|Response size of direct method invocations|Bytes|Medel|The average, min, and max of all successful direct method responses.|No Dimensions|
|c2d.twin.read.success|Successful twin reads from back end|Antal|Totalt|The count of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.failure|Failed twin reads from back end|Antal|Totalt|The count of all failed back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.size|Response size of twin reads from back end|Bytes|Medel|The average, min, and max of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.update.success|Successful twin updates from back end|Antal|Totalt|The count of all successful back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.failure|Failed twin updates from back end|Antal|Totalt|The count of all failed back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.size|Size of twin updates from back end|Bytes|Medel|The average, min, and max size of all successful back-end-initiated twin updates.|No Dimensions|
|twinQueries.success|Successful twin queries|Antal|Totalt|The count of all successful twin queries.|No Dimensions|
|twinQueries.failure|Failed twin queries|Antal|Totalt|The count of all failed twin queries.|No Dimensions|
|twinQueries.resultSize|Twin queries result size|Bytes|Medel|The average, min, and max of the result size of all successful twin queries.|No Dimensions|
|jobs.createTwinUpdateJob.success|Successful creations of twin update jobs|Antal|Totalt|The count of all successful creation of twin update jobs.|No Dimensions|
|jobs.createTwinUpdateJob.failure|Failed creations of twin update jobs|Antal|Totalt|The count of all failed creation of twin update jobs.|No Dimensions|
|jobs.createDirectMethodJob.success|Successful creations of method invocation jobs|Antal|Totalt|The count of all successful creation of direct method invocation jobs.|No Dimensions|
|jobs.createDirectMethodJob.failure|Failed creations of method invocation jobs|Antal|Totalt|The count of all failed creation of direct method invocation jobs.|No Dimensions|
|jobs.listJobs.success|Successful calls to list jobs|Antal|Totalt|The count of all successful calls to list jobs.|No Dimensions|
|jobs.listJobs.failure|Failed calls to list jobs|Antal|Totalt|The count of all failed calls to list jobs.|No Dimensions|
|jobs.cancelJob.success|Successful job cancellations|Antal|Totalt|The count of all successful calls to cancel a job.|No Dimensions|
|jobs.cancelJob.failure|Failed job cancellations|Antal|Totalt|The count of all failed calls to cancel a job.|No Dimensions|
|jobs.queryJobs.success|Successful job queries|Antal|Totalt|The count of all successful calls to query jobs.|No Dimensions|
|jobs.queryJobs.failure|Failed job queries|Antal|Totalt|The count of all failed calls to query jobs.|No Dimensions|
|jobs.completed|Completed jobs|Antal|Totalt|The count of all completed jobs.|No Dimensions|
|jobs.failed|Failed jobs|Antal|Totalt|The count of all failed jobs.|No Dimensions|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors|Antal|Totalt|Number of throttling errors due to device throughput throttles|No Dimensions|
|dailyMessageQuotaUsed|Total number of messages used|Antal|Medel|Number of total messages used today. This is a cumulative value that is reset to zero at 00:00 UTC every day.|No Dimensions|
|deviceDataUsage|Total device data usage|Bytes|Totalt|Bytes transferred to and from any devices connected to IotHub|No Dimensions|
|totalDeviceCount|Total devices (preview)|Antal|Medel|Number of devices registered to your IoT hub|No Dimensions|
|connectedDeviceCount|Connected devices (preview)|Antal|Medel|Number of devices connected to your IoT hub|No Dimensions|
|configurations|Configuration Metrics|Antal|Totalt|Metrics for Configuration Operations|No Dimensions|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RegistrationAttempts|Registration attempts|Antal|Totalt|Number of device registrations attempted|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Devices assigned|Antal|Totalt|Number of devices assigned to an IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attestation attempts|Antal|Totalt|Number of device attestations attempted|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AvailableStorage|Available Storage|Bytes|Totalt|Total available storage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Cassandra Connection Closures|Antal|Totalt|Number of Cassandra connections that were closed, reported at a 1 minute granularity|Region, ClosureReason|
|CassandraRequestCharges|Cassandra Request Charges|Antal|Totalt|RUs consumed for Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra Requests|Antal|Antal|Number of Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Dataanvändning|Bytes|Totalt|Total data usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentCount|Document Count|Antal|Totalt|Total document count reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentQuota|Document Quota|Bytes|Totalt|Total storage quota reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|IndexUsage|Index Usage|Bytes|Totalt|Total index usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests|Antal|Antal|Count of metadata requests. Cosmos DB maintains system metadata collection for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Mongo Request Charge|Antal|Totalt|Mongo Request Units Consumed|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo Requests|Antal|Antal|Number of Mongo Requests Made|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Etablerat dataflöde|Antal|Maximal|Etablerat dataflöde|DatabaseName, CollectionName|
|ReplicationLatency|P99 Replication Latency|MilliSeconds|Medel|P99 Replication Latency across source and target regions for geo-enabled account|SourceRegion, TargetRegion|
|ServiceAvailability|Service Availability|Procent|Medel|Account requests availability at one hour, day or month granularity|No Dimensions|
|TotalRequestUnits|Total Request Units|Antal|Totalt|Request Units consumed|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Totalt antal förfrågningar|Antal|Antal|Number of requests made|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Antal|Totalt|Total events published to this topic|No Dimensions|
|PublishFailCount|Publish Failed Events|Antal|Totalt|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Antal|Totalt|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Antal|Totalt|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|MatchedEventCount|Matched Events|Antal|Totalt|Total events matched to this event subscription|No Dimensions|
|DeliveryAttemptFailCount|Delivery Failed Events|Antal|Totalt|Total events failed to deliver to this event subscription|Error, ErrorType|
|DeliverySuccessCount|Delivered Events|Antal|Totalt|Total events delivered to this event subscription|No Dimensions|
|DestinationProcessingDurationInMs|Destination Processing Duration|Milliseconds|Medel|Destination processing duration in milliseconds|No Dimensions|
|DroppedEventCount|Dropped Events|Antal|Totalt|Total dropped events matching to this event subscription|DropReason|
|DeadLetteredCount|Dead Lettered Events|Antal|Totalt|Total dead lettered events matching to this event subscription|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Antal|Totalt|Total events published to this topic|No Dimensions|
|PublishFailCount|Failed Events|Antal|Totalt|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Antal|Totalt|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Antal|Totalt|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests|Antal|Totalt|Successful Requests for Microsoft.EventHub.|EntityName, |
|ServerErrors|Server Errors.|Antal|Totalt|Server Errors for Microsoft.EventHub.|EntityName, |
|UserErrors|User Errors.|Antal|Totalt|User Errors for Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Quota Exceeded Errors.|Antal|Totalt|Quota Exceeded Errors for Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Throttled Requests.|Antal|Totalt|Throttled Requests for Microsoft.EventHub.|EntityName, |
|IncomingRequests|Incoming Requests|Antal|Totalt|Incoming Requests for Microsoft.EventHub.|EntityName|
|IncomingMessages|Incoming Messages|Antal|Totalt|Incoming Messages for Microsoft.EventHub.|EntityName|
|OutgoingMessages|Outgoing Messages|Antal|Totalt|Outgoing Messages for Microsoft.EventHub.|EntityName|
|IncomingBytes|Incoming Bytes.|Bytes|Totalt|Incoming Bytes for Microsoft.EventHub.|EntityName|
|OutgoingBytes|Outgoing Bytes.|Bytes|Totalt|Outgoing Bytes for Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Antal|Medel|Total Active Connections for Microsoft.EventHub.|No Dimensions|
|ConnectionsOpened|Connections Opened.|Antal|Medel|Connections Opened for Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connections Closed.|Antal|Medel|Connections Closed for Microsoft.EventHub.|EntityName|
|CaptureBacklog|Capture Backlog.|Antal|Totalt|Capture Backlog for Microsoft.EventHub.|EntityName|
|CapturedMessages|Captured Messages.|Antal|Totalt|Captured Messages for Microsoft.EventHub.|EntityName|
|CapturedBytes|Captured Bytes.|Bytes|Totalt|Captured Bytes for Microsoft.EventHub.|EntityName|
|Storlek|Storlek|Bytes|Medel|Size of an EventHub in Bytes.|EntityName|
|INREQS|Incoming Requests (Deprecated)|Antal|Totalt|Total incoming send requests for a namespace (Deprecated)|No Dimensions|
|SUCCREQ|Successful Requests (Deprecated)|Antal|Totalt|Total successful requests for a namespace (Deprecated)|No Dimensions|
|FAILREQ|Failed Requests (Deprecated)|Antal|Totalt|Total failed requests for a namespace (Deprecated)|No Dimensions|
|SVRBSY|Server Busy Errors (Deprecated)|Antal|Totalt|Total server busy errors for a namespace (Deprecated)|No Dimensions|
|INTERR|Internal Server Errors (Deprecated)|Antal|Totalt|Total internal server errors for a namespace (Deprecated)|No Dimensions|
|MISCERR|Other Errors (Deprecated)|Antal|Totalt|Total failed requests for a namespace (Deprecated)|No Dimensions|
|INMSGS|Incoming Messages (Deprecated) (Deprecated)|Antal|Totalt|Total incoming messages for a namespace. This metric is deprecated. Please use Incoming Messages metric instead (Deprecated)|No Dimensions|
|EHINMSGS|Incoming Messages (Deprecated)|Antal|Totalt|Total incoming messages for a namespace (Deprecated)|No Dimensions|
|OUTMSGS|Outgoing Messages (Deprecated) (Deprecated)|Antal|Totalt|Total outgoing messages for a namespace. This metric is deprecated. Please use Outgoing Messages metric instead (Deprecated)|No Dimensions|
|EHOUTMSGS|Outgoing Messages (Deprecated)|Antal|Totalt|Total outgoing messages for a namespace (Deprecated)|No Dimensions|
|EHINMBS|Incoming bytes (Deprecated) (Deprecated)|Bytes|Totalt|Event Hub incoming message throughput for a namespace. This metric is deprecated. Please use Incoming bytes metric instead (Deprecated)|No Dimensions|
|EHINBYTES|Incoming bytes (Deprecated)|Bytes|Totalt|Event Hub incoming message throughput for a namespace (Deprecated)|No Dimensions|
|EHOUTMBS|Outgoing bytes (Deprecated) (Deprecated)|Bytes|Totalt|Event Hub outgoing message throughput for a namespace. This metric is deprecated. Please use Outgoing bytes metric instead (Deprecated)|No Dimensions|
|EHOUTBYTES|Outgoing bytes (Deprecated)|Bytes|Totalt|Event Hub outgoing message throughput for a namespace (Deprecated)|No Dimensions|
|EHABL|Archive backlog messages (Deprecated)|Antal|Totalt|Event Hub archive messages in backlog for a namespace (Deprecated)|No Dimensions|
|EHAMSGS|Archive messages (Deprecated)|Antal|Totalt|Event Hub archived messages in a namespace (Deprecated)|No Dimensions|
|EHAMBS|Archive message throughput (Deprecated)|Bytes|Totalt|Event Hub archived message throughput in a namespace (Deprecated)|No Dimensions|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Antal|Totalt|Successful Requests for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|ServerErrors|Server Errors. (Förhandsversion)|Antal|Totalt|Server Errors for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|UserErrors|User Errors. (Förhandsversion)|Antal|Totalt|User Errors for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|QuotaExceededErrors|Quota Exceeded Errors. (Förhandsversion)|Antal|Totalt|Quota Exceeded Errors for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|ThrottledRequests|Throttled Requests. (Förhandsversion)|Antal|Totalt|Throttled Requests for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|IncomingRequests|Incoming Requests (Preview)|Antal|Totalt|Incoming Requests for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|IncomingMessages|Incoming Messages (Preview)|Antal|Totalt|Incoming Messages for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|OutgoingMessages|Outgoing Messages (Preview)|Antal|Totalt|Outgoing Messages for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|IncomingBytes|Incoming Bytes. (Förhandsversion)|Bytes|Totalt|Incoming Bytes for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|OutgoingBytes|Outgoing Bytes. (Förhandsversion)|Bytes|Totalt|Outgoing Bytes for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|ActiveConnections|ActiveConnections (Preview)|Antal|Medel|Total Active Connections for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|ConnectionsOpened|Connections Opened. (Förhandsversion)|Antal|Medel|Connections Opened for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|ConnectionsClosed|Connections Closed. (Förhandsversion)|Antal|Medel|Connections Closed for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|CaptureBacklog|Capture Backlog. (Förhandsversion)|Antal|Totalt|Capture Backlog for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|CapturedMessages|Captured Messages. (Förhandsversion)|Antal|Totalt|Captured Messages for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|CapturedBytes|Captured Bytes. (Förhandsversion)|Bytes|Totalt|Captured Bytes for Microsoft.EventHub. (Förhandsversion)|No Dimensions|
|Processor|CPU (Preview)|Procent|Maximal|CPU utilization for the Event Hub Cluster as a percentage|Roll|
|AvailableMemory|Available Memory (Preview)|Antal|Maximal|Available memory for the Event Hub Cluster in bytes|Roll|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|GatewayRequests|Gateway Requests|Antal|Totalt|Number of gateway requests|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Categorized Gateway Requests|Antal|Totalt|Number of gateway requests by categories (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Number of Active Workers|Antal|Maximal|Number of Active Workers|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ObservedMetricValue|Observed Metric Value|Antal|Medel|The value computed by autoscale when executed|MetricTriggerSource|
|MetricThreshold|Metric Threshold|Antal|Medel|The configured autoscale threshold when autoscale ran.|MetricTriggerRule|
|ObservedCapacity|Observed Capacity|Antal|Medel|The capacity reported to autoscale when it executed.|No Dimensions|
|ScaleActionsInitiated|Scale Actions Initiated|Antal|Totalt|The direction of the scale operation.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Public Preview)

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Tillgänglighet|Procent|Medel|Percentage of successfully completed availability tests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Tillgänglighetstester|Antal|Antal|Count of availability tests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Availability test duration|MilliSeconds|Medel|Availability test duration|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Page load network connect time|MilliSeconds|Medel|Time between user request and network connection. Includes DNS lookup and transport connection.|No Dimensions|
|browserTimings/processingDuration|Client processing time|MilliSeconds|Medel|Time between receiving the last byte of a document until the DOM is loaded. Async requests may still be processing.|No Dimensions|
|browserTimings/receiveDuration|Receiving response time|MilliSeconds|Medel|Time between the first and last bytes, or until disconnection.|No Dimensions|
|browserTimings/sendDuration|Send request time|MilliSeconds|Medel|Time between network connection and receiving the first byte.|No Dimensions|
|browserTimings/totalDuration|Browser page load time|MilliSeconds|Medel|Time from user request until DOM, stylesheets, scripts and images are loaded.|No Dimensions|
|dependencies/count|Dependency calls|Antal|Antal|Count of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Dependency duration|MilliSeconds|Medel|Duration of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Dependency call failures|Antal|Antal|Count of failed dependency calls made by the application to external resources.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Page views|Antal|Antal|Count of page views.|operation/synthetic|
|pageViews/duration|Page view load time|MilliSeconds|Medel|Page view load time|operation/synthetic|
|performanceCounters/requestExecutionTime|HTTP request execution time|MilliSeconds|Medel|Execution time of the most recent request.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue|Antal|Medel|Length of the application request queue.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP request rate|CountPerSecond|Medel|Rate of all requests to the application per second from ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Exception rate|CountPerSecond|Medel|Count of handled and unhandled exceptions reported to windows, including .NET exceptions and unmanaged exceptions that are converted into .NET exceptions.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process IO rate|BytesPerSecond|Medel|Total bytes per second read and written to files, network and devices.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Process CPU|Procent|Medel|The percentage of elapsed time that all process threads used the processor to execute instructions. This can vary between 0 to 100. This metric indicates the performance of w3wp process alone.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor time|Procent|Medel|The percentage of time that the processor spends in non-idle threads.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Available memory|Bytes|Medel|Physical memory immediately available for allocation to a process or for system use.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Process private bytes|Bytes|Medel|Memory exclusively assigned to the monitored application's processes.|cloud/roleInstance|
|requests/duration|Server response time|MilliSeconds|Medel|Time between receiving an HTTP request and finishing sending the response.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Server requests|Antal|Antal|Count of HTTP requests completed.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Misslyckade förfrågningar|Antal|Antal|Count of HTTP requests marked as failed. In most cases these are requests with a response code >= 400 and not equal to 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Server request rate|CountPerSecond|Medel|Rate of server requests per second|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Undantag|Antal|Antal|Combined count of all uncaught exceptions.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Webbläsarundantag|Antal|Antal|Count of uncaught exceptions thrown in the browser.|No Dimensions|
|exceptions/server|Server exceptions|Antal|Antal|Count of uncaught exceptions thrown in the server application.|cloud/roleName, cloud/roleInstance|
|traces/count|Traces|Antal|Antal|Trace document count|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServiceApiHit|Total Service Api Hits|Antal|Antal|Number of total service api hits|ActivityType, ActivityName|
|ServiceApiLatency|Overall Service Api Latency|Milliseconds|Medel|Overall latency of service api requests|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Total Service Api Results|Antal|Antal|Number of total service api results|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CacheUtilization|Cache Utilization|Procent|Medel|Utilization level in the cluster scope|Inget|
|QueryDuration|Query Duration|Milliseconds|Medel|Queries’ duration in seconds|Query Status|
|IngestionUtilization|Ingestion Utilization|Procent|Medel|Ratio of used ingestion slots in the cluster|Inget|
|KeepAlive|Keep Alive|Antal|Medel|Sanity check indicates the cluster responds to queries|Inget|
|IngestionVolumeInMB|Ingestion Volume (In MB)|Antal|Totalt|Overall volume of ingested data to the cluster (in MB)|Databas|
|IngestionLatencyInSeconds|Ingestion Latency (In seconds)|Sekunder|Medel|Ingestion time from the source (e.g. message is in EventHub) to the cluster in seconds|Inget|
|EventProcessedForEventHubs|Events Processed (for Event Hubs)|Antal|Totalt|Number of events processed by the cluster when ingesting from Event Hub|Inget|
|IngestionResult|Ingestion Result|Antal|Antal|Number of ingestion operations|Status|
|Processor|Processor|Procent|Medel|CPU utilization level|Inget|
| ContinuousExportNumOfRecordsExported | Number of records exported in continuous export | Antal | Totalt | Number of records exported for every storage artifact written during the export operation  | Inget |
| ExportUtilization | Export Utilization | Procent | Maximal | Export utilization | Inget |
| ContinuousExportPendingCount | Continuous Export Pending Count | Antal | Maximal | The number of pending continuous export jobs ready for execution | Inget |
| ContinuousExportMaxLatenessMinutes | Continuous Export Max Lateness Minutes | Antal | Maximal | The max time in minutes of all continuous exports which are pending and ready for execution | Inget |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Antal|Antal|Count of API calls|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Antal|Totalt|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Antal|Totalt|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Antal|Totalt|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Antal|Totalt|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Antal|Totalt|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Sekunder|Medel|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Sekunder|Medel|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Antal|Totalt|Number of workflow action or trigger throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Procent|Totalt|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Antal|Totalt|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Antal|Totalt|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Antal|Totalt|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed|Antal|Totalt|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Antal|Totalt|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Sekunder|Medel|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Sekunder|Medel|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Antal|Totalt|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Antal|Totalt|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Antal|Totalt|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Antal|Totalt|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Antal|Totalt|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Antal|Totalt|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Antal|Totalt|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Sekunder|Medel|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Sekunder|Medel|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Sekunder|Medel|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Antal|Totalt|Number of workflow trigger throttled events.|No Dimensions|
|BillableActionExecutions|Billable Action Executions|Antal|Totalt|Number of workflow action executions getting billed.|No Dimensions|
|BillableTriggerExecutions|Billable Trigger Executions|Antal|Totalt|Number of workflow trigger executions getting billed.|No Dimensions|
|TotalBillableExecutions|Total Billable Executions|Antal|Totalt|Number of workflow executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Antal|Totalt|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Antal|Totalt|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Antal|Totalt|Number of storage consumption executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Antal|Totalt|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Antal|Totalt|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Antal|Totalt|Number of storage consumption executions getting billed.|No Dimensions|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Antal|Totalt|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Antal|Totalt|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Antal|Totalt|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Antal|Totalt|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Antal|Totalt|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Sekunder|Medel|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Sekunder|Medel|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Antal|Totalt|Number of workflow action or trigger throttled events.|No Dimensions|
|RunStartThrottledEvents|Run Start Throttled Events|Antal|Totalt|Number of workflow run start throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Procent|Totalt|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Antal|Totalt|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Antal|Totalt|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Antal|Totalt|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed |Antal|Totalt|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Antal|Totalt|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Sekunder|Medel|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Sekunder|Medel|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Antal|Totalt|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Antal|Totalt|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Antal|Totalt|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Antal|Totalt|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Antal|Totalt|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Antal|Totalt|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Antal|Totalt|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Sekunder|Medel|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Sekunder|Medel|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Sekunder|Medel|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Antal|Totalt|Number of workflow trigger throttled events.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Workflow Processor Usage for Integration Service Environment|Procent|Medel|Workflow processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Workflow Memory Usage for Integration Service Environment|Procent|Medel|Workflow memory usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Connector Processor Usage for Integration Service Environment|Procent|Medel|Connector processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Connector Memory Usage for Integration Service Environment|Procent|Medel|Connector memory usage for integration service environment.|No Dimensions|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Completed Runs|Completed Runs|Antal|Totalt|Number of runs completed successfully for this workspace|Scenario|
|Started Runs|Started Runs|Antal|Totalt|Number of runs started for this workspace|Scenario|
|Failed Runs|Failed Runs|Antal|Totalt|Number of runs failed for this workspace|Scenario|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Användning|Användning|Antal|Antal|Count of API calls|ApiCategory, ApiName, ResultType, ResponseCode|
|Tillgänglighet|Tillgänglighet|Procent|Medel|Availability of the APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageOtherLatency|Average other latency|ms/op|Medel|Average other latency (that is not read or write) in milliseconds per operation|No Dimensions|
|AverageReadLatency|Average read latency|ms/op|Medel|Average read latency in milliseconds per operation|No Dimensions|
|AverageTotalLatency|Average total latency|ms/op|Medel|Average total latency in milliseconds per operation|No Dimensions|
|AverageWriteLatency|Average write latency|ms/op|Medel|Average write latency in milliseconds per operation|No Dimensions|
|FilesystemOtherOps|Filesystem other ops|ops|Medel|Number of filesystem other operations (that is not read or write)|No Dimensions|
|FilesystemReadOps|Filesystem read ops|ops|Medel|Number of filesystem read operations|No Dimensions|
|FilesystemTotalOps|Filesystem total ops|ops|Medel|Sum of all filesystem operations|No Dimensions|
|FilesystemWriteOps|Filesystem write ops|ops|Medel|Number of filesystem write operations|No Dimensions|
|IoBytesPerOtherOps|Io bytes per other ops|bytes/op|Medel|Number of In/out bytes per other operations (that is not read or write)|No Dimensions|
|IoBytesPerReadOps|Io bytes per read ops|bytes/op|Medel|Number of In/out bytes per read operation|No Dimensions|
|IoBytesPerTotalOps|Io bytes per op across all operations|bytes/op|Medel|Sum of all In/out bytes operation|No Dimensions|
|IoBytesPerWriteOps|Io bytes per write ops|bytes/op|Medel|Number of In/out bytes per write operation|No Dimensions|
|OtherIops|Other iops|operations/second|Medel|Other In/out operation per second|No Dimensions|
|OtherThroughput|Other throughput|MBps|Medel|Other throughput (that is not read or write) in megabytes per second|No Dimensions|
|ReadIops|Read iops|operations/second|Medel|Read In/out operations per second|No Dimensions|
|ReadThroughput|Read throughput|MBps|Medel|Read throughput in megabytes per second|No Dimensions|
|TotalIops|Total iops|operations/second|Medel|Sum of all In/out operations per second|No Dimensions|
|TotalThroughput|Total throughput|MBps|Medel|Sum of all throughput in megabytes per second|No Dimensions|
|VolumeAllocatedSize|Volume allocated size|bytes|Medel|Allocated size of the volume (Not the actual used bytes)|No Dimensions|
|VolumeLogicalSize|Volume logical size|bytes|Medel|Logical size of the volume (used bytes)|No Dimensions|
|VolumeSnapshotSize|Volume snapshot size|bytes|Medel|Size of all snapshots in volume|No Dimensions|
|WriteIops|Write iops|operations/second|Medel|Write In/out operations per second|No Dimensions|
|WriteThroughput|Write throughput|MBps|Medel|Write throughput in megabytes per second|No Dimensions|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volume pool allocated size|bytes|Medel|Allocated size of the pool (Not the actual used bytes)|No Dimensions|
|VolumePoolAllocatedUsed|Volume pool allocated used|bytes|Medel|Allocated used size of the pool|No Dimensions|
|VolumePoolTotalLogicalSize|Volume pool total logical size|bytes|Medel|Sum of the logical size of all the volumes belonging to the pool|No Dimensions|
|VolumePoolTotalSnapshotSize|Volume pool total snapshot size|bytes|Medel|Sum of all snapshots in pool|No Dimensions|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Sent|Antal|Totalt|Number of bytes the Network Interface sent|No Dimensions|
|BytesReceivedRate|Bytes Received|Antal|Totalt|Number of bytes the Network Interface received|No Dimensions|
|PacketsSentRate|Packets Sent|Antal|Totalt|Number of packets the Network Interface sent|No Dimensions|
|PacketsReceivedRate|Packets Received|Antal|Totalt|Number of packets the Network Interface received|No Dimensions|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability|Antal|Medel|Average Load Balancer data path availability per time duration|FrontendIPAddress, FrontendPort|
|DipAvailability|Health Probe Status|Antal|Medel|Average Load Balancer health probe status per time duration|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Byte Count|Antal|Totalt|Total number of Bytes transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Packet Count|Antal|Totalt|Total number of Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN Count|Antal|Totalt|Total number of SYN Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT Connection Count|Antal|Totalt|Total number of new SNAT connections created within time period|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview)|Antal|Totalt|Total number of SNAT ports allocated within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Used SNAT Ports (Preview)|Antal|Totalt|Total number of SNAT ports used within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryVolume|Query Volume|Antal|Totalt|Number of queries served for a DNS zone|No Dimensions|
|RecordSetCount|Record Set Count|Antal|Maximal|Number of Record Sets in a DNS zone|No Dimensions|
|RecordSetCapacityUtilization|Record Set Capacity Utilization|Procent|Maximal|Percent of Record Set capacity utilized by a DNS zone|No Dimensions|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|PacketsInDDoS|Inbound packets DDoS|CountPerSecond|Maximal|Inbound packets DDoS|No Dimensions|
|PacketsDroppedDDoS|Inbound packets dropped DDoS|CountPerSecond|Maximal|Inbound packets dropped DDoS|No Dimensions|
|PacketsForwardedDDoS|Inbound packets forwarded DDoS|CountPerSecond|Maximal|Inbound packets forwarded DDoS|No Dimensions|
|TCPPacketsInDDoS|Inbound TCP packets DDoS|CountPerSecond|Maximal|Inbound TCP packets DDoS|No Dimensions|
|TCPPacketsDroppedDDoS|Inbound TCP packets dropped DDoS|CountPerSecond|Maximal|Inbound TCP packets dropped DDoS|No Dimensions|
|TCPPacketsForwardedDDoS|Inbound TCP packets forwarded DDoS|CountPerSecond|Maximal|Inbound TCP packets forwarded DDoS|No Dimensions|
|UDPPacketsInDDoS|Inbound UDP packets DDoS|CountPerSecond|Maximal|Inbound UDP packets DDoS|No Dimensions|
|UDPPacketsDroppedDDoS|Inbound UDP packets dropped DDoS|CountPerSecond|Maximal|Inbound UDP packets dropped DDoS|No Dimensions|
|UDPPacketsForwardedDDoS|Inbound UDP packets forwarded DDoS|CountPerSecond|Maximal|Inbound UDP packets forwarded DDoS|No Dimensions|
|BytesInDDoS|Inbound bytes DDoS|BytesPerSecond|Maximal|Inbound bytes DDoS|No Dimensions|
|BytesDroppedDDoS|Inbound bytes dropped DDoS|BytesPerSecond|Maximal|Inbound bytes dropped DDoS|No Dimensions|
|BytesForwardedDDoS|Inbound bytes forwarded DDoS|BytesPerSecond|Maximal|Inbound bytes forwarded DDoS|No Dimensions|
|TCPBytesInDDoS|Inbound TCP bytes DDoS|BytesPerSecond|Maximal|Inbound TCP bytes DDoS|No Dimensions|
|TCPBytesDroppedDDoS|Inbound TCP bytes dropped DDoS|BytesPerSecond|Maximal|Inbound TCP bytes dropped DDoS|No Dimensions|
|TCPBytesForwardedDDoS|Inbound TCP bytes forwarded DDoS|BytesPerSecond|Maximal|Inbound TCP bytes forwarded DDoS|No Dimensions|
|UDPBytesInDDoS|Inbound UDP bytes DDoS|BytesPerSecond|Maximal|Inbound UDP bytes DDoS|No Dimensions|
|UDPBytesDroppedDDoS|Inbound UDP bytes dropped DDoS|BytesPerSecond|Maximal|Inbound UDP bytes dropped DDoS|No Dimensions|
|UDPBytesForwardedDDoS|Inbound UDP bytes forwarded DDoS|BytesPerSecond|Maximal|Inbound UDP bytes forwarded DDoS|No Dimensions|
|IfUnderDDoSAttack|Under DDoS attack or not|Antal|Maximal|Under DDoS attack or not|No Dimensions|
|DDoSTriggerTCPPackets|Inbound TCP packets to trigger DDoS mitigation|CountPerSecond|Maximal|Inbound TCP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerUDPPackets|Inbound UDP packets to trigger DDoS mitigation|CountPerSecond|Maximal|Inbound UDP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation|CountPerSecond|Maximal|Inbound SYN packets to trigger DDoS mitigation|No Dimensions|
|VipAvailability|Data Path Availability|Antal|Medel|Average IP Address availability per time duration|Port|
|ByteCount|Byte Count|Antal|Totalt|Total number of Bytes transmitted within time period|Port, Direction|
|PacketCount|Packet Count|Antal|Totalt|Total number of Packets transmitted within time period|Port, Direction|
|SynCount|SYN Count|Antal|Totalt|Total number of SYN Packets transmitted within time period|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ApplicationRuleHit|Application rules hit count|Antal|Totalt|Number of times Application rules were hit|Status, Reason, Protocol|
|NetworkRuleHit|Network rules hit count|Antal|Totalt|Number of times Network rules were hit|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Dataflöde|Dataflöde|BytesPerSecond|Totalt|Number of bytes per second the Application Gateway has served|No Dimensions|
|UnhealthyHostCount|Unhealthy Host Count|Antal|Medel|Number of unhealthy backend hosts|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count|Antal|Medel|Number of healthy backend hosts|BackendSettingsPool|
|TotalRequests|Totalt antal förfrågningar|Antal|Totalt|Count of successful requests that Application Gateway has served|BackendSettingsPool|
|FailedRequests|Misslyckade begäranden|Antal|Totalt|Count of failed requests that Application Gateway has served|BackendSettingsPool|
|ResponseStatus|Response Status|Antal|Totalt|Http response status returned by Application Gateway|HttpStatusGroup|
|CurrentConnections|Current Connections|Antal|Totalt|Count of current connections established with Application Gateway|No Dimensions|
|CapacityUnits|Current Capacity Units|Antal|Medel|Capacity Units consumed|No Dimensions|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth|BytesPerSecond|Medel|Average site-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SBandwidth|Gateway P2S Bandwidth|BytesPerSecond|Medel|Average point-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SConnectionCount|P2S Connection Count|Antal|Maximal|Point-to-site connection count of a gateway|Protokoll|
|TunnelAverageBandwidth|Tunnel Bandwidth|BytesPerSecond|Medel|Average bandwidth of a tunnel in bytes per second|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tunnel Egress Bytes|Bytes|Totalt|Outgoing bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes|Bytes|Totalt|Incoming bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel Egress Packets|Antal|Totalt|Outgoing packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Packets|Antal|Totalt|Incoming packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop|Antal|Totalt|Outgoing packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop|Antal|Totalt|Incoming packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|Bits ingressing Azure per second|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|Bits egressing Azure per second|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Medel|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Medel|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QpsByEndpoint|Queries by Endpoint Returned|Antal|Totalt|Number of times a Traffic Manager endpoint was returned in the given time frame|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpoint Status by Endpoint|Antal|Maximal|1 if an endpoint's probe status is "Enabled", 0 otherwise.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Probes Failed|Procent|Medel|% of connectivity monitoring probes failed|No Dimensions|
|AverageRoundtripMs|Avg. Round-trip Time (ms)|MilliSeconds|Medel|Average network round-trip time (ms) for connectivity monitoring probes sent between source and destination|No Dimensions|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RequestCount|Antal begäranden|Antal|Totalt|The number of client requests served by the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Request Size|Bytes|Totalt|The number of bytes sent as requests from clients to the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Response Size|Bytes|Totalt|The number of bytes sent as responses from HTTP/S proxy to clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Backend Request Count|Antal|Totalt|The number of requests sent from the HTTP/S proxy to backends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Backend Request Latency|MilliSeconds|Medel|The time calculated from when the request was sent by the HTTP/S proxy to the backend until the HTTP/S proxy received the last response byte from the backend|Backend|
|TotalLatency|Total Latency|MilliSeconds|Medel|The time calculated from when the client request was received by the HTTP/S proxy until the client acknowledged the last response byte from the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Backend Health Percentage|Procent|Medel|The percentage of successful health probes from the HTTP/S proxy to backends|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count|Antal|Totalt|The number of client requests processed by the Web Application Firewall|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|registration.all|Registration Operations|Antal|Totalt|The count of all successful registration operations (creations updates queries and deletions). |No Dimensions|
|registration.create|Registration Create Operations|Antal|Totalt|The count of all successful registration creations.|No Dimensions|
|registration.update|Registration Update Operations|Antal|Totalt|The count of all successful registration updates.|No Dimensions|
|registration.get|Registration Read Operations|Antal|Totalt|The count of all successful registration queries.|No Dimensions|
|registration.delete|Registration Delete Operations|Antal|Totalt|The count of all successful registration deletions.|No Dimensions|
|incoming|Incoming Messages|Antal|Totalt|The count of all successful send API calls. |No Dimensions|
|incoming.scheduled|Scheduled Push Notifications Sent|Antal|Totalt|Scheduled Push Notifications Canceled|No Dimensions|
|incoming.scheduled.cancel|Scheduled Push Notifications Canceled|Antal|Totalt|Scheduled Push Notifications Canceled|No Dimensions|
|scheduled.pending|Pending Scheduled Notifications|Antal|Totalt|Pending Scheduled Notifications|No Dimensions|
|installation.all|Installation Management Operations|Antal|Totalt|Installation Management Operations|No Dimensions|
|installation.get|Get Installation Operations|Antal|Totalt|Get Installation Operations|No Dimensions|
|installation.upsert|Create or Update Installation Operations|Antal|Totalt|Create or Update Installation Operations|No Dimensions|
|installation.patch|Patch Installation Operations|Antal|Totalt|Patch Installation Operations|No Dimensions|
|installation.delete|Delete Installation Operations|Antal|Totalt|Delete Installation Operations|No Dimensions|
|outgoing.allpns.success|Successful notifications|Antal|Totalt|The count of all successful notifications.|No Dimensions|
|outgoing.allpns.invalidpayload|Payload Errors|Antal|Totalt|The count of pushes that failed because the PNS returned a bad payload error.|No Dimensions|
|outgoing.allpns.pnserror|External Notification System Errors|Antal|Totalt|The count of pushes that failed because there was a problem communicating with the PNS (excludes authentication problems).|No Dimensions|
|outgoing.allpns.channelerror|Channel Errors|Antal|Totalt|The count of pushes that failed because the channel was invalid not associated with the correct app throttled or expired.|No Dimensions|
|outgoing.allpns.badorexpiredchannel|Bad or Expired Channel Errors|Antal|Totalt|The count of pushes that failed because the channel/token/registrationId in the registration was expired or invalid.|No Dimensions|
|outgoing.wns.success|WNS Successful Notifications|Antal|Totalt|The count of all successful notifications.|No Dimensions|
|outgoing.wns.invalidcredentials|WNS Authorization Errors (Invalid Credentials)|Antal|Totalt|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked. (Windows Live does not recognize the credentials).|No Dimensions|
|outgoing.wns.badchannel|WNS Bad Channel Error|Antal|Totalt|The count of pushes that failed because the ChannelURI in the registration was not recognized (WNS status: 404 not found).|No Dimensions|
|outgoing.wns.expiredchannel|WNS Expired Channel Error|Antal|Totalt|The count of pushes that failed because the ChannelURI is expired (WNS status: 410 Gone).|No Dimensions|
|outgoing.wns.throttled|WNS Throttled Notifications|Antal|Totalt|The count of pushes that failed because WNS is throttling this app (WNS status: 406 Not Acceptable).|No Dimensions|
|outgoing.wns.tokenproviderunreachable|WNS Authorization Errors (Unreachable)|Antal|Totalt|Windows Live is not reachable.|No Dimensions|
|outgoing.wns.invalidtoken|WNS Authorization Errors (Invalid Token)|Antal|Totalt|The token provided to WNS is not valid (WNS status: 401 Unauthorized).|No Dimensions|
|outgoing.wns.wrongtoken|WNS Authorization Errors (Wrong Token)|Antal|Totalt|The token provided to WNS is valid but for another application (WNS status: 403 Forbidden). This can happen if the ChannelURI in the registration is associated with another app. Check that the client app is associated with the same app whose credentials are in the notification hub.|No Dimensions|
|outgoing.wns.invalidnotificationformat|WNS Invalid Notification Format|Antal|Totalt|The format of the notification is invalid (WNS status: 400). Note that WNS does not reject all invalid payloads.|No Dimensions|
|outgoing.wns.invalidnotificationsize|WNS Invalid Notification Size Error|Antal|Totalt|The notification payload is too large (WNS status: 413).|No Dimensions|
|outgoing.wns.channelthrottled|WNS Channel Throttled|Antal|Totalt|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-NotificationStatus:channelThrottled).|No Dimensions|
|outgoing.wns.channeldisconnected|WNS Channel Disconnected|Antal|Totalt|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.dropped|WNS Dropped Notifications|Antal|Totalt|The notification was dropped because the ChannelURI in the registration is throttled (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.pnserror|WNS Errors|Antal|Totalt|Notification not delivered because of errors communicating with WNS.|No Dimensions|
|outgoing.wns.authenticationerror|WNS Authentication Errors|Antal|Totalt|Notification not delivered because of errors communicating with Windows Live invalid credentials or wrong token.|No Dimensions|
|outgoing.apns.success|APNS Successful Notifications|Antal|Totalt|The count of all successful notifications.|No Dimensions|
|outgoing.apns.invalidcredentials|APNS Authorization Errors|Antal|Totalt|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.apns.badchannel|APNS Bad Channel Error|Antal|Totalt|The count of pushes that failed because the token is invalid (APNS binary protocol status code: 8. APNS HTTP protocol status code: 400 with "BadDeviceToken").|No Dimensions|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Antal|Totalt|The count of token that were invalidated by the APNS feedback channel.|No Dimensions|
|outgoing.apns.invalidnotificationsize|APNS Invalid Notification Size Error|Antal|Totalt|The count of pushes that failed because the payload was too large (APNS binary protocol status code: 7).|No Dimensions|
|outgoing.apns.pnserror|APNS Errors|Antal|Totalt|The count of pushes that failed because of errors communicating with APNS.|No Dimensions|
|outgoing.gcm.success|GCM Successful Notifications|Antal|Totalt|The count of all successful notifications.|No Dimensions|
|outgoing.gcm.invalidcredentials|GCM Authorization Errors (Invalid Credentials)|Antal|Totalt|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.gcm.badchannel|GCM Bad Channel Error|Antal|Totalt|The count of pushes that failed because the registrationId in the registration was not recognized (GCM result: Invalid Registration).|No Dimensions|
|outgoing.gcm.expiredchannel|GCM Expired Channel Error|Antal|Totalt|The count of pushes that failed because the registrationId in the registration was expired (GCM result: NotRegistered).|No Dimensions|
|outgoing.gcm.throttled|GCM Throttled Notifications|Antal|Totalt|The count of pushes that failed because GCM throttled this app (GCM status code: 501-599 or result:Unavailable).|No Dimensions|
|outgoing.gcm.invalidnotificationformat|GCM Invalid Notification Format|Antal|Totalt|The count of pushes that failed because the payload was not formatted correctly (GCM result: InvalidDataKey or InvalidTtl).|No Dimensions|
|outgoing.gcm.invalidnotificationsize|GCM Invalid Notification Size Error|Antal|Totalt|The count of pushes that failed because the payload was too large (GCM result: MessageTooBig).|No Dimensions|
|outgoing.gcm.wrongchannel|GCM Wrong Channel Error|Antal|Totalt|The count of pushes that failed because the registrationId in the registration is not associated to the current app (GCM result: InvalidPackageName).|No Dimensions|
|outgoing.gcm.pnserror|GCM Errors|Antal|Totalt|The count of pushes that failed because of errors communicating with GCM.|No Dimensions|
|outgoing.gcm.authenticationerror|GCM Authentication Errors|Antal|Totalt|The count of pushes that failed because the PNS did not accept the provided credentials the credentials are blocked or the SenderId is not correctly configured in the app (GCM result: MismatchedSenderId).|No Dimensions|
|outgoing.mpns.success|MPNS Successful Notifications|Antal|Totalt|The count of all successful notifications.|No Dimensions|
|outgoing.mpns.invalidcredentials|MPNS Invalid Credentials|Antal|Totalt|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.mpns.badchannel|MPNS Bad Channel Error|Antal|Totalt|The count of pushes that failed because the ChannelURI in the registration was not recognized (MPNS status: 404 not found).|No Dimensions|
|outgoing.mpns.throttled|MPNS Throttled Notifications|Antal|Totalt|The count of pushes that failed because MPNS is throttling this app (WNS MPNS: 406 Not Acceptable).|No Dimensions|
|outgoing.mpns.invalidnotificationformat|MPNS Invalid Notification Format|Antal|Totalt|The count of pushes that failed because the payload of the notification was too large.|No Dimensions|
|outgoing.mpns.channeldisconnected|MPNS Channel Disconnected|Antal|Totalt|The count of pushes that failed because the ChannelURI in the registration was disconnected (MPNS status: 412 not found).|No Dimensions|
|outgoing.mpns.dropped|MPNS Dropped Notifications|Antal|Totalt|The count of pushes that were dropped by MPNS (MPNS response header: X-NotificationStatus: QueueFull or Suppressed).|No Dimensions|
|outgoing.mpns.pnserror|MPNS Errors|Antal|Totalt|The count of pushes that failed because of errors communicating with MPNS.|No Dimensions|
|outgoing.mpns.authenticationerror|MPNS Authentication Errors|Antal|Totalt|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|notificationhub.pushes|All Outgoing Notifications|Antal|Totalt|All outgoing notifications of the notification hub|No Dimensions|
|incoming.all.requests|All Incoming Requests|Antal|Totalt|Total incoming requests for a notification hub|No Dimensions|
|incoming.all.failedrequests|All Incoming Failed Requests|Antal|Totalt|Total incoming failed requests for a notification hub|No Dimensions|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Antal|Medel|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Antal|Medel|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Antal|Medel|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Antal|Medel|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec|Antal|Medel|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Antal|Medel|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Antal|Medel|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Antal|Medel|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Antal|Medel|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Antal|Medel|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Antal|Medel|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Antal|Medel|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Antal|Medel|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Antal|Medel|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Antal|Medel|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Antal|Medel|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Antal|Medel|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Antal|Medel|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Antal|Medel|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Antal|Medel|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Antal|Medel|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Antal|Medel|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Antal|Medel|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Antal|Medel|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Total Bytes|Antal|Medel|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Antal|Medel|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Antal|Medel|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Antal|Medel|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Antal|Medel|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Antal|Medel|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Antal|Medel|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Antal|Medel|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Antal|Medel|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Antal|Medel|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Antal|Medel|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Antal|Medel|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Antal|Medel|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Antal|Medel|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Antal|Medel|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Antal|Medel|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Antal|Medel|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Antal|Medel|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Antal|Medel|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Antal|Medel|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Antal|Medel|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Antal|Medel|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Antal|Medel|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Antal|Medel|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Antal|Medel|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processer|Antal|Medel|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Antal|Medel|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Antal|Medel|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Användare|Antal|Medel|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Antal|Medel|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Antal|Medel|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|Antal|Medel|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Antal|Medel|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Antal|Medel|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Antal|Medel|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Antal|Medel|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Antal|Medel|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Available MBytes|Antal|Medel|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|Antal|Medel|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|Antal|Medel|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|Antal|Medel|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|Antal|Medel|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Antal|Medel|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Processor Queue Length|Antal|Medel|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Antal|Totalt|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Uppdatera|Uppdatera|Antal|Medel|Uppdatera|Computer, Product, Classification, UpdateState, Optional, Approved|
|Händelse|Händelse|Antal|Medel|Händelse|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueryDuration|Query Duration|Milliseconds|Medel|DAX Query duration in last interval|No Dimensions|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Antal|Medel|Number of jobs in the queue of the query thread pool.|No Dimensions|
|qpu_high_utilization_metric|QPU High Utilization|Antal|Totalt|QPU High Utilization In Last Minute, 1 For High QPU Utilization, Otherwise 0|No Dimensions|
|memory_metric|Minne|Bytes|Medel|Memory. Range 0-3 GB for A1, 0-5 GB for A2, 0-10 GB for A3, 0-25 GB for A4, 0-50 GB for A5 and 0-100 GB for A6|No Dimensions|
|memory_thrashing_metric|Memory Thrashing|Procent|Medel|Average memory thrashing.|No Dimensions|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Antal|Totalt|Successful ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Antal|Totalt|ClientError on ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Antal|Totalt|ServerError on ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Antal|Totalt|Successful SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Antal|Totalt|ClientError on SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Antal|Totalt|ServerError on SenderConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Antal|Totalt|Total ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Antal|Totalt|Total SenderConnections requests for Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Antal|Totalt|Total ActiveConnections for Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Antal|Totalt|Total ActiveListeners for Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Antal|Totalt|Total BytesTransferred for Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Antal|Totalt|Total ListenerDisconnects for Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Antal|Totalt|Total SenderDisconnects for Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Sekunder|Medel|Average search latency for the search service|No Dimensions|
|SearchQueriesPerSecond|Search queries per second|CountPerSecond|Medel|Search queries per second for the search service|No Dimensions|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Procent|Medel|Percentage of search queries that were throttled for the search service|No Dimensions|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Antal|Totalt|Total successful requests for a namespace (Preview)|EntityName|
|ServerErrors|Server Errors. (Förhandsversion)|Antal|Totalt|Server Errors for Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|UserErrors|User Errors. (Förhandsversion)|Antal|Totalt|User Errors for Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|ThrottledRequests|Throttled Requests. (Förhandsversion)|Antal|Totalt|Throttled Requests for Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|IncomingRequests|Incoming Requests (Preview)|Antal|Totalt|Incoming Requests for Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|IncomingMessages|Incoming Messages (Preview)|Antal|Totalt|Incoming Messages for Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|OutgoingMessages|Outgoing Messages (Preview)|Antal|Totalt|Outgoing Messages for Microsoft.ServiceBus. (Förhandsversion)|EntityName|
|ActiveConnections|ActiveConnections (Preview)|Antal|Totalt|Total Active Connections for Microsoft.ServiceBus. (Förhandsversion)|No Dimensions|
|Storlek|Size (Preview)|Bytes|Medel|Size of an Queue/Topic in Bytes. (Förhandsversion)|EntityName|
|Meddelanden|Count of messages in a Queue/Topic. (Förhandsversion)|Antal|Medel|Count of messages in a Queue/Topic. (Förhandsversion)|EntityName|
|ActiveMessages|Count of active messages in a Queue/Topic. (Förhandsversion)|Antal|Medel|Count of active messages in a Queue/Topic. (Förhandsversion)|EntityName|
|DeadletteredMessages|Count of dead-lettered messages in a Queue/Topic. (Förhandsversion)|Antal|Medel|Count of dead-lettered messages in a Queue/Topic. (Förhandsversion)|EntityName|
|ScheduledMessages|Count of scheduled messages in a Queue/Topic. (Förhandsversion)|Antal|Medel|Count of scheduled messages in a Queue/Topic. (Förhandsversion)|EntityName|
|CPUXNS|CPU usage per namespace|Procent|Maximal|Service bus premium namespace CPU usage metric|No Dimensions|
|WSXNS|Memory size usage per namespace|Procent|Maximal|Service bus premium namespace memory usage metric|No Dimensions|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Antal|Medel|Cpu allocated to this container in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Medel|Memory allocated to this container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Antal|Medel|Actual CPU usage in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Medel|Actual memory usage in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Medel|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Medel|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Antal|Medel|Status of Service Fabric Mesh application|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Antal|Medel|Health Status of a service in Service Fabric Mesh application|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Antal|Medel|Health Status of a service replica in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Antal|Medel|Status of the container in Service Fabric Mesh application|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Antal|Medel|Restart count of a container in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ConnectionCount|Connection Count|Antal|Maximal|The amount of user connection.|Slutpunkt|
|MessageCount|Message Count|Antal|Totalt|The total amount of messages.|No Dimensions|
|InboundTraffic|Inkommande trafik|Bytes|Totalt|The inbound traffic of service|No Dimensions|
|OutboundTraffic|Utgående trafik|Bytes|Totalt|The outbound traffic of service|No Dimensions|
|UserErrors|User Errors|Procent|Maximal|The percentage of user errors|No Dimensions|
|SystemErrors|System Errors|Procent|Maximal|The percentage of system errors|No Dimensions|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Medel|Data space allocated. Not applicable to data warehouses.|No Dimensions|
|app_cpu_billed|App CPU billed|Antal|Totalt|App CPU billed. Applies to serverless databases.|No Dimensions|
|app_cpu_percent|App CPU percentage|Procent|Medel|App CPU percentage. Applies to serverless databases.|No Dimensions|
|app_memory_percent|App memory used percentage|Procent|Medel|App memory used percentage. Applies to serverless databases.|No Dimensions|
|blocked_by_firewall|Blocked by Firewall|Antal|Totalt|Blocked by Firewall|No Dimensions|
|cache_hit_percent|Cache hit percentage|Procent|Maximal|Cache hit percentage. Applies only to data warehouses.|No Dimensions|
|cache_used_percent|Cache used percentage|Procent|Maximal|Cache used percentage. Applies only to data warehouses.|No Dimensions|
|connection_failed|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|No Dimensions|
|connection_successful|Successful Connections|Antal|Totalt|Successful Connections|No Dimensions|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|No Dimensions|
|cpu_limit|CPU limit|Antal|Medel|CPU limit. Applies to vCore-based databases.|No Dimensions|
|cpu_used|CPU used|Antal|Medel|CPU used. Applies to vCore-based databases.|No Dimensions|
|deadlock|Deadlocks|Antal|Totalt|Deadlocks. Not applicable to data warehouses.|No Dimensions|
|diff_backup_size_bytes|Differential backup storage size|Bytes|Maximal|Cumulative differential backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|dtu_limit|DTU Limit|Antal|Medel|DTU Limit. Applies to DTU-based databases.|No Dimensions|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU percentage. Applies to DTU-based databases.|No Dimensions|
|dtu_used|DTU used|Antal|Medel|DTU used. Applies to DTU-based databases.|No Dimensions|
|dw_cpu_percent|DW node level CPU percentage|Procent|Medel|DW node level CPU percentage|DwLogicalNodeId|
|dw_physical_data_read_percent|DW node level Data IO percentage|Procent|Medel|DW node level Data IO percentage|DwLogicalNodeId|
|dwu_consumption_percent|DWU percentage|Procent|Maximal|DWU percentage. Applies only to data warehouses.|No Dimensions|
|dwu_limit|DWU limit|Antal|Maximal|DWU limit. Applies only to data warehouses.|No Dimensions|
|dwu_used|DWU used|Antal|Maximal|DWU used. Applies only to data warehouses.|No Dimensions|
|full_backup_size_bytes|Full backup storage size|Bytes|Maximal|Cumulative full backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|local_tempdb_usage_percent|Local tempdb percentage|Procent|Medel|Local tempdb percentage. Applies only to data warehouses.|No Dimensions|
|log_backup_size_bytes|Log backup storage size|Bytes|Maximal|Cumulative log backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|log_write_percent|Log IO percentage|Procent|Medel|Log IO percentage. Not applicable to data warehouses.|No Dimensions|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|No Dimensions|
|sessions_percent|Sessions percentage|Procent|Medel|Sessions percentage. Not applicable to data warehouses.|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Procent|Maximal|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Procent|Maximal|This metric is a placeholder and not populated at this time.|No Dimensions|
|lagring|Data space used|Bytes|Maximal|Total database size. Not applicable to data warehouses.|No Dimensions|
|storage_percent|Data space used percent|Procent|Maximal|Database size percentage. Not applicable to data warehouses or hyperscale databases.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Antal|Maximal|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Antal|Maximal|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Procent|Maximal|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Procent|Medel|Workers percentage. Not applicable to data warehouses.|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Procent|Medel|In-Memory OLTP storage percent. Not applicable to data warehouses.|No Dimensions|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Medel|Data space allocated|No Dimensions|
|allocated_data_storage_percent|Data space allocated percent|Procent|Maximal|Data space allocated percent|No Dimensions|
|cpu_limit|CPU limit|Antal|Medel|CPU limit. Applies to vCore-based elastic pools.|No Dimensions|
|cpu_percent|CPU-procent|Procent|Medel|CPU-procent|No Dimensions|
|cpu_used|CPU used|Antal|Medel|CPU used. Applies to vCore-based elastic pools.|No Dimensions|
|dtu_consumption_percent|DTU-procent|Procent|Medel|DTU percentage. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_limit|eDTU limit|Antal|Medel|eDTU limit. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_used|eDTU used|Antal|Medel|eDTU used. Applies to DTU-based elastic pools.|No Dimensions|
|log_write_percent|Log IO percentage|Procent|Medel|Log IO percentage|No Dimensions|
|physical_data_read_percent|Data IO-procent|Procent|Medel|Data IO-procent|No Dimensions|
|sessions_percent|Sessions percentage|Procent|Medel|Sessions percentage|No Dimensions|
|storage_limit|Data max size|Bytes|Medel|Storage limit|No Dimensions|
|storage_percent|Data space used percent||Procent|Medel|Storage percentage|No Dimensions|
|storage_used|Data space used|Bytes|Medel|Använt lagringsutrymme|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Procent|Maximal|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Procent|Maximal|This metric is a placeholder and not populated at this time.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Antal|Maximal|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Antal|Maximal|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Procent|Maximal|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Procent|Medel|Workers percentage|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Procent|Medel|In-Memory OLTP storage percent|No Dimensions|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|avg_cpu_percent|Average CPU percentage|Procent|Medel|Average CPU percentage|No Dimensions|
|io_bytes_read|IO bytes read|Bytes|Medel|IO bytes read|No Dimensions|
|io_requests|IO requests count|Antal|Medel|IO requests count|No Dimensions|
|io_bytes_written|IO bytes written|Bytes|Medel|IO bytes written|No Dimensions|
|reserved_storage_mb|Storage space reserved|Antal|Medel|Storage space reserved|No Dimensions|
|storage_space_used_mb|Storage space used|Antal|Medel|Storage space used|No Dimensions|
|virtual_core_count|Virtual core count|Antal|Medel|Virtual core count|No Dimensions|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity|Bytes|Medel|Account used capacity|No Dimensions|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Ingångshändelser|Ingångshändelser|Bytes|Totalt|The amount of ingress data, in bytes. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Utgående|Utgående|Bytes|Totalt|The amount of egress data, in bytes. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Medel|The average latency used by Azure Storage to process a successful request, in milliseconds. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Medel|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|The percentage of availability for the storage service or the specified API operation. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity|Bytes|Medel|The amount of storage used by the storage account’s Blob service in bytes.|BlobType, Tier|
|BlobCount|Blob Count|Antal|Totalt|The number of Blob in the storage account’s Blob service.|BlobType|       |BlobCount|Blob Count|Antal|Medel|The number of Blob in the storage account’s Blob service.|BlobType, Tier|
|ContainerCount|Blob Container Count|Antal|Medel|The number of containers in the storage account’s Blob service.|No Dimensions|
|IndexCapacity|Index Capacity|Bytes|Medel|The amount of storage used by ADLS Gen2 (Hierarchical) Index in bytes.|No Dimensions|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Ingångshändelser|Ingångshändelser|Bytes|Totalt|The amount of ingress data, in bytes. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Utgående|Utgående|Bytes|Totalt|The amount of egress data, in bytes. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Medel|The average latency used by Azure Storage to process a successful request, in milliseconds. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Medel|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|The percentage of availability for the storage service or the specified API operation. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|FileCapacity|File Capacity|Bytes|Medel|The amount of storage used by the storage account’s File service in bytes.|No Dimensions|
|FileCount|File Count|Antal|Medel|The number of file in the storage account’s File service.|No Dimensions|
|FileShareCount|File Share Count|Antal|Medel|The number of file shares in the storage account’s File service.|No Dimensions|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Ingångshändelser|Ingångshändelser|Bytes|Totalt|The amount of ingress data, in bytes. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Utgående|Utgående|Bytes|Totalt|The amount of egress data, in bytes. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Medel|The average latency used by Azure Storage to process a successful request, in milliseconds. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Medel|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|The percentage of availability for the storage service or the specified API operation. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity|Bytes|Medel|The amount of storage used by the storage account’s Queue service in bytes.|No Dimensions|
|QueueCount|Queue Count|Antal|Medel|The number of queue in the storage account’s Queue service.|No Dimensions|
|QueueMessageCount|Queue Message Count|Antal|Medel|The approximate number of queue messages in the storage account’s Queue service.|No Dimensions|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Ingångshändelser|Ingångshändelser|Bytes|Totalt|The amount of ingress data, in bytes. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Utgående|Utgående|Bytes|Totalt|The amount of egress data, in bytes. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Medel|The average latency used by Azure Storage to process a successful request, in milliseconds. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Medel|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|The percentage of availability for the storage service or the specified API operation. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity|Bytes|Medel|The amount of storage used by the storage account’s Table service in bytes.|No Dimensions|
|TableCount|Table Count|Antal|Medel|The number of table in the storage account’s Table service.|No Dimensions|
|TableEntityCount|Table Entity Count|Antal|Medel|The number of table entities in the storage account’s Table service.|No Dimensions|
|Transaktioner|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Ingångshändelser|Ingångshändelser|Bytes|Totalt|The amount of ingress data, in bytes. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Authentication|
|Utgående|Utgående|Bytes|Totalt|The amount of egress data, in bytes. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Medel|The average latency used by Azure Storage to process a successful request, in milliseconds. I det här värdet ingår inte nätverksfördröjningen som anges i AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Medel|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication|
|Tillgänglighet|Tillgänglighet|Procent|Medel|The percentage of availability for the storage service or the specified API operation. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Sync Session Result|Antal|Medel|Metric that logs a value of 1 each time the Server Endpoint successfully completes a Sync Session with the Cloud Endpoint|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Files Synced|Antal|Totalt|Count of Files synced|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Files not syncing|Antal|Totalt|Count of files failed to sync|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes synced|Bytes|Totalt|Total file size transferred for Sync Sessions|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Server Online Status|Antal|Maximal|Metric that logs a value of 1 each time the registered server successfully records a heartbeat with the Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud tiering recall|Bytes|Totalt|Total size of data recalled by the server|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|ResourceUtilization|SU % Utilization|Procent|Maximal|SU % Utilization|LogicalName, PartitionId|
|InputEvents|Input Events|Antal|Totalt|Input Events|LogicalName, PartitionId|
|InputEventBytes|Input Event Bytes|Bytes|Totalt|Input Event Bytes|LogicalName, PartitionId|
|LateInputEvents|Late Input Events|Antal|Totalt|Late Input Events|LogicalName, PartitionId|
|OutputEvents|Output Events|Antal|Totalt|Output Events|LogicalName, PartitionId|
|ConversionErrors|Data Conversion Errors|Antal|Totalt|Data Conversion Errors|LogicalName, PartitionId|
|Fel|Runtime Errors|Antal|Totalt|Runtime Errors|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Out of order Events|Antal|Totalt|Out of order Events|LogicalName, PartitionId|
|AMLCalloutRequests|Function Requests|Antal|Totalt|Function Requests|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Failed Function Requests|Antal|Totalt|Failed Function Requests|LogicalName, PartitionId|
|AMLCalloutInputEvents|Function Events|Antal|Totalt|Function Events|LogicalName, PartitionId|
|DeserializationError|Input Deserialization Errors|Antal|Totalt|Input Deserialization Errors|LogicalName, PartitionId|
|EarlyInputEvents|Early Input Events|Antal|Totalt|Early Input Events|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Watermark Delay|Sekunder|Maximal|Watermark Delay|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Backlogged Input Events|Antal|Maximal|Backlogged Input Events|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Input Sources Received|Antal|Totalt|Input Sources Received|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Antal|Totalt|Count of messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Antal|Totalt|Count of invalid messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Totalt|Count of bytes read from all event sources|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Totalt|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Antal|Totalt|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Sekunder|Maximal|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Antal|Medel|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Antal|Maximal|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Antal|Maximal|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Antal|Totalt|Count of messages read from the event source|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Antal|Totalt|Count of invalid messages read from the event source|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Totalt|Count of bytes read from the event source|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Totalt|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Antal|Totalt|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Sekunder|Maximal|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Antal|Medel|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Antal|Maximal|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Antal|Maximal|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Medel|Average disk throughput due to read operations over the sample period.|No Dimensions|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Medel|Average disk throughput due to write operations over the sample period.|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Totalt|Total disk throughput due to read operations over the sample period.|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Totalt|Total disk throughput due to write operations over the sample period.|No Dimensions|
|DiskReadOperations|Disk Read Operations|Antal|Totalt|The number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskWriteOperations|Disk Write Operations|Antal|Totalt|The number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Medel|The average number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Medel|The average number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskReadLatency|Disk Read Latency|Milliseconds|Medel|Total read latency. The sum of the device and kernel read latencies.|No Dimensions|
|DiskWriteLatency|Disk Write Latency|Milliseconds|Medel|Total write latency. The sum of the device and kernel write latencies.|No Dimensions|
|NetworkInBytesPerSecond|Network In Bytes/Sec|BytesPerSecond|Medel|Average network throughput for received traffic.|No Dimensions|
|NetworkOutBytesPerSecond|Network Out Bytes/Sec|BytesPerSecond|Medel|Average network throughput for transmitted traffic.|No Dimensions|
|Nätverk – inkommande|Nätverk – inkommande|Bytes|Totalt|Total network throughput for received traffic.|No Dimensions|
|Nätverk – utgående|Nätverk – utgående|Bytes|Totalt|Total network throughput for transmitted traffic.|No Dimensions|
|MemoryUsed|Memory Used|Bytes|Medel|The amount of machine memory that is in use by the VM.|No Dimensions|
|MemoryGranted|Memory Granted|Bytes|Medel|The amount of memory that was granted to the VM by the host. Memory is not granted to the host until it is touched one time and granted memory may be swapped out or ballooned away if the VMkernel needs the memory.|No Dimensions|
|MemoryActive|Memory Active|Bytes|Medel|The amount of memory used by the VM in the past small window of time. This is the "true" number of how much memory the VM currently has need of. Additional, unused memory may be swapped out or ballooned with no impact to the guest's performance.|No Dimensions|
|Procent CPU|Procent CPU|Procent|Medel|The CPU utilization. This value is reported with 100% representing all processor cores on the system. As an example, a 2-way VM using 50% of a four-core system is completely using two cores.|No Dimensions|
|PercentageCpuReady|Percentage CPU Ready|Milliseconds|Totalt|Ready time is the time spend waiting for CPU(s) to become available in the past update interval.|No Dimensions|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Memory Percentage|Procent|Medel|Memory Percentage|Instans|
|DiskQueueLength|Disk Queue Length|Antal|Medel|Disk Queue Length|Instans|
|HttpQueueLength|Http Queue Length|Antal|Medel|Http Queue Length|Instans|
|BytesReceived|Data In|Bytes|Totalt|Data In|Instans|
|BytesSent|Data Out|Bytes|Totalt|Data Out|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluding functions)

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Sekunder|Totalt|CPU Time|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data In|Bytes|Totalt|Data In|Instans|
|BytesSent|Data Out|Bytes|Totalt|Data Out|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Http 2xx|Antal|Totalt|Http 2xx|Instans|
|Http3xx|Http 3xx|Antal|Totalt|Http 3xx|Instans|
|Http401|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http 4xx|Antal|Totalt|Http 4xx|Instans|
|Http5xx|Http Server Errors|Antal|Totalt|Http Server Errors|Instans|
|MemoryWorkingSet|Memory working set|Bytes|Medel|Memory working set|Instans|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Medel|Average memory working set|Instans|
|AverageResponseTime|Average Response Time|Sekunder|Medel|Average Response Time|Instans|
|AppConnections|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Handles|Handle Count|Antal|Medel|Handle Count|Instans|
|Threads|Thread Count|Antal|Medel|Thread Count|Instans|
|PrivateBytes|Private Bytes|Bytes|Medel|Private Bytes|Instans|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Totalt|IO Read Bytes Per Second|Instans|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Totalt|IO Write Bytes Per Second|Instans|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Totalt|IO Other Bytes Per Second|Instans|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Totalt|IO Read Operations Per Second|Instans|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Totalt|IO Write Operations Per Second|Instans|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Totalt|IO Other Operations Per Second|Instans|
|RequestsInApplicationQueue|Requests In Application Queue|Antal|Medel|Requests In Application Queue|Instans|
|CurrentAssemblies|Current Assemblies|Antal|Medel|Current Assemblies|Instans|
|TotalAppDomains|Total App Domains|Antal|Medel|Total App Domains|Instans|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Antal|Medel|Total App Domains Unloaded|Instans|
|Gen0Collections|Gen 0 Garbage Collections|Antal|Totalt|Gen 0 Garbage Collections|Instans|
|Gen1Collections|Gen 1 Garbage Collections|Antal|Totalt|Gen 1 Garbage Collections|Instans|
|Gen2Collections|Gen 2 Garbage Collections|Antal|Totalt|Gen 2 Garbage Collections|Instans|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|BytesReceived|Data In|Bytes|Totalt|Data In|Instans|
|BytesSent|Data Out|Bytes|Totalt|Data Out|Instans|
|Http5xx|Http Server Errors|Antal|Totalt|Http Server Errors|Instans|
|MemoryWorkingSet|Memory working set|Bytes|Medel|Memory working set|Instans|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Medel|Average memory working set|Instans|
|FunctionExecutionUnits|Function Execution Units|MB / Milliseconds|Totalt|[Function Execution Units](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instans|
|FunctionExecutionCount|Function Execution Count|Antal|Totalt|Function Execution Count|Instans|
|PrivateBytes|Private Bytes|Bytes|Medel|Private Bytes|Instans|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Totalt|IO Read Bytes Per Second|Instans|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Totalt|IO Write Bytes Per Second|Instans|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Totalt|IO Other Bytes Per Second|Instans|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Totalt|IO Read Operations Per Second|Instans|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Totalt|IO Write Operations Per Second|Instans|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Totalt|IO Other Operations Per Second|Instans|
|RequestsInApplicationQueue|Requests In Application Queue|Antal|Medel|Requests In Application Queue|Instans|
|CurrentAssemblies|Current Assemblies|Antal|Medel|Current Assemblies|Instans|
|TotalAppDomains|Total App Domains|Antal|Medel|Total App Domains|Instans|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Antal|Medel|Total App Domains Unloaded|Instans|
|Gen0Collections|Gen 0 Garbage Collections|Antal|Totalt|Gen 0 Garbage Collections|Instans|
|Gen1Collections|Gen 1 Garbage Collections|Antal|Totalt|Gen 1 Garbage Collections|Instans|
|Gen2Collections|Gen 2 Garbage Collections|Antal|Totalt|Gen 2 Garbage Collections|Instans|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Sekunder|Totalt|CPU Time|Instans|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data In|Bytes|Totalt|Data In|Instans|
|BytesSent|Data Out|Bytes|Totalt|Data Out|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Http 2xx|Antal|Totalt|Http 2xx|Instans|
|Http3xx|Http 3xx|Antal|Totalt|Http 3xx|Instans|
|Http401|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http 4xx|Antal|Totalt|Http 4xx|Instans|
|Http5xx|Http Server Errors|Antal|Totalt|Http Server Errors|Instans|
|MemoryWorkingSet|Memory working set|Bytes|Medel|Memory working set|Instans|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Medel|Average memory working set|Instans|
|AverageResponseTime|Average Response Time|Sekunder|Medel|Average Response Time|Instans|
|FunctionExecutionUnits|Function Execution Units|Antal|Totalt|Function Execution Units|Instans|
|FunctionExecutionCount|Function Execution Count|Antal|Totalt|Function Execution Count|Instans|
|AppConnections|Anslutningar|Antal|Medel|Anslutningar|Instans|
|Handles|Handle Count|Antal|Medel|Handle Count|Instans|
|Threads|Thread Count|Antal|Medel|Thread Count|Instans|
|PrivateBytes|Private Bytes|Bytes|Medel|Private Bytes|Instans|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Totalt|IO Read Bytes Per Second|Instans|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Totalt|IO Write Bytes Per Second|Instans|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Totalt|IO Other Bytes Per Second|Instans|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Totalt|IO Read Operations Per Second|Instans|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Totalt|IO Write Operations Per Second|Instans|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Totalt|IO Other Operations Per Second|Instans|
|RequestsInApplicationQueue|Requests In Application Queue|Antal|Medel|Requests In Application Queue|Instans|
|CurrentAssemblies|Current Assemblies|Antal|Medel|Current Assemblies|Instans|
|TotalAppDomains|Total App Domains|Antal|Medel|Total App Domains|Instans|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Antal|Medel|Total App Domains Unloaded|Instans|
|Gen0Collections|Gen 0 Garbage Collections|Antal|Totalt|Gen 0 Garbage Collections|Instans|
|Gen1Collections|Gen 1 Garbage Collections|Antal|Totalt|Gen 1 Garbage Collections|Instans|
|Gen2Collections|Gen 2 Garbage Collections|Antal|Totalt|Gen 2 Garbage Collections|Instans|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|Begäranden|Begäranden|Antal|Totalt|Begäranden|Instans|
|BytesReceived|Data In|Bytes|Totalt|Data In|Instans|
|BytesSent|Data Out|Bytes|Totalt|Data Out|Instans|
|Http101|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Http 2xx|Antal|Totalt|Http 2xx|Instans|
|Http3xx|Http 3xx|Antal|Totalt|Http 3xx|Instans|
|Http401|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Http 4xx|Antal|Totalt|Http 4xx|Instans|
|Http5xx|Http Server Errors|Antal|Totalt|Http Server Errors|Instans|
|AverageResponseTime|Average Response Time|Sekunder|Medel|Average Response Time|Instans|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Memory Percentage|Procent|Medel|Memory Percentage|Instans|
|DiskQueueLength|Disk Queue Length|Antal|Medel|Disk Queue Length|Instans|
|HttpQueueLength|Http Queue Length|Antal|Medel|Http Queue Length|Instans|
|ActiveRequests|Active Requests|Antal|Totalt|Active Requests|Instans|
|TotalFrontEnds|Total Front Ends|Antal|Medel|Total Front Ends|No Dimensions|
|SmallAppServicePlanInstances|Small App Service Plan Workers|Antal|Medel|Small App Service Plan Workers|No Dimensions|
|MediumAppServicePlanInstances|Medium App Service Plan Workers|Antal|Medel|Medium App Service Plan Workers|No Dimensions|
|LargeAppServicePlanInstances|Large App Service Plan Workers|Antal|Medel|Large App Service Plan Workers|No Dimensions|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Mått|Metric Display Name|Enhet|Aggregation Type|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|WorkersTotal|Total Workers|Antal|Medel|Total Workers|No Dimensions|
|WorkersAvailable|Available Workers|Antal|Medel|Available Workers|No Dimensions|
|WorkersUsed|Used Workers|Antal|Medel|Used Workers|No Dimensions|
|CpuPercentage|CPU-procent|Procent|Medel|CPU-procent|Instans|
|MemoryPercentage|Memory Percentage|Procent|Medel|Memory Percentage|Instans|

## <a name="next-steps"></a>Nästa steg
* [Read about metrics in Azure Monitor](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Export metrics to storage, Event Hub, or Log Analytics](resource-logs-overview.md)
