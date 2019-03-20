---
title: Övervaka Service Fabric-kluster i Azure | Microsoft Docs
description: Lär dig hur du övervakar ett kluster genom att visa Service Fabric-händelser, fråga EventStore APIs, övervaka prestandaräknare och visa hälsorapporter i de här självstudierna.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: b8234f286f4304b83969a01704735e1f3a7da2c6
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227448"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Självstudier: Övervaka Service Fabric-kluster i Azure

Övervakning och diagnostik är kritiska för utveckling, testning och distribution av arbetsbelastningar i alla miljöer i molnet. Den här självstudien är del två i en serie och visar hur du övervaka och diagnostisera ett Service Fabric-kluster med händelser, prestandaräknare och hälsorapporter.   Mer information finns i översikten om [klusterövervakning](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) och [infrastrukturövervakning](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Visa händelser för Service Fabric
> * Fråga EventStore APIs för klusterhändelser
> * Övervaka infrastruktur/samla in prestandaräknare
> * Visa hälsorapporter för kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure med hjälp av en mall
> * Övervaka ett kluster
> * [skala upp eller ned ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure Powershell-modulen version 4.1 eller senare](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Installationsprogrammet [diagnostik samling](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) för klustret
* Aktivera den [EventStore service](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) i klustret
* Konfigurera [Azure Monitor-loggar och Log Analytics-agenten](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) för klustret

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Visa Service Fabric-händelser med hjälp av Azure Monitor-loggar

Azure Monitor-loggar samlas in och analyserar telemetri från program och tjänster i molnet och ger analysverktyg som hjälper dig att maximera tillgänglighet och prestanda. Du kan köra frågor i Azure Monitor-loggar att få insikter och felsöka vad som händer i ditt kluster.

För att komma åt Service Fabric-analys lösningen går du till den [Azure-portalen](https://portal.azure.com) och välj den resursgrupp där du skapade Service Fabric-analys-lösningen.

Välj resursen som **ServiceFabric(mysfomsworkspace)**.

I **översikt** du se paneler i form av ett diagram för var och en av de lösningar som är aktiverad, inklusive ett för Service Fabric. Klicka på den **Service Fabric** graph för att fortsätta till Service Fabric-analys-lösningen.

![Service Fabric-lösning](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Följande bild visar öppnas startsidan för Service Fabric-analys-lösning. Den här sidan ger en ögonblicksbild överblick över vad som händer i ditt kluster.

![Service Fabric-lösning](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Om du har aktiverat diagnostik när klustret har skapats kan du se händelser för 

* [Service Fabric-kluster-händelser](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors programming modellhändelser](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programming modellhändelser](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Utöver Service Fabric-händelser direkt ur lådan mer detaljerad händelser kan samlas in av [uppdatera konfigurationen av diagnostiktillägget](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Visa Service Fabric-händelser, inklusive åtgärder på noder

På sidan Service Fabric-analys klickar du på diagrammet för **klusterhändelser**.  Loggar för alla systemhändelser som har samlats in visas. Referens så de kommer från den **WADServiceFabricSystemEventsTable** i Azure Storage-konto och på samma sätt reliable services och aktörer händelser visas bredvid kommer från dessa respektive tabeller.
    
![Fråga Användningskanal](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Frågan använder frågespråk Kusto som du kan ändra för att förfina vad du letar efter. Du kan exempelvis använda följande fråga för att hitta alla åtgärder som vidtas på noderna i klustret. Händelse-ID som används nedan hittar du i den [användningskanal händelser](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Kusto-frågespråket är kraftfullt. Här följer några användbara frågor.

Skapa en *ServiceFabricEvent* uppslagstabell som en användardefinierad funktion genom att spara frågan som en funktion med alias ServiceFabricEvent:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Gå tillbaka operativa händelser som registreras under den senaste timmen:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Returnera operativa händelser med händelse-ID == 18604 och EventName == 'NodeDownOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Returnera operativa händelser med händelse-ID == 18604 och EventName == 'NodeUpOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Returnerar hälsorapporter med HealthState == 3 (fel) och extrahera ytterligare egenskaper från fältet EventMessage:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Returnera ett tidsdiagram för händelser med händelse-ID! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Hämta Service Fabric operativa händelser aggregeras med specifik tjänst och nod:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Rendera antal Service Fabric händelser efter händelse-ID / EventName med hjälp av en fråga mellan resurser:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Visa händelser för Service Fabric-program

Du kan visa händelser för reliable services och reliable actors program som distribueras i klustret.  Service Fabric-analys-sidan, klicka på diagrammet för **programhändelser**.

Kör följande fråga om du vill visa händelser från reliable services-program:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Du kan se olika händelser för när tjänstens runasync startades och slutfördes som vanligtvis sker på distributioner och uppgraderingar.

![Service Fabric Solution Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Du kan också hitta händelser för tillförlitlig tjänst med ServiceName == ”fabric: / Watchdog/WatchdogService”:

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Tillförlitliga aktörer händelser kan läsas på liknande sätt:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Om du vill konfigurera detaljerad händelser för reliable actors, du kan ändra den `scheduledTransferKeywordFilter` i konfigurationen för diagnostiktillägg i mallen för klustret. Information om värdena för dessa finns i den [referens för reliable actors-händelser](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Visa prestandaräknare med Azure Monitor-loggar
Om du vill visa prestandaräknare, går du till den [Azure-portalen](https://portal.azure.com) och resursgrupp där du skapade Service Fabric-analys-lösningen. 

Välj resursen som **ServiceFabric(mysfomsworkspace)**, sedan **Log Analytics-arbetsytan**, och sedan **avancerade inställningar**.

Klicka på **Data**, klicka sedan på **Windows prestandaräknare**. Det finns en lista med standard-räknarna som du kan välja att aktivera och du kan ställa in intervallet för samlingen också. Du kan också lägga till [ytterligare prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) att samla in. Felaktigt format refereras till i den här [artikeln](/windows/desktop/PerfCtrs/specifying-a-counter-path). Klicka på **spara**, klicka sedan på **OK**.

Stäng bladet avancerade inställningar och välj **arbetsytan Sammanfattning** under den **Allmänt** rubrik. För var och en av de lösningar som har aktiverat det är en grafisk panel, inklusive ett för Service Fabric. Klicka på den **Service Fabric** graph för att fortsätta till Service Fabric-analys-lösningen.

Det finns grafiska paneler för användningskanal och reliable services-händelser. Grafisk representation av de data som flödar i för de räknare som du har valt visas under **Nodmätvärden**. 

Välj den **behållare mått** graph för att se ytterligare information. Du kan också fråga på prestandaräknardata på samma sätt som klusterhändelser och filtrerar på noder, perf räknarens namn och värden med hjälp av det Kusto-frågespråket.

## <a name="query-the-eventstore-service"></a>Fråga EventStore-tjänsten
Den [EventStore service](service-fabric-diagnostics-eventstore.md) gör det möjligt att förstå tillståndet för dina kluster eller arbetsbelastningar vid en viss tidpunkt. EventStore är en tillståndskänslig Service Fabric-tjänst som underhåller händelser från klustret. Händelser är tillgängliga via den [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST och API: er. EventStore frågar klustret direkt få diagnostics-data en enhet i klustret för att se en fullständig lista över händelser som är tillgängliga i EventStore, finns i [Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

The EventStore APIs kan efterfrågas via programmering med hjälp av den [Service Fabric-klientbiblioteket](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Här är en exempelbegäran för alla händelser mellan 2018-04-03T18:00:00Z och 2018-04-04T18:00:00Z via funktionen GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Här är ett annat exempel som frågar om hälsotillståndet för klustret och alla nod-händelser i September 2018 och skriver dem ut.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>Övervaka hälsotillstånd hos kluster
Service Fabric introducerar en [hälsomodellen](service-fabric-health-introduction.md) med health entiteter på vilka systemkomponenter och watchdogs kan rapporten lokal villkor som de övervakar. Den [hälsoarkivet](service-fabric-health-introduction.md#health-store) aggregerar alla hälsodata för att avgöra om entiteter är felfri.

Klustret fylls automatiskt med hälsorapporter som skickas av systemkomponenter. Läs mer i [hjälp för att felsöka](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric visar hälsoförfrågningar för var och en av de stöds [entitetstyper](service-fabric-health-introduction.md#health-entities-and-hierarchy). De kan nås via API, med hjälp av metoder på [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), PowerShell-cmdletar och REST. De här frågorna returnera fullständig hälsoinformation om entiteten: aggregerade hälsotillstånd, entiteten health-händelser, underordnade hälsotillstånd (om tillämpligt), felaktiga utvärderingar (när entiteten inte är felfri) och underordnade hälsostatistik (när tillämpligt).

### <a name="get-cluster-health"></a>Hämta klusterhälsa
Den [cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) returnerar hälsotillståndet för kluster-entiteten och innehåller hälsotillstånd på appar och noder (children i klustret).  Anslut till klustret med den [cmdleten Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

Tillståndet för klustret är 11 noder, system-program och fabric: / Voting konfigurerats enligt beskrivningen.

I följande exempel hämtas klusterhälsa med hjälp av standard-hälsoprinciper. 11 noderna är felfria men hälsotillstånd för kluster som aggregeras är fel eftersom fabric: / Röstningsprogrammet är felaktigt. Observera hur de felaktiga utvärderingarna innehåller detaljerad information om de villkor som utlöste aggregerade hälsotillståndet.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

I följande exempel hämtas hälsotillståndet för klustret med hjälp av en princip för anpassade program. Filtreras resultaten för att hämta endast appar och noder i felet eller varningen. I det här exemplet returneras inga noder, som de är alla felfritt. Endast fabric: / Röstningsprogrammet värnar om program-filtret. Eftersom den anpassade principen anger att överväga varningar som fel för fabric: / Röstningsprogrammet, programmet utvärderas som fel och är därför klustret.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Hämta nodhälsan
Den [cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) returnerar hälsotillståndet för en nod-entitet och innehåller health-händelser som rapporteras på noden. Först ansluta till klustret med hjälp av den [cmdleten Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). I följande exempel hämtas hälsotillståndet för en viss nod med hjälp av standard-hälsoprinciper:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

I följande exempel hämtas hälsotillståndet för alla noder i klustret:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Hämta service systemhälsa 

Hämta den sammanställda hälsan systemtjänster:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa händelser för Service Fabric
> * Fråga EventStore APIs för klusterhändelser
> * Övervaka infrastruktur/samla in prestandaräknare
> * Visa hälsorapporter för kluster

Fortsätt sedan till följande självstudie och lär dig hur du skalar ett kluster.
> [!div class="nextstepaction"]
> [Skala ett kluster](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json