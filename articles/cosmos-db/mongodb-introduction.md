---
title: "Introduktion till Azure Cosmos DB: API för MongoDB | Microsoft Docs"
description: "Lär dig hur du kan använda Azure Cosmos DB för att lagra och fråga massiva mängder JSON-dokument med låg latens med populära OSS MongoDB APIs."
keywords: "Vad är MongoDB"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: anhoh
ms.openlocfilehash: 9acc3268bc6a043852cadfafa40c0a54ecc66e59
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2017
---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Introduktion till Azure Cosmos DB: API för MongoDB

[Azure Cosmos DB](../cosmos-db/introduction.md) är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Azure Cosmos DB erbjuder [nyckelfärdig global distribution](distribute-data-globally.md), [elastisk skalning av dataflöde och lagring](partition-data.md) världen över, ensiffrig svarstid som den 99:e percentilen, [fem väldefinierade konsekvensnivåer](consistency-levels.md) och garanterat hög tillgänglighet, och allt understöds av [branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexerar alla data automatiskt](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) utan att du behöver bry dig om schema- eller indexhantering. Det stöder flera modeller och dokument, nyckelvärde graf och kolumndatamodeller. 

![Azure Cosmos DB: MongoDB API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Azure DB Cosmos-databaser kan användas som datalager för appar som skrivits för [MongoDB](https://docs.mongodb.com/manual/introduction/). Den här funktionen innebär att med hjälp av befintliga [drivrutiner](https://docs.mongodb.org/ecosystem/drivers/), ditt program som skrivits för MongoDB nu kan kommunicera med Azure Cosmos DB och använda Azure Cosmos DB databaser i stället för MongoDB-databaser. I många fall kan du växla från att använda MongoDB till Azure Cosmos DB genom att ändra en anslutningssträng. Med den här funktionen kan du enkelt skapa och köra MongoDB databasprogram i Azure moln med Azure Cosmos DB global distributionsplatsen och [omfattande branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db), medan du använder bekant kvalifikationer och verktyg för MongoDB.

**MongoDB kompatibilitet**: du kan använda din befintliga MongoDB kunskaper programkod och verktygsuppsättning som Azure Cosmos DB implementerar protokollet MongoDB 3.4 (version 5) under överföring och stöder de [MongoDB aggregering pipeline](mongodb-feature-support.md#aggregation-pipeline). Du kan utveckla program som använder MongoDB och distribuera dem till produktionsmiljön med helt hanterad och globalt distribuerade Azure DB som Cosmos-tjänsten.

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Vad är fördelen med att använda Azure Cosmos DB för MongoDB program?

**Elastiska och skalbara dataflöden och lagring:** uppfyller dina program behöver genom att enkelt skala uppåt eller nedåt MongoDB-databas. Dina data lagras på SSD-diskar (SSD) för korta och förutsägbara svarstider. Azure Cosmos-DB stöder MongoDB samlingar som kan skalas till praktiskt taget obegränsade lagringsstorlekar och etablerat dataflöde. Du kan skala Azure Cosmos DB elastiskt och smidigt med förutsägbara prestanda allteftersom programmet växer. 

**Flera regioner replikering:** Azure Cosmos DB transparent replikerar data till alla områden som du har kopplat till ditt konto för MongoDB, så att du kan utveckla program som kräver global åtkomst till data samtidigt som man kompromisser mellan konsekvens, tillgänglighet och prestanda, alla med motsvarande garantier. Azure Cosmos DB tillhandahåller transparent regional redundans med flera API:er, och möjligheten att elastiskt skala dataflöde och lagring i hela världen. Läs mer i [distribuera data globalt](distribute-data-globally.md).

**Inga serverhantering**: du behöver hantera och skala MongoDB-databaser. Azure Cosmos-DB är en helt hanterad tjänst, vilket innebär att du inte behöver hantera någon infrastruktur och de virtuella datorerna själv. Azure Cosmos-DB är tillgänglig i 30 + [Azure-regioner](https://azure.microsoft.com/regions/services/).

**Justerbara konsekvensnivåer:** Välj fem väldefinierade konsekvensnivåer för bästa möjliga balans mellan konsekvens och prestanda. Azure Cosmos DB erbjuder fem olika konsekvensnivåer för frågor och läsåtgärder: stark, bunden utgång, session, enhetligt prefix och slutlig. Med de här detaljerade, väldefinierade konsekvensnivåerna kan du själv avgöra balansen mellan konsekvens, tillgänglighet och svarstid. Läs mer om hur du [maximerar tillgänglighet och prestanda med hjälp av konsekvensnivåer](consistency-levels.md).

**Automatisk indexering**: som standard, Azure Cosmos DB indexerar automatiskt alla egenskaper i dokument i din MongoDB-databas och inte förväntar sig eller kräver något schema eller att sekundärindex. Kapacitet för unikt index möjliggör dessutom en unikhetsbegränsningen på dokumentfält som redan är auto-indexerat i Azure Cosmos-databasen.

**Enterprise-klass**: Azure Cosmos DB stöder flera lokala repliker för att leverera 99,99% tillgänglighet och dataskydd i händelse av lokala och regionala fel. Azure Cosmos-DB har enterprise-klass [kompatibilitet certifieringar](https://www.microsoft.com/trustcenter) och säkerhetsfunktioner. 

Läs mer i den här videon med Azure Cosmos DB programchef, Aleksey Savateyev.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T136/player]
> 

## <a name="how-to-get-started"></a>Så här kommer du igång

Följ MongoDB-Snabbstart för att skapa ett Azure DB som Cosmos-konto och migrera dina befintliga MongoDB-programmet att använda Azure Cosmos DB eller skapa en ny:

* [Migrera en befintlig MongoDB för Node.js-webbapp](create-mongodb-nodejs.md).
* [Skapa en MongoDB API webbprogram med .NET och Azure portal](create-mongodb-dotnet.md)
* [Skapa en MongoDB-API-konsolapp med Java och Azure portal](create-mongodb-java.md)

## <a name="next-steps"></a>Nästa steg

Information om Azure Cosmos DB MongoDB API är integrerat i den övergripande dokumentationen för Azure Cosmos DB, men här följer några tips för att komma igång:

* Följ den [Anslut till ett konto med MongoDB](connect-mongodb-account.md) kursen för att lära dig hur du hämtar information om anslutningssträngar ditt konto.
* Följ den [MongoChef för användning med Azure Cosmos DB](mongodb-mongochef.md) kursen lär dig hur du skapar en anslutning mellan Azure Cosmos-DB-databas och MongoDB-app i MongoChef.
* Följ den [migrera data till Azure Cosmos DB med protokollet stöd för MongoDB](mongodb-migrate.md) självstudiekursen för att importera data till en API för MongoDB-databas.
* Ansluta till en API för MongoDB kontot med [Robomongo](mongodb-robomongo.md).
* Lär dig hur många RUs åtgärderna använder med den [GetLastRequestStatistics kommando och Azure portal mått](request-units.md#GetLastRequestStatistics).
* Lär dig hur du [konfigurera Läs inställningar för globalt distribuerade appar](../cosmos-db/tutorial-global-distribution-mongodb.md).
