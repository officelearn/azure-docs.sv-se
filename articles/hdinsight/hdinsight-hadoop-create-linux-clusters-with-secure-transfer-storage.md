---
title: Apache Hadoop & säker transaktions lagring – Azure HDInsight
description: Lär dig hur du skapar HDInsight-kluster med Azure-lagringskonton som använder säker överföring.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: b8a5d48e977009cf592f271760232a3b0138c8c5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044949"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Skapa Apache Hadoop kluster med lagrings konton för säker överföring i Azure HDInsight

Funktionen [Säker överföring krävs](../storage/common/storage-require-secure-transfer.md) förbättrar säkerheten för ditt Azure Storage-konto genom att kräva att alla förfrågningar till ditt konto görs via en säker anslutning. Den här funktionen och wasbs-schemat stöds endast av HDInsight-kluster med version 3.6 eller senare.

## <a name="prerequisites"></a>Krav

Innan du börjar den här artikeln måste du ha:

* **Azure-prenumeration**: Gå till [azure.microsoft.com/free](https://azure.microsoft.com/free) för att skapa ett kostnadsfritt provkonto för en månad.
* **Ett Azure Storage-konto med säker överföring aktiverat**. Anvisningar finns i [Create a storage account](../storage/common/storage-quickstart-create-account.md) (Skapa ett lagringskonto) och [Require secure transfer](../storage/common/storage-require-secure-transfer.md) (Kräva säker överföring).
* **En blobcontainer för lagringskontot**.

## <a name="create-cluster"></a>Skapa kluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

I det här avsnittet skapar du ett Hadoop-kluster i HDInsight med en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-template-deploy.md). Mallen finns i [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Du behöver inte ha någon erfarenhet av Resource Manager-mallar för att kunna följa den här artikeln. För andra metoder för att skapa kluster och förstå de egenskaper som används i den här artikeln, se [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicka på följande bild för att logga in på Azure och öppna Resource Manager-mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

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
I den här artikeln har du lärt dig hur du skapar ett HDInsight-kluster och aktiverar säker överföring till lagrings kontona.

Mer information om att analysera data med HDInsight finns i följande artiklar:

* Mer information om hur du använder [Apache Hive](https://hive.apache.org/) med HDInsight, inklusive hur du utför Hive-frågor från Visual Studio, finns i [använda Apache Hive med HDInsight][hdinsight-use-hive].
* Om du vill lära dig mer om [Apache gris](https://pig.apache.org/), ett språk som används för att transformera data, se [Använd Apache-gris med HDInsight][hdinsight-use-pig].
* För att lära dig mer om [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), ett sätt att skriva program som bearbetar data i Hadoop, se [använda Apache Hadoop MapReduce med HDInsight][hdinsight-use-mapreduce].
* Information om hur du använder HDInsight Tools för Visual Studio för att analysera data i HDInsight finns i [komma igång med Visual Studio Apache Hadoop Tools för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Mer information om hur HDInsight lagrar data eller hur du hämtar data till HDInsight finns i följande artiklar:

* Mer information om hur HDInsight använder Azure Storage finns i [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight][hdinsight-upload-data].

Mer information om hur du skapar eller hanterar ett HDInsight-kluster finns i följande artiklar:

* Mer information om att hantera ditt Linux-baserade HDInsight-kluster finns i [Hantera HDInsight-kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md).
* Mer information om de alternativ som du kan välja när du skapar ett HDInsight-kluster finns i [Skapa HDInsight i Linux med anpassade alternativ](hdinsight-hadoop-provision-linux-clusters.md).
* Om du är bekant med Linux och Apache Hadoop, men vill veta mer om Hadoop på HDInsight, se [arbeta med HDInsight på Linux](hdinsight-hadoop-linux-information.md). Detta ger information som:

  * URL: er för tjänster som finns i klustret, till exempel [Apache Ambari](https://ambari.apache.org/) och [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * Platsen för [Apache Hadoop](https://hadoop.apache.org/) filer och exempel i det lokala fil systemet
  * Användning av Azure Storage (WASB) i stället för [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) som standard data lager

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
