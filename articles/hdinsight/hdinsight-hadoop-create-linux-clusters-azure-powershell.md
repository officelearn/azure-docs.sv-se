---
title: "Skapa Hadoop-kluster med hjälp av PowerShell - Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du skapar Hadoop, HBase, Storm eller Spark-kluster på Linux för HDInsight med hjälp av Azure PowerShell."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: adfc1158a907156ffddd27cd4eabc25c81930476
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Skapa Linux-baserade kluster i HDInsight med hjälp av Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell är en kraftfull skriptmiljö som du kan använda för att styra och automatisera distributionen och hanteringen av dina arbetsbelastningar i Microsoft Azure. Det här dokumentet innehåller information om hur du skapar ett Linux-baserade HDInsight-kluster med hjälp av Azure PowerShell. Den innehåller också ett exempelskript.

> [!NOTE]
> Azure PowerShell är bara tillgänglig på Windows-klienter. Om du använder en Linux-, Unix- eller Mac OS X-klient, se [skapa ett Linux-baserat HDInsight-kluster med Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) information om hur du använder Azure CLI för att skapa ett kluster.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande innan du påbörjar den här proceduren:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > Azure PowerShell-stöd för hantering av HDInsight-resurser med hjälp av Azure Service Manager **är föråldrat** och togs bort den 1 januari 2017. I stegen i det här dokumentet används de nya HDInsight-cmdletarna som fungerar med Azure Resource Manager.
    >
    > Följ stegen i [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) att installera den senaste versionen av Azure PowerShell. Om du har skript som behöver ändras för att använda de nya cmdletarna som fungerar med Azure Resource Manager, hittar du mer information i [Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="create-cluster"></a>Skapa kluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Om du vill skapa ett HDInsight-kluster med hjälp av Azure PowerShell, måste du utföra följande procedurer:

* Skapa en Azure-resursgrupp
* Skapa ett Azure Storage-konto
* Skapa en Azure Blob-behållare
* Skapa ett HDInsight-kluster

Följande skript visar hur du skapar ett nytt kluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Värden som du anger för klustret inloggningen används för att skapa användarkontot Hadoop för klustret. Använd det här kontot för att ansluta till tjänster som finns i klustret, till exempel web användargränssnitt eller REST API: er.

De värden som du anger för SSH-användare används för att skapa SSH-användare för klustret. Använd det här kontot för att starta en SSH-fjärrsession på klustret och köra jobb. Mer information finns i dokumentet [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Om du planerar att använda mer än 32 arbetarnoder (antingen när klustret skapas eller genom att skala klustret när den har skapats) måste du också ange en huvudnod storlek med minst 8 kärnor och 14 GB RAM-minne.
>
> Mer information om noden storlekar och relaterade kostnader finns [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).

Det kan ta upp till 20 minuter att skapa ett kluster.

## <a name="create-cluster-configuration-object"></a>Skapa kluster: konfigurationsobjekt

Du kan också skapa ett HDInsight configuration objekt med hjälp av `New-AzureRmHDInsightClusterConfig` cmdlet. Du kan ändra det här konfigurationsobjektet för att aktivera ytterligare konfigurationsalternativ för klustret. Använd slutligen den `-Config` parameter för den `New-AzureRmHDInsightCluster` för att använda konfigurationen.

Följande skript skapar ett konfigurationsobjekt för att konfigurera en R Server på typ av HDInsight-kluster. Konfigurationen kan en kantnod och RStudio ett ytterligare storage-konto.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> Med hjälp av ett lagringskonto i en annan plats än HDInsight-kluster stöds inte. När du använder det här exemplet, kan du skapa ytterligare lagringskontot på samma plats som servern.

## <a name="customize-clusters"></a>Anpassa kluster

* Se [anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Se [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett HDInsight-kluster, kan du använda följande resurser för att lära dig hur du arbetar med ditt kluster.

### <a name="hadoop-clusters"></a>Hadoop-kluster

* [Använda Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase-kluster

* [Kom igång med HBase i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för HBase i HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-kluster

* [Utveckla Java-topologier för Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python komponenter i Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Spark-kluster

* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](spark/apache-spark-livy-rest-interface.md)
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](spark/apache-spark-machine-learning-mllib-ipython.md)

