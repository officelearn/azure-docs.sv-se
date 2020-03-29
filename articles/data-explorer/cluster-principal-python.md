---
title: Lägga till klusterobjektnamn för Azure Data Explorer med hjälp av Python
description: I den här artikeln får du lära dig hur du lägger till klusterobjektnamn för Azure Data Explorer med hjälp av Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 637efdfe31d1f2eb0eaa5dd532dd9e9e67de5ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965143"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Lägga till klusterobjektnamn för Azure Data Explorer med hjälp av Python

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager-mall](cluster-principal-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. I den här artikeln lägger du till klusterobjektnamn för Azure Data Explorer med hjälp av Python.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Skapa ett kluster](create-cluster-database-python.md).

## <a name="install-python-package"></a>Installera Python-paketet

Om du vill installera Python-paketet för Azure Data Explorer (Kusto) öppnar du en kommandotolk som har Python i sökvägen. Kör följande kommando:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Lägga till ett klusterhuvudnamn

I följande exempel visas hur du lägger till ett klusterhuvudnamn programmässigt.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
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
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Kallas även katalog-ID.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Prenumerations-ID som du använder för att skapa resurser.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som kan komma åt resurser i din klient.|
| client_secret | *xxxxxxxxxxxxxx* | Klienthemligheten för programmet som kan komma åt resurser i din klientorganisation. |
| resource_group_name | *testrg* | Namnet på resursgruppen som innehåller klustret.|
| cluster_name | *mykustocluster (en)* | Namnet på klustret.|
| principal_assignment_name | *klusterPrincipAtilldelning1* | Namnet på klustrets huvudresurs.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Huvud-ID: t, som kan vara användar-E-post, program-ID eller säkerhetsgruppnamn.|
| roll | *AllDatabasesAdmin* | Rollen för ditt klusterhuvudnamn, som kan vara "AllDatabasesAdmin" eller "AllDatabasesViewer".|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för huvudmannen.|
| principal_type | *App* | Typen av huvudnamn, som kan vara "Användare", "App" eller "Grupp"|

## <a name="next-steps"></a>Nästa steg

* [Lägga till databashuvudnamn](database-principal-python.md)
