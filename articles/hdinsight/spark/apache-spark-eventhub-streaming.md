---
title: 'Självstudie: Bearbeta data från Azure Event Hubs med Apache Spark i Azure HDInsight '
description: Anslut Apache Spark i Azure HDInsight till Azure Event Hubs och bearbeta strömmande data.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: eb2145d2e7b787bafa0b546449282454f7059999
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283418"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Självstudie: Processen tweetar och använder Azure Event Hubs och Spark i HDInsight

I den här självstudien får du lära dig hur du skapar ett Apache Spark streaming-program att skicka tweets till en Azure event hub och skapa ett annat program att läsa tweets från event hub. En detaljerad förklaring av Spark-strömning finns [Apache Spark-strömning översikt](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight ger samma direktuppspelningsfunktioner till ett Spark-kluster på Azure.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skicka meddelanden till Azure Event Hub
> * Läsa meddelanden från Azure Event Hub

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* **Slutför artikeln [Självstudie: Läsa in data och köra frågor på ett Apache Spark-kluster i Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

Om du vill få en dataström med tweets skapar du ett program i Twitter. Följ anvisningarna skapa ett Twitter-program och anteckna värdena som du måste slutföra den här självstudien.

1. Bläddra till [Twitter-programhantering](https://apps.twitter.com/).
2. Välj **Skapa ny App**.
3. Ange följande värden:

    - Namn: Ange namnet på programmet. Det värde som används för den här självstudien är **HDISparkStreamApp0423**. Det här namnet måste vara ett unikt namn.
    - Beskrivning: Ange en kort beskrivning av programmet. Det värde som används för den här självstudien är **en enkel HDInsight Spark-strömning programmet**.
    - Webbplats: Ange programmets webbplats. Det behöver inte vara en giltig webbplats.  Det värde som används för den här självstudien är **http://www.contoso.com**.
    - Motringnings-URL: du kan lämna det tomt.

4. Välj **Ja, jag har läst och samtycker till Twitter-Developer-avtalet**, och välj sedan **skapa ditt Twitter-program**.
5. Välj den **nycklar och åtkomsttoken** fliken.
6. Välj **Skapa min åtkomsttoken** i slutet av sidan.
7. Anteckna följande värden på sidan.  Du behöver dessa värden senare i självstudien:

    - **Använda nyckeln (API-nyckel)**    
    - **Konsumenthemligheten (API-hemlighet)**  
    - **Åtkomsttoken**
    - **Åtkomsttokenhemligheten**   

## <a name="create-an-azure-event-hub"></a>Skapa en Azure-händelsehubb

Du kan använda den här händelsehubben för att lagra tweets.

1. Logga in på [Azure Portal](https://ms.portal.azure.com).
2. Välj **skapa en resurs** på upp till vänster på skärmen.
3. Välj **Internet of Things**och välj sedan **Händelsehubbar**.

    ![Skapa händelsehubb för Spark-strömning exempel](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "skapa händelsehubb för Spark-strömning exempel")
4. Ange följande värden för det nya namnområdet för händelsehubb:

    - **Namn på**: Ange ett namn för event hub.  Det värde som används för den här självstudien är **myeventhubns20180403**.
    - **Prisnivån**: Välj **Standard**.
    - **Resursgrupp**: har du möjlighet att skapa en ny eller Välj resursgrupp för Spark-klustret. 
    - **Plats**: Välj samma **plats** som Apache Spark-kluster i HDInsight för att minska fördröjningar och kostnader.

    ![Ange ett namn på händelsehubb för Spark-strömning exempel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "ger en event hub-namn för Spark-strömning exempel")
5. Välj **skapa** att skapa namnområdet.

7. Öppna händelsehubbens namnområde med hjälp av följande anvisningar:

    1. Från portalen, väljer **alla tjänster**.
    2. I filterrutan anger **händelsehubbar**.
    3. Välj det nyligen skapade namnområdet.
    4. Välj **+ Event Hub**.

8. Ange följande värden:

    - Namn: Ge ett namn för din Händelsehubb.
    - Partitionera antal: 10
    - Kvarhållning av meddelanden i: 1. 
   
    ![Ange event hub-information för Spark-strömning exempel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "ange event hub-information för Spark-strömning exempel")

9. Välj **Skapa**.
10. Välj **principer för delad åtkomst** för namnområde (Observera att det inte är åtkomstprinciper för event hub delad) och välj sedan **RootManageSharedAccessKey**.
    
     ![Ange Event Hub-principer för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "ange Event Hub-principer för Spark streaming exempel")

11. Spara värdena för **primärnyckel** och **anslutningssträng – primär nyckel** att använda senare i självstudien.

     ![Visa Event Hub principnycklar för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "visa Event Hub-principen nycklar för Spark direktuppspelade exempel")


## <a name="send-tweets-to-the-event-hub"></a>Skicka tweets till event hub

Du behöver skapa en Jupyter-anteckningsbok och ge den namnet **SendTweetsToEventHub**. 

1. Kör följande kod för att lägga till de externa Maven-bibliotek:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Kör följande kod för att skicka tweets till din event hub:

    ```
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

3. Öppna event hub i Azure-portalen.  På **översikt**, du kommer att se vissa diagram som visar meddelandena som skickas till händelsehubben.

## <a name="read-tweets-from-the-event-hub"></a>Läsa tweets från event hub

Du behöver skapa en annan Jupyter-anteckningsbok och ge den namnet **ReadTweetsFromEventHub**. 

1. Kör följande kod för att lägga till ett externt Maven-bibliotek:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Kör följande kod för att läsa tweets från event hub:

    ```
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

I HDInsight lagras dina data i Azure Storage eller Azure Data Lake Store för att du på ett säkert sätt ska kunna ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Om du planerar att arbeta med nästa självstudie direkt, kanske du vill behålla klustret, annars gå vidare och ta bort klustret.

Öppna klustret i Azure Portal och välj **Ta bort**.

![Ta bort HDInsight-kluster](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. När resursgruppen tas bort, tas även HDInsight Spark-klustret och standardkontot för lagring bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Läsa meddelande från en event hub.
Gå vidare till nästa artikel för att se du kan skapa en machine learning-program. 

> [!div class="nextstepaction"]
> [Skapa en machine learning-program](./apache-spark-ipython-notebook-machine-learning.md)


