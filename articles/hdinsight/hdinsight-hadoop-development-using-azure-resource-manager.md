---
title: Migrera till Azure Resource Manager-verktyg för HDInsight | Microsoft Docs
description: Hur du migrerar till Azure Resource Manager utvecklingsverktyg för HDInsight-kluster
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: ''
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: d0182afbd1a6beaabadf68f08905316be4ba034f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster

HDInsight sluta Azure Service Manager ASM-baserat verktyg för HDInsight. Om du har använt Azure PowerShell, Azure CLI eller HDInsight .NET SDK för att arbeta med HDInsight-kluster måste uppmuntras du att använda Azure Resource Manager-versioner av PowerShell, CLI och .NET SDK framöver. Den här artikeln innehåller tips på hur du migrerar till den nya Resource Manager-baserade metoden. Tillämpliga fall visar skillnaderna mellan ASM och Resource Manager-metoder för HDInsight i det här dokumentet.

> [!IMPORTANT]
> Stöd för ASM baserat PowerShell, CLI och .NET SDK ska upphöra på **1 januari 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Migrera till Azure Resource Manager för Azure CLI

> [!IMPORTANT]
> Azure CLI 2.0 saknar stöd för att arbeta med HDInsight-kluster. Du kan fortfarande använda Azure CLI 1.0 med HDInsight, men Azure CLI 1.0 är föråldrad.

Följande är de grundläggande kommandona för att arbeta med HDInsight via Azure CLI 1.0:

* `azure hdinsight cluster create` -skapar ett nytt HDInsight-kluster
* `azure hdinsight cluster delete` -tar bort ett befintligt HDInsight-kluster
* `azure hdinsight cluster show` – Visa information om ett befintligt kluster
* `azure hdinsight cluster list` – Visar en lista över HDInsight-kluster för din Azure-prenumeration

Använd den `-h` växel för att granska parametrar och växlar som är tillgängliga för varje kommando.

### <a name="new-commands"></a>Nya kommandon
Nya kommandon som är tillgängliga med Azure Resource Manager är:

* `azure hdinsight cluster resize` -ändras dynamiskt antalet arbetarnoder i klustret
* `azure hdinsight cluster enable-http-access` -Aktivera HTTPs åtkomst till klustret (om som standard)
* `azure hdinsight cluster disable-http-access` -inaktiverar HTTPs åtkomst till klustret
* `azure hdinsight script-action` -innehåller kommandon för att skapa/hantera skriptåtgärder på ett kluster
* `azure hdinsight config` -innehåller kommandon för att skapa en konfigurationsfil som kan användas med den `hdinsight cluster create` kommando för att ange konfigurationsinformation.

### <a name="deprecated-commands"></a>Föråldrade kommandon
Om du använder den `azure hdinsight job` kommandon för att skicka jobb till ditt HDInsight-kluster kommandona inte är tillgängliga via Resource Manager-kommandon. Om du behöver programmässigt skicka jobb till HDInsight från skript, bör du använda REST API: er som tillhandahålls av HDInsight i stället. Mer information om att skicka jobb med hjälp av REST API: er finns i följande dokument.

* [Kör jobb för MapReduce med Hadoop i HDInsight med cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Köra Hive-frågor med Hadoop i HDInsight med cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Köra Pig med Hadoop på HDInsight med cURL](hadoop/apache-hadoop-use-pig-curl.md)

Information om andra sätt att köra MapReduce Hive, och svin interaktivt, se [använda MapReduce med Hadoop i HDInsight](hadoop/hdinsight-use-mapreduce.md), [använda Hive med Hadoop i HDInsight](hadoop/hdinsight-use-hive.md), och [använda Pig med Hadoop i HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exempel
**Skapar ett kluster**

* Gamla kommando (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nytt kommando- `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Tar bort ett kluster**

* Gamla kommando (ASM)- `azure hdinsight cluster delete myhdicluster`
* Nytt kommando- `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Lista över kluster**

* Gamla kommando (ASM)- `azure hdinsight cluster list`
* Nytt kommando- `azure hdinsight cluster list`

> [!NOTE]
> För kommandot listan anger du en resurs grupp med `-g` returnerar endast kluster i den angivna resursgruppen.
> 
> 

**Visa information om klustret**

* Gamla kommando (ASM)- `azure hdinsight cluster show myhdicluster`
* Nytt kommando- `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrera till Azure Resource Manager för Azure PowerShell
Allmän information om Azure PowerShell i läget Azure Resource Manager finns på [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

Resurshanteraren för Azure PowerShell-cmdlet: ar kan installeras bredvid ASM-cmdlets. Cmdlet: ar från de två lägena kan särskiljas genom deras namn.  Resource Manager-läget har *AzureRmHDInsight* i cmdlet-namnen jämföra *AzureHDInsight* i ASM-läge.  Till exempel *ny AzureRmHDInsightCluster* vs. *New-AzureHDInsightCluster*. Parametrar och växlar som kan ha nyheter namn och det finns många nya parametrar när du använder Resource Manager.  Till exempel flera cmdlets kräver en ny växel kallas *- ResourceGroupName*. 

Innan du kan använda HDInsight-cmdlets, måste du ansluta till ditt Azure-konto och skapa en ny resursgrupp:

* Ansluta AzureRmAccount eller [Välj AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Se [autentiserar ett huvudnamn för tjänsten med Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Har bytt namn till cmdlet: ar
Listan HDInsight ASM-cmdlets i Windows PowerShell-konsol:

    help *azurermhdinsight*

I följande tabell visas ASM-cmdlets och deras namn i Resource Manager-läge:

| ASM-cmdlets | Resource Manager-cmdletar |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Nya cmdletar
Följande är de nya cmdletarna som endast är tillgängliga i Resource Manager-läge. 

**Skriptet åtgärd-relaterade cmdlets:**

* **Get-AzureRmHDInsightPersistedScriptAction**: hämtar beständiga skriptåtgärder för ett kluster och visar dem i kronologisk ordning eller hämtar information för en angiven beständiga skriptåtgärder. 
* **Get-AzureRmHDInsightScriptActionHistory**: hämtar skriptåtgärdshistoriken för ett kluster och visas i omvänd kronologisk ordning eller hämtar information om en tidigare skriptåtgärder. 
* **Ta bort AzureRmHDInsightPersistedScriptAction**: tar bort en beständiga skriptåtgärder från ett HDInsight-kluster.
* **Ange AzureRmHDInsightPersistedScriptAction**: Anger en tidigare skriptåtgärd vara beständiga skriptåtgärder.
* **Skicka AzureRmHDInsightScriptAction**: skickar en ny skriptåtgärd till ett Azure HDInsight-kluster. 

Av ytterligare användningsinformation finns i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

**Klustret identity-relaterade cmdlets:**

* **Lägg till AzureRmHDInsightClusterIdentity**: lägger till en kluster-identitet ett konfigurationsobjekt för klustret så att HDInsight-klustret kan komma åt Azure Data Lake butiker. Se [skapar ett HDInsight-kluster med Data Lake Store med hjälp av Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

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

    New-AzureRmHDInsightCluster `
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

Gamla kommando (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nytt kommando:

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Lista över kluster**

Gamla kommando (ASM):

    Get-AzureHDInsightCluster

Nytt kommando:

    Get-AzureRmHDInsightCluster 

**Visa kluster**

Gamla kommando (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nytt kommando:

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Andra exempel
* [Skapa HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Skicka Hive-jobb](hadoop/apache-hadoop-use-hive-powershell.md)
* [Skicka Pig-jobb](hadoop/apache-hadoop-use-pig-powershell.md)
* [Skicka Sqoop jobb](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrera till nya HDInsight .NET SDK
Azure Service Management-baserade [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) är nu föråldrad. Du uppmanas att använda Azure Resource Manager-baserade [Resource Manager-baserade HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt271028.aspx). Används inte följande ASM-baserat HDInsight-paket.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Det här avsnittet innehåller länkar till mer information om hur du utför vissa aktiviteter med hjälp av Resource Manager-baserade SDK.

| Så här... med Resource Manager-baserade HDInsight SDK | Länkar |
| --- | --- |
| Skapa HDInsight-kluster med .NET SDK |Se [skapa HDInsight-kluster med hjälp av .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Anpassa ett kluster med skriptåtgärder med .NET SDK |Se [anpassa HDInsight Linux-kluster med skriptåtgärder](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Autentisera program interaktivt genom att använda Azure Active Directory med .NET SDK |Se [köra Hive-frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Kodstycke i den här artikeln används metoden interaktiv autentisering. |
| Autentisera program interaktivt genom att använda Azure Active Directory med .NET SDK |Se [skapa icke-interaktiva program för HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Skicka ett Hive-jobb med hjälp av .NET SDK |Se [skicka Hive-jobb](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Skicka Pig-jobbet med .NET SDK |Se [skicka Pig-jobb](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Skicka ett Sqoop-jobb med hjälp av .NET SDK |Se [skicka Sqoop jobb](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Visa en lista med HDInsight-kluster med .NET SDK |Se [listan HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Skala HDInsight-kluster med .NET SDK |Se [skala HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Bevilja/återkalla åtkomst till HDInsight-kluster med .NET SDK |Se [bevilja/återkalla åtkomst till HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Uppdatera http-autentiseringsuppgifterna för HDInsight-kluster med .NET SDK |Se [uppdatering HTTP användarautentiseringsuppgifter för HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Hitta standardkontot för lagring för HDInsight-kluster med .NET SDK |Se [hittar standardkontot för lagring för HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Ta bort HDInsight-kluster med .NET SDK |Se [ta bort HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exempel
Nedan följer några exempel på hur en åtgärd är utförs med hjälp av SDK ASM-baserade och motsvarande kodfragmentet för Resource Manager-baserade SDK.

**Skapar ett kluster CRUD-klient**

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
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nytt kommando (auktoriseringen av användare)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Skapar ett kluster**

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

