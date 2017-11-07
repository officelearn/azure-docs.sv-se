---
title: "Använda Apache Spark streaming med Händelsehubbar i Azure HDInsight | Microsoft Docs"
description: "Skapa ett Apache Spark streaming exempel på hur du skickar en dataström till Azure-Händelsehubb och sedan ta emot händelser i HDInsight Spark-kluster med ett scala-program."
keywords: Apache spark streaming, spark streaming, spark-exemplet, apache spark streaming exempel event hub azure exempel, spark-exempel
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: maxluk
ms.openlocfilehash: d8f934dacc24b6de72c094eb996a722ba4b174d7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="apache-spark-streaming-process-data-from-azure-event-hubs-with-spark-cluster-on-hdinsight"></a>Apache Spark streaming: bearbetning av data från Azure Event Hubs med Spark-kluster i HDInsight

I den här artikeln får skapa du ett Apache Spark streaming exempel som omfattar följande steg:

1. Du kan använda ett fristående program för att mata in meddelanden i en Azure-Händelsehubb.

2. Med två olika sätt hämta meddelanden från Event Hub i realtid med hjälp av ett program som körs i Spark-kluster på Azure HDInsight.

3. Du skapa analytiska pipelines för att spara data till olika lagringssystem för strömning eller kunskap genom statistik från data direkt.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Spark Streaming begrepp

En detaljerad förklaring av Spark streaming finns [Apache Spark streaming översikt](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight ger samma funktioner för strömning för ett Spark-kluster i Azure.  

## <a name="what-does-this-solution-do"></a>Vad är den här lösningen?

Utför följande steg för att skapa ett Spark streaming exempel i den här artikeln:

1. Skapa en Azure-Händelsehubb som tar emot en dataström med händelser.

2. Kör ett lokala fristående program som genererar händelser och skickar den till Azure-Händelsehubb. Exempelprogram som gör detta publiceras på [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Kör en strömmande via fjärranslutning på ett Spark-kluster som läser strömningen av händelser från Azure Event Hub och utföra olika databearbetning/analys.

## <a name="create-an-azure-event-hub"></a>Skapa ett Azure Event Hub

1. Logga in på den [Azure Portal](https://ms.portal.azure.com), och klicka på **ny** på upp till vänster på skärmen.

2. Klicka på **Sakernas Internet** och sedan på **Event Hubs**.

    ![Skapa händelsehubb för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "skapa händelsehubb för Spark streaming exempel")

3. I bladet **Skapa namnområde** anger du ett namn för namnområdet. Välj prisnivå (Basic eller Standard). Välj även en Azure-prenumeration, resursgrupp och plats där du vill skapa resursen. Klicka på **Skapa** för att skapa namnområdet.

      ![Ange ett händelsenamn hubb Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "ger en händelsehubbens namn Spark streaming exempel")

    > [!NOTE]
    > Du bör välja samma **plats** som Apache Spark-kluster i HDInsight för att minska latensen och kostnader.
    >
    >

4. I listan över händelsehubbarnas namnområden klickar du på det nyligen skapade namnområdet.      


5. I bladet namnområde klickar du på **Händelsehubbar**, och klicka sedan på **+ Event Hub** att skapa en ny Händelsehubb.
   
    ![Skapa händelsehubb för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-open-event-hubs-blade-for-spark-streaming-example.png "skapa händelsehubb för Spark streaming exempel")

6. Skriv ett namn för din Händelsehubb genom att ange antalet partitioner 10 och meddelandet lagring till 1. Vi inte arkiverar meddelanden i den här lösningen så att du kan lämna resten som standard och klicka sedan på **skapa**.
   
    ![Ange händelseinformationen hubb för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "ange händelseinformationen hubb för Spark streaming exempel")

7. Nyligen skapade Händelsehubben visas i bladet Event Hub.
    
     ![Visa Event Hub Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-for-spark-streaming-example.png "View Event Hub Spark streaming exempel")

8. Tillbaka i namnområdesbladet (inte i det specifika händelsehubbladet) klickar du på **Principer för delad åtkomst** och klickar därefter på **RootManageSharedAccessKey**.
    
     ![Ange Event Hub principer för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "ange Event Hub principer för Spark streaming exempel")

9. Klicka på kopieringsknappen för att kopiera den **RootManageSharedAccessKey** primära nyckel och anslutningssträngen till Urklipp. Spara de ska använda senare under kursen.
    
     ![Visa Event Hub princip nycklar för Spark streaming exempel](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "View Event Hub princip nycklar för Spark streaming exempel")

## <a name="send-messages-to-azure-event-hub-using-a-sample-scala-application"></a>Skicka meddelanden till Azure-Händelsehubb med hjälp av ett exempelprogram Scala

I det här avsnittet använder du en fristående-lokala Scala-program som genererar en dataström med händelser och skickar den till Azure Event Hub som du skapade tidigare. Det här programmet är tillgängligt på GitHub på [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). De här stegen förutsätter att du redan har forked GitHub-lagringsplatsen.

1. Kontrollera att du har följande installerat på datorn där du kör det här programmet.

    * Oracle Java Development kit. Du kan installera den från [här](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Apache Maven. Du kan ladda ned det från [här](https://maven.apache.org/download.cgi). Anvisningar för att installera Maven finns [här](https://maven.apache.org/install.html).

2. Öppna en kommandotolk och navigera till den plats som du har klonat GitHub-lagringsplatsen för Scala exempelprogrammet och kör följande kommando för att skapa programmet.

        mvn package

3. Utdata jar för programmet, **com-microsoft-azure-eventhubs-client-example-0.2.0.jar**, skapas under **/target** directory. Du kan använda den här JAR senare i den här artikeln för att testa den kompletta lösningen.

## <a name="create-application-to-receive-messages-from-event-hub-into-a-spark-cluster"></a>Skapa program för att ta emot meddelanden från Event Hub i ett Spark-kluster 

Vi har två sätt att ansluta Spark Streaming och Azure Event Hubs, mottagar-baserade anslutningen och Direct-DStream-baserad anslutning. Direct-DStream-baserade introduceras på januari 2017 i 2.0.3 versionen. Det är ska ersätta den ursprungliga mottagar-baserade anslutningen eftersom det är mer performant och resurseffektiv. Mer information finns i [https://github.com/hdinsight/spark-eventhubs](https://github.com/hdinsight/spark-eventhubs). Direkt DStream stöder endast Spark 2.0 +.

### <a name="build-applications-with-the-dependency-to-spark-eventhubs-connector"></a>Skapa program med beroendet till spark eventhubs connector

Vi kommer också att publicera den fristående versionen av Spark EventHubs i GitHub. Om du vill använda den fristående versionen av Spark EventHubs är det första steget att ange GitHub som käll-lagringsplatsen genom att lägga till följande post pom.xml:

```xml
<repository>
      <id>spark-eventhubs</id>
      <url>https://raw.github.com/hdinsight/spark-eventhubs/maven-repo/</url>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
      </snapshots>
</repository>
```

Du kan sedan lägga till följande beroende till projektet att göra den tidigare versionen.

Maven beroende

```xml
<!-- https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11 -->
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-streaming-eventhubs_2.11</artifactId>
    <version>2.0.4</version>
</dependency>
```

Segregerade Barlasttankar beroende

```
// https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11
libraryDependencies += "com.microsoft.azure" % "spark-streaming-eventhubs_2.11" % "2.0.4"
```

### <a name="direct-dstream-connection"></a>Direkt DStream anslutning

En förskapad jar-fil som innehåller exempel som använder direkt DStream kan hämtas i [http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar](http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar).

Jar-filen innehåller tre exempel vars källkoden finns på [https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream](https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream).

Tar [WindowingWordCount](https://github.com/hdinsight/spark-eventhubs/blob/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream/WindowingWordCount.scala) som exempel:

```scala
private def createStreamingContext(
  sparkCheckpointDir: String,
  batchDuration: Int,
  namespace: String,
  eventHunName: String,
  eventhubParams: Map[String, String],
  progressDir: String) = {
val ssc = new StreamingContext(new SparkContext(), Seconds(batchDuration))
ssc.checkpoint(sparkCheckpointDir)
val inputDirectStream = EventHubsUtils.createDirectStreams(
  ssc,
  namespace,
  progressDir,
  Map(eventHunName -> eventhubParams))

inputDirectStream.map(receivedRecord => (new String(receivedRecord.getBody), 1)).
  reduceByKeyAndWindow((v1, v2) => v1 + v2, (v1, v2) => v1 - v2, Seconds(batchDuration * 3),
    Seconds(batchDuration)).print()

ssc
}

def main(args: Array[String]): Unit = {

if (args.length != 8) {
  println("Usage: program progressDir PolicyName PolicyKey EventHubNamespace EventHubName" +
    " BatchDuration(seconds) Spark_Checkpoint_Directory maxRate")
  sys.exit(1)
}

val progressDir = args(0)
val policyName = args(1)
val policykey = args(2)
val namespace = args(3)
val name = args(4)
val batchDuration = args(5).toInt
val sparkCheckpointDir = args(6)
val maxRate = args(7)

val eventhubParameters = Map[String, String] (
  "eventhubs.policyname" -> policyName,
  "eventhubs.policykey" -> policykey,
  "eventhubs.namespace" -> namespace,
  "eventhubs.name" -> name,
  "eventhubs.partition.count" -> "32",
  "eventhubs.consumergroup" -> "$Default",
  "eventhubs.maxRate" -> s"$maxRate"
)

val ssc = StreamingContext.getOrCreate(sparkCheckpointDir,
  () => createStreamingContext(sparkCheckpointDir, batchDuration, namespace, name,
    eventhubParameters, progressDir))

ssc.start()
ssc.awaitTermination()
}
```

I exemplet ovan `eventhubParameters` är parametrar som är specifika för en instans av EventHubs och du behöver skicka det till den `createDirectStreams` API som skapas direkt DStream objektet mappning till ett namnområde för Händelsehubbar. Du kan anropa DStream API: er som tillhandahålls av Spark Streaming API framework över direkt DStream-objektet. I det här exemplet beräkna vi frekvensen av varje ord under de senaste 3 micro batch-intervall.

### <a name="receiver-based-connection"></a>Mottagaren-baserade anslutningen

En Spark streaming exempelprogram som skrivits i Scala som tar emot händelser och vidarebefordra till olika mål, finns på [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Följ stegen nedan för att uppdatera programmet för din Event Hub-konfiguration och skapa utdata jar.

1. Starta IntelliJ IDEA och välj startskärmen **checka ut från versionskontroll** och klicka sedan på **Git**.
   
    ![Apache Spark streaming exempel – get källor från Git](./media/apache-spark-eventhub-streaming/spark-streaming-example-get-source-from-git.png "Apache Spark streaming exempel – get källor från Git")

2. I den **klona databasen** dialogrutan Ange URL: en Git-lagringsplatsen att klona från anger katalogen för att klona till och klicka sedan på **klona**.
   
    ![Apache Spark streaming exempel – klona från Git](./media/apache-spark-eventhub-streaming/spark-streaming-example-clone-from-git.png "Apache Spark streaming exempel – klona från Git")
3. Följ anvisningarna till projektet helt klonas. Tryck på **Alt + 1** att öppna den **projektvyn**. Det bör likna följande.
   
    ![Apache Spark streaming exempel – projektvyn](./media/apache-spark-eventhub-streaming/spark-streaming-example-project-view.png "Apache Spark streaming exempel – projektvyn")
4. Kontrollera att programkoden kompileras med Java8. Så här **filen**, klickar du på **projektstruktur**, och på den **projekt** , se till att projektet språknivå är inställd på **8 - Lambdas, typ anteckningar, etc**.
   
    ![Apache Spark streaming exempel - Set-kompilatorn](./media/apache-spark-eventhub-streaming/spark-streaming-example-java-8-compiler.png "Apache Spark streaming exempel - Set-kompilatorn")
5. Öppna den **pom.xml** och kontrollera Spark-version är korrekt. Under `<properties>` , leta efter följande kodavsnitt och verifiera Spark-version.

        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. Programmet kräver en beroende jar kallas **JDBC driver jar**. Detta är nödvändigt att skriva meddelanden togs emot från Event Hub till en Azure SQL-databas. Du kan hämta den här jar (v4.1 eller senare) från [här](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Lägg till referens till den här jar i projekt-bibliotek. Utför följande steg:
     
     1. Klicka på IntelliJ IDEA fönstret där du har program som är öppna **filen**, klickar du på **projektstruktur**, och klicka sedan på **bibliotek**. 
     2. Klicka på ikonen Lägg till (![Lägg till ikonen](./media/apache-spark-eventhub-streaming/add-icon.png)), klickar du på **Java**, och gå sedan till den plats där du sparade jar för JDBC-drivrutinen. Följ anvisningarna för att lägga till jar-filen till biblioteket i projektet.

         ![lägga till beroenden som saknas](./media/apache-spark-eventhub-streaming/add-missing-dependency-jars.png "lägga till den saknade beroende burkar")
     3. Klicka på **Använd**.

7. Skapa jar-filen för utdata. Utför följande steg.

   1. I den **projektstruktur** dialogrutan klickar du på **artefakter** och sedan klicka på plustecknet. Klicka på dialogrutan popup- **JAR**, och klicka sedan på **från moduler med beroenden**.      
       
       ![Apache Spark streaming exempel – skapa JAR](./media/apache-spark-eventhub-streaming/spark-streaming-example-create-jar.png "Apache Spark streaming exempel – skapa JAR")
   2. I den **skapa JAR från moduler** dialogrutan klickar du på ellipsknappen (![tre punkter](./media/apache-spark-eventhub-streaming/ellipsis.png)) mot den **Main klassen**.
   3. I den **Välj Main klassen** dialogrutan väljer någon av de tillgängliga klasserna och klicka sedan på **OK**.
      
       ![Apache Spark streaming exempel – Välj klass för jar](./media/apache-spark-eventhub-streaming/spark-streaming-example-select-class-for-jar.png "Apache Spark streaming exempel – Välj klass för jar")
   4. I den **skapa JAR från moduler** dialogrutan rutan, kontrollera att alternativet att **extrahera till målet JAR** är markerad och klicka sedan på **OK**. Detta skapar en enda JAR med alla beroenden.
      
       ![Apache Spark streaming exempel – skapa jar från moduler](./media/apache-spark-eventhub-streaming/spark-streaming-example-create-jar-from-modules.png "Apache Spark streaming exempel – skapa jar från moduler")
   5. Den **utdata Layout** fliken listar alla burkar som ingår som en del av Maven-projekt. Du kan välja och ta bort de där programmet Scala har ingen direkt beroende. För program som skapar vi här, kan du ta bort alla utom det senaste (**spark-streaming-data-beständiga-exempel kompilera utdata**). Välj burkar att ta bort och klicka sedan på den **ta bort** ikon (![ikonen Ta bort](./media/apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Apache Spark streaming exempel – ta bort extraherade burkar](./media/apache-spark-eventhub-streaming/spark-streaming-example-delete-output-jars.png "Apache Spark streaming exempel – ta bort extraherade burkar")
      
       Kontrollera att **bygger på Kontrollera** är markerad, vilket säkerställer att jar skapas varje gång projektet skapats eller uppdaterats. Klicka på **Använd**.
   6. I den **utdata Layout** fliken höger längst ned i den **element** finns SQL JDBC jar som du tidigare lagt till i biblioteket i projektet. Du måste lägga till den till den **utdata Layout** fliken. Högerklicka på jar-filen och klicka sedan på **extrahera roten till utdata**.
      
       ![Apache Spark streaming exempel – extrahera beroende jar](./media/apache-spark-eventhub-streaming/spark-streaming-example-extract-dependency-jar.png "Apache Spark streaming exempel – extrahera beroende jar")  
      
       Den **utdata Layout** fliken bör nu se ut så här.
      
       ![Apache Spark streaming exempel – fliken slutversionen](./media/apache-spark-eventhub-streaming/spark-streaming-example-final-output-tab.png "Apache Spark streaming exempel – fliken slutversionen")        
      
       I den **projektstruktur** dialogrutan klickar du på **tillämpa** och klicka sedan på **OK**.    
   7. På menyraden klickar du på **skapa**, och klicka sedan på **Se projektet**. Du kan också klicka på **skapa artefakter** att skapa jar. Utdata jar skapas under **\classes\artifacts**.
      
       ![Apache Spark streaming exempel - utdata JAR](./media/apache-spark-eventhub-streaming/spark-streaming-example-output-jar.png "Apache Spark streaming exempel - utdata JAR")

## <a name="run-the-application-remotely-on-a-spark-cluster-using-livy"></a>Köra programmet via fjärranslutning på ett Spark-kluster med Livy

I den här artikeln använder du Livius för att köra den Apache Spark streaming program via fjärranslutning på ett Spark-kluster. Detaljerad information om hur du använder Livius med HDInsight Spark-kluster finns i [skicka jobb via fjärranslutning till ett Apache Spark-kluster i Azure HDInsight](apache-spark-livy-rest-interface.md). Innan du kan börja köra Spark streaming program, finns det några saker du bör göra:

1. Starta det lokala fristående programmet som ska generera händelser och skickas till Händelsehubben. Använd följande kommando för att göra det:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Kopiera strömmande jar (**spark-streaming-data-beständiga-examples.jar**) till Azure Blob-lagring som är associerade med klustret. Jar blir tillgänglig för Livius. Du kan använda [ **AzCopy**](../../storage/common/storage-use-azcopy.md), ett kommandoradsverktyg, gör. Det finns en mängd andra klienter som du kan använda för att överföra data. Du kan hitta mer om dem i [överföra data för Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).
3. Installera CURL på den dator där du kör programmen från. Vi använder CURL för att anropa Livius slutpunkter för att köra jobb via fjärranslutning.

### <a name="run-the-spark-streaming-application-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Köra Spark streaming program för att ta emot händelser till en Azure Storage Blob som text

Öppna en kommandotolk och gå till den katalog där du installerade CURL kör du följande kommando (Ersätt användarnamn/lösenord och kluster namn):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Parametrarna i filen **inputBlob.txt** definieras enligt följande:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Låt oss förstå vilka parametrar i indatafilen är:

* **filen** är sökvägen till programmet jar-filen på Azure storage-konto som är associerade med klustret.
* **className** är namnet på klassen i jar.
* **argument** är listan med argument som krävs av klassen
* **numExecutors** är antalet kärnor som används av Spark för att köra programmet strömning. Detta ska alltid vara minst två gånger antalet partitioner i Händelsehubben.
* **executorMemory**, **executorCores**, **driverMemory** är parametrar som används för att tilldela resurser som krävs för strömning.

> [!NOTE]
> Du behöver inte skapa utdatamappar (EventCheckpoint, EventCount/EventCount10) som används som parametrar. Strömmande programmet skapar dem åt dig.
>
>

När du kör kommandot, bör du se utdata som liknar följande:

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Anteckna batch-ID i den sista raden i utdata (i det här exemplet är det '1'). Om du vill kontrollera att programmet har körts kan du tittar på ditt Azure storage-konto som är associerade med klustret och du bör se den **/EventCount/EventCount10** mapp som skapade det. Den här mappen bör innehålla blobbar som samlar in antalet händelser som bearbetas inom den tidsperiod som angetts för parametern **batch intervall i sekunder**.

Spark streaming programmet fortsätter att köras tills du avsluta den. Om du vill göra det använder du följande kommando:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Köra program för att ta emot händelser till en Azure Storage Blob som JSON
Öppna en kommandotolk och gå till den katalog där du installerade CURL kör du följande kommando (Ersätt användarnamn/lösenord och kluster namn):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Parametrarna i filen **inputJSON.txt** definieras enligt följande:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Parametrarna liknar vad du har angett för text-utdata i föregående steg. Igen, behöver du inte skapa utdatamappar (EventCheckpoint, EventCount/EventCount10) som används som parametrar. Strömmande programmet skapar dem åt dig.

 När du kör kommandot kan du titta på ditt Azure storage-konto som är associerade med klustret och du bör se den **/EventStore10** mapp som skapade det. Öppna en fil med prefixet **del -** ska du se de händelser som bearbetas i en JSON-format.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Köra program för att ta emot händelser till en Hive-tabell
Om du vill köra Spark streaming program som strömmas händelser till en Hive-tabell måste vissa ytterligare komponenter. Dessa är:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

Den **.jar** filer är tillgängliga på HDInsight Spark-kluster på `/usr/hdp/current/spark-client/lib`. Den **hive-site.xml** finns på `/usr/hdp/current/spark-client/conf`.

Du kan använda [WinScp](http://winscp.net/eng/download.php) att kopiera över filerna från klustret till den lokala datorn. Du kan sedan använda verktyg för att kopiera filerna över till ditt lagringskonto som associeras med klustret. Mer information om hur du överför filer till lagringskontot finns [överföra data för Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).

När du har kopierat över filerna till Azure storage-konto, öppna en kommandotolk och gå till den katalog där du installerade CURL kör du följande kommando (Ersätt användarnamn/lösenord och kluster namn):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Parametrarna i filen **inputHive.txt** definieras enligt följande:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Parametrarna liknar vad du har angett för text-utdata i föregående steg. Igen och du behöver inte skapa utdatamappar (EventCheckpoint, EventCount/EventCount10) eller utdata Hive-tabell (EventHiveTable10) som används som parametrar. Strömmande programmet skapar dem åt dig. Observera att den **burkar** och **filer** alternativet innehåller sökvägar till .jar-filer och hive-site.xml som du kopierade över till lagringskontot.

Kontrollera att hive-tabellen har skapats genom att använda den [Ambari Hive-vyn](../hadoop/apache-hadoop-use-hive-ambari-view.md). Du kan köra en SELECT-frågan, om du vill visa innehållet i tabellen.

    SELECT * FROM eventhivetable10 LIMIT 10;

Du bör se utdata som liknar följande:

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Köra program för att ta emot händelser till en Azure SQL-databastabell
Kontrollera att du har en Azure SQL-databas som har skapats innan du kör det här steget. Instruktioner finns i [skapa en SQL-databas i minuter](../../sql-database/sql-database-get-started-portal.md). För att slutföra det här avsnittet, behöver du värden för databasens namn, Databasservernamnet och administratörsautentiseringsuppgifter för databas som parametrar. Du behöver inte att skapa databastabellen om. Spark streaming programmet skapas som.

Öppna en kommandotolk och gå till den katalog där du installerade CURL kör följande kommando:

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Parametrarna i filen **inputSQL.txt** definieras enligt följande:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Du kan ansluta till Azure SQL-databasen med SQL Server Management Studio för att kontrollera att programmet har körts. Instruktioner om hur du gör finns [Anslut till SQL Database med SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md). När du är ansluten till databasen kan du navigera till den **EventContent** tabell som har skapats av strömmande programmet. Du kan köra en snabb fråga för att hämta data från tabellen. Kör följande fråga:

    SELECT * FROM EventCount

Du bör se utdata som liknar följande:

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)
* [Design av mottagare-baserade anslutningen och direkt DStream](https://www.slideshare.net/NanZhu/seattle-sparkmeetup032317)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: ../storage-create-storage-account/
