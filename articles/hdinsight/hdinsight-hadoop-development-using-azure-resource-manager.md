---
title: Migrera till Azure Resource Manager-verktyg för HDInsight
description: Migrera till utvecklingsverktyg för Azure Resource Manager för HDInsight-kluster
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 76eb3a135f7a32a30cfa62546a644bc77cf39998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934592"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster

HDInsight är inaktuella Azure Service Manager (ASM)-baserade verktyg för HDInsight. Om du har använt Azure PowerShell, Azure Classic CLI eller HDInsight .NET SDK för att arbeta med HDInsight-kluster, uppmanas du att använda Azure Resource Manager-versionerna av PowerShell, CLI och .NET SDK framöver. Den här artikeln innehåller tips om hur du migrerar till den nya resurshanteraren-baserade metoden. I det här dokumentet belyser det här dokumentet skillnaderna mellan ASM- och Resource Manager-metoderna för HDInsight.

> [!IMPORTANT]  
> Stödet för ASM-baserade PowerShell, CLI och .NET SDK upphör den **1 januari 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrera Azure Classic CLI till Azure Resource Manager

> [!IMPORTANT]  
> Azure CLI ger inte stöd för att arbeta med HDInsight-kluster. Du kan fortfarande använda Azure Classic CLI med HDInsight, men Azure Classic CLI är inaktuellt.

Följande är de grundläggande kommandona för att arbeta med HDInsight via Azure CLassic CLI:

* `azure hdinsight cluster create`- skapar ett nytt HDInsight-kluster
* `azure hdinsight cluster delete`- tar bort ett befintligt HDInsight-kluster
* `azure hdinsight cluster show`- visa information om ett befintligt kluster
* `azure hdinsight cluster list`- listar HDInsight-kluster för din Azure-prenumeration

Använd `-h` växeln för att granska parametrar och växlar tillgängliga för varje kommando.

### <a name="new-commands"></a>Nya kommandon
Nya kommandon som är tillgängliga med Azure Resource Manager är:

* `azure hdinsight cluster resize`- dynamiskt ändrar antalet arbetsnoder i klustret
* `azure hdinsight cluster enable-http-access`- ger HTTPs åtkomst till klustret (på som standard)
* `azure hdinsight cluster disable-http-access`- inaktiverar HTTPs-åtkomst till klustret
* `azure hdinsight script-action`- ger kommandon för att skapa/hantera skriptåtgärder i ett kluster
* `azure hdinsight config`- ger kommandon för att skapa en konfigurationsfil som kan användas med `hdinsight cluster create` kommandot för att tillhandahålla konfigurationsinformation.

### <a name="deprecated-commands"></a>Inaktuella kommandon
Om du `azure hdinsight job` använder kommandona för att skicka jobb till HDInsight-klustret är dessa kommandon inte tillgängliga via Resource Manager-kommandona. Om du behöver programmatiskt skicka jobb till HDInsight från skript, bör du istället använda REST API: er som tillhandahålls av HDInsight. Mer information om hur du skickar jobb med REST-API:er finns i följande dokument.

* [Kör MapReduce-jobb med Hadoop på HDInsight med cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Kör Apache Hive-frågor med Apache Hadoop på HDInsight med cURL](hadoop/apache-hadoop-use-hive-curl.md)


Information om andra sätt att köra Apache Hadoop MapReduce, Apache Hive och Apache Pig interaktivt finns i [Använd MapReduce med Hadoop på HDInsight](hadoop/hdinsight-use-mapreduce.md), [Använd Apache Hive med Apache Hadoop på HDInsight](hadoop/hdinsight-use-hive.md)och Använd Apache Pig med Apache [Hadoop på HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exempel
**Skapa ett kluster**

* Gammalt kommando (ASM) -`azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nytt kommando -`azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Ta bort ett kluster**

* Gammalt kommando (ASM) -`azure hdinsight cluster delete myhdicluster`
* Nytt kommando -`azure hdinsight cluster delete mycluster -g myresourcegroup`

**Lista kluster**

* Gammalt kommando (ASM) -`azure hdinsight cluster list`
* Nytt kommando -`azure hdinsight cluster list`

> [!NOTE]  
> För listkommandot returnerar du `-g` bara kluster i den angivna resursgruppen om du anger resursgruppen som använder.

**Visa klusterinformation**

* Gammalt kommando (ASM) -`azure hdinsight cluster show myhdicluster`
* Nytt kommando -`azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrera Azure PowerShell till Azure Resource Manager
Den allmänna informationen om Azure PowerShell i Azure Resource Manager-läget finns på [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

Cmdlets för Azure PowerShell Resource Manager kan installeras sida vid sida med ASM-cmdlets. Cmdletsen från de två lägena kan särskiljas av deras namnger.  Resurshanteraren-läget har *AzHDInsight* i cmdletnamnen som jämförs med *AzureHDInsight* i ASM-läget.  Till exempel *New-AzHDInsightCluster* vs. *Ny-AzureHDInsightCluster*. Parametrar och växlar kan ha nyhetsnamn och det finns många nya parametrar tillgängliga när du använder Resource Manager.  Flera cmdlets kräver till exempel en ny växel som kallas *-ResourceGroupName*. 

Innan du kan använda HDInsight-cmdlets måste du ansluta till ditt Azure-konto och skapa en ny resursgrupp:

* [Anslut-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Omdöpta cmdlets
Så här listar du HDInsight ASM-cmdlets i Windows PowerShell-konsolen:

    help *azurehdinsight*

I följande tabell visas ASM-cmdlets och deras namn i resurshanteraren-läge:

| ASM-cmdlets | Cmdlets för Resurshanteraren |
| --- | --- |
| Tillägg-AzureHDInsightConfigValue |[Tillägg AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Tillägg AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Tillägg AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Tillägg AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Få-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Få-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Hämta AzureHDInsightProperty |[Få-AzHDInsightProperty](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Åberopa-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[Ny-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[Nya-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[Ny-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[Ny-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[Ny-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[Ny-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[Ny-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Ta bort-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stopp-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Användning-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Vänta-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nya cmdletar
Följande är de nya cmdlets som bara är tillgängliga i Resource Manager-läge. 

**Skriptåtgärdsrelaterade cmdlets:**

* **Get-AzHDInsightPersistedScriptAction**: Hämtar de beständiga skriptåtgärderna för ett kluster och listar dem i kronologisk ordning, eller hämtar information om en angiven beständig skriptåtgärd. 
* **Get-AzHDInsightScriptActionHistory**: Hämtar skriptåtgärdshistoriken för ett kluster och listar den i omvänd kronologisk ordning, eller får information om en tidigare utförd skriptåtgärd. 
* **Remove-AzHDInsightPersistedScriptAction**: Tar bort en beständig skriptåtgärd från ett HDInsight-kluster.
* **Set-AzHDInsightPersistedScriptAction**: Anger att en tidigare utförd skriptåtgärd ska vara en beständig skriptåtgärd.
* **Skicka-AzHDInsightScriptAction**: Skickar en ny skriptåtgärd till ett Azure HDInsight-kluster. 

Mer information om användning finns i [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

**Klusteridentitetsrelaterade cmdlets:**

* **Add-AzHDInsightClusterIdentity**: Lägger till en klusteridentitet i ett klusterkonfigurationsobjekt så att HDInsight-klustret kan komma åt Azure Data Lake Storage. Se [Skapa ett HDInsight-kluster med DataSjölagring med Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exempel
**Skapa kluster**

Gammalt kommando (ASM): 

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


**Ta bort klustret**

Gammalt kommando (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nytt kommando:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Lista klustret**

Gammalt kommando (ASM):

    Get-AzureHDInsightCluster

Nytt kommando:

    Get-AzHDInsightCluster 

**Visa kluster**

Gammalt kommando (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nytt kommando:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Andra prover
* [Skapa HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Skicka Apache Hive jobb](hadoop/apache-hadoop-use-hive-powershell.md)
* [Skicka Apache Sqoop jobb](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrera till den nya HDInsight .NET SDK
Azure Service Management-baserade [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) är nu inaktuell. Du uppmanas att använda Azure Resource [Management-baserade Resource Manager-baserade HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). Följande ASM-baserade HDInsight-paket är inaktuella.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Det här avsnittet innehåller tips för mer information om hur du utför vissa uppgifter med hjälp av Resource Manager-baserade SDK.

| Hur... använda Resource Manager-baserade HDInsight SDK | Länkar |
| --- | --- |
| Azure HDInsight SDK för .NET|Se [Azure HDInsight SDK för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) |
| Autentisera program interaktivt med Azure Active Directory med .NET SDK |Se [Köra Apache Hive-frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Kodavsnittet i den här artikeln använder den interaktiva autentiseringsmetoden. |
| Autentisera program som inte interaktivt använder Azure Active Directory med .NET SDK |Se [Skapa icke-interaktiva program för HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Skicka ett Apache Hive-jobb med .NET SDK |Se [Skicka Apache Hive jobb](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Skicka ett Apache Sqoop-jobb med .NET SDK |Se [Skicka Apache Sqoop jobb](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Lista HDInsight-kluster med .NET SDK |Se [Lista HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Skala HDInsight-kluster med .NET SDK |Se [Skala HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Bevilja/återkalla åtkomst till HDInsight-kluster med .NET SDK |Se [Bevilja/återkalla åtkomst till HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Uppdatera HTTP-användarautentiseringsuppgifter för HDInsight-kluster med .NET SDK |Se [Uppdatera HTTP-användarautentiseringsuppgifter för HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Hitta standardlagringskontot för HDInsight-kluster med .NET SDK |Se [Hitta standardlagringskontot för HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Ta bort HDInsight-kluster med .NET SDK |Se [Ta bort HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exempel
Här följer några exempel på hur en åtgärd utförs med hjälp av ASM-baserade SDK och motsvarande kodavsnitt för Resource Manager-baserade SDK.

**Skapa en CRUD-klusterklient**

* Gammalt kommando (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nytt kommando (tillståndsbehörighet för tjänsten)
  
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
* Nytt kommando (användarauktorisering)
  
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

* Gammalt kommando (ASM)
  
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

* Gammalt kommando (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nytt kommando
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Ta bort ett kluster**

* Gammalt kommando (ASM)
  
        client.DeleteCluster(dnsName);
* Nytt kommando
  
        client.Clusters.Delete(resourceGroup, dnsname);

