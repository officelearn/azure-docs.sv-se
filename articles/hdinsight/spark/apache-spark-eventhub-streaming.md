---
title: 'Självstudie: Azure Event Hubs data & Apache Spark – HDInsight'
description: Självstudie – Anslut Apache Spark i Azure HDInsight till Azure Event Hubs och bearbeta strömmande data.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/17/2019
ms.openlocfilehash: 0b24d1b0215564fb9f6063d4a2d091bb7a9a1c3e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494627"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Självstudie: bearbeta tweets med Azure Event Hubs och Apache Spark i HDInsight

I den här självstudien får du lära dig hur du skapar ett [Apache Spark](https://spark.apache.org/) strömnings program för att skicka tweets till en Azure Event Hub och skapa ett annat program för att läsa tweets från händelsehubben. En detaljerad förklaring av Spark-direktuppspelning finns [Apache Spark strömnings översikt](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight har samma strömnings funktioner till ett Spark-kluster på Azure.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skicka meddelanden till Azure Event Hub
> * Läsa meddelanden från Azure Event Hub

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Se [skapa ett Apache Spark-kluster](./apache-spark-jupyter-spark-sql-use-portal.md).

* Kunskaper om Jupyter Notebooks med Spark på HDInsight. Mer information finns i [läsa in data och köra frågor med Apache Spark på HDInsight](./apache-spark-load-data-run-query.md).

* Ett [Twitter-konto](https://twitter.com/i/flow/signup) och välbekanthet med Twitter.

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

Om du vill få en dataström med tweets skapar du ett program i Twitter. Följ anvisningarna skapa ett Twitter-program och skriv ned de värden som du behöver för att slutföra den här självstudien.

1. Bläddra till [Twitter-programhantering](https://apps.twitter.com/).

1. Välj **skapa en app**.

1. Ange följande obligatoriska värden:

    |Egenskap |Värde |
    |---|---|
    |Appnamn|Ange program namnet. Det värde som används för den här självstudien är **HDISparkStreamApp0423**. Det här namnet måste vara ett unikt namn.|
    |Programbeskrivning|Ange en kort beskrivning av programmet. Det värde som används för den här självstudien är **ett enkelt program för HDInsight Spark-direktuppspelning**.|
    |Webbplats-URL|Ange programmets webbplats. Det behöver inte vara en giltig webbplats.  Det värde som används för den här självstudien är `http://www.contoso.com`.|
    |Berätta för oss hur den här appen kommer att användas|Endast i testnings syfte. Skapa ett Apache Spark streaming-program för att skicka tweets till en Azure Event Hub.|

1. Välj **Skapa**.

1. Från popup-fönstret **granska våra utvecklings villkor** väljer du **skapa**.

1. Välj fliken **nycklar och tokens** .

1. Under **åtkomsttoken & åtkomst token Secret**väljer du **skapa**.

1. Skriv ned följande fyra värden som nu visas på sidan för senare användning:

    - **Konsument nyckel (API-nyckel)**
    - **Konsument hemlighet (nyckel för API-hemlighet)**
    - **Åtkomsttoken**
    - **Hemlighet för åtkomsttoken**

## <a name="create-an-azure-event-hubs-namespace"></a>Skapa en Event Hubs-namnrymd

Du använder den här händelsehubben för att lagra tweets.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På den vänstra menyn navigerar du till **alla tjänster** > **Sakernas Internet** - > **Event Hubs**.  

    ![Exempel på Skapa händelsehubben för Spark streaming](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Exempel på Skapa händelsehubben för Spark streaming")

1. Välj **+ Lägg till**.

1. Ange följande värden för det nya Event Hubs namn rymden:

    |Egenskap |Värde |
    |---|---|
    |Namn|Ange ett namn för händelsehubben.  Det värde som används för den här självstudien är **myeventhubns20180403**.|
    |Prisnivå|Välj **standard**.|
    |Prenumeration|Välj lämplig prenumeration.|
    |Resursgrupp|Välj en befintlig resurs grupp i den nedrullningsbara listan eller Välj **Skapa ny** för att skapa en ny resurs grupp.|
    |Plats|Välj samma **plats** som Apache Spark klustret i HDInsight för att minska svars tid och kostnader.|
    |Aktivera automatisk ökning (valfritt) |Automatisk ökning skalar automatiskt antalet data flödes enheter som tilldelats Event Hubs namn området när trafiken överskrider kapaciteten hos de data flödes enheter som tilldelats den.  |
    |Högsta antal data flödes enheter automatiskt (valfritt)|Det här skjutreglaget visas bara om du markerar **Aktivera automatisk**ökning.  |

    ![Ange ett Event Hub-namn för Spark streaming-exempel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Ange ett Event Hub-namn för Spark streaming-exempel")

1. Välj **skapa** för att skapa namn området.  Distributionen kommer att slutföras på några minuter.

## <a name="create-an-azure-event-hub"></a>Skapa en Azure Event Hub

Skapa en Event Hub när Event Hubs namn rymden har distribuerats.  Från portalen:

1. På den vänstra menyn navigerar du till **alla tjänster** > **Sakernas Internet** - > **Event Hubs**.

1. Välj din Event Hubs namn område i listan.

1. På sidan **Event Hubs namnrymd** väljer du **+ Event Hub**.  

1. Ange följande värden på sidan **skapa** händelsehubben:

    - **Namn**: ge händelsehubben ett namn.

    - **Antal partitioner**: 10.  

    - **Kvarhållning av meddelande**: 1.

      ![Ange information om händelsehubben för Spark streaming-exempel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Ange information om händelsehubben för Spark streaming-exempel")

1. Välj **Skapa**.  Distributionen bör slutföras inom några sekunder och du kommer tillbaka till sidan Event Hubs namn område.

1. Under **Inställningar**väljer du **principer för delad åtkomst**.

1. Välj **RootManageSharedAccessKey**.

     ![Ange Event Hub-principer för Spark streaming-exemplet](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Ange Event Hub-principer för Spark streaming-exemplet")

1. Spara värdena för **primär nyckel** och **anslutnings sträng – primär nyckel** för senare användning i självstudien.

     ![Visa princip nycklar för Event Hub för exemplet på Spark-direktuppspelning](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Visa princip nycklar för Event Hub för exemplet på Spark-direktuppspelning")

## <a name="send-tweets-to-the-event-hub"></a>Skicka tweets till händelsehubben

1. Navigera till `https://CLUSTERNAME.azurehdinsight.net/jupyter` där `CLUSTERNAME` är namnet på ditt Apache Spark-kluster. Skapa en Jupyter Notebook och ge den namnet **SendTweetsToEventHub**.

1. Kör följande kod för att lägga till de externa Apache maven-biblioteken:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

1. Redigera koden nedan genom att ersätta `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>`och `<TOKEN SECRET>` med lämpliga värden. Kör den redigerade koden för att skicka tweets till händelsehubben:

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._

    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build

    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }

    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"

    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)

    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

1. Öppna Event Hub i Azure Portal.  I **översikten**visas några diagram som visar meddelanden som skickas till händelsehubben.

## <a name="read-tweets-from-the-event-hub"></a>Läs tweets från händelsehubben

Skapa en annan Jupyter-anteckningsbok och ge den namnet **ReadTweetsFromEventHub**.

1. Kör följande kod för att lägga till ett externt Apache maven-bibliotek:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. Redigera koden nedan genom att ersätta `<Event hub name>`och `<Event hub namespace connection string>` med lämpliga värden. Kör den redigerade koden för att läsa tweets från händelsehubben:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build

    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Med HDInsight lagras dina data i Azure Storage eller Azure Data Lake Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Om du planerar att arbeta med nästa självstudie direkt kanske du vill behålla klustret, annars kan du gå vidare och ta bort klustret.

Öppna klustret i Azure Portal och välj **Ta bort**.

![HDInsight Azure Portal ta bort kluster](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

Du kan också välja det resursgruppnamn som ska öppna resursgruppsidan och sedan välja **Ta bort resursgrupp**. När du tar bort resursgruppen tar du bort både HDInsight Spark-klustret och standardkontot för lagring.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar ett Apache Spark strömnings program för att skicka tweets till en Azure Event Hub och skapat ett annat program för att läsa tweets från händelsehubben.  Gå vidare till nästa artikel för att se hur du kan skapa ett maskin inlärnings program.

> [!div class="nextstepaction"]
> [Skapa ett Machine Learning-program](./apache-spark-ipython-notebook-machine-learning.md)
