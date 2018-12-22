---
title: 'Azure Cosmos DB: Skapa en konsolapp med Java och MongoDB API'
description: Presenterar ett Java-kodexempel som du kan använda för att ansluta till och ställa frågor via Azure Cosmos DB MongoDB-API:t
services: cosmos-db
author: slyons
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quick start connect, mvc
ms.devlang: java
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: sclyon
ms.openlocfilehash: 30e87ba14c6754fa39269f3afac318a02cf99a2c
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162028"
---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-java-and-the-azure-portal"></a>Azure Cosmos DB: Skapa en MongoDB-API-webbapp med Java och Azure Portal

> [!div class="op_single_selector"]
> * [NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabbstarten visar hur du skapar ett Microsoft Azure Cosmos DB [MongoDB API](mongodb-introduction.md)-konto, dokumentdatabas och samling med Azure Portal. Sedan kommer du att skapa och distribuera en konsolapp som är byggd med [MondoDB Java-drivrutinen](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan köra det här exemplet måste du uppfylla följande krav:
* JDK 1.7+ (Kör `apt-get install default-jdk` om du inte har JDK)
* Maven (Kör `apt-get install maven` om du inte har Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Lägga till en samling

Ge den nya databasen namnet **db** och den nya samlingen namnet **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en MongoDB API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Öppna sedan koden i det redigeringsprogram du föredrar. 

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

Följande kodfragment är alla hämtade från filen Program.java.

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

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen.

1. Från Konto väljer du **Snabbstart**, Java och kopierar sedan anslutningssträngen till Urklipp

2. Öppna filen `Program.java` och ersätt argumentet för konstruktorn MongoClientURI med anslutningssträngen. Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Kör konsolappen

1. Kör `mvn package` i en terminal för att installera de npm-moduler som krävs

2. Kör `mvn exec:java -D exec.mainClass=GetStarted.Program` i en terminal för att starta Java-programmet.

Nu kan du använda [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) till att ställa frågor mot, ändra och arbeta med dessa nya data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en samling med Datautforskaren och kör en konsolapp. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera MondoDB-data till Azure Cosmos DB](mongodb-migrate.md)


