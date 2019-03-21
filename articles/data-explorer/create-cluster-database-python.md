---
title: 'Snabbstart: Skapa ett Azure Data Explorer-kluster och en databas med hjälp av Python'
description: Lär dig hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av Python
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 4f87c5996ea323c26c32c1680ba6f627bf8f95c2
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287541"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Skapa ett Azure Data Explorer-kluster och en databas med hjälp av Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Den här snabbstarten beskriver hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av Python.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att kunna utföra den här snabbstarten. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="install-python-package"></a>Installera Python-paketet

Öppna en kommandotolk med Python i dess sökväg för att installera Python-paketet för Azure Data Explorer (Kusto), och sedan köra det här kommandot:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Skapa Azure Data Explorer-klustret

1. Skapa klustret med hjälp av följande kommando:

    

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Önskat namn på klustret.|
   | sku | *D13_v2* | Den SKU som ska användas för klustret. |
   | resource_group_name | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |

    Det finns ytterligare parametrar som du kan använda, till exempel kapaciteten för klustret.
    
    Ange autentiseringsuppgifterna du dina autentiseringsuppgifter (Mer information finns i https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python )

2. Kör följande kommando för att kontrollera om klustret har skapats:

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
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Namnet på det kluster där databasen ska skapas.|
   | databasnamn | *mykustodatabase* | Namn på databasen.|
   | resource_group_name | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |
   | soft_delete_period | *3650 dagar, 0:00:00* | Hur lång tid data ska behållas för att vara tillgängliga för frågor. |
   | hot_cache_period | *3650 dagar, 0:00:00* | Hur lång tid data ska behållas i cache. |

2. Kör följande kommando för att se den databas som du skapade:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Nu har du ett kluster och en databas.

## <a name="clean-up-resources"></a>Rensa resurser

* Om du planerar att följa våra andra snabbstarter och självstudier kan du spara alla resurser som du skapade.
* Ta bort klustret om du vill rensa resurser. När du tar bort ett kluster, raderas också alla databaser i den. Använd följande kommando för att ta bort klustret:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Mata in data med hjälp av Python-biblioteket](python-ingest-data.md) i Azure Data Explorer
