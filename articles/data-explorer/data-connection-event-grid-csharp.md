---
title: 'Skapa en händelserutnätsdataanslutning för Azure Data Explorer med hjälp av C #'
description: I den här artikeln får du lära dig hur du skapar en Event Grid-dataanslutning för Azure Data Explorer med C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255085"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Skapa en händelserutnätsdataanslutning för Azure Data Explorer med hjälp av C #

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-mall](data-connection-event-grid-resource-manager.md)


Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer erbjuder inmatning (datainläsning) från eventhubbar, IoT-hubbar och blobbar skrivna till blob-behållare. I den här artikeln skapar du en Event Grid-dataanslutning för Azure Data Explorer med C#.

## <a name="prerequisites"></a>Krav

* Om du inte har Installerat Visual Studio 2019 kan du ladda ned och använda den **kostnadsfria** [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.
* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Skapa [ett kluster och en databas](create-cluster-database-csharp.md)
* Skapa [tabell- och kolumnmappning](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Ange [databas- och tabellprinciper](database-table-policies-csharp.md) (valfritt)
* Skapa ett [lagringskonto med en Event Grid-prenumeration](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Lägga till en dataanslutning för händelserutnät

I följande exempel visas hur du lägger till en dataanslutning för händelserutnät programmässigt. Se [skapa en händelserutnätsdataanslutning i Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) för att lägga till en event grid-dataanslutning med Azure-portalen.

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
| tenantId (hyresgäst) | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Kallas även katalog-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Prenumerations-ID som du använder för att skapa resurser.|
| ClientID | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som kan komma åt resurser i din klient.|
| clientSecret (klientSecret) | *xxxxxxxxxxxxxx* | Klienthemligheten för programmet som kan komma åt resurser i din klientorganisation. |
| resourceGroupName | *testrg* | Namnet på resursgruppen som innehåller klustret.|
| clusterName | *mykustocluster (en)* | Namnet på klustret.|
| Databasename | *mykustodatabas* | Namnet på måldatabasen i klustret.|
| dataConnectionName | *myeventhubconnect* | Önskat namn på din dataanslutning.|
| tableName | *StormEvents* | Namnet på måltabellen i måldatabasen.|
| mappningRuleNamn | *StormEvents_CSV_Mapping* | Namnet på kolumnmappningen som är relaterad till måltabellen.|
| dataFormat | *Csv* | Meddelandets dataformat.|
| eventHubResourceId | *Resurs-ID* | Resurs-ID:t för händelsehubben där händelserutnätet är konfigurerat för att skicka händelser. |
| lagringKontoResourceId | *Resurs-ID* | Resurs-ID:t för ditt lagringskonto som innehåller data för inmatning. |
| consumerGroup (konsumentGrupp) | *$Default* | Konsumentgruppen för din Event Hub.|
| location | *USA, centrala* | Platsen för dataanslutningsresursen.|

## <a name="generate-sample-data"></a>Generera exempeldata

Nu när Azure Data Explorer och lagringskontot är anslutna kan du skapa exempeldata och överföra dem till blob-lagringen.

Skriptet skapar en ny behållare i ditt lagringskonto, överför en befintlig fil (som en blob) till den behållaren och visar sedan blobbar i behållaren.

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
> Azure Data Explorer tar inte bort blobbar efter inmatning. Behåll blobbar i tre till fem dagar med hjälp av [Azure Blob storage lifecycle](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) för att hantera blob-borttagning.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
