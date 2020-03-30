---
title: Optimeringssteg efter migrering med Azure Cosmos DB:s API för MongoDB
description: Det här dokumentet tillhandahåller optimeringsteknikerna efter migrering från MongoDB till Azure Cosmos DB:s APi för Mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063626"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Optimeringssteg efter migrering när du använder Azure Cosmos DB:s API för MongoDB

När du har migrerat data som lagras i MongoDB-databasen till Azure Cosmos DB:s API för MongoDB kan du ansluta till Azure Cosmos DB och hantera data. Den här guiden innehåller de steg du bör tänka på efter migreringen. Se [självstudien Migrera MongoDB till Azure Cosmos DB:s API för MongoDB](../dms/tutorial-mongodb-cosmos-db.md) för migreringsstegen.

I den här guiden får du lära dig hur du:

- [Anslut ditt program](#connect-your-application)
- [Optimera indexeringsprincipen](#optimize-the-indexing-policy)
- [Konfigurera global distribution för Azure Cosmos DB:s API för MongoDB](#globally-distribute-your-data)
- [Ange konsekvensnivå](#set-consistency-level)

> [!NOTE]
> Det enda obligatoriska steget efter migreringen på programnivå är att ändra anslutningssträngen i ditt program så att det pekar på ditt nya Azure Cosmos DB-konto. Alla andra migreringssteg är rekommenderade optimeringar.
>

## <a name="connect-your-application"></a>Anslut ditt program

1. Logga in på [Azure-portalen](https://www.portal.azure.com/) i ett nytt fönster
2. Öppna menyn **Alla resurser** i [Azure-portalen](https://www.portal.azure.com/)i den vänstra rutan och hitta azure Cosmos DB-kontot som du har migrerat dina data till.
3. Öppna **bladet Anslutningssträng.** Den högra rutan innehåller all information som du behöver för att ansluta till ditt konto.
4. Använd anslutningsinformationen i programmets konfiguration (eller andra relevanta platser) för att återspegla Azure Cosmos DB:s API för MongoDB-anslutning i din app.
![Anslutningssträng](./media/mongodb-post-migration/connection-string.png)

Mer information finns på [sidan Anslut ett MongoDB-program till Azure Cosmos DB.](connect-mongodb-account.md)

## <a name="optimize-the-indexing-policy"></a>Optimera indexeringsprincipen

Alla datafält indexeras automatiskt, som standard, under migreringen av data till Azure Cosmos DB. I många fall är den här standardindexeringsprincipen godtagbar. Om du tar bort index optimeras skrivbegäranden och standardindexeringsprincipen (dvs. automatisk indexering) optimerar läsbegäranden.

Mer information om indexering finns [i Dataindexering i Azure Cosmos DB:s API för MongoDB](mongodb-indexing.md) samt [indexeringen i Azure Cosmos](index-overview.md) DB-artiklar.

## <a name="globally-distribute-your-data"></a>Distribuera dina data globalt

Azure Cosmos DB är tillgängligt i alla [Azure-regioner](https://azure.microsoft.com/regions/#services) över hela världen. När du har valt standardkonsekvensnivån för ditt Azure Cosmos DB-konto kan du associera en eller flera Azure-regioner (beroende på dina globala distributionsbehov). För hög tillgänglighet och affärskontinuitet rekommenderar vi alltid att köra i minst 2 regioner. Du kan granska tipsen för att optimera kostnaden för [distributioner med flera regioner i Azure Cosmos DB](optimize-cost-regions.md).

Information om hur du distribuerar dina data globalt finns [i Distribuera data globalt på Azure Cosmos DB:s API för MongoDB](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Ange konsekvensnivå

Azure Cosmos DB erbjuder 5 väldefinierade [konsekvensnivåer](consistency-levels.md). Om du vill läsa om mappningen mellan MongoDB och Azure Cosmos DB-konsekvensnivåer läser du [konsekvensnivåer och Azure Cosmos DB API:er](consistency-levels-across-apis.md). Standardkonsekvensnivån är sessionskonsekvensnivån. Det är valfritt att ändra konsekvensnivån och du kan optimera den för din app. Så här ändrar du konsekvensnivån med Azure-portalen:

1. Gå till **bladet Standardkonsekvens** under Inställningar.
2. Välj [din konsekvensnivå](consistency-levels.md)

De flesta användare lämnar sin konsekvensnivå vid standardinställningen för sessionskonsekvens. Det finns dock [tillgänglighets- och prestandaavvägningar för olika konsekvensnivåer.](consistency-levels-tradeoffs.md)

## <a name="next-steps"></a>Nästa steg

* [Ansluta ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md)
* [Ansluta till Azure Cosmos DB-konto med Studio 3T](mongodb-mongochef.md)
* [Så här distribuerar du läsningar globalt med Azure Cosmos DB:s API för MongoDB](mongodb-readpreference.md)
* [Ta bort data med Azure Cosmos DB:s API för MongoDB](mongodb-time-to-live.md)
* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Indexering i Azure Cosmos DB](index-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)