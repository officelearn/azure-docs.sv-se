---
title: Migrera till Azure Resource Manager-verktyg för HDInsight
description: Hur du migrerar till Azure Resource Managers utvecklingsverktyg för HDInsight-kluster
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 2bef19c1cff93cbb381664d5f1e7d063f3398bf4
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203757"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster

HDInsight avvecklar Azure Service Manager ASM-baserade verktyg för HDInsight. Om du har använt Azure PowerShell, Azure klassiska CLI eller HDInsight .NET SDK för att arbeta med HDInsight-kluster måste uppmuntras du att använda Azure Resource Manager-versioner av PowerShell, CLI och .NET SDK framöver. Den här artikeln innehåller tips om hur du migrerar till den nya Resource Manager-baserade metoden. Tillämpliga fall visar skillnaderna mellan ASM och Resource Manager-metoder för HDInsight i det här dokumentet.

> [!IMPORTANT]  
> Stöd för ASM baserat PowerShell, CLI, och .NET SDK kommer att avbrytas på **1 januari 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrera Azure klassiskt CLI till Azure Resource Manager

> [!IMPORTANT]  
> Azure CLI tillhandhåller inte support för att arbeta med HDInsight-kluster. Du kan fortfarande använda klassiska Azure-CLI med HDInsight, men klassiska Azure-CLI är inaktuellt.

Följande är de grundläggande kommandona för att arbeta med HDInsight via klassiska Azure-CLI:

* `azure hdinsight cluster create` -skapar ett nytt HDInsight-kluster
* `azure hdinsight cluster delete` – tar bort ett befintligt HDInsight-kluster
* `azure hdinsight cluster show` – Visa information om ett befintligt kluster
* `azure hdinsight cluster list` – Visar en lista över HDInsight-kluster för din Azure-prenumeration

Använd den `-h` växel för att granska parametrar och växlar som är tillgängliga för varje kommando.

### <a name="new-commands"></a>Nya kommandon
Nya kommandon som är tillgängliga med Azure Resource Manager är:

* `azure hdinsight cluster resize` -ändras dynamiskt antalet arbetarnoder i klustret
* `azure hdinsight cluster enable-http-access` -Aktivera HTTPs-åtkomst till klustret (på som standard)
* `azure hdinsight cluster disable-http-access` -inaktiverar HTTPs åtkomst till klustret
* `azure hdinsight script-action` -innehåller kommandon för att skapa/hantera skriptåtgärder i ett kluster
* `azure hdinsight config` -innehåller kommandon för att skapa en konfigurationsfil som kan användas med den `hdinsight cluster create` kommando för att ange konfigurationsinformation.

### <a name="deprecated-commands"></a>Inaktuella kommandon
Om du använder den `azure hdinsight job` kommandon för att skicka jobb till ditt HDInsight-kluster dessa kommandon är inte tillgängliga via Resource Manager-kommandon. Om du behöver att programmatiskt skicka jobb till HDInsight från skript kan använda du i stället REST API: er tillhandahålls av HDInsight. Mer information om hur du överför jobb med hjälp av REST API: er finns i följande dokument.

* [Köra MapReduce-jobb med Hadoop på HDInsight med cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Kör Apache Hive-frågor med Apache Hadoop på HDInsight med cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Kör Apache Pig-jobb med Apache Hadoop på HDInsight med cURL](hadoop/apache-hadoop-use-pig-curl.md)

Information om andra sätt att köra Apache Hadoop MapReduce, Apache Hive och Apache Pig interaktivt finns [använda MapReduce med Hadoop i HDInsight](hadoop/hdinsight-use-mapreduce.md), [använda Apache Hive med Apache Hadoop på HDInsight](hadoop/hdinsight-use-hive.md), och [använda Apache Pig med Apache Hadoop på HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exempel
**Skapa ett kluster**

* Gamla kommando (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nytt kommando- `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Tar bort ett kluster**

* Gamla kommando (ASM)- `azure hdinsight cluster delete myhdicluster`
* Nytt kommando- `azure hdinsight cluster delete mycluster -g myresourcegroup`

**En lista över kluster**

* Gamla kommando (ASM)- `azure hdinsight cluster list`
* Nytt kommando- `azure hdinsight cluster list`

> [!NOTE]  
> Att ange en resurs-grupp med för kommandot lista `-g` returnerar endast kluster i den angivna resursgruppen.

**Visa klusterinformation**

* Gamla kommando (ASM)- `azure hdinsight cluster show myhdicluster`
* Nytt kommando- `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrera till Azure Resource Manager för Azure PowerShell
Allmän information om Azure PowerShell i Azure Resource Manager-läget finns på [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

Azure PowerShell Resource Manager-cmdletar kan installeras sida vid sida med ASM-cmdletar. Cmdlet: ar från de två lägena kan särskiljas genom deras namn.  Resource Manager-läget har *AzHDInsight* i cmdlet-namnen jämföra för *AzureHDInsight* i ASM-läge.  Till exempel *New AzHDInsightCluster* vs. *New-AzureHDInsightCluster*. Parametrar och växlar som kan ha nyheter namn och det finns många nya parametrar när du använder Resource Manager.  Till exempel flera cmdletar kräver en ny växel kallas *- ResourceGroupName*. 

Innan du kan använda HDInsight-cmdlet: ar, måste du ansluta till ditt Azure-konto och skapa en ny resursgrupp:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Omdöpt cmdlet: ar
Visa en lista över HDInsight ASM-cmdlets i Windows PowerShell-konsolen:

    help *azurehdinsight*

I följande tabell visas de ASM-cmdletarna och deras namn i Resource Manager-läge:

| ASM-cmdletar | Resource Manager-cmdletar |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nya cmdletar
Följande är de nya cmdletarna som är tillgängliga i Resource Manager-läge. 

**Skriptet åtgärd-relaterade cmdlets:**

* **Get-AzHDInsightPersistedScriptAction**: Hämtar de beständiga skriptåtgärder för ett kluster och visar dem i kronologisk ordning eller hämtar information för en angiven bestående skriptåtgärd. 
* **Get-AzHDInsightScriptActionHistory**: Hämtar historiken för skriptåtgärder för ett kluster och visas i omvänd kronologisk ordning eller hämtar information om en tidigare utförda skriptåtgärd. 
* **Remove-AzHDInsightPersistedScriptAction**: Tar bort en bestående skriptåtgärd från ett HDInsight-kluster.
* **Set-AzHDInsightPersistedScriptAction**: Anger en tidigare utförda skriptåtgärd ska vara en bestående skriptåtgärd.
* **Submit-AzHDInsightScriptAction**: Skickar en ny skriptåtgärd till ett Azure HDInsight-kluster. 

Ytterligare användningsinformation finns i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

**Klustrets identitet-relaterade cmdlets:**

* **Add-AzHDInsightClusterIdentity**: Lägger till en kluster-identitet till ett konfigurationsobjekt för klustret så att HDInsight-klustret kan komma åt Azure Data Lake Storage. Se [skapa ett HDInsight-kluster med Data Lake Storage med hjälp av Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exempel
**Skapa kluster**

Gamla kommando (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Nytt kommando:

    New-AzHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Ta bort kluster**

Gamla kommando (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nytt kommando:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Lista över kluster**

Gamla kommando (ASM):

    Get-AzureHDInsightCluster

Nytt kommando:

    Get-AzHDInsightCluster 

**Visa kluster**

Gamla kommando (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nytt kommando:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Andra exempel
* [Skapa HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Skicka Apache Hive-jobb](hadoop/apache-hadoop-use-hive-powershell.md)
* [Skicka Apache Pig-jobb](hadoop/apache-hadoop-use-pig-powershell.md)
* [Skicka Apache Sqoop jobb](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrera till ny HDInsight .NET SDK
Azure Service Management-baserade [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) är nu föråldrad. Du uppmuntras att använda Azure Resource Manager-baserade [Resource Manager-baserade HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). Följande paket i ASM-baserade HDInsight blir inaktuella.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Det här avsnittet innehåller länkar till mer information om hur du utför uppgifter med hjälp av Resource Manager-baserade SDK.

| Så här... med hjälp av Resource Manager-baserade HDInsight SDK | Länkar |
| --- | --- |
| Skapa HDInsight-kluster med hjälp av .NET SDK |Se [skapa HDInsight-kluster med hjälp av .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Anpassa ett kluster med skriptåtgärd med .NET SDK |Se [anpassa HDInsight Linux-kluster med skriptåtgärd](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Autentisera program interaktivt med hjälp av Azure Active Directory med .NET SDK |Se [kör Apache Hive-frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Kodfragmentet i den här artikeln använder metod för interaktiv autentisering. |
| Autentisera program icke-interaktivt med hjälp av Azure Active Directory med .NET SDK |Se [skapa icke-interaktiva program för HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Skicka ett Apache Hive-jobb med .NET SDK |Se [skicka Apache Hive-jobb](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Skicka ett Apache Pig-jobb med hjälp av .NET SDK |Se [skicka Apache Pig-jobb](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Skicka ett Apache Sqoop-jobb med hjälp av .NET SDK |Se [skicka Apache Sqoop jobb](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Lista över HDInsight-kluster med .NET SDK |Se [lista HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Skala HDInsight-kluster med .NET SDK |Se [skala HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Bevilja/återkalla åtkomst till HDInsight-kluster med .NET SDK |Se [bevilja/återkalla åtkomst till HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Uppdatera HTTP-autentiseringsuppgifterna för HDInsight-kluster med hjälp av .NET SDK |Se [Update HTTP-autentiseringsuppgifterna för HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Hitta standardkontot för lagring för HDInsight-kluster med .NET SDK |Se [hitta standardkontot för lagring för HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Ta bort HDInsight-kluster med hjälp av .NET SDK |Se [ta bort HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exempel
Här följer några exempel på hur en åtgärd är med i ASM-baserad SDK och motsvarande kodfragmentet för Resource Manager-baserade SDK.

**Skapa en kluster CRUD-klient**

* Gamla kommando (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nytt kommando (Service principal auktorisering)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nytt kommando (användarautentisering)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Skapa ett kluster**

* Gamla kommando (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Nytt kommando
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Aktivera HTTP-åtkomst**

* Gamla kommando (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nytt kommando
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Tar bort ett kluster**

* Gamla kommando (ASM)
  
        client.DeleteCluster(dnsName);
* Nytt kommando
  
        client.Clusters.Delete(resourceGroup, dnsname);

