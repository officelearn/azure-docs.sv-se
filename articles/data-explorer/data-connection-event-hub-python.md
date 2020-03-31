---
title: Skapa en event hub-dataanslutning för Azure Data Explorer med hjälp av Python
description: I den här artikeln får du lära dig hur du skapar en Event Hub-dataanslutning för Azure Data Explorer med hjälp av Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c08271286373ab8c3e621ee6fa59782ba2d16fb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444187"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Skapa en event hub-dataanslutning för Azure Data Explorer med hjälp av Python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager-mall](data-connection-event-hub-resource-manager.md)

I den här artikeln skapar du en Event Hub-dataanslutning för Azure Data Explorer med hjälp av Python. Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer erbjuder inmatning, eller datainläsning, från eventhubbar, IoT-hubbar och blobbar skrivna till blob-behållare.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Ett kluster och en databas](create-cluster-database-python.md).

* [Tabell- och kolumnmappning](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Databas- och tabellprinciper](database-table-policies-python.md) (valfritt).

* [Event Hub med data för inmatning](ingest-data-event-hub.md#create-an-event-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Lägga till en händelsehubb-dataanslutning

I följande exempel visas hur du lägger till en eventhub-dataanslutning programmässigt. Se [ansluta till händelsehubben](ingest-data-event-hub.md#connect-to-the-event-hub) för att lägga till en Event Hub-dataanslutning med Azure-portalen.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
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
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Kallas även katalog-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Prenumerations-ID som du använder för att skapa resurser.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som kan komma åt resurser i din klient.|
| client_secret | *xxxxxxxxxxxxxx* | Klienthemligheten för programmet som kan komma åt resurser i din klientorganisation. |
| resource_group_name | *testrg* | Namnet på resursgruppen som innehåller klustret.|
| cluster_name | *mykustocluster (en)* | Namnet på klustret.|
| database_name | *mykustodatabas* | Namnet på måldatabasen i klustret.|
| data_connection_name | *myeventhubconnect* | Önskat namn på din dataanslutning.|
| Table_name | *StormEvents* | Namnet på måltabellen i måldatabasen.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Namnet på kolumnmappningen som är relaterad till måltabellen.|
| data_format | *Csv* | Meddelandets dataformat.|
| event_hub_resource_id | *Resurs-ID* | Resurs-ID:t för händelsehubben som innehåller data för inmatning. |
| consumer_group | *$Default* | Konsumentgruppen för din Event Hub.|
| location | *USA, centrala* | Platsen för dataanslutningsresursen.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]