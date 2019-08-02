---
title: Introduktion till pålitliga samlingar i Azure Service Fabric tillstånds känsliga tjänster | Microsoft Docs
description: Service Fabric tillstånds känsliga tjänster tillhandahåller pålitliga samlingar som gör det möjligt att skriva moln program med hög tillgänglighet, skalbarhet och låg latens.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: atsenthi
ms.openlocfilehash: a7b30003fd02f8ab2e367311cdb3f56c80dbb4b2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599277"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduktion till pålitliga samlingar i Azure Service Fabric tillstånds känsliga tjänster

Med tillförlitliga samlingar kan du skriva moln program med hög tillgänglighet, skalbarhet och låg latens på samma sätt som om du har skrivit program för enskilda datorer. Klasserna i namn området **Microsoft. ServiceFabric. data. Collections** innehåller en uppsättning samlingar som automatiskt gör ditt tillstånd hög tillgängligt. Utvecklare behöver endast program till Reliable Collection-API: er och låta pålitliga samlingar hantera det replikerade och lokala läget.

Den viktigaste skillnaden mellan pålitliga samlingar och andra tekniker med hög tillgänglighet (till exempel Redis, Azure Table service och Azure Kötjänst) är att statusen hålls lokalt i tjänst instansen och också görs hög tillgänglig. Detta innebär att:

* Alla läsningar är lokala, vilket resulterar i läsningar med låg latens och stora data flöden.
* Alla skrivningar ådrar sig det lägsta antalet nätverks-IOs, vilket resulterar i låg latens och stora data flöden.

![Bild av samlings utveckling.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Tillförlitliga samlingar kan ses som en naturlig utveckling av **systemet. samlings** klasser: en ny uppsättning samlingar som har utformats för molnet och program med flera datorer utan att utveckla komplexiteten för utvecklaren. Tillförlitliga samlingar är därför:

* Replikeras Tillstånds ändringar replikeras för hög tillgänglighet.
* Beständiga Data sparas i disk för hållbarhet mot storskaliga avbrott (till exempel ett data centers strömavbrott).
* Eftersom skrivningar är bestående och replikeras kan du inte skapa en flyktig ReliableDictionary, ReliableQueue eller annan tillförlitlig samling som endast sparar data i minnet.
* Asynkron API: er är asynkrona för att säkerställa att trådar inte blockeras när de uppkommer i/o.
* Transaktions API: er använder abstraktion av transaktioner så att du enkelt kan hantera flera pålitliga samlingar i en tjänst.

Tillförlitliga samlingar ger starka konsekvens garantier från rutan för att göra det enklare att göra orsaken till program tillstånd.
Stark konsekvens uppnås genom att säkerställa att transaktions incheckningar är slutfört först när hela transaktionen har loggats på en majoritet av repliker, inklusive den primära.
För att uppnå svagare konsekvens kan program bekräftas tillbaka till klienten/beställaren innan det asynkrona genomförandet returnerar.

API: erna för Reliable Collections är en utveckling av API: er för samtidiga samlingar (som finns i **system. Collections. samtidig** namnrymd):

* Asynkron Returnerar en aktivitet sedan, till skillnad från samtidiga samlingar, replikeras och sparas.
* Inga out-parametrar: Använder `ConditionalValue<T>` för att returnera `bool` ett och ett värde i stället för parametrar. `ConditionalValue<T>`är som `Nullable<T>` men kräver inte T att vara en STRUCT.
* Transaktioner Använder ett transaktions objekt för att göra det möjligt för användaren att gruppera åtgärder på flera pålitliga samlingar i en transaktion.

Idag innehåller **Microsoft. ServiceFabric. data. Collections** tre samlingar:

* [Tillförlitlig ord lista](https://msdn.microsoft.com/library/azure/dn971511.aspx): Representerar en replikerad, transaktionell och asynkron samling nyckel/värde-par. Precis som med **ConcurrentDictionary**kan både nyckeln och värdet vara av vilken typ som helst.
* [Tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx): Representerar en replikerad, transaktionell och asynkron, första, första, första, första (FIFO) kö. Precis som med **ConcurrentQueue**kan värdet vara av vilken typ som helst.
* [Tillförlitlig samtidig kö](service-fabric-reliable-services-reliable-concurrent-queue.md): Representerar en ordning för en replikerad, transaktionell och asynkron ordning för bästa ansträngningar för stora data flöden. Precis som med **ConcurrentQueue**kan värdet vara av vilken typ som helst.

## <a name="next-steps"></a>Nästa steg

* [Rikt linjer för tillförlitlig insamling & rekommendationer](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Hantera data
  * [Säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md)
  * [Meddelanden](service-fabric-reliable-services-notifications.md)
  * [Reliable Collection-serialisering](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Tillförlitlig tillstånds hanterarens konfiguration](service-fabric-reliable-services-configuration.md)
* Övrigt
  * [Reliable Services snabb start](service-fabric-reliable-services-quick-start.md)
  * [Referens för utvecklare för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
