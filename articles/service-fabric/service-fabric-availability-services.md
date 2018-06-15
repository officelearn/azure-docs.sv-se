---
title: Tillgänglighet för Service Fabric-tjänster | Microsoft Docs
description: Beskriver felinsamling, redundans och återställning av tjänster
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0794c0e190ecbc4cce808e94f98bb0ac63d1075a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206257"
---
# <a name="availability-of-service-fabric-services"></a>Tillgänglighet för Service Fabric-tjänster
Den här artikeln ger en översikt över hur Azure Service Fabric bibehåller tillgängligheten för en tjänst.

## <a name="availability-of-service-fabric-stateless-services"></a>Tillgängligheten för Service Fabric tillståndslösa tjänster
Service Fabric-tjänster kan vara tillståndskänsliga och tillståndslösa. Tillståndslösa tjänsten är en programtjänst som inte har en [lokala tillstånd](service-fabric-concepts-state.md) som måste vara mycket tillgänglig eller tillförlitlig.

Skapa en tillståndslösa tjänsten måste du definiera en `InstanceCount`. Instansantal definierar antalet instanser av tjänsten tillståndslös programlogik som ska köras i klustret. Öka antalet instanser är det rekommenderade sättet att skala ut en tillståndslös tjänst.

När en instans av en tillståndslös med namnet-tjänsten misslyckas, skapas en ny instans på en berättigad nod i klustret. En instans av tillståndslösa tjänsten misslyckas på Nod1 och skapas på nytt på nod5.

## <a name="availability-of-service-fabric-stateful-services"></a>Tillgänglighet för tillståndskänsliga tjänster för Service Fabric
En tillståndskänslig tjänst har ett tillstånd som associeras med den. I Service Fabric modelleras en tillståndskänslig service som en uppsättning repliker. Varje replik är en instans av koden för tjänsten som körs. Repliken har även en kopia av tillståndet för tjänsten. Läsa och skriva åtgärder utförs i en replik som kallas den *primära*. Ändringar av tillståndet på skriva åtgärder är *replikeras* till andra repliker i replikuppsättningen, kallas *aktiva sekundära*, och tillämpas. 

Det kan finnas endast en primär replik, men det kan finnas flera aktiva sekundära repliker. Antal aktiva sekundära repliker konfigureras och ett högre antal repliker kan tolerera ett större antal samtidiga program- och maskinvarufel.

Om den primära repliken kraschar Service Fabric gör något av de aktiva sekundära replikerna den nya primärt replik. Den här aktiva sekundära repliken har redan den uppdaterade versionen av tillstånd, via *replikering*, och det kan fortsätta att bearbeta ytterligare åtgärder för läsning och skrivning. Den här processen kallas *omkonfiguration* och ytterligare beskrivs i den [omkonfiguration](service-fabric-concepts-reconfiguration.md) artikel.

Konceptet för en replik som en primär eller aktiva sekundära kallas den *replikroll*. De här replikeringarna beskrivs ytterligare i den [repliker och instanser](service-fabric-concepts-replica-lifecycle.md) artikel. 

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

- [Skala Service Fabric-tjänster](service-fabric-concepts-scalability.md)
- [Partitionering Service Fabric-tjänster](service-fabric-concepts-partitioning.md)
- [Definiera och hantera tillstånd](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

