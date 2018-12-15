---
title: Skapa en Azure Cosmos DB-dokumentdatabas med Java
description: Presenterar ett Java-kodexempel som du kan använda för att ansluta till och ställa frågor via Azure Cosmos DB SQL API:t
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc, devcenter
ms.devlang: java
ms.topic: quickstart
ms.date: 10/24/2018
ms.author: moderakh
ms.openlocfilehash: 5f66623c8e37f0663da7defc10d87d89bd0ad99d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105786"
---
# <a name="create-and-manage-resources-of-an-azure-cosmos-db-sql-api-account-using-a-java-application"></a>Skapa och hantera resurser för ett Azure Cosmos DB SQL API-konto med hjälp av ett Java-program

> [!div class="op_single_selector"]
> * [NET](create-sql-api-dotnet.md)
> * [.NET (förhandsversion)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Den här självstudiekursen visar hur du skapar och hanterar resurser för ett Azure Cosmos DB [SQL API](sql-api-introduction.md)-konto med hjälp av ett Java-program. Först skapar du ett Azure Cosmos DB SQL API-konto med hjälp av Azure-portalen, skapar en Java-app med hjälp av [SQL Java SDK](sql-api-sdk-async-java.md) och lägger till resurser till ditt Cosmos DB-konto med hjälp av Java-programmet. Anvisningarna i den här snabbstartsguiden gäller alla operativsystem som kan köra Java. När du har slutfört den här snabbstarten kommer du att kunna skapa och ändra Cosmos DB-databaser eller samlingar i antingen användargränssnittet eller programmatiskt, beroende på vad du föredrar.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Följande gäller också: 

* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ned](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett [Maven](https://maven.apache.org/)-binärarkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en dokumentdatabas måste du skapa ett SQL-API-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Lägga till en samling

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Lägg till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Vi ska klona en SQL API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Köra appen](#run-the-app). 

* `AsyncDocumentClient`-initiering. [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client) ger logisk representation på klientsidan för Azure Cosmos DB-databastjänsten. Den här klienten används för att konfigurera och köra förfrågningar till tjänsten.

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* Skapa [databas](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._database).

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* Skapa [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._document_collection).

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* Skapa dokument med metoden [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._document).

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* SQL-frågor via JSON utförs med hjälp av metoden [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments?view=azure-java-stable).

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>Kör appen

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och starta appen med din slutpunktsinformation. På så vis kan appen kommunicera med den värdbaserade databasen.


1. I git-terminalfönstret `cd` till exempelkodsmappen.

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. I git-terminalfönstret använder du följande kommando för att installera de Java-paket som krävs.

    ```bash
    mvn package
    ```

3. I git-terminalfönstret använder du följande kommando för att starta Java-programmet (ersätt YOUR_COSMOS_DB_HOSTNAME med det angivna URI-värdet från portalen och ersätt YOUR_COSMOS_DB_MASTER_KEY med den angivna primärnyckeln från portalen)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Terminalfönstret visar ett meddelande om att FamilyDB-databasen har skapats. 
    
4. Skapa databasen genom att trycka på en nyckel och skapa sedan samlingen genom att trycka på en annan nyckel. 

    Växla tillbaka till Datautforskaren i webbläsaren och kontrollera att den nu innehåller en FamilyDB-databas och en FamilyCollection-samling.

5. Skapa det första dokumentet genom att växla till konsolfönstret och trycka på en tangent och skapa sedan det andra dokumentet genom att tryck på en annan tangent. Visa dem sedan genom att gå tillbaka till Datautforskaren. 

6. Kör en fråga och visa utdata i konsolfönstret genom att trycka på en tangent. 

7. Appen tar inte bort de skapade resurserna. Växla tillbaka till portalen för att [rensa resurserna](#clean-up-resources).  från ditt konto så att du inte debiteras.

    ![Konsolutdata](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att skapa ett Azure Cosmos DB-konto, en dokumentdatabas och en samling med datautforskaren och att köra en app för att göra samma sak programmässigt. Du kan nu importera ytterligare data till Azure Cosmos DB-samlingen. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
