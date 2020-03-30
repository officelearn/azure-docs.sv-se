---
title: Optimera för utveckling och testning i Azure Cosmos DB
description: Den här artikeln förklarar hur Azure Cosmos DB erbjuder flera alternativ för utveckling och testning av tjänsten gratis.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246687"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimera utvecklings- och testkostnaden i Azure Cosmos DB

I den här artikeln beskrivs de olika alternativen för att använda Azure Cosmos DB för utveckling och testning utan kostnad, samt tekniker för att optimera kostnader i utvecklings- eller testkonton.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB emulator (lokalt nedladdningsbar version)

[Azure Cosmos DB emulator](local-emulator.md) är en lokal nedladdningsbar version som efterliknar Azure Cosmos DB molntjänst. Du kan skriva och testa kod som använder Azure Cosmos DB API:er även om du inte har någon nätverksanslutning och utan att ådra dig några kostnader. Azure Cosmos DB-emulatorn tillhandahåller en lokal miljö för utvecklingsändamål med hög trohet till molntjänsten. Du kan utveckla och testa ditt program lokalt, utan att skapa en Azure-prenumeration. När du är redo att distribuera ditt program till molnet uppdaterar du anslutningssträngen för att ansluta till Azure Cosmos DB-slutpunkten i molnet, inga andra ändringar behövs. Du kan också [konfigurera en CI/CD-pipeline med Azure Cosmos DB emulator](tutorial-setup-ci-cd.md) build-uppgiften i Azure DevOps för att köra tester. Du kan komma igång genom att besöka azure [Cosmos DB emulator](local-emulator.md) artikeln.

## <a name="azure-cosmos-db-free-tier"></a>Kostnadsfria Azure Cosmos DB-nivåer 
Azure Cosmos DB-kostnadsfria nivå gör det enkelt att komma igång, utveckla och testa dina program eller till och med köra små produktionsarbetsbelastningar gratis. När den kostnadsfria nivån är aktiverad på ett konto får du de första 400 RU/s-nivåerna och 5 GB lagringsutrymme i kontot utan kostnad. Du kan också skapa en databas med delat dataflöde med 25 behållare som delar 400 RU/s på databasnivå, som alla omfattas av kostnadsfria nivåer (gräns 5 delade dataflödesdatabaser i ett konto på den kostnadsfria nivån). Den kostnadsfria nivån varar på obestämd tid under kontots livstid och levereras med alla [fördelar och funktioner](introduction.md#key-benefits) i ett vanligt Azure Cosmos DB-konto, inklusive obegränsad lagring och dataflöde (RU/s), SLA,hög tillgänglighet, total global distribution i alla Azure-regioner med mera. Du kan ha upp till ett kostnadsfritt nivåkonto per Azure-prenumeration och måste anmäla dig när du skapar kontot. För att komma [igång, skapa ett nytt konto med fri nivå aktiverad](create-cosmosdb-resources-portal.md). Mer information finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/). 

## <a name="try-azure-cosmos-db-for-free"></a>Testa Azure Cosmos DB kostnadsfritt

[Prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) är en kostnadsfri upplevelse som gör att du kan experimentera med Azure Cosmos DB i molnet utan att registrera dig för ett Azure-konto eller använda ditt kreditkort. Try Azure Cosmos DB-konton är tillgängliga under en begränsad tid, för närvarande 30 dagar. Du kan förnya dem när som helst. Prova Azure Cosmos DB-konton gör det enkelt att utvärdera Azure Cosmos DB, skapa och testa ett program eller använda snabbstarter eller självstudier. Du kan också skapa en demo, utföra enhetstestning eller till och med skapa ett konto med flera regioner och köra en app på den utan att ådra dig några kostnader. I ett Try Azure Cosmos DB-konto kan du ha en delad dataflödesdatabas med högst 25 behållare och 20 000 RU/s dataflöde, eller en behållare med upp till 5 000 RU/s. Information om hur du kommer igång finns på [Prova Azure Cosmos DB för kostnadsfri](https://azure.microsoft.com/try/cosmosdb/) sida.

## <a name="azure-free-account"></a>Kostnadsfritt Azure-konto

Azure Cosmos DB ingår i [Det kostnadsfria Azure-kontot](https://azure.microsoft.com/free), som erbjuder Azure-krediter och resurser gratis under en viss tidsperiod. Speciellt för Azure Cosmos DB erbjuder det här kostnadsfria kontot 5 GB lagring och 400 ru:er med etablerat dataflöde för hela året. Med den här upplevelsen kan alla utvecklare enkelt testa funktionerna i Azure Cosmos DB eller integrera den med andra Azure-tjänster till noll kostnad. Med Azure gratis konto får du en $ 200 kredit att spendera under de första 30 dagarna. Du debiteras inte, även om du börjar använda tjänsterna förrän du väljer att uppgradera. Kom igång genom att besöka [Azures kostnadsfria kontosida.](https://azure.microsoft.com/free)

## <a name="use-shared-throughput-databases"></a>Använda delade dataflödesdatabaser

I en [databas med delat dataflöde](set-throughput.md#set-throughput-on-a-database)delar alla behållare i databasen databasen databasens etablerade dataflöde (RU/s). Om du till exempel etablerar en databas med 400 RU/s och har fyra behållare, delar alla fyra behållarna 400 RU/s. I en utvecklings- eller testmiljö, där varje behållare kan nås mindre ofta och därmed kräva lägre än minst 400 RU/s, kan det hjälpa till att optimera kostnaderna genom att placera behållare i en databas med delat dataflöde. 

Anta till exempel att ditt utvecklings- eller testkonto har fyra behållare. Om du skapar fyra behållare med dedikerat dataflöde (minst 400 RU/s) blir din totala RU/s 1600 RU/s. Om du däremot skapar en databas med delat dataflöde (minst 400 RU/s) och lägger dina behållare där, blir dina totala RU/s bara 400 RU/s. I allmänhet är delade dataflödesdatabaser bra för scenarier där du inte behöver garanterat dataflöde på en enskild behållare.  Läs mer om [databaser med delat dataflöde.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Nästa steg

Du kan komma igång med att använda emulatorn eller de kostnadsfria Azure Cosmos DB-kontona med följande artiklar:

* Läs mer om [Optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om [att förstå din Azure Cosmos DB-faktura](understand-your-bill.md)
* Läs mer om [att optimera kostnaden för dataflöde](optimize-cost-throughput.md)
* Läs mer om [att optimera lagringskostnaden](optimize-cost-storage.md)
* Läs mer om [att optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [att optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [att optimera kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)

