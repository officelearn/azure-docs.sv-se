---
title: 'Självstudie: Apache Spark Structured Streaming med Apache Kafka – Azure HDInsight'
description: Lär dig använda Apache Spark-strömning till att hämta data till eller från Apache Kafka. I den här självstudien strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 4ac341d780a3c348f9ba9f8fd0241c351bd5fdc5
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162444"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Självstudie: Använda Apache Spark Structured Streaming med Apache Kafka i HDInsight

Den här självstudien visar hur du använder [Apache Spark Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) till att läsa och skriva data med [Apache Kafka](https://kafka.apache.org/) i Azure HDInsight.

Apache Spark Structured Streaming är en bearbetningsmotor för dataströmmar som bygger på Apache Spark SQL. Med den kan du uttrycka strömmande beräkningar på samma sätt som batchberäkningar av statiska data. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Structured Streaming med Kafka
> * Skapa Kafka- och Spark-kluster
> * Ladda upp anteckningsboken till Spark
> * Använda anteckningsboken
> * Rensa resurser

Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

## <a name="prerequisites"></a>Nödvändiga komponenter

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

| Batch | Strömning |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Strömningsåtgärden använder också `awaitTermination(30000)`, som stoppar strömmen efter 30 000 ms. 

Om du vill använda Structured Streaming med Kafka måste ditt projekt ha ett beroende på paketet `org.apache.spark : spark-sql-kafka-0-10_2.11`. Versionen av det här paketet ska överensstämma med version på Spark på HDInsight. För Spark 2.2.0 (tillgängligt i HDInsight 3.6) kan du hitta beroendeinformation för olika projekttyper på [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

För Jupyter Notebook som tillhandahålls i den här självstudien läser följande cell in paketberoendet:

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
    
    Azure Resource Manager-mallen finns i **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

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
    | Plats | Azure-regionen som resurserna skapas i. |
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

## <a name="upload-the-notebook"></a>Ladda upp anteckningsboken

Använd följande steg för att ladda upp anteckningsboken från projektet till din Apache Spark på HDInsight-klustret:

1. Ladda ned projektet från [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

1. Anslut till Jupyter Notebook i Apache Spark-klustret i din webbläsare. I följande URL ersätter du `CLUSTERNAME` med namnet på ditt __Apache Spark__-kluster:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Ange klusterinloggningen (administratör) och det lösenord som användes när du skapade klustret.

2. Använd knappen __Ladda upp__ överst till höger på sidan för att ladda upp filen __spark-structured-streaming-kafka.ipynb__ till klustret. Välj __Öppna__ för att påbörja uppladdningen.

    ![Använd knappen Ladda upp för att välja och ladda upp en anteckningsbok](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Välj filen KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Leta reda på posten __spark-structured-streaming-kafka.ipynb__ i listan med anteckningsböcker och välj knappen __Ladda upp__ bredvid den.

    ![Om du vill överföra anteckningsboken använder du uppladdningsknappen för posten KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Använda anteckningsboken

När filerna har laddats upp väljer du posten __spark-structured-streaming-kafka.ipynb__ för att öppna anteckningsboken. Mer information om hur du kan använda Apache Spark Structured Streaming med Kafka på HDInsight finns i anvisningarna i anteckningsboken.

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
