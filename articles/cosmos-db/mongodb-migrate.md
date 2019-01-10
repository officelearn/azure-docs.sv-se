---
title: Migrera dina MongoDB-data till Azure Cosmos DB med hjälp av mongoimport och mongorestore
description: Du får lära dig att använda mongoimport och mongorestore för att importera data till Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 9226a49af67659ebd7bebd9beca397830ee808bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039063"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>Migrera dina MongoDB-data till Azure Cosmos DB

 Den här självstudien innehåller instruktioner om hur du migrerar data som lagras i MongoDB till Azure Cosmos DB som konfigurerats att använda Cosmos DB:s API för MongoDB. Om du importerar data från MongoDB som du vill använda med API:et för Azure Cosmos DB SQL bör du importera dem med hjälp av [datamigreringsverktyget](import-data.md).

I den här kursen ska du:

> [!div class="checklist"]
> * Förbereda en migreringsplan.
> * Migrera data med hjälp av mongoimport.
> * Migrera data med hjälp av mongorestore.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Granska och uppfyll följande förutsättningar innan du påbörjar migreringen.

### <a name="plan-for-the-migration"></a>Planera för migreringen

Det här avsnittet beskriver hur du planerar för datamigreringen. Vi beräknar RU-avgifter, fastställer svarstiden från din dator till molntjänsten samt beräknar batchstorleken och antalet infogningsarbetare.


#### <a name="pre-create-and-scale-your-collections"></a>Skapa samlingar i förväg och skala dem

Innan du migrerar med mongoimport eller mongorestore skapar du alla samlingar i förväg från [Azure-portalen](https://portal.azure.com) eller från MongoDB-drivrutiner och -verktyg. 

Från [Azure-portalen](https://portal.azure.com) ökar du samlingsdataflödet för migreringen. Med ett högre dataflöde kan du undvika hastighetsbegränsningar och migrera snabbare. Du kan minska dataflödet direkt efter migreringen för att spara kostnader.

Utöver att etablera dataflöde på samlingsnivå kan du även etablera dataflöde på databasnivå för en uppsättning samlingar för att dela det etablerade dataflödet. Du behöver skapa databasen och samlingarna i förväg samt definiera en shardnyckel för varje samling i databasen med delat dataflöde.

Du kan skapa fragmenterade (sharded) samlingar med hjälp av det verktyg, den drivrutin eller den SDK du väljer. I det här exemplet använder vi Mongo Shell för att skapa en fragmenterad samling:

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Kommandot returnerar följande resultat:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>Beräkna den ungefärliga RU-avgiften för en enskild dokumentskrivning

Från MongoDB-gränssnittet ansluter du till ditt Cosmos-konto som är konfigurerat för att använda Cosmos DB:s API för MongoDB. Anvisningar finns i avsnittet om att [ansluta ett MongoDB-program till Cosmos DB](connect-mongodb-account.md).

Sedan kör du ett insert-exempelkommando genom att använda något av dina exempeldokument:
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
Kör kommandot `db.runCommand({getLastRequestStatistics: 1})`.

Du får ett svar liknande följande utdata:
     
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
        
Notera kostnaden för begäran.
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>Fastställa svarstiden från din dator till Cosmos DB
    
Aktivera utförlig loggning från MongoDB-gränssnittet med kommandot `setVerboseShell(true)`.
    
Kör en grundläggande fråga mot databasen med kommandot `db.coll.find().limit(1)`.

Du får ett svar liknande följande utdata:

```bash
Fetched 1 record(s) in 100(ms)
```
        
Innan du kör migreringen tar du bort det infogade dokumentet för att säkerställa att det inte finns några dubblettdokument. Du kan ta bort dokument med kommandot `db.coll.remove({})`.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>Beräkna de ungefärliga värdena för egenskaperna batchSize och numInsertionWorkers

För egenskapen **batchSize** delar du det sammanlagda etablerade dataflödet (RU:er/sek) med de RU:er som används för en enskild dokumentskrivning enligt slutförandet i avsnittet ”Fastställa svarstiden från din dator till Cosmos DB”. Om det beräknade värdet är mindre än eller lika med 24 använder du det numret som egenskapsvärde. Om det beräknade värdet är större än 24 anger du egenskapsvärdet till 24.
    
För värdet för egenskapen **numInsertionWorkers** använder du den här ekvationen:

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

Vi kan använda följande värden för att beräkna ett värde för egenskapen **numInsertionWorkers**:

| Egenskap | Värde |
|--------|-----|
| **batchSize** | 24 |
| Etablerade RU:er | 10 000 |
| Svarstid | 0,100 s |
| Förbrukade RU:er | 10 RU:er |
| **numInsertionWorkers** | (10 000 RU:er x 0,100 s)/(24 x 10 RU:er) = **4,1666** |

Kör migreringskommandot **monogoimport**. Kommandoparametrarna beskrivs senare i den här artikeln.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

Du kan även kommandot **monogorestore**. Se till att alla samling har dataflödet inställt på minst det antal RU:er som användes i tidigare beräkningar.
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>Uppfylla förutsättningarna

När du har planerat för migrering slutför du följande steg: 

* **Hämta exempeldata**: Kontrollera att du har några exempeldata innan du påbörjar migreringen. 

* **Öka dataflöde**: Datamigreringens varaktighet beror på hur stort dataflöde du etablerar för en enskild samling eller databas. Du bör öka dataflödet för större datamigreringar. När du har slutfört migreringen kan du minska dataflödet för att sänka kostnaderna. 

* **Aktivera SSL**:  Cosmos DB har stränga säkerhetskrav och säkerhetsstandarder. Det är viktigt att du aktiverar SSL när du interagerar med ditt Cosmos-konto. Bland procedurerna i den här artikel ingår hur du aktiverar SSL för kommandona mongoimport och mongorestore.

* **Skapa Cosmos DB-resurser**: Innan du påbörjar migreringen skapar du alla dina samlingar i förväg från Azure-portalen. Om du migrerar till ett Cosmos-konto som har dataflöde på databasnivå etablerat ska du ange en partitionsnyckel när du skapar samlingarna.

* **Hämta anslutningssträngen**: I [Azure-portalen](https://portal.azure.com) väljer du posten **Azure Cosmos DB** till vänster. Under **Prenumerationer** väljer du ditt kontonamn. Under **Anslutningssträng** väljer du **Anslutningssträng**. Till höger i portalen visas den information du behöver för att ansluta till ditt konto:

    ![Information om anslutningssträng](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>Använda mongoimport

Använd följande mall för att importera data till ditt Cosmos-konto.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Ersätt parametrarna \<your_hostname > (värdnamn), \<your_username > (användarnamn) och \<your_password > (lösenord) med de specifika värdena för ditt konto. I följande exempel använder vi **sampleDB** som värde för \<your_database>, och **sampleColl** som värde för \<your_collection>:

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>Använda mongorestore

För att återställa data till ditt Cosmos-konto som konfigurerats med Cosmos DB:s API för MongoDB använder du följande mall och kör importen.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Ersätt parametrarna \<your_hostname > (värdnamn), \<your_username > (användarnamn) och \<your_password > (lösenord) med de specifika värdena för ditt konto. I följande exempel använder vi **./dumps/dump-2016-12-07** som värde för \<path_to_backup> (sökväg till säkerhetskopia):

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, Cosmos-kontot och alla relaterade resurser när de inte längre behövs. Ta bort resursgruppen med hjälp av följande steg:

1. Gå till den resursgrupp där du skapade Cosmos-kontot.
1. Välj **Ta bort resursgrupp**.
1. Bekräfta namnet på den resursgrupp som ska tas bort och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa självstudie och lär dig hur du kör frågor mot data med hjälp av Azure Cosmos DB:s API för MongoDB. 

> [!div class="nextstepaction"]
> [Så kör du frågor mot MongoDB-data](../cosmos-db/tutorial-query-mongodb.md)
