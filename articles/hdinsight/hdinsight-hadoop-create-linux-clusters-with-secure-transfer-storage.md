---
title: Skapa ett Hadoop-kluster med lagringskonton som använder säker överföring i Azure HDInsight
description: Lär dig hur du skapar HDInsight-kluster med Azure-lagringskonton som använder säker överföring.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: 10ec4b55bab741f19adaf193295659b7876fe02c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124772"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Skapa Apache Hadoop-kluster med säker överföring storage-konton i Azure HDInsight

Funktionen [Säker överföring krävs](../storage/common/storage-require-secure-transfer.md) förbättrar säkerheten för ditt Azure Storage-konto genom att kräva att alla förfrågningar till ditt konto görs via en säker anslutning. Den här funktionen och wasbs-schemat stöds endast av HDInsight-kluster med version 3.6 eller senare.

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar den här vägledningen måste du ha:

* **Azure-prenumeration**: Om du vill skapa ett kostnadsfritt utvärderingskonto för en månad, bläddra till [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Ett Azure Storage-konto med säker överföring aktiverat**. Anvisningar finns i [Create a storage account](../storage/common/storage-quickstart-create-account.md) (Skapa ett lagringskonto) och [Require secure transfer](../storage/common/storage-require-secure-transfer.md) (Kräva säker överföring).
* **En blobcontainer för lagringskontot**.

## <a name="create-cluster"></a>Skapa kluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


I det här avsnittet skapar du ett Hadoop-kluster i HDInsight med en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-template-deploy.md). Mallen finns i [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Du behöver inte ha någon erfarenhet av Resource Manager-mallar för att kunna följa de här självstudierna. Mer information om andra metoder för att skapa kluster och förstå de egenskaper som tillämpas i de här självstudierna finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicka på följande bild för att logga in på Azure och öppna Resource Manager-mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Följ anvisningarna för att skapa klustret med följande specifikationer: 

    - Ange HDInsight version 3.6. Version 3.6 eller senare krävs.
    - Ange ett lagringskonto som använder säker överföring.
    - Använd lagringskontots kortnamn.
    - Både lagringskontot och blobcontainern måste skapas i förväg.

      Anvisningar finns i avsnittet [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Om du använder skriptåtgärder för att skapa egna konfigurationsfiler måste du använda wasbs i följande inställningar:

- fs.defaultFS (core-site)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Lägga till fler lagringskonton

Det finns flera alternativ för att lägga till ytterligare lagringskonton som använder säker överföring:

- Ändra Azure Resource Manager-mallen i det sista avsnittet.
- Skapa ett kluster med hjälp av [Azure-portalen](https://portal.azure.com) och ange ett länkat lagringskonto.
- Använd skriptåtgärder om du vill lägga till fler lagringskonton som använder säker överföring i ett befintligt HDInsight-kluster. Mer information finns i [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="next-steps"></a>Nästa steg
I den här kursen har du lärt dig hur du skapar ett HDInsight-kluster och hur du aktiverar säker överföring för lagringskontona.

Mer information om att analysera data med HDInsight finns i följande artiklar:

* Mer information om hur du använder [Apache Hive](https://hive.apache.org/) med HDInsight, inklusive hur du gör Hive-frågor från Visual Studio, se [använda Apache Hive med HDInsight][hdinsight-use-hive].
* Vill veta mer om [Apache Pig](https://pig.apache.org/), ett språk som används för att omvandla data, se [använda Apache Pig med HDInsight][hdinsight-use-pig].
* Vill veta mer om [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), ett sätt att skriva program som bearbetar data i Hadoop, se [använda Apache Hadoop MapReduce med HDInsight][hdinsight-use-mapreduce].
* Läs om hur du använder HDInsight Tools för Visual Studio för att analysera data i HDInsight i [Kom igång med Visual Studio Apache Hadoop-verktyg för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Mer information om hur HDInsight lagrar data eller hur du hämtar data till HDInsight finns i följande artiklar:

* Mer information om hur HDInsight använder Azure Storage finns i [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight][hdinsight-upload-data].

Mer information om hur du skapar eller hanterar ett HDInsight-kluster finns i följande artiklar:

* Mer information om att hantera ditt Linux-baserade HDInsight-kluster finns i [Hantera HDInsight-kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md).
* Mer information om de alternativ som du kan välja när du skapar ett HDInsight-kluster finns i [Skapa HDInsight i Linux med anpassade alternativ](hdinsight-hadoop-provision-linux-clusters.md).
* Om du är bekant med Linux- och Apache Hadoop, men vill få närmare information om Hadoop på HDInsight, se [arbeta med HDInsight på Linux](hdinsight-hadoop-linux-information.md). Detta ger information som:

  * URL: er för tjänsterna på klustret, till exempel [Apache Ambari](https://ambari.apache.org/) och [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * Platsen för [Apache Hadoop](https://hadoop.apache.org/) filer och exempel på det lokala filsystemet
  * Användning av Azure Storage (WASB) i stället för [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) som standarddata lagra

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
