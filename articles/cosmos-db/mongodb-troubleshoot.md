---
title: Felsöka vanliga fel i Azure Cosmos DB:s API för Mongo DB
description: I det här dokumentet beskrivs olika sätt att felsöka vanliga problem som uppstått i Azure Cosmos DB:s API för MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941850"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Felsöka vanliga problem i Azure Cosmos DB:s API för MongoDB

Azure Cosmos DB implementerar trådprotokollen för vanliga NoSQL-databaser, inklusive MongoDB. På grund av implementeringen av trådprotokoll kan du interagera transparent med Azure Cosmos DB med hjälp av befintliga klient-SDK:er, drivrutiner och verktyg som fungerar med NoSQL-databaser. Azure Cosmos DB använder ingen källkod för databaserna för att tillhandahålla trådkompatibla API:er för någon av NoSQL-databaserna. Alla MongoDB-klientdrivrutiner som förstår kabelprotokollversionerna kan ansluta till Azure Cosmos DB.

Medan Azure Cosmos DB:s API för MongoDB är kompatibelt med 3.2-versionen av MongoDB:s kabelprotokoll (frågeoperanterna och funktionerna som läggs till i version 3.4 är för närvarande tillgängliga som en förhandsversion), finns det några anpassade felkoder som motsvarar Azure Cosmos DB specifika fel. I den här artikeln beskrivs olika fel, felkoder och stegen för att lösa dessa fel.

## <a name="common-errors-and-solutions"></a>Vanliga fel och lösningar

| Fel               | Kod  | Beskrivning  | Lösning  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Det totala antalet använda begärandeenheter är mer än den etablerade begäranhetsgraden för samlingen och har begränsats. | Överväg att skala dataflödet som tilldelats en behållare eller en uppsättning behållare från Azure-portalen eller så kan du försöka igen åtgärden. |
| ExceededMemoryLimit | 16501 | Som en tjänst med flera innehavare har åtgärden gått igenom klientens minnestilldelning. | Minska åtgärdens omfattning genom mer restriktiva frågevillkor eller kontaktstöd från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exempel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Indexsökvägen som motsvarar den angivna order-för-artikeln är utesluten / Ordningen efter fråga har inte ett motsvarande sammansatt index som den kan betjänas från. | 2 | Frågan begär en sortering i ett fält som inte är indexerat. | Skapa ett matchande index (eller kompositindex) för sorteringsfrågan som försökers. |
| Problem med MongoDB-trådversion | - | De äldre versionerna av MongoDB-drivrutiner kan inte identifiera Azure Cosmos-kontots namn i anslutningssträngarna. | Lägg till *appName=@**accountName** @ * i slutet av cosmos DB:s API för MongoDB-anslutningssträng, där ***accountName*** är ditt Cosmos DB-kontonamn. |


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

