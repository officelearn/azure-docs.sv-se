---
title: Bearbeta händelser för Apache Kafka med hjälp av Stream analytics - Azure Event Hubs | Microsoft Docs
description: Den här artikeln visar hur du bearbetar Kafka-händelser som har samlats in via händelsehubbar med hjälp av Azure Stream Analytics
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: b6bba4ed45530ba66a1adde274022a80091cd199
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539165"
---
# <a name="process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Bearbeta Apache Kafka för Event Hubs-händelser med Stream-analys 
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
* En **Azure Storage-konto**. Om du inte har någon, [skapar ett](../storage/common/storage-quickstart-create-account.md) innan du fortsätter. Stream Analytics-jobb i den här genomgången lagrar utdata i Azure blob storage. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Skapa ett Kafka-aktiverat Event Hubs-namnområde

1. Logga in på den [Azure-portalen](https://portal.azure.com), och klicka på **skapa en resurs** på upp till vänster på skärmen.
2. Sök efter **Händelsehubbar** och väljer de alternativ som visas här:
    
    ![Sök efter Event Hubs på portalen](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. På den **Händelsehubbar** väljer **skapa**.
4. På den **skapa Namespace** gör du följande åtgärder: 
    1. Ange ett unikt **namn** för namnområdet. 
    2. Välj en **prisnivån**. 
    3. Välj **aktivera Kafka**. Det här steget är en **viktiga** steg. 
    4. Välj din **prenumeration** som du vill att händelsehubbens namnområde som ska skapas. 
    5. Skapa en ny **resursgrupp** eller välj en befintlig resursgrupp. 
    6. Välj en **plats**. 
    7. Klicka på **Skapa**.
    
        ![Skapa ett namnområde](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. I den **meddelande**väljer den **resursgruppens namn**. 

    ![Skapa ett namnområde](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Välj den **händelsehubbnamnområde** i resursgruppen. 
2. När namnområdet har skapats kan du välja **principer för delad åtkomst** under **inställningar**.

    ![Klicka på Policyer för delad åtkomst](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Du kan välja standardprincipen **RootManageSharedAccessKey** eller lägga till en ny princip. Klicka på principnamnet och kopiera den **anslutningssträngen**. Du kan använda anslutningssträngen för att konfigurera Kafka-klienten. 
    
    ![Välj en princip](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Nu kan du strömma händelser från program som använder Kafka-protokollet till Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Skicka meddelanden med Kafka i Event Hubs

1. Klona den [Azure Event Hubs för Kafka lagringsplats](https://github.com/Azure/azure-event-hubs-for-kafka) till din dator.
2. Navigera till mappen: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Uppdatera konfigurationsinformationen för producenten i `src/main/resources/producer.config`. Ange den **namn** och **anslutningssträngen** för den **händelsehubbnamnområde**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Gå till `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`, och öppna **TestDataReporter.java** fil i ett redigeringsprogram. 
6. Kommentera ut följande rad med kod:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Lägg till följande kodrad i stället för kommenterade koden: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Den här koden skickar händelsedata i **JSON** format. När du konfigurerar indata för ett Stream Analytics-jobb kan ange du JSON som för indata. 
7. **Kör producenten** och ström till Kafka-aktiverade Event Hubs. På en Windows-dator, när du använder en **Node.js-kommandotolken**, växla till den `azure-event-hubs-for-kafka/quickstart/java/producer` mapp innan du kör dessa kommandon. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Kontrollera den händelsehubben tar emot data

1. Välj **Händelsehubbar** under **ENTITETER**. Kontrollera att du ser en händelsehubb med namnet **testa**. 

    ![Händelsehubb – testa](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Kontrollera att du ser meddelanden som kommer till händelsehubben. 

    ![Event hub - meddelanden](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Bearbeta händelsedata med hjälp av ett Stream Analytics-jobb
I det här avsnittet skapar du ett Azure Stream Analytics-jobb. Kafka-klienten skickar händelser till event hub. Du kan skapa ett Stream Analytics-jobb som tar händelsedata som indata och utdata till Azure blob storage. Om du inte har en **Azure Storage-konto**, [skapar ett](../storage/common/storage-quickstart-create-account.md).

Frågan i Stream Analytics-jobb passerar data utan att behöva genomföra några analytics. Du kan skapa en fråga som omvandlar indata till utdata i ett annat format eller med vinst insikter.  

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb 

1. Välj **+ skapa en resurs** i den [Azure-portalen](https://portal.azure.com).
2. Välj **Analytics** i den **Azure Marketplace** menyn och välj **Stream Analytics-jobbet**. 
3. På den **nya Stream Analytics** gör du följande åtgärder: 
    1. Ange en **namn** för jobbet. 
    2. Välj din **prenumeration**.
    3. Välj **Skapa nytt** för den **resursgrupp** och ange namnet. Du kan också **använder en befintlig** resursgrupp. 
    4. Välj en **plats** för jobbet.
    5. Välj **skapa** att skapa jobbet. 

        ![Nytt Stream Analytics-jobb](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurera jobbindata

1. I meddelandet, väljer **gå till resurs** att se den **Stream Analytics-jobbet** sidan. 
2. Välj **indata** i den **JOBBTOPOLOGI** avsnitt i den vänstra menyn.
3. Välj **Lägg till strömindata**, och välj sedan **Event Hub**. 

    ![Lägg till händelsehubb som indata](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. På den **Event Hub indata** konfiguration gör du följande åtgärder: 

    1. Ange en **alias** för indata. 
    2. Välj din **Azure-prenumeration**.
    3. Välj den **händelsehubbnamnområde** skapade tidigare. 
    4. Välj **testa** för den **händelsehubb**. 
    5. Välj **Spara**. 

        ![Event hub inkommande konfiguration](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurera jobbutdata 

1. Välj **utdata** i den **JOBBTOPOLOGI** avsnittet på menyn. 
2. Välj **+ Lägg till** verktygsfältet och välj **Blob-lagring**
3. Gör följande på inställningssidan för Blob storage-utdata: 
    1. Ange en **alias** för utdata. 
    2. Välj din Azure-**prenumeration**. 
    3. Välj din **Azure Storage-konto**. 
    4. Ange en **namnet för behållaren** som lagrar utdata från Stream Analytics-frågan.
    5. Välj **Spara**.

        ![Utdata-konfigurationen för BLOB-lagring](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definiera en fråga
När du har en Stream Analytics-jobbkonfiguration för att läsa en inkommande dataström är nästa steg att skapa en omvandling som analyserar data i realtid. Du definierar transformationsfrågan med [Stream Analytics-frågespråket](https://msdn.microsoft.com/library/dn834998.aspx). I den här genomgången kan du definiera en fråga som skickas via data utan att utföra en transformering.

1. Välj **fråga**.
2. I frågefönstret ersätter `[YourOutputAlias]` med utdataalias som du skapade tidigare.
3. Ersätt `[YourInputAlias]` med inmatat alias som du skapade tidigare. 
4. Välj **Spara** i verktygsfältet. 

    ![Söka i data](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

1. Välj **översikt** på den vänstra menyn. 
2. Välj **starta**. 

    ![Start-menyn](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. På den **startjobb** väljer **starta**. 

    ![Startsida för jobbet](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Vänta tills status för jobbet ändras från **startar** till **kör**. 

    ![Jobbstatus – som körs](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testa scenariot
1. Kör den **Kafka-producent** igen för att skicka händelser till event hub. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Kontrollera att du ser **utdata** har genererats i den **Azure blobblagring**. Du ser en JSON-fil i behållaren med 100 rader som ser ut som följande exempel rader: 

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
