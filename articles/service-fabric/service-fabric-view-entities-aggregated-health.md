---
title: Så här visar du Azure Service Fabric-entiteternas aggregerade hälsotillstånd
description: Beskriver hur du frågar, visar och utvärderar Azure Service Fabric-entiteternas aggregerade hälsotillstånd, genom hälsofrågor och allmänna frågor.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d02d8f717801bf51e43c9dafa5eb9379d0737674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464127"
---
# <a name="view-service-fabric-health-reports"></a>Visa hälsorapporter för Service Fabric
Azure Service Fabric introducerar en [hälsomodell](service-fabric-health-introduction.md) med hälsoentiteter där systemkomponenter och watchdogs kan rapportera lokala villkor som de övervakar. [Hälsoarkivet](service-fabric-health-introduction.md#health-store) sammanställer alla hälsodata för att avgöra om entiteter är felfria.

Klustret fylls automatiskt med hälsorapporter som skickas av systemkomponenterna. Läs mer på [Använd systemhälsorapporter för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric erbjuder flera sätt att få aggregerad hälsa för entiteterna:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) eller andra visualiseringsverktyg
* Hälsofrågor (via PowerShell, API eller REST)
* Allmänna frågor som returnerar en lista över entiteter som har hälsotillstånd som en av egenskaperna (via PowerShell, API eller REST)

Om du vill demonstrera dessa alternativ ska vi använda ett lokalt kluster med fem noder och [programmet fabric:/WordCount](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg). **Fabric:/WordCount-programmet** innehåller två standardtjänster, en tillståndskänslig tjänst av `WordCountServiceType`typen `WordCountWebServiceType`och en tillståndslös tjänst av typen . Jag ändrade `ApplicationManifest.xml` att kräva sju mål repliker för tillståndskänsliga tjänsten och en partition. Eftersom det bara finns fem noder i klustret rapporterar systemkomponenterna en varning på tjänstpartitionen eftersom den ligger under antalet mål.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Hälsoinformationsinformation i serviceinfrastruktur
Service Fabric Explorer ger en visuell vy av klustret. I bilden nedan kan du se att:

* **Programstrukturen:/WordCount** är röd (av misstag) eftersom den har en felhändelse som rapporterats av **MyWatchdog** för egenskapen **Tillgänglighet**.
* En av dess tjänster, **fabric:/WordCount/WordCountService** är gul (i varning). Tjänsten är konfigurerad med sju repliker och klustret har fem noder, så två repliker kan inte placeras. Även om det inte visas här, är tjänstpartitionen `System.FM` gul `Partition is below target replica or instance count`på grund av en systemrapport från att säga att . Den gula partitionen utlöser den gula tjänsten.
* Klustret är rött på grund av det röda programmet.

Utvärderingen använder standardprinciper från klustermanifestet och programmanifestet. De är strikta politiska åtgärder och tolererar inte något misslyckande.

Vy över klustret med Service Fabric Explorer:

![Vy över klustret med Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Läs mer om [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Hälsofrågor
Service Fabric exponerar hälsofrågor för var och en av de [entitetstyper](service-fabric-health-introduction.md#health-entities-and-hierarchy)som stöds . De kan nås via API: et med metoder på [FabricClient.HealthManager,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet)PowerShell-cmdlets och REST. Dessa frågor returnerar fullständig hälsoinformation om entiteten: aggregerat hälsotillstånd, entitetshälsohändelser, underordnade hälsotillstånd (i förekommande fall), felaktiga utvärderingar (när entiteten inte är felfri) och barnhälsostatistik (när tillämpliga).

> [!NOTE]
> En hälsoentitet returneras när den är helt ifylld i hälsoarkivet. Entiteten måste vara aktiv (inte borttagen) och ha en systemrapport. Dess överordnade entiteter i hierarkikedjan måste också ha systemrapporter. Om något av dessa villkor inte är uppfyllda returnerar hälsofrågorna ett [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) med [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` som visar varför entiteten inte returneras.
>
>

Hälsofrågorna måste passera i entitetsidentifieraren, vilket beror på entitetstypen. Frågorna accepterar valfria hälsoprincipparametrar. Om inga hälsoprinciper anges används [hälsoprinciperna](service-fabric-health-introduction.md#health-policies) från klustret eller programmanifestet för utvärdering. Om manifesten inte innehåller någon definition för hälsoprinciper används standardhälsoprinciperna för utvärdering. Standardhälsoprinciperna tolererar inga fel. Frågorna accepterar också filter för att returnera endast partiella underordnade eller händelser - de som respekterar de angivna filtren. Ett annat filter gör det möjligt att utesluta barnstatistik.

> [!NOTE]
> Utdatafiltren används på serversidan, så meddelandesvarsstorleken minskas. Vi rekommenderar att du använder utdatafiltren för att begränsa de returnerade data, i stället för att använda filter på klientsidan.
>
>

En entitets hälsotillstånd innehåller:

* Entitetens aggregerade hälsotillstånd. Beräknad av hälsoarkivet baserat på entitetshälsorapporter, underordnade hälsotillstånd (i förekommande fall) och hälsoprinciper. Läs mer om [utvärdering av entitetshälsa](service-fabric-health-introduction.md#health-evaluation).  
* Hälsohändelserna på entiteten.
* Insamling av hälsotillstånd för alla barn för de entiteter som kan få barn. Hälsotillstånden innehåller entitetsidentifierare och det aggregerade hälsotillståndet. Om du vill ha fullständig hälsotillstånd för ett barn anropar du frågehälsan för den underordnade entitetstypen och skickar in den underordnade identifieraren.
* De felaktiga utvärderingar som pekar på rapporten som utlöste tillståndet för entiteten, om entiteten inte är felfri. Utvärderingarna är rekursiva och innehåller de hälsoutvärderingar för barn som utlöste det aktuella hälsotillståndet. En watchdog rapporterade till exempel ett fel mot en replik. Programhälsan visar en felaktig utvärdering på grund av en felaktig tjänst. tjänsten är felfritt på grund av en partition av misstag; partitionen är felfritt på grund av en replik av misstag; repliken är felfritt på grund av hälsorapporten för watchdog-fel.
* Hälsostatistiken för alla underordnade typer av entiteter som har barn. Klusterhälsa visar till exempel det totala antalet program, tjänster, partitioner, repliker och distribuerade entiteter i klustret. Tjänstens hälsotillstånd visar det totala antalet partitioner och repliker under den angivna tjänsten.

## <a name="get-cluster-health"></a>Hämta klusterhälsa
Returnerar hälsotillståndet för klusterentiteten och innehåller hälsotillstånden för program och noder (underordnade kluster). Indata:

* [Valfritt] Klusterhälsoprincipen som används för att utvärdera noderna och klusterhändelserna.
* [Valfritt] Programhälsoprincipmappningen med hälsoprinciper som används för att åsidosätta programmanifestprinciperna.
* [Valfritt] Filter för händelser, noder och program som anger vilka poster som är av intresse och ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser, noder och program används för att utvärdera entiteten aggregerade hälsa, oavsett filtret.
* [Valfritt] Filtrera för att utesluta hälsostatistik.
* [Valfritt] Filtrera för att inkludera fabric:/Systemhälsostatistik i hälsostatistiken. Endast tillämpligt när hälsostatistiken inte är utesluten. Som standard omfattar hälsostatistiken endast statistik för användarprogram och inte systemprogrammet.

### <a name="api"></a>API
Skapa en `FabricClient` och anropa metoden [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) för att få klusterhälsa och anropa metoden GetClusterHealthAsync på sin **HealthManager**.

Följande anrop får klusterhälsan:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Följande kod får klustrets hälsotillstånd med hjälp av en anpassad klusterhälsoprincip och filter för noder och program. Den anger att hälsostatistiken omfattar statistiken för infrastrukturen:/Systemstatistik. Det skapar [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), som innehåller indatainformation.

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
Cmdleten för att få klusterhälsan är [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Klustrets tillstånd är fem noder, systemprogrammet och infrastrukturen:/WordCount konfigurerad enligt beskrivningen.

Följande cmdlet får klusterhälsa med hjälp av standardhälsoprinciper. Det aggregerade hälsotillståndet är varning, eftersom fabric:/WordCount-programmet är i varning. Observera hur de felaktiga utvärderingarna innehåller information om de villkor som utlöste den aggregerade hälsan.

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

Följande PowerShell-cmdlet får hälsotillståndet för klustret med hjälp av en anpassad programprincip. Den filtrerar resultat för att bara få program och noder i fel eller varning. Därför returneras inga noder, eftersom de alla är felfria. Endast fabric:/WordCount-programmet respekterar programfiltret. Eftersom den anpassade principen anger att varningar ska betraktas som fel för fabric:/WordCount-programmet utvärderas programmet som av misstag, och det gör även klustret.

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
Du kan få klusterhälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-node-health"></a>Få nodhälsa
Returnerar hälsotillståndet för en nodentitet och innehåller hälsohändelser som rapporterats på noden. Indata:

* [Krävs] Nodnamnet som identifierar noden.
* [Valfritt] De klusterhälsoprincipinställningar som används för att utvärdera hälsotillstånd.
* [Valfritt] Filter för händelser som anger vilka transaktioner som är av intresse och ska returneras i resultatet (till exempel fel eller både varningar och fel). Alla händelser används för att utvärdera entiteten aggregerade hälsa, oavsett filtret.

### <a name="api"></a>API
För att få nodhälsa genom `FabricClient` API: et skapar du en och anropar [Metoden GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) på dess HealthManager.

Följande kod får nodhälsan för det angivna nodnamnet:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Följande kod får nodhälsan för det angivna nodnamnet och passerar i händelsefilter och anpassad princip via [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att få nodens hälsa är [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)
Följande cmdlet får nodens hälsotillstånd med hjälp av standardhälsoprinciper:

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

Följande cmdlet får hälsotillståndet för alla noder i klustret:

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
Du kan få nodhälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-application-health"></a>Få programmets hälsa
Returnerar hälsotillståndet för en programentitet. Den innehåller hälsotillstånden för de distribuerade program- och tjänstbarnen. Indata:

* [Krävs] Programnamnet (URI) som identifierar programmet.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta programmanifestprinciperna.
* [Valfritt] Filter för händelser, tjänster och distribuerade program som anger vilka poster som är av intresse och ska returneras i resultatet (till exempel fel eller både varningar och fel). Alla händelser, tjänster och distribuerade program används för att utvärdera entiteten aggregerade hälsa, oavsett filtret.
* [Valfritt] Filtrera för att utesluta hälsostatistiken. Om det inte anges innehåller hälsostatistiken ok, varning och felantal för alla programmed underordnade program: tjänster, partitioner, repliker, distribuerade program och distribuerade tjänstpaket.

### <a name="api"></a>API
Skapa en `FabricClient` och anropa metoden [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) på hälsohanten för att få programhälsa.

Följande kod hämtar programmets hälsotillstånd för det angivna programnamnet (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Följande kod hämtar programmets hälsotillstånd för det angivna programnamnet (URI), med filter och anpassade principer som anges via [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

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
Den cmdlet att få ansökan hälsa är [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Följande cmdlet returnerar hälsotillståndet för **programmet fabric:/WordCount:**

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

Följande PowerShell-cmdlet passerar i anpassade principer. Det filtrerar också barn och händelser.

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
Du kan få programhälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-service-health"></a>Få tjänstens hälsa
Returnerar hälsotillståndet för en tjänstentitet. Den innehåller hälsotillstånden för partitionen. Indata:

* [Krävs] Tjänstnamnet (URI) som identifierar tjänsten.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta programmanifestprincipen.
* [Valfritt] Filter för händelser och partitioner som anger vilka poster som är av intresse och ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser och partitioner används för att utvärdera entiteten aggregerade hälsa, oavsett filtret.
* [Valfritt] Filtrera för att utesluta hälsostatistik. Om det inte anges visar hälsostatistiken ok- och varnings- och felantalet för alla partitioner och repliker av tjänsten.

### <a name="api"></a>API
Skapa en `FabricClient` och anropa [metoden GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) på hälsohantverket för att få tjänstens hälsotillstånd via API:et.

I följande exempel får du hälsotillståndet för en tjänst med angivet tjänstnamn (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Följande kod hämtar tjänstens hälsotillstånd för det angivna tjänstnamnet (URI), som anger filter och anpassade principer via [ServiceHealthQueryDescription:](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription)

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att få tjänstens hälsa är [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Följande cmdlet får tjänstens hälsotillstånd genom att använda standardhälsoprinciper:

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
Du kan få tjänstens hälsotillstånd med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-partition-health"></a>Få partitionshälsa
Returnerar hälsotillståndet för en partitionentitet. Den innehåller repliken hälsotillstånd. Indata:

* [Krävs] Partitions-ID (GUID) som identifierar partitionen.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta programmanifestprincipen.
* [Valfritt] Filter för händelser och repliker som anger vilka poster som är av intresse och ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser och repliker används för att utvärdera entiteten aggregerade hälsa, oavsett filtret.
* [Valfritt] Filtrera för att utesluta hälsostatistik. Om det inte anges visar hälsostatistiken hur många repliker som finns i ok, varning och feltillstånd.

### <a name="api"></a>API
Skapa en `FabricClient` och anropa metoden [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) på hälsohanten för att få partitionshälsa genom API:et. Om du vill ange valfria parametrar skapar du [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att få partitionen hälsa är [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Följande cmdlet får hälsotillståndet för alla partitioner i **infrastrukturen:/WordCount/WordCountService-tjänsten** och filtrerar bort replikhälsotillstånd:

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
Du kan få partitionshälsa med en [GET-begäran](/rest/api/servicefabric/sfclient-api-getpartitionhealth) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-replica-health"></a>Hämta replikhälsa
Returnerar hälsotillståndet för en tillståndskänslig tjänstreplik eller en tillståndslös tjänstinstans. Indata:

* [Krävs] Partitions-ID (GUID) och replik-ID som identifierar repliken.
* [Valfritt] Programhälsoprincipparametrarna som används för att åsidosätta programmanifestprinciperna.
* [Valfritt] Filter för händelser som anger vilka transaktioner som är av intresse och ska returneras i resultatet (till exempel fel eller både varningar och fel). Alla händelser används för att utvärdera entiteten aggregerade hälsa, oavsett filtret.

### <a name="api"></a>API
För att få repliken hälsa `FabricClient` genom API, skapa en och anropa [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) metoden på sin HealthManager. Om du vill ange avancerade parametrar använder du [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Den cmdlet för att få replika hälsa är [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Följande cmdlet får hälsotillståndet för den primära repliken för alla partitioner i tjänsten:

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
Du kan hämta replikhälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-deployed-application-health"></a>Hämta e-programmets hälsotillstånd
Returnerar hälsotillståndet för ett program som distribueras på en nodentitet. Den innehåller hälsotillstånden för det distribuerade tjänstpaketet. Indata:

* [Krävs] Programnamnet (URI) och nodnamnet (strängen) som identifierar det distribuerade programmet.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta programmanifestprinciperna.
* [Valfritt] Filter för händelser och distribuerade tjänstpaket som anger vilka transaktioner som är av intresse och ska returneras i resultatet (till exempel fel eller både varningar och fel). Alla händelser och distribuerade tjänstpaket används för att utvärdera entiteten aggregerade hälsa, oavsett filtret.
* [Valfritt] Filtrera för att utesluta hälsostatistik. Om det inte anges visar hälsostatistiken antalet distribuerade tjänstpaket i ok,varning och felhälsotillstånd.

### <a name="api"></a>API
Om du vill få hälsotillståndet för ett program `FabricClient` som distribueras på en nod via API:et skapar du en och anropar metoden [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) på dess HealthManager. Om du vill ange valfria parametrar använder du [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta den distribuerade programhälsan är [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Om du vill ta reda på var ett program distribueras kör du [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) och tittar på de distribuerade programbarnen.

Följande cmdlet får hälsotillståndet för **fabric:/WordCount-programmet** som distribueras på **_Node_2**.

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
Du kan få distribuerade programhälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="get-deployed-service-package-health"></a>Få hälsa för distribuerade tjänstpaket
Returnerar hälsotillståndet för en distribuerad tjänstpaketentitet. Indata:

* [Krävs] Programnamnet (URI), nodnamnet (strängen) och tjänstmanifestnamnet (strängen) som identifierar det distribuerade tjänstpaketet.
* [Valfritt] Programmets hälsoprincip som används för att åsidosätta programmanifestprincipen.
* [Valfritt] Filter för händelser som anger vilka transaktioner som är av intresse och ska returneras i resultatet (till exempel fel eller både varningar och fel). Alla händelser används för att utvärdera entiteten aggregerade hälsa, oavsett filtret.

### <a name="api"></a>API
Om du vill ha hälsotillståndet för ett `FabricClient` distribuerat tjänstpaket via API:et skapar du en och anropar metoden [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) på dess HealthManager. Om du vill ange valfria parametrar använder du [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Cmdlet för att hämta den distribuerade tjänstpaketet hälsotillstånd är [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Om du vill se var ett program distribueras kör du [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) och tittar på de distribuerade programmen. Om du vill se vilka tjänstpaket som finns i ett program tittar du på de distribuerade tjänstpaketen som är underordnade i [Get-ServiceFabricDeployedApplicationHealth-utdata.](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps)

Följande cmdlet får hälsotillståndet för **WordCountServicePkg-tjänstpaketet** för **fabric:/WordCount-programmet** som distribueras på **_Node_2**. Entiteten har **System.Hosting-rapporter** för lyckad aktivering av servicepaket och startpunkt och registrering av tjänsttyp.

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
Du kan få åtkomstbeställd tjänstpakethälsa med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) som innehåller hälsoprinciper som beskrivs i brödtexten.

## <a name="health-chunk-queries"></a>Frågor om hälsosegment
Hälsosegmentfrågorna kan returnera flera klusterbarn (rekursivt), per indatafilter. Den stöder avancerade filter som tillåter stor flexibilitet i valet av barn som ska returneras. Filtren kan ange underordnade med den unika identifieraren eller andra gruppidentifierare och/eller hälsotillstånd. Som standard inkluderas inga underordnade, i motsats till hälsokommandon som alltid innehåller underordnade första nivån.

[Hälsofrågorna returnerar](service-fabric-view-entities-aggregated-health.md#health-queries) endast underordnade på första nivån för den angivna entiteten per obligatoriska filter. För att få barnen till barnen måste du ringa ytterligare hälso-API:er för varje enhet av intresse. På samma sätt, för att få hälsotillståndet för specifika entiteter, måste du anropa ett hälso-API för varje önskad entitet. Med avancerad filtrering av segmentfrågan kan du begära flera objekt av intresse i en fråga, vilket minimerar meddelandestorleken och antalet meddelanden.

Värdet för segmentfrågan är att du kan få hälsotillstånd för fler klusterentiteter (eventuellt alla klusterentiteter som börjar vid obligatorisk rot) i ett anrop. Du kan uttrycka komplexa hälsofrågor som:

* Returnera endast program av misstag, och för dessa program inkluderar alla tjänster i varning eller fel. För returnerade tjänster, inkludera alla partitioner.
* Returnera endast hälsotillståndet för fyra program, som anges av deras namn.
* Returnera endast hälsotillståndet för program av önskad programtyp.
* Returnera alla distribuerade entiteter på en nod. Returnerar alla program, alla distribuerade program på den angivna noden och alla distribuerade tjänstpaket på den noden.
* Returnera alla repliker av misstag. Returnerar alla program, tjänster, partitioner och endast repliker av misstag.
* Returnera alla program. För en angiven tjänst, inkludera alla partitioner.

För närvarande är hälsosegmentfrågan endast exponerad för klusterentiteten. Den returnerar ett klusterhälsosegment som innehåller:

* Klustret aggregerade hälsotillståndet.
* Listan över hälsotillståndssegment med noder som respekterar indatafilter.
* Listan över hälsotillståndssegment för program som respekterar indatafilter. Varje program hälsotillstånd segment innehåller en segment lista med alla tjänster som respekterar indatafilter och en segment lista med alla distribuerade program som respekterar filtren. Samma för barn till tjänster och distribuerade program. På så sätt kan alla entiteter i klustret returneras om så önskas, på ett hierarkiskt sätt.

### <a name="cluster-health-chunk-query"></a>Klustrets kurluststråfn
Returnerar hälsotillståndet för klusterentiteten och innehåller hierarkiska hälsotillståndssegment för obligatoriska underordnade. Indata:

* [Valfritt] Klusterhälsoprincipen som används för att utvärdera noderna och klusterhändelserna.
* [Valfritt] Programhälsoprincipmappningen med hälsoprinciper som används för att åsidosätta programmanifestprinciperna.
* [Valfritt] Filter för noder och program som anger vilka transaktioner som är av intresse och ska returneras i resultatet. Filtren är specifika för en entitet/grupp av entiteter eller är tillämpliga på alla entiteter på den nivån. Listan över filter kan innehålla ett allmänt filter och/eller filter för specifika identifierare för finkorniga entiteter som returneras av frågan. Om de är tomma returneras inte barnen som standard.
  Läs mer om filtren på [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) och [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Programfiltren kan rekursivt ange avancerade filter för underordnade.

Segmentet resultatet inkluderar de underordnade som respekterar filtren.

För närvarande returnerar segmentfrågan inte felaktiga utvärderingar eller entitetshändelser. Den extra informationen kan erhållas med hjälp av den befintliga klusterhälsofrågan.

### <a name="api"></a>API
Om du vill hämta `FabricClient` klusterhälsosegmentet skapar du en och anropar [metoden GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) på sin **HealthManager**. Du kan skicka in [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) för att beskriva hälsoprinciper och avancerade filter.

Följande kod hämtar klusterhälsosegment med avancerade filter.

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
Cmdleten för att få klusterhälsan är [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Följande kod får noder endast om de är i Fel med undantag för en viss nod, som alltid ska returneras.

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

Följande cmdlet hämtar klustersegment med programfilter.

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
Du kan hämta klusterhälsosegment med en [GET-begäran](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) eller en [POST-begäran](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) som innehåller hälsoprinciper och avancerade filter som beskrivs i brödtexten.

## <a name="general-queries"></a>Allmänna frågor
Allmänna frågor returnerar en lista över Service Fabric-entiteter av en angiven typ. De exponeras via API:et (via metoderna **fabricclient.querymanager),** PowerShell-cmdlets och REST. Dessa frågor aggregera underfrågor från flera komponenter. En av dem är [hälsoarkivet](service-fabric-health-introduction.md#health-store), som fyller i det aggregerade hälsotillståndet för varje frågeresultat.  

> [!NOTE]
> Allmänna frågor returnerar aggregerade hälsotillstånd för entiteten och innehåller inte omfattande hälsodata. Om en entitet inte är felfri kan du följa upp med hälsofrågor för att få all hälsoinformation, inklusive händelser, barnhälsotillstånd och felaktiga utvärderingar.
>
>

Om allmänna frågor returnerar ett okänt hälsotillstånd för en entitet är det möjligt att hälsoarkivet inte har fullständiga data om entiteten. Det är också möjligt att en underavdelning till hälsobutiken inte lyckades (till exempel uppstod ett kommunikationsfel eller så begränsades hälsoarkivet). Följ upp med en hälsofråga för entiteten. Om underfrågan påträffade tillfälliga fel, till exempel nätverksproblem, kan den här uppföljningsfrågan lyckas. Det kan också ge dig mer information från hälsobutiken om varför entiteten inte exponeras.

De frågor som innehåller **HealthState** för entiteter är:

* Nodlista: Returnerar listnoderna i klustret (sökt).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Programlista: Returnerar listan över program i klustret (sökt).
  * API: [FabricClient.QueryClient.getApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Tjänstlista: Returnerar listan över tjänster i ett program (sökt).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partitionslista: Returnerar listan över partitioner i en tjänst (sökt).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Repliklista: Returnerar listan över repliker i en partition (sidst).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Distribuerad programlista: Returnerar listan över distribuerade program på en nod.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Distribuerad tjänstpaketlista: Returnerar listan över tjänstpaket i ett distribuerat program.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Vissa frågor returnerar växlingsresultat. Returen av dessa frågor är en lista som härleds från [PagedList\<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Om resultaten inte stämmer av ett meddelande returneras bara en sida och en ContinuationToken som spårar var uppräkningen stoppas. Fortsätt att anropa samma fråga och skicka in fortsättningstoken från föregående fråga för att få nästa resultat.

### <a name="examples"></a>Exempel
Följande kod hämtar de felaktiga programmen i klustret:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Följande cmdlet hämtar programinformation för fabric:/WordCount-programmet. Observera att hälsotillståndet är på varning.

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

Följande cmdlet hämtar tjänsterna med ett hälsotillstånd:

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

## <a name="cluster-and-application-upgrades"></a>Kluster- och programuppgraderingar
Under en övervakad uppgradering av klustret och programmet kontrollerar Service Fabric hälsotillståndet för att säkerställa att allt förblir felfritt. Om en entitet är felfritt som utvärderas med hjälp av konfigurerade hälsoprinciper, tillämpar uppgraderingen uppgraderingsspecifika principer för att avgöra nästa åtgärd. Uppgraderingen kan pausas för att tillåta användarinteraktion (t.ex. åtgärda felvillkor eller ändra principer), eller så kan den automatiskt återställa till den tidigare bra versionen.

Under *cluster* en klusteruppgradering kan du hämta statusen klusteruppgradering. Uppgraderingsstatusen innehåller felaktiga utvärderingar, som pekar på vad som är felfritt i klustret. Om uppgraderingen återställs på grund av hälsoproblem kommer uppgraderingsstatusen ihåg de senaste felaktiga orsakerna. Den här informationen kan hjälpa administratörer att undersöka vad som gick fel efter att uppgraderingen återställts eller stoppats.

På samma sätt *application* finns eventuella felaktiga utvärderingar i programuppgraderingsstatusen under en programuppgradering.

Följande visar programuppgraderingsstatusen för ett modifierat fabric:/WordCount-program. En watchdog rapporterade ett fel på en av dess repliker. Uppgraderingen rullas tillbaka eftersom hälsokontrollerna inte respekteras.

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

Läs mer om uppgraderingen av [Service Fabric-programmet](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Använd hälsoutvärderingar för felsökning
När det finns ett problem med klustret eller ett program, titta på klustret eller programmets hälsa för att identifiera vad som är fel. De felaktiga utvärderingarna ger information om vad som utlöste det aktuella felaktiga tillståndet. Om du behöver kan du öka detaljnivån i felaktiga underordnade enheter för att identifiera grundorsaken.

Tänk till exempel på ett program som inte är fel eftersom det finns en felrapport på en av dess repliker. Följande Powershell-cmdlet visar de felaktiga utvärderingarna:

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
> De felaktiga utvärderingarna visar den första orsaken till att entiteten utvärderas till aktuellt hälsotillstånd. Det kan finnas flera andra händelser som utlöser det här tillståndet, men de återspeglas inte i utvärderingarna. Om du vill ha mer information bör du öka detaljnivån i hälsoentiteterna för att ta reda på alla felaktiga rapporter i klustret.
>
>

## <a name="next-steps"></a>Nästa steg
[Felsök med hjälp av systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Lägga till anpassade hälsorapporter för Service Fabric](service-fabric-report-health.md)

[Så här rapporterar och kontrollerar du tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)
