---
title: Azure Event Hubs – Bearbeta Apache Kafka-händelser
description: 'Självstudiekurs: Den här artikeln visar hur du bearbetar Kafka-händelser som intas via händelsehubbar med hjälp av Azure Stream Analytics'
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
ms.openlocfilehash: 4cabd63dc39590a9bf728528f3e7b3aa5d3f6b04
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521762"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Självstudiekurs: Bearbeta Apache Kafka för händelser i händelsehubbar med hjälp av Stream-analyser 
Den här artikeln visar hur du strömmar data till eventhubbar och bearbetar dem med Azure Stream Analytics. Den går igenom följande steg: 

1. Skapa ett Event Hubs-namnområde.
2. Skapa en Kafka-klient som skickar meddelanden till händelsehubben.
3. Skapa ett Stream Analytics-jobb som kopierar data från händelsehubben till en Azure-bloblagring. 

Du behöver inte ändra dina protokollklienter eller köra egna kluster när du använder Kafka-slutpunkten som visas av en händelsehubb. Azure Event Hubs stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html) och uppåt. 


## <a name="prerequisites"></a>Krav

För att kunna slutföra den här snabbstarten behöver du följande:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Ladda ned](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett Maven-binärarkiv.
* [Git](https://www.git-scm.com/)
* Ett **Azure Storage-konto**. Om du inte har en, [skapa en](../storage/common/storage-account-create.md) innan du fortsätter vidare. Stream Analytics-jobbet i den här genomgången lagrar utdata i en Azure-bloblagring. 


## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde
När du skapar **ett** standardnivånamnområde för händelsehubbar aktiveras Kafka-slutpunkten för namnområdet automatiskt. Du kan strömma händelser från dina program som använder Kafka-protokollet till standardnivåhändelsehubbar. Följ steg-för-steg-instruktioner i [skapa en händelsehubb med Azure-portalen](event-hubs-create.md) för att skapa ett **standardnivånamnområde** för eventhubbar. 

> [!NOTE]
> Event Hubs för Kafka är endast tillgängligt på **standard-** och **dedikerade** nivåer. Den **grundläggande** nivån stöder inte Kafka på Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Skicka meddelanden med Kafka i eventhubbar

1. Klona [Azure Event Hubs för Kafka-databasen](https://github.com/Azure/azure-event-hubs-for-kafka) till din dator.
2. Navigera till mappen: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Uppdatera konfigurationsinformationen för `src/main/resources/producer.config`tillverkaren i . Ange **namn** och **anslutningssträng** för **namnområdet för händelsehubben**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navigera `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`till och öppna **filen TestDataReporter.java** i valfri redigerare. 
6. Kommentera följande kodrad:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Lägg till följande kodrad i stället för den kommenterade koden: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Den här koden skickar händelsedata i **JSON-format.** När du konfigurerar indata för ett Stream Analytics-jobb anger du JSON som format för indata. 
7. **Kör producenten** och strömma till Event Hubs. Växla till `azure-event-hubs-for-kafka/quickstart/java/producer` mappen innan du kör dessa kommandon när du använder **kommandotolken Node.js**på en Windows-dator. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Kontrollera att händelsehubben tar emot data

1. Välj **händelsehubbar** under **ENTITETER**. Bekräfta att du ser en händelsehubb med namnet **test**. 

    ![Händelsenav - test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Bekräfta att du ser meddelanden som kommer in till händelsehubben. 

    ![Händelsenav - meddelanden](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Bearbeta händelsedata med hjälp av ett Stream Analytics-jobb
I det här avsnittet skapar du ett Azure Stream Analytics-jobb. Kafka-klienten skickar händelser till händelsehubben. Du skapar ett Stream Analytics-jobb som tar händelsedata som indata och matar ut dem till en Azure-bloblagring. Om du inte har ett **Azure Storage-konto** [skapar du ett](../storage/common/storage-account-create.md).

Frågan i Stream Analytics-jobbet skickar data utan att utföra några analyser. Du kan skapa en fråga som omvandlar indata för att producera utdata i ett annat format eller med förvärvade insikter.  

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb 

1. Välj **+ Skapa en resurs** i [Azure-portalen](https://portal.azure.com).
2. Välj **Analys** på **Azure Marketplace-menyn** och välj Stream **Analytics-jobb**. 
3. Gör följande på sidan **Ny streamanalys:** 
    1. Ange ett **namn** på jobbet. 
    2. Välj **din prenumeration**.
    3. Välj **Skapa nytt** för **resursgruppen** och ange namnet. Du kan också **använda en befintlig** resursgrupp. 
    4. Välj en **plats** för jobbet.
    5. Välj **Skapa** om du vill skapa jobbet. 

        ![Nytt Stream Analytics-jobb](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurera jobbindata

1. I meddelandemeddelandet väljer du **Gå till resurs** för att se **jobbsidan För Stream Analytics.** 
2. Välj **Indata** i avsnittet **JOBBTOPOLOGI** på den vänstra menyn.
3. Välj **Lägg till indata för ström**och välj sedan **Händelsehubb**. 

    ![Lägga till händelsehubb som indata](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Gör följande åtgärder på sidan **Konfiguration av händelsehubbens indatakonfiguration:** 

    1. Ange ett **alias** för indata. 
    2. Välj din **Azure-prenumeration**.
    3. Välj **det namnområde för händelsehubben** som du skapade tidigare. 
    4. Välj **test** för **händelsehubben**. 
    5. Välj **Spara**. 

        ![Konfiguration av indata för händelsehubb](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurera jobbutdata 

1. Välj **Utdata** i avsnittet **JOBBTOPOLOGI** på menyn. 
2. Välj **+ Lägg till** i verktygsfältet och välj **Blob-lagring**
3. Gör följande åtgärder på sidan Inställningar för Blob-lagringsutdata: 
    1. Ange ett **alias** för utdata. 
    2. Välj din **Azure-prenumeration**. 
    3. Välj ditt **Azure Storage-konto**. 
    4. Ange ett **namn på behållaren** som lagrar utdata från Stream Analytics-frågan.
    5. Välj **Spara**.

        ![Konfiguration av Blob Storage-utdata](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definiera en fråga
När du har en Stream Analytics-jobbkonfiguration för att läsa en inkommande dataström är nästa steg att skapa en omvandling som analyserar data i realtid. Du definierar transformationsfrågan med [Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). I den här genomgången definierar du en fråga som skickar genom data utan att utföra någon omvandling.

1. Välj **Fråga**.
2. Ersätt `[YourOutputAlias]` med utdataalias som du skapade tidigare i frågefönstret.
3. Ersätt `[YourInputAlias]` med indataalias som du skapade tidigare. 
4. Välj **Spara** i verktygsfältet. 

    ![Söka i data](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

1. Välj **Översikt** på den vänstra menyn. 
2. Välj **Starta**. 

    ![Start-menyn](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. På sidan **Startjobb** väljer du **Start**. 

    ![Sidan Starta jobb](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Vänta tills statusen för jobbet ändras från **Start** till **körning**. 

    ![Jobbstatus - körs](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testa scenariot
1. Kör **Kafka-producenten** igen för att skicka händelser till händelsehubben. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Bekräfta att du ser **att utdata** genereras i **Azure blob storage**. Du ser en JSON-fil i behållaren med 100 rader som ser ut som följande exempelrader: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Azure Stream Analytics-jobbet tog emot indata från händelsehubben och lagrade den i Azure-blob-lagringen i det här scenariot. 



## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att strömma till eventhubbar utan att ändra dina protokollklienter eller köra egna kluster. Läs mer om Event Hubs och Event Hubs för Kafka i följande ämne:  

- [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Skapa en händelsehubb](event-hubs-create.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka Connect med ett händelsenav](event-hubs-kafka-connect-tutorial.md)
- [Ansluta Akka-strömmar till ett händelsenav](event-hubs-kafka-akka-streams-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) 
