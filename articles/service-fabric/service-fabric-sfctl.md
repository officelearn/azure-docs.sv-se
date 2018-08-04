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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: b6e3c769307f037c9f31dbb3f1a86675145ec1f6
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495415"
---
# <a name="sfctl"></a>sfctl
Kommandon för att hantera Service Fabric-kluster och entiteter. Den här versionen är kompatibel med Service Fabric 6.3 runtime.

Kommandon följer mönstret substantiv-verb. Mer information finns i undergrupper.

## <a name="subgroups"></a>Undergrupper
|Undergrupp|Beskrivning|
| --- | --- |
| [Programmet](service-fabric-sfctl-application.md) | Skapa, ta bort och hantera program och program. |
| [Chaos](service-fabric-sfctl-chaos.md) | Starta, stoppa och rapportera om chaos-testtjänsten. |
| [Kluster](service-fabric-sfctl-cluster.md) | Välj, hantera och driva Service Fabric-kluster. |
| [Compose](service-fabric-sfctl-compose.md) | Skapa, ta bort och hantera Docker Compose-program. |
| [Behållare](service-fabric-sfctl-container.md) | Kör behållaren-relaterade kommandon på en klusternod. |
| [är](service-fabric-sfctl-is.md) | Fråga efter och skicka kommandon till tjänsten infrastruktur. |
| [Noden](service-fabric-sfctl-node.md) | Hantera de noder som formar ett kluster. |
| [partition](service-fabric-sfctl-partition.md) | Fråga och hantera partitioner för alla tjänster. |
| [Egenskapen](service-fabric-sfctl-property.md) | Egenskaper för Store och fråga under Service Fabric-namn. |
| [replik](service-fabric-sfctl-replica.md) | Hantera replikerna som hör till tjänstpartitioner. |
| [rpm](service-fabric-sfctl-rpm.md) | Fråga efter och skicka kommandon till reparera manager-tjänsten. |
| [SA-kluster](service-fabric-sfctl-sa-cluster.md) | Hantera fristående Service Fabric-kluster. |
| [Tjänsten](service-fabric-sfctl-service.md) | Skapa, ta bort och hantera tjänsten, tjänsttyper och paket. |
| [Store](service-fabric-sfctl-store.md) | Utföra grundläggande nivå filåtgärder på avbildningsarkivet kluster. |

## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).