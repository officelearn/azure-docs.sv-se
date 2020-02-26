---
title: Azure Event Hubs process Apache Kafka händelser
description: 'Självstudie: den här artikeln visar hur du bearbetar Kafka-händelser som matas in via Event Hub med hjälp av Azure Stream Analytics'
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: d7b060a2b35ca41bf87b69be706284174d7b1012
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587166"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Självstudie: bearbeta Apache Kafka för Event Hubs händelser med Stream Analytics 
Den här artikeln visar hur du strömma data till Kafka-aktiverade Event Hubs och bearbeta dem med Azure Stream Analytics. Vi går igenom följande steg: 

1. Skapa en Kafka aktiverat Event Hubs-namnområdet.
2. Skapa en Kafka-klient som skickar meddelanden till event hub.
3. Skapa ett Stream Analytics-jobb som kopierar data från event hub i Azure blob storage. 

Du behöver inte ändra protokoll-klienter eller kör ditt eget kluster när du använder Kafka-slutpunkt som exponeras av en event hub. Azure Event Hubs stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html) och senare. 


## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här snabbstarten behöver du följande:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Ladda ned](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett Maven-binärarkiv.
* [Git](https://www.git-scm.com/)
* Ett **Azure Storage konto**. Om du inte har ett kan du [skapa en](../storage/common/storage-account-create.md) innan du fortsätter. Stream Analytics-jobb i den här genomgången lagrar utdata i Azure blob storage. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Skapa ett Kafka-aktiverat Event Hubs-namnområde
När du skapar en **standard** nivå Event Hubs-namnrymd aktive ras Kafka-slutpunkten för namn området automatiskt. Du kan strömma händelser från dina program som använder Kafka-protokollet till standard nivå Event Hubs. Följ steg-för-steg-instruktionerna i [skapa en Event Hub med Azure Portal](event-hubs-create.md) för att skapa ett **standard** -nivå Event Hubs-namnområde. 

> [!NOTE]
> Event Hubs för Kafka är bara tillgängligt på **standard** -och **dedikerade** nivåer. **Basic** -nivån har inte stöd för Kafka på Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Skicka meddelanden med Kafka i Event Hubs

1. Klona [Azure-Event Hubs för Kafka-lagringsplatsen](https://github.com/Azure/azure-event-hubs-for-kafka) till din dator.
2. Navigera till mappen: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Uppdatera konfigurations detaljerna för producenten i `src/main/resources/producer.config`. Ange **namn** och **anslutnings sträng** för **Event Hub-namnområdet**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Gå till `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`och öppna **TestDataReporter. java** -fil i valfritt redigerings program. 
6. Kommentera ut följande rad med kod:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Lägg till följande kodrad i stället för kommenterade koden: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Den här koden skickar händelse data i **JSON** -format. När du konfigurerar indata för ett Stream Analytics-jobb kan ange du JSON som för indata. 
7. **Kör Producer** och Stream i Kafka-aktiverade Event Hubs. När du använder en **Node. js-kommandotolk**på en Windows-dator växlar du till `azure-event-hubs-for-kafka/quickstart/java/producer` mapp innan du kör kommandona. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Kontrollera den händelsehubben tar emot data

1. Välj **Event Hubs** under **entiteter**. Bekräfta att du ser en Event Hub med namnet **test**. 

    ![Händelsehubb – testa](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Kontrollera att du ser meddelanden som kommer till händelsehubben. 

    ![Event hub - meddelanden](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Bearbeta händelsedata med hjälp av ett Stream Analytics-jobb
I det här avsnittet skapar du ett Azure Stream Analytics-jobb. Kafka-klienten skickar händelser till event hub. Du kan skapa ett Stream Analytics-jobb som tar händelsedata som indata och utdata till Azure blob storage. Om du inte har ett **Azure Storage konto** [skapar du ett](../storage/common/storage-account-create.md).

Frågan i Stream Analytics-jobb passerar data utan att behöva genomföra några analytics. Du kan skapa en fråga som omvandlar indata till utdata i ett annat format eller med vinst insikter.  

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb 

1. Välj **+ skapa en resurs** i [Azure Portal](https://portal.azure.com).
2. Välj **analys** på menyn för **Azure Marketplace** och välj **Stream Analytics jobb**. 
3. Utför följande åtgärder på sidan **ny Stream Analytics** : 
    1. Ange ett **namn** för jobbet. 
    2. Välj din **prenumeration**.
    3. Välj **Skapa ny** för **resurs gruppen** och ange namnet. Du kan också **använda en befintlig** resurs grupp. 
    4. Välj en **plats** för jobbet.
    5. Välj **skapa** för att skapa jobbet. 

        ![Nytt Stream Analytics-jobb](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurera jobbindata

1. I aviserings meddelandet väljer du **gå till resurs** för att se sidan **Stream Analytics jobb** . 
2. Välj **indata** i **jobb sto pol** section på den vänstra menyn.
3. Välj **Lägg till Stream-indata**och välj sedan **Event Hub**. 

    ![Lägg till händelsehubb som indata](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Gör följande på sidan konfiguration av **Event Hub-ingångs** konfiguration: 

    1. Ange ett **alias** för indatamängden. 
    2. Välj din **Azure-prenumeration**.
    3. Välj den **Event Hub-namnrymd** som du skapade tidigare. 
    4. Välj **test** för **händelsehubben**. 
    5. Välj **Spara**. 

        ![Event hub inkommande konfiguration](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurera jobbutdata 

1. Välj **utdata** i avsnittet **jobb TOPOLOGY** på menyn. 
2. Välj **+ Lägg till** i verktygsfältet och välj **Blob Storage**
3. Gör följande på inställningssidan för Blob storage-utdata: 
    1. Ange ett **alias** för utdata. 
    2. Välj din Azure-**prenumeration**. 
    3. Välj ditt **Azure Storage-konto**. 
    4. Ange ett **namn för den behållare** som lagrar utdata från den Stream Analytics frågan.
    5. Välj **Spara**.

        ![Utdata-konfigurationen för BLOB-lagring](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definiera en fråga
När du har en Stream Analytics-jobbkonfiguration för att läsa en inkommande dataström är nästa steg att skapa en omvandling som analyserar data i realtid. Du definierar transformationsfrågan med [Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). I den här genomgången kan du definiera en fråga som skickas via data utan att utföra en transformering.

1. Välj **fråga**.
2. I frågefönstret ersätter du `[YourOutputAlias]` med aliaset som du skapade tidigare.
3. Ersätt `[YourInputAlias]` med det angivna aliaset som du skapade tidigare. 
4. Välj **Spara** i verktygsfältet. 

    ![Fråga](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

1. Välj **Översikt** på den vänstra menyn. 
2. Välj **Starta**. 

    ![Start-menyn](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. På sidan **starta jobb** väljer du **Start**. 

    ![Startsida för jobbet](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Vänta tills jobbets status ändras från att **börja** **köras**. 

    ![Jobbstatus – som körs](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testa scenariot
1. Kör **Kafka-producenten** igen för att skicka händelser till händelsehubben. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Bekräfta att du ser **utdata** som genereras i **Azure Blob Storage**. Du ser en JSON-fil i behållaren med 100 rader som ser ut som följande exempel rader: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Azure Stream Analytics-jobbet togs emot indata från händelsehubben och lagras i Azure blob storage i det här scenariot. 



## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du strömmar till Kafka-aktiverade Event Hubs utan att ändra dina protokollklienter och utan att köra dina egna kluster. Läs mer om Event Hubs och Event Hubs för Kafka i följande ämne:  

- [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Så skapar du Kafka-aktiverade händelsehubbar](event-hubs-create-kafka-enabled.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en asynkron Kafka-meddelandekö i en Kafka-aktiverad händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en Kafka-aktiverad händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en Kafka-aktiverad händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka Connect med en Kafka-aktiverad händelsehubb](event-hubs-kafka-connect-tutorial.md)
- [Ansluta Akka Streams till en Kafka-aktiverad händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) 
