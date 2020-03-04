---
title: Skapa en Event Grid data anslutning för Azure Datautforskaren genom att användaC#
description: I den här artikeln får du lära dig hur du skapar en Event Grid data anslutning för Azure Datautforskaren C#genom att använda.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255085"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Skapa en Event Grid data anslutning för Azure Datautforskaren genom att användaC#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-mall](data-connection-event-grid-resource-manager.md)


Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Datautforskaren erbjuder inmatning (data inläsning) från Event Hubs, IoT-hubbar och blobbar skrivna till BLOB-behållare. I den här artikeln skapar du en Event Grid data anslutning för Azure Datautforskaren med hjälp C#av.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Skapa [ett kluster och en databas](create-cluster-database-csharp.md)
* Skapa [tabell-och kolumn mappning](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Ange [databas-och tabell principer](database-table-policies-csharp.md) (valfritt)
* Skapa ett [lagrings konto med en Event Grid prenumeration](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Lägg till en Event Grid data anslutning

I följande exempel visas hur du lägger till en Event Grid data anslutning program mässigt. Se [skapa en Event Grid data anslutning i Azure datautforskaren](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) för att lägga till en Event Grid data anslutning med hjälp av Azure Portal.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Även känt som katalog-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Det prenumerations-ID som du använder för att skapa resurser.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som har åtkomst till resurser i din klient organisation.|
| clientSecret | *xxxxxxxxxxxxxx* | Klient hemligheten för programmet som har åtkomst till resurser i din klient organisation. |
| resourceGroupName | *testrg* | Namnet på resurs gruppen som innehåller klustret.|
| clusterName | *mykustocluster* | Namnet på klustret.|
| Databas | *mykustodatabase* | Namnet på mål databasen i klustret.|
| dataConnectionName | *myeventhubconnect* | Det önskade namnet för din data anslutning.|
| tableName | *StormEvents* | Namnet på mål tabellen i mål databasen.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Namnet på kolumn mappningen som är relaterad till mål tabellen.|
| dataFormat | *SKV* | Meddelandets data format.|
| eventHubResourceId | *Resurs-ID* | Resurs-ID för Händelsehubben där Event Grid har kon figurer ATS för att skicka händelser. |
| storageAccountResourceId | *Resurs-ID* | Resurs-ID för ditt lagrings konto som innehåller data för inmatning. |
| consumerGroup | *$Default* | Konsument gruppen för Händelsehubben.|
| location | *USA, centrala* | Platsen för data anslutnings resursen.|

## <a name="generate-sample-data"></a>Generera exempeldata

Nu när Azure Datautforskaren och lagrings kontot är anslutna kan du skapa exempel data och ladda upp det till blob-lagringen.

Det här skriptet skapar en ny behållare i ditt lagrings konto, laddar upp en befintlig fil (som en BLOB) till behållaren och listar sedan blobarna i behållaren.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Azure Datautforskaren tar inte bort blobar efter inmatning. Behåll Blobbarna i tre till fem dagar med [Azure Blob Storage-livscykeln](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) för att hantera BLOB-borttagning.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
