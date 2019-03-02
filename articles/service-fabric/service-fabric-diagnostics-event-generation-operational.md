---
title: Azure Service Fabric-händelselista | Microsoft Docs
description: Omfattande lista över händelser som tillhandahålls av Azure Service Fabric för att övervaka kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 6aec4e4bbf0abcfb6201da50381b6697a47716fa
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244595"
---
# <a name="list-of-service-fabric-events"></a>Lista över Service Fabric-händelser 

Service Fabric visar en primär uppsättning klusterhändelser om status för klustret som [Service Fabric-händelser](service-fabric-diagnostics-events.md). De bygger på åtgärder som utförs av Service Fabric på noderna och klustret eller beslut om gjorda av en kluster-ägaren /-operator. Dessa händelser kan nås genom att konfigurera i flera olika sätt, inklusive konfiguration av [Azure Monitor-loggar med ditt kluster](service-fabric-diagnostics-oms-setup.md), eller skicka en fråga i [EventStore](service-fabric-diagnostics-eventstore.md). På Windows-datorer måste matas dessa händelser in EventLog - så att du kan se Service Fabric-händelser i Loggboken. 

Här följer några egenskaper av dessa händelser
* Varje händelse är kopplad till en specifik enhet i klustret t.ex. program, tjänst, Node, repliken.
* Varje händelse som innehåller en uppsättning vanliga fält: EventInstanceId EventName och kategori.
* Varje händelse innehåller fält som knyter händelsen tillbaka till den enhet som den är associerad med. Händelsen ApplicationCreated skulle exempelvis ha fält som identifierar namnet på programmet som har skapat.
* Händelser är strukturerade så att de kan användas i en mängd olika verktyg för att göra vidare analyser. Dessutom definieras relevant information om en händelse som olika egenskaper, till skillnad från en lång sträng. 
* Händelser som är skrivna med olika delsystem i Service Fabric identifieras av Source(Task) nedan. Mer information finns på dessa undersystem i [arkitektur för Service Fabric](service-fabric-architecture.md) och [teknisk översikt över Service Fabric](service-fabric-technical-overview.md).

Här är en lista över dessa Service Fabric-händelser som är ordnade efter entitet.

## <a name="cluster-events"></a>Klusterhändelser

**Uppgradera klusterhändelser**

Mer information om klusteruppgradering finns [här](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Namn | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Uppgradera | En uppgradering av klustret har startats | CM | Information |
| 29628 | ClusterUpgradeCompleted | Uppgradera | En uppgradering av klustret har slutförts | CM | Information | 
| 29629 | ClusterUpgradeRollbackStarted | Uppgradera | En uppgradering av klustret har startats för att återställa  | CM | Varning | 
| 29630 | ClusterUpgradeRollbackCompleted | Uppgradera | En uppgradering av klustret har slutförts återtagning | CM | Varning | 
| 29631 | ClusterUpgradeDomainCompleted | Uppgradera | En uppgraderingsdomän har uppgraderats under en uppgradering av klustret | CM | Information | 

## <a name="node-events"></a>Noden händelser

**Livscykelhändelser för noden** 

| EventId | Namn | Kategori | Beskrivning |Källa (aktivitet) | Nivå |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Inaktivering av en nod har slutförts | FM | Information | 
| 18603 | NodeUp | StateTransition | Klustret har identifierat en nod har startats | FM | Information | 
| 18604 | NodeDown | StateTransition | Klustret har identifierat en nod har avslutats. Under noden startas om visas en NodeDown händelse följt av en NodeUp-händelse |  FM | Fel | 
| 18605 | NodeAddedToCluster | StateTransition |  En ny nod har lagts till i klustret och Service Fabric kan distribuera program till den här noden | FM | Information | 
| 18606 | NodeRemovedFromCluster | StateTransition |  En nod har tagits bort från klustret. Service Fabric kommer inte längre distribuera program till den här noden | FM | Information | 
| 18607 | NodeDeactivateStarted | StateTransition |  Inaktivering av en nod har startats | FM | Information | 
| 25621 | NodeOpenSucceeded | StateTransition |  En nod har startats | FabricNode | Information | 
| 25622 | NodeOpenFailed | StateTransition |  Det gick inte att starta och ansluta till ringen som en nod | FabricNode | Fel | 
| 25624 | NodeClosed | StateTransition |  En nod som har avslutats | FabricNode | Information | 
| 25626 | NodeAborted | StateTransition |  En nod har ungracefully avslutats | FabricNode | Fel | 

## <a name="application-events"></a>Programhändelser

**Livscykelhändelser för program**

| EventId | Namn | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | Ett nytt program har skapats | CM | Information | 
| 29625 | ApplicationDeleted | LifeCycle | Ett befintligt program har tagits bort | CM | Information | 
| 23083 | ApplicationProcessExited | LifeCycle | En process i ett program har avslutats | Värd | Information | 

**Uppgradera programhändelser**

Mer information om programuppgraderingar finns [här](service-fabric-application-upgrade.md).

| EventId | Namn | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Uppgradera | En uppgradering av programmet har startats | CM | Information | 
| 29622 | ApplicationUpgradeCompleted | Uppgradera | En uppgradering av programmet har slutförts | CM | Information | 
| 29623 | ApplicationUpgradeRollbackStarted | Uppgradera | En uppgradering av programmet har startats för att återställa |CM | Varning | 
| 29624 | ApplicationUpgradeRollbackCompleted | Uppgradera | En uppgradering av programmet har slutförts återtagning | CM | Varning | 
| 29626 | ApplicationUpgradeDomainCompleted | Uppgradera | En uppgraderingsdomän har uppgraderats under en uppgradering av programmet | CM | Information | 

## <a name="service-events"></a>Tjänsten-händelser

**Livscykelhändelser för tjänsten**

| EventId | Namn | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | En ny tjänst skapades | FM | Information | 
| 18658 | ServiceDeleted | LifeCycle | En befintlig tjänst har tagits bort | FM | Information | 

## <a name="partition-events"></a>Partition händelser

**Partition flytta händelser**

| EventId | Namn | Kategori | Beskrivning |Källa (aktivitet) | Nivå | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | En omkonfiguration av partitionen har slutförts | RA | Information | 

## <a name="container-events"></a>Behållarhändelser

**Livscykelhändelser för behållare** 

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | En behållare har startats | Värd | Information | 1 |
| 23075 | ContainerDeactivated | En behållare har stoppats | Värd | Information | 1 |
| 23082 | ContainerExited | En behållare har avslutats - Kontrollera flaggan UnexpectedTermination | Värd | Information | 1 |

## <a name="health-reports"></a>Hälsorapporter

Den [Hälsomodellen för Service Fabric](service-fabric-health-introduction.md) tillhandahåller en omfattande, flexibel och utökningsbar health-utvärdering och rapportering. Starta Service Fabric version 6.2 skrivs hälsodata som plattform aktiviteter för att tillhandahålla historiska poster av hälsotillstånd. Om du vill behålla mängden health-händelser med låg skriver vi bara följande som Service Fabric-händelser:

* Alla `Error` eller `Warning` hälsorapporter
* `Ok` hälsorapporter vid övergångar
* När en `Error` eller `Warning` hälsotillståndshändelse upphör att gälla. Detta kan användas för att avgöra hur lång tid en entitet har feltillstånd

**Kluster rapporten hälsohändelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Ett nytt kluster hälsorapport är tillgänglig | HM | Information | 1 |
| 54437 | ClusterHealthReportExpired | Ett befintligt kluster hälsorapport har upphört att gälla | HM | Information | 1 |

**Noden hälsohändelser i rapporten**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | En ny nod hälsorapport är tillgänglig | HM | Information | 1 |
| 54432 | NodeHealthReportExpired | En befintlig nod hälsorapport har upphört att gälla | HM | Information | 1 |

**Programhändelser hälsotillstånd rapport**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | En ny application health rapport skapades. Detta är för odistribuerade program. | HM | Information | 1 |
| 54426 | DeployedApplicationNewHealthReport | Ett nytt distribuerat program hälsorapport skapades | HM | Information | 1 |
| 54427 | DeployedServicePackageNewHealthReport | En ny hälsorapport distribuerade tjänsten har skapats | HM | Information | 1 |
| 54434 | ApplicationHealthReportExpired | En befintlig rapport i programmet hälsotillstånd har upphört att gälla | HM | Information | 1 |
| 54435 | DeployedApplicationHealthReportExpired | En befintlig rapport i distribuerade programmets hälsotillstånd har upphört att gälla | HM | Information | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | En befintlig distribuerad tjänst hälsorapport har upphört att gälla | HM | Information | 1 |

**Rapporten hälsotillståndshändelser för tjänsten**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | En ny service health-rapporten har skapats | HM | Information | 1 |
| 54433 | ServiceHealthReportExpired | En befintlig service health rapport har upphört att gälla | HM | Information | 1 |

**Rapporten partition health-händelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | En ny partition health-rapporten har skapats | HM | Information | 1 |
| 54431 | PartitionHealthReportExpired | En befintlig partition health rapport har upphört att gälla | HM | Information | 1 |

**Repliken hälsohändelser i rapporten**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | En hälsorapport för tillståndskänsliga repliken har skapats | HM | Information | 1 |
| 54430 | StatelessInstanceNewHealthReport | En ny tillståndslösa instans hälsorapport skapades | HM | Information | 1 |
| 54438 | StatefulReplicaHealthReportExpired | En befintlig tillståndskänsliga replica health rapport har upphört att gälla | HM | Information | 1 |
| 54439 | StatelessInstanceHealthReportExpired | En befintlig tillståndslös instans hälsorapport har upphört att gälla | HM | Information | 1 |

## <a name="chaos-testing-events"></a>Chaos testning händelser 

**Chaos Sessionshändelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | En Chaos testning sessionen har startats | Möjligheten att testa | Information | 1 |
| 50023 | ChaosStopped | En Chaos testning session har stoppats | Möjligheten att testa | Information | 1 |

**Chaos noden händelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | En nod har schemalagts för att starta om som en del av en Chaos testning session | Möjligheten att testa | Information | 1 |
| 50087 | ChaosNodeRestartCompleted | En nod har slutförts startar om som en del av en Chaos testning session | Möjligheten att testa | Information | 1 |

**Chaos programhändelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | En omstart av kod paketet har schemalagts under en Chaos testning session | Möjligheten att testa | Information | 1 |
| 50101 | ChaosCodePackageRestartCompleted | En omstart av kod paketet har slutförts under en Chaos testning session | Möjligheten att testa | Information | 1 |

**Chaos partition händelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | En primär partition har schemalagts för att flytta som en del av en Chaos testning session | Möjligheten att testa | Information | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | En sekundär partition har schemalagts för att flytta som en del av en Chaos testning session | Möjligheten att testa | Information | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Djupare analyser av den primära partitionen flytten är tillgänglig | Möjligheten att testa | Information | 1 |

**Chaos replica-händelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | En omstart av repliken har schemalagts som en del av en Chaos testning session | Möjligheten att testa | Information | 1 |
| 50051 | ChaosReplicaRemovalScheduled | En replik borttagning har schemalagts som en del av en Chaos testning session | Möjligheten att testa | Information | 1 |
| 50093 | ChaosReplicaRemovalCompleted | En replik borttagningen är klar som en del av en Chaos testning session | Möjligheten att testa | Information | 1 |

## <a name="other-events"></a>Andra händelser

**Korrelationshändelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | En korrelation har identifierats | Möjligheten att testa | Information | 1 |

## <a name="events-prior-to-version-62"></a>Händelser före version 6.2

Här är en omfattande lista över händelser som tillhandahålls av Service Fabric före version 6.2.

| EventId | Namn | Källa (aktivitet) | Nivå |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Information |
| 25621 | NodeOpenedSuccess | FabricNode | Information |
| 25622 | NodeOpenedFailed | FabricNode | Information |
| 25623 | NodeClosing | FabricNode | Information |
| 25624 | NodeClosed | FabricNode | Information |
| 25625 | NodeAborting | FabricNode | Information |
| 25626 | NodeAborted | FabricNode | Information |
| 29627 | ClusterUpgradeStart | CM | Information |
| 29628 | ClusterUpgradeComplete | CM | Information |
| 29629 | ClusterUpgradeRollback | CM | Information |
| 29630 | ClusterUpgradeRollbackComplete | CM | Information |
| 29631 | ClusterUpgradeDomainComplete | CM | Information |
| 23074 | ContainerActivated | Värd | Information |
| 23075 | ContainerDeactivated | Värd | Information |
| 29620 | ApplicationCreated | CM | Information |
| 29621 | ApplicationUpgradeStart | CM | Information |
| 29622 | ApplicationUpgradeComplete | CM | Information |
| 29623 | ApplicationUpgradeRollback | CM | Information |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Information |
| 29625 | ApplicationDeleted | CM | Information |
| 29626 | ApplicationUpgradeDomainComplete | CM | Information |
| 18566 | ServiceCreated | FM | Information |
| 18567 | ServiceDeleted | FM | Information |

## <a name="next-steps"></a>Nästa steg

* Få en översikt över [diagnostik i Service Fabric](service-fabric-diagnostics-overview.md)
* Mer information om EventStore i [översikt över Service Fabric-Eventstore](service-fabric-diagnostics-eventstore.md)
* Ändra din [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfiguration för att samla in mer loggar
* [Konfigurera Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) att se din drift kanal-loggar
