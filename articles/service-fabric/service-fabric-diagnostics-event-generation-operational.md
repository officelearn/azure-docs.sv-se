---
title: Azure Service Fabric-händelselistan | Microsoft Docs
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
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212967"
---
# <a name="list-of-service-fabric-events"></a>Lista över händelser som Service Fabric 

Service Fabric Exponerar en primär uppsättning klusterhändelser att meddela dig om status för klustret som [Service Fabric händelser](service-fabric-diagnostics-events.md). Dessa är baserade på åtgärder som utförs av Service Fabric på noderna och klustret eller beslut om livscykelhantering av klustret ägare/operator. Dessa händelser kan nås genom att fråga den [EventStore](service-fabric-diagnostics-eventstore.md) i klustret eller via operativa kanalen. På Windows-datorer, är operativa kanalen också kopplat till EventLog - så att du kan se Service Fabric-händelser i Loggboken. 

>[!NOTE]
>En lista över Service Fabric-händelser för kluster i versioner < 6.2, finns i följande avsnitt. 

Här är en lista över alla händelser i den plattform sorterad efter den enhet som de mappas till.

## <a name="cluster-events"></a>Klusterhändelser

**Uppgradera klusterhändelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | Information | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | Information | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | Information | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | Information | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | Information | 1 |

**Klustret hälsa rapporten händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | Information | 1 |
| 54437 | ExpiredClusterEventOperational | HM | Information | 1 |

**Chaos tjänsten-händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Möjlighet att testa | Information | 1 |
| 50023 | ChaosStoppedEvent | Möjlighet att testa | Information | 1 |

## <a name="node-events"></a>Nod-händelser

**Noden Livscykelhändelser** 

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | Information | 1 |
| 18603 | NodeUpOperational | FM | Information | 1 |
| 18604 | NodeDownOperational | FM | Information | 1 |
| 18605 | NodeAddedOperational | FM | Information | 1 |
| 18606 | NodeRemovedOperational | FM | Information | 1 |
| 18607 | DeactivateNodeStartOperational | FM | Information | 1 |
| 25620 | NodeOpening | FabricNode | Information | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | Information | 1 |
| 25622 | NodeOpenedFailed | FabricNode | Information | 1 |
| 25623 | NodeClosing | FabricNode | Information | 1 |
| 25624 | NodeClosed | FabricNode | Information | 1 |
| 25625 | NodeAborting | FabricNode | Information | 1 |
| 25626 | NodeAborted | FabricNode | Information | 1 |

**Noden hälsa rapporten händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | Information | 1 |
| 54432 | ExpiredNodeEventOperational | HM | Information | 1 |

**Chaos nod händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Möjlighet att testa | Information | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Möjlighet att testa | Information | 1 |

## <a name="application-events"></a>Programhändelser

**Livscykel för programhändelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | Information | 1 |
| 29625 | ApplicationDeletedOperational | CM | Information | 1 |
| 23083 | ProcessExitedOperational | Värd för | Information | 1 |

**Uppgradera programhändelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | Information | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | Information | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | Information | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | Information | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | Information | 1 |

**Programhändelser hälsa rapport**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | Information | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | Information | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | Information | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | Information | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | Information | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | Information | 1 |

**Chaos programhändelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Möjlighet att testa | Information | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Möjlighet att testa | Information | 1 |

## <a name="service-events"></a>Tjänsten-händelser

**Livscykelhändelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | Information | 1 |
| 18658 | ServiceDeletedOperational | FM | Information | 1 |

**Tjänstens hälsa rapporten händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | Information | 1 |
| 54433 | ExpiredServiceEventOperational | HM | Information | 1 |

## <a name="partition-events"></a>Partitionen händelser

**Partitionen flytta händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | Information | 1 |

**Partitionen hälsa rapporten händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | Information | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | Information | 1 |

**Chaos partition händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Möjlighet att testa | Information | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Möjlighet att testa | Information | 1 |

**Händelser för analys av partition**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Möjlighet att testa | Information | 1 |

## <a name="replica-events"></a>Replik-händelser

**Replik hälsa rapporten händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | Information | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | Information | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | Information | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | Information | 1 |

**Chaos replik händelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Möjlighet att testa | Information | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Möjlighet att testa | Information | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Möjlighet att testa | Information | 1 |

## <a name="container-events"></a>Behållaren händelser

**Behållaren Livscykelhändelser** 

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Värd för | Information | 1 |
| 23075 | ContainerDeactivatedOperational | Värd för | Information | 1 |
| 23082 | ContainerExitedOperational | Värd för | Information | 1 |

## <a name="other-events"></a>Andra händelser

**Korrelationshändelser**

| EventId | Namn | Källa (aktivitet) | Nivå | Version |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Möjlighet att testa | Information | 1 |

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
| 23074 | ContainerActivated | Värd för | Information |
| 23075 | ContainerDeactivated | Värd för | Information |
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

* Mer information om övergripande [händelse genereras på nivån plattform](service-fabric-diagnostics-event-generation-infra.md) i Service Fabric
* Ändra din [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfiguration för att samla in mer loggar
* [Konfigurera Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) att se din Operational kanaler loggar
