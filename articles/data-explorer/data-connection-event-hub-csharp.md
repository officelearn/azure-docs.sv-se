---
title: 'Skapa en eventhubb-dataanslutning för Azure Data Explorer med hjälp av C #'
description: I den här artikeln får du lära dig hur du skapar en Event Hub-dataanslutning för Azure Data Explorer med C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667684"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Skapa en eventhubb-dataanslutning för Azure Data Explorer med hjälp av C #

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager-mall](data-connection-event-hub-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer erbjuder inmatning (datainläsning) från eventhubbar, IoT-hubbar och blobbar skrivna till blob-behållare. I den här artikeln skapar du en Event Hub-dataanslutning för Azure Data Explorer med C#.

## <a name="prerequisites"></a>Krav

* Om du inte har Installerat Visual Studio 2019 kan du ladda ned och använda den **kostnadsfria** [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.
* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Skapa [ett kluster och en databas](create-cluster-database-csharp.md)
* Skapa [tabell- och kolumnmappning](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Ange [databas- och tabellprinciper](database-table-policies-csharp.md) (valfritt)
* Skapa en [händelsehubb med data för inmatning](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Lägga till en händelsehubb-dataanslutning

I följande exempel visas hur du lägger till en eventhub-dataanslutning programmässigt. Se [ansluta till händelsehubben](ingest-data-event-hub.md#connect-to-the-event-hub) för att lägga till en Event Hub-dataanslutning med Azure-portalen.

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
|---|---|---|
| tenantId (hyresgäst) | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Kallas även katalog-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Prenumerations-ID som du använder för att skapa resurser.|
| ClientID | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som kan komma åt resurser i din klient.|
| clientSecret (klientSecret) | *xxxxxxxxxxxxxx* | Klienthemligheten för programmet som kan komma åt resurser i din klientorganisation.|
| resourceGroupName | *testrg* | Namnet på resursgruppen som innehåller klustret.|
| clusterName | *mykustocluster (en)* | Namnet på klustret.|
| Databasename | *mykustodatabas* | Namnet på måldatabasen i klustret.|
| dataConnectionName | *myeventhubconnect* | Önskat namn på din dataanslutning.|
| tableName | *StormEvents* | Namnet på måltabellen i måldatabasen.|
| mappningRuleNamn | *StormEvents_CSV_Mapping* | Namnet på kolumnmappningen som är relaterad till måltabellen.|
| dataFormat | *Csv* | Meddelandets dataformat.|
| eventHubResourceId | *Resurs-ID* | Resurs-ID:t för händelsehubben som innehåller data för inmatning. |
| consumerGroup (konsumentGrupp) | *$Default* | Konsumentgruppen för din Event Hub.|
| location | *USA, centrala* | Platsen för dataanslutningsresursen.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
