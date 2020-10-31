---
title: Välja mellan ett insamlat data flöde och Server lös Azure Cosmos DB
description: Lär dig mer om hur du väljer mellan allokerat data flöde och Server lös för din arbets belastning.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: d16343864d9602d644b31d34a2b66e39211b6ece
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079347"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Välja mellan ett allokerat data flöde och Server lös
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB finns i två olika kapacitets lägen: [etablerade data flöden](set-throughput.md) och [Server](serverless.md)lös. Du kan utföra exakt samma databas åtgärder i båda lägena, men det sätt som du debiteras för dessa åtgärder är radikalt annorlunda. I följande videoklipp förklaras de grundläggande skillnaderna mellan dessa lägen och hur de passar olika typer av arbets belastningar:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

> [!NOTE]
> Server lös stöds för närvarande endast i API: et för Azure Cosmos DB Core (SQL).

## <a name="detailed-comparison"></a>Detaljerad jämförelse

| Kriterie | Etablerat dataflöde | Utan server |
| --- | --- | --- |
| Status | Allmänt tillgänglig | I förhandsversion |
| Passar bäst för | Verksamhets kritiska arbets belastningar som kräver förutsägbara prestanda | Små till medel stora icke-kritiska arbets belastningar med låg trafik |
| Så här fungerar det | För var och en av dina behållare tillhandahåller du viss mängd data flöde som uttrycks i [enheter för programbegäran](request-units.md) per sekund. Varje sekund är den här mängden enheter för programbegäran tillgänglig för dina databas åtgärder. Det etablerade data flödet kan uppdateras manuellt eller justeras automatiskt med [autoskalning](provision-throughput-autoscale.md). | Du kör dina databas åtgärder mot dina behållare utan att behöva etablera någon kapacitet. |
| Begränsningar per konto | Maximalt antal Azure-regioner: obegränsade | Maximalt antal Azure-regioner: 1 |
| Begränsningar per behållare | Maximalt data flöde: obegränsat<br>Maximal lagring: obegränsad | Maximalt data flöde: 5 000 RU/s<br>Högsta lagrings utrymme: 50 GB |
| Prestanda | 99,99% till 99,999% tillgänglighet som omfattas av SLA<br>< 10 MS-svars tid för punkt läsningar och skrivningar som omfattas av SLA<br>99,99% garanterat data flöde som omfattas av SLA | 99,9% till 99,99% tillgänglighet som omfattas av SLA<br>< 10 MS-svars tid för punkt läsningar och < 30 ms för skrivningar som omfattas av service nivå mål<br>95% burst-överföring som omfattas av service nivå mål |
| Faktureringsmodell | Faktureringen görs per timme för RU/s etablerad, oavsett hur många ru: er som för bruk ATS. | Faktureringen görs per timme för den mängd ru: er som förbrukas av dina databas åtgärder. |

> [!IMPORTANT]
> Några av Server lösa begränsningar kan vara minskat eller tas bort när Server lös blir allmänt tillgängligt och **din feedback** hjälper oss att avgöra! Kontakta oss och berätta mer om din server lös upplevelse: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="burstability-and-expected-consumption"></a>Burst och förväntad förbrukning

I vissa situationer kan det vara oklart om det etablerade data flödet eller Server lösa ska väljas för en viss arbets belastning. För att få hjälp med det här beslutet kan du uppskatta följande:

- Arbets Belastningens **krav på** begränsning, det vill säga hur många ru: er du kan behöva använda på en sekund
- Den övergripande **förväntad förbrukning** , det vill säga det totala antalet ru: er som du kan använda under en månad (du kan beräkna detta med hjälp av tabellen som visas [här](plan-manage-costs.md#estimating-serverless-costs))

Om din arbets belastning kräver burst över 5 000 RU per sekund, ska det allokerade data flödet väljas eftersom Server behållare som inte kan överföras över den här gränsen. Om inte, kan du jämföra kostnaden för båda lägena baserat på din förväntade förbrukning.

**Exempel 1** : en arbets belastning förväntas överföras till maximalt 10 000 ru/s och förbruka totalt 20 000 000 ru: er under en månad.

- Endast tillhandahållet data flödes läge kan leverera ett data flöde på 10 000 RU/s

**Exempel 2** : en arbets belastning förväntas överföras till maximalt 500 ru/s och förbruka totalt 20 000 000 ru: er under en månad.

- I etablerat data flödes läge skulle du tillhandahålla en behållare med 500 RU/s för en månatlig kostnad av: $0,008 * 5 * 730 = **$29,20**
- I Server fritt läge betalar du för den förbrukade ru: er: $0,25 * 20 = **$5,00**

**Exempel 3** : en arbets belastning förväntas överföras till maximalt 500 ru/s och förbruka totalt 250 000 000 ru: er under en månad.

- I etablerat data flödes läge skulle du tillhandahålla en behållare med 500 RU/s för en månatlig kostnad av: $0,008 * 5 * 730 = **$29,20**
- I Server fritt läge betalar du för den förbrukade ru: er: $0,25 * 250 = **$62,50**

(de här exemplen redovisas inte för lagrings kostnaden, vilket är detsamma mellan de två lägena)

> [!NOTE]
> Kostnaderna som visas i föregående exempel är endast i demonstrations syfte. På [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) finns den senaste pris informationen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [konfigurerar data flödet på Azure Cosmos DB](set-throughput.md)
- Läs mer om [Azure Cosmos DB utan server](serverless.md)
- Bekanta dig med begreppet enheter för [programbegäran](request-units.md)
