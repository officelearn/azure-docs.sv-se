---
title: Skapa en IoT Hub data anslutning för Azure Datautforskaren med hjälp av python
description: I den här artikeln får du lära dig hur du skapar en IoT Hub data anslutning för Azure Datautforskaren med hjälp av python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 86e966cc3bf98e63edbe90d7649242dcb1ccdf42
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964387"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Skapa en IoT Hub data anslutning för Azure Datautforskaren med hjälp av python (för hands version)

> [!div class="op_single_selector"]
> * [Portalen](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-mall](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Datautforskaren erbjuder inmatning (data inläsning) från Event Hubs, IoT-hubbar och blobbar skrivna till BLOB-behållare. I den här artikeln skapar du en IoT Hub data anslutning för Azure Datautforskaren med hjälp av python.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Skapa [ett kluster och en databas](create-cluster-database-python.md)
* Skapa [tabell-och kolumn mappning](python-ingest-data.md#create-a-table-on-your-cluster)
* Ange [databas-och tabell principer](database-table-policies-python.md) (valfritt)
* Skapa en [IoT Hub med en delad åtkomst princip konfigurerad](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Lägg till en IoT Hub data anslutning 

I följande exempel visas hur du lägger till en IoT Hub data anslutning program mässigt. Se [ansluta Azure datautforskaren-tabellen till IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) för att lägga till en data anslutning för IoT Hub med hjälp av Azure Portal.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
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
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
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
| iot_hub_resource_id | *Resurs-ID* | Resurs-ID för din IoT-hubb som innehåller data för inmatning.|
| shared_access_policy_name | *iothubforread* | Namnet på den delade åtkomst principen som definierar behörigheterna för enheter och tjänster som ska anslutas till IoT Hub. |
| consumer_group | *$Default* | Konsument gruppen för händelsehubben.|
| location | *USA, centrala* | Platsen för data anslutnings resursen.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]