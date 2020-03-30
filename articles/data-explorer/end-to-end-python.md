---
title: Inmatning från på till sluttid i Azure Data Explorer via Python
description: I den här artikeln får du lära dig hur du intar blobbar i Azure Data Explorer med ett end-to-end-exempel som använder Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 61864c51c2ab99e5266e39f2c9a7344aaf7413c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964316"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-python"></a>Inmatning från på till sluttid i Azure Data Explorer via Python

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Data Explorer är en snabb och skalbar datautforskningstjänst för logg- och telemetridata. Den här artikeln ger dig ett heltäckande exempel på hur du inmatar data från Azure Blob-lagring i Azure Data Explorer. 

Du får lära dig hur du programmässigt skapar en resursgrupp, ett lagringskonto och en behållare, en händelsenav och ett Azure Data Explorer-kluster och databas. Du får också lära dig hur du programmässigt konfigurerar Azure Data Explorer för att få in data från det nya lagringskontot.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="install-the-python-package"></a>Installera Python-paketet

Om du vill installera Python-paketet för Azure Data Explorer (Kusto) öppnar du en kommandotolk som har Python i sökvägen. Kör dessa kommandon:

```
pip install azure-common
pip install azure-mgmt-resource
pip install azure-mgmt-kusto
pip install azure-mgmt-eventgrid
pip install azure-kusto-data
pip install azure-storage-blob
```
[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](../../includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Kodexempel 

I följande kodexempel får du en steg-för-steg-process som resulterar i datainmatning i Azure Data Explorer. 

Du skapar först en resursgrupp. Du skapar också Azure-resurser som ett lagringskonto och en behållare, en händelsenav och ett Azure Data Explorer-kluster och databas och lägger till huvudnamn. Du skapar sedan en Azure Event Grid-prenumeration, tillsammans med en tabell- och kolumnmappning, i Azure Data Explorer-databasen. Slutligen skapar du dataanslutningen för att konfigurera Azure Data Explorer för att få in data från det nya lagringskontot.

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.resource.resources.models import DeploymentMode
import os.path
import json
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.mgmt.eventgrid import EventGridManagementClient
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
location = "West Europe"
location_small_case = "westeurope"
#Path to the Azure Resource Manager template JSON from the previous section
azure_resource_template_path = "xxxxxxxxx/template.json";

deployment_name = 'e2eexample'
resource_group_name = deployment_name + "resourcegroup"
event_hub_name = deployment_name + "eventhub"
event_hub_namespace_name = event_hub_name + "ns"
storage_account_name = deployment_name + "storage"
storage_container_name = deployment_name + "storagecontainer"
event_grid_subscription_name = deployment_name + "eventgrid"
kusto_cluster_name = deployment_name + "kustocluster"
kusto_database_name = deployment_name + "kustodatabase"
kusto_table_name = "Events"
kusto_column_mapping_name = "Events_CSV_Mapping"
kusto_data_connection_name = deployment_name + "kustoeventgridconnection"

#principals
principal_id_for_cluster = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
role_for_cluster_principal = "AllDatabasesAdmin";
tenant_id_for_cluster_principal = tenant_id;
principal_type_for_cluster = "App";
principal_id_for_database = "xxxxxxxx@xxxxxxxx.com";//User Email
role_for_database_principal = "Admin";
tenant_id_for_database_principal = tenant_id;
principal_type_for_database = "User";


credentials = ServicePrincipalCredentials(
    client_id=client_id,
    secret=client_secret,
    tenant=tenant_id
)
resource_client = ResourceManagementClient(credentials, subscription_id)

print('Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.')
resource_client.resource_groups.create_or_update(
    resource_group_name,
    {
        'location': location_small_case
    }
)

print('Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, database, and add principals by using an Azure Resource Manager template.')
#Read the Azure Resource Manager template
with open(azure_resource_template_path, 'r') as template_file_fd:
    template = json.load(template_file_fd)

parameters = {
    'eventHubNamespaceName': event_hub_namespace_name,
    'eventHubName': event_hub_name,
    'storageAccountName': storage_account_name,
    'containerName': storage_container_name,
    'kustoClusterName': kusto_cluster_name,
    'kustoDatabaseName': kusto_database_name,
    'principalIdForCluster': principal_id_for_cluster,
    'roleForClusterPrincipal': role_for_cluster_principal,
    'tenantIdForClusterPrincipal': tenant_id_for_cluster_principal,
    'principalTypeForCluster': principal_type_for_cluster,
    'principalIdForDatabase': principal_id_for_database,
    'roleForDatabasePrincipal': role_for_database_principal,
    'tenantIdForDatabasePrincipal': tenant_id_for_database_principal,
    'principalTypeForDatabase': principal_type_for_database
}
parameters = {k: {'value': v} for k, v in parameters.items()}
deployment_properties = {
    'mode': DeploymentMode.incremental,
    'template': template,
    'parameters': parameters
}

#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = resource_client.deployments.create_or_update(
    resource_group_name,
    deployment_name,
    deployment_properties
)
poller.wait()

print('Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.')
event_client = EventGridManagementClient(credentials, subscription_id)
storage_resource_id = '/subscriptions/{}/resourceGroups/{}/providers/Microsoft.Storage/storageAccounts/{}'.format(subscription_id, resource_group_name, storage_account_name)
event_hub_resource_id = '/subscriptions/{}/resourceGroups/{}/providers/Microsoft.EventHub/namespaces/{}/eventhubs/{}'.format(subscription_id, resource_group_name, event_hub_namespace_name, event_hub_name)
event_client.event_subscriptions.create_or_update(storage_resource_id, event_grid_subscription_name, {
    'destination': {
        'endpointType': 'EventHub',
        'properties': {
            'resourceId': event_hub_resource_id
        }
    },
    "filter": {
        "subjectBeginsWith": "/blobServices/default/containers/{}".format(storage_container_name),
        "includedEventTypes": ["Microsoft.Storage.BlobCreated"],
        "advancedFilters": []
    }
})


print('Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.')
kusto_uri = "https://{}.{}.kusto.windows.net".format(kusto_cluster_name, location_small_case)
database_name = kusto_database_name
kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)
kusto_client = KustoClient(kusto_connection_string_builder)
create_table_command = ".create table " + kusto_table_name + " (EventTime: datetime, EventId: int, EventSummary: string)"
kusto_client.execute_mgmt(database_name, create_table_command)

create_column_mapping_command = ".create table " + kusto_table_name + " ingestion csv mapping '" + kusto_column_mapping_name \
                                + """' '[{"Name":"EventTime","datatype":"datetime","Ordinal":0},{"Name":"EventId","datatype":"int","Ordinal":1},{"Name":"EventSummary","datatype":"string","Ordinal":2}]'"""
kusto_client.execute_mgmt(database_name, create_column_mapping_command)


print('Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.')
kusto_management_client = KustoManagementClient(credentials, subscription_id)
data_connections = kusto_management_client.data_connections
#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=kusto_cluster_name, database_name=kusto_database_name, data_connection_name=kusto_data_connection_name,
                                           parameters=EventGridDataConnection(storage_account_resource_id=storage_resource_id,
                                                                              event_hub_resource_id=event_hub_resource_id, consumer_group="$Default", location=location, table_name=kusto_table_name, mapping_rule_name=kusto_column_mapping_name, data_format="csv"))
poller.wait()
```
|**Inställning** | **Fältbeskrivning**|
|---|---|---|
| tenant_id | Ditt klient-ID. Det är också känt som ett katalog-ID.|
| subscription_id | Prenumerations-ID som du använder för att skapa resurser.|
| client_id | Klient-ID för programmet som kan komma åt resurser i din klient.|
| client_secret | Klienthemligheten för programmet som kan komma åt resurser i din klientorganisation. |

## <a name="test-the-code-example"></a>Testa kodexemplet

1. Ladda upp en fil till lagringskontot.

    ```python
    account_key = "xxxxxxxxxxxxxx"
    block_blob_service = BlockBlobService(account_name=storage_account_name, account_key=account_key)
    blob_name = "test.csv"
    blob_content = """2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm"""
    block_blob_service.create_blob_from_text(container_name=storage_container_name, blob_name=blob_name, text=blob_content)
    ```
    |**Inställning** | **Fältbeskrivning**|
    |---|---|---|
    | account_key | Åtkomstnyckeln för det programmatiskt skapade lagringskontot.|

2. Kör en testfråga i Azure Data Explorer.

    ```python
    kusto_uri = "https://{}.{}.kusto.windows.net".format(kusto_cluster_name, location_small_case)
    kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)
    kusto_client = KustoClient(kusto_connection_string_builder)
    query = "{} | take 10".format(kusto_table_name)
    response = kusto_client.execute_query(kusto_database_name, query)
    print(response.primary_results[0].rows_count)
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort resursgruppen och rensa resurser använder du följande kommando:

```python
#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = resource_client.resource_groups.delete(resource_group_name=resource_group_name)
poller.wait()
```

## <a name="next-steps"></a>Nästa steg

*  Mer information om andra sätt att skapa ett kluster och en databas finns i [Skapa ett Azure Data Explorer-kluster och -databas](create-cluster-database-python.md).
* Mer information om inmatningsmetoder finns [i Azure Data Explorer datainmatning](ingest-data-overview.md).
* Mer information om webbprogrammet finns [i Snabbstart: Frågedata i webbgränssnittet i Azure Data Explorer](web-query-data.md).
* [Skriv frågor](write-queries.md) med Kusto Query Language.