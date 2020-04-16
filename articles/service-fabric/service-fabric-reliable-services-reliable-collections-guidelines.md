---
title: Riktlinjer för tillförlitliga samlingar
description: Riktlinjer och rekommendationer för att använda tillförlitliga samlingar för serviceinfrastruktur i ett Azure Service Fabric-program.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: db37067069b2a9eb08009eb6bb373f6fce1cafa9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398526"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Riktlinjer och rekommendationer för tillförlitliga samlingar i Azure Service Fabric
Det här avsnittet innehåller riktlinjer för hur du använder Reliable State Manager och Reliable Collections. Målet är att hjälpa användare att undvika vanliga fallgropar.

Riktlinjerna är organiserade som enkla rekommendationer som föregås av termerna *Do*, *Consider*, *Avoid* och *Do not*.

* Ändra inte ett objekt av anpassad typ som returneras av läsåtgärder (till exempel `TryPeekAsync` eller `TryGetValueAsync`). Tillförlitliga samlingar, precis som Samtidiga samlingar, returnerar en referens till objekten och inte en kopia.
* Kopiera det returnerade objektet av en anpassad typ djup innan du ändrar det. Eftersom strukturer och inbyggda typer är direkt för värde behöver du inte göra en djupkopia av dem om de inte innehåller referensskrivna fält eller egenskaper som du tänker ändra.
* Använd inte `TimeSpan.MaxValue` för time-outs. Time-outs bör användas för att upptäcka dödlägen.
* Använd inte en transaktion när den har genomförts, avbrutits eller avyttrats.
* Använd inte en uppräkning utanför transaktionsomfånget som den skapades i.
* Skapa inte en transaktion i `using` en annan transaktions utdrag eftersom den kan orsaka dödlägen.
* Skapa inte tillförlitligt `IReliableStateManager.GetOrAddAsync` tillstånd med och använd tillförlitligt tillstånd i samma transaktion. Detta resulterar i en InvalidOperationException.
* Se till `IComparable<TKey>` att implementeringen är korrekt. Systemet är beroende `IComparable<TKey>` av för att slå samman kontrollpunkter och rader.
* Använd Uppdatera lås när du läser ett objekt med avsikt att uppdatera den för att förhindra en viss klass av dödlägen.
* Överväg att behålla antalet tillförlitliga samlingar per partition för att vara mindre än 1000. Föredrar tillförlitliga samlingar med fler objekt framför mer tillförlitliga samlingar med färre objekt.
* Överväg att hålla dina objekt (till exempel TKey + TValue för Tillförlitlig Ordlista) under 80 KBytes: mindre desto bättre. Detta minskar mängden large object heap-användning samt disk- och nätverks-IO-krav. Ofta minskar det replikerande data när endast en liten del av värdet uppdateras. Vanligt sätt att uppnå detta i Tillförlitlig ordlista, är att bryta raderna i flera rader.
* Överväg att använda funktioner för säkerhetskopiering och återställning för att få haveriberedskap.
* Undvik att blanda enskilda entitetsoperationer `GetCountAsync`och `CreateEnumerableAsync`transaktioner med flera entiteter (t.ex. ) i samma transaktion på grund av de olika isoleringsnivåerna.
* Hantera InvalidOperationException. Användartransaktioner kan avbrytas av systemet av olika skäl. Till exempel när Reliable State Manager ändrar sin roll från Primär eller när en tidskrävande transaktion blockerar trunkering av transaktionsloggen. I sådana fall kan användaren få InvalidOperationException som anger att deras transaktion redan har avslutats. Förutsatt att uppsägningen av transaktionen inte begärdes av användaren, bästa sättet att hantera detta undantag är att avyttra transaktionen, kontrollera om annulleringstoken har signalerats (eller rollen för repliken har ändrats) och om inte skapa en ny transaktion och försök igen.  

Här är några saker att tänka på:

* Standardutgång är fyra sekunder för alla API:er för tillförlitlig insamling. De flesta användare bör använda standardtidsutgången.
* Standardavbeställningstoken finns `CancellationToken.None` i alla API:er för tillförlitliga samlingar.
* Parametern för nyckeltyp *(TKey)* för en `GetHashCode()` `Equals()`tillförlitlig ordlista måste implementeras korrekt och . Nycklarna måste vara oföränderliga.
* För att uppnå hög tillgänglighet för tillförlitliga samlingar bör varje tjänst ha minst ett mål och minsta replikuppsättningsstorlek på 3.
* Läsåtgärder på den sekundära kan läsa versioner som inte är kvorumbesterat.
  Det innebär att en version av data som läs avstängts från en enda sekundär kan vara falsk.
  Läsningar från Primär är alltid stabila: kan aldrig vara falska framsteg.
* Säkerhet/sekretess för de data som sparas av din applikation i en tillförlitlig samling är ditt beslut och omfattas av det skydd som tillhandahålls av din lagringshantering; Dvs. Diskkryptering av operativsystem kan användas för att skydda dina data i vila.  

## <a name="volatile-reliable-collections"></a>Flyktiga tillförlitliga samlingar
När du bestämmer dig för att använda flyktiga tillförlitliga samlingar bör du tänka på följande:

* ```ReliableDictionary```har flyktigt stöd
* ```ReliableQueue```har flyktigt stöd
* ```ReliableConcurrentQueue```har INTE flyktigt stöd
* Ihållande tjänster KAN INTE göras volatila. Ändra ```HasPersistedState``` flaggan ```false``` till kräver återskapa hela tjänsten från grunden
* Flyktiga tjänster kan inte göras kvar. Ändra ```HasPersistedState``` flaggan ```true``` till kräver återskapa hela tjänsten från grunden
* ```HasPersistedState```är en servicenivå config. Detta innebär att **alla** samlingar antingen kommer att finnas kvar eller flyktiga. Du kan inte blanda flyktiga och beständiga samlingar
* Kvorumförlust av en flyktig partition resulterar i fullständig dataförlust
* Säkerhetskopiering och återställning är INTE tillgängligt för flyktiga tjänster

## <a name="next-steps"></a>Nästa steg
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Hantera data
  * [Säkerhetskopiera och återställa](service-fabric-reliable-services-backup-restore.md)
  * [Meddelanden](service-fabric-reliable-services-notifications.md)
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Tillförlitlig tillståndshanterare konfiguration](service-fabric-reliable-services-configuration.md)
* Andra
  * [Snabbstart för tillförlitliga tjänster](service-fabric-reliable-services-quick-start.md)
  * [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
