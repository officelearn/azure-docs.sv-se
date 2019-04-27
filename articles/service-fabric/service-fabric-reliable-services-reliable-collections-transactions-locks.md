---
title: Transaktioner och lås läget i Azure Service Fabric Reliable Collections | Microsoft Docs
description: Azure Service Fabric Reliable State Manager och Reliable Collections transaktioner och låsningar.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: 9785a09a3ac3e119507b4ac28075d887c7edc619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774071"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transaktioner och lås läget i Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transaktion
En transaktion är en sekvens med åtgärder som utförs som en enda logisk enhet för arbete.
En transaktion måste ha följande ACID-egenskaper. (se: https://technet.microsoft.com/library/ms190612)
* **Atomicitet**: En transaktion måste vara en atomisk enhet för arbete. Med andra ord antingen alla dataändringar utförs eller ingen av dem har utförts.
* **Konsekvens**: När du är klar, måste en transaktion lämna alla data i ett konsekvent tillstånd. Alla interna datastrukturer måste vara korrekt i slutet av transaktionen.
* **Isolering**: Ändringar som görs av samtidiga transaktioner måste isoleras från de ändringar som gjorts av andra samtidiga transaktioner. Isoleringsnivån som används för en åtgärd i en ITransaction bestäms av IReliableState som utför åtgärden.
* **Hållbarhet**: När en transaktion har slutförts kan är effekterna permanent på plats i systemet. Ändringarna sparas även i händelse av ett systemfel.

### <a name="isolation-levels"></a>Isoleringsnivåer
Isoleringsnivå som definierar hur som transaktionen måste isoleras från ändringar som görs av andra transaktioner.
Det finns två isoleringsnivåer som stöds i tillförlitliga samlingar:

* **Upprepbar läsning**: Anger att uttryck inte kan läsa data som har ändrats, men ännu inte har skickats av andra transaktioner och att inga andra transaktioner kan ändra data som har lästs av den aktuella transaktionen tills den aktuella transaktionen har slutförts. Mer information finns i [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).
* **Ögonblicksbild**: Anger att data som läses av någon instruktion i en transaktion är transaktionellt konsekvent version av de data som fanns i början av transaktionen.
  Transaktionen kan identifiera endast datamodifieringar som begåtts innan början av transaktionen.
  Datamodifieringar som gjorts av andra transaktioner efter början av den aktuella transaktionen är inte synliga för instruktioner som körs i den aktuella transaktionen.
  Effekten är som om alla uttryck i en transaktion får en ögonblicksbild av den allokerade data som den såg i början av transaktionen.
  Ögonblicksbilder är konsekventa för tillförlitliga samlingar.
  Mer information finns i [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).

Tillförlitliga samlingar väljer automatiskt Isoleringsnivån för en viss Läsåtgärd beroende på åtgärden och replikrollen vid tidpunkten för skapandet av transaktionen.
Följande är den tabell som visar isolering standardnivåer för tillförlitlig ordlista och kö.

| Åtgärden \ roll | Primär | Sekundär |
| --- |:--- |:--- |
| Läs och samma enhet |Upprepbar läsning |Ögonblicksbild |
| Uppräkning, antal |Ögonblicksbild |Ögonblicksbild |

> [!NOTE]
> Vanliga exempel för enskild entitet åtgärder är `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Stöd för Läs Your skriver både tillförlitlig ordlista och tillförlitlig kön.
Alla skrivåtgärder inom en transaktion ska med andra ord vara synliga för följande Läs som hör till samma transaktion.

## <a name="locks"></a>Lås
I Reliable Collections alla transaktioner implementera rigorösa två fas låsning: en transaktion inte släpptes låsen har det fått tills transaktionen avslutas med ett avbrott eller ett genomförande.

Tillförlitlig ordlista använder säkerhet på låsning för alla åtgärder på samma enhet.
Tillförlitlig kö användning av samtidighet för strikt transaktionella FIFO-egenskapen.
Tillförlitlig kö använder åtgärden på Lås så att en transaktion med `TryPeekAsync` och/eller `TryDequeueAsync` och en transaktion med `EnqueueAsync` i taget.
Observera att om du vill bevara FIFO, om en `TryPeekAsync` eller `TryDequeueAsync` någonsin iakttar att tillförlitlig kön är tom, låses också `EnqueueAsync`.

Skriva åtgärder tar alltid exklusivt lås.
För läsåtgärder beror på låsning på ett antal faktorer.
Alla Läsåtgärden klar med ögonblicksbildisolering är låsfri.
Alla Upprepbar läsning åtgärder som standard tar delade Lås.
Användaren kan dock be om en uppdateringslåset i stället för delade låset för skrivskyddade åtgärder som har stöd för Upprepbar läsning.
Något är ett asymmetriskt lås som används för att förhindra att en gemensam form av dödläge som uppstår när flera transaktioner låsa resurser för potentiella uppdateringar vid ett senare tillfälle.

Lås kompatibilitetsöversikten finns i följande tabell:

| Begär \ beviljas | Ingen | Delad | Uppdatering | Exklusiva |
| --- |:--- |:--- |:--- |:--- |
| Delad |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Uppdatering |Ingen konflikt |Ingen konflikt |Konflikt |Konflikt |
| Exklusiva |Ingen konflikt |Konflikt |Konflikt |Konflikt |

Timeout-argumentet i API: er för tillförlitliga samlingar används för identifiering av dödläge.
Till exempel försöker två transaktioner (T1 och T2) läsa och uppdatera K1.
Det är möjligt för dem att deadlock, eftersom de båda hamnar med delade låset.
I det här fallet når en eller båda av åtgärder tidsgränsen.

Det här scenariot för deadlock är ett bra exempel på hur ett uppdateringslås kan förhindra låsningar.

## <a name="next-steps"></a>Nästa steg
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services-aviseringar](service-fabric-reliable-services-notifications.md)
* [Reliable Services-säkerhetskopiering och återställning (haveriberedskap)](service-fabric-reliable-services-backup-restore.md)
* [Konfiguration av Reliable State Manager](service-fabric-reliable-services-configuration.md)
* [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

