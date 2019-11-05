---
title: Skapa ett Azure Datautforskaren-kluster och-databas med hjälp avC#
description: Lär dig hur du skapar ett Azure Datautforskaren-kluster och-databas med hjälp avC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: b5839ef7d9f1e5498beabfcdf0f1605fc1378498
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800425"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Skapa ett Azure Datautforskaren-kluster och-databas med hjälp avC#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-mall](create-cluster-database-resource-manager.md)

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. I den här artikeln skapar du ett kluster och en databas med hjälp C#av.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="install-c-nuget"></a>Installera C# NuGet

* Installera [Azure datautforskaren (Kusto) NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Installera [Microsoft. IdentityModel. clients. ActiveDirectory NuGet-paketet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) för autentisering.

## <a name="authentication"></a>Autentisering
För att kunna köra exemplen i den här artikeln behöver vi ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser. Markera [skapa ett Azure AD-program](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) om du vill skapa ett kostnads fritt Azure AD-program och lägga till roll tilldelning i prenumerations omfånget. Det visar också hur du hämtar `Directory (tenant) ID`, `Application ID` och `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Skapa Azure Data Explorer-klustret

1. Skapa klustret med hjälp av följande kod:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | clusterName | *mykustocluster* | Önskat namn på klustret.|
   | skuName | *Standard_D13_v2* | Den SKU som ska användas för klustret. |
   | nivå | *Standard* | SKU-nivån. |
   | capacity | *många* | Antalet instanser av klustret. |
   | resourceGroupName | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |

    > [!NOTE]
    > **Skapa ett kluster** är en tids krävande åtgärd, så det rekommenderas att använda CreateOrUpdateAsync i stället för CreateOrUpdate. 

1. Kör följande kommando för att kontrollera om klustret har skapats:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Om resultatet innehåller `ProvisioningState` med värdet `Succeeded` har klustret skapats.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Skapa databasen i Azure Data Explorer-klustret

1. Skapa databasen med hjälp av följande kod:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | clusterName | *mykustocluster* | Namnet på det kluster där databasen ska skapas.|
   | Databas | *mykustodatabase* | Namn på databasen.|
   | resourceGroupName | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |
   | softDeletePeriod | *3650:00:00:00* | Hur lång tid data ska behållas för att vara tillgängliga för frågor. |
   | hotCachePeriod | *3650:00:00:00* | Hur lång tid data ska behållas i cache. |

2. Kör följande kommando för att se den databas som du skapade:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Nu har du ett kluster och en databas.

## <a name="clean-up-resources"></a>Rensa resurser

* Behåll de resurser du har skapat om du planerar att följa våra andra artiklar.
* Ta bort klustret om du vill rensa resurser. När du tar bort ett kluster, raderas också alla databaser i den. Använd följande kommando för att ta bort klustret:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Nästa steg

* [Mata in data med Azure Datautforskaren .NET standard SDK (för hands version)](net-standard-ingest-data.md)
