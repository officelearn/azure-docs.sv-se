---
title: Självstudiekurs - ett end-to-end Async Java SQL API-programexempel med Ändringsfeed
description: Den här självstudien går igenom ett enkelt Java SQL API-program som infogar dokument i en Azure Cosmos DB-behållare, samtidigt som du behåller en materialiserad vy av behållaren med ändra feed.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586703"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Självstudiekurs - ett end-to-end Async Java SQL API-programexempel med Ändringsfeed

Den här självstudieguiden går igenom ett enkelt Java SQL API-program som infogar dokument i en Azure Cosmos DB-behållare, samtidigt som du behåller en materialiserad vy av behållaren med ändra feed.

## <a name="prerequisites"></a>Krav

* Persondator

* URI och nyckel för ditt Azure Cosmos DB-konto

* Maven

* Java 8

## <a name="background"></a>Bakgrund

Azure Cosmos DB Change Feed tillhandahåller ett händelsedrivet gränssnitt för att utlösa åtgärder som svar på infogning av dokument. Detta har många användningsområden. Till exempel i program som både läs och skriv tungt, en huvudanvändning av Ändra Feed är att skapa en realtid **materialiserad vy** av en behållare som det är intag av dokument. Den materialiserade vybehållaren innehåller samma data men partitioneras för effektiva läsningar, vilket gör programmet både läsa och skriva effektivt.

Arbetet med att hantera förändringsflödeshändelser tas till stor del om hand av biblioteket Ändra feedprocessor som är inbyggt i SDK. Det här biblioteket är tillräckligt kraftfullt för att distribuera händelser i ändringsflödet mellan flera arbetare, om så önskas. Allt du behöver göra är att ge biblioteket Ändra feed en motringning.

Det här enkla exemplet visar Ändra feedprocessorbibliotek med en enda arbetare som skapar och tar bort dokument från en materialiserad vy.

## <a name="setup"></a>Installation

Om du inte redan har gjort det, klona appexemplet repo:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> Du kan välja att arbeta igenom denna snabbstart med Java SDK 4.0 eller Java SDK 3.7.0. **Om du vill använda Java SDK 3.7.0, ```git checkout SDK3.7.0```i terminaltypen **. Annars kan du ```master``` stanna kvar på grenen, som standard till Java SDK 4.0.

Öppna en terminal i arkivet. Skapa appen genom att köra

```bash
mvn clean package
```

## <a name="walkthrough"></a>Genomgång

1. Som en första kontroll bör du ha ett Azure Cosmos DB-konto. Öppna **Azure Portal** i webbläsaren, gå till ditt Azure Cosmos DB-konto och navigera i den vänstra rutan till **Data Explorer**.

    ![Azure Cosmos DB-konto](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Kör appen i terminalen med följande kommando:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Tryck på returen när du ser

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    gå sedan tillbaka till Azure Portal Data Explorer i din webbläsare. Du kommer att se en databas **GroceryStoreDatabase** har lagts till med tre tomma behållare: 

    * **InventoryContainer** - Lagerposten för vårt exempel livsmedelsbutik, ```id``` partitionerad på objekt som är en UUID.
    * **InventoryContainer-pktype** - En materialiserad vy av lagerposten, optimerad för frågor över artikel```type```
    * **InventoryContainer-leases** - En lånebehållare behövs alltid för ändringsfeed; arrende spåra appens framsteg när du läser ändringsflödet.


    ![Tomma behållare](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. I terminalen bör du nu se en uppmaning

    ```bash
    Press enter to start creating the materialized view...
    ```

    Tryck på Retur. Nu kommer följande kodblock att köra och initiera ändringsflödesprocessorn på en annan tråd: 


    **Java SDK 4,0**
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

    **Java SDK 3.7.0**
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

    ```"SampleHost_1"```är namnet på processorarbetaren Ändra feed. ```changeFeedProcessorInstance.start()```är vad som faktiskt startar Change Feed-processorn.

    Gå tillbaka till Azure Portal Data Explorer i din webbläsare. Klicka på **artiklar** under behållaren **InventoryContainer-leases** för att se dess innehåll. Du kommer att se att Ändra feedprocessor har fyllt i lånebehållaren, dvs. ```SampleHost_1``` **InventoryContainer**

    ![Lån](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Tryck enter igen i terminalen. Detta utlöser 10 dokument som ska infogas i **InventoryContainer**. Varje dokumentinfogning visas i ändringsflödet som JSON. Följande motringningskod hanterar dessa händelser genom att spegla JSON-dokumenten till en materialiserad vy:

    **Java SDK 4,0**
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

    **Java SDK 3.7.0**
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

1. Tillåt att koden körs 5-10 sek. Gå sedan tillbaka till Azure Portal Data Explorer och navigera till **InventoryContainer > objekt**. Du bör se att artiklar infogas i lagerbehållaren. notera partitionsnyckeln (```id```).

    ![Foderbehållare](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Nu i Data Explorer navigera till **InventoryContainer-pktype > objekt**. Det här är den materialiserade vyn – artiklarna i den här behållarspegeln **InventoryContainer** eftersom de infogades programmässigt av Ändringsflödet. Notera partitionsnyckeln (```type```). Så den här materialiserade vyn är ```type```optimerad för frågor som filtrerar över , vilket ```id```skulle vara ineffektivt på **InventoryContainer** eftersom den är partitionerad på .

    ![Materialiserad vy](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Vi kommer att ta bort ett dokument från både **InventoryContainer** och **InventoryContainer-pktype** med bara ett enda ```upsertItem()``` samtal. Ta först en titt på Azure Portal Data Explorer. Vi tar bort dokumentet ```/type == "plums"```som; den är omgiven i rött under

    ![Materialiserad vy](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Tryck enter igen för ```deleteDocument()``` att anropa funktionen i exempelkoden. Den här funktionen, som visas nedan, upserts en ny version av dokumentet med ```/ttl == 5```, som anger dokumentet Time-To-Live (TTL) till 5sec. 
    
    **Java SDK 4,0**
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

    **Java SDK 3.7.0**
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

    Ändringsflödet ```feedPollDelay``` är inställt på 100ms. Därför svarar Ändringsflödet på den här ```updateInventoryTypeMaterializedView()``` uppdateringen nästan omedelbart och anrop som visas ovan. Det sista funktionsanropet kommer att uppdatera det nya dokumentet med TTL på 5 sek i **InventoryContainer-pktype**.

    Effekten är att dokumentet efter cirka 5 sekunder upphör att gälla och tas bort från båda behållarna.

    Den här proceduren är nödvändig eftersom ändringsflödet endast utfärdar händelser vid infogning eller uppdatering av objektet, inte vid borttagning av objekt.

1. Tryck på Ange en gång till för att stänga programmet och rensa upp dess resurser.
