---
title: Azure Service Fabric operativa kanalen | Microsoft Docs
description: "Omfattande lista över loggar som genereras i operativa kanalen av Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: dekapur
ms.openlocfilehash: 934719868ab9968db352db2b440014d35dbc0274
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="operational-channel"></a>Operativa kanalen 

Den operativa kanalen består av loggar från övergripande åtgärder som utförs av Service Fabric på noderna och klustret. När ”-diagnostik är aktiverat för ett kluster, Azure-diagnostik-agent har distribuerats på klustret och är som standard konfigurerad för att läsa i loggar från den operativa kanalen. Läs mer om hur du konfigurerar den [Azure Diagnostics agent](service-fabric-diagnostics-event-aggregation-wad.md) att ändra konfigurationen för diagnostik av klustret för att hämta mer loggar eller prestandaräknare. 

## <a name="operational-channel-logs"></a>Operativa kanalen loggar 

Här är en omfattande lista över loggarna som tillhandahålls av Service Fabric i den operativa kanalen. 

| Händelse-ID | Namn | Källa (aktivitet) | Nivå |
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
