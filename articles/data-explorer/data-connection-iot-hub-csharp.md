---
title: Skapa en IoT Hub data anslutning för Azure Datautforskaren genom att användaC#
description: I den här artikeln får du lära dig hur du skapar en IoT Hub data anslutning för Azure Datautforskaren C#genom att använda.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: bcc80000be5e061a37601f05a2a245aac031fc15
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031661"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Skapa en IoT Hub data anslutning för Azure Datautforskaren med C# (för hands version)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Datautforskaren erbjuder inmatning (data inläsning) från Event Hubs, IoT-hubbar och blobbar skrivna till BLOB-behållare. I den här artikeln skapar du en IoT Hub data anslutning för Azure Datautforskaren med hjälp C#av.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

* Skapa [ett kluster och en databas](create-cluster-database-csharp.md)

* Skapa [tabell-och kolumn mappning](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* Ange [databas-och tabell principer](database-table-policies-csharp.md) (valfritt)

* Skapa en [IoT-hubb med en princip för delad åtkomst konfigurerad](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Lägg till en IoT Hub data anslutning 

I följande exempel visas hur du lägger till en IoT Hub data anslutning program mässigt. Se [ansluta Azure datautforskaren-tabellen till IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) för att lägga till en data anslutning för IoT Hub med hjälp av Azure Portal.

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
| TenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Även känt som katalog-ID.|
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
| iotHubResourceId | *Resurs-ID* | Resurs-ID för din IoT-hubb som innehåller data för inmatning. |
| sharedAccessPolicyName | *iothubforread* | Namnet på den delade åtkomst principen som definierar behörigheterna för enheter och tjänster som ska anslutas till IoT Hub. |
| consumerGroup | *$Default* | Konsument gruppen för händelsehubben.|
| location | *USA, centrala* | Platsen för data anslutnings resursen.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
