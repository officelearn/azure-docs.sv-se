---
title: 'Självstudier: Bearbeta data från Azure Event Hubs med Apache Spark i Azure HDInsight | Microsoft Docs'
description: Ansluta Apache Spark i Azure HDInsight till Azure Event Hubs och bearbeta strömmande data.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 9b59f5d58234aaf8f8385f722d6659548e066933
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787257"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Självstudier: Processen tweets med Händelsehubbar i Azure och Spark i HDInsight

I kursen får du lära dig hur du skapar ett Apache Spark streaming programmet skickar tweets till en Azure event hub och skapa ett annat program för att läsa tweets från event hub. En detaljerad förklaring av Spark streaming finns [Apache Spark streaming översikt](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight ger samma funktioner för strömning för ett Spark-kluster i Azure.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skicka meddelanden till Azure Event Hub
> * Läsa meddelanden från Azure Event Hub

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* **Slutföra artikeln [Självstudier: Läs in data och köra frågor i ett Apache Spark-kluster i Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

Om du vill få en dataström med tweets skapar du ett program i Twitter. Följ anvisningarna skapa ett Twitter-program och anteckna värdena som du behöver den här kursen.

1. Bläddra till [Twitter programhantering](https://apps.twitter.com/).
2. Välj **Skapa ny App**.
3. Ange följande värden:

    - Namn: Ange namnet på programmet. Det värde som används för den här kursen är **HDISparkStreamApp0423**. Det här namnet måste vara ett unikt namn.
    - Beskrivning: Ange en kort beskrivning av programmet. Det värde som används för den här kursen är **en enkel HDInsight Spark streaming programmet**.
    - Webbplats: Ange programmets webbplats. Det behöver inte vara en giltig webbplats.  Det värde som används för den här kursen är **http://www.contoso.com**.
    - Motringning URL: du kan lämna det tomt.

4. Välj **Ja, jag har läst och accepterar avtalet Twitter Developer**, och välj sedan **skapa programmet Twitter**.
5. Välj den **nycklar och åtkomst-token** fliken.
6. Välj **skapa åtkomst-token** i slutet av sidan.
7. Skriv ned följande värden från sidan.  Du behöver dessa värden senare under kursen:

    - **Konsumentnyckel (API-nyckel)**    
    - **Konsumenten hemligheten (API hemliga)**  
    - **Åtkomst-Token**
    - **Åtkomst-Token hemlighet**   

## <a name="create-an-azure-event-hub"></a>Skapa en Azure-händelsehubb

Du kan använda den här händelsehubben för att lagra tweets.

1. Logga in på [Azure Portal](https://ms.portal.azure.com).
2. Välj **skapar du en resurs** på upp till vänster på skärmen.
3. Välj **Sakernas Internet**och välj **Händelsehubbar**.

    ![Skapa händelsehubb för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "skapa händelsehubb för Spark streaming exempel")
4. Ange följande värden för det nya event hub namnområdet:

    - **Namnet**: Ange ett namn för händelsehubben.  Det värde som används för den här kursen är **myeventhubns20180403**.
    - **Priset nivå**: Välj **Standard**.
    - **Resursgruppen**: har du möjlighet att skapa en ny eller välj resursgruppen för Spark-klustret. 
    - **Plats**: Välj samma **plats** som Apache Spark-kluster i HDInsight för att minska latensen och kostnader.

    ![Ange ett händelsenamn hubb Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "ger en händelsehubbens namn Spark streaming exempel")
5. Välj **skapa** att skapa namnområdet.

6. Öppna hubb händelsenamnrymden med hjälp av följande anvisningar:

    1. Portal, Välj **alla tjänster**.
    2. I filterrutan anger **händelsehubbar**.
    3. Dubbelklicka på namnområdet som du skapade.
    4. Välj **+ Händelsehubb**.

6. Markera den nyligen skapade namnområden i listan Händelsehubbar namnområde.      
5. Välj **Händelsehubbar**, och välj sedan **+ Event Hub** att skapa en ny Händelsehubb.
  

6. Ange följande värden:

    - Namn: Namnge din Event Hub.
    - Partitions-antal: 10
    - Meddelande kvarhållning: 1. 
   
    ![Ange händelseinformationen hubb för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "ange händelseinformationen hubb för Spark streaming exempel")

7. Välj **Skapa**.
8. Välj **principer för delad åtkomst** för namnområde (Observera inte är principer för händelsen hubb delad åtkomst) och välj sedan **RootManageSharedAccessKey**.
    
     ![Ange Event Hub principer för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "ange Event Hub principer för Spark streaming exempel")

9. Spara värdena för **primärnyckel** och **sträng-primära anslutningsnyckel** att använda senare under kursen.

     ![Visa Event Hub princip nycklar för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "View Event Hub princip nycklar för Spark streaming exempel")


## <a name="send-tweets-to-the-event-hub"></a>Skicka tweets till händelsehubben

Du måste skapa en Jupyter-anteckningsbok och ge den namnet **SendTweetsToEventHub**. 

1. Kör följande kod för att lägga till externa Maven-bibliotek:

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

3. Öppna händelsehubben i Azure-portalen.  På **översikt**, bör du se vissa diagram som visar meddelanden som skickas till händelsehubben.

## <a name="read-tweets-from-the-event-hub"></a>Läs tweets från event hub

Du måste skapa en annan Jupyter-anteckningsbok och ge den namnet **ReadTweetsFromEventHub**. 

1. Kör följande kod för att lägga till ett externt Maven-bibliotek:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Kör följande kod för att läsa tweets från din händelsehubb:

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connectionString).setMaxEventsPerTrigger(5)
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

Med HDInsight lagras dina data i Azure Storage eller Azure Data Lake Store, så kan du ta bort ett kluster när den inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Om du planerar att arbeta med i nästa kurs direkt kanske du vill behålla klustret.

Öppna klustret i Azure-portalen och välj **ta bort**.

![Ta bort HDInsight-kluster](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "ta bort HDInsight-kluster")

Du kan också välja resursgruppens namn att öppna sidan resurs grupp och välj sedan **ta bort resursgruppen**. Genom att ta bort resursgruppen bort du både HDInsight Spark-kluster och standardkontot för lagring.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Läsa meddelande från en händelsehubb.
Gå vidare till nästa artikel för att se att du kan skapa machine learning-program. 

> [!div class="nextstepaction"]
> [Skapa machine learning-program](./apache-spark-ipython-notebook-machine-learning.md)


