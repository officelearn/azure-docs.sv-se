---
title: Med hjälp av Java-bibliotek för bulk-executor att utföra massimport och uppdateringsåtgärder i Azure Cosmos DB
description: Massimport och uppdatera Azure Cosmos DB-dokument med hjälp av bulk executor Java-bibliotek.
services: cosmos-db
author: tknandu
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.openlocfilehash: 2767b8e493327e4e8275481ffc7aa18d70c81ccd
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836805"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Använd bulk executor Java-bibliotek för att utföra massåtgärder på Azure Cosmos DB-data

Den här självstudiekursen innehåller instruktioner om hur du använder Azure Cosmos DB: s bulk executor Java-bibliotek för att importera och uppdatera Azure Cosmos DB-dokument. Läs om bulk executor biblioteket och hur den hjälper dig att utnyttja massivt dataflöde och lagring i [bulk executor biblioteksöversikt](bulk-executor-overview.md) artikeln. I den här självstudien får du skapa ett Java-program som genererar slumpmässiga dokument och de är samtidigt som importeras till en Azure Cosmos DB-behållare. När du har importerat, bulk uppdatera vissa egenskaper för ett dokument. 

Massinläsning executor biblioteket är för närvarande stöds av Azure Cosmos DB SQL API och Gremlin-API-konton. Den här artikeln beskriver hur du använder bulk executor-biblioteket för .net med SQL API-konton. Läs om hur du använder .NET-biblioteket för bulk-executor med Gremlin-API i [utföra massåtgärder i Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.  

* Du kan [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. Du kan också använda den [Azure Cosmos DB-emulatorn](https://docs.microsoft.com/azure/cosmos-db/local-emulator) med den `https://localhost:8081` URI. Primärnyckeln finns i [Autentisera begäranden](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.  

  - Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.

* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett [Maven](http://maven.apache.org/)-binärarkiv  
  
  - I Ubuntu kan du köra `apt-get install maven` för att installera Maven.

* Skapa ett Azure Cosmos DB SQL API-konto med hjälp av stegen som beskrivs i [skapa databaskonto](create-sql-api-java.md#create-a-database-account) i artikeln för Java-Snabbstart.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod genom att ladda ned en Java-exempelprogram från GitHub. Det här programmet utför massåtgärder på Azure Cosmos DB-data. Om du vill klona programmet, öppna en kommandotolk, navigerar du till den katalog där du vill kopiera programmet och kör följande kommando:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Den klonade lagringsplatsen innehåller två exempel ”bulkimport” och ”bulkupdate” i förhållande till mappen ”\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor”. ”Bulkimport” programmet genererar slumpmässiga dokument och importerar dem till Azure Cosmos DB. ”Bulkupdate” programmet uppdaterar några dokument i Azure Cosmos DB. I nästa avsnitt kommer vi att granska koden i var och en av dessa exempelappar. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Importera stora mängder data till Azure Cosmos DB

1. Azure Cosmos DB-anslutningssträngar läses som argument och tilldelats variabler som definieras i CmdLineConfiguration.java-filen.  

2. Bredvid initieras DocumentClient-objektet med hjälp av följande uttryck:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. DocumentBulkExecutor-objektet har initierats med en hög retry-värdena för väntetid och begränsade begäranden. Och sedan de är inställda på 0 att skicka överbelastningskontroll till DocumentBulkExecutor för dess livslängd.  

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

4. Anropa importAll API som genererar slumpmässiga dokument om du vill Massuppdatera import till en Azure Cosmos DB-behållare. Du kan konfigurera kommandoraden-konfigurationer i filen CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   Massinläsning importera API accepterar en samling JSON-serialiserat dokument och den har följande syntax, för mer information, se den [API-dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   ImportAll-metoden godkänner följande parametrar:
 
   |**Parametern**  |**Beskrivning**  |
   |---------|---------|
   |isUpsert    |   En flagga för att aktivera upsert dokument. Om ett dokument med angiven ID finns redan, den har uppdaterats.  |
   |disableAutomaticIdGeneration     |   En flagga för att inaktivera automatisk generering av ID. Som standard är den inställd till true.   |
   |maxConcurrencyPerPartitionRange    |  Högsta grad av samtidighet per partitionsnyckelintervall. Standardvärdet är 20.  |

   **Bulk import svar objektdefinition** resultatet av massimport API-anropet innehåller följande get-metoder:

   |**Parametern**  |**Beskrivning**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Det totala antalet dokument som importerades från de dokument som angetts för stora importera API-anrop.      |
   |dubbla getTotalRequestUnitsConsumed()   |  De totala begäransenheter (RU) som används av stora importera API-anrop.       |
   |Varaktighet getTotalTimeTaken()   |    Den totala tid som massimporten API-anrop för att slutföra körning.     |
   |Lista<Exception> getErrors() |  Hämtar lista över fel om vissa dokumenten batch som angetts för stora importera API-anrop som det gick inte att hämta infogas.       |
   |Lista<Object> getBadInputDocuments()  |    Listan över dokument felaktig-format som inte har importerats i grupp importera API-anrop. Användaren bör åtgärda dokumenten som returneras och försök att importera igen. Felaktig-formaterade dokument innehålla dokument vars ID-värdet inte är en sträng (det är ogiltiga null eller andra datatype).     |

5. När du har den stora importera program är klara kan du skapa kommandoradsverktyget från källan med hjälp av kommandot mvn ren paketet. Det här kommandot genererar en jar-filen i målmappen:  

   ```java
   mvn clean package
   ```

6. När beroendena som mål har genererats kan du anropa bulk-Importverktyget programmet med hjälp av följande kommando:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint URI>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Massinläsning importören skapar en ny databas och en samling med databasens namn, samlingsnamn och dataflöde värdena som anges i filen App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Uppdatera stora mängder data i Azure Cosmos DB

Du kan uppdatera befintliga dokument med hjälp av BulkUpdateAsync-API. I det här exemplet kommer du ange fältet namn till ett nytt värde och ta bort beskrivningsfältet från befintliga dokument. Uppdateringsåtgärder för den fullständiga uppsättningen fält som stöds, se [API-dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definierar update-objekt tillsammans med motsvarande fält uppdateringsåtgärder. I det här exemplet ska du använda SetUpdateOperation för att uppdatera i namnfältet och UnsetUpdateOperation att ta bort fältet Beskrivning från alla dokument. Du kan också utföra andra åtgärder som att öka ett dokumentfält med ett specifikt värde, skicka specifika värden till ett matrisfält eller ta bort ett specifikt värde från ett matrisfält. Läs mer om olika metoder som tillhandahålls av Massuppdatering API, i den [API-dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Anropa updateAll API som genererar slumpmässiga dokument för att sedan samtidigt som importeras till en Azure Cosmos DB-behållare. Du kan konfigurera konfigurationerna som kommandorad som ska skickas i CmdLineConfiguration.java-filen.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Massuppdatering API accepterar en samling objekt som ska uppdateras. Varje uppdateringsobjekt anger listan över åtgärder för uppdatering av fält som ska utföras på ett dokument som identifieras av ett ID och ett partitionsnyckelvärde. Mer information finns i den [API-dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   UpdateAll-metoden godkänner följande parametrar:

   |**Parametern** |**Beskrivning** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Högsta grad av samtidighet per partitionsnyckelintervall. Standardvärdet är 20.  |
 
   **Bulk import svar objektdefinition** resultatet av massimport API-anropet innehåller följande get-metoder:

   |**Parametern** |**Beskrivning**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Det totala antalet dokument som har uppdaterats av dokumenten som angetts för massuppdatering API-anrop.      |
   |dubbla getTotalRequestUnitsConsumed() |  Totalt antal begäransenheter (RU) används av Massuppdatering API-anrop.       |
   |Varaktighet getTotalTimeTaken()  |   Den totala tid som stora uppdatera API-anrop för att slutföra körning.      |
   |Lista<Exception> getErrors()   |     Hämtar lista över fel om vissa dokumenten batch kan du API-anrop samtidigt uppdateringen kunde inte hämta infogas.      |

3. När du har den stora uppdatera programmet är klara kan skapa kommandoradsverktyget från källan med hjälp av kommandot mvn ren paketet. Det här kommandot genererar en jar-filen i målmappen:  

   ```
   mvn clean package
   ```

4. När beroendena som mål har genererats kan du anropa bulk uppdatera program med hjälp av följande kommando:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint URI>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Prestandatips 

Tänk på följande för bättre prestanda när du använder bulk executor bibliotek:

* Kör ditt program från en Azure virtuell dator i samma region som skrivregion Cosmos DB-konto för bästa prestanda.  
* För att uppnå högre dataflöde:  

   * Ange i JVM. generace till ett tal som är tillräckligt stor för att undvika eventuella problem med minne för att hantera stora antal dokument. Föreslås stackstorlek: max (3GB, 3 * sizeof (alla dokument som skickats till bulk importera API i en batch)).  
   * Det finns en förbearbetning tid, vilket du får högre dataflöde när du utför massåtgärder med ett stort antal dokument. Om du vill importera 10 000 000 dokument är köra massimport 10 gånger på 10 största delen av dokument varje storlek 1 000 000 så bättre än att köra massimport 100 gånger på 100 största delen av dokument var och en av storlek på 100 000 dokument.  

* Vi rekommenderar att skapa en instans av ett enda DocumentBulkExecutor-objekt för hela programmet i en enda virtuell dator som motsvarar en viss Azure Cosmos DB-behållare.  

* Eftersom ett enda API massåtgärder förbrukar en stor del av klientdatorns processor- och IO. Detta sker genom att skapa flera aktiviteter internt bör du undvika att skapa flera samtidiga aktiviteter i din programprocessen varje körs samtidigt åtgärden API-anrop. Om ett enda bulk åtgärden API-anrop som körs på en virtuell dator inte kan använda hela behållaren dataflöde (om din behållare dataflöde > 1 miljon RU/s), är det bättre att skapa separata virtuella datorer för att samtidigt köra grupp åtgärden API-anrop.

    
## <a name="next-steps"></a>Nästa steg
* Mer information om maven-Paketinformation och viktig information för bulk executor Java-bibliotek, se[bulk information om SDK-executor](sql-api-sdk-bulk-executor-java.md).


