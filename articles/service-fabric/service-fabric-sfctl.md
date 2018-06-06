---
title: Azure Service Fabric CLI - sfctl | Microsoft Docs
description: Beskriver de sfctl Service Fabric CLI-kommandona.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 456257e54da83ac629039b714cc74f9dafda2174
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763280"
---
# <a name="sfctl"></a>Sfctl
Kommandon för att hantera Service Fabric-kluster och enheter. Den här versionen är kompatibel med Service Fabric 6.2 runtime.

Kommandon följer mönstret substantiv-verb. Mer information finns i undergrupper.

## <a name="subgroups"></a>Undergrupper
|Undergrupp|Beskrivning|
| --- | --- |
| [Programmet](service-fabric-sfctl-application.md) | Skapa, ta bort och hantera program och programtyper. |
| [Chaos](service-fabric-sfctl-chaos.md) | Starta, stoppa och rapportera om tjänsten chaos test. |
| [Klustret](service-fabric-sfctl-cluster.md) | Välj, hantera och driva Service Fabric-kluster. |
| [Skapa](service-fabric-sfctl-compose.md) | Skapa, ta bort och hantera Docker Compose program. |
| [Behållaren](service-fabric-sfctl-container.md) | Kör behållaren-relaterade kommandon på en klusternod. |
| [Är](service-fabric-sfctl-is.md) | Fråga efter och skicka kommandon till tjänsten infrastruktur. |
| [Noden](service-fabric-sfctl-node.md) | Hantera de noder som formar ett kluster. |
| [Partition](service-fabric-sfctl-partition.md) | Fråga efter och hantera partitioner för alla tjänster. |
| [Egenskapen](service-fabric-sfctl-property.md) | Lagra och fråga egenskaper under Service Fabric-namn. |
| [Replik](service-fabric-sfctl-replica.md) | Hantera replikerna som tillhör tjänsten partitioner. |
| [rpm](service-fabric-sfctl-rpm.md) | Fråga efter och skicka kommandon till reparera manager-tjänsten. |
| [SA-kluster](service-fabric-sfctl-sa-cluster.md) | Hantera fristående Service Fabric-kluster. |
| [Tjänsten](service-fabric-sfctl-service.md) | Skapa, ta bort och hantera service, tjänsttyp och servicepaket. |
| [lagra](service-fabric-sfctl-store.md) | Utföra grundläggande nivån filåtgärder på klustret image store. |

## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).