---
title: Apache Spark streaming med Apache Kafka - Azure HDInsight
description: Lär dig hur du använder Apache Spark för att strömma data till eller från Apache Kafka med DStreams. I det här exemplet strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d868cdd346c79cf77d4f8c1ea6e4b20adcd99b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327404"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming (DStream) exempel med Apache Kafka på HDInsight

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) för att strömma data till eller från [Apache Kafka](https://kafka.apache.org/) på HDInsight med [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). I det här exemplet används en [Jupyter-anteckningsbok](https://jupyter.org/) som körs i Spark-klustret.

> [!NOTE]  
> Stegen i det här dokumentet skapar en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

> [!IMPORTANT]  
> I det här exemplet används DStreams, som är en äldre Spark-strömningsteknik. Ett exempel som använder nyare spark-streamingfunktioner finns i [Spark Structured Streaming med Apache Kafka-dokumentet.](hdinsight-apache-kafka-spark-structured-streaming.md)

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till Kafka mäklare över det offentliga internet. Allt som talar med Kafka måste finnas i samma virtuella Azure-nätverk som noderna i Kafka-klustret. I det här exemplet finns både Kafka- och Spark-kluster i ett virtuellt Azure-nätverk. Följande diagram visar hur kommunikationen flödar mellan klustren:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Även Kafka själv är begränsad till kommunikation inom det virtuella nätverket, andra tjänster på klustret som SSH och Ambari kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Även om du kan skapa ett virtuellt Azure-nätverk, Kafka och Spark-kluster manuellt, är det enklare att använda en Azure Resource Manager-mall. Följ följande steg för att distribuera ett virtuella Azure-nätverk, Kafka och Spark-kluster till din Azure-prenumeration.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager-mallen **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**finns på .

    > [!WARNING]  
    > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Den här mallen skapar ett Kafka-kluster som innehåller tre arbetarnoder.

    Den här mallen skapar ett HDInsight 3.6-kluster för både Kafka och Spark.

1. Använd följande information för att fylla i posterna i avsnittet **Anpassad distribution:**

    |Egenskap |Värde |
    |---|---|
    |Resursgrupp|Skapa en grupp eller välj en befintlig.|
    |Location|Välj en plats geografiskt nära dig.|
    |Namn på baskluster|Det här värdet används som basnamn för Spark- och Kafka-kluster. Om du till exempel anger **hdistreaming** skapas ett Spark-kluster med namnet __spark-hdistreaming__ och ett Kafka-kluster med namnet **kafka-hdistreaming**.|
    |Användarnamn för klusterinloggning|Administratörens användarnamn för Spark- och Kafka-kluster.|
    |Lösenord för klusterinloggning|Administratörsanvändarlösenordet för Spark- och Kafka-kluster.|
    |SSH-användarnamn|SSH-användaren som ska skapas för Spark- och Kafka-kluster.|
    |SSH-lösenord|Lösenordet för SSH-användaren för Spark- och Kafka-kluster.|

    ![Anpassade distributionsparametrar för HDInsight](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

1. Slutligen väljer du **Köp**. Det tar cirka 20 minuter att skapa kluster.

När resurserna har skapats visas en sammanfattningssida.

![Sammanfattning av resursgrupp för vnet och kluster](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Observera att namnen på HDInsight-klustren är **spark-BASENAME** och **kafka-BASENAME**, där BASENAME är det namn du angav i mallen. Du använder dessa namn i senare steg när du ansluter till klustren.

## <a name="use-the-notebooks"></a>Använda anteckningsböckerna

Koden för det exempel som beskrivs [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)i det här dokumentet finns på .

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Eftersom stegen i det här dokumentet skapar båda klustren i samma Azure-resursgrupp kan du ta bort resursgruppen i Azure-portalen. Om du tar bort gruppen tas alla resurser som skapas bort genom att följa det här dokumentet, Azure Virtual Network och lagringskontot som används av klustren.

## <a name="next-steps"></a>Nästa steg

I det här exemplet lärde du dig hur du använder Spark för att läsa och skriva till Kafka. Använd följande länkar för att upptäcka andra sätt att arbeta med Kafka:

* [Kom igång med Apache Kafka på HDInsight](kafka/apache-kafka-get-started.md)
* [Använd MirrorMaker för att skapa en kopia av Apache Kafka på HDInsight](kafka/apache-kafka-mirroring.md)
* [Använd Apache Storm med Apache Kafka på HDInsight](hdinsight-apache-storm-with-kafka.md)
