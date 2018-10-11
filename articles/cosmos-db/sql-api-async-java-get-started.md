---
title: Skapa ett Java-program med hjälp av Azure Cosmos DB Async Java SDK | Microsoft Docs
description: Den här självstudien visar hur du använder Azure Cosmos DB SQL API-konton för att lagra och komma åt data med hjälp av ett Async Java-program.
keywords: nosql tutorial, online database, java console application
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: aa2613f7cb73c2c338189aaaa48587c49a3093f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962229"
---
# <a name="build-a-java-application-by-using-azure-cosmos-db-async-java-sdk"></a>Skapa ett Java-program med hjälp av Azure Cosmos DB Async Java SDK 

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Azure Cosmos DB är en globalt distribuerad databas för flera datamodeller. Den här självstudien visar hur du använder Azure Cosmos DB SQL API-konton för att lagra och komma åt data med hjälp av ett Async Java-program. 

Vi går igenom:

* Skapa och ansluta till ett Azure Cosmos DB-konto
* Konfigurera din lösning
* Skapa en samling
* Skapa JSON-dokument
* Skicka frågor till samlingen

Nu sätter vi igång!

## <a name="prerequisites"></a>Nödvändiga komponenter
Se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Java Development Kit (JDK) 8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto
Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Klona GitHub-projektet](#GitClone). Om du använder Azure Cosmos DB-emulatorn följer du stegen i [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och gå vidare till [Klona GitHub-projektet](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Steg 2: Klona GitHub-projekt
Du kan komma igång genom att klona GitHub-lagringsplatsen. Gå till [Komma igång med Azure Cosmos DB och Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Från en lokal katalog kör du till exempel följande för att hämta exempelprojektet lokalt.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started

```
Katalogen innehåller en `pom.xml`-fil för projektet och en `src/main/java/com/microsoft/azure/cosmosdb/sample`-mapp med Java-källkod, inklusive `Main.java` som visar hur du utför enkla åtgärder med Azure Cosmos DB, t.ex. hur du skapar dokument och frågar data i en samling. `pom.xml` innehåller ett beroende för [Azure Cosmos DB Java SDK för Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Steg 3: Ansluta till ett Azure Cosmos DB-konto
Gå sedan tillbaka till [Azure Portal](https://portal.azure.com) för att hämta din slutpunkt och primära huvudnyckel. Azure DB Cosmos-slutpunkten och den primära nyckeln krävs för att ditt program ska veta vart det ska ansluta, och för att Azure Cosmos DB ska lita på programmets anslutning. Filen `AccountSettings.java` innehåller den primära nyckeln och URI-värden. 

Gå till ditt Azure Cosmos DB-konto på Azure Portal och klicka på **Nycklar**. Kopiera URI och PRIMÄRNYCKEL från portalen och klistra in den i filen `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Skärmdump av Azure Portal som används i NoSQL-självstudiekursen för att skapa ett Java-konsolprogram. Visar ett Azure Cosmos DB-konto där den AKTIVA hubben är markerad, där knappen NYCKLAR är markerad på bladet för Azure Cosmos DB-kontot och där värdena för URI, PRIMÄR NYCKEL och SEKUNDÄR NYCKEL är markerade på bladet Nycklar][keys]

## <a name="step-4-initialize-the-client-object"></a>Steg 4: Initiera klientobjektet
Initiera klientobjektet med hjälp av värdena för värd-URI och primärnyckel som definieras i filen ”AccountSettings.java”

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Steg 5: Skapa en databas

Du kan skapa Azure Cosmos DB-[databasen](sql-api-resources.md#databases) med hjälp av metoden createDatabaseIfNotExists() i klassen DocumentClient. En databas är en logisk container för JSON-dokumentlagring, partitionerad över samlingarna.

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

## <a id="CreateColl"></a>Steg 6: Skapa en samling

> [!WARNING]
> **createCollection** skapar en ny samling med reserverat dataflöde, vilket får priskonsekvenser. Mer information finns på vår [prissättningssida](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 
Du kan skapa en samling med metoden createDocumentCollectionIfNotExists() i klassen DocumentClient. En samling är en container för JSON-dokument och associerad JavaScript-applogik.

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

## <a id="CreateDoc"></a>Steg 7: Skapa JSON-dokument

Ett [dokument](sql-api-resources.md#documents) kan skapas med hjälp av metoden createDocument i klassen DocumentClient. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Vi kan nu infoga ett eller flera dokument. Filen ”src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java” definierar familjen med JSON-dokument 

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

## <a id="Query"></a>Steg 8: Skicka frågor mot Azure Cosmos DB-resurser

Azure Cosmos DB stöder [komplexa frågor](sql-api-sql-query.md) mot JSON-dokument som lagras i varje samling. Följande exempelkod visar hur du frågar dokument i Azure Cosmos DB med hjälp av SQL-syntaxen med [queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments)-metoden.

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

## <a id="Run"></a>Steg 9: Kör ditt Java-konsolprogram i sin helhet!
Om du vill köra programmet från konsolen går du till projektmappen och kompilerar med hjälp av Maven:

```bash
mvn package
```

Om du kör `mvn package` hämtas det senaste Azure Cosmos DB-biblioteket från Maven och `GetStarted-0.0.1-SNAPSHOT.jar` skapas. Kör sedan appen genom att köra:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```
Grattis! Du har slutfört den här självstudiekursen om NoSQL och har ett fungerande Java-konsolprogram!

## <a name="next-steps"></a>Nästa steg
* Vill du ha en självstudie om Java-webbappar? Mer information finns i [Skapa ett webbprogram i Java med Azure Cosmos DB](sql-api-java-application.md).
* Lär dig hur du [övervakar ett Azure Cosmos DB-konto](monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
