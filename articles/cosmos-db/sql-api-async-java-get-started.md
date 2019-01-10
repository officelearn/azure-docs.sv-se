---
title: 'Självstudie: Skapa en Java-app med Async Java SDK för att hantera ett SQL API-konto i Azure Cosmos DB'
description: Den här självstudien visar hur du lagrar och kommer åt data i ett SQL API-konto i Azure Cosmos DB med hjälp av ett Async Java-program.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 80146c7ec7b496b33bede8172d9945c78b26511e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039035"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Självstudier: Skapa en Java-app med Async Java SDK för att hantera data som lagras i ett SQL API-konto

> [!div class="op_single_selector"]
> * [NET](sql-api-get-started.md)
> * [.NET (förhandsversion)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (förhandsversion)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Som utvecklare kan du ha program som använder NoSQL-dokumentdata. Du kan använda SQL API-kontot i Azure Cosmos DB för att lagra och komma åt dessa dokumentdata. Den här självstudien visar hur du skapar ett Java-program med Async Java SDL för att lagra och hantera dokumentdata. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa och ansluta till ett Azure Cosmos-konto
> * Konfigurera din lösning
> * Skapa en samling
> * Skapa JSON-dokument
> * Skicka frågor till samlingen

## <a name="prerequisites"></a>Nödvändiga komponenter

Se till att du har följande resurser:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Skapa ett Azure Cosmos-konto med hjälp av följande steg:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Klona GitHub-lagringsplatsen

Klona GitHub-lagringsplatsen för [Komma igång med Azure Cosmos DB och Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Från en lokal katalog kör du till exempel följande för att hämta exempelprojektet lokalt.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

Katalogen innehåller en `pom.xml`-fil och en `src/main/java/com/microsoft/azure/cosmosdb/sample`-mapp som innehåller Java-källkod inklusive `Main.java`. Projektet innehåller den kod som krävs för att utföra åtgärder med Azure Cosmos DB såsom att skapa dokument och köra frågor mot data i en samling. Filen `pom.xml` innehåller ett beroende för [Azure Cosmos DB Java SDK för Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Ansluta till ett Azure Cosmos-konto

Gå sedan tillbaka till [Azure-portalen](https://portal.azure.com) för att hämta din slutpunkt och primära huvudnyckel. Azure DB Cosmos-slutpunkten och den primära nyckeln krävs för att ditt program ska veta vart det ska ansluta, och för att Azure Cosmos DB ska lita på programmets anslutning. Filen `AccountSettings.java` innehåller den primära nyckeln och URI-värden. 

Gå till ditt Azure Cosmos-konto på Azure-portalen och klicka på **Nycklar**. Kopiera URI och PRIMÄRNYCKEL från portalen och klistra in den i filen `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Hämta nycklar från portalen, skärmbild][keys]

## <a name="initialize-the-client-object"></a>Initiera klientobjektet

Initiera klientobjektet med hjälp av de värden för värd-URI och primärnyckel som definieras i filen ”AccountSettings.java”.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Skapa en databas

Skapa din Azure Cosmos DB-databas genom att använda metoden `createDatabaseIfNotExists()` i klassen DocumentClient. En databas är en logisk container för JSON-dokumentlagring, partitionerad över samlingarna.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>Skapa en samling

Du kan skapa en samling med hjälp av metoden `createDocumentCollectionIfNotExists()` i klassen DocumentClient. En samling är en container för JSON-dokument och associerad JavaScript-applogik.

> [!WARNING]
> **createCollection** skapar en ny samling med reserverat dataflöde, vilket får priskonsekvenser. Mer information finns på vår [prissättningssida](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>Skapa JSON-dokument

Skapa ett dokument med hjälp av metoden createDocument i klassen DocumentClient. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Vi kan nu infoga ett eller flera dokument. Filen ”src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java” definierar familjen med JSON-dokument. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Köra frågor mot Azure Cosmos DB-resurser

Azure Cosmos DB stöder komplexa frågor mot JSON-dokument som lagras i varje samling. Följande exempelkod visar hur du kör frågor mot dokument i Azure Cosmos DB med hjälp av SQL-syntaxen med metoden `queryDocuments`.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Köra ditt Java-konsolprogram

Om du vill köra programmet från konsolen går du till projektmappen och kompilerar med hjälp av Maven:

```bash
mvn package
```

Om du kör `mvn package` hämtas det senaste Azure Cosmos DB-biblioteket från Maven och `GetStarted-0.0.1-SNAPSHOT.jar` skapas. Kör sedan appen genom att köra:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Du har nu slutfört den här självstudien om NoSQL och har ett fungerande Java-konsolprogram.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, Azure Cosmos-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en Java-app med Async Java SDK för att hantera SQL API-data i Azure Cosmos DB. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Skapa ett Node.js-konsolprogram med JavaScript SDK och Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
