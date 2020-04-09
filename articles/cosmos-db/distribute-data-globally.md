---
title: Distribuera data globalt med Azure Cosmos DB
description: Lär dig mer om georeplikering i planetskala, multimaster, redundans och dataåterställning med hjälp av globala databaser från Azure Cosmos DB, en globalt distribuerad databastjänst med flera modeller.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: ef511c203caa11aad4dea1047c982158810c4038
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982131"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Global datadistribution med Azure Cosmos DB – Översikt

Dagens applikationer måste vara mycket lyhörda och alltid online. Instanser av dessa program måste distribueras i datacenter som ligger nära användarna för att uppnå kort svarstid och hög tillgänglighet. Dessa program distribueras vanligtvis i flera datacenter och kallas globalt distribuerade. Globalt distribuerade program behöver en globalt distribuerad databas som transparent kan replikera data var som helst i världen så att programmen kan köras med en datakopia som ligger nära användarna. 

Azure Cosmos DB är en globalt distribuerad databastjänst som är utformad för att ge låg latens, elastisk skalbarhet för dataflöde, väldefinierad semantik för datakonsekvens och hög tillgänglighet. Kort sagt, om ditt program behöver garanterad snabb svarstid var som helst i världen, om det krävs för att alltid vara online, och behöver obegränsad och elastisk skalbarhet av dataflöde och lagring, bör du bygga ditt program på Azure Cosmos DB.

Du kan konfigurera dina databaser så att de distribueras globalt och är tillgängliga i någon av Azure-regionerna. Om du vill sänka svarstiden placerar du data nära den plats där användarna befinner sig. Att välja vilka regioner som krävs beror på programmets globala räckvidd och var användarna finns. Cosmos DB replikerar data på ett transparent sätt till alla regioner som är associerade med ditt Cosmos-konto. Det ger en enda systemavbildning av din globalt distribuerade Azure Cosmos-databas och behållare som ditt program kan läsa och skriva till lokalt. 

Med Azure Cosmos DB kan du när som helst lägga till eller ta bort de regioner som är associerade med ditt konto. Ditt program behöver inte pausas eller distribueras om för att lägga till eller ta bort en region. Det fortsätter att vara mycket tillgänglig hela tiden på grund av flera homing funktioner som tjänsten internt ger.

![Topologi för distribution med hög tillgänglig distribution](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Viktiga fördelar med global distribution

**Skapa globala aktiva och aktiva appar.** Med sitt nya multi-master replikeringsprotokoll stöder varje region både skrivningar och läsningar. Multi-master-funktionen gör det också möjligt:

- Obegränsad elastisk skriv- och lässkalbarhet. 
- 99.999% läs och skriv tillgänglighet över hela världen.
- Garanterade läsningar och skrivningar serveras på mindre än 10 millisekunder vid den 99: e percentilen.

Genom att använda Azure Cosmos DB multi-homing API:er är ditt program medvetet om närmaste region och kan skicka begäranden till den regionen. Den närmaste regionen identifieras utan några konfigurationsändringar. När du lägger till och tar bort regioner i och från ditt Azure Cosmos-konto behöver ditt program inte distribueras om eller pausas, det fortsätter att vara mycket tillgängligt hela tiden.

**Skapa mycket responsiva appar.** Ditt program kan utföra läsningar och skrivningar i nära realtid mot alla regioner som du har valt för databasen. Azure Cosmos DB hanterar internt datareplikeringen mellan regioner med konsekvensnivågarantier för den nivå du har valt.

**Skapa appar med hög tillgång.** Om du kör en databas i flera regioner över hela världen ökar tillgängligheten för en databas. Om en region inte är tillgänglig hanterar andra regioner automatiskt programbegäranden. Azure Cosmos DB erbjuder 99,999 % läs- och skrivtillgänglighet för databaser med flera regioner.

**Upprätthålla kontinuitet i verksamheten vid regionala avbrott.** Azure Cosmos DB stöder [automatisk redundans](how-to-manage-database-account.md#automatic-failover) under ett regionalt avbrott. Under ett regionalt avbrott fortsätter Azure Cosmos DB att upprätthålla sina svarstider, tillgänglighet, konsekvens och dataflödesslakrotar. Cosmos DB erbjuder ett manuellt redundans-API för att simulera ett regionalt avbrott för att säkerställa att hela programmet är mycket tillgängligt. Genom att använda det här API:et kan du utföra regelbundna övningar i affärskontinuitet.

**Skala läs- och skrivdataflöde globalt.** Du kan göra det möjligt för varje region att vara skrivbar och elastiskt skala läser och skriver över hela världen. Dataflödet som ditt program konfigurerar på en Azure Cosmos-databas eller en behållare kommer garanterat att levereras i alla regioner som är associerade med ditt Azure Cosmos-konto. Det etablerade genomströmningen garanteras av [ekonomiskt säkerhetskopierade SLA.](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)

**Välj mellan flera väldefinierade konsekvensmodeller.** Azure Cosmos DB-replikeringsprotokollet erbjuder fem väldefinierade, praktiska och intuitiva konsekvensmodeller. Varje modell har en kompromiss mellan konsekvens och prestanda. Använd dessa konsekvensmodeller för att enkelt skapa globalt distribuerade program.

## <a name="next-steps"></a><a id="Next Steps"></a>Nästa steg

Läs mer om global distribution i följande artiklar:

* [Global distribution](global-dist-under-the-hood.md)
* [Konfigurera multi-master i dina program](how-to-multi-master.md)
* [Konfigurera klienter för multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Lägga till eller ta bort regioner från ditt Azure Cosmos DB-konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Skapa en anpassad konfliktlösningsprincip för SQL API-konton](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programmerbara konsekvensmodeller i Cosmos DB](consistency-levels.md)
* [Välj rätt konsekvensnivå för ditt program](consistency-levels-choosing.md)
* [Konsekvensnivåer i Azure Cosmos DB-API:er](consistency-levels-across-apis.md)
* [Tillgänglighets- och prestandaavvägningar för olika konsekvensnivåer](consistency-levels-tradeoffs.md)

