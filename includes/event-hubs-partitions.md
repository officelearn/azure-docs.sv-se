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
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68481580"
---
Event Hubs tillhandahåller meddelandeströmning via ett partitionerat konsumentmönster där varje konsument endast läser en specifik delmängd, eller partition, av meddelandeströmmen. Det här mönstret gör det möjligt att skala horisontellt för händelsebearbetning och tillhandahåller andra strömfokuserade funktioner som inte är tillgängliga i köer och ämnen.

En partition är en ordnad sekvens av händelser som hålls kvar i en händelsehubb. När nya händelser anländer läggs de till i slutet av denna sekvens. En partition kan betraktas som en ”genomförandelogg”.

![Händelsehubbar](./media/event-hubs-partitions/partition.png)

Event Hubs behåller data för en konfigurerad kvarhållningstid som gäller för alla partitioner i händelsehubben. Händelser löper ut enligt ett tidsschema. Det går inte att ta bort dem. Eftersom partitioner är oberoende av varandra och innehåller sina egna sekvenser med data, växer de ofta i olika takt.

![Händelsehubbar](./media/event-hubs-partitions/multiple-partitions.png)

Antalet partitioner anges när de skapas och måste vara mellan 2 och 32. Eftersom det inte går att ändra antalet partitioner bör du tänka på hur många partitioner som kommer att behövas på längre sikt när du anger antalet partitioner. Partitioner är en mekanism för organisering av data som har att göra med vilken underordnad parallellitet som krävs i de program som används. Antalet partitioner i en händelsehubb är direkt kopplat till antalet samtidiga läsare som du förväntar dig. Du kan öka antalet partitioner till mer än 32 genom att kontakta Event Hubs-teamet.

Du kanske vill ställa in det så att det är det högsta möjliga värdet, vilket är 32, vid tidpunkten för skapandet. Kom ihåg att om du har mer än en partition kommer att resultera i händelser som skickas till flera partitioner utan att behålla ordningen, såvida du inte konfigurerar avsändare för att bara skicka till en enda partition av de 32 lämnar de återstående 31 partitionerna överflödiga. I det förra fallet måste du läsa händelser över alla 32 partitioner. I det senare fallet finns det ingen uppenbar extra kostnad förutom den extra konfiguration du måste göra på Event Processor Host.

Partitioner kan identifieras och kan skickas direkt, men det rekommenderas inte att skicka direkt till en partition. I stället kan du använda konstruktioner på högre nivå som introduceras i avsnittet [Händelseutgivare.](../articles/event-hubs/event-hubs-features.md#event-publishers) 

Partitioner fylls med en sekvens av händelsedata som innehåller händelsens brödtext, en användardefinierad egenskapspåse och metadata, till exempel dess förskjutning i partitionen och dess nummer i flödessekvensen.

Vi rekommenderar att du balanserar 1:1-dataflödesenheter och partitioner för att uppnå optimal skala. En enda partition har en garanterad ingående och utgående på upp till en dataflödesenhet. Även om du kanske kan uppnå högre dataflöde på en partition, är prestanda inte garanteras. Därför rekommenderar vi starkt att antalet partitioner i en händelsenav är större än eller lika med antalet dataflödesenheter.

Med tanke på det totala dataflödet du planerar att behöva, vet du hur många dataflödesenheter du behöver och det minsta antalet partitioner, men hur många partitioner ska du ha? Välj antal partitioner baserat på den underordnade parallellism du vill uppnå samt dina framtida dataflödesbehov. Det finns ingen avgift för antalet partitioner du har i en eventhubb.

Mer information om partitioner och avvägningen mellan tillgänglighet och tillförlitlighet finns i [Programmeringsguide för Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) och i artikeln om [tillgänglighet och konsekvens i Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md).
