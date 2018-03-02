---
title: Azure Service Fabric CLI - sfctl | Microsoft Docs
description: Beskriver de sfctl Service Fabric CLI-kommandona.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 7c8563539ca8507f05fa99fdeffbf511b1540a6a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl"></a>sfctl 
Kommandon för att hantera Service Fabric-kluster och enheter. Den här versionen är kompatibel med Service Fabric 6.1 runtime. Kommandon följer mönstret substantiv-verb, se följande undergrupper för mer information.

## <a name="subgroups"></a>Undergrupper
|Undergrupp|Beskrivning|
| --- | --- |
| [Programmet](service-fabric-sfctl-application.md)| Skapa, ta bort och hantera program och programtyper.|
| [Chaos](service-fabric-sfctl-chaos.md)   | Starta, stoppa och rapportera om tjänsten chaos test.|
| [cluster](service-fabric-sfctl-cluster.md) | Välj, hantera och driva Service Fabric-kluster.|
| [compose](service-fabric-sfctl-compose.md) | Skapa, ta bort och hantera Docker Compose program.|
| [Är](service-fabric-sfctl-is.md)      | Fråga efter och skicka kommandon till tjänsten infrastruktur.|
| [node](service-fabric-sfctl-node.md)    | Hantera de noder som formar ett kluster.|
| [partition](service-fabric-sfctl-partition.md)  | Fråga efter och hantera partitioner för alla tjänster.|
| egenskap  | Lagra och fråga egenskaper under Service Fabric-namn.|
| [rpm](service-fabric-sfctl-rpm.md)        | Fråga efter och skicka kommandon till reparera manager-tjänsten.|
| [Replik](service-fabric-sfctl-replica.md) | Hantera replikerna som tillhör tjänsten partitioner.|
| [Tjänsten](service-fabric-sfctl-service.md) | Skapa, ta bort och hantera service, tjänsttyp och servicepaket.|
| [lagra](service-fabric-sfctl-store.md)   | Utföra grundläggande nivån filåtgärder på klustret image store.|

## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).