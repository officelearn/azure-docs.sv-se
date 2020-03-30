---
title: 'Lägga till databasobjekt för Azure Data Explorer med hjälp av C #'
description: I den här artikeln får du lära dig hur du lägger till databashuvudnamn för Azure Data Explorer med C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965039"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Lägga till databasobjekt för Azure Data Explorer med hjälp av C #

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager-mall](database-principal-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. I den här artikeln lägger du till databasobjekt för Azure Data Explorer med C#.

## <a name="prerequisites"></a>Krav

* Om du inte har Installerat Visual Studio 2019 kan du ladda ned och använda den **kostnadsfria** [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.
* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Skapa ett kluster och en databas](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Installera C# NuGet

* Installera [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installera [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) för autentisering.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Lägga till ett huvudnamn för databasen

I följande exempel visas hur du lägger till en huvudnamn för en databas programmässigt.

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
| tenantId (hyresgäst) | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Kallas även katalog-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Prenumerations-ID som du använder för att skapa resurser.|
| ClientID | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som kan komma åt resurser i din klient.|
| clientSecret (klientSecret) | *xxxxxxxxxxxxxx* | Klienthemligheten för programmet som kan komma åt resurser i din klientorganisation. |
| resourceGroupName | *testrg* | Namnet på resursgruppen som innehåller klustret.|
| clusterName | *mykustocluster (en)* | Namnet på klustret.|
| Databasename | *mykustodatabas* | Namn på databasen.|
| principalAssignmentName | *databasPrincipatilldelning1* | Namnet på databasens huvudresurs.|
| principalId (huvudsakligaId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Huvud-ID: t, som kan vara användar-E-post, program-ID eller säkerhetsgruppnamn.|
| roll | *Administratör* | Rollen för din databas huvudnamn, som kan vara "Admin", "Ingestor", "Monitor", "Användare", "ObegränsadViewers", "Viewer".|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för huvudmannen.|
| principalType (principalType) | *App* | Typen av huvudnamn, som kan vara "Användare", "App" eller "Grupp"|

## <a name="next-steps"></a>Nästa steg

* [Mata in data med hjälp av Node-biblioteket i Azure Data Explorer](node-ingest-data.md)
