---
title: Skapa Apache Hadoop-kluster med PowerShell - Azure HDInsight
description: Lär dig hur du skapar Apache Hadoop-, Apache HBase-, Apache Storm- eller Apache Spark-kluster på Linux för HDInsight med hjälp av Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 3c93eca493275612ac14a995140b2e91cc40fe98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644672"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Skapa Linux-baserade kluster i HDInsight med Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell är en kraftfull skriptmiljö som du kan använda för att styra och automatisera distributionen och hanteringen av dina arbetsbelastningar i Microsoft Azure. Det här dokumentet innehåller information om hur du skapar ett Linux-baserat HDInsight-kluster med hjälp av Azure PowerShell. Den innehåller också ett exempel skript.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps) Az modul.

## <a name="create-cluster"></a>Skapa kluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Om du vill skapa ett HDInsight-kluster med Hjälp av Azure PowerShell måste du utföra följande procedurer:

* Skapa en Azure-resursgrupp
* Skapa ett Azure Storage-konto
* Skapa en Azure Blob-behållare
* Skapa ett HDInsight-kluster

> [!NOTE]
> Att använda PowerShell för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen2 stöds för närvarande inte.

Följande skript visar hur du skapar ett nytt kluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

De värden som du anger för klusterinloggningen används för att skapa Hadoop-användarkontot för klustret. Använd det här kontot om du vill ansluta till tjänster som finns i klustret, till exempel webb-API:er eller REST-API:er.

De värden som du anger för SSH-användaren används för att skapa SSH-användaren för klustret. Använd det här kontot för att starta en fjärr-SSH-session i klustret och köra jobb. Mer information finns i dokumentet [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Om du planerar att använda mer än 32 arbetsnoder (antingen vid klusterskapande eller genom att skala klustret när du har skapat), måste du också ange en huvudnodstorlek med minst 8 kärnor och 14 GB RAM.
>
> Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).

Det kan ta upp till 20 minuter att skapa ett kluster.

## <a name="create-cluster-configuration-object"></a>Skapa kluster: Konfigurationsobjekt

Du kan också skapa ett HDInsight-konfigurationsobjekt med cmdlet. [`New-AzHDInsightClusterConfig`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) Du kan sedan ändra det här konfigurationsobjektet för att aktivera ytterligare konfigurationsalternativ för klustret. Slutligen använder `-Config` du parametern för [`New-AzHDInsightCluster`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet för att använda konfigurationen.

Följande skript skapar ett konfigurationsobjekt för att konfigurera en R-server på HDInsight-klustertyp. Konfigurationen möjliggör en kantnod, RStudio och ett ytterligare lagringskonto.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Det går inte att använda ett lagringskonto på en annan plats än HDInsight-klustret. När du använder det här exemplet skapar du ytterligare lagringskontot på samma plats som servern.

## <a name="customize-clusters"></a>Anpassa kluster

* Se [Anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Se [Anpassa HDInsight-kluster med hjälp av skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett HDInsight-kluster använder du följande resurser för att lära dig hur du arbetar med klustret.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använd MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Komma igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Stormkluster

* [Utveckla Java-topologier för Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark kluster

* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](spark/apache-spark-machine-learning-mllib-ipython.md)