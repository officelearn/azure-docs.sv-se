---
title: Apache Spark streaming with Apache Kafka - Azure HDInsight
description: Learn how to use Apache Spark to stream data into or out of Apache Kafka using DStreams. I det här exemplet strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d868cdd346c79cf77d4f8c1ea6e4b20adcd99b6c
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327404"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming (DStream) example with Apache Kafka on HDInsight

Learn how to use [Apache Spark](https://spark.apache.org/) to stream data into or out of [Apache Kafka](https://kafka.apache.org/) on HDInsight using [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). This example uses a [Jupyter Notebook](https://jupyter.org/) that runs on the Spark cluster.

> [!NOTE]  
> Stegen i det här dokumentet skapar en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

> [!IMPORTANT]  
> This example uses DStreams, which is an older Spark streaming technology. For an example that uses newer Spark streaming features, see the [Spark Structured Streaming with Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) document.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka on HDInsight doesn't provide access to the Kafka brokers over the public internet. Anything that talks to Kafka must be in the same Azure virtual network as the nodes in the Kafka cluster. For this example, both the Kafka and Spark clusters are located in an Azure virtual network. The following diagram shows how communication flows between the clusters:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Though Kafka itself is limited to communication within the virtual network, other services on the cluster such as SSH and Ambari can be accessed over the internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

While you can create an Azure virtual network, Kafka, and Spark clusters manually, it's easier to use an Azure Resource Manager template. Use the following steps to deploy an Azure virtual network, Kafka, and Spark clusters to your Azure subscription.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager-mallen finns i **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Den här mallen skapar ett Kafka-kluster som innehåller tre arbetarnoder.

    This template creates an HDInsight 3.6 cluster for both Kafka and Spark.

1. Use the following information to populate the entries on the **Custom deployment** section:

    |Egenskap |Värde |
    |---|---|
    |Resursgrupp|Create a group or select an existing one.|
    |Plats|Select a location geographically close to you.|
    |Base Cluster Name|This value is used as the base name for the Spark and Kafka clusters. For example, entering **hdistreaming** creates a Spark cluster named __spark-hdistreaming__ and a Kafka cluster named **kafka-hdistreaming**.|
    |Användarnamn för klusterinloggning|The admin user name for the Spark and Kafka clusters.|
    |Lösenord för klusterinloggning|The admin user password for the Spark and Kafka clusters.|
    |SSH-användarnamn|The SSH user to create for the Spark and Kafka clusters.|
    |SSH-lösenord|The password for the SSH user for the Spark and Kafka clusters.|

    ![HDInsight custom deployment parameters](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

1. Finally, select **Purchase**. It takes about 20 minutes to create the clusters.

Once the resources have been created, a summary page appears.

![Resource group summary for the vnet and clusters](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Notice that the names of the HDInsight clusters are **spark-BASENAME** and **kafka-BASENAME**, where BASENAME is the name you provided to the template. You use these names in later steps when connecting to the clusters.

## <a name="use-the-notebooks"></a>Use the notebooks

Koden för exemplet som beskrivs i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Since the steps in this document create both clusters in the same Azure resource group, you can delete the resource group in the Azure portal. Deleting the group removes all resources created by following this document, the Azure Virtual Network, and storage account used by the clusters.

## <a name="next-steps"></a>Nästa steg

In this example, you learned how to use Spark to read and write to Kafka. Use the following links to discover other ways to work with Kafka:

* [Get started with Apache Kafka on HDInsight](kafka/apache-kafka-get-started.md)
* [Use MirrorMaker to create a replica of Apache Kafka on HDInsight](kafka/apache-kafka-mirroring.md)
* [Use Apache Storm with Apache Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md)
