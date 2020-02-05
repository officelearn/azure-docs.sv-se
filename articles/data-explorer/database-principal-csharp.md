---
title: Lägg till databasens huvud namn för Azure Datautforskaren med hjälp avC#
description: I den här artikeln får du lära dig hur du lägger till databasens huvud namn för C#Azure datautforskaren med hjälp av.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965039"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Lägg till databasens huvud namn för Azure Datautforskaren med hjälp avC#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager-mall](database-principal-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. I den här artikeln lägger du till databas huvud namn för Azure Datautforskaren med C#hjälp av.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Skapa ett kluster och en databas](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Installera C# NuGet

* Installera [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installera [Microsoft. rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) för autentisering.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Lägg till ett databas huvud namn

I följande exempel visas hur du lägger till ett databas huvud objekt program mässigt.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Även känt som katalog-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Det prenumerations-ID som du använder för att skapa resurser.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som har åtkomst till resurser i din klient organisation.|
| clientSecret | *xxxxxxxxxxxxxx* | Klient hemligheten för programmet som har åtkomst till resurser i din klient organisation. |
| resourceGroupName | *testrg* | Namnet på resurs gruppen som innehåller klustret.|
| clusterName | *mykustocluster* | Namnet på klustret.|
| Databas | *mykustodatabase* | Namn på databasen.|
| principalAssignmentName | *databasePrincipalAssignment1* | Namnet på din databas huvud resurs.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ägar-ID: t, som kan vara användarens e-post, program-ID eller namn på säkerhets grupp.|
| roll | *Innehavaradministration* | Rollen som databasens säkerhets objekt, som kan vara admin, "inmatnings", "Monitor", "User", "UnrestrictedViewers", "Viewer".|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Innehavarens ID för huvud kontot.|
| principalType | *App* | Typ av huvud konto, som kan vara User, app eller Group|

## <a name="next-steps"></a>Nästa steg

* [Mata in data med hjälp av Azure Datautforskaren Node-biblioteket](node-ingest-data.md)
