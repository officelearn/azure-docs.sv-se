---
title: 'Lägga till klusterobjektnamn för Azure Data Explorer med hjälp av C #'
description: I den här artikeln får du lära dig hur du lägger till klusterobjektnamn för Azure Data Explorer med C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e6c3970890dfe2c669dee1acf631e9dd45ab1085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965065"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>Lägga till klusterobjektnamn för Azure Data Explorer med hjälp av C #

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager-mall](cluster-principal-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. I den här artikeln lägger du till klusterobjektnamn för Azure Data Explorer med C#.

## <a name="prerequisites"></a>Krav

* Om du inte har Installerat Visual Studio 2019 kan du ladda ned och använda den **kostnadsfria** [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.
* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Skapa ett kluster](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Installera C# NuGet

* Installera [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installera [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) för autentisering.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Lägga till ett klusterhuvudnamn

I följande exempel visas hur du lägger till ett klusterhuvudnamn programmässigt.

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
//The cluster that is created as part of the Prerequisites
var clusterName = "mykustocluster";
string principalAssignmentName = "clusterPrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "AllDatabasesAdmin";//AllDatabasesAdmin or AllDatabasesViewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var clusterPrincipalAssignment = new ClusterPrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.ClusterPrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, principalAssignmentName, clusterPrincipalAssignment);
```

|**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
|---|---|---|
| tenantId (hyresgäst) | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ditt klient-ID. Kallas även katalog-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Prenumerations-ID som du använder för att skapa resurser.|
| ClientID | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för programmet som kan komma åt resurser i din klient.|
| clientSecret (klientSecret) | *xxxxxxxxxxxxxx* | Klienthemligheten för programmet som kan komma åt resurser i din klientorganisation. |
| resourceGroupName | *testrg* | Namnet på resursgruppen som innehåller klustret.|
| clusterName | *mykustocluster (en)* | Namnet på klustret.|
| principalAssignmentName | *klusterPrincipAtilldelning1* | Namnet på klustrets huvudresurs.|
| principalId (huvudsakligaId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Huvud-ID: t, som kan vara användar-E-post, program-ID eller säkerhetsgruppnamn.|
| roll | *AllDatabasesAdmin* | Rollen för ditt klusterhuvudnamn, som kan vara "AllDatabasesAdmin" eller "AllDatabasesViewer".|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Klient-ID för huvudmannen.|
| principalType (principalType) | *App* | Typen av huvudnamn, som kan vara "Användare", "App" eller "Grupp"|

## <a name="next-steps"></a>Nästa steg

* [Lägga till databashuvudnamn](database-principal-csharp.md)
