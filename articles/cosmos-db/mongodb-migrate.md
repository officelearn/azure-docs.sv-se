---
title: Använd mongoimport och mongorestore med Azure Cosmos DB-API:et för MongoDB | Microsoft Docs
description: Lär dig hur du använder mongoimport och mongorestore för att importera data till ett API för MongoDB-konto
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 13422434e6392ec7681ec4478533c45a84f40c9a
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706984"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-mongodb-api-account"></a>Självstudie: Migrera data till Azure Cosmos DB MongoDB-API-konto

Den här självstudiekursen innehåller instruktioner om hur du migrerar data som lagras i MongoDB till Azure Cosmos DB MongoDB API-konto. Om du importerar data från MongoDB som du vill använda med Azure Cosmos DB SQL API bör du importera dem med hjälp av [datamigreringsverktyget](import-data.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Planera för migrering
> * Förutsättningar för migrering
> * Migrera data med mongoimport
> * Migrera data med mongorestore

Innan du migrera data till MongoDB API-konto ser du till att ha lite exempel på MongoDB-data. Om du inte har en MongoDB-exempeldatabas kan du ladda ned och installera [MongoDB-gruppservern](https://www.mongodb.com/download-center), skapa en exempeldatabas och använda mongoimport.exe eller mongorestore.exe till att ladda upp exempeldata. 

## <a name="plan-for-migration"></a>Planera för migrering

1. Skapa samlingar i förväg och skala dem:
        
   * Som standard etablerar Azure Cosmos DB en ny MongoDB-samling med 1 000 enheter för programbegäran per sekund (RU/s). Innan du påbörjar migreringen med mongoimport eller mongorestore skapar du alla samlingar i förväg från [Azure Portal](https://portal.azure.com) eller från MongoDB-drivrutiner och MongoDB-verktyg. Om datastorleken är större än 10 GB skapar du en [partitionerad samling](partition-data.md) med lämplig shard-nyckel. MongoDB rekommenderar att du lagrar entitetsdata i samlingar. Du kan samplacera enheterna med jämförbar storlek och etablera dataflöde på Azure Cosmos-databasnivå.

   * Öka samlingarnas dataflöde på [Azure-portalen](https://portal.azure.com) från 1000 RU/s för en enskild partitionssamling och 2 500 RU/s för en fragmenterad samling under migreringen. Med ett högre dataflöde kan du undvika begränsningar och migrera snabbare. Du kan minska dataflödet direkt efter migreringen för att spara kostnader.

   * Förutom att etablera RU/s på samlingsnivå kan du även etablera RU/s för en uppsättning samlingar på den överordnade databasnivån. Detta kräver att du skapar databasen och samlingarna i förväg, samt att du definierar en shard-nyckel för varje samling.

   * Du kan skapa fragmenterade (sharded) samlingar med valfritt verktyg, drivrutin eller SDK. I det här exemplet använder vi Mongo Shell för att skapa en fragmenterad samling:

        ```bash
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        Resultat:

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

1. Beräkna den ungefärliga RU-kostnaden för en enskild dokumentskrivning:

   a. Anslut till Azure Cosmos DB MongoDB-API-kontot från MongoDB Shell. Anvisningar finns i [Connect a MongoDB application to Azure Cosmos DB](connect-mongodb-account.md) (Ansluta ett MongoDB-program till Azure Cosmos DB).
    
   b. Kör ett insert-exempelkommando genom att använda något av dina exempeldokument från MongoDB Shell:
   
      ```bash
      db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
      ```
        
   c. Kör ```db.runCommand({getLastRequestStatistics: 1})```. Ett svar liknande följande returneras:
     
      ```bash
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
      ```
        
    d. Notera kostnaden för begäran.
    
1. Kontrollera svarstiden från din dator till Azure Cosmos DB-molntjänsten:
    
    a. Aktivera utförlig loggning från MongoDB Shell med hjälp av följande kommando: ```setVerboseShell(true)```
    
    b. Kör en enkel fråga mot databasen: ```db.coll.find().limit(1)```. Ett svar liknande följande returneras:

       ```bash
       Fetched 1 record(s) in 100(ms)
       ```
        
1. Ta bort det infogade dokumentet innan migreringen för att undvika dubblettdokument. Du kan ta bort dokument genom att köra följande kommando: ```db.coll.remove({})```

1. Beräkna de ungefärliga *batchSize*- och *numInsertionWorkers*-värdena:

    * För *batchSize* dividerar du det totala antalet etablerade RU:er med antalet förbrukade RU:er från dokumentskrivningen i steg 3.
    
    * Om den beräknade *batchSize* <= 24 använder du det värdet som ditt *batchSize*-värde.
    
    * Om den beräknade *batchSize* > 24 anger du *batchSize*-värdet till 24.
    
    * För *numInsertionWorkers* använder du följande formel: *numInsertionWorkers = (etablerat dataflöde * svarstid i sekunder) / (batchstorlek * förbrukade RU:er för en enskild skrivning)*.
        
    |Egenskap|Värde|
    |--------|-----|
    |batchSize| 24 |
    |Etablerade RU:er | 10000 |
    |Svarstid | 0,100 s |
    |Debiterade RU:er för 1 dokumentskrivning | 10 RU:er |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10 000 RU:er × 0,1 s) / (24 × 10 RU:er) = 4,1666*

1. Kör migreringskommandot. Alternativen att migrera data beskrivs i nästa avsnitt.

   ```bash
   mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   Eller med mongorestore (kontrollera att dataflödet för alla samlingar minst har angetts till antalet RU:er som användes i tidigare beräkningar):
   
   ```bash
   mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="prerequisites-for-migration"></a>Förutsättningar för migrering

* **Öka dataflödet**: Hur lång tid datamigreringen tar beror på hur stort dataflöde du anger för en enskild samling eller en uppsättning samlingar. Du bör öka dataflödet för större datamigreringar. När du har slutfört migreringen minskar du dataflödet för att spara kostnader. Mer information om hur du ökar dataflödet på [Azure Portal](https://portal.azure.com) finns i avsnittet om [prestandanivåer och prisnivåer](performance-levels.md) i Azure Cosmos DB.

* **Aktivera SSL:** Azure Cosmos DB har stränga säkerhetskrav och säkerhetsstandarder. Det är viktigt att du aktiverar SSL när du kommunicerar med ditt konto. Resten av den här artikeln innehåller anvisningar som beskriver hur du aktiverar SSL för mongoimport och mongorestore.

* **Skapa Azure Cosmos DB-resurser:** Innan du börjar migrera data skapar du alla dina samlingar i förväg från Azure-portalen. Om du migrerar till ett Azure Cosmos DB-konto som har dataflöde på databasnivå anger du en partitionsnyckel när du skapar Azure Cosmos DB-samlingar.

## <a name="get-your-connection-string"></a>Hämta anslutningssträngen 

1. Klicka på posten **Azure Cosmos DB** i det vänstra fönstret på [Azure Portal](https://portal.azure.com).
1. Välj namnet på ditt konto i fönstret **Prenumerationer**.
1. Klicka på **Anslutningssträng** på bladet **Anslutningssträng**.

   Den högra rutan innehåller all information som du behöver för att ansluta till ditt konto.

   ![Bladet Anslutningssträng](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="migrate-data-by-using-mongoimport"></a>Migrera data med mongoimport

Använd följande mall för att importera data till ditt Azure Cosmos DB-konto. Fyll i *värd*, *användarnamn* och *lösenord* med de värden som är specifika för ditt konto.  

Mall:

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Exempel:  

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="migrate-data-by-using-mongorestore"></a>Migrera data med mongorestore

Du kan återställa data till ditt API för MongoDB-konto genom att importera dem med hjälp av följande mall. Fyll i *värd*, *användarnamn* och *lösenord* med värdena för ditt konto.

Mall:

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Exempel:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="next-steps"></a>Nästa steg

Du kan fortsätta till nästa självstudiekurs där du lär dig hur du kör frågor mot MongoDB-data med hjälp av Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Hur kör jag frågor mot MongoDB-data?](../cosmos-db/tutorial-query-mongodb.md)
