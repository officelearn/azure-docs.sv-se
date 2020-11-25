---
title: Felsök vanliga fel i Azure Cosmos DB s API för mongo DB
description: Det här dokumentet innehåller information om hur du felsöker vanliga problem som uppstått i Azure Cosmos DB s API för MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 9d76c3d9943300f88a146e82b862624d491cf546
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017822"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Felsök vanliga problem i Azure Cosmos DBs API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

I följande artikel beskrivs vanliga fel och lösningar för databaser med hjälp av Azure Cosmos DB API för MongoDB.

>[!Note]
> Azure Cosmos DB är inte värd för MongoDB-motorn. Den innehåller en implementering av MongoDB [Wire Protocol version 3,6](mongodb-feature-support-36.md) och Legacy-stöd för [wire Protocol version 3,2](mongodb-feature-support.md), och därför finns vissa av dessa fel bara i Azure Cosmos DB s API för MongoDB. 

## <a name="common-errors-and-solutions"></a>Vanliga fel och lösningar

| Fel               | Kod  | Description  | Lösning  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | Förfrågan har överskridit tidsgränsen på 60 sekunder. | Det kan finnas många orsaker till det här felet. En av orsakerna är när den allokerade förfrågningskapaciteten inte räcker för att slutföra förfrågan. Det här kan du lösa genom att öka antalet förfrågningsenheter för samlingen eller databasen. I andra fall kan det här felet hanteras – genom att dela upp en stor begäran i mindre. |
| TooManyRequests     | 16500 | Det totala antalet förbrukade förfrågansenheter överskrider samlingens etablerade enhetsfrekvens och en begränsning har införts. | Överväg att skala upp det tilldelade dataflödet för en container eller en uppsättning containrar i Azure-portalen, eller försök utföra åtgärden igen. |
| ExceededMemoryLimit | 16501 | Som en tjänst för flera innehavare har åtgärden gått över klientens minnes tilldelning. | Minska åtgärds området genom mer restriktiva frågevillkor eller kontakta supporten från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exempel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Indexsökvägen som motsvarar den angivna order by-posten finns inte med, eller så har inte order by-frågan ett motsvarande sammansatt index att hämta information från. | 2 | Frågan begär en sortering av ett fält som inte är indexerat. | Skapa ett matchande index (eller sammansatt index) för sorterings frågan. |
| Problem med MongoDB-trådversion | - | De äldre versionerna av MongoDB-drivrutinerna kan inte identifiera Azure Cosmos-kontots namn i anslutnings strängarna. | Lägg till *APPNAME = @**accountName** @* i slutet av din Cosmos DBS API för MongoDB-anslutningssträng, där ***accountName*** är ditt Cosmos DB konto namn. |

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

