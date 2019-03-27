---
title: Skapa Apache Hadoop-kluster med hjälp av .NET - Azure HDInsight
description: Lär dig hur du skapar Apache Hadoop, Apache HBase, Apache Storm eller Apache Spark-kluster på Linux för HDInsight med hjälp av HDInsight .NET SDK.
services: hdinsight
author: mamccrea
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: mamccrea
ms.openlocfilehash: 0106063cfe376f31040b5dcd6856282ee09ae5c9
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447959"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-net-sdk"></a>Skapa Linux-baserade kluster i HDInsight med .NET SDK

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]


Lär dig hur du skapar en [Apache Hadoop](https://hadoop.apache.org/) kluster i Azure HDInsight-kluster med hjälp av .NET SDK.

> [!IMPORTANT]  
> Stegen i det här dokumentet kan du skapa ett kluster med en underordnad nod. Om du planerar att mer än 32 arbetarnoder i klustret har skapats eller skala klustret när du har skapat, måste du välja en huvudnod storlek med minst 8 kärnor och 14GB RAM-minne.
>
> Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Ett Azure storage-konto**. Se [skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).
* **Visual Studio 2013, Visual Studio 2015 eller Visual Studio 2017**.

## <a name="create-clusters"></a>Skapa kluster

1. Öppna Visual Studio 2017.
2. Skapa ett nytt Visual C#-konsolprogram.
3. Från den **verktyg** -menyn klickar du på **NuGet-Pakethanteraren**, och klicka sedan på **Pakethanterarkonsolen**.
4. Kör följande kommando i konsolen för att installera paket:

    ```powershell
    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.HDInsight
    ```

    Dessa kommandon Lägg till .NET-bibliotek och referenser till dem i aktuella Visual Studio-projektet.
5. Från Solution Explorer dubbelklickar du på **Program.cs** klistra in följande kod för att öppna den och ange värden för variablerna:

    ```csharp
    using System;
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.HDInsight;
    using Microsoft.Azure.Management.HDInsight.Models;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    namespace CreateHDInsightCluster
    {
        class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;

            private const string SubscriptionId = "<Your Azure Subscription ID>";
            // Replace with your AAD tenant ID if necessary
            private const string TenantId = UserTokenProvider.CommonTenantId; 
            // This is the GUID for the PowerShell client. Used for interactive logins in this example.
            private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

            private const string ExistingResourceGroupName = "<Enter Resource Group Name>";
            private const string ExistingStorageName = "<Enter Default Storage Account Name>.blob.core.windows.net";
            private const string ExistingStorageKey = "<Enter Default Storage Account Key>";
            private const string ExistingBlobContainer = "<Enter Default Bob Container Name>";

            private const string NewClusterName = "<Enter HDInsight Cluster Name>";
            private const int NewClusterNumNodes = 2;
            private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
            private const OSType NewClusterOSType = OSType.Linux;
            private const string NewClusterType = "Hadoop";
            private const string NewClusterVersion = "3.6";
            private const string NewClusterUsername = "admin";
            private const string NewClusterPassword = "<Enter HTTP User Password>";
            private const string NewClusterSshUserName = "sshuser";

            // You can use eitehr password or public key. See https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix
            private const string NewClusterSshPassword = "<Enter SSH User Password>";
            private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
                Comment: ""rsa-key-20150731""
                AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
                gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
                yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
                WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
                pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
                zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
                ---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

            static void Main(string[] args)
            {
                System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

                // Authenticate and get a token
                var authToken = GetTokenCloudCredentials(TenantId, ClientId, SubscriptionId);
                // Flag subscription for HDInsight, if it isn't already.
                EnableHDInsight(authToken);
                // Get an HDInsight management client
                _hdiManagementClient = new HDInsightManagementClient(authToken);

                // Set parameters for the new cluster
                var parameters = new ClusterCreateParameters
                {
                    ClusterSizeInNodes = NewClusterNumNodes,
                    UserName = NewClusterUsername,
                    ClusterType = NewClusterType,
                    OSType = NewClusterOSType,
                    Version = NewClusterVersion,

                    // Use an Azure storage account as the default storage
                    DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingBlobContainer),

                    // Is the cluster type RServer? If so, you can set the EdgeNodeSize.
                    // Otherwise, the default VM size is used.
                    //EdgeNodeSize = "Standard_D12_v2",

                    Password = NewClusterPassword,
                    Location = NewClusterLocation,

                    SshUserName = NewClusterSshUserName,
                    SshPassword = NewClusterSshPassword,
                    //SshPublicKey = NewClusterSshPublicKey
                };

                // Is the cluster type RServer? If so, add the RStudio configuration option.
                /*
                parameters.Configurations.Add(
                    "rserver",
                    new Dictionary<string, string>()
                    {
                        { "rstudio", "true" }
                    }
                );
                */

                // Create the cluster
                _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

                System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            /// <summary>
            /// Authenticate to an Azure subscription and retrieve an authentication token
            /// </summary>
            static TokenCloudCredentials GetTokenCloudCredentials(string TenantId, string ClientId, string SubscriptionId)
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
                var tokenAuthResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
                    ClientId, 
                    new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                    new PlatformParameters(PromptBehavior.Always), 
                    UserIdentifier.AnyUser);
                return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.Result.AccessToken);
            }
            /// <summary>
            /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
            /// </summary>
            /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
            /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
            /// <param name="authToken">An authentication token for your Azure subscription</param>
            static void EnableHDInsight(TokenCloudCredentials authToken)
            {
                // Create a client for the Resource manager and set the subscription ID
                var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
                resourceManagementClient.SubscriptionId = SubscriptionId;
                // Register the HDInsight provider
                var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
            }
        }
    }
    ```

6. Ersätt klassen medlemsvärden.
7. Tryck på **F5** för att köra programmet. Ett konsolfönster ska öppna och visa status för programmet. Du uppmanas att ange dina autentiseringsuppgifter för Azure-konto. Det kan ta flera minuter att skapa ett HDInsight-kluster, normalt cirka 15.

## <a name="use-bootstrap"></a>Använd bootstrap

Du använder bootstrap, kan du konfigurera ytterligare inställningar under de kluster som skapats.  Mer information finns i [anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

Ändrar i exemplet i [Skapa kluster](#create-clusters) att konfigurera en Apache Hive-inställningen:

```csharp
static void Main(string[] args)
{
    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

    // Authenticate and get a token
    var authToken = GetTokenCloudCredentials(TenantId, ClientId, SubscriptionId);
    // Flag subscription for HDInsight, if it isn't already.
    EnableHDInsight(authToken);
    // Get an HDInsight management client
    _hdiManagementClient = new HDInsightManagementClient(authToken);

    // Set parameters for the new cluster
    var extendedParameters = new ClusterCreateParametersExtended
    {
        Location = NewClusterLocation,
        Properties = new ClusterCreateProperties
        {
            ClusterDefinition = new ClusterDefinition
            {
                ClusterType = NewClusterType.ToString()
            },
            ClusterVersion = NewClusterVersion,
            OperatingSystemType = NewClusterOSType
        }
    };

    var coreConfigs = new Dictionary<string, string>
    {
        {"fs.defaultFS", string.Format("wasb://{0}@{1}", ExistingBlobContainer, ExistingStorageName)},
        {
            string.Format("fs.azure.account.key.{0}", ExistingStorageName),
            ExistingStorageKey
        }
    };

    // bootstrap
    var hiveConfigs = new Dictionary<string, string>
    {
        { "hive.metastore.client.socket.timeout", "90"}
    };

    var gatewayConfigs = new Dictionary<string, string>
    {
        {"restAuthCredential.isEnabled", "true"},
        {"restAuthCredential.username", NewClusterUsername},
        {"restAuthCredential.password", NewClusterPassword}
    };

    var configurations = new Dictionary<string, Dictionary<string, string>>
    {
        {"core-site", coreConfigs},
        {"gateway", gatewayConfigs},
        {"hive-site", hiveConfigs}
    };

    var serializedConfig = JsonConvert.SerializeObject(configurations);
    extendedParameters.Properties.ClusterDefinition.Configurations = serializedConfig;

    var sshPublicKeys = new List<SshPublicKey>();
    var sshPublicKey = new SshPublicKey
    {
        CertificateData =
            string.Format("ssh-rsa {0}", NewClusterSshPublicKey)
    };
    sshPublicKeys.Add(sshPublicKey);

    var headNode = new Role
    {
        Name = "headnode",
        TargetInstanceCount = 2,
        HardwareProfile = new HardwareProfile
        {
            VmSize = "Large"
        },
        OsProfile = new OsProfile
        {
            LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
            {
                UserName = NewClusterSshUserName,
                Password = NewClusterSshPassword //,
                // When use a SSH pulbic key, make sure to remove comments, headers and trailers, and concatenate the key into one line 
                //SshProfile = new SshProfile
                //{
                //    SshPublicKeys = sshPublicKeys
                //}
            }
        }
    };

    var workerNode = new Role
    {
        Name = "workernode",
        TargetInstanceCount = NewClusterNumNodes,
        HardwareProfile = new HardwareProfile
        {
            VmSize = "Large"
        },
        OsProfile = new OsProfile
        {
            LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
            {
                UserName = NewClusterSshUserName,
                Password = NewClusterSshPassword //,
                //SshProfile = new SshProfile
                //{
                //    SshPublicKeys = sshPublicKeys
                //}
            }
        }
    };

    extendedParameters.Properties.ComputeProfile = new ComputeProfile();
    extendedParameters.Properties.ComputeProfile.Roles.Add(headNode);
    extendedParameters.Properties.ComputeProfile.Roles.Add(workerNode);

    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, extendedParameters);

    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
    System.Console.ReadLine();
}
```

## <a name="use-script-action"></a>Använd skriptåtgärd

Du använder skriptåtgärd, kan du konfigurera ytterligare inställningar under skapande av kluster.  Mer information finns i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

Ändrar i exemplet i [Skapa kluster](#create-clusters) att anropa en skriptåtgärd för att installera R:

```csharp
static void Main(string[] args)
{
    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

    // Authenticate and get a token
    var authToken = GetTokenCloudCredentials(TenantId, ClientId, SubscriptionId);
    // Flag subscription for HDInsight, if it isn't already.
    EnableHDInsight(authToken);
    // Get an HDInsight management client
    _hdiManagementClient = new HDInsightManagementClient(authToken);

    // Set parameters for the new cluster
    var parameters = new ClusterCreateParameters
    {
        ClusterSizeInNodes = NewClusterNumNodes,
        Location = NewClusterLocation,
        ClusterType = NewClusterType,
        OSType = NewClusterOSType,
        Version = NewClusterVersion,

        DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingBlobContainer),

        UserName = NewClusterUsername,
        Password = NewClusterPassword,
        SshUserName = NewClusterSshUserName,
        SshPublicKey = NewClusterSshPublicKey
    };

    ScriptAction rScriptAction = new ScriptAction("Install R",
        new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

    parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
    parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });

    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
    System.Console.ReadLine();
}
```

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ett HDInsight-kluster, kan du använda följande för att lära dig hur du arbetar med ditt kluster. 

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-kluster
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster
* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-kluster
* [Utveckla Java-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark-kluster
* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark med BI: Utföra interaktiv dataanalys med Spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="run-jobs"></a>Kör jobb
* [Kör Apache Hive-jobb i HDInsight med hjälp av .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [Kör Apache Pig-jobb i HDInsight med hjälp av .NET SDK](hadoop/apache-hadoop-use-pig-dotnet-sdk.md)
* [Kör Apache Sqoop jobb i HDInsight med hjälp av .NET SDK](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md)
* [Kör Apache Oozie-jobb i HDInsight](hdinsight-use-oozie-linux-mac.md)

