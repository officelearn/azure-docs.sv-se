---
title: Introduktion till Azure Cosmos DB:s API för MongoDB
description: Lär dig hur du kan använda Azure Cosmos DB för att lagra och fråga stora mängder data med hjälp av Azure Cosmos DB:s API för MongoDB.
keywords: API för Azure Cosmos DB för MongoDB
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: overview
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: f17d8f46bc14297c70c21919abe236fc544facfa
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53787621"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

[Azure Cosmos DB](introduction.md) är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Azure Cosmos DB erbjuder [nyckelfärdig global distribution](distribute-data-globally.md), [elastisk skalning av dataflöde och lagring](partition-data.md) världen över, ensiffrig svarstid som den 99:e percentilen, och garanterat hög tillgänglighet, och allt understöds av [branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexerar alla data automatiskt](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) utan att du behöver bry dig om schema- eller indexhantering. Det stöder flera modeller och dokument, nyckelvärde graf och kolumndatamodeller. Som standard kan du interagera med Cosmos DB med SQL-API. Dessutom implementerar Cosmos DB-tjänsten trådprotokoll för vanliga NoSQL-API:er som Cassandra, MongoDB, Gremlin och Azure Table Storage. På så sätt kan du använda välbekanta NoSQL-klientdrivrutiner och verktyg för att interagera med din Cosmos-databas.

## <a name="wire-protocol-compatibility"></a>Trådprotokollkompatibilitet

Azure Cosmos DB implementerar trådprotokoll för vanliga NoSQL-databaser som Cassandra, MongoDB, Gremlin och Azure Table Storage. Genom att tillhandahålla en ursprunglig implementering av trådprotokollen direkt och effektivt i Cosmos DB kan befintliga klient-SDK:er, drivrutiner och verktyg för NoSQL-databaser interagera transparent med Cosmos DB. Cosmos DB använder inte någon källkod för databaserna för att tillhandahålla trådkompatibla API:er för någon av NoSQL-databaserna.

Azure Cosmos DB:s API för MongoDB är som standard kompatibelt med version 3.2 av MongoDB:s trådprotokoll. Funktioner eller frågeoperatorer som lagts till i version 3.4 av trådprotokollet är för närvarande tillgängliga som en förhandsversionsfunktion. MongoDB-klientdrivrutiner som förstår dessa protokollversioner bör kunna ansluta internt till Cosmos DB.

![API för Azure Cosmos DB för MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

## <a name="key-benefits"></a>Viktiga fördelar

De främsta fördelarna med Cosmos DB som en fullständigt hanterad och globalt distribuerad databas som en tjänst beskrivs [här](introduction.md). Genom att implementera trådprotokoll internt för populära NoSQL-API:er tillhandahåller Cosmos DB dessutom följande fördelar:

* Migrera ditt program enkelt till Cosmos DB samtidigt som du bevarar betydande delar av din programlogik.
* Håll ditt program portabelt och förbli molnleverantörsoberoende.
* Skaffa branschledande, ekonomiskt uppbackade serviceavtal för vanliga Cosmos DB-baserade NoSQL-API:er.
* Skala det etablerade dataflödet och lagringen elastiskt för dina Cosmos-databaser utifrån dina behov och betala bara för det dataflöde och lagringsutrymme du behöver. Detta medför betydande kostnadsbesparingar.
* Nyckelfärdig global distribution med replikering med flera original.

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB:s API:er för MongoDB

Följ snabbstarterna när du ska skapa ett Cosmos-konto och migrera ditt befintliga MongoDB-program så att det kan använda Azure Cosmos DB eller skapa ett nytt:

* [Migrera en befintlig MongoDB Node.js-webbapp](create-mongodb-nodejs.md).
* [Skapa en webbapp med Azure Cosmos DB:s API för MongoDB och .NET SDK](create-mongodb-dotnet.md)
* [Skapa en konsolapp med Azure Cosmos DB:s API för MongoDB och Java SDK](create-mongodb-java.md)

## <a name="next-steps"></a>Nästa steg

Här följer några tips för att komma igång:

* Följ självstudien [Anslut ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md) om du vill lära dig hur du hämtar information om anslutningssträngar för ditt konto.
* Följ självstudiekursen [Använda Studio 3T med Azure Cosmos DB](mongodb-mongochef.md) om du vill lära dig hur du skapar en anslutning mellan din Cosmos-databas och MongoDB-appen i Studio 3T.
* Följ självstudiekursen [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md) om du vill lära dig hur du importerar dina data till en Cosmos-databas.
* Anslut till ett Cosmos-konto med [Robo 3T](mongodb-robomongo.md).
* Lär dig hur du [konfigurerar läsinställningar för globalt distribuerade appar](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Obs! I den här artikeln beskrivs en funktion i Azure Cosmos DB som tillhandahåller trådprotokollkompatibilitet med MongoDB-databaser. Microsoft kan inte köra MongoDB-databaser för att tillhandahålla den här tjänsten. Azure Cosmos DB är inte kopplat till MongoDB, Inc.</sup>
