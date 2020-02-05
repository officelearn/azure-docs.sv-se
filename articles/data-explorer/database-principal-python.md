---
title: Lägg till databasens huvud namn för Azure Datautforskaren med hjälp av python
description: I den här artikeln får du lära dig hur du lägger till databasens huvud namn för Azure Datautforskaren med hjälp av python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965013"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Lägg till databasens huvud namn för Azure Datautforskaren med hjälp av python

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager-mall](database-principal-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. I den här artikeln lägger du till databas huvud namn för Azure Datautforskaren med hjälp av python.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Skapa ett kluster och en databas](create-cluster-database-python.md)

## <a name="install-python-package"></a>Installera Python-paketet

Om du vill installera python-paketet för Azure Datautforskaren (Kusto) öppnar du en kommando tolk med python i sökvägen. Kör följande kommando:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Lägg till ett databas huvud namn

I följande exempel visas hur du lägger till ett databas huvud objekt program mässigt.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
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
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Även känt som katalog-ID.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Det prenumerations-ID som du använder för att skapa resurser.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som har åtkomst till resurser i din klient organisation.|
| client_secret | *xxxxxxxxxxxxxx* | Klient hemligheten för programmet som har åtkomst till resurser i din klient organisation. |
| resource_group_name | *testrg* | Namnet på resurs gruppen som innehåller klustret.|
| cluster_name | *mykustocluster* | Namnet på klustret.|
| database_name | *mykustodatabase* | Namn på databasen.|
| principal_assignment_name | *databasePrincipalAssignment1* | Namnet på din databas huvud resurs.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ägar-ID: t, som kan vara användarens e-post, program-ID eller namn på säkerhets grupp.|
| roll | *Innehavaradministration* | Rollen som databasens säkerhets objekt, som kan vara admin, "inmatnings", "Monitor", "User", "UnrestrictedViewers", "Viewer".|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Innehavarens ID för huvud kontot.|
| principal_type | *App* | Typ av huvud konto, som kan vara User, app eller Group|

## <a name="next-steps"></a>Nästa steg

* [Mata in data med Azure Datautforskaren python-biblioteket](python-ingest-data.md)
