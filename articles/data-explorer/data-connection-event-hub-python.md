---
title: Skapa en data anslutning för Händelsehubben för Azure Datautforskaren med hjälp av python
description: I den här artikeln får du lära dig hur du skapar en data anslutning för Event Hub för Azure Datautforskaren med hjälp av python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c08271286373ab8c3e621ee6fa59782ba2d16fb4
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444187"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Skapa en data anslutning för Händelsehubben för Azure Datautforskaren med hjälp av python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager-mall](data-connection-event-hub-resource-manager.md)

I den här artikeln skapar du en data anslutning för Event Hub för Azure Datautforskaren med hjälp av python. Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Datautforskaren erbjuder inmatning, eller data inläsning från Event Hubs, IoT-hubbar och blobbar skrivna till BLOB-behållare.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Ett kluster och en databas](create-cluster-database-python.md).

* [Tabell-och kolumn mappning](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Databas-och tabell principer](database-table-policies-python.md) (valfritt).

* [Event Hub med data som ska](ingest-data-event-hub.md#create-an-event-hub)matas in.

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Lägg till en data anslutning för Event Hub

I följande exempel visas hur du lägger till en data anslutning för Event Hub program mässigt. Mer information finns i [ansluta till händelsehubben](ingest-data-event-hub.md#connect-to-the-event-hub) för att lägga till en Event Hub-dataanslutning med hjälp av Azure Portal.

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
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Även känt som katalog-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Det prenumerations-ID som du använder för att skapa resurser.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som har åtkomst till resurser i din klient organisation.|
| client_secret | *xxxxxxxxxxxxxx* | Klient hemligheten för programmet som har åtkomst till resurser i din klient organisation. |
| resource_group_name | *testrg* | Namnet på resurs gruppen som innehåller klustret.|
| cluster_name | *mykustocluster* | Namnet på klustret.|
| database_name | *mykustodatabase* | Namnet på mål databasen i klustret.|
| data_connection_name | *myeventhubconnect* | Det önskade namnet för din data anslutning.|
| table_name | *StormEvents* | Namnet på mål tabellen i mål databasen.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Namnet på kolumn mappningen som är relaterad till mål tabellen.|
| data_format | *SKV* | Meddelandets data format.|
| event_hub_resource_id | *Resurs-ID* | Resurs-ID för Händelsehubben som innehåller data för inmatning. |
| consumer_group | *$Default* | Konsument gruppen för Händelsehubben.|
| location | *USA, centrala* | Platsen för data anslutnings resursen.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]