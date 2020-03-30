---
title: 'Skapa en IoT Hub-dataanslutning för Azure Data Explorer med hjälp av C #'
description: I den här artikeln får du lära dig hur du skapar en IoT Hub-dataanslutning för Azure Data Explorer med C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667388"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Skapa en IoT Hub-dataanslutning för Azure Data Explorer med C# (förhandsversion)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-mall](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer erbjuder inmatning (datainläsning) från eventhubbar, IoT-hubbar och blobbar skrivna till blob-behållare. I den här artikeln skapar du en IoT Hub-dataanslutning för Azure Data Explorer med C#.

## <a name="prerequisites"></a>Krav

* Om du inte har Installerat Visual Studio 2019 kan du ladda ned och använda den **kostnadsfria** [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.
* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Skapa [ett kluster och en databas](create-cluster-database-csharp.md)
* Skapa [tabell- och kolumnmappning](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Ange [databas- och tabellprinciper](database-table-policies-csharp.md) (valfritt)
* Skapa en [IoT-hubb med en princip för delad åtkomst konfigurerad](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Lägga till en IoT Hub-dataanslutning 

I följande exempel visas hur du lägger till en IoT Hub-dataanslutning programmässigt. Se [ansluta Azure Data Explorer-tabellen till IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) för att lägga till en Iot Hub-dataanslutning med Azure-portalen.

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
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
| iotHubResourceId | *Resurs-ID* | Resurs-ID:et för IoT-hubben som innehåller data för inmatning. |
| sharedAccessPolicyName | *iothubforread* | Namnet på principen för delad åtkomst som definierar behörigheter för enheter och tjänster att ansluta till IoT Hub. |
| consumerGroup (konsumentGrupp) | *$Default* | Konsumentgruppen för din händelsehubb.|
| location | *USA, centrala* | Platsen för dataanslutningsresursen.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
