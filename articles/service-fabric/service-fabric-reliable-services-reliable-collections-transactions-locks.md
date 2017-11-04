---
title: "Transaktioner och låslägen i Azure Service Fabric tillförlitliga samlingar | Microsoft Docs"
description: "Azure Service Fabric tillförlitliga tillstånd Manager och tillförlitlig samlingar transaktioner och låsa."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: 3452473f5b2f86d29e46339c997193bc6403736a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transaktioner och låslägen i Azure Service Fabric tillförlitliga samlingar

## <a name="transaction"></a>Transaktionen
En transaktion är en sekvens med åtgärder som utförs som en logisk enhet på arbetet.
En transaktion måste uppvisa följande ACID-egenskaper. (se: https://technet.microsoft.com/en-us/library/ms190612)
* **Odelbarhet**: en transaktion måste vara ett atomic arbetsenheten. Med andra ord antingen alla dataändringar utförs eller ingen av dem har utförts.
* **Konsekvenskontroll**: när du är klar, en transaktion måste lämna alla data i ett konsekvent tillstånd. Alla interna datastrukturer måste vara korrekt i slutet av transaktionen.
* **Isolering**: ändringar som görs av samtidiga transaktioner måste isoleras från de ändringar som gjorts av andra samtidiga transaktioner. Isoleringsnivån som används för en åtgärd i en ITransaction bestäms av IReliableState utför åtgärden.
* **Hållbarhet**: när en transaktion har slutförts effekterna permanent är på plats i systemet. Ändringarna sparas även i händelse av ett systemfel.

### <a name="isolation-levels"></a>Isoleringsnivåer
Isoleringsnivå som definierar i vilken grad som transaktionen måste isoleras från ändringar som görs av andra transaktioner.
Det finns två isoleringsnivåer som stöds i tillförlitliga samlingar:

* **Upprepbar läsning**: Anger att rapporterna inte kan läsa data som har ändrats, men ännu inte har skickats av andra transaktioner och att inga andra transaktioner kan ändra data som har lästs av den aktuella transaktionen tills den aktuella transaktionen har slutförts. Mer information finns i [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Ögonblicksbild**: Anger att data läses av någon instruktion i en transaktion är transaktionellt konsekvent versionen av informationen som fanns i början av transaktionen.
  Transaktionen kan identifiera endast dataändringar som har genomförts före början av transaktionen.
  Dataändringar har gjorts av andra transaktioner efter starten av den aktuella transaktionen är inte synliga för instruktioner som körs i den aktuella transaktionen.
  Effekten blir som om alla uttryck i en transaktion hämta en ögonblicksbild av den allokerade data som den såg i början av transaktionen.
  Ögonblicksbilder är konsekventa för tillförlitlig samlingar.
  Mer information finns i [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Tillförlitliga samlingar väljer automatiskt Isoleringsnivån som ska användas för en viss Läsåtgärd beroende på åtgärden och rollen för repliken vid tidpunkten för skapandet av transaktionen.
Följande är den tabell som visar isolering nivån standardvärden för tillförlitlig ordlista och kön.

| Åtgärden \ roll | Primär | Sekundär |
| --- |:--- |:--- |
| Läs för enhet |Upprepbar läsning |Ögonblicksbild |
| Uppräkning, antal |Ögonblicksbild |Ögonblicksbild |

> [!NOTE]
> Vanliga exempel för den enda entiteten åtgärder är `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Stöd för Läs din skriver både tillförlitliga ordlistan och tillförlitlig kön.
Med andra ord visas alla skrivning i en transaktion för följande Läs som tillhör samma transaktion.

## <a name="locks"></a>Lås
I tillförlitliga samlingar alla transaktioner implementera rigorösa två fas låsning: en transaktion inte frigöra lås som den genererade tills transaktionen avslutas med ett avbrott eller ett genomförande.

Tillförlitliga ordlista använder radnivå låsning för alla åtgärder för samma enhet.
Tillförlitliga kön användning av samtidighet för strikt transaktionella FIFO-egenskapen.
Tillförlitliga kön använder åtgärden nivån Lås så att en transaktion med `TryPeekAsync` och/eller `TryDequeueAsync` och en transaktion med `EnqueueAsync` i taget.
Observera att om du vill bevara FIFO, om en `TryPeekAsync` eller `TryDequeueAsync` någonsin iakttas att tillförlitliga kön är tom, låses också `EnqueueAsync`.

Skriva operations alltid vidta exklusivt lås.
För läsåtgärder beror på låsning på ett antal faktorer.
Alla skrivskyddade åtgärd utfördes med ögonblicksbildisolering är låsfri.
Alla repeterbara Läsåtgärd som standard tar delade Lås.
Användaren kan dock be om något uppdateringslås i stället för det delade låset för alla skrivskyddade åtgärder som stöder Upprepbar läsning.
Något uppdateringslås är ett asymmetriskt lås som används för att förhindra att en gemensam form av dödläge som uppstår när flera transaktioner låsa resurser för potentiella uppdateringar vid ett senare tillfälle.

Kompatibilitetsmatrix för lås finns i följande tabell:

| Begära \ beviljas | Ingen | Delad | Uppdatering | Exklusivt |
| --- |:--- |:--- |:--- |:--- |
| Delad |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Uppdatering |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Exklusivt |Ingen konflikt |Konflikt |Konflikt |Konflikt |

Timeout-argumentet i API: er för tillförlitlig samlingar används för identifiering av dödläge.
Till exempel försöker två transaktioner (T1 och T2) läsa och uppdatera K1.
Det är möjligt för dem att deadlock, eftersom de båda hamna med delade låset.
I det här fallet gör en eller båda av åtgärderna timeout.

Det här scenariot för deadlock är ett bra exempel på hur ett uppdateringslås kan förhindra deadlocks.

## <a name="next-steps"></a>Nästa steg
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services-meddelanden](service-fabric-reliable-services-notifications.md)
* [Tillförlitlig säkerhetskopiering och återställning (återställning)](service-fabric-reliable-services-backup-restore.md)
* [Konfigurationen för hanteraren för tillförlitlig tillstånd](service-fabric-reliable-services-configuration.md)
* [För utvecklare för tillförlitlig samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

