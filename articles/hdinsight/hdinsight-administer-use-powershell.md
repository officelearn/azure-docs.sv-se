---
title: Hantera Hadoop-kluster i HDInsight med PowerShell - Azure | Microsoft Docs
description: "Lär dig mer om att utföra administrativa uppgifter för Hadoop-kluster i HDInsight med hjälp av Azure PowerShell."
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: 
ms.assetid: bfdfa754-18e5-4ef9-b0d6-2dbdcebc0283
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: 3522cae228e92b47023cfca217e09c2e2104190b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Hantera Hadoop-kluster i HDInsight med hjälp av Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kan användas för att styra och automatisera distributionen och hanteringen av dina arbetsbelastningar i Azure. I den här artikeln lär du dig hantera Hadoop-kluster i Azure HDInsight med hjälp av Azure PowerShell. Lista över HDInsight PowerShell-cmdlets, se [cmdlet-referens för HDInsight][hdinsight-powershell-reference].

**Förutsättningar**

Innan du börjar den här artikeln, måste du ha följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="install-azure-powershell"></a>Installera Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Om du har installerat Azure PowerShell version 0,9 x, måste du avinstallera den innan du installerar en senare version.

Kontrollera versionen installerad PowerShell:

```powershell
Get-Module *azure*
```

Om du vill avinstallera den äldre versionen, köra program och funktioner på Kontrollpanelen.

## <a name="create-clusters"></a>Skapa kluster
Se [skapa Linux-baserade kluster i HDInsight med hjälp av Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Lista över kluster
Använd följande kommando för att lista alla kluster i den aktuella prenumerationen:

```powershell
Get-AzureRmHDInsightCluster
```

## <a name="show-cluster"></a>Visa kluster
Använd följande kommando om du vill visa information om ett specifikt kluster i den aktuella prenumerationen:

```powershell
Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Ta bort kluster
Använd följande kommando för att ta bort ett kluster:

```powershell
Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

Du kan också ta bort ett kluster genom att ta bort resursgruppen som innehåller klustret. Tar bort en resursgrupp alla resurser i gruppen inklusive standardkontot för lagring.

```powershell
Remove-AzureRmResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Skala kluster
Klustret skalning funktionen kan du ändra antalet arbetarnoder som används av ett kluster som körs i Azure HDInsight utan att behöva återskapa klustret.

> [!NOTE]
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av klustret kan kontrollera du egenskapssidan.  Se [listan och visa](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
>
>

Konsekvenser av att ändra antalet datanoder som för varje typ av kluster som stöds av HDInsight:

* Hadoop

    Sömlöst kan du öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller körs jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras korrekt så att klustret alltid kvar i ett fungerande tillstånd.

    När ett Hadoop-kluster skalas ned genom att minska antalet datanoder som, en del av tjänsterna i klustret har startats om. Att starta om tjänsterna gör alla körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.
* HBase

    Du kan sömlöst Lägg till eller ta bort noder till HBase-kluster, medan den körs. Regional servrar balanseras automatiskt inom några minuter för att slutföra åtgärden. skalning. Du kan dock manuellt balansera regionala servrar genom att logga in headnode i kluster och kör följande kommandon från en kommandotolk:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Storm

    Du kan sömlöst lägga till eller ta bort datanoder till ditt Storm-kluster medan den körs. Men efter en lyckad skalning åtgärden har slutförts, behöver du ombalansera topologin.

    Ombalansering kan utföras på två sätt:

  * Storm webbgränssnittet
  * Verktyget kommandoradsgränssnittet (CLI)

    Referera till den [Apache Storm dokumentationen](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.

    Storm webbgränssnittet är tillgänglig på HDInsight-kluster:

    ![HDInsight storm skala balansera](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

    Här är ett exempel så använder du kommandot CLI för att balansera Storm-topologi:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Om du vill ändra storleken på Hadoop-kluster med hjälp av Azure PowerShell, kör du följande kommando från en klientdator:

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Bevilja/återkalla åtkomst
HDInsight-kluster har följande HTTP-webbtjänster (alla dessa tjänster har RESTful slutpunkter):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Som standard beviljas dessa tjänster för åtkomst. Du kan återkalla/bevilja åtkomst. Återkalla:

```powershell
Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>
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

Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]
> Genom att bevilja/återkalla åtkomst, återställa klustret användarnamn och lösenord.
>
>

Bevilja och återkalla åtkomst kan också ske via portalen. Se [administrera HDInsight med hjälp av Azure portal][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Uppdatera autentiseringsuppgifterna för HTTP
Det är på samma sätt som [HTTP att bevilja/återkalla åtkomst](#grant/revoke-access). Om klustret har beviljats HTTP-åtkomst, måste du först återkalla den.  Och sedan ge åtkomst med nya HTTP-autentiseringsuppgifter.

## <a name="find-the-default-storage-account"></a>Hitta standardkontot för lagring
Följande PowerShell-skript visar hur du hämtar standard lagringskontots namn och relaterad information:

```powershell
#Login-AzureRmAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>Hitta resursgruppen.
Varje HDInsight-klustret tillhör en Azure-resursgrupp i Resource Manager-läget.  Att hitta resursgruppen:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Skicka jobb
**Att skicka MapReduce-jobb**

Se [kör Hadoop-MapReduce-exempel i Windows-baserade HDInsight](hdinsight-run-samples.md).

**Att skicka Hive-jobb**

Se [köra Hive-frågor med hjälp av PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Skicka Pig-jobb**

Se [köra Pig-jobb med hjälp av PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Skicka Sqoop jobb**

Se [använda Sqoop med HDInsight](hdinsight-use-sqoop.md).

**Skicka Oozie jobb**

Se [Oozie för användning med Hadoop för att definiera och köra ett arbetsflöde i HDInsight](hdinsight-use-oozie.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure Blob storage
Mer information finns i [Överföra data till HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Se även
* [Cmdlet-referensdokumentationen för HDInsight][hdinsight-powershell-reference]
* [Administrera HDInsight med hjälp av Azure portal][hdinsight-admin-portal]
* [Administrera HDInsight med ett kommandoradsgränssnitt][hdinsight-admin-cli]
* [Skapa HDInsight-kluster][hdinsight-provision]
* [Överföra data till HDInsight][hdinsight-upload-data]
* [Skicka Hadoop-jobb via programmering][hdinsight-submit-jobs]
* [Kom igång med Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
