---
title: Anpassa HDInsight-kluster med bootstrap - Azure
description: Lär dig mer om att anpassa HDInsight-kluster med bootstrap.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: fe653d36b2c527391a2f6d4ce33b89ba8dd648ac
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202800"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Anpassa HDInsight-kluster med Bootstrap

Ibland kan vill du konfigurera konfigurationsfiler, bland annat:

* clusterIdentity.xml
* core-site.xml
* gateway.XML
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (konfiguration av kafkas asynkrona meddelandekö)

Det finns tre olika metoder för att använda bootstrap:

* Använda Azure PowerShell
* Använd .NET SDK
* Använd Azure Resource Manager-mallar

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Information om hur du installerar ytterligare komponenter på HDInsight-kluster under tiden för skapandet finns i:

* [Anpassa HDInsight-kluster med skriptåtgärd (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Följande PowerShell-kod anpassar en [Apache Hive](https://hive.apache.org/) konfiguration:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

New-AzureRmHDInsightCluster `
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

En fullständig fungerande PowerShell-skript finns i [bilaga](#appendix-powershell-sample).

**För att verifiera ändringen:**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn klickar du på **HDInsight-kluster**. Om du inte ser det klickar du på **alla tjänster** första.
3. Klicka på klustret som du skapade med hjälp av PowerShell-skriptet.
4. Klicka på **instrumentpanelen** högst upp på bladet för att öppna Ambari UI.
5. Klicka på **Hive** menyn till vänster.
6. Klicka på **HiveServer2** från **sammanfattning**.
7. Klicka på den **Peeringkonfigurationer** fliken.
8. Klicka på **Hive** menyn till vänster.
9. Klicka på fliken **Avancerat**.
10. Rulla nedåt och expandera sedan **avancerade hive-plats**.
11. Leta efter **hive.metastore.client.socket.timeout** i avsnittet.

Några fler exempel för att anpassa andra konfigurationsfiler:

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
Mer information finns i Azim Uddin blogg benämnt [anpassa HDInsight-klustret har skapats](https://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>Använd .NET SDK
Se [skapa Linux-baserade kluster i HDInsight med .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Använd Resource Manager-mall
Du kan använda bootstrap i Resource Manager-mall:

```json
"configurations": {
    �
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop anpassar kluster bootstrap Azure Resource Manager-mall](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Se också
* [Skapa Apache Hadoop-kluster i HDInsight] [ hdinsight-provision-cluster] innehåller instruktioner om hur du skapar ett HDInsight-kluster med hjälp av andra anpassade alternativ.
* [Utveckla skriptåtgärder skript för HDInsight][hdinsight-write-script]
* [Installera och använda Apache Spark på HDInsight-kluster][hdinsight-install-spark]
* [Installera och använda Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Steg när klustret skapas"

## <a name="appendix-powershell-sample"></a>Tillägg: PowerShell-exempel
Det här PowerShell-skriptet skapar ett HDInsight-kluster och anpassar en Hive-inställning:

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
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

$location = "East US 2"
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzureRmResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_GRS

$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageContext = New-AzureStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzureRmHDInsightCluster `
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
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

#endregion
```
