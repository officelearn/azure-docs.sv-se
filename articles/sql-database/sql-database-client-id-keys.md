---
title: Hämta värden för appautentisering – Azure SQL Database | Microsoft Docs
description: Skapa ett huvudnamn för tjänsten för att komma åt SQL-databas från kod.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: 6c4c001fc538e5ad93a5c4fc3d6405209be7fc53
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309374"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Hämta värdena som krävs för att autentisera ett program för att få åtkomst till SQL-databas från kod

Du måste registrera din app i Azure Active Directory (AAD)-domän i prenumerationen där dina Azure-resurser har tagits fram för att skapa och hantera SQL-databas från kod.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Skapa tjänstens huvudnamn för åtkomst till resurser från ett program

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du måste ha senast [Azure PowerShell](/powershell/azure) installerade och körs. Detaljerad information finns i [så här installerar du Azure PowerShell](/powershell/azure/install-az-ps).

Följande PowerShell-skript skapar Active Directory-programmet (AD) och tjänstobjektet som vi behöver för att autentisera vår C#-app. Skriptet matar ut värden som vi behöver för det föregående C#-exemplet. Detaljerad information finns i [Skapa ett tjänstobjekt med Azure PowerShell för att komma åt resurser](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

    # Sign in to Azure.
    Connect-AzAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Se också
* [Skapa en SQL-databas med C#](sql-database-get-started-csharp.md)
* [Ansluta till SQL Database med hjälp av Azure Active Directory-autentisering](sql-database-aad-authentication.md)

