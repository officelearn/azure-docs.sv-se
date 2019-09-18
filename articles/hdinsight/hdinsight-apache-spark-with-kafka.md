---
title: Apache Spark strömning med Apache Kafka – Azure HDInsight
description: Lär dig hur du använder Apache Spark för att strömma data till eller från Apache Kafka med hjälp av DStreams. I det här exemplet strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
keywords: Kafka-exempel, Kafka Zookeeper, Spark streaming Kafka, Spark streaming Kafka-exempel
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 201e86908b61efa72eed76346f70cfc55e08d03c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076800"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming (DStream)-exempel med Apache Kafka på HDInsight

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) för att strömma data till eller från [Apache Kafka](https://kafka.apache.org/) i HDInsight med hjälp av [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). I det här exemplet används en [Jupyter Notebook](https://jupyter.org/) som körs i Spark-klustret.

> [!NOTE]  
> Stegen i det här dokumentet skapar en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

> [!IMPORTANT]  
> I det här exemplet används DStreams, som är en äldre Spark streaming-teknik. Ett exempel som använder nya funktioner för Spark-direktuppspelning finns i [Spark Structured streaming med Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) Document.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till asynkrona meddelandeköer i Kafka via offentligt Internet. Allt som pratar med Kafka måste finnas i samma virtuella Azure-nätverk som noderna i Kafka-klustret. I det här exemplet finns både Kafka-och Spark-klustren i ett virtuellt Azure-nätverk. Följande diagram visar hur kommunikations flöden mellan klustren:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Även om Kafka är begränsad till kommunikation inom det virtuella nätverket kan andra tjänster i klustret, till exempel SSH och Ambari, nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Även om du kan skapa ett virtuellt Azure-nätverk, Kafka och Spark-kluster manuellt, är det enklare att använda en Azure Resource Manager mall. Använd följande steg för att distribuera ett virtuellt Azure-nätverk, Kafka och Spark-kluster till din Azure-prenumeration.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager-mallen finns i **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Den här mallen skapar ett Kafka-kluster som innehåller tre arbetarnoder.

    Den här mallen skapar ett HDInsight 3,6-kluster för både Kafka och Spark.

2. Använd följande information för att fylla i posterna i avsnittet **Anpassad distribution** :

    ![Anpassade parametrar för HDInsight-distribution](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

    * **Resursgrupp**: Skapa en grupp eller Välj en befintlig. Den här gruppen innehåller HDInsight-klustret.

    * **Plats**: Välj en plats geografiskt nära dig.

    * **Namn på bas kluster**: Det här värdet används som grund namn för Spark-och Kafka-klustren. Om du till exempel anger **hdistreaming** skapas ett Spark-kluster med namnet __Spark-Hdistreaming__ och ett Kafka-kluster med namnet **Kafka-hdistreaming**.

    * **Användar namn för kluster inloggning**: Administratörs användar namnet för Spark-och Kafka-klustren.

    * **Lösen ord för kluster inloggning**: Administratörs användarens lösen ord för Spark-och Kafka-kluster.

    * **SSH-användar namn**: SSH-användaren som ska skapas för Spark-och Kafka-kluster.

    * **SSH-lösenord**: Lösen ordet för SSH-användaren för Spark-och Kafka-klustren.

3. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

4. Välj slutligen **köp**. Det tar ungefär 20 minuter att skapa klustren.

När resurserna har skapats visas en sammanfattnings sida.

![Resurs grupps Sammanfattning för VNet och kluster](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Observera att namnen på HDInsight-klustren är **Spark-BASENAME** och **Kafka-BASENAME**, där BASENAME är det namn som du angav för mallen. Du kan använda dessa namn i senare steg när du ansluter till klustren.

## <a name="use-the-notebooks"></a>Använda antecknings böckerna

Koden för exemplet som beskrivs i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Slutför det här exemplet genom att följa stegen i `README.md`.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Eftersom stegen i det här dokumentet skapar båda klustren i samma Azure-resurs grupp kan du ta bort resurs gruppen i Azure Portal. Om du tar bort gruppen tas alla resurser som skapats i följande dokument, Azure-Virtual Network och lagrings kontot som används av klustren bort.

## <a name="next-steps"></a>Nästa steg

I det här exemplet har du lärt dig hur du använder Spark för att läsa och skriva till Kafka. Använd följande länkar för att identifiera andra sätt att arbeta med Kafka:

* [Kom igång med Apache Kafka på HDInsight](kafka/apache-kafka-get-started.md)
* [Använd MirrorMaker för att skapa en replik av Apache Kafka på HDInsight](kafka/apache-kafka-mirroring.md)
* [Använda Apache Storm med Apache Kafka på HDInsight](hdinsight-apache-storm-with-kafka.md)

