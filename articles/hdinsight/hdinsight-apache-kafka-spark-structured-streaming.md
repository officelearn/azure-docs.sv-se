---
title: 'Självstudie: Apache Spark Structured Streaming med Apache Kafka – Azure HDInsight'
description: Lär dig använda Apache Spark-strömning till att hämta data till eller från Apache Kafka. I den här självstudien strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: hrasheed
ms.openlocfilehash: 51f84234ac35be5f60d1aaa5dac661ad9ce5e0c2
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257906"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Självstudie: Använda Apache Spark Structured Streaming med Apache Kafka i HDInsight

Den här självstudien visar hur du använder [Apache Spark Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) till att läsa och skriva data med [Apache Kafka](https://kafka.apache.org/) i Azure HDInsight.

Spark Structured Streaming är en motor för bearbetning av dataströmmar som bygger på Spark SQL. Med den kan du uttrycka strömmande beräkningar på samma sätt som batchberäkningar av statiska data.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Du skapar kluster med en Azure Resource Manager-mall
> * Använda Apache Spark Structured Streaming med Kafka

Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* jq en kommandorad JSON-processor.  Se [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

* Kunskaper om [Jupyter Notebooks](https://jupyter.org/) med Spark på HDInsight. Mer information finns i dokumentet [Läsa in data och köra frågor med Apache Spark på HDInsight](spark/apache-spark-load-data-run-query.md).

* Kunskaper i programmeringsspråket [Scala](https://www.scala-lang.org/). Koden som används i den här självstudien är skriven i Scala.

* Känna till hur man skapar Kafka-ämnen. Mer information finns i dokumentet [Snabbstart för Apache Kafka i HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]  
> Stegen i det här dokumentet kräver en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
> 
> Dokumentet innehåller länkar till en mall som kan skapa alla nödvändiga Azure-resurser. 
>
> Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i dokumentet [Utöka HDInsight med hjälp av ett virtuellt nätverk](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-apache-kafka"></a>Structured Streaming med Apache Kafka

Apache Spark Structured Streaming är en bearbetningsmotor för dataströmmar som bygger på Apache Spark SQL-motorn. När du använder Structured Streaming kan du skriva strömningsfrågor på samma sätt som du skriver batch-frågor.

Följande kodfragment visar läsning från Kafka och lagring på en fil. Den första är en batchåtgärd och den andra är en strömningsåtgärd:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

I båda kodfragmenten läses data från Kafka och skrivs till en fil. Skillnader mellan exemplen är:

| Batch | Direktuppspelning |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Strömmande åtgärden använder också `awaitTermination(30000)`, som avbryter efter 30 000 ms. 

Om du vill använda Structured Streaming med Kafka måste ditt projekt ha ett beroende på paketet `org.apache.spark : spark-sql-kafka-0-10_2.11`. Versionen av det här paketet ska överensstämma med version på Spark på HDInsight. För Spark 2.2.0 (tillgängligt i HDInsight 3.6) kan du hitta beroendeinformation för olika projekttyper på [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Läser in det här beroendet paket för Jupyter-anteckningsboken som används i den här självstudiekursen, följande cell:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till asynkrona meddelandeköer i Kafka via offentligt Internet. Allt som använder Kafka måste finnas i samma virtuella Azure-nätverk. I den här självstudien finns både Kafka- och Apache Spark-klustren i samma virtuella Azure-nätverk. 

Följande diagram visar hur kommunikation flödar mellan Apache Spark och Kafka:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Kafka-tjänsten är begränsad till kommunikation inom det virtuella nätverket. Andra tjänster på klustret, till exempel SSH och Ambari, kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Om du vill skapa ett Azure Virtual Network och sedan skapa Kafka- och Spark-kluster i den, använder du följande steg:

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Azure Resource Manager-mallen finns i **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Den här mallen skapar följande resurser:

   * En Kafka på HDInsight 3.6-klustret.
   * En Apache Spark 2.2.0 på HDInsight 3.6-klustret.
   * Ett virtuellt Azure-nätverk som innehåller HDInsight-klustren.

     > [!IMPORTANT]  
     > Anteckningsboken för Structured Streaming som används i den här självstudien kräver Apache Spark 2.2.0 i HDInsight 3.6. Om du använder en tidigare version av Apache Spark i HDInsight får du ett felmeddelande när du använder anteckningsboken.

2. Använd följande information för att fylla i posterna i avsnittet **Anpassad mall**:

    | Inställning | Värde |
    | --- | --- |
    | Prenumeration | Din Azure-prenumeration |
    | Resursgrupp | Resursgruppen som innehåller resurserna. |
    | Location | Azure-regionen som resurserna skapas i. |
    | Apache Spark-klusternamn | Namnet på Apache Spark-klustret. De första sex tecknen får inte vara samma som Kafka-klusternamnet. |
    | Kafka-klusternamn | Namnet på Kafka-klustret. De första sex tecknen får inte vara samma som Spark-klusternamnet. |
    | Användarnamn för klusterinloggning | Ett administratörsanvändarnamn för klustren. |
    | Lösenord för klusterinloggning | Ett administratörslösenord för klustren. |
    | SSH-användarnamn | SSH-användare som ska skapas för klustren. |
    | SSH-lösenord | Lösenord för SSH-användaren. |
   
    ![Skärmbild av den anpassade mallen](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Granska **villkoren** och klicka sedan i kryssrutan **Jag godkänner villkoren ovan**

4. Markera slutligen **Fäst på instrumentpanelen** och välj sedan **Inköp**. 

> [!NOTE]  
> Det kan ta upp till 20 minuter att skapa klustren.

## <a name="use-spark-structured-streaming"></a>Använda Apache Spark Structured Streaming

Det här exemplet visar hur du använder Spark Structured Streaming med Kafka på HDInsight. Det använder data på taxi resor, som tillhandahålls av New York City.  Datamängden som används av den här anteckningsboken är från [2016 grönt Taxi Resedata](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Samla in information om värden. Använd curl och [jq](https://stedolan.github.io/jq/) kommandona nedan för att hämta Kafka-ZooKeeper och broker värdinformation. Kommandona är utformade för en Windows-kommandotolk, små variationer som krävs för andra miljöer. Ersätt `KafkaCluster` med namnet på Kafka-kluster och `KafkaPassword` med inloggningslösenordet för klustret. Dessutom måste du ersätta `C:\HDI\jq-win64.exe` med den faktiska sökvägen till den jq-installationen. Ange kommandon i en Windows-kommandotolk och spara utdata för användning i senare steg.

    ```cmd
    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

2. Anslut till Jupyter Notebook i Apache Spark-klustret i din webbläsare. I följande URL ersätter du `CLUSTERNAME` med namnet på ditt __Apache Spark__-kluster:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Ange klusterinloggningen (administratör) och det lösenord som användes när du skapade klustret.

3. Välj **New > Spark** att skapa en anteckningsbok.

4. Läsa in paket som används av anteckningsboken genom att ange följande information i en cell i anteckningsboken. Kör kommandot med hjälp av **CTRL + RETUR**.

    ```
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
        }
    }
    ```

5. Skapa Kafka-ämne. Redigera kommandot nedan genom att ersätta `YOUR_ZOOKEEPER_HOSTS` med Zookeeper vara värd för information som hämtas i det första steget. Ange kommandot redigerade i Jupyter-anteckningsboken för att skapa den `tripdata` avsnittet.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

6. Hämta data på taxi och RETUR. Ange kommandot i cellen nästa för att läsa in data på taxi-kommunikation i New York City. Data läses in i en dataram och sedan dataramen visas som cell-utdata.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString
    
    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)
    
    // Display the dataframe containing trip data
    taxiDF.show()
    ```

7. Ange information om Kafka broker värdar. Ersätt `YOUR_KAFKA_BROKER_HOSTS` med broker värdinformation som du extraherade i steg 1.  Ange kommandot redigerade i cellen nästa Jupyter-anteckningsboken.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"
    
    println("Finished setting Kafka broker and topic configuration.")
    ```

8. Skicka data till Kafka. I följande kommando i `vendorid` fältet används som nyckelvärdet för Kafka-meddelandet. Nyckeln används av Kafka när partitionera data. Alla fält lagras i Kafka-meddelandet som en JSON-strängvärde. Ange följande kommando i Jupyter att spara data till Kafka med hjälp av en batchfråga.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

9. Deklarera ett schema. Följande kommando visar hur du använder ett schema vid läsning av JSON-data från kafka. Ange kommandot i ditt nästa Jupyter-cell.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)
    
    println("Schema declared")
    ```

10. Välj data och starta dataströmmen. Följande kommando visar hur du hämtar data från kafka med hjälp av en batchfråga och sedan skriva resultaten till HDFS i Spark-klustret. I det här exemplet på `select` hämtar meddelanden (värdefält) från Kafka och tillämpar schemat. Data skrivs sedan till HDFS (WASB eller ADL) i parquet-format. Ange kommandot i ditt nästa Jupyter-cell.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()
    
    println("Wrote data to file")
    ```

11. Du kan kontrollera att filerna har skapats genom att ange kommandot i ditt nästa Jupyter-cell. Den visar en lista över filer i den `/example/batchtripdata` directory.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

12. Medan det föregående exemplet används en batchfråga, visar följande kommando hur du gör samma sak med hjälp av en strömmande fråga. Ange kommandot i ditt nästa Jupyter-cell.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

13. Kör följande cell om du vill verifiera att filerna har skrivits av strömmande frågan.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade HDInsight-klustret och eventuella andra resurser som är associerade med resursgruppen.

Ta bort en resursgrupp med Azure Portal:

1. I Azure Portal expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer sedan __Resursgrupper__ för att visa listan med dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen __Mer__ (...) till höger om listan.
3. Välj __Ta bort resursgrupp__ och bekräfta.

> [!WARNING]  
> Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används.
> 
> Om du tar bort en Kafka i ett HDInsight-kluster tas alla data som lagrats i Kafka bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att använda [Structured Streaming med Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) för att skriva och läsa data från [Apache Kafka](https://kafka.apache.org/) i HDInsight. Via följande länk kan du lära dig att använda [Apache Storm](https://storm.apache.org/) med Kafka.

> [!div class="nextstepaction"]
> [Använda Apache Storm med Apache Kafka](hdinsight-apache-storm-with-kafka.md)
