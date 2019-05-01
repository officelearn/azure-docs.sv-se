---
title: Skapa Apache Hadoop-kluster med hjälp av PowerShell - Azure HDInsight
description: Lär dig hur du skapar Apache Hadoop, Apache HBase, Apache Storm eller Apache Spark-kluster på Linux för HDInsight med hjälp av Azure PowerShell.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 757c3ef55e93397030b0fe847dc19d81a7bbe568
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713844"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Skapa Linux-baserade kluster i HDInsight med Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell är en kraftfull skriptmiljö som du kan använda för att styra och automatisera distributionen och hanteringen av dina arbetsbelastningar i Microsoft Azure. Det här dokumentet innehåller information om hur du skapar ett Linux-baserade HDInsight-kluster med hjälp av Azure PowerShell. Den innehåller också ett exempelskript.

> [!NOTE]  
> Azure PowerShell är endast tillgängligt på Windows-klienter. Om du använder en Linux-, Unix- eller Mac OS X-klient, se [skapar ett Linux-baserade HDInsight-kluster med klassiska Azure-CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) för information om hur du använder den klassiska CLI för att skapa ett kluster.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du måste ha följande innan du sätter igång:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > Azure PowerShell-stöd för hantering av HDInsight-resurser med hjälp av Azure Service Manager **är föråldrat** och togs bort den 1 januari 2017. I stegen i det här dokumentet används de nya HDInsight-cmdletarna som fungerar med Azure Resource Manager.
    >
    > Följ stegen i [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) att installera den senaste versionen av Azure PowerShell. Om du har skript som behöver ändras för att använda de nya cmdletarna som fungerar med Azure Resource Manager, hittar du mer information i [Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="create-cluster"></a>Skapa kluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Om du vill skapa ett HDInsight-kluster med hjälp av Azure PowerShell, måste du utföra följande procedurer:

* Skapa en Azure-resursgrupp
* Skapa ett Azure Storage-konto
* Skapa en Azure Blob-behållare
* Skapa ett HDInsight-kluster

Följande skript visar hur du skapar ett nytt kluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

De värden som du anger för klusterinloggning används för att skapa Hadoop-användarkonto för klustret. Använd det här kontot för att ansluta till tjänster som finns i klustret, till exempel web UIs eller REST API: er.

De värden som du anger för SSH-användaren används för att skapa SSH-användare för klustret. Använd det här kontot för att starta en fjärrsession med SSH på klustret och köra jobb. Mer information finns i dokumentet [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Om du planerar att använda mer än 32 arbetsnoder (antingen när klustret skapas eller genom att skala klustret när du har skapat) måste du även ange en huvudnod storlek med minst 8 kärnor och 14 GB RAM-minne.
>
> Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).

Det kan ta upp till 20 minuter att skapa ett kluster.

## <a name="create-cluster-configuration-object"></a>Skapa kluster: Konfigurationsobjekt

Du kan också skapa ett HDInsight konfiguration objekt med hjälp av `New-AzHDInsightClusterConfig` cmdlet. Sedan kan du ändra det här konfigurationsobjektet för att aktivera ytterligare konfigurationsalternativ för klustret. Använd slutligen den `-Config` -parametern för den `New-AzHDInsightCluster` cmdlet för att använda konfigurationen.

Följande skript skapar ett konfigurationsobjekt för att konfigurera en R Server på typ av HDInsight-kluster. Konfigurationen kan en kantnod, RStudio och ett annat lagringskonto.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Med ett storage-konto i en annan plats än HDInsight-kluster stöds inte. När du använder det här exemplet kan du skapa ytterligare storage-konto på samma plats som servern.

## <a name="customize-clusters"></a>Anpassa kluster

* Se [anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Se [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett HDInsight-kluster, kan du använda följande resurser för att lära dig hur du arbetar med ditt kluster.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-kluster

* [Utveckla Java-topologier för Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark-kluster

* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark med BI: Utföra interaktiv dataanalys med Spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](spark/apache-spark-machine-learning-mllib-ipython.md)

