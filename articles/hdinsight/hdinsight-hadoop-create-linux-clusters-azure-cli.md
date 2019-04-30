---
title: Skapa Apache Hadoop-kluster med hjälp av Azure klassiska CLI - Azure HDInsight
description: Lär dig hur du skapar HDInsight-kluster med hjälp av plattformsoberoende Azure klassiskt CLI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 21985b009694dc5a21c65d4c9dc9536cf6c01a0e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128727"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Skapa HDInsight-kluster med den klassiska Azure-CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Stegen i den här genomgången för dokumentet som skapar ett HDInsight 3.5-kluster med hjälp av den klassiska Azure-CLI.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure klassiskt CLI**. Stegen i det här dokumentet testades senast med klassiska Azure-CLI version 0.10.14.

## <a name="log-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration

Följ stegen i [Anslut till en Azure-prenumeration från Azure-kommandoradsgränssnittet](/cli/azure/authenticate-azure-cli) och ansluta till din prenumeration med hjälp av den **inloggning** metod.

## <a name="create-a-cluster"></a>Skapa ett kluster

Följande steg ska utföras från en kommandorad, till exempel PowerShell eller Bash.

1. Använd följande kommando för att autentisera till Azure-prenumerationen:

        azure login

    Du uppmanas att ange ditt namn och lösenord. Om du har flera Azure-prenumerationer kan du använda `azure account set <subscriptionname>` att ange den prenumeration som använder de klassiska CLI-kommandona.

2. Växla till läget Azure Resource Manager, med följande kommando:

        azure config mode arm

3. Skapa en resursgrupp. Den här resursgruppen innehåller HDInsight-kluster och tillhörande storage-konto.

        azure group create groupname location

    * Ersätt `groupname` med ett unikt namn för gruppen.

    * Ersätt `location` med den geografiska region som du vill skapa gruppen i.

       En lista över giltiga platser, använder den `azure location list` kommandot och Använd sedan någon av platserna från den `Name` kolumn.

4. Skapa ett lagringskonto. Det här lagringskontot används som standardlagringen för HDInsight-klustret.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Ersätt `groupname` med namnet på den grupp som skapades i föregående steg.

    * Ersätt `location` med samma plats som används i föregående steg.

    * Ersätt `storagename` med ett unikt namn för lagringskontot.

        > [!NOTE]  
        > Mer information om de parametrar som används i det här kommandot använder `azure storage account create -h` Visa hjälp för det här kommandot.

5. Hämta nyckeln som används för att komma åt lagringskontot.

        azure storage account keys list -g groupname storagename

    * Ersätt `groupname` med resursgruppens namn.
    * Ersätt `storagename` med namnet på lagringskontot.

      I de data som returneras, spara den `key` för `key1`.

6. Skapa ett HDInsight-kluster.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Ersätt `groupname` med resursgruppens namn.

    * Ersätt `Hadoop` med den typ av kluster som du vill skapa. Till exempel `Hadoop`, `HBase`, `Kafka`, `Spark`, eller `Storm`.

      > [!IMPORTANT]  
      > HDInsight kluster finns olika typer, vilket motsvarar arbetsbelastning eller teknik som klustret är anpassad till. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase i ett kluster.

    * Ersätt `location` med samma plats som används i föregående steg.

    * Ersätt `storagename` med namnet på lagringskontot.

    * Ersätt `storagekey` med den nyckel som hämtades i föregående steg.

    * För den `--defaultStorageContainer` parametern, använder samma namn som du använder för klustret.

    * Ersätt `admin` och `httppassword` med namn och lösenord som du vill använda vid åtkomst till klustret via HTTPS.

    * Ersätt `sshuser` och `sshuserpassword` med användarnamnet och lösenordet som du vill använda vid åtkomst till klustret med SSH

      > [!IMPORTANT]  
      > Det här exemplet skapar ett kluster med två arbetarnoder. Du kan också ändra antalet arbetarnoder när klustret har skapats genom att utföra skalningsåtgärder. Om du tänker använda mer än 32 arbetsnoder, måste du välja en head nodstorlek med minst 8 kärnor och 14 GB RAM-minne. Du kan ange huvudnoden storlek med hjälp av den `--headNodeSize` parametern när klustret skapas.
      >
      > Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).
      
      Det kan ta flera minuter innan klustret skapas ska slutföras. Normalt cirka 15.

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett HDInsight-kluster med hjälp av klassiska CLI, Använd följande för att lära dig hur du arbetar med ditt kluster:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-kluster

* [Utveckla Java-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
