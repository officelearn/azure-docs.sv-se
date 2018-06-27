---
title: Hantera Hadoop-kluster i HDInsight med .NET SDK - Azure | Microsoft Docs
description: Lär dig mer om att utföra administrativa uppgifter för Hadoop-kluster i HDInsight med HDInsight .NET SDK.
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: ''
ms.assetid: fd134765-c2a0-488a-bca6-184d814d78e9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 08c9d16570a923c79c81cebb8669a43488129d9a
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017945"
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Hantera Hadoop-kluster i HDInsight med hjälp av .NET SDK
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

En uppmaning visas när du kör programmet.  Om du inte vill se uppmaningen [skapa icke-interaktiv autentisering .NET HDInsight-program](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Skapa kluster
Se [skapa Linux-baserade kluster i HDInsight med hjälp av .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>Lista över kluster
Följande kodavsnitt visas kluster och vissa egenskaper:

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
Klustret skalning funktionen kan du ändra antalet arbetarnoder som används av ett kluster som körs i Azure HDInsight utan att behöva återskapa klustret.

> [!NOTE]
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av klustret kan kontrollera du egenskapssidan.  Se [listan och visa](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
> 
> 

Konsekvenser av att ändra antalet datanoder som för varje typ av kluster som stöds av HDInsight:

* Hadoop
  
    Sömlöst kan du öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller körs jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras korrekt så att klustret alltid kvar i ett fungerande tillstånd.
  
    När ett Hadoop-kluster skalas ned genom att minska antalet datanoder som, en del av tjänsterna i klustret har startats om. Detta medför att alla körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.
* HBase
  
    Du kan sömlöst Lägg till eller ta bort noder till HBase-kluster, medan den körs. Regional servrar balanseras automatiskt inom några minuter för att slutföra åtgärden. skalning. Du kan också manuellt balansera regionala servrar genom att logga in på headnode i klustret och köra följande kommandon från en kommandotolk:
  
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
    
    Mer information finns i [Apache Storm dokumentationen](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.
    
    Storm webbgränssnittet är tillgänglig på HDInsight-kluster:
    
    ![HDInsight Storm skala balansera](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Här är ett exempel så använder du kommandot CLI för att balansera Storm-topologi:
    
    ```cli
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
HDInsight-kluster har följande HTTP-webbtjänster (alla dessa tjänster har RESTful slutpunkter):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Som standard beviljas dessa tjänster för åtkomst. Du kan återkalla/bevilja åtkomst. Återkalla:

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
> Genom att bevilja/återkalla åtkomst, återställs klustret användarnamn och lösenord.
> 
> 

Detta kan även göras via portalen. Se [administrera HDInsight med hjälp av Azure portal][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Uppdatera autentiseringsuppgifterna för HTTP
Det är på samma sätt som [HTTP att bevilja/återkalla åtkomst](#grant/revoke-access). Om klustret har beviljats HTTP-åtkomst, måste du först återkalla den.  Och sedan ge åtkomst med nya HTTP-autentiseringsuppgifter.

## <a name="find-the-default-storage-account"></a>Hitta standardkontot för lagring
Följande kodavsnitt visar hur du hämtar standard lagringskontots namn och standard lagringskontots åtkomstnyckel för ett kluster.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Skicka jobb
**Att skicka MapReduce-jobb**

Se [kör Hadoop-MapReduce-exempel i HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Att skicka Hive-jobb** 

Se [köra Hive-frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Skicka Pig-jobb**

Se [köra Pig-jobb med hjälp av .NET SDK](hadoop/apache-hadoop-use-pig-dotnet-sdk.md).

**Skicka Sqoop jobb**

Se [använda Sqoop med HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Skicka Oozie jobb**

Se [Oozie för användning med Hadoop för att definiera och köra ett arbetsflöde i HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure Blob storage
Mer information finns i [Överföra data till HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Se även
* [HDInsight .NET SDK-referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Administrera HDInsight med hjälp av Azure portal][hdinsight-admin-portal]
* [Administrera HDInsight med ett kommandoradsgränssnitt][hdinsight-admin-cli]
* [Skapa HDInsight-kluster][hdinsight-provision]
* [Överföra data till HDInsight][hdinsight-upload-data]
* [Kom igång med Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


