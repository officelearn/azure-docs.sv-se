---
title: Introduktion till tillförlitliga samlingar i Azure Service Fabric tillståndskänsliga tjänster | Microsoft Docs
description: Service Fabric tillståndskänsliga tjänster ger tillförlitliga samlingar som hjälper dig att skriva mycket tillgängliga, skalbara och låg latens molnprogram.
services: service-fabric
documentationcenter: .net
author: tylermsft
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/6/2017
ms.author: twhitney
ms.openlocfilehash: caca297afb9ed4e2d85f1068ad3c1122db60c1d7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191996"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduktion till tillförlitliga samlingar i Azure Service Fabric tillståndskänsliga tjänster
Tillförlitliga samlingar kan du skriva mycket tillgängliga, skalbara och låg latens molnprogram som om du skriver datorprogram. Klasserna i den **Microsoft.ServiceFabric.Data.Collections** namnområde tillhandahåller en uppsättning samlingar som automatiskt gör din delstat med hög tillgänglighet. Utvecklare måste program som endast ska tillförlitlig samling API: er och låt Reliable Collections hantera replikerade och lokala tillstånd.

Den viktigaste skillnaden mellan tillförlitliga samlingar och andra tekniker för hög tillgänglighet (till exempel Redis, Azure Table service och Azure-kötjänsten) är att tillståndet sparas lokalt i tjänstinstansen vid också som görs med hög tillgänglighet. Detta innebär att:

* Alla läsningar är local, vilket innebär att låg fördröjning och högt dataflöde läser.
* Alla skrivåtgärder debiteras det minsta antal IOs-nätverket, vilket resulterar i låg fördröjning och högt dataflöde skriver.

![Bild av utvecklingen av samlingar.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Tillförlitliga samlingar kan betraktas som en naturlig utveckling av den **System.Collections** klasser: en ny uppsättning samlingar som är utformade för molnprogram och flera datorer program utan att öka komplexiteten för den utvecklare. Därför är tillförlitliga samlingar:

* Replikerade: Tillståndsändringar replikeras för hög tillgänglighet.
* Beständiga: Data sparas till disk för hållbarhet mot storskaliga avbrott (till exempel ett datacenter strömavbrott).
* Asynkron: API: er är asynkron så att trådar inte är blockerad när medför IO.
* Transaktionell: API: er använder abstraktionen av transaktioner så att du enkelt kan hantera flera tillförlitliga samlingar inom en tjänst.

Tillförlitliga samlingar ger stark konsekvens garanterar att underlätta resonemang om programtillståndet direkt.
Stark konsekvens uppnås genom att kontrollera att transaktionen incheckningar Slutför förrän hela transaktionen har loggats in ett majoritetskvorum av repliker, inklusive primärt.
Om du vill uppnå svagare konsekvens bekräftar program tillbaka till klienten/beställaren innan asynkron incheckning returnerar.

API: er för tillförlitliga samlingar är en utveckling av samtidiga samlingar API: er (finns i den **System.Collections.Concurrent** namnområde):

* Asynkron: Returnerar en uppgift eftersom, till skillnad från samtidiga samlingar åtgärderna replikeras och sparas.
* Nej out-parametrar. Använder `ConditionalValue<T>` att returnera bool och ett värde i stället för out-parametrar. `ConditionalValue<T>` liknar `Nullable<T>` men kräver inte T ska vara en Struct-datatypen.
* Transaktioner: Använder en transaktion för att låta användarna gruppåtgärder på flera tillförlitliga samlingar i en transaktion.

Idag, **Microsoft.ServiceFabric.Data.Collections** innehåller tre samlingarna:

* [Tillförlitlig ordlista](https://msdn.microsoft.com/library/azure/dn971511.aspx): Representerar en replikerade transaktioner och asynkrona samling nyckel/värde-par. Liknar **ConcurrentDictionary**, både nyckeln och värdet kan vara av valfri typ.
* [Tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx): Representerar en replikerade transaktioner och asynkrona strikt först in, skickas kö. Liknar **ConcurrentQueue**, värdet kan vara av valfri typ.
* [Tillförlitlig samtidiga kö](service-fabric-reliable-services-reliable-concurrent-queue.md): Representerar en replikerade transaktioner och asynkrona bästa prestanda ordning kön för högt dataflöde. Liknar den **ConcurrentQueue**, värdet kan vara av valfri typ.

## <a name="next-steps"></a>Nästa steg
* [Riktlinjer och rekommendationer Reliable Collections](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Hantera Data
  * [Säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md)
  * [Meddelanden](service-fabric-reliable-services-notifications.md)
  * [Reliable Collection-serialisering](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Konfiguration av Reliable State Manager](service-fabric-reliable-services-configuration.md)
* Andra
  * [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
