---
title: Fråga för klusterhändelser med hjälp av the EventStore APIs i Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig hur du använder Azure Service Fabric EventStore APIs att fråga efter plattformshändelser
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 1b0b369f0021580d3add583f001bad04c70b03fd
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661759"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Fråga EventStore APIs för klusterhändelser

Den här artikeln beskriver hur du frågar the EventStore APIs som är tillgängliga i Service Fabric version 6.2 och senare – om du vill lära dig mer om EventStore-tjänsten finns i den [EventStore tjänstöversikt](service-fabric-diagnostics-eventstore.md). För närvarande kan tjänsten EventStore kan endast komma åt data för de senaste 7 dagarna (Detta baseras på ditt kluster diagnostik databevarandeprincip).

>[!NOTE]
>The EventStore APIs är allmänt tillgänglig från och med Service Fabric version 6.4 för endast Windows kluster som körs på Azure.

The EventStore APIs kan nås direkt via en REST-slutpunkt eller programmässigt. Beroende på frågan finns det flera parametrar som krävs för att samla in rätt data. Det är vanligtvis dessa parametrar:
* `api-version`: versionen av the EventStore APIs du använder
* `StartTimeUtc`: definierar Periodstart du är intresserad av att titta på
* `EndTimeUtc`: slutet av hur lång tid

Förutom dessa parametrar finns valfria parametrar finns också, till exempel som:
* `timeout`: åsidosätter du standardvärdet 60 andra för timeout för att utföra begäran igen
* `eventstypesfilter`: Det ger dig möjlighet att filtrera för specifika händelsetyper
* `ExcludeAnalysisEvents`: returnerar inte ”analys”-händelser. Som standard returneras EventStore frågor med ”analys” händelser där det är möjligt. Analys av händelser är rikare användningskanal händelser som innehåller ytterligare kontext eller uppgifter efter en vanlig Service Fabric-händelse och ger mer på djupet.
* `SkipCorrelationLookup`: kan inte hitta potentiella korrelerade händelser i klustret. Som standard försöker EventStore att samordna händelser över ett kluster och länka dina händelser tillsammans när det är möjligt. 

Varje entitet i ett kluster kan vara frågor för händelser. Du kan också fråga efter händelser för alla entiteter av typen. Du kan till exempel fråga efter händelser för en viss nod eller för alla noder i klustret. Den aktuella uppsättningen entiteter som du kan fråga efter händelser är (med hur frågan skulle struktureras):
* Kluster: `/EventsStore/Cluster/Events`
* Noder: `/EventsStore/Nodes/Events`
* Nod: `/EventsStore/Nodes/<NodeName>/$/Events`
* Program: `/EventsStore/Applications/Events`
* Program: `/EventsStore/Applications/<AppName>/$/Events`
* Tjänster: `/EventsStore/Services/Events`
* Tjänsten: `/EventsStore/Services/<ServiceName>/$/Events`
* Partitioner: `/EventsStore/Partitions/Events`
* Partition: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Replicas: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Repliken: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>När du refererar till ett program eller tjänstnamn, frågan inte behöver inkludera de ”fabric: /” prefix. Även om ditt program eller tjänstnamn som har en ”/” i dem kan växla den till en ”~” att fortsätta att arbeta för frågan. Om ditt program visas som till exempel ”fabric: / App1/FrontendApp”, dina specifika frågor app skulle vara strukturerade som `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Dessutom hälsorapporter för tjänster idag visas under motsvarande program, så att du vill fråga efter `DeployedServiceHealthReportCreated` händelser för entiteten rätt program. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Fråga EventStore via REST API-slutpunkter

Du kan fråga EventStore direkt via en REST-slutpunkt genom att göra `GET` begäranden till: `<your cluster address>/EventsStore/<entity>/Events/`.

Till exempel för att fråga efter alla klusterhändelser mellan `2018-04-03T18:00:00Z` och `2018-04-04T18:00:00Z`, din begäran skulle se ut:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Detta kan antingen returnera inga händelser eller listan över händelser som returneras i json:

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

Här ser vi att mellan `2018-04-03T18:00:00Z` och `2018-04-04T18:00:00Z`, det här klustret har slutförts första uppgraderingen när den har först allt som krävs, från `"CurrentClusterVersion": "0.0.0.0:"` till `"TargetClusterVersion": "6.2:1.0"`i `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Fråga efter EventStore programmässigt

Du kan också fråga EventStore programmässigt, via den [Service Fabric-klientbiblioteket](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

När du har Service Fabric klienten ställa in kan fråga du efter händelser genom att öppna EventStore så här: ` sfhttpClient.EventStore.<request>`

Här är en exempelbegäran för att alla händelser mellan `2018-04-03T18:00:00Z` och `2018-04-04T18:00:00Z`, via den `GetClusterEventListAsync` funktion.

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

## <a name="sample-scenarios-and-queries"></a>Exempelscenarier och frågor

Här följer några exempel på hur du kan anropa händelse Store REST API: er om du vill förstå statusen för klustret.

*Klusteruppgradering:*

Om du vill se den senaste gången klustret har eller försökt uppgraderas förra veckan, kan du fråga API: er för nyligen utförda uppgraderingar till ditt kluster genom att fråga efter ”ClusterUpgradeCompleted”-händelser i EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Klustret uppgraderingsproblem:*

På samma sätt, om det finns problem med en uppgradering av klustret, du kan fråga efter alla händelser för kluster-entiteten. Du ser olika händelser, inklusive uppgraderingar och varje UD som uppgraderingen samlas via har. Du kan även se händelser för platsen där återställningen igång och motsvarande health-händelser. Här är den fråga som du använder för detta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Noden status ändras:*

Om du vill se din nodstatusen ändras under senaste Använd dagarna – när noder gick upp eller ned, eller har aktiveras eller inaktiveras (baserat på plattform, chaos-tjänsten, eller från indata från användaren) - följande fråga: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Programhändelser:*

Du kan också spåra dina senaste programdistributioner och uppgraderingar. Använd följande fråga om du vill visa programhändelser för alla i klustret: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historiska hälsotillståndet för ett program:*

Förutom enbart Livscykelhändelser för programmet, kan du även vill se historiska data om hälsan för ett visst program. Du kan göra detta genom att ange namnet på programmet som du vill samla in data. Använd den här frågan för att hämta alla hälsohändelser för programmet: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Om du vill inkludera health-händelser som kan ha gått ut (gått skickas tiden till live (TTL)), Lägg till `,ApplicationHealthReportExpired` i slutet av frågan, för att filtrera på två typer av händelser.

*Historiska hälsotillstånd för alla tjänster i ”myApp”:*

För närvarande hälsohändelser i rapporten för tjänster som visas som `DeployedServicePackageNewHealthReport` händelser under motsvarande program entitet. Använd följande fråga om du vill se hur dina tjänster har gör för ”App1”: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Omkonfiguration av partitionen:*

Alla partition-förflyttningar som har inträffat i klustret och fråga efter den `PartitionReconfigured` händelse. Detta kan hjälpa dig att ta reda på vilka arbetsbelastningar som kördes på vilken nod vid specifika tidpunkter, när diagnostisera problem i klustret. Här är en exempelfråga som utför som: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Chaos-tjänsten:*

Det finns en händelse när Chaos tjänsten startas eller stoppas som visas på klusternivå. Om du vill se dina senaste användning av tjänsten Chaos, använder du följande fråga: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

