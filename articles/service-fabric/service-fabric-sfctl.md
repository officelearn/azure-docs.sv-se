---
title: Azure Service Fabric CLI- sfctl
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista över kommandon och undergrupper.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906217"
---
# <a name="sfctl"></a>sfctl
Kommandon för hantering av kluster och entiteter för tjänstinfrastruktur. Den här versionen är kompatibel med Service Fabric 7.0-körningen.

Kommandona följer substantiv-verbmönstret. Mer information finns i undergrupperna.

## <a name="subgroups"></a>Undergrupper
|Undergrupp|Beskrivning|
| --- | --- |
| [program](service-fabric-sfctl-application.md) | Skapa, ta bort och hantera program och programtyper. |
| [Kaos](service-fabric-sfctl-chaos.md) | Starta, stoppa och rapportera om kaostesttjänsten. |
| [Kluster](service-fabric-sfctl-cluster.md) | Välj, hantera och använd Service Fabric-kluster. |
| [Komponera](service-fabric-sfctl-compose.md) | Skapa, ta bort och hantera Docker Compose-program. |
| [Behållare](service-fabric-sfctl-container.md) | Kör behållarrelaterade kommandon på en klusternod. |
| [Händelser](service-fabric-sfctl-events.md) | Hämta händelser från händelsearkivet (om EventStore-tjänsten redan är installerad). |
| [Är](service-fabric-sfctl-is.md) | Fråga och skicka kommandon till infrastrukturtjänsten. |
| [mesh](service-fabric-sfctl-mesh.md) | Ta bort och hantera Service Fabric Mesh-program. |
| [Nod](service-fabric-sfctl-node.md) | Hantera noderna som bildar ett kluster. |
| [Partition](service-fabric-sfctl-partition.md) | Fråga och hantera partitioner för alla tjänster. |
| [Egenskapen](service-fabric-sfctl-property.md) | Lagra och frågeegenskaper under Service Fabric-namn. |
| [Replika](service-fabric-sfctl-replica.md) | Hantera replikerna som tillhör tjänstpartitioner. |
| [Rpm](service-fabric-sfctl-rpm.md) | Fråga och skicka kommandon till reparationshanteraren. |
| [sa-kluster](service-fabric-sfctl-sa-cluster.md) | Hantera fristående Service Fabric-kluster. |
| [tjänst](service-fabric-sfctl-service.md) | Skapa, ta bort och hantera tjänst, tjänsttyper och servicepaket. |
| [inställningar](service-fabric-sfctl-settings.md) | Konfigurera inställningar lokalt för den här instansen av sfctl. |
| [store](service-fabric-sfctl-store.md) | Utför grundläggande filnivååtgärder i klusteravbildningsarkivet. |

## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).