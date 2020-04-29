---
title: Introduktion till pålitliga samlingar
description: Service Fabric tillstånds känsliga tjänster tillhandahåller pålitliga samlingar som gör det möjligt att skriva moln program med hög tillgänglighet, skalbarhet och låg latens.
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 78ecc57a4da43bf416839226253e6d0e2f4c1651
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398435"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduktion till pålitliga samlingar i Azure Service Fabric tillstånds känsliga tjänster

Med tillförlitliga samlingar kan du skriva moln program med hög tillgänglighet, skalbarhet och låg latens på samma sätt som om du har skrivit program för enskilda datorer. Klasserna i namn området **Microsoft. ServiceFabric. data. Collections** innehåller en uppsättning samlingar som automatiskt gör ditt tillstånd hög tillgängligt. Utvecklare behöver endast program till Reliable Collection-API: er och låta pålitliga samlingar hantera det replikerade och lokala läget.

Den viktigaste skillnaden mellan pålitliga samlingar och andra tekniker med hög tillgänglighet (till exempel Redis, Azure Table service och Azure Kötjänst) är att statusen hålls lokalt i tjänst instansen och också görs hög tillgänglig. Det innebär att:

* Alla läsningar är lokala, vilket resulterar i läsningar med låg latens och stora data flöden.
* Alla skrivningar ådrar sig det lägsta antalet nätverks-IOs, vilket resulterar i låg latens och stora data flöden.

![Bild av samlings utveckling.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Tillförlitliga samlingar kan ses som en naturlig utveckling av **systemet. samlings** klasser: en ny uppsättning samlingar som har utformats för molnet och program med flera datorer utan att utveckla komplexiteten för utvecklaren. Tillförlitliga samlingar är därför:

* Replikerad: status ändringar replikeras för hög tillgänglighet.
* Asynkron: API: er är asynkrona för att säkerställa att trådar inte blockeras när de uppkommer i/o.
* Transaktionell: API: er använder abstraktion av transaktioner så att du enkelt kan hantera flera pålitliga samlingar i en tjänst.
* Persisted eller volatile: data kan sparas på disk för hållbarhet mot storskaliga drift avbrott (till exempel ett data centers strömavbrott). Vissa pålitliga samlingar stöder också ett temporärt läge (med [varningar](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)) där alla data lagras i minnet, till exempel en replikerad minnes intern cache.

Tillförlitliga samlingar ger starka konsekvens garantier från rutan för att göra det enklare att göra orsaken till program tillstånd.
Stark konsekvens uppnås genom att säkerställa att transaktions incheckningar är slutfört först när hela transaktionen har loggats på en majoritet av repliker, inklusive den primära.
För att uppnå svagare konsekvens kan program bekräftas tillbaka till klienten/beställaren innan det asynkrona genomförandet returnerar.

API: erna för Reliable Collections är en utveckling av API: er för samtidiga samlingar (som finns i **system. Collections. samtidig** namnrymd):

* Asynkron: returnerar en aktivitet sedan, till skillnad från samtidiga samlingar, replikeras och sparas.
* Inga out-parametrar: `ConditionalValue<T>` använder för att `bool` returnera ett och ett värde i stället för parametrar. `ConditionalValue<T>`är som `Nullable<T>` men kräver inte T att vara en STRUCT.
* Transaktioner: använder ett transaktions objekt för att göra det möjligt för användaren att gruppera åtgärder på flera pålitliga samlingar i en transaktion.

Idag innehåller **Microsoft. ServiceFabric. data. Collections** tre samlingar:

* [Tillförlitlig ord lista](https://msdn.microsoft.com/library/azure/dn971511.aspx): representerar en replikerad, transaktionell och asynkron samling nyckel/värde-par. Precis som med **ConcurrentDictionary**kan både nyckeln och värdet vara av vilken typ som helst.
* [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx): representerar en replikerad, transaktionell och asynkron, första, första, första, första (FIFO) kö. Precis som med **ConcurrentQueue**kan värdet vara av vilken typ som helst.
* [Tillförlitlig kö för samtidig](service-fabric-reliable-services-reliable-concurrent-queue.md)användning: motsvarar en ordning för replikerad, transaktionell och asynkron ordning för bästa ansträngningar för stora data flöden. Precis som med **ConcurrentQueue**kan värdet vara av vilken typ som helst.

## <a name="next-steps"></a>Nästa steg

* [Rikt linjer för tillförlitlig insamling & rekommendationer](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Hantera data
  * [Säkerhets kopiering och återställning](service-fabric-reliable-services-backup-restore.md)
  * [Meddelanden](service-fabric-reliable-services-notifications.md)
  * [Reliable Collection-serialisering](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Tillförlitlig tillstånds hanterarens konfiguration](service-fabric-reliable-services-configuration.md)
* Andra
  * [Reliable Services snabb start](service-fabric-reliable-services-quick-start.md)
  * [Referens för utvecklare för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
