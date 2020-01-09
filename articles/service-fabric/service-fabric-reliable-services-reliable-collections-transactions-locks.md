---
title: Transaktioner och lås lägen i Reliable Collections
description: Azure Service Fabric Reliable State Manager och Reliable Collections-transaktioner och-låsning.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: f27381aa0979b37c759f66d0e873126edc006d6d
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614187"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transaktioner och lås lägen i Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transaktionen
En transaktion är en sekvens med åtgärder som utförs som en enda logisk arbets enhet.
En transaktion måste ha följande egenskaper för syra. (se: https://technet.microsoft.com/library/ms190612)
* **Atomicer**: en transaktion måste vara en atomisk arbets enhet. Med andra ord utförs antingen alla data ändringar eller ingen av dem.
* **Konsekvens**: när det är slutfört måste en transaktion lämna alla data i ett konsekvent tillstånd. Alla interna data strukturer måste vara korrekta i slutet av transaktionen.
* **Isolering**: ändringar gjorda av samtidiga transaktioner måste isoleras från ändringar som gjorts av andra samtidiga transaktioner. Isolerings nivån som används för en åtgärd i en ITransaction bestäms av IReliableState som utför åtgärden.
* **Hållbarhet**: när en transaktion har slutförts, är dess effekter permanent på plats i systemet. Ändringarna är kvar även om ett systemfel inträffar.

### <a name="isolation-levels"></a>Isoleringsnivåer
Isolerings nivån definierar i vilken grad transaktionen måste isoleras från ändringar som gjorts av andra transaktioner.
Det finns två isolerings nivåer som stöds i pålitliga samlingar:

* **Repeterbar läsning**: anger att instruktioner inte kan läsa data som har ändrats men ännu inte har allokerats av andra transaktioner och att inga andra transaktioner kan ändra data som har lästs av den aktuella transaktionen tills den aktuella transaktionen har slutförts. Mer information finns i [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Ögonblicks bild**: anger att data som lästs av valfri instruktion i en transaktion är den transaktions konsekventa versionen av de data som fanns i början av transaktionen.
  Transaktionen kan bara identifiera data ändringar som har genomförts innan transaktionen startades.
  Data ändringar som gjorts av andra transaktioner efter det att den aktuella transaktionen har startats är inte synliga för instruktioner som körs i den aktuella transaktionen.
  Detta är som om uttrycken i en transaktion får en ögonblicks bild av de allokerade data som fanns i början av transaktionen.
  Ögonblicks bilder är konsekventa i pålitliga samlingar.
  Mer information finns i [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Reliable Collections väljer automatiskt den isolerings nivå som ska användas för en specifik Läs åtgärd beroende på åtgärden och replik rollen vid tidpunkten för skapandet av transaktionen.
Följande är den tabell som visar isolerings nivå standarder för tillförlitliga ord listor och Queue-åtgärder.

| Åtgärd \ roll | Primär | Sekundär |
| --- |:--- |:--- |
| En entitet har lästs |Upprepnings bar läsning |Ögonblicksbild |
| Uppräkning, antal |Ögonblicksbild |Ögonblicksbild |

> [!NOTE]
> Vanliga exempel för åtgärder med enstaka entiteter `IReliableDictionary.TryGetValueAsync``IReliableQueue.TryPeekAsync`.
> 

Både den pålitliga ord listan och den pålitliga kön har stöd för att läsa dina skrivningar.
Alla Skriv åtgärder i en transaktion är med andra ord synliga för en följande läsning som tillhör samma transaktion.

## <a name="locks"></a>Lås
I Reliable Collections implementerar alla transaktioner rigoröst två fas låsningar: en transaktion släpper inte de lås som har förvärv ATS förrän transaktionen avslutas med antingen ett avbrott eller en incheckning.

En tillförlitlig ord lista använder rad nivå låsning för alla åtgärder för enskild entitet.
Reliable Queue-handelsmässiga från samtidighet för en strikt transaktionell FIFO-egenskap.
I Reliable Queue används åtgärds nivå lås som tillåter en transaktion med `TryPeekAsync` och/eller `TryDequeueAsync` och en transaktion med `EnqueueAsync` i taget.
Observera att för att bevara FIFO, om en `TryPeekAsync` eller `TryDequeueAsync` någonsin ser att den Reliable kön är tom, kommer de också att låsa `EnqueueAsync`.

Skriv åtgärder tar alltid exklusiva lås.
Vid Läs åtgärder är låsningen beroende av några faktorer.
Alla Läs åtgärder som utfördes med ögonblicks bild isolering är helt kostnads fria.
Alla upprepnings bara Läs åtgärder tar som standard delade lås.
För alla Läs åtgärder som stöder repeterbar läsning kan användaren dock be om ett uppdaterings lås i stället för det delade låset.
Ett uppdaterings lås är ett asymmetriskt lås som används för att förhindra en vanlig form av död läge som inträffar när flera transaktioner låser resurser för potentiella uppdateringar vid ett senare tillfälle.

Matrisen för att låsa kompatibilitet finns i följande tabell:

| Begäran har beviljats | Inget | Delad | Uppdatering | Öppna |
| --- |:--- |:--- |:--- |:--- |
| Delad |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Uppdatering |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Öppna |Ingen konflikt |Konflikt |Konflikt |Konflikt |

Timeout-argument i API: erna för Reliable Collections används för identifiering av död läge.
Två transaktioner (T1 och T2) försöker till exempel läsa och uppdatera K1.
Det är möjligt för dem att deadlock, eftersom de båda har ett delat lås.
I det här fallet har en eller båda åtgärderna nått sin tids gräns.

Det här deadlock-scenariot är ett bra exempel på hur ett uppdaterings lås kan förhindra död lägen.

## <a name="next-steps"></a>Nästa steg
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services meddelanden](service-fabric-reliable-services-notifications.md)
* [Reliable Services säkerhets kopiering och återställning (haveri beredskap)](service-fabric-reliable-services-backup-restore.md)
* [Tillförlitlig tillstånds hanterarens konfiguration](service-fabric-reliable-services-configuration.md)
* [Referens för utvecklare för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

