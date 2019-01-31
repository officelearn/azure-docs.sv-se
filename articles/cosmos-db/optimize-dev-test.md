---
title: Optimera för utveckling och testning i Azure Cosmos DB
description: Den här artikeln förklarar hur Azure Cosmos DB erbjuder flera alternativ för utveckling och testning av tjänsten utan kostnad.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: 3bb39fe99e612fb19f12fd049ebb8f600e48373e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457878"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimera utveckling och testning kostnaden i Azure Cosmos DB

Den här artikeln beskrivs olika alternativ för att använda Azure Cosmos DB för utveckling och testning utan kostnad för kostnad.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB-emulatorn (lokalt nedladdningsbar version)

[Azure Cosmos DB-emulatorn](local-emulator.md) är en lokal nedladdningsbar version som efterliknar Molntjänsten Azure Cosmos DB. Du kan skriva och testa kod som använder Azure Cosmos DB-API: er, även om du har någon nätverksanslutning och utan utan kostnad. Azure Cosmos DB-emulatorn tillhandahåller en lokal miljö för utveckling av hög kvalitet till Molntjänsten. Du kan utveckla och testa ditt program lokalt, utan att skapa en Azure-prenumeration. När du är redo att distribuera ditt program till molnet, uppdatera anslutningssträngen för att ansluta till Azure Cosmos DB-slutpunkt i molnet, behövs inga andra ändringar. Du kan också [ställa in en CI/CD-pipeline med Azure Cosmos DB-emulatorn](tutorial-setup-ci-cd.md) Skapa aktivitet i Azure DevOps ska kunna köra tester. Du kan komma igång genom att besöka den [Azure Cosmos DB-emulatorn](local-emulator.md) artikeln.

## <a name="try-azure-cosmos-db-for-free"></a>Testa Azure Cosmos DB kostnadsfritt

[Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) är en kostnadsfri kostnad upplevelse som gör det möjligt att skapa databasen och samlingar och experimentera med Azure Cosmos DB i molnet. Du inte behöver registrera dig för Azure eller betala någon kostnad. Prova Azure Cosmos DB-konton är tillgängliga under en begränsad tid, för närvarande 30 dagar. Du kan förnya dem när som helst. Prova Azure Cosmos DB konton gör det enkelt att prova Azure Cosmos DB, bygga och testa ett program med hjälp av Snabbstarter eller självstudier. Du kan skapa en demonstration eller utföra Enhetstestning utan utan kostnad. Prova Azure Cosmos DB med hjälp av för kostnadsfria konton, du kan utvärdera premiumfunktioner för Azure Cosmos DB utan kostnad, inklusive nyckelfärdig global distribution, serviceavtal och konsekvens modeller. Du kan skapa en databas med högst 25 Azure Cosmos-behållare och 10 000 RU/s genomströmning. Du kan köra exempelprogrammet utan att prenumerera på ett Azure-konto eller använda ditt kreditkort. Med prova Azure Cosmos DB du utan kostnad, skapa ett Azure Cosmos-konto för flera regioner och kör en app på den på bara några minuter. Kom igång genom att se [testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) sidan.

## <a name="azure-free-account"></a>Kostnadsfritt Azure-konto

Azure Cosmos DB ingår i den [kostnadsfritt Azure-konto](https://azure.microsoft.com/free), som erbjuder Azure-kredit och resurser utan kostnad för en viss tidsperiod. Mer specifikt för Azure Cosmos DB erbjuder det här kostnadsfria kontot 5 GB lagringsutrymme och 400 ru: er för etablerat dataflöde för hela året. Detta gör det möjligt för utvecklare att enkelt testa funktionerna i Azure Cosmos DB eller integrera det med andra Azure-tjänster utan kostnad. Med kostnadsfria Azure-konto får du en kredit på 200 USD att spendera under de första 30 dagarna. Du debiteras inte, även om du börjar använda tjänsterna förrän du väljer att uppgradera. Kom igång genom att gå till [kostnadsfritt Azure-konto](https://azure.microsoft.com/free) sidan.

## <a name="next-steps"></a>Nästa steg

Du kan komma igång med hjälp av emulatorn eller de kostnadsfria Azure Cosmos DB-konton med följande artiklar:

* Läs mer om [Optimera för utveckling och testning](optimize-dev-test.md)
* Läs mer om [förstå din faktura för Azure Cosmos DB](understand-your-bill.md)
* Läs mer om [optimera dataflödet kostnad](optimize-cost-throughput.md)
* Läs mer om [optimera kostnaden för lagring](optimize-cost-storage.md)
* Läs mer om [optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [optimera kostnaden för Azure Cosmos-konton för flera regioner](optimize-cost-regions.md)

