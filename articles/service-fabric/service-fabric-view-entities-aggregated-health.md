---
title: "Så här visar du Azure Service Fabric entiteter samman hälsa | Microsoft Docs"
description: "Beskriver hur du fråga, visa och utvärdera Azure Service Fabric entiteter aggregerade hälsa genom hälsoförfrågningar och allmänna frågor."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: e73da18ffe146d09b191f1ea5ad1772b36ad8947
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="view-service-fabric-health-reports"></a>Visa Service Fabric-hälsorapporter
Azure Service Fabric introducerar en [hälsomodell](service-fabric-health-introduction.md) med hälsa entiteter där systemkomponenter och watchdogs kan rapporten lokala villkor som de övervakar. Den [hälsoarkivet](service-fabric-health-introduction.md#health-store) aggregerar alla hälsotillståndsdata för att avgöra om enheterna är felfria.

Klustret fylls automatiskt med hälsorapporter som skickas av systemkomponenter. Läs mer på [använda hälsorapporter system för att felsöka](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric finns flera sätt att hämta aggregerade hälsotillståndet hos enheterna:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) eller andra visualiseringsverktyg för
* Hälsoförfrågningar (via PowerShell API eller REST)
* Allmänna frågor som returnerar en lista över enheter som har hälsa som någon av egenskaperna (via PowerShell API eller REST)

För att demonstrera alternativen, skriver du ett lokala kluster med fem noder och [fabric: / WordCount programmet](http://aka.ms/servicefabric-wordcountapp). Den **fabric: / WordCount** program innehåller två standardtjänster är en tillståndskänslig tjänst av typen `WordCountServiceType`, och en tillståndslös tjänst av typen `WordCountWebServiceType`. Du har ändrat den `ApplicationManifest.xml` kräver sju mål repliker för tjänsten tillståndskänsliga och en partition. Eftersom det finns bara fem noder i klustret kan rapportera systemkomponenter en varning om tjänsten partitionen eftersom det är mindre än målantalet.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Hälsa i Service Fabric Explorer
Service Fabric Explorer ger en visuell översikt över klustret. I bilden nedan kan du se att:

* Programmet **fabric: / WordCount** är röd (fel) eftersom den har en felhändelse som rapporterats av **MyWatchdog** för egenskapen **tillgänglighet**.
* En av dess tjänster, **fabric: / WordCount/WordCountService** är gul (i varning). Tjänsten har konfigurerats med sju repliker och klustret har fem noder så att två repicas inte kan placeras. Även om det inte visas här, service-partitionen är gult på grund av en rapport från `System.FM` säger som `Partition is below target replica or instance count`. Gult partitionen utlöser gul tjänsten.
* Klustret är röd på grund av röd programmet.

Utvärderingen använder standardprinciper från klustermanifestet och programmanifestet. De strikta principer och tolererar inte att ett fel.

Vy över klustret med Service Fabric Explorer:

![Vy över klustret med Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Läs mer om [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Hälsoförfrågningar
Service Fabric visar hälsoförfrågningar för var och en av de stöds [entitetstyper](service-fabric-health-introduction.md#health-entities-and-hierarchy). De kan nås via API: et, med metoder på [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), PowerShell-cmdletar och REST. De här frågorna returnera hela hälsotillståndet information om entiteten: aggregerade hälsotillstånd, entiteten hälsa händelser, underordnade hälsotillstånd (i förekommande fall), ohälsosamt utvärderingar (om enheten inte är felfri) och underordnade hälsostatistik (när tillämpligt).

> [!NOTE]
> En entitet hälsa returneras när den är helt fylld i health store. Enheten måste vara aktiva (inte tas bort) och har en system-rapport. Dess överordnade entiteter i kedjan hierarkin måste också ha systemrapporter. Om någon av dessa villkor inte är uppfyllda hälsotillståndet frågar returnera en [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) med [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` som visar varför entiteten returneras inte.
>
>

Hälsoförfrågningar måste klara i entiteten ID, som beror på vilken enhetstyp. Frågorna accepterar valfria hälsa principparametrar. Om inga hälsoprinciper har angetts i [hälsoprinciper](service-fabric-health-introduction.md#health-policies) från manifestet kluster eller ett program som används för utvärdering. Om manifesten får inte innehålla en definition för hälsoprinciper, som hälsoprinciper standard används för utvärdering. Standard-hälsoprinciper inte tolerera fel. Frågorna även godta filter för att returnera endast delvis underordnade objekt eller händelser--de som ta hänsyn till de angivna filtren. Ett annat filter kan exklusive underordnade statistik.

> [!NOTE]
> Utdatafilter tillämpas på serversidan, så att svara meddelandelagringsstorlek minskas. Vi rekommenderar att du använder utdatafilter för att begränsa de data som returneras i stället använda filter på klientsidan.
>
>

En entitets hälsotillstånd innehåller:

* Sammanställda hälsotillståndet för entiteten. Beräknad av health store baserat på entiteten hälsorapporter, underordnade hälsotillstånd (i förekommande fall) och hälsoprinciper. Läs mer om [entiteten hälsoutvärderingen](service-fabric-health-introduction.md#health-evaluation).  
* Hälsotillstånd händelser på enheten.
* Insamling av hälsotillstånd för alla underordnade för de enheter som kan ha underordnade. Hälsotillstånd innehåller entitet identifierare och aggregerade hälsotillståndet. Om du vill ha hela hälsotillståndet för ett barn anropa frågan hälsotillståndet för den underordnade entitetstypen och ange underordnade identifierare.
* Feltillstånd utvärderingarna som pekar på den rapport som utlöste tillståndet för entiteten om enheten inte är felfri. Utvärderingarna är rekursiv, som innehåller underordnade hälsa utvärderingar som utlöste aktuellt hälsotillstånd. Till exempel rapporterade en watchdog ett fel mot en replik. Programmets hälsotillstånd visar en ohälsosamt utvärdering på grund av en tjänst som ohälsosamt. tjänsten är inte hälsosamt eftersom en partition i fel. partitionen är inte hälsosamt eftersom en replik i fel. repliken är inte hälsosamt eftersom watchdog felrapporten hälsa.
* Hälsostatistik för alla underordnade typer av enheter som har underordnade. Kluster visar det totala antalet program, tjänster, partitioner, repliker och distribuerats entiteter i klustret. Tjänstens hälsa visar det totala antalet partitioner och repliker under den angivna tjänsten.

## <a name="get-cluster-health"></a>Hämta kluster
Returnerar hälsotillståndet för entiteten kluster och innehåller hälsotillstånd för program och noder (underordnade i klustret). Indata:

* [Valfritt] Klustret hälsoprincip som används för att utvärdera noder och klusterhändelser.
* [Valfritt] Programmet hälsa princip kartan med hälsoprinciper som används för att åsidosätta manifestet användningsprinciper.
* [Valfritt] Filter för händelser, noder och program som anger vilka poster som är intressanta och returneras i resultatet (till exempel fel, eller både varningar och fel). Alla händelser, noder och program används för att utvärdera den samman entitetshälsa, oavsett filtret.
* [Valfritt] Filter för att utesluta hälsostatistik.
* [Valfritt] Till att inkludera fabric: / System hälsostatistik i statistiken hälsa. Gäller endast när hälsostatistik inte utesluts. Som standard innehåller hälsostatistik bara statistik för inte programmet System och program.

### <a name="api"></a>API
För att få kluster kan du skapa en `FabricClient` och anropa den [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metod på dess **HealthManager**.

Följande anrop hämtar hälsotillståndet för klustret:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Följande kod hämtar klustret hälsa genom att använda en anpassad klustret hälsoprincip och filter för noder och program. Det anger att hälsostatistik innehåller i fabric: / statistik för filsystemet. Den skapar [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), som innehåller informationen som indata.

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
För att hämta klustret hälsa är [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Tillståndet för klustret är fem noder,, systemprogram och fabric: / WordCount konfigurerats enligt beskrivningen.

Följande cmdlet hämtar kluster med hjälp av standard hälsoprinciper. Sammanställda hälsotillståndet varning eftersom i fabric: / WordCount-program finns i varning. Observera hur ohälsosamt utvärderingar innehåller detaljerad information om de villkor som utlöses aggregerade hälsa.

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

Följande PowerShell-cmdlet hämtar hälsotillståndet för klustret med hjälp av en princip för anpassade program. Den filtrerar resultaten för att få endast program och noderna i felet eller varningen. Därför kan returneras inga noder, som de är alla felfritt. Endast fabric: / WordCount programmet respekterar filtret program. Eftersom den anpassade principen anger att överväga varningar som fel för infrastrukturen: / WordCount program, programmet utvärderas som fel och är därför klustringen.

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
Du kan hämta klustret hälsotillstånd med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-node-health"></a>Hämta nod hälsa
Returnerar hälsotillståndet för en nod entitet och innehåller händelserna hälsotillstånd rapporteras på noden. Indata:

* [Krävs] Nodnamnet som identifierar noden.
* [Valfritt] Klustret hälsa principinställningarna används för att utvärdera hälsan.
* [Valfritt] Filter för händelser som anger vilka poster som är intressanta och returneras i resultatet (till exempel fel, eller både varningar och fel). Alla händelser som används för att utvärdera den samman entitetshälsa, oavsett filtret.

### <a name="api"></a>API
För att få nod hälsa via API, skapa en `FabricClient` och anropa den [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) metod på dess HealthManager.

Följande kod hämtar nod hälsotillståndet för namnet på angivna noden:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Följande kod hämtar nod hälsotillståndet för namnet på angivna noden och skickar i händelsefilter och anpassad princip via [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
För att hämta noden hälsa är [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.
Följande cmdlet hämtar nod hälsa genom att använda standard hälsoprinciper:

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
Du kan hämta nod hälsotillstånd med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-application-health"></a>Hämta programmets hälsotillstånd
Returnerar hälsotillståndet för en entitet i programmet. Den innehåller hälsotillstånd för distribuerade program och tjänsten underordnade. Indata:

* [Krävs] Programmets namn (URI) som identifierar programmet.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta manifestet användningsprinciper.
* [Valfritt] Filter för händelser, tjänster och distribuerade program som anger vilka poster returneras i resultatet (till exempel fel, eller både varningar och fel) är av intresse. Alla händelser, tjänster och distribuerade program används för att utvärdera den samman entitetshälsa, oavsett filtret.
* [Valfritt] Filter för att utesluta hälsa statistik. Om den inte anges i hälsostatistik inkluderar ok, varning och antal fel för alla program underordnade: tjänster, partitioner, repliker, distribuerade program och distribueras servicepaket.

### <a name="api"></a>API
Om du vill hämta programmets hälsotillstånd, skapa en `FabricClient` och anropa den [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) metod på dess HealthManager.

Följande kod hämtar programhälsan för det angivna programnamnet (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Följande kod hämtar programhälsan för det angivna programnamnet (URI) med filter och anpassade principer som angetts via [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

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
För att hämta programmets hälsotillstånd är [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

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

Följande PowerShell-cmdlet skickar i anpassade principer. Den filtrerar underordnade och händelser.

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
Du kan hämta programmets hälsotillstånd med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-service-health"></a>Hämta tjänstens hälsa
Returnerar hälsotillståndet för en tjänst-enhet. Den innehåller hälsotillstånd partition. Indata:

* [Krävs] Tjänstnamn (URI) som identifierar tjänsten.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta manifestet programprincipen.
* [Valfritt] Filter för händelser och partitioner som anger vilka poster returneras i resultatet (till exempel fel, eller både varningar och fel) är av intresse. Alla händelser och partitioner som används för att utvärdera den samman entitetshälsa, oavsett filtret.
* [Valfritt] Filter för att utesluta hälsostatistik. Om inget anges hälsostatistik visa ok, varning, och fel count för alla partitioner och repliker för tjänsten.

### <a name="api"></a>API
För att få tjänstens hälsa via API kan du skapa en `FabricClient` och anropa den [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) metod på dess HealthManager.

I följande exempel hämtas hälsotillståndet för en tjänst med angivet tjänstnamn (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Följande kod hämtar tjänstens hälsa för det angivna tjänstnamnet (URI), som att ange filter och anpassade principer via [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
För att hämta tjänstens hälsa är [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Följande cmdlet hämtar tjänstens hälsa med hjälp av standard hälsoprinciper:

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
Du kan hämta tjänstens hälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-partition-health"></a>Hämta partitionen hälsa
Returnerar hälsotillståndet för en partition entitet. Den innehåller repliken hälsotillstånd. Indata:

* [Krävs] Partitions-ID (GUID) som identifierar partitionen.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta manifestet programprincipen.
* [Valfritt] Filter för händelser och repliker som anger vilka poster returneras i resultatet (till exempel fel, eller både varningar och fel) är av intresse. Alla händelser och repliker används för att utvärdera den samman entitetshälsa, oavsett filtret.
* [Valfritt] Filter för att utesluta hälsostatistik. Om inget annat anges, visar hälsostatistik hur många repliker är i ok, varning och fel tillstånd.

### <a name="api"></a>API
För att få partition hälsa via API, skapa en `FabricClient` och anropa den [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) metod på dess HealthManager. Om du vill ange valfria parametrar, skapa [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
För att hämta partitionen hälsa är [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Följande cmdlet hämtar hälsan för alla partitioner i den **fabric: / WordCount/WordCountService** tjänsten och filtrerar bort repliken hälsotillstånd:

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
Du kan hämta partitionen hälsotillstånd med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-replica-health"></a>Hämta replik hälsa
Returnerar hälsotillståndet för en tillståndskänslig service replik eller en tillståndslös tjänstinstans. Indata:

* [Krävs] Partitions-ID (GUID) och replik-ID som identifierar repliken.
* [Valfritt] Hälsotillstånd princip applikationsparametrarna används för att åsidosätta manifestet användningsprinciper.
* [Valfritt] Filter för händelser som anger vilka poster som är intressanta och returneras i resultatet (till exempel fel, eller både varningar och fel). Alla händelser som används för att utvärdera den samman entitetshälsa, oavsett filtret.

### <a name="api"></a>API
För att få replik hälsa via API, skapa en `FabricClient` och anropa den [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) metod på dess HealthManager. Använd för att ange avancerade parametrar [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
För att hämta replik hälsa är [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Följande cmdlet hämtar hälsotillståndet för den primära repliken för alla partitioner för tjänsten:

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
Du kan hämta replik hälsotillstånd med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-deployed-application-health"></a>Hämta distribuerade programhälsan
Returnerar hälsotillståndet för ett program som distribuerats på en nod entitet. Den innehåller hälsotillstånd för distribuerade tjänsten paketet. Indata:

* [Krävs] Programnamn (URI) och nodnamnet (sträng) som identifierar det distribuerade programmet.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta manifestet användningsprinciper.
* [Valfritt] Filter för händelser och distribuerade tjänstpaket som anger vilka poster som är intressanta och returneras i resultatet (till exempel fel, eller både varningar och fel). Alla händelser och paket för distribuerad tjänst används för att utvärdera den samman entitetshälsa, oavsett filtret.
* [Valfritt] Filter för att utesluta hälsostatistik. Om inget annat anges, visa hälsostatistik antalet distribuerade tjänstpaket i ok, varning och fel hälsotillstånd.

### <a name="api"></a>API
För att få hälsotillståndet för ett program som distribuerats på en nod via API kan du skapa en `FabricClient` och anropa den [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) metod på dess HealthManager. Om du vill ange valfria parametrar [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
För att hämta den distribuerade programhälsan är [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet. Om du vill ta reda på om ett program ska distribueras, kör [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) och titta på underordnade distribuerade program.

Följande cmdlet hämtar hälsotillståndet för den **fabric: / WordCount** program som distribuerats på **_Node_2**.

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
Du kan hämta distribuerade programhälsan med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-deployed-service-package-health"></a>Hämta distribuerade tjänstens hälsa för paketet
Returnerar hälsotillståndet för en distribuerad tjänst paketet entitet. Indata:

* [Krävs] Programnamn (URI), nodnamnet (sträng) och service manifest namn (sträng) som identifierar paketets distribuerade tjänsten.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta manifestet programprincipen.
* [Valfritt] Filter för händelser som anger vilka poster som är intressanta och returneras i resultatet (till exempel fel, eller både varningar och fel). Alla händelser som används för att utvärdera den samman entitetshälsa, oavsett filtret.

### <a name="api"></a>API
För att få hälsotillståndet för en distribuerad tjänstpaket via API kan du skapa en `FabricClient` och anropa den [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) metod på dess HealthManager. Om du vill ange valfria parametrar [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
För att hämta distribuerade tjänstens hälsa för paketet är [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet. För att se om ett program ska distribueras, kör [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) och titta på de distribuerade program. För att se vilken tjänst som paket som är i ett program, visar distribuerad tjänst paketet underordnade i den [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) utdata.

Följande cmdlet hämtar hälsotillståndet för den **WordCountServicePkg** tjänstpaket av den **fabric: / WordCount** program som distribuerats på **_Node_2**. Entiteten har **System.Hosting** rapporter för service-paket och startpunkten aktiveringen och typ av tjänst att registreringen har lyckats.

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
Du kan hämta distribuerade tjänstens hälsa för paketet med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="health-chunk-queries"></a>Segmentet hälsoförfrågningar
Segmentet hälsoförfrågningar kan returnera flera nivåer klustret underordnade (rekursivt) per inkommande trafik. Det har stöd för avancerade filter som gör att många alternativ att välja underordnade ska returneras. Filter kan ange underordnade av den unika identifieraren eller av andra gruppidentifierare och/eller hälsotillstånd. Som standard inkluderas inga underordnade, till skillnad från hälsa kommandon som alltid innehåller underordnade på första nivån.

Den [hälsoförfrågningar](service-fabric-view-entities-aggregated-health.md#health-queries) returnerar endast underordnade på första nivån för den angivna entiteten per krävs filter. Du måste anropa ytterligare hälsa API: er för varje entitet av intresse för att få underordnade underordnade. På samma sätt för att få hälsotillståndet för specifika enheter, måste du anropa en hälsa API för varje entitet. Segmentet frågan avancerad filtrering kan du begära flera objekt av intresse för en fråga, vilket minimerar meddelandelagringsstorlek och antalet meddelanden.

Värdet för segmentet frågan är att du kan få hälsotillståndet för flera kluster entiteter (potentiellt alla kluster entiteter från krävs rot) i ett anrop. Exempelvis kan du ange komplexa hälsa fråga:

* Returnerar endast program i fel och för dessa program innehåller alla tjänster i varning eller fel. Inkludera alla partitioner för returnerade tjänster.
* Returnera endast hälsotillståndet hos fyra program som anges av deras namn.
* Returnera endast hälsotillståndet hos program för en typ för önskat program.
* Returnera alla distribuerade enheter på en nod. Returnerar alla program, alla distribuerade program på den angivna noden och alla distribuerade tjänstpaket på noden.
* Returnera alla repliker i fel. Returnerar alla program, tjänster, partitioner och endast repliker i fel.
* Returnera alla program. Inkludera alla partitioner för en angiven tjänst.

Hälsotillstånd segmentet frågan exponeras för närvarande endast för kluster-entiteten. Den returnerar ett kluster hälsa segment som innehåller:

* Klustret samman hälsotillstånd.
* Hälsotillstånd tillstånd segmentet listan över noder som respektera inkommande trafik.
* Hälsotillstånd tillstånd segmentet listan över program som respektera inkommande trafik. Varje program hälsa tillstånd segment innehåller en segmentet lista med alla tjänster som respektera inkommande trafik och ett segment lista med alla distribuerade program som respekterar filtren. Samma för underordnade tjänster och distribuerade program. På så sätt kan alla entiteter i klustret kan potentiellt returneras om det krävs hierarkiskt.

### <a name="cluster-health-chunk-query"></a>Klustret hälsa segmentet fråga
Returnerar hälsotillståndet för entiteten kluster och innehåller hierarkiska hälsa tillstånd segment med nödvändiga underordnade. Indata:

* [Valfritt] Klustret hälsoprincip som används för att utvärdera noder och klusterhändelser.
* [Valfritt] Programmet hälsa princip kartan med hälsoprinciper som används för att åsidosätta manifestet användningsprinciper.
* [Valfritt] Filter för noder och program som anger vilka poster som är intressanta och returneras i resultatet. Filtren är specifika för en entitet/grupp av enheter eller är tillämpliga på alla enheter på den nivån. Listan över filter kan innehålla ett allmänt filter och/eller filter för specifika identifierare till fine grain entiteter som returneras av frågan. Om den är tom returneras inte underordnade som standard.
  Läs mer om filter på [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) och [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Programmet filter kan rekursivt ange avancerade filter för barn.

Segmentet resultatet innehåller underordnade som respekterar filtren.

Segmentet frågan returnerar för närvarande inte ohälsosamt utvärderingar eller entiteten händelser. Den extra informationen kan hämtas med hjälp av den befintliga kluster hälsa frågan.

### <a name="api"></a>API
För att få klustret hälsa segment, skapa en `FabricClient` och anropa den [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metod på dess **HealthManager**. Du kan skicka in [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) att beskriva hälsoprinciper och avancerade filter.

Följande kod hämtar klustret hälsa segment med avancerade filter.

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
För att hämta klustret hälsa är [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Först ansluta till klustret med hjälp av den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

Följande kod hämtar noder endast om de är i fel utom en viss nod som alltid ska returneras.

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

Följande cmdlet hämtar klustret segment med filter för programmet.

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

Följande cmdlet returnerar alla distribuerade enheter på en nod.

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
Du kan hämta klustret hälsa segment med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) som innehåller hälsoprinciper och avancerade filter som beskrivs i brödtexten.

## <a name="general-queries"></a>Allmänna frågor
Allmänna frågor returnera en lista över Service Fabric-enheter på en viss typ av. De är tillgängliga via API: et (via metoder i **FabricClient.QueryManager**), PowerShell-cmdletar och REST. De här frågorna aggregera underfrågor från flera komponenter. En av dem är de [hälsoarkivet](service-fabric-health-introduction.md#health-store), som fyller på aggregerade hälsotillståndet för varje frågeresultatet.  

> [!NOTE]
> Allmänna frågor returnera aggregerade hälsotillståndet för entiteten och innehåller inte omfattande hälsa data. Om en enhet inte är felfri, kan du följa upp med hälsoförfrågningar att hämta alla dess hälsa information, inklusive händelser, underordnade hälsotillstånd och feltillstånd utvärderingar.
>
>

Om allmänna frågor returnerar ett okänt hälsotillståndet för en entitet, är det möjligt att arkivet för hälsotillstånd inte har fullständig information om entiteten. Det är också möjligt att en underfråga i arkivet för hälsotillstånd inte lyckades (t.ex, det uppstod ett kommunikationsfel eller health store har begränsats). Följa upp med en fråga för hälsa för entiteten. Om underfrågan ett tillfälligt fel, till exempel nätverksproblem, kan den här uppföljning frågan lyckas. Det kan också ge mer information från health store om varför exponeras inte entiteten.

Frågor som innehåller **HealthState** för entiteter är:

* Nodlistan: returnerar listan-noder i klustret (växlat minne).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Programlista: returnerar en lista med program i klustret (växlat minne).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Tjänstlista: returnerar listan över tjänster i ett program (växlat minne).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partitionslista: returnerar en lista över partitioner i en tjänst (växlat minne).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Replikeringslistan: returnerar listan över repliker i en partition (växlat minne).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Distribuerat programlista: returnerar listan med distribuerade program på en nod.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Distribuera tjänsten paketlistan: returnerar listan över tjänstpaket för i ett distribuerat program.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Alla frågor returnerar växlingsbara resultat. Returnera dessa frågor är en lista som härletts från [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Om resultatet inte ryms ett meddelande, returneras endast en sida och en ContinuationToken som spårar där uppräkningen avbröts. Fortsätta att anropa samma fråga och skicka i fortsättningstoken från den föregående frågan att hämta nästa resultat.
>
>

### <a name="examples"></a>Exempel
Följande kod hämtar felaktiga program i klustret:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Följande cmdlet hämtar programinformation för infrastrukturen: / WordCount-programmet. Observera att hälsotillståndet är på varningen.

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

## <a name="cluster-and-application-upgrades"></a>Uppgradering av klustret och program
Under en övervakad uppgradering av kluster och program kontrollerar Service Fabric hälsa för att se till att allt är felfri. Om en enhet är i feltillstånd som utvärderas med hjälp av konfigurerade hälsoprinciper, gäller uppgraderingen uppgraderingen-specifika policys för att avgöra nästa åtgärd. Uppgraderingen kan pausas för att tillåta interaktion från användaren (till exempel korrigera felvillkor eller ändra principer) eller den kan automatiskt återställa till den tidigare fungerande versionen.

Under en *klustret* uppgraderar du uppgraderingsstatus för klustret. Uppgraderingsstatus innehåller felaktiga utvärderingar, peka på vad är i feltillstånd i klustret. Om uppgraderingen återställs på grund av hälsotillståndsproblem med, uppgraderingsstatus kommer ihåg senaste ohälsosamt orsaker. Den här informationen kan hjälpa administratörer att undersöka vad som gick fel när uppgraderingen återställde eller stoppats.

På liknande sätt under en *programmet* uppgraderar alla feltillstånd utvärderingar finns i uppgraderingsstatus för programmet.

Följande visar programmet uppgraderingsstatus för ändrade fabric: / WordCount-programmet. En watchdog rapporterade ett fel på en av dess repliker. Inför uppgraderingen eftersom hälsokontrollerna inte uppfylls.

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

Läs mer om den [uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Använd hälsa utvärderingar för att felsöka
När det finns ett problem med klustret eller ett program, ser hälsotillståndet kluster eller ett program att hitta fel. Feltillstånd utvärderingar innehåller information om vad som utlöste aktuella feltillståndet. Om du behöver, kan du detaljnivån i feltillstånd underordnade enheter att identifiera den bakomliggande orsaken.

Tänk dig ett program ohälsosamt eftersom det inte finns en felrapport på en av dess repliker. Följande Powershell-cmdlet visar ohälsosamt utvärderingar:

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
> Feltillstånd utvärderingar visar det första skälet entiteten utvärderas till aktuella hälsotillstånd. Det kan finnas flera andra händelser som utlöser detta tillstånd, men de är inte återspeglas i utvärderingarna. Om du vill ha mer information, öka detaljnivån hälsa enheterna för att ta reda på alla feltillstånd rapporter i klustret.
>
>

## <a name="next-steps"></a>Nästa steg
[Felsök med hjälp av systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Lägg till anpassade hälsorapporter för Service Fabric](service-fabric-report-health.md)

[Hur du rapporten och kontrollera tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)
