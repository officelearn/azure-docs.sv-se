---
title: "Använd mongoimport och mongorestore med Azure Cosmos DB API för MongoDB | Microsoft Docs"
description: "Lär dig hur du använder mongoimport och mongorestore för att importera data till en API för MongoDB-konto"
keywords: mongoimport mongorestore
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 1555f13c3ea88b61be0ea240b51218b83f6f9724
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: Importera MongoDB-data 

Om du vill migrera data från MongoDB till ett Azure DB som Cosmos-konto för användning med API för MongoDB, måste du:

* Hämta antingen *mongoimport.exe* eller *mongorestore.exe* från den [MongoDB Download Center](https://www.mongodb.com/download-center).
* Hämta din [API: et för MongoDB-anslutningssträng](connect-mongodb-account.md).

Om du importerar data från MongoDB och planerar att använda med Azure Cosmos DB, bör du använda den [datamigreringsverktyget](import-data.md) att importera data.

Den här kursen ingår följande uppgifter:

> [!div class="checklist"]
> * Hämta din anslutningssträng
> * Importera MongoDB-data med hjälp av mongoimport
> * Importera MongoDB-data med hjälp av mongorestore

## <a name="prerequisites"></a>Krav

* Öka genomflödet: din datamigrering varaktighet beror på mängden genomströmning som du angett för samlingar. Se till att öka genomflödet för större migrering av data. När du har slutfört migreringen, minska dataflöde för att spara kostnader. Mer information om ökar genomströmningen i den [Azure-portalen](https://portal.azure.com), se [prestandanivåer och prisnivåerna i Azure Cosmos DB](performance-levels.md).

* Aktivera SSL: Azure Cosmos-DB har stränga säkerhetskrav och standarder. Se till att aktivera SSL när du kommunicerar med ditt konto. I resten av artikeln finns information om hur du aktiverar SSL för mongoimport och mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Hitta din Anslutningssträngsinformation (värd, port, användarnamn och lösenord)

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan klickar du på den **Azure Cosmos DB** post.
2. I den **prenumerationer** rutan, Välj namnet på ditt konto.
3. I den **anslutningssträngen** bladet, klickar du på **anslutningssträngen**.  
Den högra rutan innehåller all information som du behöver för att ansluta till ditt konto.

    ![Anslutningen sträng bladet](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Importera data till API: et för MongoDB med hjälp av mongoimport

Använd följande mall för att importera data till Azure DB som Cosmos-konto. Fyll i *värden*, *användarnamn*, och *lösenord* med värden som är specifika för ditt konto.  

Mall:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Exempel:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Importera data till API: et för MongoDB med hjälp av mongorestore

Om du vill återställa data till ditt API för MongoDB-konto, använder du följande mall för att utföra importen. Fyll i *värden*, *användarnamn*, och *lösenord* med värden som är specifika för ditt konto.

Mall:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Exempel:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Guide för en lyckad migrering

1. Skapa och skala dina samlingar:
        
    * Standard etablerar Azure Cosmos DB en ny MongoDB-samling med 1 000 frågeenheter (RUs). Innan du påbörjar migreringen genom att använda mongoimport, mongorestore eller mongomirror skapa alla samlingar från den [Azure-portalen](https://portal.azure.com) eller från MongoDB-drivrutiner och verktyg. Om samlingen är större än 10 GB, se till att skapa en [delat/partitionerad samling](partition-data.md) med en lämplig Fragmentera nyckel.

    * Från den [Azure-portalen](https://portal.azure.com), öka genomflödet av dina samlingar från 1 000 RUs för en enskild partition samling och 2 500 RUs för en delat samling för migreringen. Du kan undvika begränsning och migrera på kortare tid med högre genomströmning. Med varje timme fakturering i Azure Cosmos-databasen, kan du minska genomflödet omedelbart efter migrering för att spara kostnader.

2. Debiteringen ungefärliga RU för att skriva ett enskilt dokument:

    a. Ansluta till din Azure Cosmos DB MongoDB-databas från MongoDB-gränssnittet. Du hittar anvisningar i [ansluta ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md).
    
    b. Kör ett Exempelkommando för insert genom att använda ett av dokumenten exempel från MongoDB-Shell:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Kör ```db.runCommand({getLastRequestStatistics: 1})``` och du får ett svar som detta:
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. Anteckna begäran kostnad.
    
3. Avgör svarstid från din dator till Azure DB som Cosmos-Molntjänsten:
    
    a. Aktivera utförlig loggning i MongoDB-gränssnittet med hjälp av det här kommandot:```setVerboseShell(true)```
    
    b. Kör en enkel fråga mot databasen: ```db.coll.find().limit(1)```. Du får ett svar som detta:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Ta bort det infogade dokumentet innan migrering för att säkerställa att det inte finns några dubbletter dokument. Du kan ta bort dokument med hjälp av det här kommandot:```db.coll.remove({})```

5. Beräkna den ungefärliga *batchSize* och *numInsertionWorkers* värden:

    * För *batchSize*, dela upp det totala antalet etablerade RUs av RUs förbrukad från dina dokument Skriv i steg 3.
    
    * Om den beräknade *batchSize* < = 24, använder den som din *batchSize* värde.
    
    * Om den beräknade *batchSize* > 24, ange den *batchSize* värdet till 24.
    
    * För *numInsertionWorkers*, Använd den här formeln: *numInsertionWorkers = (dataflöde * fördröjning i sekunder) / (batchstorlek * används RUs för en enskild skrivning)*.
        
    |Egenskap|Värde|
    |--------|-----|
    |batchSize| 24 |
    |RUs etablerats | 10000 |
    |Svarstid | 0.100 s |
    |RU debiteras för 1 doc-skrivning | 10 RUs |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 RUs x 0,1 s) / (24 x 10 RUs) = 4.1666*

6. Kör kommandot slutliga migrering:

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Nästa steg

Du kan gå vidare till nästa kurs och lär dig hur du fråga MongoDB-data med hjälp av Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Hur man frågar MongoDB data?](../cosmos-db/tutorial-query-mongodb.md)
