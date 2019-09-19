---
title: Anpassa Azure HDInsight-klusterkonfigurationer med start
description: Lär dig hur du anpassar konfiguration av HDInsight-kluster program mässigt med hjälp av .net, PowerShell och Resource Manager-mallar.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 15d08b14e38f097e8e9c3e0db893efb1d6efe44d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098676"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Anpassa HDInsight-kluster med hjälp av bootstrap

Med Bootstrap-skript kan du installera och konfigurera komponenter i Azure HDInsight program mässigt.

Det finns tre metoder för att ange inställningar för konfigurations fil när HDInsight-klustret skapas:

* Använda Azure PowerShell
* Använd .NET SDK
* Använd Azure Resource Manager-mallar

Med dessa programmerings metoder kan du till exempel konfigurera alternativ i följande filer:

* clusterIdentity.xml
* Site. XML
* Gateway. XML
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – plats
* oozie-site.xml
* oozie-env.xml
* Storm-site. XML
* tez-site.xml
* webhcat-site.xml
* yarn-site. XML
* Server. Properties (Kafka-Broker-konfiguration)

Information om hur du installerar ytterligare komponenter i HDInsight-kluster under skapande tiden finns i [Anpassa HDInsight-kluster med skript åtgärd (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Förutsättningar

* Om du använder PowerShell behöver du AZ- [modulen](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Följande PowerShell-kod anpassar en [Apache Hive](https://hive.apache.org/) -konfiguration:

> [!IMPORTANT]  
> Parametern `Spark2Defaults` kan behöva användas med [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Du kan skicka tomma värden till parametern som visas i kod exemplet nedan.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

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

Du hittar ett fullständigt fungerande PowerShell-skript i [bilagan](#appendix-powershell-sample).

**Så här kontrollerar du ändringen:**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **HDInsight-kluster**i den vänstra menyn. Om du inte ser det klickar du på **alla tjänster** först.
3. Klicka på det kluster som du nyss skapade med PowerShell-skriptet.
4. Klicka på **instrument panel** överst på bladet för att öppna AMBARI-användargränssnittet.
5. Klicka på **Hive** på den vänstra menyn.
6. Klicka på **HiveServer2** från **Sammanfattning**.
7. Klicka på fliken **konfigurationer** .
8. Klicka på **Hive** på den vänstra menyn.
9. Klicka på fliken **Avancerat**.
10. Rulla ned och expandera sedan **Avancerad Hive-plats**.
11. Leta efter **Hive. metaarkiv. client. socket. timeout** i avsnittet.

Några fler exempel på hur du anpassar andra konfigurationsfiler:

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

## <a name="use-net-sdk"></a>Använd .NET SDK
Se [skapa Linux-baserade kluster i HDInsight med hjälp av .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

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

![Hadoop anpassar start Azure Resource Manager mall för kluster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Se också

* [Skapa Apache Hadoop kluster i HDInsight][hdinsight-provision-cluster] innehåller instruktioner om hur du skapar ett HDInsight-kluster med hjälp av andra anpassade alternativ.
* [Utveckla skript åtgärds skript för HDInsight][hdinsight-write-script]
* [Installera och använda Apache Spark på HDInsight-kluster][hdinsight-install-spark]
* [Installera och Använd Apache Giraph i HDInsight-kluster](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Steg när klustret skapas"

## <a name="appendix-powershell-sample"></a>Fjärde PowerShell-exempel

Det här PowerShell-skriptet skapar ett HDInsight-kluster och anpassar en Hive-inställning. Se till att ange värden för `$nameToken`, `$httpPassword`och `$sshPassword`.

> [!WARNING]  
> Det går inte `BlobStorage` att använda lagrings konto typen för HDInsight-kluster.

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
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

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
