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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: cb48cff488c4f0bcd3c4a742f5d9922ec59a36b6
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269863"
---
# <a name="sfctl"></a>sfctl
Kommandon för att hantera Service Fabric-kluster och entiteter. Den här versionen är kompatibel med Service Fabric 6.4 runtime.

Kommandon följer mönstret substantiv-verb. Se undergrupper för mer information.

## <a name="subgroups"></a>Undergrupper
|Undergrupp|Beskrivning|
| --- | --- |
| [Programmet](service-fabric-sfctl-application.md) | Skapa, ta bort och hantera program och program. |
| [Chaos](service-fabric-sfctl-chaos.md) | Starta, stoppa och rapportera om chaos-testtjänsten. |
| [Kluster](service-fabric-sfctl-cluster.md) | Välj, hantera och driva Service Fabric-kluster. |
| [Compose](service-fabric-sfctl-compose.md) | Skapa, ta bort och hantera Docker Compose-program. |
| [Behållare](service-fabric-sfctl-container.md) | Kör behållaren relaterade kommandon på en klusternod. |
| [är](service-fabric-sfctl-is.md) | Fråga efter och skicka kommandon till tjänsten infrastruktur. |
| [mesh](service-fabric-sfctl-mesh.md) | Ta bort och hantera program i Service Fabric-nät. |
| [Noden](service-fabric-sfctl-node.md) | Hantera de noder som formar ett kluster. |
| [partition](service-fabric-sfctl-partition.md) | Fråga och hantera partitioner för alla tjänster. |
| [Egenskapen](service-fabric-sfctl-property.md) | Egenskaper för Store och fråga under Service Fabric-namn. |
| [replik](service-fabric-sfctl-replica.md) | Hantera replikerna som hör till tjänstpartitioner. |
| [rpm](service-fabric-sfctl-rpm.md) | Fråga efter och skicka kommandon till reparera manager-tjänsten. |
| [SA-kluster](service-fabric-sfctl-sa-cluster.md) | Hantera fristående Service Fabric-kluster. |
| [Tjänsten](service-fabric-sfctl-service.md) | Skapa, ta bort och hantera tjänsten, tjänsttyper och paket. |
| [Inställningar](service-fabric-sfctl-settings.md) | Konfigurera inställningar som är lokala för den här instansen av sfctl. |
| [Store](service-fabric-sfctl-store.md) | Utföra grundläggande nivå filåtgärder på avbildningsarkivet kluster. |

## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).