---
title: Hantera Apache Hadoop-kluster i HDInsight med .NET SDK - Azure
description: Lär dig mer om att utföra administrativa uppgifter för Apache Hadoop-kluster i HDInsight med hjälp av HDInsight .NET SDK.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: d46b1cb5bb8a0b65a9e1b38d5331977132f7eb40
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226461"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Hantera Apache Hadoop-kluster i HDInsight med hjälp av .NET SDK
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Lär dig att hantera HDInsight-kluster med [HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).

**Förutsättningar**

Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Ansluta till Azure HDInsight

Du behöver följande NuGet-paket:

```
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Följande kodexempel visar hur du ansluter till Azure innan du kan administrera HDInsight-kluster för din Azure-prenumeration.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
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

Du bör se ett meddelande när du kör programmet.  Om du inte vill se uppmaningen [skapa .NET HDInsight-program med icke-interaktiv autentisering](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Skapa kluster
Se [skapa Linux-baserade kluster i HDInsight med .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>En lista över kluster
Följande kodavsnitt visar en lista över kluster och vissa egenskaper:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Ta bort kluster
Använd följande kodfragment för att ta bort ett kluster synkront eller asynkront: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Skala kluster
Klusterskalning funktionen kan du ändra antalet arbetarnoder som används av ett kluster som körs i Azure HDInsight utan att behöva återskapa klustret.

> [!NOTE]  
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av ditt kluster kan kontrollera du egenskapssidan.  Se [lista och visa kluster](hdinsight-administer-use-portal-linux.md#showClusters).

Effekten av att ändra antalet datanoder som för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop
  
    Du kan smidigt öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller pågående jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras ett smidigt sätt så att klustret finns alltid kvar i funktionsdugligt tillstånd.
  
    När ett Hadoop-kluster skalas genom att minska antalet datanoder några tjänster i klustret startas om. Detta gör allt körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.
* Apache HBase
  
    Du kan smidigt lägga till eller ta bort noder till HBase-kluster medan den körs. Regionservrar balanseras automatiskt inom ett par minuter efter att du skalar igen. Du kan också manuellt balansera regionservrar genom att logga till huvudnoden i klustret och köra följande kommandon från en kommandotolk:
  
    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```
* Apache Storm
  
    Du kan smidigt lägga till eller ta bort datanoder till ditt Storm-kluster medan den körs. Men när en installationen har slutförts åtgärdens skalning, behöver du balansera om topologin.
  
    Ombalansering kan utföras på två sätt:
  
  * Storm-Webbgränssnittet
  * Verktyget kommandoradsgränssnittet (CLI)
    
    Finns det [Apache Storm-dokumentationen](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.
    
    Storm-webbgränssnittet finns på HDInsight-klustret:
    
    ![HDInsight Storm skala ombalansering](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Här är ett exempel hur du använder CLI-kommando för att balansera om Storm-topologi:
    
    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Följande kodfragment visar hur du ändrar storlek på ett kluster synkront eller asynkront:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Bevilja/återkalla åtkomst
HDInsight-kluster har följande HTTP-webbtjänster (alla dessa tjänster har RESTful-slutpunkter):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Som standard beviljas de här tjänsterna för åtkomst. Du kan återkalla/bevilja åtkomst. Återkalla:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Så här ger:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Genom att bevilja/återkalla åtkomst, ska du återställa klustret användarnamn och lösenord.

Detta kan även göras via portalen. Se [hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Uppdatera HTTP-autentiseringsuppgifterna
Det är på samma sätt som Grant/revoke HTTP-åtkomst.  Om klustret har beviljats HTTP-åtkomst, måste du först återkalla den.  Och sedan bevilja åtkomst med nya HTTP-autentiseringsuppgifterna.

## <a name="find-the-default-storage-account"></a>Hitta standardkontot för lagring
Följande kodfragment visar hur du hämtar standard lagringskontonamn och lagringskontonyckeln standard för ett kluster.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Skicka jobb
**Att skicka MapReduce-jobb**

Se [kör MapReduce-exemplen i HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Att skicka Apache Hive-jobb** 

Se [kör Apache Hive-frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Skicka Apache Pig-jobb**

Se [kör Apache Pig-jobb med hjälp av .NET SDK](hadoop/apache-hadoop-use-pig-dotnet-sdk.md).

**Skicka Apache Sqoop jobb**

Se [Använd Apache Sqoop med HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Skicka Apache Oozie-jobb**

Se [Använd Apache Oozie med Hadoop för att definiera och köra ett arbetsflöde i HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure Blob storage
Mer information finns i [Överföra data till HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Se även
* [HDInsight .NET SDK referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md)
* [Administrera HDInsight med ett kommandoradsgränssnitt][hdinsight-admin-cli]
* [Skapa HDInsight-kluster][hdinsight-provision]
* [Överföra data till HDInsight][hdinsight-upload-data]
* [Kom igång med Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md


