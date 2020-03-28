---
title: Övervaka ett service fabric-kluster i Azure
description: I den här självstudien får du lära dig hur du övervakar ett kluster genom att visa Service Fabric-händelser, frågar eventstore-API:erna, övervakar perf-räknare och visar hälsorapporter.
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: ab58d622511e0d5793eb6df312bc3fd6dd15bfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75376638"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Självstudiekurs: Övervaka ett service fabric-kluster i Azure

Övervakning och diagnostik är avgörande för att utveckla, testa och distribuera arbetsbelastningar i alla molnmiljöer. Den här självstudien är del två i en serie och visar hur du övervakar och diagnostiserar ett Service Fabric-kluster med hjälp av händelser, prestandaräknare och hälsorapporter.   Mer information finns i översikten om [klusterövervakning](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) och [övervakning av infrastruktur](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Visa service fabric-händelser
> * Api:er för Frågehändelseaffärer för klusterhändelser
> * Övervaka infrastruktur/samla in perf-räknare
> * Visa hälsorapporter för kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure med hjälp av en mall
> * Övervaka ett kluster
> * [Skala in eller ut ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Installationsdiagnostiksamling för [klustret](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor)
* Aktivera [EventStore-tjänsten](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) i klustret
* Konfigurera [Azure Monitor-loggar och Log Analytics-agenten](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) för klustret

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Visa tjänst fabric-händelser med Hjälp av Azure Monitor-loggar

Azure Monitor loggar samlar in och analyserar telemetri från program och tjänster som finns i molnet och tillhandahåller analysverktyg som hjälper dig att maximera deras tillgänglighet och prestanda. Du kan köra frågor i Azure Monitor-loggar för att få insikter och felsöka vad som händer i klustret.

Om du vill komma åt Service Fabric Analytics-lösningen går du till [Azure-portalen](https://portal.azure.com) och väljer den resursgrupp där du skapade Service Fabric Analytics-lösningen.

Välj **resursen ServiceFabric(mysfomsworkspace)**.

I **Översikt** visas paneler i form av ett diagram för var och en av de lösningar som är aktiverade, inklusive en för Service Fabric. Klicka på diagrammet **Service Fabric** om du vill fortsätta till Service Fabric Analytics-lösningen.

![Service Fabric-lösning](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Följande bild visar startsidan för Service Fabric Analytics-lösningen. Den här startsidan innehåller en ögonblicksbild av vad som händer i klustret.

![Service Fabric-lösning](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Om du har aktiverat diagnostik när du skapar kluster kan du se händelser för 

* [Klusterhändelser för service fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Tillförlitliga aktörer programmering modell händelser](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programmeringsmodellhändelser](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Förutom Service Fabric-händelserna direkt kan mer detaljerade systemhändelser samlas in genom [att uppdatera konfigurationen av diagnostiktillägget](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Visa händelser för serviceinfrastruktur, inklusive åtgärder på noder

På sidan Service Fabric Analytics klickar du på diagrammet för **klusterhändelser**.  Loggarna för alla systemhändelser som har samlats in visas. Som referens kommer dessa från **WADServiceFabricSystemEventsTable** i Azure Storage-kontot, och på samma sätt kommer de tillförlitliga tjänster och aktörer som visas härnäst från respektive tabeller.
    
![Operativ kanal för fråga](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Frågan använder frågespråket Kusto, som du kan ändra för att förfina det du letar efter. Om du till exempel vill hitta alla åtgärder som vidtas på noder i klustret kan du använda följande fråga. Händelse-ID:erna som används nedan finns i [referensen för operativa kanalhändelser](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Kusto-frågespråket är kraftfullt. Här är några andra användbara frågor.

Skapa en *ServiceFabricEvent-uppslagstabell* som användardefinierad funktion genom att spara frågan som en funktion med alias ServiceFabricEvent:

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

Operativa returhändelser som registrerats under den senaste timmen:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Returoperationshändelser med EventId == 18604 och EventName == 'NodeDownOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Returoperationshändelser med EventId == 18604 och EventName == 'NodeUpOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Returnerar hälsorapporter med HealthState == 3 (Fel) och extraherar ytterligare egenskaper från fältet EventMessage:

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

Returnera ett tidsschema över händelser med EventId != 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Hämta drifthändelser för Service Fabric som aggregeras med den specifika tjänsten och noden:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Rendera antalet Service Fabric-händelser efter EventId/EventName med hjälp av en resursfråga:

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

### <a name="view-service-fabric-application-events"></a>Visa programhändelser för Service Fabric

Du kan visa händelser för tillförlitliga tjänster och tillförlitliga aktörsprogram som distribueras i klustret.  Klicka på diagrammet för **programhändelser**på sidan Service Fabric Analytics .

Kör följande fråga för att visa händelser från dina tillförlitliga tjänsteprogram:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Du kan se olika händelser för när tjänsten runasync startas och slutförs som vanligtvis sker på distributioner och uppgraderingar.

![Tillförlitliga tjänster för serviceinfrastruktur](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Du kan också hitta händelser för den tillförlitliga tjänsten med ServiceName == "fabric:/Watchdog/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Tillförlitliga skådespelare händelser kan ses på ett liknande sätt:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Om du vill konfigurera mer detaljerade `scheduledTransferKeywordFilter` händelser för tillförlitliga aktörer kan du ändra konfigurationen för diagnostiktillägget i klustermallen. Detaljer om värdena för dessa finns i referensen för [tillförlitliga aktörer händelser](service-fabric-reliable-actors-diagnostics.md#keywords).

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
Om du vill visa prestandaräknare går du till [Azure-portalen](https://portal.azure.com) och resursgruppen där du skapade Service Fabric Analytics-lösningen. 

Välj **resurstjänsten ServiceFabric(mysfomsworkspace)** och logga sedan **analytics-arbetsytan**och sedan **avancerade inställningar**.

Klicka på **Data**och sedan på **Windows prestandaräknare**. Det finns en lista över standardräknare som du kan välja att aktivera och du kan också ange intervallet för samlingen. Du kan också lägga till [ytterligare prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) att samla in. Rätt format refereras i den här [artikeln](/windows/desktop/PerfCtrs/specifying-a-counter-path). Klicka på **Spara**och sedan på **OK**.

Stäng bladet Avancerade inställningar och välj **Sammanfattning av arbetsytan** under rubriken **Allmänt.** För var och en av de lösningar som är aktiverade finns en grafisk panel, inklusive en för Service Fabric. Klicka på diagrammet **Service Fabric** om du vill fortsätta till Service Fabric Analytics-lösningen.

Det finns grafiska paneler för operativ kanal och tillförlitliga tjänster händelser. Den grafiska representationen av data som flödar in för de räknare som du har valt visas under **Nod Metrics**. 

Välj diagrammet **Behållarmått** om du vill se ytterligare information. Du kan också fråga på prestandaräknaredata på samma sätt som klusterhändelser och filtrera på noderna, perf-räknarens namn och värden med hjälp av Frågespråket Kusto.

## <a name="query-the-eventstore-service"></a>Fråga eventstore-tjänsten
[EventStore-tjänsten](service-fabric-diagnostics-eventstore.md) är ett sätt att förstå tillståndet för klustret eller arbetsbelastningarna vid en viss tidpunkt. EventStore är en tillståndskänslig Service Fabric-tjänst som underhåller händelser från klustret. Händelserna exponeras via [Service Fabric Explorer,](service-fabric-visualizing-your-cluster.md)REST och API:er. EventStore frågar klustret direkt för att hämta diagnostikdata på alla entiteter i klustret Om du vill se en fullständig lista över händelser som är tillgängliga i EventStore läser du [Service Fabric-händelser](service-fabric-diagnostics-event-generation-operational.md).

EventStore-API:erna kan efterfrågas programmässigt med hjälp av [service fabric-klientbiblioteket](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Här är en exempelbegäran för alla klusterhändelser mellan 2018-04-03T18:00:00Z och 2018-04-04T18:00:00Z, via funktionen GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Här är ett annat exempel som frågar efter klusterhälsan och alla nodhändelser i september 2018 och skriver ut dem.

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


## <a name="monitor-cluster-health"></a>Övervaka klusterhälsa
Service Fabric introducerar en [hälsomodell](service-fabric-health-introduction.md) med hälsoentiteter på vilka systemkomponenter och watchdogs kan rapportera lokala förhållanden som de övervakar. [Hälsoarkivet](service-fabric-health-introduction.md#health-store) sammanställer alla hälsodata för att avgöra om entiteter är felfria.

Klustret fylls automatiskt med hälsorapporter som skickas av systemkomponenterna. Läs mer på [Använd systemhälsorapporter för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric exponerar hälsofrågor för var och en av de [entitetstyper](service-fabric-health-introduction.md#health-entities-and-hierarchy)som stöds . De kan nås via API: et med metoder på [FabricClient.HealthManager,](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet)PowerShell-cmdlets och REST. Dessa frågor returnerar fullständig hälsoinformation om entiteten: aggregerat hälsotillstånd, entitetshälsohändelser, underordnade hälsotillstånd (i förekommande fall), felaktiga utvärderingar (när entiteten inte är felfri) och barnhälsostatistik (när tillämpliga).

### <a name="get-cluster-health"></a>Hämta klusterhälsa
[Cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) returnerar hälsotillståndet för klusterentiteten och innehåller hälsotillstånden för program och noder (underordnade kluster).  Anslut först till klustret med [cmdleten Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

Klustrets tillstånd är 11 noder, systemprogrammet och infrastruktur:/Röstning konfigurerad enligt beskrivningen.

I följande exempel får klusterhälsan genom att använda standardhälsoprinciper. 11-noderna är felfria men klustret aggregerade hälsotillstånd är Fel eftersom fabric:/voting-programmet är i fel. Observera hur de felaktiga utvärderingarna innehåller information om de villkor som utlöste den aggregerade hälsan.

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

Följande exempel hämtar klustrets hälsotillstånd med hjälp av en anpassad programprincip. Den filtrerar resultat för att bara få program och noder i fel eller varning. I det här exemplet returneras inga noder, eftersom de alla är felfria. Endast fabric:/röstningsprogrammet respekterar programfiltret. Eftersom den anpassade principen anger att varningar ska betraktas som fel för fabric:/voting-programmet, utvärderas programmet som ett fel och så är klustret.

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

### <a name="get-node-health"></a>Få nodhälsa
[Cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) returnerar hälsan för en nodentitet och innehåller hälsohändelser som rapporterats på noden. Anslut först till klustret med hjälp av [cmdleten Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). I följande exempel får du hälsotillståndet för en viss nod med hjälp av standardhälsoprinciper:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

I följande exempel får du hälsotillståndet för alla noder i klustret:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Få hälsa på systemtjänsten 

Få den aggregerade hälsan hos systemtjänsterna:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa service fabric-händelser
> * Api:er för Frågehändelseaffärer för klusterhändelser
> * Övervaka infrastruktur/samla in perf-räknare
> * Visa hälsorapporter för kluster

Gå sedan vidare till följande självstudie för att lära dig hur du skalar ett kluster.
> [!div class="nextstepaction"]
> [Skala ett kluster](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
