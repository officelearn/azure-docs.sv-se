---
title: Hantera Apache Hadoop-kluster med PowerShell – Azure HDInsight
description: Lär dig hur du utför administrativa uppgifter för Apache Hadoop-kluster i HDInsight med Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 104975e6424ed96d43434a588997957033c31d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560362"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kan användas för att styra och automatisera distributionen och hanteringen av dina arbetsbelastningar i Azure. I den här artikeln får du lära dig hur du hanterar [Apache Hadoop-kluster](https://hadoop.apache.org/) i Azure HDInsight med hjälp av Azure PowerShell Az-modulen. Listan över HDInsight PowerShell-cmdlets finns i [Az.HDInsight-referensen](https://docs.microsoft.com/powershell/module/az.hdinsight).

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell [Az-modulen](https://docs.microsoft.com/powershell/azure/overview) är installerad.

## <a name="create-clusters"></a>Skapa kluster

Se [Skapa Linux-baserade kluster i HDInsight med Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Lista kluster

Använd följande kommando för att lista alla kluster i den aktuella prenumerationen:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Visa kluster

Använd följande kommando för att visa information om ett visst kluster i den aktuella prenumerationen:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Ta bort kluster

Använd följande kommando för att ta bort ett kluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Du kan också ta bort ett kluster genom att ta bort resursgruppen som innehåller klustret. Om du tar bort en resursgrupp tas alla resurser i gruppen bort, inklusive standardlagringskontot.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Skala kluster

Med klusterskalningsfunktionen kan du ändra antalet arbetsnoder som används av ett kluster som körs i Azure HDInsight utan att behöva återskapa klustret. Om du vill ändra Hadoop-klusterstorleken med Hjälp av Azure PowerShell kör du följande kommando från en klientdator:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Mer information om skalning av kluster finns i [Skala HDInsight-kluster](./hdinsight-scaling-best-practices.md).

## <a name="update-http-user-credentials"></a>Uppdatera HTTP-användaruppgifter

[Set-AzHDInsightGatewayCredential](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) anger HTTP-autentiseringsuppgifter för gatewayen för ett Azure HDInsight-kluster.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Hitta standardlagringskontot

Följande PowerShell-skript visar hur du hämtar standardkontonamnet för lagring och den relaterade informationen:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Hitta resursgruppen

I resource manager-läget tillhör varje HDInsight-kluster en Azure-resursgrupp.  Så här hittar du resursgruppen:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Skicka jobb

**Så här skickar du MapReduce-jobb**

Se [Kör mapreduce-exemplen som ingår i HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Så här skickar du Apache Hive-jobb**

Se [Köra Apache Hive-frågor med PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Så här skickar du Apache Sqoop-jobb**

Se [Använd Apache Sqoop med HDInsight](hadoop/hdinsight-use-sqoop.md).

**Så här skickar du Apache Oozie-jobb**

Se [Använda Apache Oozie med Apache Hadoop för att definiera och köra ett arbetsflöde i HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure Blob-lagring

Mer information finns i [Överföra data till HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Se även

* [Az.HDInsight-cmdlets](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md)
* [Administrera HDInsight med hjälp av ett kommandoradsgränssnitt](hdinsight-administer-use-command-line.md)
* [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Skicka Apache Hadoop jobb programmässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
