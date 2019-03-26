---
title: 'Snabbstart: Skapa ett Azure Data Explorer-kluster och en databas med hjälp av Python'
description: Lär dig hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av Python
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: db6064feb379bf7da4f2c2e6417583c3d8b8b0d3
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417895"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Skapa ett Azure Data Explorer-kluster och en databas med hjälp av Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. I den här snabbstarten skapar du ett kluster och en databas med hjälp av Python.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

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
    
1. Ange [ *dina autentiseringsuppgifter*](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)

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

1. Kör följande kommando för att se den databas som du skapade:

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
