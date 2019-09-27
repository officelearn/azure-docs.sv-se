---
title: Skapa ett Azure Datautforskaren-kluster och-databas med hjälp avC#
description: Lär dig hur du skapar ett Azure Datautforskaren-kluster och-databas med hjälp avC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 4a3f37c232fcd7a0fcbdac051ed36916ef5c2868
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326669"
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

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="install-c-nuget"></a>Installera C# NuGet

1. Installera [Azure datautforskaren (Kusto) NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

1. Installera [Microsoft. IdentityModel. clients. ActiveDirectory NuGet-paketet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) för autentisering.

## <a name="create-the-azure-data-explorer-cluster"></a>Skapa Azure Data Explorer-klustret

1. Skapa klustret med hjälp av följande kod:

    ```csharp
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var sku = new AzureSku("D13_v2", 5);
    var cluster = new Cluster(location, sku);

    var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    kustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | clusterName | *mykustocluster* | Önskat namn på klustret.|
   | sku | *D13_v2* | Den SKU som ska användas för klustret. |
   | resourceGroupName | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |

    Det finns ytterligare parametrar som du kan använda, till exempel kapaciteten för klustret.

1. Ange [dina autentiseringsuppgifter](https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)

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

    kustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
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
