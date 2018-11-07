---
title: Total hälsa för hur du visar Azure Service Fabric entiteter | Microsoft Docs
description: Beskriver hur du fråga, visa och utvärdera Azure Service Fabric entiteter aggregerade hälsotillstånd, via hälsoförfrågningar och allmänna frågor.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: b6f6653381b5fcf80b9647c64334dfed1a2230bb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230860"
---
# <a name="view-service-fabric-health-reports"></a>Visa hälsorapporter i Service Fabric
Azure Service Fabric introducerar en [hälsomodellen](service-fabric-health-introduction.md) med health entiteter på vilka systemkomponenter och watchdogs kan rapporten lokal villkor som de övervakar. Den [hälsoarkivet](service-fabric-health-introduction.md#health-store) aggregerar alla hälsodata för att avgöra om entiteter är felfri.

Klustret fylls automatiskt med hälsorapporter som skickas av systemkomponenter. Läs mer i [hjälp för att felsöka](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric finns flera sätt att hämta aggregerade hälsotillståndet för entiteterna:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) eller andra visualiseringsverktyg för
* Hälsoförfrågningar (via PowerShell, API: et eller REST)
* Allmänna frågor som returnerar en lista över entiteter som har health som en av egenskaperna (via PowerShell, API: et eller REST)

Om du vill demonstrera dessa alternativ kan vi använda ett lokalt kluster med fem noder och [fabric: / WordCount-programmet](https://aka.ms/servicefabric-wordcountapp). Den **fabric: / WordCount** programmet innehåller två standardtjänster, en tillståndskänslig tjänst av typen `WordCountServiceType`, och en tillståndslös tjänst av typen `WordCountWebServiceType`. Jag har ändrat den `ApplicationManifest.xml` att kräva sju rikta repliker för den tillståndskänsliga tjänsten och en partition. Eftersom det finns bara fem noder i klustret, rapport systemkomponenter en varning om tjänsten partitionen eftersom den är lägre än antalet mål.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Hälsotillstånd i Service Fabric Explorer
Service Fabric Explorer tillhandahåller en visuell översikt över klustret. Du kan se att i bilden nedan:

* Programmet **fabric: / WordCount** är röd (i fel) eftersom den har en felhändelse som rapporteras av **MyWatchdog** för egenskapen **tillgänglighet**.
* En av dess tjänster **fabric: / WordCount/WordCountService** är gul (i varning). Tjänsten är konfigurerad med sju repliker och klustret har fem noder, så det inte går att placera två repicas. Även om det inte visas här, service-partitionen är gul på grund av en rapport från `System.FM` säga att `Partition is below target replica or instance count`. Den gula partitionen utlöser gul tjänsten.
* Klustret är röda på grund av red programmet.

Utvärderingen använder standard-principer i klustermanifestet och programmanifestet. De strikta principer och tål inte eventuella fel.

Vy över klustret med Service Fabric Explorer:

![Vy över klustret med Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Läs mer om [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Hälsoförfrågningar
Service Fabric visar hälsoförfrågningar för var och en av de stöds [entitetstyper](service-fabric-health-introduction.md#health-entities-and-hierarchy). De kan nås via API, med hjälp av metoder på [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), PowerShell-cmdletar och REST. De här frågorna returnera fullständig hälsoinformation om entiteten: aggregerade hälsotillstånd, entiteten health-händelser, underordnade hälsotillstånd (om tillämpligt), felaktiga utvärderingar (när entiteten inte är felfri) och underordnade hälsostatistik (när tillämpligt).

> [!NOTE]
> En entitet för health returneras när det är helt fylld i health store. Entiteten måste vara aktiva (inte bort) och ha en rapport. Dess överordnade entiteter i kedjan hierarki måste också ha systemrapporter. Om något av följande villkor inte uppfylls, hälsotillståndet frågar returnera en [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) med [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` som visar varför entiteten returneras inte.
>
>

Hälsoförfrågningar måste klara i entitets-ID, beroende på enhetstypen. Frågorna acceptera valfritt hälsotillstånd principparametrar. Om inga hälsoprinciper anges den [hälsoprinciper](service-fabric-health-introduction.md#health-policies) från manifestet kluster eller ett program som används för utvärdering. Om manifesten inte innehåller en definition för hälsoprinciper, används standard-hälsoprinciper för utvärdering. Standardprinciper för hälsotillstånd tål inte eventuella fel. Frågorna kan också acceptera filter för att returnera endast partiella barn eller händelser – de som respekterar de angivna filtren har använts. Ett annat filter kan exklusive barn-statistik.

> [!NOTE]
> Utdatafilter används på serversidan, så svara meddelandestorleken minskas. Vi rekommenderar att du använda filter för utgående trafik för att begränsa de data som returneras i stället använda filter på klientsidan.
>
>

En entitets hälsotillstånd innehåller:

* Sammanställda hälsotillståndet för entiteten. Beräknas av hälsoarkivet baserat på entiteten hälsorapporter, underordnade hälsotillstånd (om tillämpligt) och hälsoprinciper. Läs mer om [entitet hälsotillstånd utvärdering](service-fabric-health-introduction.md#health-evaluation).  
* Hälsohändelser i entiteten.
* Insamling av hälsotillstånd för alla underordnade för de enheter som kan ha underordnade. Hälsotillstånden innehålla entitet identifierare och aggregerade hälsotillståndet. För att få fullständig hälsotillståndet för ett barn, anropa fråga hälsotillståndet för den underordnade entitetstypen och skicka in det underordnade ID: t.
* Felaktiga utvärderingar som pekar på rapporten som utlöste tillståndet för entiteten, om enheten inte är felfri. Utvärderingarna är rekursiv, som innehåller de underordnade hälsotillstånd utvärderingar som utlöste aktuella hälsotillstånd. Till exempel rapporterade en watchdog ett fel mot en replik. Programmets hälsotillstånd visar en defekt utvärdering på grund av en felaktig tjänst; tjänsten är skadad på grund av en partition i fel. partitionen är skadad på grund av en replik i fel. repliken är skadad på grund av hälsorapport för watchdog-fel.
* Hälsostatistik för alla underordnade typer av entiteter som har underordnade. Klusterhälsa visar det totala antalet program, tjänster, partitioner, repliker och distribuerat entiteter i klustret. Tjänstehälsa visar det totala antalet partitioner och -repliker under den angivna tjänsten.

## <a name="get-cluster-health"></a>Hämta klusterhälsa
Returnerar hälsotillståndet för kluster-entiteten och innehåller hälsotillstånd på appar och noder (children i klustret). Indata:

* [Valfritt] Klusterprincipen för hälsotillstånd som används för att utvärdera noder och klusterhändelser.
* [Valfritt] Hälsotillstånd princip programkartan med hälsoprinciper som används för att åsidosätta användningsprinciperna som manifest.
* [Valfritt] Filter för händelser, noder och program som anger vilka transaktioner är av intresse och ska returneras i resultatet (till exempel endast, fel eller varningar och fel). Alla händelser, noder och program används för att utvärdera entitet som aggregeras hälsotillstånd, oavsett filtret.
* [Valfritt] Filter för att utesluta hälsostatistik.
* [Valfritt] Till att inkludera fabric: / System hälsostatistik i hälsostatistik. Gäller endast när hälsostatistik inte undantas identifieras. Som standard innehåller hälsostatistik bara statistik för användarnas program och inte System-program.

### <a name="api"></a>API
För att få klusterhälsa kan skapa en `FabricClient` och anropa den [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metoden på dess **HealthManager**.

Följande anrop hämtar hälsotillstånd hos kluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Följande kod hämtar hälsotillstånd hos kluster med hjälp av en anpassad kluster hälsoprincip och filter för noder och program. Anger att hälsostatistik inkluderar fabric: / statistik för filsystemet. Den skapar [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), som innehåller informationen som indata.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta klusterhälsa är [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Tillståndet för klustret är fem noder, system-program och fabric: / WordCount konfigurerats enligt beskrivningen.

Följande cmdlet hämtar klusterhälsa med hjälp av standard-hälsoprinciper. Sammanställda hälsotillståndet varning, eftersom fabric: / WordCount-programmet är varning. Observera hur de felaktiga utvärderingarna innehåller detaljerad information om de villkor som utlöste aggregerade hälsotillståndet.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                          
                          
NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_0
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Följande PowerShell-cmdlet hämtar hälsotillståndet för klustret med hjälp av en princip för anpassade program. Filtreras resultaten för att hämta endast appar och noder i felet eller varningen. Därför kan returneras inga noder, eftersom de är alla felfritt. Endast fabric: / WordCount-programmet värnar om program-filtret. Eftersom den anpassade principen anger att överväga varningar som fel för fabric: / WordCount-programmet ser programmet utvärderas som fel, och så är klustret.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                          
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="rest"></a>REST
Du kan hämta klusterhälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i texten.

## <a name="get-node-health"></a>Hämta nodhälsan
Returnerar hälsotillståndet för en nod-entitet och innehåller health-händelser som rapporteras på noden. Indata:

* [Krävs] Namnet på noden som identifierar noden.
* [Valfritt] Klustret hälsotillstånd principinställningarna används för att utvärdera hälsan.
* [Valfritt] Filter för händelser som anger vilka transaktioner är av intresse och ska returneras i resultatet (till exempel endast, fel eller varningar och fel). Alla händelser som används för att utvärdera entitet som aggregeras hälsotillstånd, oavsett filtret.

### <a name="api"></a>API
För att få nodhälsan via API: et kan du skapa en `FabricClient` och anropa den [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) metoden på dess HealthManager.

Följande kod hämtar noden hälsotillståndet för det angivna noden namnet:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Följande kod hämtar noden hälsotillståndet för det angivna nodnamnet och skickar i filter för klusterhändelser och anpassad princip via [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta nodhälsan är [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.
Följande cmdlet hämtar noden hälsotillståndet genom att använda standard-hälsoprinciper:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Följande cmdlet hämtar hälsotillståndet för alla noder i klustret:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Du kan hämta nodhälsan med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i texten.

## <a name="get-application-health"></a>Hämta programmets hälsotillstånd
Returnerar hälsotillståndet för en entitet i programmet. Den innehåller hälsotillstånd på det distribuerade programmet och service underordnade. Indata:

* [Krävs] Programnamnet (URI) som identifierar programmet.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta användningsprinciperna som manifest.
* [Valfritt] Filter för händelser, tjänster och distribuerade program som anger vilka transaktioner kan vara av intresse och ska returneras i resultatet (till exempel endast, fel eller varningar och fel). Alla händelser, tjänster och distribuerade program används för att utvärdera entitet som aggregeras hälsotillstånd, oavsett filtret.
* [Valfritt] Filtrera för att undanta hälsostatistik. Om inte anges hälsostatistik är ok, varning och antal fel för alla program underordnade: tjänster, partitioner, repliker, distribuerade program och distribuerade tjänstpaket.

### <a name="api"></a>API
Om du vill hämta programmets hälsotillstånd, skapa en `FabricClient` och anropa den [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) metoden på dess HealthManager.

Följande kod hämtar programhälsa för det angivna programnamnet (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Följande kod hämtar programhälsa för det angivna programnamnet (URI) med filter och anpassade principer som angetts via [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta programmets hälsotillstånd är [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Följande cmdlet returnerar hälsotillståndet för den **fabric: / WordCount** program:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok
                                  
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning
                                  
DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok
                                  
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
                                  
HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

Följande PowerShell-cmdlet skickar i anpassade principer. Även filtreras barn och händelser.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error
                                  
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Du kan hämta programmets hälsotillstånd med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) som innehåller hälsoprinciper som beskrivs i texten.

## <a name="get-service-health"></a>Hämta tjänstehälsa
Returnerar hälsotillståndet för en service-entitet. Den innehåller hälsotillstånd partition. Indata:

* [Krävs] Tjänstnamnet (URI) som identifierar tjänsten.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta manifest-princip för program.
* [Valfritt] Filter för händelser och partitioner som anger vilka transaktioner är av intresse och ska returneras i resultatet (till exempel endast, fel eller varningar och fel). Alla händelser och partitioner som används för att utvärdera entitet som aggregeras hälsotillstånd, oavsett filtret.
* [Valfritt] Filter för att utesluta hälsostatistik. Om inte anges används hälsostatistik visa ok, varning, och fel antal för alla partitioner och repliker av tjänsten.

### <a name="api"></a>API
För att få tjänstehälsa via API: et kan du skapa en `FabricClient` och anropa den [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) metoden på dess HealthManager.

I följande exempel hämtas hälsotillståndet för en tjänst med det angivna tjänstnamnet (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Följande kod hämtar tjänstehälsa för det angivna tjänstnamnet (URI), som att ange filter och anpassade principer via [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta tjänstehälsa är [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Följande cmdlet hämtar tjänstehälsa med hjälp av standard-hälsoprinciper:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                        
                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                        
                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Du kan hämta tjänstehälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i texten.

## <a name="get-partition-health"></a>Hämta partition health
Returnerar hälsotillståndet för en partition entitet. Den innehåller repliken hälsotillstånd. Indata:

* [Krävs] Partitions-ID (GUID) som identifierar partitionen.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta manifest-princip för program.
* [Valfritt] Filter för händelser och repliker som anger vilka transaktioner är av intresse och ska returneras i resultatet (till exempel endast, fel eller varningar och fel). Alla händelser och repliker används för att utvärdera entitet som aggregeras hälsotillstånd, oavsett filtret.
* [Valfritt] Filter för att utesluta hälsostatistik. Om inte anges, visar hälsostatistik hur många repliker är i ok, varning och fel tillstånd.

### <a name="api"></a>API
För att få partition health via API: et kan du skapa en `FabricClient` och anropa den [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) metoden på dess HealthManager. Om du vill ange valfria parametrar, skapa [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta partition health är [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Följande cmdlet hämtar hälsotillståndet för alla partitioner i den **fabric: / WordCount/WordCountService** service och filtrerar bort repliken hälsotillstånd:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Du kan hämta partition health med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i texten.

## <a name="get-replica-health"></a>Hämta replica health
Returnerar hälsotillståndet för en replik för tillståndskänsliga tjänster eller en tillståndslös tjänst-instans. Indata:

* [Krävs] Partitions-ID (GUID) och repliken ID som identifierar repliken.
* [Valfritt] Application health principparametrarna används för att åsidosätta användningsprinciperna som manifest.
* [Valfritt] Filter för händelser som anger vilka transaktioner är av intresse och ska returneras i resultatet (till exempel endast, fel eller varningar och fel). Alla händelser som används för att utvärdera entitet som aggregeras hälsotillstånd, oavsett filtret.

### <a name="api"></a>API
För att få replica health via API: et kan du skapa en `FabricClient` och anropa den [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) metoden på dess HealthManager. Om du vill ange avancerade parametrar, använda [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta repliken hälsotillståndet är [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Följande cmdlet hämtar hälsotillståndet för den primära repliken för alla partitioner i tjänsten:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Du kan hämta tillståndet för repliken med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i texten.

## <a name="get-deployed-application-health"></a>Hämta distribuerade programmets hälsotillstånd
Returnerar hälsotillståndet för ett program som distribueras på en nod-entitet. Den innehåller hälsotillstånd för distribuerade tjänsten paketet. Indata:

* [Krävs] Programnamn (URI) och nodnamnet (sträng) som identifierar det distribuerade programmet.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta användningsprinciperna som manifest.
* [Valfritt] Filter för händelser och distribuerade tjänstpaket som anger vilka transaktioner är av intresse och ska returneras i resultatet (till exempel endast, fel eller varningar och fel). Alla händelser och distribuerade tjänstpaket som används för att utvärdera entitet som aggregeras hälsotillstånd, oavsett filtret.
* [Valfritt] Filter för att utesluta hälsostatistik. Om inte anges, visar hälsostatistik antalet distribuerade tjänstpaket i ok, varning och fel hälsotillstånd.

### <a name="api"></a>API
För att få hälsotillståndet för ett program som distribueras på en nod via API: et kan du skapa en `FabricClient` och anropa den [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) metoden på dess HealthManager. Om du vill ange valfria parametrar, använda [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta den distribuerade programhälsan är [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet. Om du vill ta reda på om ett program har distribuerats kan du köra [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) och titta på underordnade distribuerade programmet.

Följande cmdlet hämtar hälsotillståndet för den **fabric: / WordCount** tillämpning som distribuerats i **_Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM
                                     
HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Du kan hämta distribuerade programhälsan med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i texten.

## <a name="get-deployed-service-package-health"></a>Hämta distribuerade tjänstehälsa för paketet
Returnerar hälsotillståndet för en distribuerad tjänst paketet entitet. Indata:

* [Krävs] Den programnamn (URI), nodnamnet (sträng) och manifest tjänstnamnet (sträng) som identifierar det distribuerade tjänstpaketet.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta manifest-princip för program.
* [Valfritt] Filter för händelser som anger vilka transaktioner är av intresse och ska returneras i resultatet (till exempel endast, fel eller varningar och fel). Alla händelser som används för att utvärdera entitet som aggregeras hälsotillstånd, oavsett filtret.

### <a name="api"></a>API
Om du vill ha hälsotillståndet för en distribuerad tjänst-paketet via API, skapa en `FabricClient` och anropa den [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) metoden på dess HealthManager. Om du vill ange valfria parametrar, använda [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta distribuerade tjänstehälsa för paketet är [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet. Om du vill se där ett program distribueras, kör [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) och titta på de distribuerade program. För att se vilken tjänst som paket som är i ett program, visar de underordnade objekten paketet distribuerad tjänst i den [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) utdata.

Följande cmdlet hämtar hälsotillståndet för den **WordCountServicePkg** tjänstpaket av den **fabric: / WordCount** tillämpning som distribuerats i **_Node_2**. Entiteten har **System.Hosting** rapporter för service-package och startpunkt aktiveringen och tjänsttyp att registreringen har lyckats.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Du kan hämta distribuerade paketet tjänsthälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i texten.

## <a name="health-chunk-queries"></a>Segmentet hälsoförfrågningar
Hälsotillstånd segmentet frågorna kan returnera flera nivåer kluster underordnade (rekursivt), per inkommande filter. Det har stöd för avancerade filter som gör att en stor flexibilitet när de väljer de underordnade objekten som ska returneras. Filtren kan ange underordnade med den unika identifieraren eller genom andra grupp-ID: n och/eller hälsotillstånd. Som standard ingår inga underordnade, till skillnad från health-kommandon som alltid innehåller underordnade på första nivån.

Den [hälsoförfrågningar](service-fabric-view-entities-aggregated-health.md#health-queries) returnera endast första nivån underordnade till den angivna entiteten per krävs filter. Om du vill ha underordnade grupper till de underordnade objekten, måste du anropa ytterligare hälsotillstånd API: er för varje entitet av intresse. På samma sätt för att få hälsotillståndet för specifika entiteter, måste du anropa en hälso-API för varje entitet. Segmentet frågan avancerad filtrering kan du begära flera objekt av intresse i en fråga, minimerar meddelandestorleken och hur många meddelanden.

Värdet för segmentet frågan är att du kan få hälsotillståndet för flera kluster entiteter (potentiellt alla kluster entiteter med början vid krävs rot) i ett anrop. Du kan uttrycka komplexa hälsotillstånd frågan som:

* Returnera endast program av misstag och för programmen inkludera alla tjänster i varning eller fel. Inkludera alla partitioner för returnerade tjänster.
* Returnera endast hälsotillståndet för fyra program som anges av deras namn.
* Returnera endast hälsoläget för program av en önskad programtyp.
* Returnera alla distribuerade enheter på en nod. Returnerar alla program, alla distribuerade program på den angivna noden och alla distribuerade tjänstpaket på noden.
* Returnera alla repliker av misstag. Returnerar alla program, tjänster, partitioner och endast repliker av misstag.
* Returnera alla program. Inkludera alla partitioner för en specifik tjänst.

För närvarande visas hälsotillstånd segment frågan endast för kluster-entiteten. Den returnerar ett kluster hälsotillstånd segment som innehåller:

* Klustret samman hälsotillstånd.
* Hälsotillstånd tillstånd segment listan över noder som respektera inkommande filter.
* Hälsotillstånd tillstånd segment lista över program som respektera inkommande filter. Varje application health tillstånd segment innehåller en segment lista med alla tjänster som respektera inkommande filter och en segment lista med alla distribuerade program som respekterar filtren. Samma för tjänster och distribuerade program. På så sätt kan alla entiteter i klustret kan potentiellt returneras vid begäran, hierarkiskt.

### <a name="cluster-health-chunk-query"></a>Klustret hälsotillstånd segment fråga
Returnerar hälsotillståndet för kluster-entiteten och innehåller hierarkisk hälsotillstånd tillstånd segment med nödvändiga underordnade. Indata:

* [Valfritt] Klusterprincipen för hälsotillstånd som används för att utvärdera noder och klusterhändelser.
* [Valfritt] Hälsotillstånd princip programkartan med hälsoprinciper som används för att åsidosätta användningsprinciperna som manifest.
* [Valfritt] Filter för noder och program som anger vilka transaktioner är av intresse och ska returneras i resultatet. Filtren är specifika för en entitet/grupp av enheter eller gäller för alla entiteter på den nivån. I listan med filter kan innehålla ett allmänt filter och/eller filter för specifika identifierare till noggrann entiteter som returneras av frågan. Om den är tom returneras inte de underordnade objekten som standard.
  Läs mer om filter på [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) och [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Programmet filter kan rekursivt ange avancerade filter för barn.

Segmentet resultatet inkluderar de underordnade objekten som respekterar filtren.

Segment-frågan returnerar för närvarande inte felaktiga utvärderingar eller entitet händelser. Den extra informationen kan hämtas med hjälp av den befintliga kluster hälsotillstånd frågan.

### <a name="api"></a>API
För att få kluster hälsotillstånd segment kan du skapa en `FabricClient` och anropa den [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metoden på dess **HealthManager**. Du kan skicka in [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) att beskriva hälsoprinciper och avancerade filter.

Följande kod hämtar kluster hälsotillstånd segment med avancerade filter.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta klusterhälsa är [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Följande kod hämtar noder bara om de är i fel utom en viss nod som alltid ska returneras.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1
                               
                               NodeName              : _Node_1
                               HealthState           : Ok
                               
ApplicationHealthStateChunks : None
```

Följande cmdlet hämtar kluster segment med programmet filter.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1
                               
                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1
                               
                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5
                               
                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Följande cmdlet returnerar alla distribuerade entiteter på en nod.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2
                               
                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
                               
                               
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Du kan hämta kluster hälsotillstånd segment med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) som innehåller hälsoprinciper och avancerade filter som beskrivs i texten.

## <a name="general-queries"></a>Allmänna frågor
Allmänna frågor returnerar en lista över en viss typ av Service Fabric-entiteter. De är tillgängliga via API: et (via metoder på **FabricClient.QueryManager**), PowerShell-cmdletar och REST. De här frågorna aggregera underfrågor från flera komponenter. En av dem är den [hälsoarkivet](service-fabric-health-introduction.md#health-store), som fyller på aggregerade hälsotillståndet för varje frågeresultatet.  

> [!NOTE]
> Allmänna frågor returnerar aggregerade hälsotillståndet för entiteten och innehåller inte omfattande hälsodata. Om en enhet inte är felfri, kan du följa upp med hälsoförfrågningar att hämta alla dess health information, inklusive händelser, underordnade hälsotillstånd och felaktiga utvärderingar.
>
>

Om allmänna frågor returnerar det okända hälsotillståndet för en entitet, är det möjligt att health store inte har fullständig information om entiteten. Det är också möjligt att en underfråga till health store inte lyckas (till exempel det uppstod ett kommunikationsfel eller health store begränsades). Följ upp med en fråga för hälsa för entiteten. Om underfrågan uppstår tillfälliga fel, till exempel nätverksproblem, kan den här Uppföljnings frågan lyckas. Det kan också ge dig mer från health store om varför exponeras inte entiteten.

Frågor som innehåller **HealthState** för entiteter är:

* Nodlistan: returnerar lista över noderna i klustret (växlat minne).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Lista över program: returnerar listan över program i klustret (växlat minne).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Tjänstelistan: returnerar listan över tjänster i ett program (växlat minne).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partitionslista: returnerar listan över partitioner i en tjänst (växlat minne).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Replikeringslistan: returnerar listan över repliker i en partition (växlat minne).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Distribuerat programlista: returnerar listan över distribuerade program på en nod.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Distribuerat service paketlistan: returnerar listan över paket i ett distribuerat program.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Några av frågorna som returnerar växlade resultat. Avkastningen på dessa frågor är en lista som härletts från [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Om resultatet inte ryms ett meddelande, returneras endast en sida och en ContinuationToken som spårar där uppräkning stoppats. Fortsätta att anropa samma fråga och skicka in fortsättningstoken från den föregående frågan att hämta nästa resultat.
>
>

### <a name="examples"></a>Exempel
Följande kod hämtar felaktiga program i klustret:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Följande cmdlet hämtar information om program för fabric: / WordCount-programmet. Observera att hälsotillståndet är på varningen.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Följande cmdlet hämtar tjänster med ett hälsotillstånd fel:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Uppgradering av kluster och program
Under en övervakad och programmet kontrollerar Service Fabric health att se till att allt är felfri. Om en entitet är defekt som utvärderas med hjälp av konfigurerade hälsoprinciper, gäller uppgraderingen uppgraderingen-specifika policys för att avgöra nästa åtgärd. Uppgraderingen pausas för att tillåta interaktion från användaren (t.ex åtgärda fel eller ändra principer) eller den kan automatiskt återställa den tidigare fungerande versionen.

Under en *kluster* uppgraderingen kan du uppgradera klusterstatusen. Uppgraderingsstatusen innehåller felaktiga utvärderingar som pekar på vad är skadad i klustret. Om uppgraderingen återställs på grund av problem med hälsotillstånd uppgraderingsstatusen kommer ihåg senaste felaktiga orsaker. Den här informationen kan hjälpa administratörer att undersöka vad som gick fel när uppgraderingen återtagits eller stoppats.

På samma sätt under en *program* kan eventuella felaktiga utvärderingar finns i uppgraderingsstatus för programmet.

Följande visar uppgradera programstatus för en ändrad fabric: / WordCount-programmet. En watchdog rapporterade ett fel på en av dess repliker. Uppgraderingen är aktiv eftersom hälsokontrollerna inte uppfylls.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Läs mer om den [Service Fabric-Programuppgradering](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Använd hälsotillstånd utvärderingar för att felsöka
När det finns ett problem med klustret eller ett program, kan du titta på hälsotillståndet för klustret eller ett program att hitta fel. Felaktiga utvärderingar innehåller information om vad som utlöste det aktuella feltillståndet. Om du vill kan granska du nedåt i feltillstånd underordnade entiteter för att identifiera den bakomliggande orsaken.

Anta exempelvis att ett program defekt eftersom det inte finns en felrapport på en av dess repliker. Följande Powershell-cmdlet visar felaktiga utvärderingar:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.
                                  
                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.
                                  
                                            Error event: SourceId='MyWatchdog', Property='Memory'.
                                  
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Du kan titta på repliken för att få mer information:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> Felaktiga utvärderingar visar det första skälet entiteten utvärderas till aktuella hälsotillstånd. Det kan finnas flera andra händelser som utlöser det här tillståndet, men de är inte återspeglas i utvärderingarna. Om du vill ha mer information kan du granska nedåt i health-entiteter för att ta reda på alla rapporter i klustret som defekt.
>
>

## <a name="next-steps"></a>Nästa steg
[Felsök med hjälp av systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Lägg till anpassade hälsorapporter i Service Fabric](service-fabric-report-health.md)

[Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Programuppgradering](service-fabric-application-upgrade.md)
