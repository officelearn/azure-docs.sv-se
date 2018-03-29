---
title: Riktlinjer och rekommendationer för tillförlitlig samlingar i Azure Service Fabric | Microsoft Docs
description: Riktlinjer och rekommendationer för att använda tillförlitliga samlingar för Service Fabric
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 12/10/2017
ms.author: mcoskun
ms.openlocfilehash: b0eb6a5aab5e71c0a8ac8263e177c493a07dafc0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Riktlinjer och rekommendationer för tillförlitlig samlingar i Azure Service Fabric
Det här avsnittet innehåller riktlinjer för att använda tillförlitliga Tillståndshanterare och tillförlitlig samlingar. Målet är att hjälpa användare att undvika vanliga fallgropar.

Riktlinjerna som är ordnade som enkel rekommendationer med villkoren prefixet *gör*, *Överväg att*, *Undvik* och *inte*.

* Ändra inte ett objekt av anpassade typer som returneras av läsåtgärder (till exempel `TryPeekAsync` eller `TryGetValueAsync`). Tillförlitliga samlingar, precis som samtidiga samlingar returnerar en referens till objekt och inte en kopia.
* Göra djup kopia returnerade objekt av en anpassad typ innan du ändrar den. Eftersom strukturer och inbyggda typer är pass-av-värde, behöver du inte göra en djup kopia på dem om de innehåller referens angiven fält eller egenskaper som du vill ändra.
* Använd inte `TimeSpan.MaxValue` för timeout. Timeout ska användas för att identifiera deadlocks.
* Använd inte en transaktion när den har genomförts, avbröts eller tagits bort.
* Använd inte en uppräkning utanför transaktionsomfång som den skapades i.
* Skapa inte en transaktion i en annan transaktion `using` instruktionen eftersom det kan orsaka deadlocks.
* Att din `IComparable<TKey>` -implementeringen är korrekt. Systemet tar beroende på `IComparable<TKey>` för sammanslagning kontrollpunkter och rader.
* Använd uppdateringslås vid läsning av ett objekt med avsikt för att uppdatera det för att förhindra en viss klass av deadlocks.
* Överväg att behålla antal tillförlitliga samlingar per partition ska vara mindre än 1 000. Föredra tillförlitliga samlingar med flera objekt mer tillförlitlig samlingar med färre objekt.
* Fundera objekten (till exempel TKey + TValue för tillförlitlig ordlistan) under 80 kilobyte: mindre bättre. Detta minskar mängden heapen för stora objekt användning samt disk- och -i/o-kraven. Det minskar ofta replikera dubblettdata när bara en liten del av värdet uppdateras. Vanliga sätt att uppnå det i tillförlitliga ordlistan är att ta bort rader till flera rader.
* Överväg att använda säkerhetskopiering och återställning av funktioner för katastrofåterställning.
* Undvika enhet åtgärder och åtgärder för flera enheter (t.ex `GetCountAsync`, `CreateEnumerableAsync`) i samma transaktion på grund av de olika isoleringsnivåerna tillåter.
* Hantera InvalidOperationException. Användartransaktioner kan avbröts av systemet för många olika skäl. Till exempel blockerar när hanteraren för tillförlitlig tillstånd ändras dess roll utanför primära eller när en tidskrävande transaktion trunkering av loggen transaktionella. I sådana fall kan användare få InvalidOperationException som anger deras transaktionen har redan avslutats. Under förutsättning att, avslutning av transaktionen begärdes inte av användaren, bästa sättet att hantera det här undantaget är att ta bort transaktionen, kontrollera om cancellation-token har har signalerat (eller replikrollen har ändrats) och om inte skapa en ny transaktion och försök igen.  

Här följer några saker att tänka på:

* Standardtidsgränsen är fyra sekunder för alla tillförlitliga samling API: erna. De flesta användare bör använda standardtidsgränsen.
* Standard annullering token är `CancellationToken.None` i alla tillförlitliga samlingar API: er.
* Parametern nyckeltyp (*TKey*) för en tillförlitlig ordlista korrekt måste implementera `GetHashCode()` och `Equals()`. Nycklar måste vara ändras.
* För att uppnå hög tillgänglighet för tillförlitlig samlingar, bör du ha minst ett mål och minsta storleken på 3 för replikuppsättningen varje tjänst.
* Läsåtgärder på sekundärt kan läsa versioner som inte är allokerade kvorum.
  Det innebär att en version av data som läses från en enskild sekundär kan vara false nått.
  Läser från primära är alltid stabila: kan aldrig vara false nått.

### <a name="next-steps"></a>Nästa steg
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Datahantering
  * [Säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md)
  * [Meddelanden](service-fabric-reliable-services-notifications.md)
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Konfigurationen för hanteraren för tillförlitlig tillstånd](service-fabric-reliable-services-configuration.md)
* Andra
  * [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [För utvecklare för tillförlitlig samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
