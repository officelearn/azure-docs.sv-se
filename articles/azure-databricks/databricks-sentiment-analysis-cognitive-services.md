---
title: Använd Azure Databricks för sentiment-analys
description: Lär dig att använda Azure Databricks med Event Hubs och Cognitive Services API för att köra sentiment analys på strömmande data i nära real tid.
services: azure-databricks
author: lenadroid
ms.author: alehall
ms.reviewer: mamccrea
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 07/29/2019
ms.openlocfilehash: 382dff156c088f367200f0dd46c3758193ade189
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889232"
---
# <a name="tutorial-sentiment-analysis-on-streaming-data-using-azure-databricks"></a>Självstudie: Attitydanalys på strömmad data med hjälp av Azure Databricks

I den här självstudien får du lära dig att köra sentimentanalys på en dataström med hjälp av Azure Databricks nästan i realtid. Du konfigurerar datainmatningssystemet med Azure Event Hubs. Du kan använda meddelanden från Event Hubs i Azure Databricks med hjälp av Spark Event Hubs-anslutningsprogrammet. Slutligen kan du använda API: er för kognitiva tjänster för att köra sentiment analys på strömmade data.

I slutet av den här självstudien har du strömmat tweets från Twitter som innehåller termen ”Azure” och kört attitydanalys på dessa tweets.

Följande bild visar programflödet:

![Azure Databricks med Event Hubs och Cognitive Services](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-tutorial.png "Azure Databricks med Event Hubs och Cognitive Services")

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure Databricks-arbetsyta
> * Skapa ett Spark-kluster i Azure Databricks
> * Skapa en Twitter-app om du vill ha tillgång strömmande data
> * Skapa anteckningsböcker i Azure Databricks
> * Bifoga bibliotek för Event Hubs och Twitter-API
> * Skapa ett Cognitive Services konto och hämta åtkomst nyckeln
> * Skicka tweets till Event Hubs
> * Läsa tweets från Event Hubs
> * Kör attitydanalys på tweets

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/?WT.mc_id=sparkeventhubs-docs-alehall) innan du börjar.

> [!Note]
> Den här självstudien kan inte utföras med **Azures kostnads fri utvärderings prenumeration**.
> Om du har ett kostnads fritt konto går du till din profil och ändrar din prenumeration till **betala per**användning. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Ta sedan [bort utgifts gränsen](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-azure-portal)och [begär en kvot ökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för virtuella processorer i din region. När du skapar din Azure Databricks arbets yta kan du välja pris nivån **utvärdering (Premium-14-dagar gratis DBU)** för att ge arbets ytan åtkomst till kostnads fria Premium Azure Databricks DBU i 14 dagar.

## <a name="prerequisites"></a>Krav

Innan du börjar med den här självstudien måste du uppfylla följande krav:
- Ett Event Hubs-namnområde.
- En händelsehubb i namnområdet.
- Anslutningssträng för åtkomst till Event Hubs-namnområdet. Anslutningssträngen måste ha ett format som liknar `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- Namn på princip för delad åtkomst och principnyckel för Event Hubs.

Du kan uppfylla dessa krav genom att slutföra stegen i artikeln [Skapa ett Azure Event Hubs-namnområde och en händelsehubb](../event-hubs/event-hubs-create.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall).

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med Azure-portalen.

1. Välj **Skapa en resurs** > **Data och analys** > **Azure Databricks** i Azure Portal.

    ![Databricks på Azure Portal](./media/databricks-sentiment-analysis-cognitive-services/azure-databricks-on-portal.png "Databricks på Azure Portal")

3. Under **Azure Databricks-tjänst** anger du värden för att skapa en Databricks-arbetsyta.

    ![Skapa en Azure Databricks-arbetsyta](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-workspace.png "Skapa en Azure Databricks-arbetsyta")

    Ange följande värden:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../azure-resource-manager/management/overview.md). |
    |**Plats**     | Välj **USA, östra 2**. För andra tillgängliga regioner läser du informationen om [Azure-tjänsttillgänglighet per region](https://azure.microsoft.com/regions/services/?WT.mc_id=sparkeventhubs-docs-alehall).        |
    |**Prisnivå**     |  Välj mellan **Standard** och **Premium**. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).       |

    Välj **Fäst på instrumentpanelen** och välj sedan **Skapa**.

4. Det tar några minuter att skapa kontot. När kontot skapas i portalen visas panelen för att **skicka distribution för Azure Databricks** på höger sida. Du kan behöva rulla åt höger på instrumentpanelen för att se panelen. En förloppsindikator visas även längst upp på skärmen. Båda dessa områden visar förloppet.

    ![Databricks distributions panel](./media/databricks-sentiment-analysis-cognitive-services/databricks-deployment-tile.png "Databricks distributions panel")

## <a name="create-a-spark-cluster-in-databricks"></a>Skapa ett Spark-kluster i Databricks

1. I Azure-portalen går du till Databricks-arbetsytan som du skapade. Välj sedan **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. I portalen väljer du **Kluster**.

    ![Databricks på Azure](./media/databricks-sentiment-analysis-cognitive-services/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Godkänn alla övriga standardvärden, förutom följande:

   * Ange ett namn för klustret.
   * I den här artikeln skapar du ett kluster med **6,0** Runtime.
   * Se till att markera kryssrutan **Avsluta efter \_\_ minuters inaktivitet**. Ange en varaktighet (i minuter) för att avsluta klustret om klustret inte används.

   Välj den kluster arbetare och den driv rutins nod som passar dina tekniska kriterier och [budget](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).

     Välj **Skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

Om du vill få en dataström med tweets skapar du ett program i Twitter. Följ anvisningarna för att skapa ett Twitter-program och registrera de värden som du behöver för självstudien.

1. I en webbläsare går du till [Twitter för utvecklare](https://developer.twitter.com/en/apps)och väljer **skapa en app**. Du kan se ett meddelande om att du måste använda för ett Twitter-konto för utvecklare. Det är kostnads fritt att göra det och när ditt program har godkänts bör du se ett bekräftelse meddelande. Det kan ta flera dagar att godkännas för ett Developer-konto.

    ![Konto bekräftelse för Twitter-utvecklare](./media/databricks-sentiment-analysis-cognitive-services/databricks-twitter-dev-confirmation.png "Konto bekräftelse för Twitter-utvecklare")

2. På sidan **Skapa ett program** anger du information om den nya appen. Välj sedan **Create your Twitter application** (Skapa ditt Twitter-program).

    ![Information om Twitter-program](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details.png "Information om Twitter-program")

    ![Information om Twitter-program](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details-create.png "Information om Twitter-program")

3. På sidan program väljer du fliken **nycklar och tokens** och kopierar värdena för klient-API- **nyckel** och **hemlig nyckel för konsument-API**. Välj också **skapa** under **åtkomst-token och åtkomst till token Secret** för att generera åtkomsttoken. Kopiera värdena för **åtkomsttoken** och **åtkomsttokenhemligheten**.

    ![Information om Twitter-program](./media/databricks-sentiment-analysis-cognitive-services/twitter-app-key-secret.png "Information om Twitter-program")

Spara de värden som du hämtade för Twitter-programmet. Du behöver dem senare i självstudien.

## <a name="attach-libraries-to-spark-cluster"></a>Bifoga bibliotek till Spark-kluster

I den här självstudien använder du Twitter-API:er för att skicka tweets till Event Hubs. Du använder också [Apache Spark Event Hubs-anslutningsprogram](https://github.com/Azure/azure-event-hubs-spark?WT.mc_id=sparkeventhubs-docs-alehall) för att läsa och skriva data till Azure Event Hubs. Om du vill använda dessa API: er som en del av klustret lägger du till dem som bibliotek i Azure Databricks och kopplar dem till ditt Spark-kluster. Följande instruktioner visar hur du lägger till ett bibliotek.

1. I arbets ytan Azure Databricks väljer du **kluster**och sedan ditt befintliga Spark-kluster. I kluster-menyn väljer du **bibliotek** och klickar på **installera ny**.

   ![Dialog rutan Lägg till bibliotek](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-locate-cluster.png "Lägg till bibliotek hitta kluster")

   ![Dialog rutan Lägg till bibliotek](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-install-new.png "Lägg till bibliotek installera ny")

2. På sidan nytt bibliotek för **källa** väljer du **maven**. För **koordinat**klickar du på **Sök efter paket** för det paket som du vill lägga till. Här är Maven-koordinaterna för de bibliotek som används i självstudien:

   * Spark Event Hubs-anslutningsprogram – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter-API – `org.twitter4j:twitter4j-core:4.0.7`

     ![Ange maven-koordinater](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search.png "Ange maven-koordinater")

     ![Ange maven-koordinater](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search-dialogue.png "Sök maven-koordinater")

3. Välj **Installera**.

4. Kontrol lera att båda biblioteken är installerade och bifogas korrekt i kluster menyn.

    ![Kontrol lera bibliotek](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-check.png "Kontrol lera bibliotek")

6. Upprepa dessa steg för Twitter-paketet `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Hämta en Cognitive Services-åtkomstnyckel

I den här självstudien använder du [Azure Cognitive Services textanalys-API: er](../cognitive-services/text-analytics/overview.md) för att köra sentiment analys på en ström med tweets i nära real tid. Innan du använder API: erna måste du skapa ett Azure Cognitive Services-konto på Azure och hämta en åtkomst nyckel för att använda de Textanalys API: erna.

1. Logga in på [Azure-portalen](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall).

2. Välj **+ Skapa en resurs**.

3. Under Azure Marketplace väljer du **AI och Cognitive Services** > **API för textanalys**.

    ![Skapa konto för kognitiva tjänster](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-text-api.png "Skapa konto för kognitiva tjänster")

4. I dialogrutan **Skapa** anger du följande värden:

    ![Skapa konto för kognitiva tjänster](./media/databricks-sentiment-analysis-cognitive-services/create-cognitive-services-account.png "Skapa konto för kognitiva tjänster")

   - Ange ett namn på Cognitive Services-kontot.
   - Välj den Azure-prenumeration som kontot skapas under.
   - Välj en Azure-plats.
   - Välj en prisnivå för tjänsten. Läs mer om prissättningen för Cognitive Services på [sidan med priser](https://azure.microsoft.com/pricing/details/cognitive-services/?WT.mc_id=sparkeventhubs-docs-alehall).
   - Ange om du vill skapa en ny resursgrupp eller välja en befintlig.

     Välj **Skapa**.

5. När kontot har skapats går du till fliken **Översikt** och väljer **Visa åtkomstnycklar**.

    ![Visa åtkomst nycklar](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-get-access-keys.png "Visa åtkomst nycklar")

    Kopiera också en del av slutpunkts-URL:en, enligt det som visas i skärmbilden. Du behöver den här URL:en i självstudien.

6. Under **Hantera nycklar** väljer du kopieringsikonen för den nyckel som du vill använda.

    ![Kopiera åtkomst nycklar](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-copy-access-keys.png "Kopiera åtkomst nycklar")

7. Spara värdena för slutpunkts-URL:en och åtkomstnyckeln som du hämtade i det här steget. Du behöver dem senare i den här självstudien.

## <a name="create-notebooks-in-databricks"></a>Skapa anteckningsböcker i Databricks

I det här avsnittet skapar du två anteckningsböcker i Databricks-arbetsytan med följande namn

- **SendTweetsToEventHub** – En producentanteckningsbok som du använder för att få tweets från Twitter och strömma dem till Event Hubs.
- **AnalyzeTweetsFromEventHub** – En konsumentanteckningsbok som du använder för att läsa tweets från Event Hubs och köra attitydanalys.

1. Välj **Arbetsyta** i det vänstra fönstret. I listrutan **Arbetsyta** väljer du **Skapa** och sedan **Anteckningsbok**.

    ![Skapa antecknings bok i Databricks](./media/databricks-sentiment-analysis-cognitive-services/databricks-create-notebook.png "Skapa antecknings bok i Databricks")

2. I dialogrutan **Skapa anteckningsbok** anger du **SendTweetsToEventHub**, väljer **Scala** som språk och väljer det Spark-kluster som du skapade tidigare.

    ![Skapa antecknings bok i Databricks](./media/databricks-sentiment-analysis-cognitive-services/databricks-notebook-details.png "Skapa antecknings bok i Databricks")

    Välj **Skapa**.

3. Upprepa stegen för att skapa anteckningsboken **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Skicka tweets till Event Hubs

I anteckningsboken **SendTweetsToEventHub** klistrar du in följande kod och ersätter platshållarna med värden för Event Hubs-namnrymden och det Twitter-program som du skapade tidigare. Anteckningsboken strömmar tweets med nyckelordet ”Azure” till Event Hubs i realtid.

> [!NOTE]
> Twitter API har vissa begränsningar och [kvoter](https://developer.twitter.com/en/docs/basics/rate-limiting.html)för begäran. Om du inte är nöjd med standard hastighets begränsning i Twitter-API kan du generera text innehåll utan att använda Twitter-API i det här exemplet. Om du vill göra det ställer du in variabel **data källa** till `test` i stället för `twitter` och fyller i listan **testSource** med önskade test indata.

```scala
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._
    import scala.collection.immutable._
    import scala.concurrent.Future
    import scala.concurrent.ExecutionContext.Implicits.global

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newScheduledThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sleep(time: Long): Unit = Thread.sleep(time)

    def sendEvent(message: String, delay: Long) = {
      sleep(delay)
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    // Add your own values to the list
    val testSource = List("Azure is the greatest!", "Azure isn't working :(", "Azure is okay.")

    // Specify 'test' if you prefer to not use Twitter API and loop through a list of values you define in `testSource`
    // Otherwise specify 'twitter'
    val dataSource = "test"

    if (dataSource == "twitter") {

      import twitter4j._
      import twitter4j.TwitterFactory
      import twitter4j.Twitter
      import twitter4j.conf.ConfigurationBuilder

      // Twitter configuration!
      // Replace values below with you

      val twitterConsumerKey = "<CONSUMER API KEY>"
      val twitterConsumerSecret = "<CONSUMER API SECRET>"
      val twitterOauthAccessToken = "<ACCESS TOKEN>"
      val twitterOauthTokenSecret = "<TOKEN SECRET>"

      val cb = new ConfigurationBuilder()
        cb.setDebugEnabled(true)
        .setOAuthConsumerKey(twitterConsumerKey)
        .setOAuthConsumerSecret(twitterConsumerSecret)
        .setOAuthAccessToken(twitterOauthAccessToken)
        .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

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
            sendEvent(status.getText(), 5000)
          }
          lowestStatusId = Math.min(status.getId(), lowestStatusId)
        }
        query.setMaxId(lowestStatusId - 1)
      }

    } else if (dataSource == "test") {
      // Loop through the list of test input data
      while (true) {
        testSource.foreach {
          sendEvent(_,5000)
        }
      }

    } else {
      System.out.println("Unsupported Data Source. Set 'dataSource' to \"twitter\" or \"test\"")
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
```

Om du vill köra anteckningsboken trycker du på **SKIFT + RETUR**. Utdata som liknar kodfragmentet nedan visas. Varje utdatahändelse är en tweet med termen ”Azure” som matas in i Event Hubs.

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Läsa tweets från Event Hubs

I anteckningsboken **AnalyzeTweetsFromEventHub** klistrar du in följande kod och ersätter platshållarna med värden för Azure Event Hubs som du skapade tidigare. Den här anteckningsboken läser de tweets som du tidigare strömmade till Event Hubs med hjälp av anteckningsboken **SendTweetsToEventHub**.

```scala

    import org.apache.spark.eventhubs._
    import com.microsoft.azure.eventhubs._

    // Build connection string with the above information
    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new com.microsoft.azure.eventhubs.ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val customEventhubParameters =
      EventHubsConf(connStr.toString())
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Du får följande utdata:


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Eftersom utdatan är i ett binärt läge använder du följande kodfragment för att konvertera den till en sträng.

```scala
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Utdatan liknar nu följande kodfragment:

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Nu har du strömmat data från Azure Event Hubs till Azure Databricks nästan i realtid med hjälp av Event Hubs-anslutningsappen för Apache Spark. Mer information om hur du använder Event Hubs-anslutningsprogrammet för Apache Spark finns i [dokumentationen till anslutningsprogrammet](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs?WT.mc_id=sparkeventhubs-docs-alehall).

## <a name="run-sentiment-analysis-on-tweets"></a>Kör attitydanalys på tweets

I det här avsnittet kör du attitydanalysen på de tweets som tagits emot med Twitter-API:n. I det här avsnittet lägger du till kodfragmenten till samma **AnalyzeTweetsFromEventHub**-anteckningsbok.

Börja med att lägga till en ny kodcell i anteckningsboken och klistra in kodfragmentet nedan. Det här kodfragmentet definierar datatyper som använder språk- och attityd-API:er.

```scala
import java.io._
import java.net._
import java.util._

case class Language(documents: Array[LanguageDocuments], errors: Array[Any]) extends Serializable
case class LanguageDocuments(id: String, detectedLanguages: Array[DetectedLanguages]) extends Serializable
case class DetectedLanguages(name: String, iso6391Name: String, score: Double) extends Serializable

case class Sentiment(documents: Array[SentimentDocuments], errors: Array[Any]) extends Serializable
case class SentimentDocuments(id: String, score: Double) extends Serializable

case class RequestToTextApi(documents: Array[RequestToTextApiDocument]) extends Serializable
case class RequestToTextApiDocument(id: String, text: String, var language: String = "") extends Serializable
```

Lägg till en ny kodcell och klistra in kodfragmentet nedan. Kodfragmentet definierar ett objekt med funktioner för att anropa API:n för textanalys som kör språkidentifiering och attitydanalys. Se till att ersätta plats hållaren `<PROVIDE ACCESS KEY HERE>` med det värde som du hämtade för ditt Cognitive Services-konto.

```scala
import javax.net.ssl.HttpsURLConnection
import com.google.gson.Gson
import com.google.gson.GsonBuilder
import com.google.gson.JsonObject
import com.google.gson.JsonParser
import scala.util.parsing.json._

object SentimentDetector extends Serializable {

    // Cognitive Services API connection settings
    val accessKey = "<PROVIDE ACCESS KEY HERE>"
    val host = "https://cognitive-docs.cognitiveservices.azure.com/"
    val languagesPath = "/text/analytics/v2.1/languages"
    val sentimentPath = "/text/analytics/v2.1/sentiment"
    val languagesUrl = new URL(host+languagesPath)
    val sentimenUrl = new URL(host+sentimentPath)
    val g = new Gson

    def getConnection(path: URL): HttpsURLConnection = {
        val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
        connection.setRequestMethod("POST")
        connection.setRequestProperty("Content-Type", "text/json")
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey)
        connection.setDoOutput(true)
        return connection
    }

    def prettify (json_text: String): String = {
        val parser = new JsonParser()
        val json = parser.parse(json_text).getAsJsonObject()
        val gson = new GsonBuilder().setPrettyPrinting().create()
        return gson.toJson(json)
    }

    // Handles the call to Cognitive Services API.
    def processUsingApi(request: RequestToTextApi, path: URL): String = {
        val requestToJson = g.toJson(request)
        val encoded_text = requestToJson.getBytes("UTF-8")
        val connection = getConnection(path)
        val wr = new DataOutputStream(connection.getOutputStream())
        wr.write(encoded_text, 0, encoded_text.length)
        wr.flush()
        wr.close()

        val response = new StringBuilder()
        val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
        var line = in.readLine()
        while (line != null) {
            response.append(line)
            line = in.readLine()
        }
        in.close()
        return response.toString()
    }

    // Calls the language API for specified documents.
    def getLanguage (inputDocs: RequestToTextApi): Option[Language] = {
        try {
            val response = processUsingApi(inputDocs, languagesUrl)
            // In case we need to log the json response somewhere
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val language = g.fromJson(niceResponse, classOf[Language])
            if (language.documents(0).detectedLanguages(0).iso6391Name == "(Unknown)")
                return None
            return Some(language)
        } catch {
            case e: Exception => return None
        }
    }

    // Calls the sentiment API for specified documents. Needs a language field to be set for each of them.
    def getSentiment (inputDocs: RequestToTextApi): Option[Sentiment] = {
        try {
            val response = processUsingApi(inputDocs, sentimenUrl)
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val sentiment = g.fromJson(niceResponse, classOf[Sentiment])
            return Some(sentiment)
        } catch {
            case e: Exception => return None
        }
    }
}
```

Lägg till ytterligare en cell för att definiera en Spark-UDF (User Defined Function – användardefinierad funktion) som avgör sentiment.

```scala
// User Defined Function for processing content of messages to return their sentiment.
val toSentiment =
    udf((textContent: String) =>
        {
            val inputObject = new RequestToTextApi(Array(new RequestToTextApiDocument(textContent, textContent)))
            val detectedLanguage = SentimentDetector.getLanguage(inputObject)
            detectedLanguage match {
                case Some(language) =>
                    if(language.documents.size > 0) {
                        inputObject.documents(0).language = language.documents(0).detectedLanguages(0).iso6391Name
                        val sentimentDetected = SentimentDetector.getSentiment(inputObject)
                        sentimentDetected match {
                            case Some(sentiment) => {
                                if(sentiment.documents.size > 0) {
                                    sentiment.documents(0).score.toString()
                                }
                                else {
                                    "Error happened when getting sentiment: " + sentiment.errors(0).toString
                                }
                            }
                            case None => "Couldn't detect sentiment"
                        }
                    }
                    else {
                        "Error happened when getting language" + language.errors(0).toString
                    }
                case None => "Couldn't detect language"
            }
        }
    )
```

Lägg till en slutlig kodcell för att förbereda en DataFrame med innehåll från tweeten och den attitydanalys som är associerad med tweeten.

```scala
// Prepare a dataframe with Content and Sentiment columns
val streamingDataFrame = incomingStream.selectExpr("cast (body as string) AS Content").withColumn("Sentiment", toSentiment($"Content"))

// Display the streaming data with the sentiment
streamingDataFrame.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Du bör se utdata som liknar följande kodavsnitt:

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +--------------------------------+------------------+
    |Content                         |Sentiment         |
    +--------------------------------+------------------+
    |Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah   #cloudcomputing #Azure          |0.7761918306350708|
    |Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |0.8558163642883301|
    |@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|0.5               |
    |4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |0.5               |
    +--------------------------------+------------------+

Ett värde nära **1** i kolumnen **Attityd** innebär en bra Azure-upplevelse. Ett värde närmare **0** tyder på problem som användare kan uppleva när de använder Microsoft Azure.

Klart! Med Azure Databricks har du strömmat data till Azure Event Hubs, använt dataströmmar med Event Hubs-anslutningsappen och sedan kört sentimentanalys på strömmande data nästan i realtid.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med självstudien kan du avsluta klustret. Detta gör du genom att välja **Kluster** i det vänstra fönstret i Azure Databricks-arbetsytan. Gå till klustret som du vill avsluta och rör markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**.

![Stoppa ett Databricks-kluster](./media/databricks-sentiment-analysis-cognitive-services/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte manuellt avslutar klustret kommer det att stoppas automatiskt, förutsatt att du har markerat kryssrutan **Avsluta efter \_\_ minuters inaktivitet** när klustret skapades. I dessa fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

## <a name="next-steps"></a>Nästa steg
I självstudien har du lärt dig hur du använder Azure Databricks till att strömma data till Azure Event Hubs och sedan läsa strömmande data från Event Hubs i realtid. Du har lärt dig att:
> [!div class="checklist"]
> * Skapa en Azure Databricks-arbetsyta
> * Skapa ett Spark-kluster i Azure Databricks
> * Skapa en Twitter-app om du vill ha tillgång strömmande data
> * Skapa anteckningsböcker i Azure Databricks
> * Lägga till och bifoga bibliotek för Event Hubs och Twitter-API
> * Skapa ett Microsoft Cognitive Services-konto och hämta åtkomstnyckeln
> * Skicka tweets till Event Hubs
> * Läsa tweets från Event Hubs
> * Kör attitydanalys på tweets

Gå vidare till nästa självstudie för att lära dig att utföra maskininlärning med Azure Databricks.

> [!div class="nextstepaction"]
>[Maskininlärning med hjälp av Azure Databricks](/azure/databricks/applications/machine-learning/mllib/decision-trees)

