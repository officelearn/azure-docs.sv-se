---
title: Introduktion till tillförlitliga samlingar
description: Service Fabric tillståndskänsliga tjänster ger tillförlitliga samlingar som gör att du kan skriva mycket tillgängliga, skalbara och låg latens molnprogram.
ms.topic: conceptual
ms.date: 1/3/2019
ms.openlocfilehash: 48fa682f4c017f66911729e1f581f3aa91cdc28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609731"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduktion till tillförlitliga samlingar i Azure Service Fabric tillståndskänsliga tjänster

Med tillförlitliga samlingar kan du skriva molnprogram med hög tillgänglig, skalbar och låg latens som om du skrev endatornsprogram. Klasserna i namnområdet **Microsoft.ServiceFabric.Data.Collections** innehåller en uppsättning samlingar som automatiskt gör ditt tillstånd mycket tillgängligt. Utvecklare behöver bara programmera till API:erna för tillförlitlig insamling och låta tillförlitliga samlingar hantera replikerade och lokala tillstånd.

Den viktigaste skillnaden mellan tillförlitliga samlingar och andra tekniker med hög tillgänglighet (till exempel Redis, Azure Table Service och Azure Queue service) är att tillståndet hålls lokalt i tjänstinstansen samtidigt som det görs mycket tillgängligt. Detta innebär att:

* Alla läsningar är lokala, vilket resulterar i låg latens och höggenomströmning läsningar.
* Alla skrivningar ådrar sig det minsta antalet nätverks-IOs, vilket resulterar i skrivningar med låg latens och högt dataflöde.

![Bild av utvecklingen av samlingar.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Tillförlitliga samlingar kan ses som den naturliga utvecklingen av **System.Collections** klasser: en ny uppsättning samlingar som är utformade för molnet och flera datorprogram utan att öka komplexiteten för utvecklaren. Som sådan, Tillförlitliga samlingar är:

* Replikerad: Tillståndsändringar replikeras för hög tillgänglighet.
* Beständig: Data sparas på disken för hållbarhet mot storskaliga avbrott (till exempel ett strömavbrott i datacenter).
* Eftersom skrivningar sparas och replikeras kan du inte skapa en flyktig ReliableDictionary, ReliableQueue eller annan tillförlitlig samling som bara finns kvar i minnet.
* Asynkrona: API:er är asynkrona för att säkerställa att trådar inte blockeras när de ådrar sig IO.
* Transaktionell: API:er använder abstraktion av transaktioner så att du enkelt kan hantera flera tillförlitliga samlingar inom en tjänst.

Tillförlitliga samlingar ger starka konsekvensgarantier direkt för att göra resonemanget om programtillstånd enklare.
Stark konsekvens uppnås genom att säkerställa att transaktionsförseringar slutförs först efter att hela transaktionen har loggats på en majoritet kvorum av repliker, inklusive den primära.
För att uppnå svagare konsekvens kan program bekräfta tillbaka till klienten/beställaren innan asynkrona commit-returerna.

Api:erna för tillförlitliga samlingar är en utveckling av samtidiga insamlings-API:er (finns i **namnområdet System.Collections.Concurrent):**

* Asynkron: Returnerar en aktivitet eftersom åtgärderna, till skillnad från samtidiga samlingar, replikeras och bevaras.
* No out parametrar: `ConditionalValue<T>` Används `bool` för att returnera ett och ett värde i stället för ut parametrar. `ConditionalValue<T>`är `Nullable<T>` som men kräver inte T vara en struct.
* Transaktioner: Använder ett transaktionsobjekt för att göra det möjligt för användaren att gruppera åtgärder på flera tillförlitliga samlingar i en transaktion.

Idag innehåller **Microsoft.ServiceFabric.Data.Collections** tre samlingar:

* [Tillförlitlig ordlista](https://msdn.microsoft.com/library/azure/dn971511.aspx): Representerar en replikerad, transaktionell och asynkron samling av nyckel-/värdepar. I likhet **med ConcurrentDictionary**kan både nyckeln och värdet vara av vilken typ som helst.
* [Tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx): Representerar en replikerad, transaktionell och asynkron strikt FIFO-kö (first-in, first-out). I likhet **med ConcurrentQueue**kan värdet vara av vilken typ som helst.
* [Tillförlitlig samtidig kö](service-fabric-reliable-services-reliable-concurrent-queue.md): Representerar en replikerad, transaktionell och asynkron bästa möjliga beställningskö för högt dataflöde. I likhet **med concurrentQueue**kan värdet vara av vilken typ som helst.

## <a name="next-steps"></a>Nästa steg

* [Tillförlitliga riktlinjer för insamling & rekommendationer](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Hantera data
  * [Säkerhetskopiera och återställ](service-fabric-reliable-services-backup-restore.md)
  * [Meddelanden](service-fabric-reliable-services-notifications.md)
  * [Reliable Collection-serialisering](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Tillförlitlig tillståndshanterare konfiguration](service-fabric-reliable-services-configuration.md)
* Andra
  * [Snabbstart för Tillförlitliga tjänster](service-fabric-reliable-services-quick-start.md)
  * [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
