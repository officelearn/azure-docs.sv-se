---
title: Skapa ett Azure Datautforskaren-kluster och-databas med hjälp av python
description: Lär dig hur du skapar ett Azure Datautforskaren-kluster och-databas med hjälp av python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 50e050a05fd364a4b1f880e3501b04274ffd360c
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444238"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Skapa ett Azure Datautforskaren-kluster och-databas med hjälp av python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-mall](create-cluster-database-resource-manager.md)

I den här artikeln skapar du ett Azure Datautforskaren-kluster och-databas med hjälp av python. Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. Om du vill använda Azure Datautforskaren måste du först skapa ett kluster och skapa en eller flera databaser i klustret. Mata in eller Läs in data i en databas så att du kan köra frågor mot den.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Ett Azure AD-program och tjänstens huvud namn som har åtkomst till resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Hämta värden för `Directory (tenant) ID`, `Application ID`och `Client Secret`.

## <a name="install-python-package"></a>Installera Python-paketet

Om du vill installera python-paketet för Azure Datautforskaren (Kusto) öppnar du en kommando tolk med python i sökvägen. Kör följande kommando:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Autentisering
För att kunna köra exemplen i den här artikeln behöver vi ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser. Markera [skapa ett Azure AD-program](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) om du vill skapa ett kostnads fritt Azure AD-program och lägga till roll tilldelning i prenumerations omfånget. Det visar också hur du hämtar `Directory (tenant) ID`, `Application ID`och `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Skapa Azure Data Explorer-klustret

1. Skapa klustret med hjälp av följande kommando:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
    from azure.common.credentials import ServicePrincipalCredentials

    #Directory (tenant) ID
    tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Application ID
    client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Client Secret
    client_secret = "xxxxxxxxxxxxxx"
    subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Önskat namn på klustret.|
   | sku_name | *Standard_D13_v2* | Den SKU som ska användas för klustret. |
   | nivå | *Standard* | SKU-nivån. |
   | capacity | *många* | Antalet instanser av klustret. |
   | resource_group_name | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |

    > [!NOTE]
    > **Skapa ett kluster** är en tids krävande åtgärd. Metoden **create_or_update** returnerar en instans av LROPoller, se [LROPoller-klassen](/python/api/msrest/msrest.polling.lropoller?view=azure-python) för att få mer information.

1. Kör följande kommando för att kontrollera om klustret har skapats:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Om resultatet innehåller `provisioningState` med värdet `Succeeded` har klustret skapats.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Skapa databasen i Azure Data Explorer-klustret

1. Skapa databasen med hjälp av följande kommando:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

        [!NOTE]
        If you are using Python version 0.4.0 or below, use Database instead of ReadWriteDatabase.

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Namnet på det kluster där databasen ska skapas.|
   | database_name | *mykustodatabase* | Namn på databasen.|
   | resource_group_name | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |
   | soft_delete_period | *3650 dagar, 0:00:00* | Hur lång tid data ska behållas för att vara tillgängliga för frågor. |
   | hot_cache_period | *3650 dagar, 0:00:00* | Hur lång tid data ska behållas i cache. |

1. Kör följande kommando för att se den databas som du skapade:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Nu har du ett kluster och en databas.

## <a name="clean-up-resources"></a>Rensa resurser

* Behåll de resurser du har skapat om du planerar att följa våra andra artiklar.
* Ta bort klustret om du vill rensa resurser. När du tar bort ett kluster, raderas också alla databaser i den. Använd följande kommando för att ta bort klustret:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Nästa steg

* [Mata in data med Azure Datautforskaren python-biblioteket](python-ingest-data.md)
