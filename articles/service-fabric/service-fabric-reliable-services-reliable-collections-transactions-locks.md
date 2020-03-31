---
title: Transaktioner och låslägen i tillförlitliga samlingar
description: Azure Service Fabric Reliable State Manager och tillförlitliga samlingar transaktioner och låsning.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938915"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transaktioner och låslägen i tillförlitliga samlingar för Azure Service Fabric

## <a name="transaction"></a>Transaktion

En transaktion är en sekvens av åtgärder som utförs som en enda logisk arbetsenhet. Den uppvisar de gemensamma [ACID](https://en.wikipedia.org/wiki/ACID) *(atomicitet,* *konsekvens,* *isolering,* *hållbarhet)* egenskaper databastransaktioner:

* **Atomicitet**: En transaktion måste vara en atomenhet. Med andra ord utförs antingen alla dess dataändringar, eller så utförs ingen av dem.
* **Konsekvens**: När den är slutförd måste en transaktion lämna alla data i ett konsekvent tillstånd. Alla interna datastrukturer måste vara korrekta i slutet av transaktionen.
* **Isolering**: Ändringar som görs av samtidiga transaktioner måste isoleras från de ändringar som görs av andra samtidiga transaktioner. Den isoleringsnivå som används för en åtgärd inom en [ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) bestäms av [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) som utför åtgärden.
* **Hållbarhet**: När en transaktion har slutförts är dess effekter permanent på plats i systemet. Ändringarna kvarstår även i händelse av ett systemfel.

### <a name="isolation-levels"></a>Isoleringsnivåer

Isoleringsnivå definierar i vilken utsträckning transaktionen måste isoleras från ändringar som görs av andra transaktioner.
Det finns två isoleringsnivåer som stöds i tillförlitliga samlingar:

* **Repeterbar läsning**: Anger att utdrag inte kan läsa data som har ändrats men ännu inte bekräftats av andra transaktioner och att inga andra transaktioner kan ändra data som har lästs av den aktuella transaktionen förrän den aktuella transaktionen har slutförts.
* **Ögonblicksbild:** Anger att data som läss av ett utdrag i en transaktion är den transaktionskonsekventa versionen av de data som fanns i början av transaktionen.
  Transaktionen kan endast identifiera dataändringar som har genomförts innan transaktionen startades.
  Dataändringar som görs av andra transaktioner efter starten av den aktuella transaktionen är inte synliga för utdrag som körs i den aktuella transaktionen.
  Effekten är som om utdragen i en transaktion får en ögonblicksbild av de bekräftade data som den fanns i början av transaktionen.
  Ögonblicksbilder är konsekventa i tillförlitliga samlingar.

Tillförlitliga samlingar väljer automatiskt den isoleringsnivå som ska användas för en viss läsåtgärd beroende på åtgärden och replikens roll när transaktionen skapas.
Följande är tabellen som visar standardvärden för isoleringsnivå för tillförlitliga ordliste- och köåtgärder.

| Åtgärd \ Roll | Primär | Sekundär |
| --- |:--- |:--- |
| Läsa en enhet |Repeterbar läsning |Ögonblicksbild |
| Uppräkning, antal |Ögonblicksbild |Ögonblicksbild |

> [!NOTE]
> Vanliga exempel för enskilda `IReliableDictionary.TryGetValueAsync` `IReliableQueue.TryPeekAsync`entitetstransaktioner är .
> 

Både den tillförlitliga ordlistan och den tillförlitliga kön stöder *Läs dina skrivningar*.
Med andra ord kommer alla skrivningar inom en transaktion att vara synliga för en följande läsning som tillhör samma transaktion.

## <a name="locks"></a>Lås

I Tillförlitliga samlingar implementerar alla transaktioner rigorös tvåfaslåsning: en transaktion släpper inte de lås som den har fått förrän transaktionen avslutas med antingen en abort eller ett genomförande.

Reliable Dictionary använder låsning på radnivå för alla enskilda entitetsåtgärder.
Tillförlitlig kö kompromisser samtidighet för strikt transaktionella FIFO-egenskap.
Reliable Queue använder låsningar på `TryPeekAsync` åtgärdsnivå `TryDequeueAsync` som tillåter `EnqueueAsync` en transaktion med och/eller och en transaktion med åt gången.
Observera att om du vill `TryPeekAsync` `TryDequeueAsync` bevara FIFO, om en eller någonsin `EnqueueAsync`observerar att den tillförlitliga kön är tom, kommer de också att låsa .

Skrivåtgärder tar alltid exklusiva lås.
För läsåtgärder beror låsningen på ett par faktorer:

- Alla läsåtgärd som utförs med ögonblicksbildisolering är låsfria.
- Alla repeterbara läsåtgärd som standard tar delade lås.
- Men för alla läsåtgärder som stöder Repeatable Read kan användaren be om ett uppdateringslås i stället för det delade låset.
Ett uppdateringslås är ett asymmetriskt lås som används för att förhindra en vanlig form av dödläge som uppstår när flera transaktioner låser resurser för potentiella uppdateringar vid ett senare tillfälle.

Låskompatibilitetsmatrisen finns i följande tabell:

| Begäran \ Beviljad | Inget | Delad | Uppdatering | Exklusiva |
| --- |:--- |:--- |:--- |:--- |
| Delad |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Uppdatering |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Exklusiva |Ingen konflikt |Konflikt |Konflikt |Konflikt |

Timeout-argumentet i API:er för tillförlitliga samlingar används för identifiering av dödläge.
Till exempel försöker två transaktioner (T1 och T2) läsa och uppdatera K1.
Det är möjligt för dem att dödläge, eftersom de båda sluta med delad lås.
I det här fallet kommer en eller båda av åtgärderna att ta time out. I detta scenario, en uppdatering lås kan förhindra ett sådant dödläge.

## <a name="next-steps"></a>Nästa steg

* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Meddelanden om tillförlitliga tjänster](service-fabric-reliable-services-notifications.md)
* [Reliable Services säkerhetskopiering och återställning (haveriberedskap)](service-fabric-reliable-services-backup-restore.md)
* [Tillförlitlig tillståndshanterare konfiguration](service-fabric-reliable-services-configuration.md)
* [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
