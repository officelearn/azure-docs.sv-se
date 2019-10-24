---
title: Optimering för utveckling och testning i Azure Cosmos DB
description: I den här artikeln förklaras hur Azure Cosmos DB erbjuder flera alternativ för utveckling och testning av tjänsten kostnads fritt.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 992d165d323aab79bb7b5475aa396d4432691530
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754902"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimera utvecklings-och test kostnaden i Azure Cosmos DB

I den här artikeln beskrivs de olika alternativen för att använda Azure Cosmos DB för utveckling och testning kostnads fritt.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB emulator (lokalt hämtnings bar version)

[Azure Cosmos DB emulator](local-emulator.md) är en lokal nedladdnings bar version som imiterar Azure Cosmos DB moln tjänsten. Du kan skriva och testa kod som använder Azure Cosmos DB-API: er även om du inte har någon nätverks anslutning och utan att behöva betala några kostnader. Azure Cosmos DB emulator är en lokal miljö i utvecklings syfte med hög kvalitet på moln tjänsten. Du kan utveckla och testa ditt program lokalt utan att skapa en Azure-prenumeration. När du är redo att distribuera ditt program till molnet uppdaterar du anslutnings strängen för att ansluta till Azure Cosmos DB slut punkten i molnet. inga andra ändringar krävs. Du kan också skapa [en CI/CD-pipeline med åtgärden Azure Cosmos DB emulator-](tutorial-setup-ci-cd.md) skapande i Azure DevOps för att köra tester. Du kan komma igång genom att gå till [Azure Cosmos DB emulator](local-emulator.md) -artikeln.

## <a name="try-azure-cosmos-db-for-free"></a>Testa Azure Cosmos DB kostnadsfritt

[Prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) är en kostnads fri upplevelse som gör det möjligt att skapa databaser och samlingar och experimentera med Azure Cosmos db i molnet. Du behöver inte registrera dig för Azure eller betala någon kostnad. Försök Azure Cosmos DB-konton är tillgängliga under en begränsad tid, för närvarande 30 dagar. Du kan förnya dem när du vill. Försök Azure Cosmos DB konton gör det enkelt att utvärdera Azure Cosmos DB, bygga och testa ett program med hjälp av snabb starter eller självstudier. Du kan skapa en demonstration eller utföra enhets testning utan att behöva göra några kostnader. Genom att använda Try-Azure Cosmos DB för kostnads fria konton kan du utvärdera Azure Cosmos DBs Premium funktioner kostnads fritt, inklusive nyckel färdiga globala distributions-, service avtal-och konsekvens modeller. Du kan skapa en databas med högst 25 Azure Cosmos-behållare och 10 000 RU/s av data flödet. Du kan köra exempel programmet utan att prenumerera på ett Azure-konto eller använda ditt kredit kort. Med försök Azure Cosmos DB kostnads fritt kan du skapa ett Azure Cosmos-konto med flera regioner och köra en app på det på bara några minuter. Kom igång genom att läsa [testa Azure Cosmos DB kostnads fri](https://azure.microsoft.com/try/cosmosdb/) sida.

## <a name="azure-free-account"></a>Kostnadsfritt Azure-konto

Azure Cosmos DB ingår i det [kostnads fria Azure-kontot](https://azure.microsoft.com/free), vilket ger Azure-krediter och resurser kostnads fritt under en viss tids period. Specifikt för Azure Cosmos DB erbjuder det här kostnads fria kontot 5 GB lagring och 400 ru: er data flöde för hela året. Den här upplevelsen gör det möjligt för utvecklare att enkelt testa funktionerna i Azure Cosmos DB eller integrera den med andra Azure-tjänster på noll kostnader. Med Azures kostnads fria konto får du en $200 kredit att spendera under de första 30 dagarna. Du debiteras inte, även om du börjar använda tjänsterna tills du väljer att uppgradera. Gå till sidan [kostnads fritt Azure-konto](https://azure.microsoft.com/free) för att komma igång.

## <a name="next-steps"></a>Nästa steg

Du kan komma igång med att använda emulatorn eller de kostnads fria Azure Cosmos DBs kontona med följande artiklar:

* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om hur [du optimerar kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)

