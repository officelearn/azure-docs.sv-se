---
title: Distribuera data globalt med Azure Cosmos DB
description: Lär dig mer om global skala geo-replikering, multimaster, redundans och data med hjälp av globala databaser från Azure Cosmos DB, en globalt distribuerad databastjänst.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 70ead36e20861026e08e864f438071948c526844
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889062"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globala data-distribution med Azure Cosmos DB – översikt

Dagens program måste alltid vara mycket responsiva och ständigt online. Instanser av dessa program måste distribueras i datacenter som ligger nära användarna för att uppnå kort svarstid och hög tillgänglighet. Dessa program distribueras vanligen i flera datacenter och kallas globalt distribuerade. Globalt distribuerade program behöver en globalt distribuerad databas som kan transparent replikerar data överallt i världen att programmen ska fungera på en kopia av de data som ligger nära dess användare. 

Azure Cosmos DB är en globalt distribuerad databastjänst som har utformats för att ge låg latens, elastisk skalbarhet för dataflödet, väldefinierade semantik för datakonsekvens och hög tillgänglighet. Om ditt program behöver tid för garanterade snabba svar var som helst i världen, om det krävs för att alltid vara online och behöver obegränsad och elastisk skalbarhet för dataflöde och lagring, kort sagt så bör du skapa ditt program i Azure Cosmos DB.

Du kan konfigurera databaserna och globalt distribuerad och vara tillgängligt i alla Azure-regioner. Om du vill minska svarstiden, placera data nära där användarna finns. Välja regionerna som krävs beror på global räckvidden för ditt program och var dina användare finns. Cosmos DB replikerar data transparent till alla regioner som är associerat med ditt Cosmos-konto. Det ger en enskild systemavbildning av globalt distribuerade Azure Cosmos-databasen och behållare som ditt program kan läsa och skriva till lokalt. 

Med Azure Cosmos DB kan du lägga till eller ta bort de regioner som är associerade med ditt konto när som helst. Ditt program behöver inte pausas eller omdistribueras om du vill lägga till eller ta bort en region. Det fortsätter att ha hög tillgänglighet när som helst på grund av flera funktioner som internt tillhandahåller tjänsten.

![Topologi för distribution med hög tillgänglighet](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Viktiga fördelar med global distribution

**Skapa globala aktiv-aktiv-appar.** Med dess nya Multi-Master-replikering-protokollet stöder både skrivningar och läsningar i varje region. Flera huvudservrar funktionen ger också:

- Obegränsad elastisk skriva och läsa skalbarhet. 
- 99,999% läsa och skriva tillgänglighet över hela världen.
- Garanterad läsningar och skrivningar som hanteras i mindre än 10 millisekunder i 99: e percentilen.

Med hjälp av Azure Cosmos DB-multihoming API: er, ditt program är medveten om den närmaste regionen och kan skicka begäranden till den regionen. Den närmaste regionen identifieras utan ändringar i konfigurationen. När du lägger till och ta bort regioner till och från ditt Azure Cosmos-konto ditt program behöver inte distribueras om eller pausats, det fortsätter att ha hög tillgänglighet vid alla tidpunkter.

**Skapa tillgängliga appar.** Ditt program kan utföra nästan i realtid läsningar och skrivningar i alla regioner som du valde för din databas. Azure Cosmos DB hanterar internt datareplikeringen mellan regioner med nivån konsekvensgarantier för den nivå du har valt.

**Skapa appar med hög tillgänglighet.** Kör en databas i flera regioner över hela världen ökar tillgängligheten för en databas. Om en region är tillgänglig, hantera programbegäranden automatiskt i andra regioner. Azure Cosmos DB erbjuder 99,999% läsa och skriva tillgänglighet för databaser i flera regioner.

**Upprätthålla affärskontinuiteten vid regionala avbrott.** Azure Cosmos DB stöder [automatisk redundans](how-to-manage-database-account.md#automatic-failover) under ett regionalt strömavbrott. Azure Cosmos DB fortsätter att bibehålla sitt serviceavtal för svarstider, tillgänglighet, konsekvens och dataflöde under ett regionalt strömavbrott. För att se till att hela programmet har hög tillgänglighet, erbjuder Cosmos DB en manuell redundansväxling API för att simulera ett regionalt strömavbrott. Genom att använda detta API kan utföra du regelbundna business continuity tester.

**Skala läser och skriver dataflöde globalt.** Du kan aktivera varje region som ska vara skrivbar och Elastiskt skala läsningar och skrivningar över hela världen. Det dataflöde som ditt program konfigureras på en Azure Cosmos-databas eller en behållare är säkert att levereras i alla regioner som är associerat med ditt Azure Cosmos-konto. Det etablerade dataflödet som garanteras upp av [med ekonomisk uppbackning serviceavtal](https://aka.ms/acdbsla).

**Välj bland flera väldefinierade konsekvensmodeller.** Protokollet replikering för Azure Cosmos DB erbjuder fem väldefinierade, praktiska och intuitiva konsekvensmodeller. Varje modell har en kompromiss mellan konsekvens och prestanda. Använd dessa konsekvensmodeller för att skapa globalt distribuerade program enkelt.

## <a id="Next Steps"></a>Nästa steg

Läs mer om global distribution i följande artiklar:

* [Global distribution – under huven](global-dist-under-the-hood.md)
* [Så här konfigurerar du multimaster i dina program](how-to-multi-master.md)
* [Konfigurera klienter för multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Lägg till eller ta bort regioner från ditt Azure Cosmos DB-konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Skapa principen för en anpassad konfliktlösning för SQL-API-konton](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)