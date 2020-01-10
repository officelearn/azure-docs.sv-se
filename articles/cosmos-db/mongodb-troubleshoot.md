---
title: Felsök vanliga fel i Azure Cosmos DB s API för mongo DB
description: Det här dokumentet innehåller information om hur du felsöker vanliga problem som uppstått i Azure Cosmos DB s API för MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 41d667f4e90114052a17c5707989634bbb5fc421
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719842"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Felsök vanliga problem i Azure Cosmos DBs API för MongoDB

Azure Cosmos DB implementerar överförings protokollen för vanliga NoSQL-databaser, inklusive MongoDB. På grund av implementeringen av överförings protokoll kan du transparent interagera med Azure Cosmos DB med hjälp av befintliga klient-SDK: er, driv rutiner och verktyg som fungerar med NoSQL-databaser. Azure Cosmos DB använder inte någon käll kod för databaserna för att tillhandahålla kabel-kompatibla API: er för någon av NoSQL-databaserna. Eventuella MongoDB-klientdatorer som förstår kabel protokoll versioner kan ansluta till Azure Cosmos DB.

Medan Azure Cosmos DBs API för MongoDB är kompatibelt med 3,2-versionen av MongoDBs kabel protokoll (de operatörer och funktioner som läggs till i version 3,4 är för närvarande tillgängliga som en för hands version), finns det vissa anpassade fel koder som motsvarar Azure Cosmos DB vissa fel. I den här artikeln beskrivs olika fel, felkoder och stegen för att lösa dessa fel.

## <a name="common-errors-and-solutions"></a>Vanliga fel och lösningar

| Fel               | Programmera  | Beskrivning  | Lösning  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Det totala antalet enheter för programbegäran som används är högre än den etablerade begäransenhet för samlingen och har begränsats. | Överväg att skala data flödet som är kopplat till en behållare eller en uppsättning behållare från Azure Portal eller så kan du försöka utföra åtgärden igen. |
| ExceededMemoryLimit | 16501 | Åtgärden har gått över klientens minne mängd som en tjänst med flera klienter. | Minska omfånget för åtgärd via mer restriktiva frågevillkor eller kontakta support från den [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exempel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Index Sök vägen som motsvarar den angivna order by-posten är exkluderad/order by-frågan har inget motsvarande sammansatt index som det kan hanteras från. | 2 | Frågan begär en sortering för ett fält som inte är indexerat. | Skapa ett matchande index (eller sammansatt index) för sorterings frågan. |
| MongoDB tråd versions problem | - | De äldre versionerna av MongoDB-drivrutinerna kan inte identifiera Azure Cosmos-kontots namn i anslutnings strängarna. | Lägg till *APPNAME = @**accountName**@* i slutet av din Cosmos DBS API för MongoDB-anslutningssträng, där ***accountName*** är ditt Cosmos DB konto namn. |


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

