---
title: Tillgängligheten för Service Fabric-tjänster | Microsoft Docs
description: Beskriver felinsamling, redundans och återställning av tjänster
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: dd10af0d3c8a57168a27a039286ea0ec4c1dad02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310952"
---
# <a name="availability-of-service-fabric-services"></a>Tillgängligheten för Service Fabric-tjänster
Den här artikeln ger en översikt över hur Azure Service Fabric bibehåller tillgängligheten för en tjänst.

## <a name="availability-of-service-fabric-stateless-services"></a>Tillgängligheten för tillståndslös Service Fabric-tjänster
Service Fabric-tjänster kan vara tillståndslösa eller tillståndskänsliga. En tillståndslös tjänst är en programtjänst som inte har en [lokal state](service-fabric-concepts-state.md) som måste vara mycket tillgänglig eller tillförlitlig.

Skapa en tillståndslös tjänst kräver att du definierar en `InstanceCount`. Instansantalet definierar antalet instanser av den tillståndslösa tjänsten programlogik som ska köras i klustret. Öka antalet instanser är det rekommenderade sättet att skala ut en tillståndslös tjänst.

När en instans av en tillståndslös med namnet-tjänsten misslyckas, skapas en ny instans på en berättigad nod i klustret. En tillståndslös tjänst-instans kan misslyckas på Nod1 och skapas på nytt på nod5.

## <a name="availability-of-service-fabric-stateful-services"></a>Tillgänglighet för tillståndskänsliga tjänster för Service Fabric
En tillståndskänslig tjänst har ett tillstånd som associeras med den. I Service Fabric modelleras en tillståndskänslig tjänst av repliker. Varje replik är en instans av koden för tjänsten som körs. Repliken har också en kopia av tillståndet för tjänsten. Läsa och skriva åtgärder utförs i en replik, kallas de *primära*. Ändringar av tillståndet på skriva åtgärder är *replikerade* till andra repliker i uppsättningen, kallas *Active sekundärservrar*, och tillämpas. 

Det kan finnas endast en primär replik, men det kan finnas flera aktiva sekundära repliker. Antal aktiva sekundära repliker konfigureras och ett högre antal repliker kan tolerera fel på ett större antal samtidiga programvara och maskinvarufel.

Om den primära repliken stängs av, Service Fabric gör en av de aktiva sekundära replikerna den nya primärt repliken. Den här aktiva sekundära repliken har redan den uppdaterade versionen av tillstånd, via *replikering*, och den kan fortsätta att bearbeta ytterligare läs-/ skrivåtgärder. Den här processen kallas *omkonfiguration* och beskrivs ytterligare i den [omkonfiguration](service-fabric-concepts-reconfiguration.md) artikeln.

Konceptet med en replik som antingen en primär eller aktiva sekundära kallas den *replikroll*. De här replikeringarna beskrivs ytterligare i den [repliker och instanser](service-fabric-concepts-replica-lifecycle.md) artikeln. 

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

- [Skala Service Fabric-tjänster](service-fabric-concepts-scalability.md)
- [Partitionera Service Fabric-tjänster](service-fabric-concepts-partitioning.md)
- [Ange och hantera tillstånd](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

