---
title: Felsök vanliga fel i Azure Cosmos DB s API för mongo DB
description: Det här dokumentet innehåller information om hur du felsöker vanliga problem som uppstått i Azure Cosmos DB s API för MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: lbosq
ms.openlocfilehash: f75374fc88923a0f131d513bebf0ffe1feeca359
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076760"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Felsök vanliga problem i Azure Cosmos DBs API för MongoDB

I följande artikel beskrivs vanliga fel och lösningar för databaser med hjälp av Azure Cosmos DB API för MongoDB.

>[!Note]
> Azure Cosmos DB är inte värd för MongoDB-motorn. Den innehåller en implementering av MongoDB [Wire Protocol version 3,6](mongodb-feature-support-36.md) och Legacy-stöd för [wire Protocol version 3,2](mongodb-feature-support.md), och därför finns vissa av dessa fel bara i Azure Cosmos DB s API för MongoDB. 

## <a name="common-errors-and-solutions"></a>Vanliga fel och lösningar

| Fel               | Kod  | Beskrivning  | Lösning  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | Begäran har överskridit tids gränsen på 60 sekunders körning. | Det kan finnas många orsaker till det här felet. En av orsakerna är när den aktuella allokerade enheternas kapacitet inte räcker för att slutföra begäran. Detta kan lösas genom att öka enhets enheterna för samlingen eller databasen. I andra fall kan det här felet hanteras – genom att dela upp en stor begäran i mindre. |
| TooManyRequests     | 16500 | Det totala antalet förbrukade enheter för förbrukad enhet är fler än det etablerade enhets antalet för samlingen och har begränsats. | Överväg att skala data flödet som är kopplat till en behållare eller en uppsättning behållare från Azure Portal eller så kan du försöka utföra åtgärden igen. |
| ExceededMemoryLimit | 16501 | Som en tjänst för flera innehavare har åtgärden gått över klientens minnes tilldelning. | Minska åtgärds området genom mer restriktiva frågevillkor eller kontakta supporten från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exempel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Index Sök vägen som motsvarar den angivna order by-posten är exkluderad/order by-frågan har inget motsvarande sammansatt index som det kan hanteras från. | 2 | Frågan begär en sortering för ett fält som inte är indexerat. | Skapa ett matchande index (eller sammansatt index) för sorterings frågan. |
| Problem med MongoDB-trådversion | - | De äldre versionerna av MongoDB-drivrutinerna kan inte identifiera Azure Cosmos-kontots namn i anslutnings strängarna. | Lägg till *APPNAME = @**accountName** @ * i slutet av din Cosmos DBS API för MongoDB-anslutningssträng, där ***accountName*** är ditt Cosmos DB konto namn. |

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

