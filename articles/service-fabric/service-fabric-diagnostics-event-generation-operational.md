---
title: Evenemangslista för Azure Service Fabric
description: Omfattande lista över händelser som tillhandahålls av Azure Service Fabric för att övervaka kluster.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258543"
---
# <a name="list-of-service-fabric-events"></a>Lista över evenemang för service fabric 

Service Fabric exponerar en primär uppsättning klusterhändelser för att informera dig om status för klustret som [Service Fabric Events](service-fabric-diagnostics-events.md). Dessa baseras på åtgärder som utförs av Service Fabric på dina noder och dina kluster- eller hanteringsbeslut som fattas av en klusterägare/-operatör. Dessa händelser kan nås genom att konfigurera på flera olika sätt, till exempel konfigurera [Azure Monitor-loggar med klustret](service-fabric-diagnostics-oms-setup.md)eller fråga [EventStore](service-fabric-diagnostics-eventstore.md). På Windows-datorer matas dessa händelser in i EventLog – så att du kan se Service Fabric-händelser i Loggboken. 

Här är några egenskaper hos dessa händelser
* Varje händelse är knuten till en viss entitet i klustret, t.ex.
* Varje händelse innehåller en uppsättning vanliga fält: EventInstanceId, EventName och Kategori.
* Varje händelse innehåller fält som binder händelsen tillbaka till den entitet den är associerad med. Till exempel skulle applicationcreated händelsen har fält som identifierar namnet på det program som skapats.
* Händelser är strukturerade på ett sådant sätt att de kan konsumeras i en mängd olika verktyg för att göra ytterligare analys. Dessutom definieras relevant information för en händelse som separata egenskaper i motsats till en lång sträng. 
* Händelser skrivs av olika delsystem i Service Fabric identifieras av Source(Task) nedan. Mer information finns på dessa delsystem i [Service Fabric Architecture](service-fabric-architecture.md) och Service Fabric Technical [Overview](service-fabric-technical-overview.md).

Här är en lista över dessa Service Fabric-händelser ordnade efter entitet.

## <a name="cluster-events"></a>Klusterhändelser

**Klusteruppgraderingshändelser**

Mer information om klusteruppgraderingar finns [här](service-fabric-cluster-upgrade-windows-server.md).

| EventId (på)EventId ) | Namn | Kategori | Beskrivning |Källa (uppgift) | Nivå | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Uppgradera | En klusteruppgradering har startat | CM | Information |
| 29628 | ClusterUpgradeCompleted | Uppgradera | En klusteruppgradering har slutförts | CM | Information | 
| 29629 | ClusterUpgradeRollbackStarted | Uppgradera | En klusteruppgradering har börjat återställas  | CM | Varning | 
| 29630 | ClusterUpgradeRollbackKomsten | Uppgradera | En klusteruppgradering har slutförts | CM | Varning | 
| 29631 | ClusterUpgradeDomainCompleted | Uppgradera | En uppgraderingsdomän har uppgraderats under en klusteruppgradering | CM | Information | 

## <a name="node-events"></a>Nodhändelser

**Livscykelhändelser för nod** 

| EventId (på)EventId ) | Namn | Kategori | Beskrivning |Källa (uppgift) | Nivå |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | Tillståndstransition | Inaktiveringen av en nod har slutförts | FM | Information | 
| 18603 | NodUp | Tillståndstransition | Klustret har upptäckt att en nod har startats | FM | Information | 
| 18604 | NodeDown | Tillståndstransition | Klustret har upptäckt att en nod har stängts av. Under en omstart av noden visas en NodeDown-händelse följt av en NodeUp-händelse |  FM | Fel | 
| 18605 | NodeAddedToCluster | Tillståndstransition |  En ny nod har lagts till i klustret och Service Fabric kan distribuera program till den här noden | FM | Information | 
| 18606 | NodTa bort frånCluster | Tillståndstransition |  En nod har tagits bort från klustret. Service Fabric distribuerar inte längre program till den här noden | FM | Information | 
| 18607 | NodeDeactivateStarted | Tillståndstransition |  Inaktivering av en nod har startat | FM | Information | 
| 25621 | NodeOpenSucceeded | Tillståndstransition |  En nod har startats | FabricNod | Information | 
| 25622 | NodeOpenFailed | Tillståndstransition |  Det gick inte att starta en nod och ansluta till ringen | FabricNod | Fel | 
| 25624 | NodSluten | Tillståndstransition |  En nod har stängts av | FabricNod | Information | 
| 25626 | NodeAborted | Tillståndstransition |  En nod har stängts av | FabricNod | Fel | 

## <a name="application-events"></a>Programhändelser

**Livscykelhändelser för program**

| EventId (på)EventId ) | Namn | Kategori | Beskrivning |Källa (uppgift) | Nivå | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Livscykel | Ett nytt program har skapats | CM | Information | 
| 29625 | ApplicationDeleted | Livscykel | Ett befintligt program har tagits bort | CM | Information | 
| 23083 | ApplicationProcessExited | Livscykel | En process i ett program har avslutats | Värd | Information | 

**Programuppgraderingshändelser**

Mer information om programuppgraderingar finns [här](service-fabric-application-upgrade.md).

| EventId (på)EventId ) | Namn | Kategori | Beskrivning |Källa (uppgift) | Nivå | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Uppgradera | En programuppgradering har startat | CM | Information | 
| 29622 | ApplicationUpgradeCompleted | Uppgradera | En programuppgradering har slutförts | CM | Information | 
| 29623 | ApplicationUpgradeRollbackStarted | Uppgradera | En programuppgradering har börjat återställas |CM | Varning | 
| 29624 | ApplicationUpgradeRollbackCompleted | Uppgradera | En programuppgradering har slutförts | CM | Varning | 
| 29626 | ApplicationUpgradeDomainCompleted | Uppgradera | En uppgraderingsdomän har uppgraderats under en programuppgradering | CM | Information | 

## <a name="service-events"></a>Servicehändelser

**Livscykelhändelser för tjänsten**

| EventId (på)EventId ) | Namn | Kategori | Beskrivning |Källa (uppgift) | Nivå | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceSkapad | Livscykel | En ny tjänst skapades | FM | Information | 
| 18658 | ServiceDelerad | Livscykel | En befintlig tjänst har tagits bort | FM | Information | 

## <a name="partition-events"></a>Partitionshändelser

**Partition flytta händelser**

| EventId (på)EventId ) | Namn | Kategori | Beskrivning |Källa (uppgift) | Nivå | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionRekonfigurerad | Livscykel | En omkonfiguration av partitionen har slutförts | RA | Information | 

## <a name="replica-events"></a>Replikhändelser

**Händelser för repliklivscykeln**

| EventId (på)EventId ) | Namn | Kategori | Beskrivning |Källa (uppgift) | Nivå |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryÖppnad | Livscykel | Tillförlitlig ordlista har öppnats | DistributedDictionary | Information |
| 61702 | ReliableDictionaryClosed | Livscykel | Tillförlitlig ordlista har stängts | DistributedDictionary | Information |
| 61703 | ReliableDictionaryCheckpointRecovered | Livscykel | Tillförlitlig ordbok har återfått sin kontrollpunkt | DistributedDictionary | Information |
| 61704 | ReliableDictionaryCheckpointFilesSent | Livscykel | Repliken har skickat tillförlitliga ordlistas kontrollpunktsfiler | DistributedDictionary | Information |
| 61705 | ReliableDictionaryCheckpointFilesKom igen | Livscykel | Repliken har fått tillförlitliga ordlistas kontrollpunktsfiler | DistributedDictionary | Information |
| 61963 | PålitligqueueÖppnad | Livscykel | Tillförlitlig kö har öppnats | DistributedQueue | Information |
| 61964 | ReliableQueueClosed | Livscykel | Tillförlitlig kö har stängts | DistributedQueue | Information |
| 61965 | ReliableQueueCheckpointRecovered | Livscykel | Tillförlitlig kö har återställt sin kontrollpunkt | DistributedQueue | Information |
| 61966 | ReliableQueueCheckpointFilesSent | Livscykel | Repliken har skickat tillförlitliga kökontrollpunkter | DistributedQueue | Information |
| 63647 | ReliableQueueCheckpointFilesKom igen | Livscykel | Repliken har tagit emot tillförlitliga kökontrollpunkter | DistributedQueue | Information |
| 63648 | ReliableConcurrentQueueÖppnad | Livscykel | Tillförlitlig samtidig kö har öppnats | ReliableConcurrentQueue | Information |
| 63649 | ReliableConcurrentQueueClosed | Livscykel | Tillförlitlig samtidig kö har stängts | ReliableConcurrentQueue | Information |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Livscykel | Tillförlitlig samtidig kö har återställt sin kontrollpunkt | ReliableConcurrentQueue | Information |
| 61687 | TStoreError (TStoreError) | Fel | Tillförlitlig samling har fått ett oväntat fel | TStore (på ett sätt) | Fel |
| 63831 | PrimaryFullCopyInitiated | Livscykel | Primär replik har initierat en fullständig kopia | TReplicator (TReplicator) | Information |
| 63832 | PrimaryPartialCopyInitiated PrimärPartialCopyInitiated PrimärPartialCopyInitiated PrimärParti | Livscykel | Primär replik har initierat en partiell kopia | TReplicator (TReplicator) | Information |
| 16831 | BuildIdleReplicaStarted | Livscykel | Primär replik har börjat bygga inaktiv replik | Replikering | Information |
| 16832 | BuildIdleReplicaCompleted | Livscykel | Den primära repliken har slutfört byggrepliken | Replikering | Information |
| 16833 | BuildIdleReplicaFailed | Livscykel | Primär replik har misslyckats med att bygga inaktiv replik | Replikering | Varning |
| 16834 | PrimaryReplicationQueueFull | Hälsa | Replikeringskön för primär replikering är full | Replikering | Varning |
| 16835 | PrimaryReplicationQueueVarning | Hälsa | Replikeringskön för primär replikering är nära full | Replikering | Varning |
| 16836 | PrimaryReplicationQueueWarningMitigated | Hälsa | Replikeringskö för primär repliker är okej | Replikering | Information |
| 16837 | SecondaryReplicationQueueFull | Hälsa | Replikeringskön för sekundär replikering är full | Replikering | Varning |
| 16838 | SekundärReplicationQueueVarning | Hälsa | Replikeringskön för sekundär replikering är nära full | Replikering | Varning |
| 16839 | SecondaryReplicationQueueWarningMitigated | Hälsa | Replikeringskö för sekundär repliker är okej | Replikering | Information |
| 16840 | PrimärFaultedSlowSecondary | Hälsa | Primär replik har fel en långsam sekundär replik | Replikering | Varning |
| 16841 | ReplikatorFaulted | Hälsa | Repliken har fel | Replikering | Varning |

## <a name="container-events"></a>Containerhändelser

**Livscykelhändelser för behållare** 

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | Behållareaktiverad | En behållare har startat | Värd | Information | 1 |
| 23075 | ContainerDeactivated | En behållare har stoppats | Värd | Information | 1 |
| 23082 | BehållareExiterad | En behållare har avslutats - Kontrollera flaggan Oväntad bestämning | Värd | Information | 1 |

## <a name="health-reports"></a>Hälsorapporter

[Service Fabric Health Model](service-fabric-health-introduction.md) ger en rik, flexibel och utökningsbar hälsoutvärdering och rapportering. Med start av Service Fabric version 6.2 skrivs hälsodata som plattformshändelser för att tillhandahålla historiska hälsouppgifter. För att hålla volymen av hälsohändelser låga skriver vi bara följande som Service Fabric-händelser:

* Alla `Error` `Warning` eller hälsorapporter
* `Ok`hälsorapporter under övergångar
* När `Error` en `Warning` eller hälsohändelse upphör att gälla. Detta kan användas för att avgöra hur länge en entitet var felaktig

**Klusterhälsorapporthändelser**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | En ny klusterhälsorapport finns tillgänglig | Hm | Information | 1 |
| 54437 | ClusterHealthReportExpirerad | En befintlig klusterhälsorapport har upphört att gälla | Hm | Information | 1 |

**Händelser i nodrapport**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | En ny nodhälsorapport är tillgänglig | Hm | Information | 1 |
| 54432 | NodeHealthReportExpirerad | En befintlig nodhälsorapport har upphört att gälla | Hm | Information | 1 |

**Hälsorapporthändelser för program**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ProgramNewHealthReport | En ny programhälsorapport har skapats. Detta gäller odeployed applikationer. | Hm | Information | 1 |
| 54426 | DistribueradApplicationNewHealthReport | En ny distribuerad programhälsorapport har skapats | Hm | Information | 1 |
| 54427 | DistribueradServicePackageNewHealthReport | En ny distribuerad hälsorapport för tjänsten har skapats | Hm | Information | 1 |
| 54434 | ApplicationHealthReportExpirerad | En befintlig programhälsorapport har upphört att gälla | Hm | Information | 1 |
| 54435 | DistribuerasApplicationHealthReportExpirerad | En befintlig hälsorapport för distribuerade program har upphört att gälla | Hm | Information | 1 |
| 54436 | DistribueradServicePackageHealthReportExpirerad | En befintlig tjänsthälsorapport har upphört att gälla | Hm | Information | 1 |

**Hälsorapporthändelser för tjänster**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | En ny hälsorapport för tjänsten har skapats | Hm | Information | 1 |
| 54433 | ServiceHealthReportExpirerad | En befintlig hälsorapport för tjänsten har upphört att gälla | Hm | Information | 1 |

**Händelser i hälsorapport för partitionering**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | En ny hälsorapport för partitionen har skapats | Hm | Information | 1 |
| 54431 | PartitionHealthReportExpirerad | En befintlig hälsorapport för partitionen har upphört att gälla | Hm | Information | 1 |

**Händelser i replikhälsorapport**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | En tillståndskänslig replikhälsorapport har skapats | Hm | Information | 1 |
| 54430 | StatslösInstanceNewHealthReport | En ny tillståndslös instanshälsarapport har skapats | Hm | Information | 1 |
| 54438 | StatefulReplicaHealthReportExpirerad | En befintlig tillståndskänslig replikhälsorapport har upphört att gälla | Hm | Information | 1 |
| 54439 | StatelessInstanceHealthReportExpired | En befintlig tillståndslös instanshälsorapport har upphört att gälla | Hm | Information | 1 |

## <a name="chaos-testing-events"></a>Kaos testhändelser 

**Kaos session händelser**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | KaosStartat | En Chaos-testsession har startat | Testbarhet | Information | 1 |
| 50023 | KaosStoppad | En Chaos-testsession har stoppats | Testbarhet | Information | 1 |

**Händelser för kaosnod**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartPlanerad | En nod har schemalagt att startas om som en del av en Chaos-testsession | Testbarhet | Information | 1 |
| 50087 | ChaosNodeRestartCompleted | En nod har startats om som en del av en Chaos-testsession | Testbarhet | Information | 1 |

**Kaos programhändelser**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartPlanerad | En omstart av kodpaketet har schemalagts under en Chaos-testsession | Testbarhet | Information | 1 |
| 50101 | ChaosCodePackageRestartCompleted | En omstart av kodpaketet har slutförts under en Chaos-testsession | Testbarhet | Information | 1 |

**Kaospartitionhändelser**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | En primär partition har schemalagt att flyttas som en del av en Chaos-testsession | Testbarhet | Information | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | En sekundär partition har schemalagt att flyttas som en del av en Chaos-testsession | Testbarhet | Information | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Den djupare analysen av den primära partitionsflytten är tillgänglig | Testbarhet | Information | 1 |

**Kaosreplikhändelser**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartPlanerad | En omstart av repliken har schemalagts som en del av en Chaos-testsession | Testbarhet | Information | 1 |
| 50051 | ChaosReplicaRemovalPlanerad | En borttagning av repliker har schemalagts som en del av en Chaos-testsession | Testbarhet | Information | 1 |
| 50093 | ChaosReplicaRemovalCompleted | En borttagning av repliker har slutförts som en del av en Chaos-testsession | Testbarhet | Information | 1 |

## <a name="other-events"></a>Andra evenemang

**Korrelationshändelser**

| EventId (på)EventId ) | Namn | Beskrivning |Källa (uppgift) | Nivå | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | KorrelationOperational | En korrelation har upptäckts | Testbarhet | Information | 1 |

## <a name="events-prior-to-version-62"></a>Händelser före version 6.2

Här är en omfattande lista över händelser som tillhandahålls av Service Fabric före version 6.2.

| EventId (på)EventId ) | Namn | Källa (uppgift) | Nivå |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNod | Information |
| 25621 | NodeOpenedSuccess | FabricNod | Information |
| 25622 | NodeOpenedFailed | FabricNod | Information |
| 25623 | NodClosing | FabricNod | Information |
| 25624 | NodSluten | FabricNod | Information |
| 25625 | NodeAborting (NodeAborting) | FabricNod | Information |
| 25626 | NodeAborted | FabricNod | Information |
| 29627 | ClusterUpgradeStart | CM | Information |
| 29628 | ClusterUpgradeComplete | CM | Information |
| 29629 | ClusterUpgradeRollback | CM | Information |
| 29630 | ClusterUpgradeRollbackComplete | CM | Information |
| 29631 | ClusterUpgradeDomainKomplett | CM | Information |
| 23074 | Behållareaktiverad | Värd | Information |
| 23075 | ContainerDeactivated | Värd | Information |
| 29620 | ApplicationCreated | CM | Information |
| 29621 | ApplicationUpgradeStart | CM | Information |
| 29622 | ApplicationUpgradeComplete | CM | Information |
| 29623 | ApplicationUpgradeRollback | CM | Information |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Information |
| 29625 | ApplicationDeleted | CM | Information |
| 29626 | ApplicationUpgradeDomainComplete | CM | Information |
| 18566 | ServiceSkapad | FM | Information |
| 18567 | ServiceDelerad | FM | Information |

## <a name="next-steps"></a>Nästa steg

* Få en översikt över [diagnostik i Service Fabric](service-fabric-diagnostics-overview.md)
* Läs mer om EventStore i [Service Fabric Eventstore Översikt](service-fabric-diagnostics-eventstore.md)
* Ändra konfigurationen för [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) för att samla in fler loggar
* [Konfigurera programinsikter](service-fabric-diagnostics-event-analysis-appinsights.md) för att se dina operativa kanalloggar
