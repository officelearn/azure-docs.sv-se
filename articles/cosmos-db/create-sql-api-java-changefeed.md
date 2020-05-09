---
title: Självstudie – ett asynkront Java SQL API-exempel från slut punkt till slut punkt med ändrings flöde
description: Den här självstudien vägleder dig genom ett enkelt Java SQL API-program som infogar dokument i en Azure Cosmos DB-behållare, samtidigt som en materialiserad vy av behållaren upprätthålls med hjälp av Change feed.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: c74ec73eb06c43110747d87e6fecd12183527759
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872541"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Självstudie – ett asynkront Java SQL API-exempel från slut punkt till slut punkt med ändrings flöde

Den här guiden vägleder dig genom ett enkelt Java SQL API-program som infogar dokument i en Azure Cosmos DB-behållare, samtidigt som en materialiserad vy av behållaren används med hjälp av Change feed.

## <a name="prerequisites"></a>Krav

* Personlig dator

* URI och nyckel för ditt Azure Cosmos DB konto

* Maven

* Java 8

## <a name="background"></a>Bakgrund

Azure Cosmos DB ändra feed innehåller ett händelse drivna gränssnitt för att utlösa åtgärder som svar på dokument infogning. Detta har många användnings områden. Till exempel i program som både är både Läs och skrivbara är en Chief-användning av ändrings flöden att skapa en real tids **materialiserad vy** över en behållare när den matar in dokument. Behållaren för materialiserad vy innehåller samma data, men partitioneras för effektiva läsningar, vilket gör programmet både Läs-och skriv effektivitet.

Arbetet med att hantera ändringar av flödes händelser tas i stor utsträckning av det process bibliotek för ändrings flöden som är inbyggt i SDK: n. Det här biblioteket är tillräckligt kraftfullt för att distribuera ändringar av flödes händelser mellan flera arbetare, om det behövs. Allt du behöver göra är att ange ett återanrops bibliotek för ändrings flöden.

I det här enkla exemplet visas ett bibliotek för ändrings flödes processor med en enda arbets tagare som skapar och tar bort dokument från en materialiserad vy.

## <a name="setup"></a>Installation

Om du inte redan har gjort det kan du klona appens exempel lagrings platsen:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> Du kan välja att arbeta med den här snabb starten med Java SDK 4,0 eller Java SDK 3.7.0. **Om du vill använda Java SDK-3.7.0 skriver du i terminaltypen ```git checkout SDK3.7.0``` **. Annars fortsätter du till ```master``` grenen, som använder Java SDK 4,0 som standard.

Öppna en Terminal i lagrings platsen-katalogen. Bygg appen genom att köra

```bash
mvn clean package
```

## <a name="walkthrough"></a>Genomgång

1. Som första kontroll bör du ha ett Azure Cosmos DB-konto. Öppna **Azure Portal** i webbläsaren, gå till ditt Azure Cosmos DB konto och gå till det vänstra fönstret och navigera till **datautforskaren**.

    ![Azure Cosmos DB konto](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Kör appen i terminalen med följande kommando:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Tryck på RETUR när du ser

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    Gå sedan tillbaka till Azure Portal Datautforskaren i webbläsaren. Du ser att en databas- **GroceryStoreDatabase** har lagts till med tre tomma behållare: 

    * **InventoryContainer** – inventerings posten för vårt exempel på livsmedels lager, partitionerad på objekt ```id``` som är ett uuid.
    * **InventoryContainer – pktype** – en materialiserad vy av inventerings posten, optimerad för frågor över objekt```type```
    * **InventoryContainer – leasing** avtal – en container container krävs alltid för ändrings flödet. lån spårar appens förlopp vid läsning av ändrings flödet.


    ![Tomma behållare](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. I terminalen bör du nu se en prompt

    ```bash
    Press enter to start creating the materialized view...
    ```

    Tryck på RETUR. Nu kommer följande kodblock att köra och initiera processorn för ändrings flöden i en annan tråd: 

   # <a name="java-sdk-40"></a>[Java SDK 4,0](#tab/v4sdk)

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

   # <a name="java-sdk-370"></a>[Java SDK-3.7.0](#tab/v3sdk)

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```
   ---

    ```"SampleHost_1"```är namnet på den ändrade flödes processorns arbetare. ```changeFeedProcessorInstance.start()```är vad som faktiskt startar bearbetningen av Change feeds.

    Gå tillbaka till Azure Portal Datautforskaren i webbläsaren. Under behållaren **InventoryContainer-Leases** , klickar du på **objekt** för att se dess innehåll. Du ser att Change feed-processorn har fyllt i leasing containern, dvs. processorn har tilldelat ```SampleHost_1``` arbetaren ett lån på vissa partitioner i **InventoryContainer**.

    ![Lån](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Tryck på RETUR igen i terminalen. Detta kommer att utlösa 10 dokument som ska infogas i **InventoryContainer**. Varje dokument infogning visas i ändra feed som JSON. följande callback-kod hanterar dessa händelser genom att spegla JSON-dokumenten till en materialiserad vy:

   # <a name="java-sdk-40"></a>[Java SDK 4,0](#tab/v4sdk)

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

   # <a name="java-sdk-370"></a>[Java SDK-3.7.0](#tab/v3sdk)

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```
   ---

1. Tillåt att koden kör 5-10sec. Gå sedan tillbaka till Azure Portal Datautforskaren och navigera till **InventoryContainer > objekt**. Du bör se att objekten infogas i lager behållaren. Anteckna partitionsnyckel (```id```).

    ![Feed-behållare](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Nu går du till Datautforskaren navigera till **InventoryContainer-pktype > objekt**. Detta är den materialiserade vyn – objekten i denna behållares spegel **InventoryContainer** eftersom de infogades program mässigt av ändrings flödet. Anteckna partitionsnyckel (```type```). Den här materialiserade vyn är optimerad för frågor som filtrerar över ```type```, vilket skulle vara ineffektivt på **InventoryContainer** eftersom den är partitionerad. ```id```

    ![Materialiserad vy](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Vi håller på att ta bort ett dokument från både **InventoryContainer** och **InventoryContainer-pktype** med bara ett ```upsertItem()``` enda anrop. Börja med att titta på Azure Portal Datautforskaren. Vi tar bort dokumentet som ```/type == "plums"```. Det är inringat i rött under

    ![Materialiserad vy](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Tryck på RETUR igen för att anropa ```deleteDocument()``` funktionen i exempel koden. Den här funktionen, som visas nedan, upsertar en ny version av dokumentet ```/ttl == 5```med, som anger TTL-värdet (Time-to-Live) till 5Sec. 
    
   # <a name="java-sdk-40"></a>[Java SDK 4,0](#tab/v4sdk)

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```
   # <a name="java-sdk-370"></a>[Java SDK-3.7.0](#tab/v3sdk)

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```
   ---

    Ändrings flödet ```feedPollDelay``` är inställt på 100 MS; därför svarar ändra feed på den här uppdateringen nästan omedelbart och samtal ```updateInventoryTypeMaterializedView()``` som visas ovan. Det senaste funktions anropet kommer att upsert det nya dokumentet med TTL för 5Sec till **InventoryContainer-pktype**.

    Detta innebär att dokumentet upphör att gälla efter 5 sekunder och tas bort från båda behållarna.

    Den här proceduren är nödvändig eftersom ändrings flödet bara utfärdar händelser vid artikel infogning eller uppdatering, inte för borttagning av objekt.

1. Tryck på RETUR en gång för att stänga programmet och rensa dess resurser.
