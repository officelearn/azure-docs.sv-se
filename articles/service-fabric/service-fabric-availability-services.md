---
title: Tillgång till Service Fabric-tjänster
description: Beskriver felidentifiering, redundans och återställning av en tjänst i ett Azure Service Fabric-program.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551870"
---
# <a name="availability-of-service-fabric-services"></a>Tillgång till Service Fabric-tjänster
Den här artikeln innehåller en översikt över hur Azure Service Fabric upprätthåller tillgängligheten för en tjänst.

## <a name="availability-of-service-fabric-stateless-services"></a>Tillgång till Service Fabric statslösa tjänster
Service Fabric-tjänster kan vara antingen tillståndskänsliga eller tillståndslösa. En tillståndslös tjänst är en programtjänst som inte har ett [lokalt tillstånd](service-fabric-concepts-state.md) som måste vara mycket tillgängligt eller tillförlitligt.

För att skapa en tillståndslös tjänst måste du definiera en `InstanceCount`. Instansantalet definierar antalet instanser av den tillståndslösa tjänstens programlogik som ska köras i klustret. Att öka antalet instanser är det rekommenderade sättet att skala ut en tillståndslös tjänst.

När en instans av en tillståndslös namngivningstjänst misslyckas skapas en ny instans på en berättigad nod i klustret. En tillståndslös tjänstinstans kan till exempel misslyckas på nod1 och återskapas på Node5.

## <a name="availability-of-service-fabric-stateful-services"></a>Tillgång till service fabric tillståndskänsliga tjänster
En tillståndskänslig tjänst har ett tillstånd som är associerat med den. I Service Fabric modelleras en tillståndskänslig tjänst som en uppsättning repliker. Varje replik är en instans av tjänstens kod. Repliken har också en kopia av tillståndet för den tjänsten. Läs- och skrivåtgärder utförs på en replik, som kallas *Primär*. Ändringar av tillstånd från skrivåtgärder *replikeras* till de andra replikerna i replikuppsättningen, så kallade *Aktiva sekundärer,* och tillämpas. 

Det kan bara finnas en primär replik, men det kan finnas flera aktiva sekundära repliker. Antalet aktiva sekundära repliker kan konfigureras och ett större antal repliker kan tolerera ett större antal samtidiga programvaru- och maskinvarufel.

Om den primära repliken går ned gör Service Fabric en av de aktiva sekundära replikerna till den nya primära repliken. Den här aktiva sekundära repliken har redan den uppdaterade versionen av tillståndet, via *replikering*, och den kan fortsätta att bearbeta ytterligare läs-/skrivåtgärder. Den här processen kallas *omkonfigurering* och beskrivs ytterligare i artikeln [Omkonfiguration.](service-fabric-concepts-reconfiguration.md)

Konceptet med en replik som antingen är primär eller aktiv sekundär kallas *replikrollen*. Dessa repliker beskrivs ytterligare i artikeln [Repliker och instanser.](service-fabric-concepts-replica-lifecycle.md) 

## <a name="next-steps"></a>Nästa steg
Mer information om service fabric-begrepp finns i följande artiklar:

- [Skala service fabric-tjänster](service-fabric-concepts-scalability.md)
- [Tjänster för partitionering av tjänst fabric](service-fabric-concepts-partitioning.md)
- [Definiera och hantera tillstånd](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

