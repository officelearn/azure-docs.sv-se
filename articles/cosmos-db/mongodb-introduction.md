---
title: 'Introduktion till Azure Cosmos DB: MongoDB-API'
description: Lär dig hur du kan använda Azure Cosmos DB för att lagra och ställa frågor till omfattande volymer av JSON-dokument med kort svarstid med de populära API:erna för OSS MongoDB.
keywords: vad är MongoDB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: overview
ms.date: 02/12/2018
ms.author: sclyon
ms.openlocfilehash: 0d36d4c18860f6448d98d9d67b854d91b07ea9d2
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409096"
---
# <a name="introduction-to-azure-cosmos-db-mongodb-api"></a>Introduktion till Azure Cosmos DB: MongoDB-API

[Azure Cosmos DB](../cosmos-db/introduction.md) är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Azure Cosmos DB erbjuder [nyckelfärdig global distribution](distribute-data-globally.md), [elastisk skalning av dataflöde och lagring](partition-data.md) världen över, ensiffrig svarstid som den 99:e percentilen, och garanterat hög tillgänglighet, och allt understöds av [branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexerar alla data automatiskt](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) utan att du behöver bry dig om schema- eller indexhantering. Det stöder flera modeller och dokument, nyckelvärde graf och kolumndatamodeller. 

![Azure Cosmos DB: MongoDB-API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Azure DB Cosmos-databaser kan användas som datalager för appar som skrivits för [MongoDB](https://docs.mongodb.com/manual/introduction/). Funktionen innebär att ditt program nu kan kommunicera med Azure Cosmos DB och använda Azure Cosmos DB-databaser istället för MongoDB-databaser genom att använda befintliga [drivrutiner](https://docs.mongodb.org/ecosystem/drivers/) för MongoDB-databaser. I många fall kan du växla från att använda MongoDB till DocumentDB genom att bara ändra en anslutningssträng. Med den här funktionen kan du enkelt skapa och köra MongoDB:s globalt distribuerade databasprogram i Azure-moln med Azure Cosmos DB och dess [omfattande branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db), medan du använder bekanta kunskaper och verktyg för MongoDB.

**MongoDB-kompatibilitet**: Du kan använda dina befintliga MongoDB-kunskaper, programkod och verktyg eftersom Azure Cosmos DB implementerar MongoDB-kabelprotokollet. Du kan utveckla program som använder MongoDB och distribuera dem till produktionsmiljön med helt den hanterade och globalt distribuerade Azure Cosmos DB-tjänsten. Mer information om versioner som stöds finns i [Protokollstöd för MongoDB](mongodb-feature-support.md#mongodb-protocol-support).

MongoDB API i Azure Cosmos DB kan inte användas som en direkt slutpunkt för tjänster som Azure Stream Analytics, eftersom MongoDB API använder samma [klientdrivrutiner](https://docs.mongodb.org/ecosystem/drivers/) som den interna MongoDB:n. Om du vill integrera med Azure Stream Analytics kan du överväga att använda [Azure App Service](../app-service/app-service-web-overview.md) eller [Azure Functions-tjänsten](../azure-functions/functions-overview.md) som ett mellanprogram som kan skriva data till MongoDB API i Azure Cosmos DB.

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Vad är fördelen med att använda Azure Cosmos DB för MongoDB-program?

**Elastiska och skalbara dataflöden och lagringsutrymmen:** Skala enkelt din MongoDB-databas upp eller ned så att den passar dina programbehov. Dina data lagras på SSD-diskar (Solid State Disk) för korta och förutsägbara svarstider. Azure Cosmos DB stöder MongoDB-samlingar som kan skalas till praktiskt taget obegränsade lagringsstorlekar och etablerade dataflöden. Du kan skala Azure Cosmos DB elastiskt och smidigt med förutsägbara prestanda allteftersom programmet växer. 

**Replikering mellan flera regioner:** Azure Cosmos DB replikerar data transparent till alla regioner som du har associerat med ditt Mongo DB-konto, så att du kan utveckla program som kräver global åtkomst till data med rätt balans mellan konsekvens, tillgänglighet och prestanda – allt med motsvarande garantier. Azure Cosmos DB tillhandahåller transparent regional redundans med flera API:er, och möjligheten att elastiskt skala dataflöde och lagring i hela världen. Läs mer i [Distribuera data globalt](distribute-data-globally.md).

**Ingen serverhantering**: Du behöver inte hantera och skala dina MongoDB-databaser. Azure Cosmos-DB är en helt hanterad tjänst, vilket innebär att du inte behöver hantera någon infrastruktur eller de virtuella datorerna själv. Azure Cosmos DB finns tillgängligt i över 30 [Azure-regioner](https://azure.microsoft.com/regions/services/).

**Justerbara konsekvensnivåer:** Eftersom Azure Cosmos DB har stöd för API:er med flera modeller kan konsekvensinställningarna tillämpas på kontonivå och styras av varje API. Tills MongoDB 3.6 existerade inte begreppet sessionskonsekvens, så om du anger ett MongoDB API-konto för att använda sessionskonsekvens nedgraderas konsekvenskontrollen till eventuell när du använder MongoDB APIs. Om du behöver en garanti för läsa-egen-kod för ett MongoDB API-konto måste standardkonsekvensnivån för kontot vara stark eller begränsad föråldring. Läs mer om hur du [maximerar tillgänglighet och prestanda med hjälp av konsekvensnivåer](consistency-levels.md).

| Se konsekvensnivåer i Azure Cosmos DB |   Mongo API (3.4) |
|---|---|
|Eventuell| Eventuell |
|Konsekvent prefix| Eventuell med konsekvent ordning |
|Session| Eventuell med konsekvent ordning |
|Begränsad föråldring| Stark |
| Stark | Stark |

**Automatisk indexering**: Som standard indexerar Azure Cosmos DB automatiskt alla egenskaper inom dokument i MongoDB-databasen och varken förväntar sig eller kräver något schema eller att sekundära index skapas. Dessutom inför funktionen för unikt index ett unikhetskrav på alla dokumentfält som redan indexeras automatiskt i Azure Cosmos DB.

**Enterprise-klass**: Azure Cosmos DB stöder flera lokala repliker för att leverera 99,99 % tillgänglighet och dataskydd i händelse av lokala och regionala fel. Azure Cosmos-DB har [kompatibilitetscertifieringar](https://www.microsoft.com/trustcenter) och säkerhetsfunktioner för enterprise-klass. 

## <a name="how-to-get-started"></a>Så här kommer du igång

Följ MongoDB-Snabbstart för att skapa ett Azure Cosmos DB-konto och migrera ditt befintliga MongoDB-program för att använda Azure Cosmos DB eller skapa en ny:

* [Migrera en befintlig Node.js MongoDB-webbapp](create-mongodb-nodejs.md).
* [Skapa en MongoDB-API-webbapp med .NET och Azure Portal](create-mongodb-dotnet.md)
* [Skapa en MongoDB-API-webbapp med .NET och Azure Portal](create-mongodb-java.md)

## <a name="next-steps"></a>Nästa steg

Information om Azure Cosmos DB MongoDB API är integrerad i den övergripande dokumentationen för Azure Cosmos DB, men här följer några tips för att komma igång:

* Följ kursen [Anslut till ett konto med MongoDB](connect-mongodb-account.md) för att lära dig hur du hämtar information om anslutningssträngar för ditt konto.
* Följ kursen [Använd Studio 3T (MongoChef) med Azure Cosmos DB](mongodb-mongochef.md) och lär dig hur du skapar en anslutning mellan Azure Cosmos-DB-databasen och MongoDB-appen i Studio 3T.
* Följ guiden [Migrera data till Azure Cosmos DB med protokollstöd för MongoDB](mongodb-migrate.md) för att importera data till en API för en MongoDB-databas.
* Ansluta till en API för MongoDB-kontot med [Robomongo](mongodb-robomongo.md).
* Lär dig hur du [konfigurerar läsinställningar för globalt distribuerade appar](../cosmos-db/tutorial-global-distribution-mongodb.md).
