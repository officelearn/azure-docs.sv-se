---
title: Apache Spark-strömning med Apache Kafka – Azure HDInsight
description: Lär dig hur du använder Apache Spark för att strömdata till eller från Apache Kafka med DStreams. I det här exemplet strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
keywords: kafka exempel zookeeper kafka, spark-strömning kafka, spark-strömning kafka-exempel
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 5f4b7994ad5061c64021f3625f42ac028cbee859
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653415"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark-strömning (DStream) exempel med Apache Kafka på HDInsight

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) att sända data till eller från [Apache Kafka](https://kafka.apache.org/) på HDInsight med hjälp av [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Det här exemplet används en [Jupyter Notebook](https://jupyter.org/) som körs på Spark-klustret.

> [!NOTE]  
> Stegen i det här dokumentet skapar en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

> [!IMPORTANT]  
> Det här exemplet används DStreams, vilket är en äldre strömmande Spark-teknik. Ett exempel som använder nyare Spark streaming funktioner finns i den [Spark Structured Streaming med Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentet.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till asynkrona meddelandeköer i Kafka via offentligt Internet. Allt som pratar med Kafka måste finnas i samma Azure-nätverket som noder i Kafka-klustret. I det här exemplet finns både Kafka och Spark-kluster i Azure-nätverk. Följande diagram visar hur kommunikation flödar mellan kluster:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Om Kafka själva är begränsad till kommunikation inom det virtuella nätverket kan kan andra tjänster på klustret, till exempel SSH och Ambari nås via internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Du kan skapa ett Azure-nätverk, Kafka och Spark-kluster manuellt, men det är enklare att använda en Azure Resource Manager-mall. Använd följande steg för att distribuera ett Azure-nätverk, Kafka och Spark-kluster på Azure-prenumerationen.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager-mallen finns i **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]  
    > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Den här mallen skapar ett Kafka-kluster som innehåller tre arbetarnoder.

    Den här mallen skapar ett HDInsight 3.6-klustret för både Kafka och Spark.

2. Använd följande information för att fylla i posterna i den **anpassad distribution** avsnittet:
   
    ![Anpassade HDInsight-distribution](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Resursgrupp**: Skapa en grupp eller välj en befintlig. Den här gruppen innehåller HDInsight-klustret.

    * **Plats**: Välj en plats geografiskt nära dig.

    * **Basera klusternamnet**: Det här värdet används som huvudnamnet för Spark och Kafka-kluster. Till exempel innebär **hdistreaming** skapar ett Spark-kluster med namnet __spark-hdistreaming__ och ett Kafka-kluster med namnet **kafka hdistreaming**.

    * **Kluster-användarnamnet för inloggningen**: Administratörsanvändarnamn för Spark och Kafka-kluster.

    * **Kluster inloggningslösenordet**: Administratörslösenord för Spark och Kafka-kluster.

    * **SSH-användarnamn**: SSH-användaren skapar för Spark och Kafka-kluster.

    * **SSH lösenord**: Lösenordet för SSH-användare för Spark och Kafka-kluster.

3. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

4. Välj slutligen **köp**. Det tar cirka 20 minuter för att skapa kluster.

När resurserna har skapats, visas en sammanfattning av sida.

![Resursgrupp sammanfattning för det virtuella nätverket och -kluster](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]  
> Lägg märke till att namnen på HDInsight-kluster är **spark-BASENAME** och **kafka BASENAME**, där BASENAME är det namn du angav i mallen. Du kan använda dessa namn i senare steg när du ansluter till klustren.

## <a name="use-the-notebooks"></a>Använd de bärbara datorerna

Koden för exemplet som beskrivs i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

För att slutföra det här exemplet, följer du stegen i den `README.md`.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Eftersom stegen i det här dokumentet skapar båda klustren i samma Azure-resursgruppen kan du ta bort resursgruppen på Azure-portalen. Tar bort gruppen tar bort alla resurser som skapats genom att följa det här dokumentet, Azure Virtual Network och storage-konto som används av klustren.

## <a name="next-steps"></a>Nästa steg

I detta exempel beskrivs hur du använder Spark för att läsa och skriva till Kafka. Använd följande länkar för att upptäcka andra sätt att arbeta med Kafka:

* [Kom igång med Apache Kafka på HDInsight](kafka/apache-kafka-get-started.md)
* [Använd MirrorMaker för att skapa en replik av Apache Kafka på HDInsight](kafka/apache-kafka-mirroring.md)
* [Använda Apache Storm med Apache Kafka på HDInsight](hdinsight-apache-storm-with-kafka.md)

