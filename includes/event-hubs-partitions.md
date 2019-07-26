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
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68481580"
---
Event Hubs tillhandahåller meddelandeströmning via ett partitionerat konsumentmönster där varje konsument endast läser en specifik delmängd, eller partition, av meddelandeströmmen. Det här mönstret gör det möjligt att skala horisontellt för händelsebearbetning och tillhandahåller andra strömfokuserade funktioner som inte är tillgängliga i köer och ämnen.

En partition är en ordnad sekvens av händelser som hålls kvar i en händelsehubb. När nya händelser anländer läggs de till i slutet av denna sekvens. En partition kan betraktas som en ”genomförandelogg”.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Event Hubs behåller data under en konfigurerad kvarhållningstid som gäller för alla partitioner i händelsehubben. Händelser löper ut enligt ett tidsschema. Det går inte att ta bort dem. Eftersom partitioner är oberoende av varandra och innehåller sina egna sekvenser med data, växer de ofta i olika takt.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Antalet partitioner anges när de skapas och måste vara mellan 2 och 32. Eftersom det inte går att ändra antalet partitioner bör du tänka på hur många partitioner som kommer att behövas på längre sikt när du anger antalet partitioner. Partitioner är en mekanism för organisering av data som har att göra med vilken underordnad parallellitet som krävs i de program som används. Antalet partitioner i en händelsehubb är direkt kopplat till antalet samtidiga läsare som du förväntar dig. Du kan öka antalet partitioner till mer än 32 genom att kontakta Event Hubs-teamet.

Du kanske vill ange att den ska vara det högsta möjliga värdet, vilket är 32 vid tidpunkten för skapandet. Kom ihåg att om du har mer än en partition resulterar det i händelser som skickas till flera partitioner utan att behållas i ordningen, om du inte konfigurerar avsändare till att bara skicka till en enda partition av 32 som lämnar de återstående 31 partitionerna överflödiga. I det förra fallet måste du läsa händelser över alla 32-partitioner. I det senare fallet finns det ingen uppenbar ytterligare kostnad utöver den extra konfiguration som du måste göra på händelse bearbetnings värden.

Du rekommenderas att inte skicka direkt till en partition partitioner kan identifieras och kan skickas till direkt. I stället kan du använda konstruktioner på högre nivå som introduceras i avsnittet [händelse utfärdare](../articles/event-hubs/event-hubs-features.md#event-publishers) . 

Partitioner är fyllda med en sekvens av händelsedata som innehåller själva händelsen, en användardefinierad egenskapsuppsättning och metadata, till exempel dess offset i partitionen och dess nummer i dataströmsekvensen.

Vi rekommenderar att du balanserar 1:1-dataflödes enheter och partitioner för att uppnå optimal skalning. En enda partition har en garanterad ingående och utgående enhet till en data flödes enhet. Även om du kan få högre data flöde på en partition, garanterar du inte prestanda. Därför rekommenderar vi starkt att antalet partitioner i en händelsehubben är större än eller lika med antalet data flödes enheter.

Utifrån det totala data flöde som du planerar att använda, vet du antalet data flödes enheter du behöver och det lägsta antalet partitioner, men hur många partitioner bör du ha? Välj antal partitioner baserat på den underordnade parallellitet som du vill uppnå samt dina framtida data flödes behov. Det finns ingen kostnad för antalet partitioner som du har i en Event Hub.

Mer information om partitioner och avvägningen mellan tillgänglighet och tillförlitlighet finns i [Programmeringsguide för Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) och i artikeln om [tillgänglighet och konsekvens i Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md).
