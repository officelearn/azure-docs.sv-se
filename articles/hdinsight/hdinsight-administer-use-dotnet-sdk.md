---
title: Hantera Apache Hadoop-kluster i HDInsight med .NET SDK - Azure
description: Lär dig hur du utför administrativa uppgifter för Apache Hadoop-kluster i HDInsight med HDInsight .NET SDK.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 36a77d49b507d3d0158d1b4b492d0141350de50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240645"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Hantera Apache Hadoop-kluster i HDInsight med hjälp av .NET SDK

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Lär dig hur du hanterar HDInsight-kluster med [HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).

**Krav**

Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Ansluta till Azure HDInsight

Du behöver följande NuGet-paket:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Följande kodexempel visar hur du ansluter till Azure innan du kan administrera HDInsight-kluster under din Azure-prenumeration.

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

Du ska se en uppmaning när du kör det här programmet.  Om du inte vill se uppmaningen läser du [Skapa icke-interaktiva autentisering .NET HDInsight-program](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


## <a name="list-clusters"></a>Lista kluster

Följande kodavsnitt visar kluster och vissa egenskaper:

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

Använd följande kodavsnitt för att ta bort ett kluster synkront eller asynkront: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Skala kluster

Med klusterskalningsfunktionen kan du ändra antalet arbetsnoder som används av ett kluster som körs i Azure HDInsight utan att behöva återskapa klustret.

> [!NOTE]  
> Endast kluster med HDInsight version 3.1.3 eller senare stöds. Om du är osäker på vilken version av klustret du har kan du kontrollera sidan Egenskaper.  Se [Lista och visa kluster](hdinsight-administer-use-portal-linux.md#showClusters).

Effekten av att ändra antalet datanoder för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop
  
    Du kan sömlöst öka antalet arbetsnoder i ett Hadoop-kluster som körs utan att påverka väntande eller köra jobb. Nya jobb kan också skickas in medan åtgärden pågår. Fel i en skalningsåtgärd hanteras smidigt så att klustret alltid lämnas i ett funktionellt tillstånd.
  
    När ett Hadoop-kluster skalas ned genom att minska antalet datanoder startas vissa tjänster i klustret om. Detta medför att alla jobb som körs och väntar misslyckas när skalningsåtgärden har slutförts. Du kan dock skicka jobben igen när åtgärden är klar.
* Apache HBase
  
    Du kan sömlöst lägga till eller ta bort noder i HBase-klustret medan det körs. Regionala servrar balanseras automatiskt inom några minuter efter att skalningsåtgärden har slutförts. Du kan dock också balansera de regionala servrarna manuellt genom att logga in på huvudnoden för klustret och köra följande kommandon från ett kommandotolksfönster:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Du kan sömlöst lägga till eller ta bort datanoder i Storm-klustret medan det körs. Men efter ett framgångsrikt slutförande av skalningsåtgärden måste du balansera om topologin.
  
    Ombalansering kan åstadkommas på två sätt:
  
  * Användargränssnittet för stormwebb
  * Cli-verktyg (Command-line interface)
    
    Mer information finns i [Apache Storm-dokumentationen.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)
    
    Webbgränssnittet Storm är tillgängligt i HDInsight-klustret:
    
    ![HDInsight Storm skala balans](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Här är ett exempel på hur du använder CLI-kommandot för att balansera om Storm-topologin:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Följande kodavsnitt visar hur du ändrar storlek på ett kluster synkront eller asynkront:

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

Som standard beviljas dessa tjänster för åtkomst. Du kan återkalla/bevilja åtkomsten. Så här återkallar du:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Så här beviljar du:

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
> Genom att bevilja/återkalla åtkomsten återställer du klustrets användarnamn och lösenord.

Detta kan också göras via portalen. Se [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Uppdatera HTTP-användaruppgifter

Det är samma procedur som bevilja/återkalla HTTP-åtkomst.  Om klustret har tilldelats HTTP-åtkomsten måste du först återkalla den.  Och sedan bevilja åtkomst med nya HTTP-användarautentiseringsuppgifter.

## <a name="find-the-default-storage-account"></a>Hitta standardlagringskontot

Följande kodavsnitt visar hur du hämtar standardkontonamnet för lagringskontot och standardlagringskontonyckeln för ett kluster.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Skicka jobb

**Så här skickar du MapReduce-jobb**

Se [Kör MapReduce-exempel i HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Så här skickar du Apache Hive-jobb** 

Se [Köra Apache Hive-frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Så här skickar du Apache Sqoop-jobb**

Se [Använd Apache Sqoop med HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Så här skickar du Apache Oozie-jobb**

Se [Använda Apache Oozie med Hadoop för att definiera och köra ett arbetsflöde i HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure Blob-lagring

Mer information finns i [Överföra data till HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Se även

* [HDInsight .NET SDK-referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md)
* [Administrera HDInsight med hjälp av ett kommandoradsgränssnitt][hdinsight-admin-cli]
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
