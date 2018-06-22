---
title: Använda bulk utföraren Java-bibliotek för att utföra massåtgärder i Azure Cosmos DB | Microsoft Docs
description: Använd Azure Cosmos DB bulk utföraren Java-biblioteket massimport och uppdatera dokument till Azure Cosmos DB samlingar.
keywords: Java bulk utförare
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: f241a98cdcc847ddb579b86b51034d1438ee1395
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300722"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Använda bulk utföraren Java-bibliotek för att utföra massåtgärder på Azure Cosmos DB data

Den här självstudiekursen innehåller instruktioner om hur du använder Azure Cosmos DB bulk utföraren Java-bibliotek för att importera och uppdatera Azure Cosmos DB dokument. Läs om bulk utföraren biblioteket och hur det hjälper dig utnyttja omfattande genomströmning och lagring i [Massredigera utföraren biblioteket – översikt](bulk-executor-overview.md) artikel. I kursen får du skapa ett Java-program som genererar slumpmässiga dokument och de bulk importeras till en samling Azure Cosmos DB. När du har importerat, massutskick uppdatera vissa egenskaper för ett dokument. 

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.  

* Du kan [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. Du kan använda den [Azure Cosmos DB emulatorn](https://docs.microsoft.com/azure/cosmos-db/local-emulator) med den `https://localhost:8081` URI. Primärnyckeln finns i [Autentisera begäranden](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
  - I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.  

  - Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.

* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett [Maven](http://maven.apache.org/)-binärarkiv  
  
  - I Ubuntu kan du köra `apt-get install maven` för att installera Maven.

* Skapa ett Azure SQL DB-API Cosmos-konto med hjälp av stegen som beskrivs i [skapa databaskonto](create-sql-api-java.md#create-a-database-account) i Java quickstart artikeln.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi växla till att arbeta med kod genom att hämta ett exempelprogram för Java från GitHub. Det här programmet utför massåtgärder på Azure Cosmos DB data. Öppna en kommandotolk för att klona programmet, gå till den katalog där du vill kopiera programmet och kör följande kommando:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Klonade lagringsplatsen innehåller två exempel ”bulkimport” och ”bulkupdate” i förhållande till mappen ”\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor”. Programmet ”bulkimport” genererar slumpmässiga dokument och importerar dem till Azure Cosmos DB. ”Bulkupdate” programmet uppdateras vissa dokument i Azure Cosmos-databasen. I nästa avsnitt kommer vi går igenom koden i var och en av dessa exempelappar. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Massinläsning importera data till Azure Cosmos DB

1. Azure Cosmos DB-anslutningssträngar som argument och tilldelade variabler som definieras i filen CmdLineConfiguration.java.  

2. Nästa har DocumentClient-objektet initierats med hjälp av följande instruktioner:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. DocumentBulkExecutor-objektet har initierats med en hög försök värden för väntetiden och begränsas begäranden. Och sedan de har angetts till 0 att skicka överbelastningskontroll till DocumentBulkExecutor för dess livslängd.  

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
     offerThroughput) // throughput you want to allocate for bulk import out of the collection's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
```

4. Anropa importAll API som genererar slumpmässiga dokument om du vill Massuppdatera import till en Azure DB som Cosmos-samling. Du kan konfigurera kommandoraden konfigurationer i filen CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   Bulk import API accepterar en samling serialiseras till JSON-dokument och den har följande syntax för mer information, se den [API-dokumentationen](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

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
   |isUpsert    |   En flagga för att aktivera upsert dokument. Om ett dokument med angiven ID finns redan, uppdateras.  |
   |disableAutomaticIdGeneration     |   En flagga för att inaktivera automatisk generering av ID. Som standard är den inställd på true.   |
   |maxConcurrencyPerPartitionRange    |  Maximal graden av samtidighet per partitionsnyckelintervallet. Standardvärdet är 20.  |

   **Massredigera Importera svar objektdefinitionen** massimport API-anrop som innehåller följande get-metoder:

   |**Parametern**  |**Beskrivning**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Det totala antalet dokument som importeras av dokument som angetts för flesta importera API-anrop.      |
   |dubbla getTotalRequestUnitsConsumed()   |  De totala frågeenheter (RU) som används av flesta importera API-anrop.       |
   |Varaktighet getTotalTimeTaken()   |    Den totala tid som massimporten API-anrop för att köras.     |
   |Lista<Exception> getErrors() |  Hämtar listan över fel om vissa dokument utanför batch som angetts för flesta importerar det gick inte att hämta infogas API-anrop.       |
   |Lista<Object> getBadInputDocuments()  |    En lista med felaktigt format dokument som inte har importerats i flesta importera API-anrop. Användaren måste åtgärda de dokument som returneras och försök importera. Felaktig-formaterade dokument innehåller dokument vars ID-värdet inte är en sträng (det är ogiltiga null eller andra datatype).     |

5. När du har de flesta importera program klar skapa kommandoradsverktyget från datakällan med hjälp av kommandot mvn ren paketet. Det här kommandot genererar en jar-filen i målmappen:  

   ```java
   mvn clean package
   ```

6. När mål-beroenden genereras, kan du anropa bulk Importverktyget programmet med hjälp av följande kommando:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint URI>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Importverktyget för bulk skapar en ny databas och en samling med databasens namn, samlingsnamn och genomströmning värdena som anges i filen App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Uppdatera stora mängder data i Azure Cosmos DB

Du kan uppdatera befintliga dokument med BulkUpdateAsync API. I det här exemplet ska du ange fältet namn till ett nytt värde och ta bort beskrivningsfältet från befintliga dokument. En fullständig uppsättning stöds fältet uppdateringsåtgärder finns i avsnittet [API-dokumentationen](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definierar update objekten tillsammans med motsvarande fält uppdateringsåtgärder. I det här exemplet använder du SetUpdateOperation för att uppdatera fältet namn och UnsetUpdateOperation bort beskrivningsfältet från alla dokument. Du kan också utföra andra åtgärder som ökningen ett dokumentfält med ett specifikt värde, push specifika värden i en matris fält eller ta bort ett specifikt värde från en array-fält. Mer information om olika metoder som tillhandahålls av Massuppdatering API, finns det [API-dokumentationen](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Anropa updateAll API som genererar slumpmässiga dokument för att sedan bulk importeras till en Azure DB som Cosmos-samling. Du kan konfigurera kommandoradsverktyget konfigurationer som ska skickas i CmdLineConfiguration.java-filen.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Massuppdatering API accepterar en samling objekt som ska uppdateras. Varje uppdatering objekt anger en lista över åtgärder för uppdatering av fält som ska utföras på ett dokument som identifieras med ett ID och värdet för en partition. Mer information finns i [API-dokumentationen](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Metoden updateAll accepterar följande parametrar:

   |**Parametern** |**Beskrivning** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Maximal graden av samtidighet per partitionsnyckelintervallet. Standardvärdet är 20.  |
 
   **Massredigera Importera svar objektdefinitionen** massimport API-anrop som innehåller följande get-metoder:

   |**Parametern** |**Beskrivning**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Totalt antal dokument som har uppdaterats av dokument som angetts för bulk update API-anrop.      |
   |dubbla getTotalRequestUnitsConsumed() |  Totalt antal begäranden enheter (RU) används av Massuppdatering API-anrop.       |
   |Varaktighet getTotalTimeTaken()  |   Den totala tid som flesta uppdatera API-anrop för att köras.      |
   |Lista<Exception> getErrors()   |     Hämtar listan över fel om vissa dokument out-of-satsen som har angetts för API-anrop samtidigt uppdatering gick inte att hämta infogas.      |

3. När du har de flesta uppdatera programmet klar, kan du skapa kommandoradsverktyget från datakällan med hjälp av kommandot mvn ren paketet. Det här kommandot genererar en jar-filen i målmappen:  

   ```
   mvn clean package
   ```

4. När beroenden som mål har genererats kan du anropa uppdateringsprogrammet bulk med hjälp av följande kommando:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint URI>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Prestandatips 

Tänk på följande för bättre prestanda när du använder bulk utföraren bibliotek:

* Kör ditt program från en Azure-dator i samma region som Cosmos DB kontot Skriv region för bästa prestanda.  
* För att uppnå högre genomströmning:  

   * Ange i JVM heap-storlek till ett tal som är tillräckligt stor för att undvika eventuella problem minne för att hantera många dokument. Föreslagen heap-storlek: max (3GB, 3 * sizeof (alla dokument som skickades till bulk import API i en batch)).  
   * Det finns en förbearbetning tid, på grund av som du får bättre genomströmning när du utför massåtgärder med ett stort antal dokument. Om du vill importera 10 000 000 dokument är köra massimport 10 gånger på 10 största delen av dokument varje storlek 1 000 000 så bättre än att köra massimport 100 gånger på 100 huvuddelen av dokument var och en av storleken 100 000 dokument.  

* Det rekommenderas att instansiera ett DocumentBulkExecutor objekt för hela programmet i en enskild virtuell dator som motsvarar en viss Azure DB som Cosmos-samling.  

* Eftersom en enda API massåtgärder förbrukar en stor del av den klientdatorn CPU: N och IO. Detta sker genom att skapa flera uppgifter internt bör du undvika att skapa flera samtidiga uppgifter i din programprocessen varje körs samtidigt åtgärden API-anrop. Om ett enda bulk åtgärden API-anrop som körs på en enskild virtuell dator kan inte använda din hela samlingen genomströmning (om din samling genomströmning > 1 miljon RU/s), är det bättre att skapa separata virtuella datorer för att samtidigt köra grupp åtgärden API-anrop.

    
## <a name="next-steps"></a>Nästa steg
* Mer information om information om maven och viktig information för bulk utföraren Java-bibliotek finns[Massredigera utföraren SDK information](sql-api-sdk-bulk-executor-java.md).


