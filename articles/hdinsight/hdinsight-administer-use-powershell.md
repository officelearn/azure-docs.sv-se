---
title: Hantera Apache Hadoop-kluster i HDInsight med PowerShell - Azure
description: Lär dig mer om att utföra administrativa uppgifter för Apache Hadoop-kluster i HDInsight med Azure PowerShell.
services: hdinsight
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tylerfox
ms.openlocfilehash: 6cf05437d5fc181a9fadae110a44efd88d06a2da
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011640"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kan användas för att styra och automatisera distributionen och hanteringen av dina arbetsbelastningar i Azure. I den här artikeln får du lära dig hur du hanterar [Apache Hadoop](https://hadoop.apache.org/) kluster i Azure HDInsight med hjälp av Azure PowerShell Az-modulen. Lista över HDInsight PowerShell-cmdlets finns i den [Az.HDInsight referens](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* PowerShell [Az modulen](https://docs.microsoft.com/powershell/azure/overview) installerad.

## <a name="create-clusters"></a>Skapa kluster
Se [skapa Linux-baserade kluster i HDInsight med Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>En lista över kluster
Använd följande kommando för att lista alla kluster i den aktuella prenumerationen:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Visa kluster
Använd följande kommando visar information om ett specifikt kluster i den aktuella prenumerationen:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Ta bort kluster
Använd följande kommando för att ta bort ett kluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Du kan också ta bort ett kluster genom att ta bort resursgruppen som innehåller klustret. Ta bort en resursgrupp tar du bort alla resurser i gruppen, inklusive standardkontot för lagring.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Skala kluster
Klusterskalning funktionen kan du ändra antalet arbetarnoder som används av ett kluster som körs i Azure HDInsight utan att behöva återskapa klustret.

Effekten av att ändra antalet datanoder som för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop

    Du kan smidigt öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller pågående jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras ett smidigt sätt så att klustret finns alltid kvar i funktionsdugligt tillstånd.

    När ett Hadoop-kluster skalas genom att minska antalet datanoder några tjänster i klustret startas om. Startar om tjänster gör allt körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.
* Apache HBase

    Du kan smidigt lägga till eller ta bort noder till HBase-kluster medan den körs. Regionservrar balanseras automatiskt inom ett par minuter efter att du skalar igen. Du kan dock också manuellt balansera regionservrar genom att logga in till huvudnod i kluster och kör sedan följande kommandon från en kommandotolk:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Du kan smidigt lägga till eller ta bort datanoder till ditt Storm-kluster medan den körs. Men när en installationen har slutförts åtgärdens skalning, behöver du balansera om topologin.

    Ombalansering kan utföras på två sätt:

  * Storm-Webbgränssnittet
  * Verktyget kommandoradsgränssnittet (CLI)

    Referera till den [Apache Storm-dokumentationen](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.

    Storm-webbgränssnittet finns på HDInsight-klustret:

    ![HDInsight storm skala ombalansering](./media/hdinsight-administer-use-powershell/hdinsight.portal.scale.cluster.png)

    Här är ett exempel hur du använder CLI-kommando för att balansera om Storm-topologi:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Om du vill ändra storlek för Hadoop-kluster med hjälp av Azure PowerShell kör du följande kommando från en klientdator:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Bevilja/återkalla åtkomst
HDInsight-kluster har följande HTTP-webbtjänster (alla dessa tjänster har RESTful-slutpunkter):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Som standard beviljas de här tjänsterna för åtkomst. Du kan återkalla/bevilja åtkomst. Återkalla:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Så här ger:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Bevilja/återkalla åtkomst, återställs klusteranvändarnamn och lösenord.

Bevilja och återkalla åtkomst kan även göras via portalen. Se [hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Uppdatera HTTP-autentiseringsuppgifterna
Det är på samma sätt som Grant/revoke HTTP-åtkomst. Om klustret har beviljats HTTP-åtkomst, måste du först återkalla den.  Och sedan bevilja åtkomst med nya HTTP-autentiseringsuppgifterna.

## <a name="find-the-default-storage-account"></a>Hitta standardkontot för lagring
Följande PowerShell-skript visar hur du hämtar standard lagringskontonamn och relaterad information:

> [!IMPORTANT]  
> Värdena för `DefaultStorageAccount`, och `DefaultStorageContainer` returneras inte från [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) när [säker överföring](../storage/common/storage-require-secure-transfer.md) är aktiverat för lagringskontot.


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


## <a name="find-the-resource-group"></a>Hitta resursgruppen.
Varje HDInsight-klustret tillhör en Azure-resursgrupp i Resource Manager-läge.  Hitta resursgruppen:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Skicka jobb
**Att skicka MapReduce-jobb**

Se [kör MapReduce-exemplen som ingår i HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Att skicka Apache Hive-jobb**

Se [kör Apache Hive-frågor med hjälp av PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Skicka Apache Pig-jobb**

Se [kör Apache Pig-jobb med hjälp av PowerShell](hadoop/apache-hadoop-use-pig-powershell.md).

**Skicka Apache Sqoop jobb**

Se [Använd Apache Sqoop med HDInsight](hadoop/hdinsight-use-sqoop.md).

**Skicka Apache Oozie-jobb**

Se [Använd Apache Oozie med Apache Hadoop för att definiera och köra ett arbetsflöde i HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure Blob storage

Se [ladda upp data till HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Se även

* [Referensdokumentation för HDInsight-cmdlet](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md)
* [Administrera HDInsight med ett kommandoradsgränssnitt](hdinsight-administer-use-command-line.md)
* [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Skicka Apache Hadoop-jobb programmässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
