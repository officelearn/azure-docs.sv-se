---
title: Anpassa Azure HDInsight-klusterkonfigurationer med bootstrap
description: Lär dig hur du anpassar HDInsight-klusterkonfigurationen programmässigt med hjälp av .Net-, PowerShell- och Resource Manager-mallar.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: e641340ac04415ee4a20cda2bc09bbdbef9802a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272531"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Anpassa HDInsight-kluster med Bootstrap

Bootstrap-skript kan du installera och konfigurera komponenter i Azure HDInsight programmässigt.

Det finns tre metoder för att ange konfigurationsfilinställningar när ditt HDInsight-kluster skapas:

* Använda Azure PowerShell
* Använda .NET SDK
* Använda Azure Resource Manager-mall

Med hjälp av dessa programmatiska metoder kan du till exempel konfigurera alternativ i följande filer:

* klusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-plats
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* garn-site.xml
* server.properties (kafka-mäklare konfiguration)

Information om hur du installerar ytterligare komponenter i HDInsight-klustret under skapandetiden finns i [Anpassa HDInsight-kluster med Hjälp av Skriptåtgärd (Linux).](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="prerequisites"></a>Krav

* Om du använder PowerShell behöver du [Az-modulen](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Följande PowerShell-kod anpassar en [Apache Hive-konfiguration:](https://hive.apache.org/)

> [!IMPORTANT]  
> Parametern `Spark2Defaults` kan behöva användas med [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Du kan skicka tomma värden till parametern enligt kodexemplet nedan.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Ett fullständigt fungerande PowerShell-skript finns i [tillägg](#appendix-powershell-sample).

**Så här verifierar du ändringen:**

1. Navigera `https://CLUSTERNAME.azurehdinsight.net/` till `CLUSTERNAME` var är namnet på klustret.
1. Från den vänstra menyn navigerar du till **Hive** > **Configs** > **Advanced**.
1. Expandera **Avancerad hive-site**.
1. Leta upp **hive.metastore.client.socket.timeout** och bekräfta att värdet är **90-talet**.

Några fler exempel på att anpassa andra konfigurationsfiler:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Använda .NET SDK

Se [Azure HDInsight SDK för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).

## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar

Du kan använda bootstrap i Resource Manager-mallen:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop anpassar kluster bootstrap Azure Resource Manager-mall](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Se även

* [Skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md) innehåller instruktioner om hur du skapar ett HDInsight-kluster med andra anpassade alternativ.
* [Utveckla skript för skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installera och använda Apache Spark på HDInsight-kluster](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Installera och använd Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install.md).

## <a name="appendix-powershell-sample"></a>Tillägg: PowerShell-exempel

Det här PowerShell-skriptet skapar ett HDInsight-kluster och anpassar en Hive-inställning. Var noga med `$nameToken`att `$httpPassword`ange `$sshPassword`värden för , och .

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
