---
title: Apache Spark Structured Streaming med Kafka – Azure HDInsight | Microsoft Docs
description: Lär dig använda Apache Spark-strömning (DStream) till att hämta data till eller från Apache Kafka. I det här exemplet strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Använda Apache Spark Structured Streaming med Kafka på HDInsight

Läs om hur du använder Spark Structured Streaming till att läsa data från Apache Kafka på Azure HDInsight.

Apache Spark Structured Streaming är en bearbetningsmotor för dataströmmar som bygger på Apache Spark SQL. Med den kan du uttrycka strömmande beräkningar på samma sätt som batchberäkningar av statiska data. Läs mer om Structured Streaming i [Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) på Apache.org.

> [!IMPORTANT]
> I det här exemplet används Apache Spark 2.2 på HDInsight 3.6.
>
> Stegen i det här dokumentet skapar en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till asynkrona meddelandeköer i Kafka via offentligt Internet. Allt som använder Kafka måste finnas i samma virtuella Azure-nätverk. I den här självstudien finns både Kafka- och Apache Spark-klustren i samma virtuella Azure-nätverk. 

Följande diagram visar hur kommunikation flödar mellan Apache Spark och Kafka:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka-tjänsten är begränsad till kommunikation inom det virtuella nätverket. Andra tjänster på klustret, till exempel SSH och Ambari, kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

I följande steg kan du använda en Azure Resource Manager-mall för att på ett enkelt sätt skapa Kafka- och Apache Spark-kluster i ett virtuellt nätverk.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager-mallen finns i **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Den här mallen skapar följande resurser:

    * En Kafka på HDInsight 3.6-klustret.
    * En Apache Spark 2.2.0 på HDInsight 3.6-klustret.
    * Ett virtuellt Azure-nätverk som innehåller HDInsight-klustren.

    > [!IMPORTANT]
    > Anteckningsboken för Structured Streaming som används i det här exemplet kräver Apache Spark i HDInsight 3.6. Om du använder en tidigare version av Apache Spark i HDInsight får du ett felmeddelande när du använder anteckningsboken.

2. Använd följande information för att fylla i posterna i avsnittet **Anpassad mall**:

    | Inställning | Värde |
    | --- | --- |
    | Prenumeration | Din Azure-prenumeration |
    | Resursgrupp | Resursgruppen som innehåller resurserna. |
    | Plats | Azure-regionen som resurserna skapas i. |
    | Apache Spark-klusternamn | Namnet på Apache Spark-klustret. |
    | Kafka-klusternamn | Namnet på Kafka-klustret. |
    | Användarnamn för klusterinloggning | Ett administratörsanvändarnamn för klustren. |
    | Lösenord för klusterinloggning | Ett administratörslösenord för klustren. |
    | SSH-användarnamn | SSH-användare som ska skapas för klustren. |
    | SSH-lösenord | Lösenord för SSH-användaren. |
   
    ![Skärmbild av den anpassade mallen](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. Markera slutligen **Fäst på instrumentpanelen** och välj sedan **Inköp**. 

> [!NOTE]
> Det kan ta upp till 20 minuter att skapa klustren.

## <a name="get-the-notebook"></a>Hämta anteckningsboken

Koden för exemplet som beskrivs i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Ladda upp anteckningsböckerna

Använd följande steg för att ladda upp anteckningsboken från projektet till din Apache Spark på HDInsight-klustret:

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

Nu när du har lärt dig hur du använder Apache Spark Structured Streaming, kan du i följande dokument läsa mer om hur du arbetar med Spark och Kafka:

* [Använda Apache Spark-strömning (DStream) med Kafka](hdinsight-apache-spark-with-kafka.md).
* [Börja med Jupyter Notebook och Apache Spark i HDInsight](spark/apache-spark-jupyter-spark-sql.md)