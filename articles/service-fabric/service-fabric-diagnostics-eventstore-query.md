---
title: Frågan för klusterhändelser med EventStore APIs i Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig mer om Azure Service Fabric EventStore APIs frågan för plattformen händelser
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: af97385981c61f32c4136921d3cf14a526fc6ddb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Frågan EventStore APIs för klusterhändelser

Den här artikeln beskriver hur man frågar EventStore-APIs som är tillgängliga i Service Fabric version 6.2 och senare – om du vill lära dig mer om tjänsten EventStore finns i [EventStore översikt över](service-fabric-diagnostics-eventstore.md). För närvarande EventStore tjänsten kan endast komma åt data för de senaste 7 dagarna (Detta baseras på ditt kluster diagnostik databevarandeprincip).

>[!NOTE]
>Från och med Service Fabric version 6.2. EventStore APIs finns för närvarande i förhandsvisning för Windows-kluster som kör bara på Azure. Vi arbetar på att överföra den här funktionen till Linux, samt våra fristående kluster.

EventStore APIs kan nås direkt via en REST-slutpunkt eller programmässigt. Beroende på frågan och finns det flera parametrar som krävs för att samla in rätt data. Dessa omfattar vanligtvis:
* `api-version`: versionen av EventStore-APIs du använder
* `StartTimeUtc`: Anger början av den period som du är intressanta tittar
* `EndTimeUtc`: slutet av tidsperioden

Förutom dessa finns det valfria parametrar, exempelvis:
* `timeout`: åsidosätta standardvärdet för 60 sekunder timeout för att utföra begäran igen
* `eventstypesfilter`: Detta ger dig möjlighet att filtrera för specifika händelsetyper
* `ExcludeAnalysisEvents`: inte returnerar 'Analysis-händelser. Som standard EventStore frågor returnerar med ”analys” händelser där det är möjligt - analys händelser är bättre operativa kanalen händelser som innehåller ytterligare kontext eller information utanför en vanlig Service Fabric-händelsen och ger mer djup.
* `SkipCorrelationLookup`: Sök inte efter potentiella korrelerade händelser i klustret. Som standard försöker EventStore att sammanföra händelserna i ett kluster och länka händelserna tillsammans när det är möjligt. 

Varje entitet i ett kluster kan vara frågor för händelser. Du kan också fråga efter händelser för alla entiteter av typen. Du kan till exempel fråga efter händelser för en viss nod eller för alla noder i klustret. Den aktuella uppsättningen av enheter som du kan fråga efter händelser är (med hur frågan skulle strukturerad):
* Klustret: `/EventsStore/Cluster/Events`
* Noder: `/EventsStore/Nodes/Events`
* Nod: `/EventsStore/Nodes/<NodeName>/$/Events`
* Program: `/EventsStore/Applications/Events`
* Program: `/EventsStore/Applications/<AppName>/$/Events`
* Tjänster: `/EventsStore/Services/Events`
* Tjänsten: `/EventsStore/Services/<ServiceName>/$/Events`
* Partitioner: `/EventsStore/Partitions/Events`
* Partitionen: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliker: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Repliken är: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>När du refererar till ett program eller tjänstnamn frågan behöver inte inkludera den ”fabric: /” prefix. Dessutom om programmet eller tjänstnamn har en ”/” i dem, växlar den till en ”~” att hålla en fråga som arbetar. Om ditt program visas som till exempel ”fabric: / App1/FrontendApp”, skulle vara strukturerad din app specifika frågor som `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Dessutom hälsorapporter för tjänster idag visas under motsvarande program så att du vill fråga efter `DeployedServiceHealthReportCreated` händelser för entiteten rätt program. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Frågan EventStore via REST API-slutpunkter

Du kan fråga EventStore direkt via en REST-slutpunkt genom att göra `GET` begäranden till: `<your cluster address>/EventsStore/<entity>/Events/`.

Till exempel för att fråga efter alla klusterhändelser mellan `2018-04-03T18:00:00Z` och `2018-04-04T18:00:00Z`, din begäran skulle se ut:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.2-preview&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Detta kan antingen ett fel returneras om inga händelser är tillgängliga, eller om frågan lyckades visas händelser som returneras i json:

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

Här ser vi att mellan `2018-04-03T18:00:00Z` och `2018-04-04T18:00:00Z`, detta kluster har slutförts första uppgraderas när det först har legat, från `"CurrentClusterVersion": "0.0.0.0:"` till `"TargetClusterVersion": "6.2:1.0"`i `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Fråga EventStore programmässigt

Du kan också fråga efter EventStore programmässigt via den [Service Fabric-klientbiblioteket](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

När du har din Service Fabric-klienten ställer in kan fråga du efter händelser genom att öppna EventStore så här: ` sfhttpClient.EventStore.<request>`

Här är en exempelbegäran för alla händelser mellan `2018-04-03T18:00:00Z` och `2018-04-04T18:00:00Z`, via den `GetClusterEventListAsync` funktion.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

## <a name="sample-scenarios-and-queries"></a>Exempelscenarier och frågor

Här följer några exempel på hur du kan anropa händelse Store REST API: er för att förstå statusen för klustret.

1. Uppgraderingar:

Om du vill se senast klustret har eller försökt uppgraderas föregående vecka, kan du fråga efter API: er för nyligen utförda uppgraderingar till ditt kluster genom att fråga efter ”ClusterUpgradeComplete”-händelser i EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeComplete`

1. Klustret uppgraderingsproblem:

På samma sätt kan kan det fanns problem med en uppgradering av klustret, du fråga efter alla händelser för entiteten klustret. Olika händelser, inklusive uppgraderingar och varje UD som uppgraderingen samlas via har visas. Du kan även se händelser för platsen där återställningen igång och motsvarande hälsa händelser. Här är den fråga du vill använda för detta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Noden status ändras:

Om du vill se nod status har ändrats under senaste Använd dagarna - när noder gick uppåt eller nedåt, eller har aktiveras eller inaktiveras (av plattformen chaos-tjänsten, eller från användarindata) - följande fråga: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Programhändelser:

Du kan också spåra din senaste application-distributioner och uppgraderingar. Använd följande fråga om du vill se alla relaterade händelser i klustret: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Historiska hälsotillståndet för ett program:

Förutom enbart programhändelser livscykel, kan du också se historiska data om hälsotillståndet för ett visst program. Du kan göra detta genom att ange namnet på programmet som du vill samla in data. Använd den här frågan för att hämta alla händelser för program-hälsa: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.2-preview&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ProcessApplicationReport`. Om du vill inkludera hälsa händelser som kan ha upphört att gälla (rest skickades tiden live (TTL)), Lägg till `,ExpiredDeployedApplicationEvent` till slutet av frågan, för att filtrera på två typer av händelser.

1. Historiska hälsotillstånd för alla tjänster i ”myApp”:

För närvarande hälsa rapport för tjänster visas händelser som `DeployedServiceHealthReportCreated` händelser under motsvarande program entitet. Använd följande fråga om du vill se hur dina tjänster har gjort för ”App1”: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServiceHealthReportCreated`

1. Omkonfiguration av partitionen:

Visa alla partition-förflyttningar som har inträffat i klustret, fråga efter den `ReconfigurationCompleted` händelse. Detta kan hjälpa dig att ta reda på vilka arbetsbelastningar som kördes på vilken nod vid specifika tidpunkter, när diagnostisera problem i klustret. Här är en exempelfråga som gör detta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.2-preview&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigurationCompleted`

1. Chaos-tjänst:

Det finns en händelse när Chaos tjänsten startas eller stoppas som är exponerad på klusternivå. Din senaste användning av tjänsten Chaos, Använd följande fråga: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

