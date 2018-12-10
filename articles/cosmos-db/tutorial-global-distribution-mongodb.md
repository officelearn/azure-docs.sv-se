---
title: Självstudie om global distribution i Azure Cosmos DB för MongoDB-API
description: Lär dig hur du konfigurerar global distribution i Azure Cosmos DB med MongoDB-API:et.
services: cosmos-db
keywords: global distribution, MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 27fa9849c13de151f6922e829514cc8838f295ea
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874778"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>Konfigurera global distribution i Azure Cosmos DB med MongoDB-API:et

I den här artikeln visar vi hur du kan konfigurera global distribution i Azure Cosmos DB med Azure Portal och sedan ansluta med hjälp av MongoDB API.

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med [MongoDB API](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>Verifiera dina regionala inställningar med hjälp av MongoDB API
Det enklaste sättet att kontrollera den globala konfigurationen inom API för MongoDB är att köra kommandot *isMaster()* från Mongo-gränssnittet.

Från ditt Mongo-gränssnitt:

   ```
      db.isMaster()
   ```
   
Exempel på resultat:

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

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>Ansluta till en önskad region med hjälp av MongoDB API

Med MongoDB API kan du ange din samlings läsinställningar för en globalt distribuerad databas. För både läsningar med låg fördröjning och med globalt hög tillgänglighet rekommenderar vi att du ställer in din samlings läsinställningar på *närmaste*. Läsinställningen *närmaste* är konfigurerad att läsa från den närmaste regionen.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

För program med en primär läs-/skrivregion och en sekundär region för haveriberedskapsscenarier, rekommenderar vi att ange samlingens läsinställning till *sekundärt önskad*. Läsinställningen *sekundärt önskad* är konfigurerad att läsa från den sekundära regionen när den primära regionen inte är tillgänglig.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Slutligen kan du ange dina läsregioner manuellt, om du vill. Du kan ange regionstaggen i dina läsinställningar.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Och med detta är den här självstudiekursen klar. Mer information om hur du kan hantera ditt globalt replikerade kontos konsekvens finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Mer information om hur global databasreplikering fungerar i Azure Cosmos DB finns i [Distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med SQL-API:erna

Du kan nu fortsätta till nästa självstudiekurs och lära dig hur du utvecklar lokalt med hjälp av den lokala Azure Cosmos DB-emulatorn.

> [!div class="nextstepaction"]
> [Utveckla lokalt med emulatorn](local-emulator.md)
