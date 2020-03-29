---
title: Använda Java-bibliotek för massutnr0 i Azure Cosmos DB för att utföra massimport- och uppdateringsåtgärder
description: Massimport och uppdatering av Azure Cosmos DB-dokument med hjälp av Java-bibliotek för massutringar
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: bf2a2385b3129ddf24ede7f6d851701186b0e33c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445716"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Använda massexecutor-biblioteket i Java för att utföra massåtgärder på Azure Cosmos DB-data

Den här självstudien innehåller instruktioner om hur du använder Azure Cosmos DB:s javabibliotek för massutdelning för att importera och uppdatera Azure Cosmos DB-dokument. Mer information om massutflödesbibliotek och hur det hjälper dig att utnyttja massivt dataflöde och lagringsutrymme finns i översiktsartikeln [för massutflödesbibliotek.](bulk-executor-overview.md) I den här självstudien skapar du ett Java-program som genererar slumpmässiga dokument och de massimporteras till en Azure Cosmos-behållare. När du har importerat ska du masskopiera vissa egenskaper för ett dokument. 

För närvarande stöds massutnämningsbiblioteket endast av Azure Cosmos DB SQL API- och Gremlin API-konton. I den här artikeln beskrivs hur du använder Java-bibliotek för massutnrör med SQL API-konton. Mer information om hur du använder .NET-bibliotek med Gremlin API finns [i utföra massåtgärder i Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.  

* Du kan [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, kostnadsfritt och åtaganden. Du kan också använda [Azure Cosmos DB Emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) med `https://localhost:8081` slutpunkten. Primärnyckeln finns i [Autentisera begäranden](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1,7+](https://aka.ms/azure-jdks)  
  - I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.  

  - Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.

* [Ladda ner](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett [Maven](https://maven.apache.org/) binärt arkiv  
  
  - I Ubuntu kan du köra `apt-get install maven` för att installera Maven.

* Skapa ett Azure Cosmos DB SQL API-konto med hjälp av stegen som beskrivs i avsnittet [skapa databaskonto](create-sql-api-java.md#create-a-database-account) i snabbstartsartikeln Java.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi byta till att arbeta med kod genom att ladda ner ett exempel Java-program från GitHub. Det här programmet utför massåtgärder på Azure Cosmos DB-data. Om du vill klona programmet öppnar du en kommandotolk genom att navigera till katalogen där du vill kopiera programmet och kör följande kommando:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Den klonade databasen innehåller två exempel "bulkimport" och "bulkupdate" i förhållande till mappen "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". Programmet "massimport" genererar slumpmässiga dokument och importerar dem till Azure Cosmos DB. Programmet "bulkupdate" uppdaterar vissa dokument i Azure Cosmos DB. I nästa avsnitt granskar vi koden i var och en av dessa exempelappar. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Massimportdata till Azure Cosmos DB

1. Azure Cosmos DB:s anslutningssträngar läss som argument och tilldelas variabler som definierats i filen CmdLineConfiguration.java.  

2. Därefter initieras DocumentClient-objektet med hjälp av följande satser:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. DocumentBulkExecutor-objektet initieras med ett högt återförsöksvärden för väntetid och begränsade begäranden. Och sedan är de inställda på 0 för att passera överbelastning kontroll till DocumentBulkExecutor för sin livstid.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Anropa importAlla API som genererar slumpmässiga dokument för massimport till en Azure Cosmos-behållare. Du kan konfigurera kommandoradskonfigurationerna i filen CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   Massimport-API:et accepterar en samling JSON-serialiserade dokument och har följande syntax, för mer information, se [API-dokumentationen:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Metoden importAll accepterar följande parametrar:
 
   |**Parametern**  |**Beskrivning**  |
   |---------|---------|
   |ärUpsert    |   En flagga som aktiverar upsert av dokumenten. Om det redan finns ett dokument med givet ID uppdateras det.  |
   |inaktiveraAutomaticIdGeneration     |   En flagga för att inaktivera automatisk generering av ID. Som standard är den inställd på true.   |
   |maxConcurrencyPerPartitionRange    |  Den maximala graden av samtidighet per partition nyckelintervall. Standardvärdet är 20.  |

   **Definition av svarsobjekt för massimport** Resultatet av api-anropet för massimport innehåller följande get-metoder:

   |**Parametern**  |**Beskrivning**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Det totala antalet dokument som har importerats från de dokument som lämnats till api-anropet för massimport.      |
   |dubbel getTotalRequestUnitsConsumed()   |  De totala begärandeenheter (RU) som förbrukas av API-anropet för massimport.       |
   |Varaktighet getTotalTimeTaken()   |    Den totala tid det tar för massimport-API-anropet att slutföra körningen.     |
   |Lista\<undantag> getErrors() |  Hämtar listan över fel om vissa dokument från batchen som levereras till api-anropet för massimport inte kunde infogas.       |
   |Lista\<objekt> fåBadInputDocuments()  |    Listan över dokument i felformat som inte har importerats i api-anropet för massimport. Användaren bör åtgärda de dokument som returneras och försöka importera igen. Dokument med felaktiga format omfattar dokument vars ID-värde inte är en sträng (null eller någon annan datatyp anses ogiltig).     |

5. När du har massimportprogrammet klart skapar du kommandoradsverktyget från källan med kommandot "mvn clean package". Det här kommandot genererar en jar-fil i målmappen:  

   ```java
   mvn clean package
   ```

6. När målberoendena har genererats kan du anropa massimportörsprogrammet med hjälp av följande kommando:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Massimportören skapar en ny databas och en samling med databasnamn, samlingsnamn och dataflödesvärden som anges i filen App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Massuppdateringsdata i Azure Cosmos DB

Du kan uppdatera befintliga dokument med hjälp av MassUpdateAsync API. I det här exemplet anger du fältet Namn till ett nytt värde och tar bort fältet Beskrivning från de befintliga dokumenten. Fullständig uppsättning fältuppdateringsåtgärder som stöds finns i [API-dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definierar uppdateringsobjekten tillsammans med motsvarande fältuppdateringsåtgärder. I det här exemplet använder du SetUpdateOperation för att uppdatera fältet Namn och UnsetUpdateOperation för att ta bort fältet Beskrivning från alla dokument. Du kan också utföra andra operationer som att öka ett dokumentfält med ett visst värde, skicka specifika värden till ett matrisfält eller ta bort ett visst värde från ett matrisfält. Mer information om olika metoder som tillhandahålls av massuppdaterings-API:et finns i [API-dokumentationen](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Anropa uppdateringenAlla API som genererar slumpmässiga dokument som sedan massimporteras till en Azure Cosmos-behållare. Du kan konfigurera kommandoradskonfigurationerna som ska skickas i filen CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Massuppdaterings-API:et accepterar en samling artiklar som ska uppdateras. Varje uppdateringsobjekt anger listan över fältuppdateringsåtgärder som ska utföras på ett dokument som identifieras av ett ID och ett partitionsnyckelvärde. Mer information finns i [API-dokumentationen:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Metoden updateAll accepterar följande parametrar:

   |**Parametern** |**Beskrivning** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Den maximala graden av samtidighet per partition nyckelintervall. Standardvärdet är 20.  |
 
   **Definition av svarsobjekt för massimport** Resultatet av api-anropet för massimport innehåller följande get-metoder:

   |**Parametern** |**Beskrivning**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Det totala antalet dokument som har uppdaterats av de dokument som lämnats till API-anropet för massuppdatering.      |
   |dubbel getTotalRequestUnitsConsumed() |  De totala begärandeenheter (RU) som förbrukas av API-anropet för massuppdatering.       |
   |Varaktighet getTotalTimeTaken()  |   Den totala tid det tar för massuppdaterings-API-anropet att slutföra körningen.      |
   |Lista\<undantag> getErrors()   |    Hämtar listan över fel om vissa dokument från batchen som levereras till API-anropet för massuppdatering inte kunde infogas.      |

3. När du har massuppdateringsprogrammet klart skapar du kommandoradsverktyget från källan med kommandot "mvn clean package". Det här kommandot genererar en jar-fil i målmappen:  

   ```
   mvn clean package
   ```

4. När målberoendena har genererats kan du anropa massuppdateringsprogrammet med hjälp av följande kommando:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Prestandatips 

Tänk på följande punkter för bättre prestanda när du använder massutförandeörsbibliotek:

* För bästa prestanda kan du köra ditt program från en Azure VM i samma region som din cosmos DB-kontoskrivningsregion.  
* För att uppnå högre genomströmning:  

   * Ställ in JVM:s heap-storlek på ett tillräckligt stort antal för att undvika minnesproblem vid hantering av ett stort antal dokument. Föreslagen heap storlek: max(3GB, 3 * sizeof (alla dokument som skickas till bulk import API i en batch)).  
   * Det finns en förbearbetningstid, på grund av vilken du får högre dataflöde när du utför massåtgärder med ett stort antal dokument. Så om du vill importera 10.000.000 dokument, kör bulk import 10 gånger på 10 bulk av dokument vardera av storlek 1.000.000 är att föredra än att köra bulk import 100 gånger på 100 bulk dokument vardera av storlek 100.000 dokument.  

* Vi rekommenderar att du instansierar ett enda DocumentBulkExecutor-objekt för hela programmet inom en enda virtuell dator som motsvarar en specifik Azure Cosmos-behållare.  

* Eftersom en enda massåtgärd API-körning förbrukar en stor del av klientdatorns CPU och nätverks-IO. Detta händer genom att skapa flera aktiviteter internt, undvika att skapa flera samtidiga uppgifter i programprocessen varje körning av API-anrop för massåtgärder. Om ett API-anrop för en enda massåtgärd som körs på en enda virtuell dator inte kan använda hela behållarens dataflöde (om behållarens dataflöde > 1 miljon RU/s) är det bättre att skapa separata virtuella datorer för att samtidigt köra API-anrop för massåtgärder.

    
## <a name="next-steps"></a>Nästa steg
* Mer information om maven-paketinformation och viktig information om Java-bibliotek för bulk executor finns i[information om SDK-bulkutgångare](sql-api-sdk-bulk-executor-java.md).


