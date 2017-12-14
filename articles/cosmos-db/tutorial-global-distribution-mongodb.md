---
title: "Azure DB Cosmos global distributionsplatsen självstudier för MongoDB API | Microsoft Docs"
description: "Lär dig hur du ställer in Azure Cosmos DB global distributionsplatsen med hjälp av MongoDB-API."
services: cosmos-db
keywords: Global distributionsplatsen, MongoDB
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: d051c648ac66a42cefe0113d2571fe0a3050a237
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>Hur du konfigurerar Azure Cosmos DB global distributionsplatsen med hjälp av MongoDB-API

I den här artikeln visar vi hur du använder Azure-portalen för att konfigurera Azure Cosmos DB global distributionsplatsen och ansluter sedan med hjälp av MongoDB-API.

Den här artikeln omfattar följande aktiviteter: 

> [!div class="checklist"]
> * Konfigurera distributionslistor med Azure-portalen
> * Konfigurera distributionslistor med hjälp av den [MongoDB-API](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>Verifiera dina nationella inställningar med hjälp av MongoDB-API
Det enklaste sättet för double globala konfigurationen inom API kontrolleras för MongoDB är att köra den *isMaster()* från Mongo-gränssnittet.

Från din Mongo Shell:

   ```
      db.isMaster()
   ```
   
Exempel resultat:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>Ansluter till en önskad region med MongoDB-API

MongoDB-API kan du ange din samling Läs inställningar för ett globalt distribuerad databas. För både låg latens läsningar och globala hög tillgänglighet rekommenderar vi att ställa in din samling skrivskyddade inställningar till *närmaste*. Läs inställningen för *närmaste* har konfigurerats för att läsa från den närmaste regionen.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

För program med en primär läsning/skrivning och en sekundär region för katastrofåterställning (DR) scenarier, rekommenderar vi att ställa in din samling skrivskyddade inställningar till *sekundära önskad*. Läs inställningen för *sekundära önskade* har konfigurerats för att läsa från den sekundära regionen när den primära regionen är inte tillgänglig.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Slutligen, om du vill att manuellt ange din skrivskyddade regioner. Du kan ange region taggen inom önskemål skrivskyddade.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Det är den som Slutför den här kursen. Du kan lära dig hur du hanterar konsekvensen för globalt replikerade kontot genom att läsa [konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Och för mer information om hur globala databasreplikering fungerar i Azure Cosmos DB, se [distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Konfigurera distributionslistor med Azure-portalen
> * Konfigurera distributionslistor med SQL-API: er

Du kan nu fortsätta till nästa kurs att lära dig hur du utvecklar lokalt med hjälp av lokala Azure DB som Cosmos-emulatorn.

> [!div class="nextstepaction"]
> [Utveckla lokalt med emulatorn](local-emulator.md)
