---
title: Apache Spark streaming med Kafka - Azure HDInsight | Microsoft Docs
description: "Lär dig hur du använder för att sända data till eller från Apache Kafka med DStreams Spark Apache Spark. I det här exemplet strömma data med hjälp av en Jupyter-anteckningsbok från Spark på HDInsight."
keywords: "kafka exempel kafka zookeeper Väck kafka, spark streaming kafka exempel för strömning"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: larryfr
ms.openlocfilehash: a780a7d954486dbce402e4bf45be55af8ad36ab9
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>Apache Spark streaming (DStream) exempel med Kafka på HDInsight

Lär dig hur du använder Spark Apache Spark att sända data till eller från Apache Kafka på HDInsight med DStreams. Det här exemplet används en Jupyter-anteckningsbok som körs på Spark-klustret.

> [!NOTE]
> Stegen i det här dokumentet skapa ett Azure-resursgrupp som innehåller både en Spark i HDInsight och en Kafka på HDInsight-kluster. Dessa kluster finns både i ett Azure Virtual Network, vilket innebär att Spark-klustret kan kommunicera direkt med Kafka kluster.
>
> Kom ihåg att ta bort kluster för att undvika överdriven avgifter när du är klar med steg i det här dokumentet.

> [!IMPORTANT]
> Det här exemplet använder DStreams, vilket är en äldre strömmande Spark-teknik. Ett exempel som använder nyare Spark streaming funktioner finns i [Spark strukturerade strömning med Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentet.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till Kafka mäklare via det offentliga internet. Allt som kommunicerar Kafka måste finnas i samma virtuella Azure-nätverket som noder i klustret Kafka. I det här exemplet finns både Kafka och Spark-kluster i Azure-nätverk. Följande diagram visar hur kommunikation som flödar mellan kluster:

![Diagram över Spark och Kafka kluster i Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Även om Kafka själva är begränsad till kommunikation inom det virtuella nätverket, kan andra tjänster på klustret, till exempel SSH och Ambari nås via internet. Mer information om de offentliga portarna som är tillgängliga med HDInsight finns [portar och URI: er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Du kan skapa en Azure-nätverk, Kafka, och Spark-kluster manuellt, men det är enklare att använda en Azure Resource Manager-mall. Använd följande steg för att distribuera ett Azure-nätverk, Kafka, och vidtar kluster på Azure-prenumerationen.

1. Använd knappen följande för att logga in på Azure och öppna mallen i Azure-portalen.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager-mallen finns på **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Den här mallen skapar ett Kafka kluster som innehåller tre arbetsnoderna.

    Den här mallen skapar ett kluster i HDInsight 3,6 för både Kafka och Spark.

2. Använd följande information för att fylla i posterna på den **anpassad distribution** avsnitt:
   
    ![HDInsight anpassad distribution](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Resursgruppen**: skapa en grupp eller välj en befintlig. Den här gruppen innehåller HDInsight-klustret.

    * **Plats**: Välj en plats geografiskt nära dig.

    * **Basera klusternamnet**: det här värdet används som det grundläggande namnet på Spark och Kafka kluster. Ange till exempel **hdi** skapar ett Spark-kluster med namnet __spark hdi__ och ett Kafka kluster med namnet **kafka hdi**.

    * **Klustrets inloggningsnamn**: admin användarnamn för Spark och Kafka kluster.

    * **Klustret inloggningslösenordet**: administratörslösenord för Spark och Kafka kluster.

    * **SSH-användarnamn**: SSH-användare skapa för Spark och Kafka kluster.

    * **SSH-lösenordet**: lösenord för SSH-användare för Spark och Kafka kluster.

3. Läs den **villkor**, och välj sedan **jag samtycker till villkoren som anges ovan**.

4. Kontrollera slutligen **fäst på instrumentpanelen** och välj sedan **inköp**. Det tar ungefär 20 minuter för att skapa kluster.

När resurserna som har skapats, visas en översikt över sida.

![Resursgruppen sammanfattning för virtuella nätverk och kluster](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Lägg märke till att namnen på HDInsight-kluster **spark BASENAME** och **kafka BASENAME**, där BASENAME är det namn du angav i mallen. Du kan använda dessa namn i senare steg när du ansluter till kluster.

## <a name="use-the-notebooks"></a>Använd de bärbara datorerna

Koden för det exempel som beskrivs i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

För att slutföra det här exemplet, följer du stegen i den `README.md`.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Eftersom stegen i det här dokumentet skapa båda klustren i samma Azure resursgrupp måste du ta bort resursgruppen i Azure-portalen. Ta bort gruppen tar du bort alla resurser som har skapats genom att följa det här dokumentet, Azure Virtual Network och storage-konto som används av kluster.

## <a name="next-steps"></a>Nästa steg

I det här exemplet du har lärt dig hur du använder Spark att läsa och skriva till Kafka. Använd följande länkar för att identifiera andra sätt att arbeta med Kafka:

* [Kom igång med Apache Kafka på HDInsight](kafka/apache-kafka-get-started.md)
* [Använd MirrorMaker för att skapa en replik av Kafka på HDInsight](kafka/apache-kafka-mirroring.md)
* [Använda Apache Storm med Kafka på HDInsight](hdinsight-apache-storm-with-kafka.md)

