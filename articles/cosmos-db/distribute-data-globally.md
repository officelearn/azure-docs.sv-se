---
title: Distribuera data globalt med Azure Cosmos DB
description: Lär dig mer om global skala geo-replikering, multimaster, redundans och data med hjälp av globala databaser från Azure Cosmos DB, en globalt distribuerad databastjänst.
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.openlocfilehash: 2c217a1a89d3b573bfe2297a263bf55849b5f6e1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843860"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Globala data-distribution med Azure Cosmos DB

Dagens program måste vara tillgängliga och alltid online. Instanser av dessa program måste distribueras i datacenter som ligger nära användarna för att uppnå med låg fördröjning och hög tillgänglighet. Dessa program distribueras vanligen i flera datacenter och kallas globalt distribuerade. Globalt distribuerade program behöver en globalt distribuerad databas som kan transparent replikerar data överallt i världen att programmen ska fungera på en kopia av de data som ligger nära dess användare. 

Azure Cosmos DB är en globalt distribuerad databastjänst som har utformats för att ge låg latens, elastisk skalbarhet för dataflödet, väldefinierade semantik för datakonsekvens och hög tillgänglighet. Om ditt program behöver tid för garanterade snabba svar var som helst i världen, om det krävs för att alltid vara online och behöver obegränsad och elastisk skalbarhet för dataflöde och lagring, Överväg du kort sagt: att skapa program med hjälp av Azure Cosmos DB.

Du kan konfigurera databaserna och globalt distribuerad och vara tillgängligt i alla Azure-regioner. Om du vill minska svarstiden, placera data närmare till där användarna finns. Välja regionerna som krävs beror på global räckvidden för ditt program och var dina användare finns. Azure Cosmos DB replikerar data i ditt konto transparent till alla regioner som är associerat med ditt konto. Det ger en enskild systemavbildning av globalt distribuerade Azure Cosmos-databasen och behållare som ditt program kan läsa och skriva till lokalt. 

Med Azure Cosmos DB kan du lägga till eller ta bort de regioner som är associerade med ditt konto när som helst. Ditt program behöver inte pausas eller omdistribueras om du vill lägga till eller ta bort en region. Det fortsätter att ha hög tillgänglighet när som helst på grund av multihoming funktionerna som tillhandahåller tjänsten.

## <a name="key-benefits-of-global-distribution"></a>Viktiga fördelar med global distribution

**Skapa globala aktiv-aktiv-appar.** Med funktionen flera huvudservrar är varje region en skrivregionen. Det är också läsbar. Det garanterar även flera huvudservrar funktionen:

- Obegränsad elastisk skriva skalbarhet. 
- 99,999% läsa och skriva tillgänglighet över hela världen.
- Garanterad läsningar och skrivningar som hanteras i mindre än 10 millisekunder i 99: e percentilen.

Med hjälp av Azure Cosmos DB-multihoming API: er, är ditt program medveten om den närmaste regionen. Det kan sedan skicka begäranden till den regionen. Den närmaste regionen identifieras utan ändringar i konfigurationen. När du lägger till och ta bort regioner från ditt Azure Cosmos DB-konto, behöver inte distribuera om ditt program. Programmet fortsätter att ha hög tillgänglighet.

**Skapa tillgängliga appar.** Ditt program kan enkelt utformas för att utföra nästan i realtid läsningar och skrivningar. Det kan använda ensiffrig svarstid mot alla regioner som du har valt för din databas. Azure Cosmos DB hanterar internt datareplikeringen mellan regioner. Därför garanteras konsekvensnivå som valts för Azure Cosmos DB-kontot.

Många program dra nytta av prestandaförbättringar som levereras med möjligheten att utföra (lokalt) skrivningar i flera regioner. Vissa program som kräver stark konsekvens föredrar att styra alla skrivningar till en enskild region. Azure Cosmos DB stöder en enda region och konfigurationer för flera regioner för dessa program.

**Skapa appar med hög tillgänglighet.** Kör en databas i flera regioner ökar tillgängligheten för databasen. Om en region är tillgänglig, hantera programbegäranden automatiskt i andra regioner. Azure Cosmos DB erbjuder 99,999% läsa och skriva tillgänglighet för databaser i flera regioner.

**Upprätthålla affärskontinuiteten vid regionala avbrott.** Azure Cosmos DB stöder [automatisk redundans](how-to-manage-database-account.md#automatic-failover) under ett regionalt strömavbrott. Azure Cosmos DB fortsätter att bibehålla sitt serviceavtal för svarstider, tillgänglighet, konsekvens och dataflöde under ett regionalt strömavbrott. För att se till att hela programmet har hög tillgänglighet, erbjuder Azure Cosmos DB en manuell redundansväxling API för att simulera ett regionalt strömavbrott. Genom att använda detta API kan utföra du regelbundna business continuity tester.

**Skala läser och skriver dataflöde globalt.** Med flera huvudservrar-funktionen kan du Elastiskt skala läsa och skriva dataflöde över hela världen. Funktionen flera huvudservrar garanterar vilket dataflöde som ditt program konfigureras på en Azure Cosmos DB-databas eller en behållare levereras i alla regioner. Dataflödet också skyddas av [med ekonomisk uppbackning serviceavtal](https://aka.ms/acdbsla).

**Välj bland flera väldefinierade konsekvensmodeller.** Protokollet replikering för Azure Cosmos DB erbjuder fem väldefinierade, praktiska och intuitiva konsekvensmodeller. Varje modell har en kompromiss mellan konsekvens och prestanda. Använd dessa konsekvensmodeller för att skapa globalt distribuerade program enkelt.

## <a id="Next Steps"></a>Nästa steg

Läs mer om global distribution i följande artiklar:

* [Global distribution – under huven](global-dist-under-the-hood.md)
* [Konfigurera klienter för multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Lägg till eller ta bort regioner från ditt Azure Cosmos DB-konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Skapa principen för en anpassad konfliktlösning för SQL-API-konton](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)