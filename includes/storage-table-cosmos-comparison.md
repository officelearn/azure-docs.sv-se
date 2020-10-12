---
title: inkludera fil
description: inkludera fil
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 04/06/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 6cf6cc781a9fd5353f458c2317b51a47df779e4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85942362"
---
Om du använder Azure Table Storage får du följande fördelar genom att byta till Azure Cosmos DB:s tabell-API:

|Funktion | Azure Table Storage | Tabell-API för Azure Cosmos DB |
| --- | --- | --- |
| Svarstid | Snabb, men inga övre gränser för svarstid. | Ensiffrig millisekundsvarstid för läsningar och skrivningar med <10 ms svarstid för läsning och <15 ms svarstid vid skrivning vid 99:e percentilen, i valfri skala, var som helst i världen. |
| Dataflöde | Modell med variabelt dataflöde. Tabeller har en gräns för skalbarhet på 20 000 åtgärder/s. | Mycket skalbara med [dedikerat reserverat dataflöde per tabell](../articles/cosmos-db/request-units.md) som understöds av serviceavtal. Konton har ingen maxgräns för dataflöde och kan hantera >10 miljoner åtgärder/s per tabell. |
| Global distribution | En enda region med en valfri läsbar sekundär läsregion för hög tillgänglighet. Du kan inte initiera redundans. | [Nyckelfärdig global distribution](../articles/cosmos-db/distribute-data-globally.md) mellan 1 och 30+ regioner. Stöd för [automatisk och manuell redundans](../articles/cosmos-db/high-availability.md) när som helst och var som helst i världen. |
| Indexering | Ett primärt index för PartitionKey och RowKey. Inga sekundära index. | Automatisk och fullständig indexering för alla egenskaper, utan indexhantering. |
| Söka i data | Frågekörningen använder index för primär nyckel och genomsöker annars. | Frågor kan dra nytta av automatisk indexering av egenskaper för snabba frågetider. |
| Konsekvens | Stark inom primär region. Eventuell inom sekundär region. | [Fem väldefinierade konsekvens nivåer](../articles/cosmos-db/consistency-levels.md) för att handla om tillgänglighet, svars tid, data flöde och konsekvens baserat på dina program behov. |
| Prissättning | Optimerad för lagring. | Optimerad för dataflöde. |
| Serviceavtal | 99,99 % tillgänglighet. | Serviceavtal med 99,99 % tillgänglighet för alla konton med tillgång till en eller flera regioner med konsekvensmodellen ”relaxed” (avslappnad). 99,999 % läsningstillgänglighet för alla databaskonton med tillgång till flera regioner. [Branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) när detta blivit allmänt tillgängligt. |
