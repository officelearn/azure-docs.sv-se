---
title: 'Självstudiekurs: Apache Spark Streaming & Apache Kafka - Azure HDInsight'
description: Lär dig använda Apache Spark-strömning till att hämta data till eller från Apache Kafka. I den här självstudien strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,seodec18
ms.date: 04/22/2020
ms.openlocfilehash: 5fa25f54faecbc7caf130ffeb0d24c3d8fef7e09
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084812"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Självstudie: Använda Apache Spark Structured Streaming med Apache Kafka i HDInsight

Den här självstudien visar hur du använder [Apache Spark Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) till att läsa och skriva data med [Apache Kafka](./kafka/apache-kafka-introduction.md) i Azure HDInsight.

Spark Structured Streaming är en strömbearbetningsmotor byggd på Spark SQL. Med den kan du uttrycka strömmande beräkningar på samma sätt som batchberäkningar av statiska data.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använda en Azure Resource Manager-mall för att skapa kluster
> * Använd Spark Structured Streaming med Kafka

När du är klar med stegen i det här dokumentet, kom ihåg att ta bort kluster för att undvika överskjutande avgifter.

## <a name="prerequisites"></a>Krav

* jq, en kommandorads-JSON-processor.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Kunskaper om [Jupyter Notebooks](https://jupyter.org/) med Spark på HDInsight. Mer information finns i dokumentet [Läsa in data och köra frågor med Apache Spark på HDInsight](spark/apache-spark-load-data-run-query.md).

* Kunskaper i programmeringsspråket Scala. Koden som används i den här självstudien är skriven i Scala.

* Om du vet hur man skapar Kafka-avsnitt. Mer information finns i dokumentet [Snabbstart för Apache Kafka i HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]  
> Stegen i det här dokumentet kräver en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Dokumentet innehåller länkar till en mall som kan skapa alla nödvändiga Azure-resurser.
>
> Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i [Abonnemang ett virtuellt nätverk för HDInsight-dokument.](hdinsight-plan-virtual-network-deployment.md)

## <a name="structured-streaming-with-apache-kafka"></a>Structured Streaming med Apache Kafka

Apache Spark Structured Streaming är en bearbetningsmotor för dataströmmar som bygger på Apache Spark SQL-motorn. När du använder strukturerad strömning kan du skriva direktuppspelade frågor på samma sätt som du skriver batchfrågor.

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

| Batch | Strömning |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Direktuppspelningsåtgärden `awaitTermination(30000)`använder också , som stoppar strömmen efter 30 000 ms.

Om du vill använda Structured Streaming med Kafka måste ditt projekt ha ett beroende på paketet `org.apache.spark : spark-sql-kafka-0-10_2.11`. Versionen av det här paketet ska överensstämma med version på Spark på HDInsight. För Spark 2.2.0 (finns i HDInsight 3.6) hittar du beroendeinformation för olika projekttyper på [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

För den Jupyter-anteckningsbok som används med den här självstudien läser följande cell in det här paketberoendet:

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

Apache Kafka på HDInsight ger inte tillgång till Kafka mäklare över det offentliga internet. Allt som använder Kafka måste finnas i samma virtuella Azure-nätverk. I den här självstudien finns både Kafka- och Apache Spark-klustren i samma virtuella Azure-nätverk.

Följande diagram visar hur kommunikation flödar mellan Apache Spark och Kafka:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Kafka-tjänsten är begränsad till kommunikation inom det virtuella nätverket. Andra tjänster på klustret, till exempel SSH och Ambari, kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Om du vill skapa ett Azure Virtual Network och sedan skapa Kafka- och Spark-kluster i den, använder du följande steg:

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager-mallen **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**finns på .

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

3. Läs **villkoren**och välj sedan **jag godkänner de villkor som anges ovan**.

4. Välj **Köp**.

> [!NOTE]  
> Det kan ta upp till 20 minuter att skapa klustren.

## <a name="use-spark-structured-streaming"></a>Använda strukturerad spark-strömning

Det här exemplet visar hur du använder Spark Structured Streaming med Kafka på HDInsight. Den använder data om taxiresor, som tillhandahålls av New York City.  Datauppsättningen som används av den här anteckningsboken är från [2016 Green Taxi Trip Data](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Samla in värdinformation. Använd curl och [jq](https://stedolan.github.io/jq/) kommandon nedan för att få din Kafka ZooKeeper och mäklare värdar information. Kommandona är utformade för en Windows-kommandotolk, små variationer kommer att behövas för andra miljöer. Ersätt `KafkaCluster` med namnet på Kafka-klustret och `KafkaPassword` med lösenordet för klusterinloggning. Ersätt också `C:\HDI\jq-win64.exe` med den faktiska sökvägen till jq-installationen. Ange kommandona i en Kommandotolk i Windows och spara utdata för användning i senare steg.

    ```cmd
    REM Enter cluster name in lowercase

    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/jupyter`, var `CLUSTERNAME` är namnet på klustret. Ange klusterinloggningen (administratör) och det lösenord som användes när du skapade klustret.

1. Välj **Ny > Spark** om du vill skapa en anteckningsbok.

1. Spark streaming har mikrobatching, vilket innebär att data kommer som batchar och utförare körs på buntar av data. Om utföraren har inaktiv tidsgränsen mindre än den tid det tar att bearbeta batchen, läggs körarna ständigt till och tas bort. Om den inaktiva timeouten för köror är större än batch-varaktigheten tas utföraren aldrig bort. Vi **rekommenderar därför att du inaktiverar dynamisk allokering genom att ange spark.dynamicAllocation.enabled till false när du kör strömmande program.**

    Läs in paket som används av anteckningsboken genom att ange följande information i en anteckningsbokscell. Kör kommandot med **CTRL + RETUR**.

    ```configuration
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11",
            "spark.dynamicAllocation.enabled": false
        }
    }
    ```

1. Skapa Kafka-avsnittet. Redigera kommandot nedan `YOUR_ZOOKEEPER_HOSTS` genom att ersätta med zookeeper värdinformation extraheras i det första steget. Ange det redigerade kommandot i den Jupyter-anteckningsboken `tripdata` för att skapa ämnet.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

1. Hämta data om taxiresor. Ange kommandot i nästa cell för att läsa in data på taxiresor i New York City. Data läses in i en dataram och sedan visas dataramen som cellutdata.

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

1. Ställ in Kafka mäklare värdar information. Ersätt `YOUR_KAFKA_BROKER_HOSTS` med mäklaren värdar information som du extraherade i steg 1.  Ange det redigerade kommandot i nästa Jupyter Notebook-cell.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

1. Skicka data till Kafka. I följande kommando `vendorid` används fältet som nyckelvärde för Kafka-meddelandet. Nyckeln används av Kafka vid partitionering av data. Alla fält lagras i Kafka-meddelandet som ett JSON-strängvärde. Ange följande kommando i Jupyter för att spara data till Kafka med hjälp av en batchfråga.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

1. Deklarera ett schema. Följande kommando visar hur du använder ett schema när du läser JSON-data från kafka. Ange kommandot i nästa Jupyter-cell.

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

1. Välj data och starta strömmen. Följande kommando visar hur du hämtar data från Kafka med hjälp av en batchfråga. Och skriv sedan resultaten ut till HDFS på Spark-klustret. I det `select` här exemplet hämtar meddelandet (värdefältet) från Kafka och schemat tillämpas på det. Uppgifterna skrivs sedan till HDFS (WASB eller ADL) i parkettformat. Ange kommandot i nästa Jupyter-cell.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()

    println("Wrote data to file")
    ```

1. Du kan kontrollera att filerna skapades genom att ange kommandot i nästa Jupyter-cell. Den listar filerna `/example/batchtripdata` i katalogen.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

1. Medan föregående exempel använde en batchfråga visar följande kommando hur du gör samma sak med hjälp av en direktuppspelningsfråga. Ange kommandot i nästa Jupyter-cell.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

1. Kör följande cell för att kontrollera att filerna skrevs av direktuppspelningsfrågan.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen. Om du tar bort resursgruppen tas också det associerade HDInsight-klustret bort. Och alla andra resurser som är associerade med resursgruppen.

Ta bort en resursgrupp med Azure Portal:

1. Expandera menyn till vänster i [Azure-portalen](https://portal.azure.com/)för att öppna menyn med tjänster och välj sedan __Resursgrupper__ för att visa listan över dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen __Mer__ (...) till höger om listan.
3. Välj __Ta bort resursgrupp__ och bekräfta.

> [!WARNING]  
> Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används.
>
> Om du tar bort en Kafka i ett HDInsight-kluster tas alla data som lagrats i Kafka bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder Apache Spark Structured Streaming. Att skriva och läsa data från Apache Kafka på HDInsight. Via följande länk kan du lära dig att använda Apache Storm med Kafka.

> [!div class="nextstepaction"]
> [Använda Apache Storm med Apache Kafka](hdinsight-apache-storm-with-kafka.md)
