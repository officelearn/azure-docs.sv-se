---
title: Optimerings steg efter migreringen med Azure Cosmos DBs API för MongoDB
description: Det här dokumentet innehåller optimerings tekniker efter migrering från MongoDB till Azure Cosmos DB s APi för mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: lbosq
ms.openlocfilehash: 3a8da5df4c661a160c7ace37144f6ac1a9859da2
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942255"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Optimerings steg efter migreringen när du använder Azure Cosmos DBs API för MongoDB 

När du har migrerat data som lagras i MongoDB-databasen till Azure Cosmos DB s API för MongoDB, kan du ansluta till Azure Cosmos DB och hantera data. Den här guiden innehåller de steg du bör tänka på efter migreringen. Mer information om hur du [migrerar MongoDB finns i avsnittet migrera till Azure Cosmos DB s API för MongoDB](../dms/tutorial-mongodb-cosmos-db.md) .

I den här guiden får du lära dig hur du:
- [Anslut ditt program](#connect-account)
- [Optimera indexerings principen](#indexing)
- [Konfigurera global distribution för Azure Cosmos DB s API för MongoDB](#distribute-data)
- [Ange konsekvens nivå](#consistency)

> [!NOTE]
> Det enda obligatoriska steget efter migrering på program nivån är att ändra anslutnings strängen i programmet så att den pekar på ditt nya Azure Cosmos DB-konto. Alla andra steg i migreringen är rekommenderade optimeringar.
>

## <a id="connect-account"></a>Anslut ditt program 

1. Logga in på [Azure Portal](https://www.portal.azure.com/) i ett nytt fönster
2. Från [Azure Portal](https://www.portal.azure.com/)öppnar du menyn **alla resurser** i den vänstra rutan och letar upp det Azure Cosmos DB konto som du har migrerat dina data till.
3. Öppna bladet **anslutnings sträng** . Den högra rutan innehåller all information som du behöver för att ansluta till ditt konto.
4. Använd anslutnings informationen i programmets konfiguration (eller andra relevanta platser) för att återspegla Azure Cosmos DBens API för MongoDB-anslutning i din app. 
![anslutnings sträng](./media/mongodb-post-migration/connection-string.png)

Mer information finns på sidan [Anslut ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md) .

## <a id="indexing"></a>Optimera indexerings principen

Alla data fält indexeras automatiskt som standard under migreringen av data till Azure Cosmos DB. I många fall är den här standard indexerings principen acceptabel. I allmänhet optimerar borttagning av index Skriv förfrågningar och har standard indexerings principen (dvs. automatisk indexering) optimerar Läs begär Anden.

Mer information om indexering finns [i data indexering i Azure Cosmos DBS API för MongoDB](mongodb-indexing.md) samt [indexering i Azure Cosmos DB](index-overview.md) artiklar.

## <a id="distribute-data"></a>Distribuera dina data globalt

Azure Cosmos DB är tillgängligt i alla [Azure-regioner](https://azure.microsoft.com/regions/#services) i hela världen. När du har valt standard konsekvens nivå för ditt Azure Cosmos DB konto kan du associera en eller flera Azure-regioner (beroende på dina globala distributions behov). För hög tillgänglighet och affärs kontinuitet rekommenderar vi alltid att du kör i minst två regioner. Du kan läsa tipsen för att [optimera kostnaderna för distributioner i flera regioner i Azure Cosmos DB](optimize-cost-regions.md).

Om du vill distribuera dina data globalt kan du läsa [Distribuera data globalt på Azure Cosmos DBS API för MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Ange konsekvens nivå
Azure Cosmos DB erbjuder 5 väldefinierade [konsekvens nivåer](consistency-levels.md). Läs [konsekvens nivåer och Azure Cosmos DBS-API: er](consistency-levels-across-apis.md)för att läsa om mappningen mellan MongoDB och Azure Cosmos DB konsekvens nivåer. Standard konsekvens nivån är konsekvens nivån för sessionen. Att ändra konsekvens nivån är valfritt och du kan optimera den för din app. Ändra konsekvens nivå med Azure Portal:

1. Gå till bladet **standard konsekvens** under Inställningar.
2. Välj din [konsekvens nivå](consistency-levels.md)

De flesta användare lämnar sin konsekvens nivå i standardinställningen för konsekvens för sessioner. Det finns dock [tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Nästa steg

* [Ansluta ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md)
* [Anslut till Azure Cosmos DB konto med Studio 3T](mongodb-mongochef.md)
* [Distribuera läsningar globalt med Azure Cosmos DB s API för MongoDB](mongodb-readpreference.md)
* [Ta bort data med Azure Cosmos DB:s API för MongoDB](mongodb-time-to-live.md)
* [Konsekvens nivåer i Azure Cosmos DB](consistency-levels.md)
* [Indexering i Azure Cosmos DB](index-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)





