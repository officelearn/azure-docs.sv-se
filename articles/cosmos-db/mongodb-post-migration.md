---
title: Uppgifter efter migrering optimering steg när du använder Azure Cosmos DB API för MongoDB
description: Det här dokumentet ger efter migrering optimeringstekniker från MongoDB till Azure Cosmos DB APi för Mongo DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331217"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Uppgifter efter migrering optimering steg när du använder Azure Cosmos DB API för MongoDB 

När du har migrerat data som lagras i MongoDB-databas till Azure Cosmos DB-API för MongoDB kan du ansluta till Azure Cosmos DB och hantera data. Den här guiden innehåller de steg som du bör tänka på efter migreringen. Se den [migrera MongoDB till Azure Cosmos DB-API för MongoDB-självstudien](../dms/tutorial-mongodb-cosmos-db.md) för stegen i migreringen.

I den här guiden får du lära dig hur du:
- [Anslut ditt program](#connect-account)
- [Optimera indexprincip](#indexing)
- [Konfigurera global distribution för Azure Cosmos DB API för MongoDB](#distribute-data)
- [Set-konsekvensnivå](#consistency)

> [!NOTE]
> Endast obligatoriska efter migrering steget på din programnivå ändras anslutningssträngen i ditt program så att den pekar till ett nytt Azure Cosmos DB-konto. Alla andra migreringssteg rekommenderas optimeringar.
>

## <a id="connect-account"></a>Anslut ditt program 

1. I ett nytt fönster-inloggningsnamn i den [Azure-portalen](https://www.portal.azure.com/)
2. Från den [Azure-portalen](https://www.portal.azure.com/), i den vänstra rutan Öppna den **alla resurser** menyn och Sök efter Azure Cosmos DB-kontot som du har migrerat dina data.
3. Öppna den **Connection String** bladet. Den högra rutan innehåller all information som du behöver för att ansluta till ditt konto.
4. Använd informationen i programmets konfiguration (eller andra relevanta platser) för att återspegla Azure Cosmos DB: s API för MongoDB-anslutningen i din app. 
![Anslutningssträng](./media/mongodb-post-migration/connection-string.png)

Mer information finns i den [Anslut en MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md) sidan.

## <a id="indexing"></a>Optimera indexprincip

Alla datafält indexeras automatiskt som standard under migrering av data till Azure Cosmos DB. I många fall är det här standardvärdet indexeringspolicy godkända. I allmänhet tar bort index optimerar skrivbegäranden och med standard indexeringspolicy (d.v.s. automatisk indexering) optimerar läsbegäranden.

Läs mer om indexering, [Data indexering i Azure Cosmos DB API för MongoDB](mongodb-indexing.md) samt de [indexering i Azure Cosmos DB](index-overview.md) artiklar.

## <a id="distribute-data"></a>Distribuera dina data globalt

Azure Cosmos DB är tillgängligt i alla [Azure-regioner](https://azure.microsoft.com/regions/#services) över hela världen. När du har valt standardkonsekvensnivå för ditt Azure Cosmos DB-konto, kan du associera en eller flera Azure-regioner (beroende på dina globala distributionsbehov). För hög tillgänglighet och affärskontinuitet rekommenderar vi alltid körs i minst 2 regioner. Du kan granska tips för [optimera kostnaden för distributioner över flera regioner i Azure Cosmos DB](optimize-cost-regions.md).

Om du vill distribuera dina data globalt finns [distribuera data globalt på Azure Cosmos DB API för MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Set-konsekvensnivå
Azure Cosmos DB erbjuder väldefinierade 5 [konsekvensnivåer](consistency-levels.md). Mer information om mappningen mellan MongoDB och Azure Cosmos DB-konsekvensnivåer, läsa [konsekvensnivåer och Azure Cosmos DB API: er](consistency-levels-across-apis.md). Standard-konsekvensnivå är konsekvensnivå session. Om du byter konsekvens är valfri och du kan optimera den för din app. Så här ändrar konsekvensnivå med hjälp av Azure portal:

1. Gå till den **Standardkonsekvens** bladet under inställningar.
2. Välj din [konsekvensnivå](consistency-levels.md)

De flesta användare lämnar sina konsekvensnivå på standardinställningen för konsekvens i sessionen. Det finns dock [kompromisser för tillgänglighet och prestanda för olika konsekvensnivåer](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Nästa steg

* [Ansluta ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md)
* [Anslut till Azure Cosmos DB-konto använda Studio 3T](mongodb-mongochef.md)
* [Hur du distribuerar globalt läser via Azure Cosmos DB API för MongoDB](mongodb-readpreference.md)
* [Ta bort data med Azure Cosmos DB API för MongoDB](mongodb-time-to-live.md)
* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Indexering i Azure Cosmos DB](index-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)





