---
title: Felsöka vanliga fel i Azure Cosmos DB API för Mongo DB
description: Det här dokumentet beskrivs hur du felsöker vanliga problem som uppstod i Azure Cosmos DB API för MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735713"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Felsöka vanliga problem i Azure Cosmos DB API för MongoDB

Azure Cosmos DB implementerar protokollen under överföring med vanliga NoSQL-databaser, inklusive MongoDB. På grund av protokollimplementering under överföring kan du transparent interagera med Azure Cosmos DB med hjälp av den befintliga klienten SDK: er, drivrutiner och verktyg som fungerar med NoSQL-databaser. Azure Cosmos DB använder inte någon källkoden för databaserna för att tillhandahålla wire-kompatibla API: er för alla NoSQL-databaser. Alla MongoDB-klientdrivrutinen som förstår protokollversioner under överföring kan ansluta till Azure Cosmos DB.

Azure Cosmos DB-API för MongoDB är kompatibel med 3,2 versionen av den MongoDB-protokollet (frågeoperatorer och funktioner i version 3.4 är för närvarande finns som förhandsversion), finns men det vissa anpassade felkoder för Azure Cosmos DB specifika fel. Den här artikeln beskrivs olika fel, felkoder och stegen för att åtgärda dessa fel.

## <a name="common-errors-and-solutions"></a>Vanliga fel och lösningar

| Fel               | Kod  | Beskrivning  | Lösning  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Det totala antalet enheter för programbegäran som används är högre än den etablerade begäransenhet för samlingen och har begränsats. | Överväg att skala dataflöde som tilldelats till en behållare eller en uppsättning behållare från Azure-portalen eller du kan försöka igen. |
| ExceededMemoryLimit | 16501 | Åtgärden har gått över klientens minne mängd som en tjänst med flera klienter. | Minska omfånget för åtgärd via mer restriktiva frågevillkor eller kontakta support från den [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exempel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Problem med MongoDB wire-version | - | Äldre versioner av MongoDB drivrutiner kan inte identifiera Azure Cosmos-kontots namn i anslutningssträngar. | Lägg till *appName = @**accountName** @*  i slutet av din Cosmos DB: s API för MongoDB-anslutningssträngen, där ***accountName*** är namnet på ditt Cosmos DB . |


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

