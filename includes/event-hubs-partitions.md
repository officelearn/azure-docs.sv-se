---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b23f9532aa1ca6f7bae914ff8cb9d7566a0fec86
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841493"
---
Event Hubs tillhandahåller meddelandeströmning via ett partitionerat konsumentmönster där varje konsument endast läser en specifik delmängd, eller partition, av meddelandeströmmen. Det här mönstret gör det möjligt att skala horisontellt för händelsebearbetning och tillhandahåller andra strömfokuserade funktioner som inte är tillgängliga i köer och ämnen.

En partition är en ordnad sekvens av händelser som hålls kvar i en händelsehubb. När nya händelser anländer läggs de till i slutet av denna sekvens. En partition kan betraktas som en ”genomförandelogg”.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Event Hubs behåller data under en konfigurerad kvarhållningstid som gäller för alla partitioner i händelsehubben. Händelser löper ut enligt ett tidsschema. Det går inte att ta bort dem. Eftersom partitioner är oberoende av varandra och innehåller sina egna sekvenser med data, växer de ofta i olika takt.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Antalet partitioner anges när de skapas och måste vara mellan 2 och 32. Eftersom det inte går att ändra antalet partitioner bör du tänka på hur många partitioner som kommer att behövas på längre sikt när du anger antalet partitioner. Partitioner är en mekanism för organisering av data som har att göra med vilken underordnad parallellitet som krävs i de program som används. Antalet partitioner i en händelsehubb är direkt kopplat till antalet samtidiga läsare som du förväntar dig. Du kan öka antalet partitioner till mer än 32 genom att kontakta Event Hubs-teamet.

Du rekommenderas att inte skicka direkt till en partition partitioner kan identifieras och kan skickas till direkt. Du kan i stället använda konstruktioner på högre nivå introducerades i den [händelseutfärdare](../articles/event-hubs/event-hubs-features.md#event-publishers) avsnittet. 

Partitioner är fyllda med en sekvens av händelsedata som innehåller själva händelsen, en användardefinierad egenskapsuppsättning och metadata, till exempel dess offset i partitionen och dess nummer i dataströmsekvensen.

Vi rekommenderar att du balanserar 1:1-genomflödesenheter och partitioner för att uppnå optimal skala. En enskild partition har en garanterad ingående och utgående på upp till en genomflödesenhet. Du kanske kan uppnå högre dataflöde på en partition, garanteras inte prestanda. Det är därför vi rekommenderar starkt att antalet partitioner i en händelsehubb är större än eller lika med antalet dataflödesenheter.

Det totala dataflödet som du planerar att behöva får du vet antal throughput units som du behöver och det minsta antalet partitioner, men hur många partitioner bör du ha? Välj antal partitioner baserat på den underordnade parallellitet som du vill uppnå samt framtida dataflödet behöver. Det finns ingen kostnad för antalet partitioner som du har i en Händelsehubb.

Mer information om partitioner och avvägningen mellan tillgänglighet och tillförlitlighet finns i [Programmeringsguide för Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) och i artikeln om [tillgänglighet och konsekvens i Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md).
