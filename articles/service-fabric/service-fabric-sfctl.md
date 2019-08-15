---
title: Azure Service Fabric CLI – sfctl | Microsoft Docs
description: Beskriver Service Fabric CLI-sfctl kommandon.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 35b881268ca21a840836c96388a4562a54d17d3b
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035818"
---
# <a name="sfctl"></a>sfctl
Kommandon för att hantera Service Fabric kluster och entiteter. Den här versionen är kompatibel med Service Fabric 6,4-körningsmiljön.

Kommandon följer mönstret Substantiv-verb. Se under grupper för mer information.

## <a name="subgroups"></a>Under grupper
|Under grupp|Beskrivning|
| --- | --- |
| [applicering](service-fabric-sfctl-application.md) | Skapa, ta bort och hantera program och program typer. |
| [kaos](service-fabric-sfctl-chaos.md) | Starta, stoppa och rapportera om kaos test service. |
| [cluster](service-fabric-sfctl-cluster.md) | Välj, hantera och använda Service Fabric kluster. |
| [utgör](service-fabric-sfctl-compose.md) | Skapa, ta bort och hantera Docker Compose-program. |
| [fönster](service-fabric-sfctl-container.md) | Kör behållar relaterade kommandon på en klusternod. |
| [utgör](service-fabric-sfctl-is.md) | Fråga och skicka kommandon till infrastruktur tjänsten. |
| [mesh](service-fabric-sfctl-mesh.md) | Ta bort och hantera Service Fabric nät-program. |
| [nodfel](service-fabric-sfctl-node.md) | Hantera noderna som utgör ett kluster. |
| [partitionstabellen](service-fabric-sfctl-partition.md) | Fråga och hantera partitioner för alla tjänster. |
| [immaterialrätt](service-fabric-sfctl-property.md) | Lagra och fråga efter egenskaper under Service Fabric namn. |
| [Replica](service-fabric-sfctl-replica.md) | Hantera de repliker som hör till-tjänstepartitioner. |
| [rpm](service-fabric-sfctl-rpm.md) | Fråga och skicka kommandon till tjänsten Repair Manager. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Hantera fristående Service Fabric kluster. |
| [telefonitjänstprovider](service-fabric-sfctl-service.md) | Skapa, ta bort och hantera tjänst, tjänst typer och service paket. |
| [autentiseringsinställningar](service-fabric-sfctl-settings.md) | Konfigurera lokala inställningar för den här instansen av sfctl. |
| [auktoriseringsarkiv](service-fabric-sfctl-store.md) | Utför grundläggande åtgärder på filnivå på klustrets avbildnings lager. |

## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).