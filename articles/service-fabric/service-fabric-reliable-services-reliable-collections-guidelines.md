---
title: Riktlinjer och rekommendationer för tillförlitliga samlingar i Azure Service Fabric | Microsoft Docs
description: Riktlinjer och rekommendationer för att använda Service Fabric Reliable Collections
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
ms.date: 12/10/2017
ms.author: mcoskun
ms.openlocfilehash: 72091d592475e41f254ef7c3882e3d3fee0c491b
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389539"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Riktlinjer och rekommendationer för tillförlitliga samlingar i Azure Service Fabric
Det här avsnittet innehåller riktlinjer för användning av Reliable State Manager och tillförlitliga samlingar. Målet är att hjälpa användarna att undvika vanliga fallgropar.

Riktlinjerna är ordnade som enkel rekommendationer som föregås av villkoren *gör*, *Överväg att*, *Undvik att* och *inte*.

* Ändra inte ett objekt av anpassade typer som returneras av läsåtgärder (till exempel `TryPeekAsync` eller `TryGetValueAsync`). Tillförlitliga samlingar, precis som samtidiga samlingar, returnerar en referens till objekt och inte en kopia.
* Göra djup kopiera det returnerade objektet för en anpassad typ innan du ändrar den. Eftersom strukturer och inbyggda typer av passvärde, behöver du inte göra en djup kopia på dem om de innehåller referens typifierade fält eller egenskaper som du vill ändra.
* Använd inte `TimeSpan.MaxValue` för timeout. Tidsgränser ska användas för att identifiera låsningar.
* Använd inte en transaktion när den har allokerat, avbröts eller tagits bort.
* Använd inte en uppräkning utanför omfattningen för transaktionen som den skapades i.
* Skapa inte en transaktion i en annan transaktion `using` instruktionen eftersom det kan orsaka låsningar.
* Att din `IComparable<TKey>` implementering är korrekt. Systemet tar beroende på `IComparable<TKey>` för sammanslagning kontrollpunkter och rader.
* Använd uppdateringslåset vid läsning av ett objekt med avsikt för att uppdatera den för att förhindra att en viss klass av låsningar.
* Överväg att se till att antalet Reliable Collections per partition ska vara mindre än 1 000. Föredra tillförlitliga samlingar med fler objekt över mer tillförlitliga samlingar med färre objekt.
* Bör du behålla dina objekt (till exempel TKey + TValue för tillförlitlig ordlista) nedan 80 kB: mindre desto bättre. Detta minskar mängden stora objekt Heap användning samt disk- och i/o-kraven. Det minskar ofta replikering av duplicerade data när bara en liten del av värdet uppdateras. Vanliga sätt att uppnå detta i tillförlitlig ordlista är att ta bort rader till flera rader.
* Överväg att använda säkerhetskopiering och återställa funktioner för haveriberedskap.
* Undvika att blanda och samma enhet-åtgärder och åtgärder för flera enheter (t.ex `GetCountAsync`, `CreateEnumerableAsync`) i samma transaktion på grund av de olika isoleringsnivåerna tillåter.
* Hantera InvalidOperationException. Användartransaktioner kan avbrytas av systemet av olika skäl. Till exempel blockerar när Reliable State Manager ändras dess roll från primära eller när en tidskrävande transaktion trunkering av transaktionella loggen. I sådana fall kan användare få InvalidOperationException som anger deras transaktionen har redan avslutats. Under förutsättning att, uppsägning av transaktionen inte begärdes av användaren, bästa sättet att hantera det här undantaget är att ta bort transaktionen, kontrollerar du om annullering token har tagits signalerat (eller replikrollen har ändrats) och om inte att skapa en ny transaktionen och försök igen.  

Här följer några saker att tänka på:

* Standardtidsgränsen är fyra sekunder för alla tillförlitlig samling API: erna. De flesta användare bör använda standard-timeout.
* Annulleringen standardtoken är `CancellationToken.None` i alla tillförlitliga samlingar API: er.
* Parametern nyckeltyp (*TKey*) för en tillförlitlig ordlista korrekt måste implementera `GetHashCode()` och `Equals()`. Nycklar måste vara inte kan ändras.
* Varje tjänst bör ha minst en mål- och replikuppsättningens ange storleken på 3 för att uppnå hög tillgänglighet för tillförlitliga samlingar.
* Läsåtgärder på sekundärt kan läsa versioner som inte är allokerat kvorum.
  Det innebär att en version av data som läses från en enskild sekundär kan vara false gått framåt.
  Läsningar från primära alltid är stabila: kan aldrig vara false gått framåt.
* Säkerhet och sekretess för data som sparas av ditt program i en tillförlitlig samling är ditt beslut och ämne till skydd som tillhandahålls av din lagringshantering; DVS. Diskkryptering för operativsystemet kan användas för att skydda dina data i vila.  

### <a name="next-steps"></a>Nästa steg
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Hantera Data
  * [Säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md)
  * [Meddelanden](service-fabric-reliable-services-notifications.md)
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Konfiguration av Reliable State Manager](service-fabric-reliable-services-configuration.md)
* Andra
  * [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
