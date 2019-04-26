---
title: Azure Service Fabric CLI - sfctl | Microsoft Docs
description: Beskriver de sfctl Service Fabric CLI-kommandona.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: c195d0c4250022102e735cf584370278e354bc41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545052"
---
# <a name="sfctl"></a>sfctl
Kommandon för att hantera Service Fabric-kluster och entiteter. Den här versionen är kompatibel med Service Fabric 6.4 runtime.

Kommandon följer mönstret substantiv-verb. Se undergrupper för mer information.

## <a name="subgroups"></a>Undergrupper
|Undergrupp|Beskrivning|
| --- | --- |
| [Programmet](service-fabric-sfctl-application.md) | Skapa, ta bort och hantera program och program. |
| [Chaos](service-fabric-sfctl-chaos.md) | Starta, stoppa och rapportera om chaos-testtjänsten. |
| [cluster](service-fabric-sfctl-cluster.md) | Välj, hantera och driva Service Fabric-kluster. |
| [Compose](service-fabric-sfctl-compose.md) | Skapa, ta bort och hantera Docker Compose-program. |
| [container](service-fabric-sfctl-container.md) | Kör behållaren relaterade kommandon på en klusternod. |
| [är](service-fabric-sfctl-is.md) | Fråga efter och skicka kommandon till tjänsten infrastruktur. |
| [mesh](service-fabric-sfctl-mesh.md) | Ta bort och hantera program i Service Fabric-nät. |
| [node](service-fabric-sfctl-node.md) | Hantera de noder som formar ett kluster. |
| [partition](service-fabric-sfctl-partition.md) | Fråga och hantera partitioner för alla tjänster. |
| [Egenskapen](service-fabric-sfctl-property.md) | Egenskaper för Store och fråga under Service Fabric-namn. |
| [replik](service-fabric-sfctl-replica.md) | Hantera replikerna som hör till tjänstpartitioner. |
| [rpm](service-fabric-sfctl-rpm.md) | Fråga efter och skicka kommandon till reparera manager-tjänsten. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Hantera fristående Service Fabric-kluster. |
| [Tjänsten](service-fabric-sfctl-service.md) | Skapa, ta bort och hantera tjänsten, tjänsttyper och paket. |
| [Inställningar](service-fabric-sfctl-settings.md) | Konfigurera inställningar som är lokala för den här instansen av sfctl. |
| [store](service-fabric-sfctl-store.md) | Utföra grundläggande nivå filåtgärder på avbildningsarkivet kluster. |

## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).