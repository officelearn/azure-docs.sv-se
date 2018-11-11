---
title: Distribuera data globalt med Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om global skala geo-replikering, multimaster, redundans och data med hjälp av globala databaser från Azure Cosmos DB, en globalt distribuerad databastjänst.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 4aa5e4ff46eeaa4e8d8c723f626dd1f1193fd12a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281616"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Globala data-distribution med Azure Cosmos DB

Många av dagens program körs i global skala. Dessa program är alltid på och tillgänglig för användare över hela världen. Hantera den globala distributionen av data som används av dessa program samtidigt som det ger hög prestanda och hög tillgänglighet är svåra problem. Azure Cosmos DB är en globalt distribuerad databastjänst som är utformad att ge hög prestanda och hög tillgänglighet. Dessa orsaker, Azure Cosmos DB som passar bäst för programmen i realtid.

Cosmos DB är en grundläggande Azure-tjänst och den är tillgänglig i alla [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/) som standard. Microsoft fungerar Azure-datacenter i 54 + regioner över hela världen och fortsätter att för att möta de växande behov av kunderna. När du skapar en Cosmos DB-konto kan bestämma du vilken region som den ska distribueras i. Microsoft Driver Cosmos-DB-tjänsten 24/7, så du kan fokusera på dina program.

Du kan konfigurera databaserna och globalt distribuerad och vara tillgängligt i någon av de 50 Azure-regionerna. Om du vill minska svarstiden, bör du placera data närmare på användarens plats. Välja regionerna som krävs beror på global räckvidden för ditt program och var dina användare finns. Cosmos DB replikerar data i ditt konto transparent till alla konfigurerade regioner. Det ger en enda systemavbildning Cosmos-databasen och behållare som ditt program kan läsa och skriva lokalt. Med Cosmos DB kan du lägga till eller ta bort de regioner som är associerade med ditt konto när som helst. Ditt program behöver inte pausas eller omdistribueras om du vill lägga till eller ta bort en region. Det fortsätter att ha hög tillgänglighet när som helst på grund av flera funktioner som tjänsten tillhandahåller.

## <a name="key-benefits-of-global-distribution"></a>Viktiga fördelar med global distribution

**Skapa appar i global aktiv-aktiv**: med funktionen för flera huvudservrar, varje region är en skrivregion (förutom att vara läsbara). Flera master funktion också garantier - obegränsad elastisk skrivning skalbarhet, tillförlitlighet på 99,999% läser och skriver tillgänglighet hela världen och garanterad läsningar/skrivningar hanteras i mindre än 10 millisekunder i 99: e percentilen.  

Genom att använda Azure Cosmos DB flera API: er, ditt program är medveten om den närmaste regionen och den kan skicka begäranden till den regionen. Närmaste regionen identifieras utan ändringar i konfigurationen. När du lägger till och ta bort regioner från ditt Cosmos DB-konto, ditt program behöver inte distribueras och fortsätter att ha hög tillgänglighet.

**Skapa tillgängliga appar**: ditt program enkelt kan utformas för att utföra nästan i realtid läsningar och skrivningar med ensiffrig svarstid mot alla regioner som du har valt för din databas.  Azure Cosmos DB hanterar internt datareplikering mellan regioner så att konsekvensnivå som valts för Cosmos-konto är korrekt.

Många program kan dra nytta prestandaförbättringar som levereras med möjligheten att utföra (lokalt) skrivningar i flera regioner. Vissa program som kräver stark konsekvens föredrar att styra alla skrivningar till en enskild region. För att stödja dessa program, stöder Cosmos DB både en enda region och konfigurationer för flera regioner.

**Skapa appar med hög tillgänglighet**: köra en databas i flera områden ökar tillgängligheten för databasen. Om en region är tillgänglig, hanterar automatiskt programförfrågningar i andra regioner. Azure Cosmos DB erbjuder 99,999% läsa och skriva tillgänglighet för databaser i flera regioner.

**Affärskontinuitet under regionala avbrott**: Azure Cosmos DB stöder [automatisk redundans](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) under ett regionalt strömavbrott. Under ett regionalt strömavbrott, är det dessutom Cosmos DB som fortsätter att upprätthålla appens svarstid, tillgänglighet, konsekvens och dataflöde serviceavtal. För att säkerställa att hela programmet har hög tillgänglighet, erbjuder Azure Cosmos DB manuell redundans API för att simulera ett regionalt strömavbrott. Genom att använda detta API kan utföra du regelbundna business continuity tester.

**Global läsa och skriva skalbarhet**: med flera huvudservrar kapacitet kan du Elastiskt skala läsa och skriva dataflöde över hela världen. Flera huvudservrar funktionen garanterar att dataflödet som ditt program konfigureras på en Azure Cosmos DB-databas eller en behållare levereras i alla regioner och skyddas av [med ekonomisk uppbackning serviceavtal](https://aka.ms/acdbsla).

**Flera, väldefinierade konsekvensmodeller**: Azure Cosmos DB replikering protocol är avsett att erbjuda fem väldefinierade, praktiska och intuitiva konsekvensmodeller. Varje konsekvensmodell har en kompromiss mellan konsekvens och prestanda. Dessa konsekvensmodeller kan du skapa globalt distribuerade program enkelt.

## <a id="Next Steps"></a>Nästa steg

Läs mer om global distribution i följande artiklar:

* [Global distribution – under huven](global-dist-under-the-hood.md)
* [Så här konfigurerar du klienter för flera värdar](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Hur du lägger till/ta bort regioner från ditt Cosmos-konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Så här skapar du en principen för anpassad konfliktlösning för SQL-API-konton](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)