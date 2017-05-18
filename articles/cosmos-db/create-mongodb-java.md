---
title: 'Azure Cosmos DB: Skapa en konsolapp med Java och MongoDB-API:t | Microsoft Docs'
description: "Presenterar ett Java-kodexempel som du kan använda för att ansluta till och ställa frågor via Azure Cosmos DB MongoDB-API:t"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 22bce387c2b33759c1b83247f253c72fb3132739
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-java-and-the-azure-portal"></a>Azure Cosmos DB: Skapa en MongoDB API-konsolapp med Java och Azure Portal

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

I den här snabbstarten visas hur du skapar ett Azure Cosmos DB-konto, en dokumentdatabas och en samling med hjälp av Azure Portal. Sedan kommer du att skapa och distribuera en konsolapp som är byggd med [MondoDB Java-drivrutinen](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Krav

* Innan du kan köra det här exemplet måste du uppfylla följande krav:
   * JDK 1.7+ (Kör `apt-get install default-jdk` om du inte har JDK)
   * Maven (Kör `apt-get install maven` om du inte har Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmosdb-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Lägga till en samling

Ge den nya databasen namnet **db** och den nya samlingen namnet **coll**.

[!INCLUDE [cosmosdb-create-collection](../../includes/cosmosdb-create-collection.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en MongoDB API-app från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, till exempel git bash, och `cd` till en arbetskatalog.  

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Öppna därefter lösningsfilen i Visual Studio. 

## <a name="review-the-code"></a>Granska koden

Vi gör en snabb genomgång av vad som händer i appen. Öppna filen `Program.cs` så ser du att de här kodraderna skapar Azure Cosmos DB-resurserna. 

* DocumentClient initieras.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* En ny databas och samling skapas.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Några dokument infogas med `MongoCollection.insertOne`

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Några frågor körs med `MongoCollection.find`

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());        
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. Från Konto väljer du **Snabbstart**, Java och kopierar sedan anslutningssträngen till Urklipp

2. Öppna filen `Program.java` och ersätt argumentet för konstruktorn MongoClientURI med anslutningssträngen. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Kör konsolappen

1. Kör `mvn package` i en terminal för att installera de npm-moduler som krävs

2. Kör `mvn exec:java -D exec.mainClass=GetStarted.Program` i en terminal för att starta Java-programmet.

Nu kan du använda [Robomongo](../documentdb/documentdb-mongodb-robomongo.md) / [Studio 3T](../documentdb/documentdb-mongodb-mongochef.md) till att ställa frågor mot, ändra och arbeta med dessa nya data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. Klicka på **Ta bort** på sidan med resursgrupper, ange namnet på resursen du vill ta bort i textrutan och klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en samling med Datautforskaren och kör en konsolapp. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera MondoDB-data till Azure Cosmos DB](../documentdb/documentdb-mongodb-migrate.md)



