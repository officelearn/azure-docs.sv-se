---
title: Fråga efter klusterhändelser med hjälp av EventStore-API:erna
description: Lär dig hur du använder Azure Service Fabric EventStore API:er för att fråga efter plattformshändelser
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 48350caef6bdaafda9aff7ac776d67b314aeaf8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614408"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Api:er för Frågehändelseaffärer för klusterhändelser

Den här artikeln beskriver hur du frågar om EventStore-API:er som är tillgängliga i Service Fabric version 6.2 och senare – om du vill veta mer om EventStore-tjänsten finns i [EventStore-tjänstöversikten](service-fabric-diagnostics-eventstore.md). För närvarande kan EventStore-tjänsten endast komma åt data under de senaste 7 dagarna (detta baseras på klustrets diagnostikdatalagringsprincip).

>[!NOTE]
>EventStore-API:erna är GA från och med Service Fabric version 6.4 för endast Windows-kluster som körs på Azure.

EventStore-API:erna kan nås direkt via en REST-slutpunkt eller programmässigt. Beroende på frågan finns det flera parametrar som krävs för att samla in rätt data. Dessa parametrar omfattar vanligtvis:
* `api-version`: vilken version av EventStore-API:erna du använder
* `StartTimeUtc`: definierar början av den period du är intresserad av att titta på
* `EndTimeUtc`: slutet av tidsperioden

Utöver dessa parametrar finns det även valfria parametrar, till exempel:
* `timeout`: åsidosätta standardtidsgränsen på 60 sekunder för att utföra begäran
* `eventstypesfilter`: Detta ger dig möjlighet att filtrera efter specifika händelsetyper
* `ExcludeAnalysisEvents`: returnera inte "Analys"-händelser. Som standard returneras EventStore-frågor med "analys"-händelser där det är möjligt. Analyshändelser är rikare operativa kanalhändelser som innehåller ytterligare sammanhang eller information utöver en vanlig Service Fabric-händelse och ger mer djup.
* `SkipCorrelationLookup`: leta inte efter potentiella korrelerade händelser i klustret. Som standard försöker EventStore korrelera händelser över ett kluster och länka samman dina händelser när det är möjligt. 

Varje entitet i ett kluster kan vara frågor om händelser. Du kan också fråga efter händelser för alla entiteter av typen. Du kan till exempel fråga efter händelser efter en viss nod eller för alla noder i klustret. Den aktuella uppsättningen entiteter som du kan fråga efter händelser för är (med hur frågan skulle struktureras):
* Kluster:`/EventsStore/Cluster/Events`
* Noder:`/EventsStore/Nodes/Events`
* Nod:`/EventsStore/Nodes/<NodeName>/$/Events`
* Program:`/EventsStore/Applications/Events`
* Program:`/EventsStore/Applications/<AppName>/$/Events`
* Tjänster:`/EventsStore/Services/Events`
* Tjänst:`/EventsStore/Services/<ServiceName>/$/Events`
* Partitioner:`/EventsStore/Partitions/Events`
* Partition:`/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliker:`/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replika:`/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>När du refererar till ett program eller tjänstnamn behöver frågan inte innehålla "fabric:/" Prefix. Om ditt program eller tjänstnamn har ett "/" i dem växlar du dessutom den till ett "~" för att hålla frågan igång. Om ditt program till exempel visas som "fabric:/App1/FrontendApp" struktureras `/EventsStore/Applications/App1~FrontendApp/$/Events`dina appspecifika frågor som .
>Dessutom visas hälsorapporter för tjänster idag under motsvarande program, `DeployedServiceHealthReportCreated` så du skulle fråga efter händelser för rätt programentitet. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Fråga EventStore via REST API-slutpunkter

Du kan fråga EventStore direkt via en `GET` REST-slutpunkt genom att göra förfrågningar till: `<your cluster address>/EventsStore/<entity>/Events/`.

För att till exempel fråga efter `2018-04-03T18:00:00Z` alla `2018-04-04T18:00:00Z`klusterhändelser mellan och skulle din begäran se ut:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Detta kan antingen returnera några händelser eller listan över händelser som returneras i json:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Här kan vi `2018-04-03T18:00:00Z` se `2018-04-04T18:00:00Z`att mellan och , detta kluster framgångsrikt avslutat `"CurrentClusterVersion": "0.0.0.0:"` `"TargetClusterVersion": "6.2:1.0"`sin `"OverallUpgradeElapsedTimeInMs": "120196.5212"`första uppgradering när den först stod upp, från till , i .

## <a name="query-the-eventstore-programmatically"></a>Fråga EventStore programmässigt

Du kan också fråga EventStore programmässigt via [service fabric-klientbiblioteket](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

När du har konfigurerat Din Service Fabric-klient kan du fråga efter händelser genom att komma åt EventStore så här:`sfhttpClient.EventStore.<request>`

Här är en exempelbegäran för `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z`alla klusterhändelser mellan och , via `GetClusterEventListAsync` funktionen.

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

## <a name="sample-scenarios-and-queries"></a>Exempel på scenarier och frågor

Här är några exempel på hur du kan anropa REST-API:erna för event store för att förstå statusen för klustret.

*Klusteruppgraderingar:*

Om du vill se den senaste gången klustret lyckades eller försökte uppgraderas förra veckan kan du fråga API:erna för nyligen slutförda uppgraderingar till klustret genom att fråga efter händelserna "ClusterUpgradeCompleted" i EventStore:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problem med klusteruppgradering:*

På samma sätt, om det fanns problem med en nyligen kluster uppgradering, kan du fråga efter alla händelser för klustret entiteten. Du ser olika händelser, inklusive inledandet av uppgraderingar och varje UD som uppgraderingen rullade igenom framgångsrikt. Du kommer också att se händelser för den punkt där återställningen startade och motsvarande hälsohändelser. Här är frågan du skulle använda för detta:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Nodstatusändringar:*

Om du vill visa ändringar i nodstatusen under de senaste dagarna – när noder gick upp eller ned eller aktiverades eller inaktiverades (antingen av plattformen, kaostjänsten eller från användarindata) – använder du följande fråga:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Programhändelser:*

Du kan också spåra dina senaste programdistributioner och uppgraderingar. Använd följande fråga för att se alla programhändelser i klustret:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historisk hälsa för en ansökan:*

Förutom att bara se programlivscykelhändelser kanske du också vill se historiska data om hälsotillståndet för ett visst program. Du kan göra detta genom att ange det programnamn som du vill samla in data för. Använd den här frågan för att `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`hämta alla programhälsohändelser: . Om du vill inkludera hälsohändelser som kan ha upphört att gälla (gått `,ApplicationHealthReportExpired` vidare till live (TTL)), lägg till i slutet av frågan för att filtrera på två typer av händelser.

*Historisk hälsa för alla tjänster i "myApp":*

För närvarande visas hälsorapporthändelser `DeployedServicePackageNewHealthReport` för tjänster som händelser under motsvarande programentitet. Om du vill se hur det har gått med dina tjänster för "App1" använder du följande fråga:`https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Omkonfigurering av partitioner:*

Om du vill se alla partitionsrörelser som `PartitionReconfigured` hände i klustret frågar du efter händelsen. Detta kan hjälpa dig att ta reda på vilka arbetsbelastningar som kördes på vilken nod vid specifika tidpunkter, när du diagnostiserar problem i klustret. Här är en exempelfråga som gör det:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Kaos tjänst:*

Det finns en händelse för när Chaos-tjänsten startas eller stoppas som exponeras på klusternivå. Om du vill se din senaste användning av Chaos-tjänsten använder du följande fråga:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

