---
title: Introduktion till tillförlitliga samlingar i Azure Service Fabric tillståndskänsliga tjänster | Microsoft Docs
description: Service Fabric tillståndskänsliga tjänster har tillförlitliga samlingar som gör det möjligt att skriva hög tillgänglighet, skalbara och låg latens molnprogram.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: 2876d90c02995394104009d1b2d62d5b3ed6a8d9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212933"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduktion till tillförlitliga samlingar i Azure Service Fabric tillståndskänsliga tjänster
Tillförlitliga samlingar kan du skriva hög tillgänglighet, skalbara och låg latens molnprogram som om du skriver datorprogram. Klasserna i den **Microsoft.ServiceFabric.Data.Collections** namnområde ger en uppsättning av samlingar som automatiskt ge ditt tillstånd hög tillgänglighet. Utvecklare måste programmet bara tillförlitliga samling API: er och låta tillförlitliga samlingar som hanterar replikeras och lokala tillstånd.

Den viktigaste skillnaden mellan tillförlitliga samlingar och andra tekniker för hög tillgänglighet (till exempel Redis Azure tabelltjänsten och Azure-kötjänsten) är att tillståndet sparas lokalt i tjänstinstansen när också görs högtillgänglig. Detta innebär att:

* Alla Läs är lokala, vilket leder till låg latens och hög genomströmning läser.
* Alla skrivningar innebära det minsta antalet IOs-nätverket, vilket resulterar i låg latens och hög genomströmning skriver.

![Bild av utvecklingen av samlingar.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Tillförlitliga samlingar kan betraktas som fysiska utvecklingen av den **System.Collections** klasser: en ny uppsättning samlingar som är utformade för moln och flera datorer-program utan att öka komplexiteten för utvecklare. Därför är tillförlitlig samlingar:

* Replikerade: Tillståndsändringar replikeras för hög tillgänglighet.
* Beständiga: Data sparas till disk för hållbarhet mot storskaliga avbrott (till exempel ett datacenter strömavbrott).
* Asynkron: API: er är asynkron så att trådar inte blockeras när medför IO.
* Transaktionell: API: er använda för transaktioner så att du enkelt kan hantera flera tillförlitliga samlingar i en tjänst.

Tillförlitliga samlingar ger stark konsekvens garanterar out of box att underlätta motivationen om programmets tillstånd.
Stark konsekvens uppnås genom att säkerställa transaktion incheckningar Slutför förrän hela transaktionen har loggats in ett kvorum för merparten av repliker, inklusive den primära servern.
För att uppnå svagare konsekvenskontroll bekräftar program tillbaka till klienten/beställaren innan asynkront genomförande returnerar.

API: er för tillförlitlig samlingar är en utveckling av samtidiga samlingar API: er (finns i den **System.Collections.Concurrent** namnområde):

* Asynkron: Returnerar en aktivitet eftersom, till skillnad från samlingar med samtidiga åtgärder replikeras och beständig.
* Inte out-parametrar: använder `ConditionalValue<T>` att returnera bool och ett värde i stället för out-parametrar. `ConditionalValue<T>` liknar `Nullable<T>` men kräver inte T vara en struktur.
* Transaktioner: Använder ett transaction-objekt för att hjälpa användarna att gruppera åtgärder på flera tillförlitliga samlingar i en transaktion.

Idag **Microsoft.ServiceFabric.Data.Collections** innehåller tre samlingar:

* [Tillförlitliga ordlista](https://msdn.microsoft.com/library/azure/dn971511.aspx): representerar en replikerad transaktionell och asynkrona mängd nyckel/värde-par. Liknar **ConcurrentDictionary**, både nyckeln och värdet kan vara av valfri typ.
* [Tillförlitliga kön](https://msdn.microsoft.com/library/azure/dn971527.aspx): representerar en replikerad transaktionell och asynkrona strikt först in, skickas kö. Liknar **ConcurrentQueue**, värdet kan vara av valfri typ.
* [Tillförlitliga samtidiga kön](service-fabric-reliable-services-reliable-concurrent-queue.md): representerar en replikerad transaktionell och asynkrona bästa prestanda ordning kön för högt genomflöde. Liknar den **ConcurrentQueue**, värdet kan vara av valfri typ.

## <a name="next-steps"></a>Nästa steg
* [Riktlinjer för tillförlitlig samling & rekommendationer](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Datahantering
  * [Säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md)
  * [Meddelanden](service-fabric-reliable-services-notifications.md)
  * [Reliable Collection-serialisering](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Konfigurationen för hanteraren för tillförlitlig tillstånd](service-fabric-reliable-services-configuration.md)
* Andra
  * [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [För utvecklare för tillförlitlig samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
