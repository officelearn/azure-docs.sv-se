---
title: Distribuera data globalt med Azure Cosmos DB
description: Lär dig mer om att skala geo-replikering, multi-master, redundans och data återställning med globala databaser från Azure Cosmos DB, en globalt distribuerad databas tjänst för flera data modeller.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: ef511c203caa11aad4dea1047c982158810c4038
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982131"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Global datadistribution med Azure Cosmos DB – Översikt

Dagens program måste ha hög tillgänglighet och är alltid online. Instanser av dessa program måste distribueras i datacenter som ligger nära användarna för att uppnå kort svarstid och hög tillgänglighet. Dessa program distribueras vanligt vis i flera data Center och kallas globalt distribuerade. Globalt distribuerade program behöver en globalt distribuerad databas som transparent kan replikera data var som helst i världen så att programmen kan köras med en datakopia som ligger nära användarna. 

Azure Cosmos DB är en globalt distribuerad databas tjänst som är utformad för att tillhandahålla låg latens, elastisk skalbarhet för data flöde, väldefinierade semantik för data konsekvens och hög tillgänglighet. Om ditt program behöver garanterad snabb svars tid var som helst i världen, om det är nödvändigt att alltid vara online och behöver en obegränsad och elastisk skalbarhet för data flöde och lagring, bör du bygga ditt program på Azure Cosmos DB.

Du kan konfigurera dina databaser så att de distribueras globalt och är tillgängliga i alla Azure-regioner. Minska svars tiden genom att placera data nära var användarna finns. Att välja de regioner som krävs beror på programmets globala räckvidd och var användarna finns. Cosmos DB replikerar data transparent till alla regioner som är kopplade till ditt Cosmos-konto. Den innehåller en enda system avbildning av din globalt distribuerade Azure Cosmos-databas och behållare som ditt program kan läsa och skriva till lokalt. 

Med Azure Cosmos DB kan du när som helst lägga till eller ta bort regioner som är kopplade till ditt konto. Ditt program behöver inte pausas eller omdistribueras för att lägga till eller ta bort en region. Den fortsätter att vara hög tillgänglig hela tiden på grund av de värdar-funktioner som tjänsten har inbyggt.

![Topologi för distribution med hög tillgänglighet](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Viktiga fördelar med global distribution

**Bygg globala Active-Active-appar.** Med den nya multi-master Replication-protokollet stöder varje region både skrivningar och läsningar. Funktionen med flera huvud servrar möjliggör också:

- Obegränsad elastisk Skriv-och Läs skalbarhet. 
- 99,999% tillgänglighet för läsning och skrivning över hela världen.
- Garanterade läsningar och skrivningar som betjänas på mindre än 10 millisekunder vid 99 percentil.

Genom att använda API: erna Azure Cosmos DB multi-värdar är ditt program medvetna om den närmaste regionen och kan skicka begär anden till den regionen. Den närmaste regionen identifieras utan konfigurations ändringar. När du lägger till och tar bort regioner i och från ditt Azure Cosmos-konto behöver programmet inte distribueras om eller pausas, men det fortsätter att vara hög tillgängligt hela tiden.

**Bygg appar med hög tillgänglighet.** Ditt program kan utföra nästan real tids läsningar och skrivningar mot alla regioner som du har valt för din databas. Azure Cosmos DB hanterar datareplikeringen internt mellan regioner med konsekvens nivå garantier för den nivå som du har valt.

**Bygg appar med hög tillgänglighet.** Att köra en databas i flera regioner ökar över gången till en databas. Om en region inte är tillgänglig hanterar andra regioner automatiskt program begär Anden. Azure Cosmos DB erbjuder 99,999% Läs-och skriv tillgänglighet för databaser i flera regioner.

**Upprätthålla verksamhets kontinuitet under regionala drifts avbrott.** Azure Cosmos DB stöder [Automatisk redundans](how-to-manage-database-account.md#automatic-failover) under ett regionalt avbrott. Under ett regionalt avbrott fortsätter Azure Cosmos DB att bibehålla svars tid, tillgänglighet, konsekvens och data flödes service avtal. För att se till att hela programmet har hög tillgänglighet Cosmos DB erbjuder en manuell redundansväxlings-API för att simulera ett regionalt avbrott. Genom att använda det här API: et kan du utföra regelbundna affärs kontinuitets övningar.

**Skala Läs-och Skriv data flöde globalt.** Du kan aktivera varje region för att vara skrivbar och samtidigt skala läsningar och skriv över hela världen. Data flödet som ditt program konfigurerar på en Azure Cosmos-databas eller en behållare garanterar att de levereras i alla regioner som är kopplade till ditt Azure Cosmos-konto. Det etablerade data flödet garanteras av [finansiellt säkerhetskopierade service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

**Välj bland flera väldefinierade konsekvens modeller.** Azure Cosmos DB Replication-protokollet erbjuder fem väldefinierade, praktiska och intuitiva konsekvens modeller. Varje modell har en kompromiss mellan konsekvens och prestanda. Använd dessa konsekvens modeller för att enkelt bygga globalt distribuerade program.

## <a name="next-steps"></a><a id="Next Steps"></a>Nästa steg

Läs mer om global distribution i följande artiklar:

* [Global distribution](global-dist-under-the-hood.md)
* [Så här konfigurerar du flera huvud i dina program](how-to-multi-master.md)
* [Konfigurera klienter för multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Lägg till eller ta bort regioner från ditt Azure Cosmos DB konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Skapa en anpassad konflikt lösnings princip för SQL API-konton](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programmerings bara konsekvens modeller i Cosmos DB](consistency-levels.md)
* [Välj rätt konsekvens nivå för ditt program](consistency-levels-choosing.md)
* [Konsekvens nivåer i Azure Cosmos DB API: er](consistency-levels-across-apis.md)
* [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](consistency-levels-tradeoffs.md)

