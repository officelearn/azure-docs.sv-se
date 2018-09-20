---
title: Azure Service Fabric-händelselista | Microsoft Docs
description: Omfattande lista över händelser som tillhandahålls av Azure Service Fabric för att övervaka kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 3e12acb670d1f23c8ccfc6f4ea1b6e8aff5abee7
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365595"
---
# <a name="list-of-service-fabric-events"></a>Lista över Service Fabric-händelser 

Service Fabric visar en primär uppsättning klusterhändelser om status för klustret som [Service Fabric-händelser](service-fabric-diagnostics-events.md). De bygger på åtgärder som utförs av Service Fabric på noderna och klustret eller beslut om gjorda av en kluster-ägaren /-operator. Dessa händelser kan nås genom att fråga den [EventStore](service-fabric-diagnostics-eventstore.md) i klustret eller via den operativa kanalen. På Windows-datorer måste är den operativa kanalen också kopplat till EventLog - så att du kan se Service Fabric-händelser i Loggboken. 

>[!NOTE]
>En lista över Service Fabric händelser för kluster i versioner < 6.2, finns i följande avsnitt. 

Här är en lista över alla händelser i plattformen, sorterade efter den entitet som de mappas till.

## <a name="cluster-events"></a>Klusterhändelser

**Uppgradera klusterhändelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | En uppgradering av klustret har startats | CM | Information | 1 |
| 29628 | ClusterUpgradeCompleted | En uppgradering av klustret har slutförts| CM | Information | 1 |
| 29629 | ClusterUpgradeRollbackStarted | En uppgradering av klustret har startats för att återställa | CM | Information | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | En uppgradering av klustret har slutförts återtagning | CM | Information | 1 |
| 29631 | ClusterUpgradeDomainCompleted | Uppgradering av en har slutförts under en uppgradering av klustret | CM | Information | 1 |

## <a name="node-events"></a>Noden händelser

**Livscykelhändelser för noden** 

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | Inaktivering av en nod har slutförts | FM | Information | 1 |
| 18603 | NodeUp | Klustret har identifierat en nod har startats | FM | Information | 1 |
| 18604 | NodeDown | Klustret har identifierat en nod har avslutats |  FM | Information | 1 |
| 18605 | NodeAddedToCluster | En ny nod har lagts till i klustret | FM | Information | 1 |
| 18606 | NodeRemovedFromCluster | En nod har tagits bort från klustret | FM | Information | 1 |
| 18607 | NodeDeactivateStarted | Inaktivering av en nod har startats | FM | Information | 1 |
| 25620 | NodeOpening | En nod startas. Första steget i livscykeln för noden | FabricNode | Information | 1 |
| 25621 | NodeOpenSucceeded | En nod har startats | FabricNode | Information | 1 |
| 25622 | NodeOpenFailed | Det gick inte att starta en nod | FabricNode | Information | 1 |
| 25623 | NodeClosing | En nod stängs av. Början av det sista steget i livscykeln för noden | FabricNode | Information | 1 |
| 25624 | NodeClosed | En nod som har avslutats | FabricNode | Information | 1 |
| 25625 | NodeAborting | En nod startas ungracefully stänga | FabricNode | Information | 1 |
| 25626 | NodeAborted | En nod har ungracefully avslutats | FabricNode | Information | 1 |

## <a name="application-events"></a>Programhändelser

**Livscykelhändelser för program**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | Ett nytt program har skapats | CM | Information | 1 |
| 29625 | ApplicationDeleted | Ett befintligt program har tagits bort | CM | Information | 1 |
| 23083 | ApplicationProcessExited | En process i ett program har avslutats | Som är värd för | Information | 1 |

**Uppgradera programhändelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | En uppgradering av programmet har startats | CM | Information | 1 |
| 29622 | ApplicationUpgradeCompleted | En uppgradering av programmet har slutförts | CM | Information | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | En uppgradering av programmet har startats för att återställa |CM | Information | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | En uppgradering av programmet har slutförts återtagning | CM | Information | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | En domän-uppgraderingen har slutförts under en uppgradering av programmet | CM | Information | 1 |

## <a name="service-events"></a>Tjänsten-händelser

**Livscykelhändelser för tjänsten**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 18602 | ServiceCreated | En ny tjänst skapades | FM | Information | 1 |
| 18658 | ServiceDeleted | En befintlig tjänst har tagits bort | FM | Information | 1 |

## <a name="partition-events"></a>Partition händelser

**Partition flytta händelser**

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | En omkonfiguration av partitionen har slutförts | RA | Information | 1 |

## <a name="container-events"></a>Behållarhändelser

**Livscykelhändelser för behållare** 

| EventId | Namn | Beskrivning |Källa (aktivitet) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | En behållare har startats | Som är värd för | Information | 1 |
| 23075 | ContainerDeactivated | En behållare har stoppats | Som är värd för | Information | 1 |
| 23082 | ContainerExited | En behållare har avslutats - Kontrollera flaggan UnexpectedTermination | Som är värd för | Information | 1 |

## <a name="health-reports"></a>Hälsorapporter

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
| 65011 | CorrelationOperational | En korrelation har detacted | Möjligheten att testa | Information | 1 |

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
| 23074 | ContainerActivated | Som är värd för | Information |
| 23075 | ContainerDeactivated | Som är värd för | Information |
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

* Mer information om övergripande [händelsegenerering på nivån plattform](service-fabric-diagnostics-event-generation-infra.md) i Service Fabric
* Ändra din [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfiguration för att samla in mer loggar
* [Konfigurera Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) att se din drift kanal-loggar
