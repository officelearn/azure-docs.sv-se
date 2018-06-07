---
title: Hämta värden för app - autentisering i Azure SQL Database | Microsoft Docs
description: Skapa ett huvudnamn för tjänsten för att komma åt SQL-databas från kod.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: f6d3599425669a6717a58bb1e5ac0af4b291d4d6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646345"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Hämta värden som krävs för att verifiera ett program för att komma åt SQL-databas från kod
Om du vill skapa och hantera SQL-databas från kod måste du registrera din app i Azure Active Directory (AAD)-domän i prenumerationen där resurserna i Azure har skapats.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Skapa en tjänstens huvudnamn för åtkomst av resurser från ett program
Du måste ha senast [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) installerad och körs. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

Följande PowerShell-skript skapar Active Directory-programmet (AD) och tjänstobjektet som vi behöver för att autentisera vår C#-app. Skriptet matar ut värden som vi behöver för det föregående C#-exemplet. Detaljerad information finns i [Skapa ett tjänstobjekt med Azure PowerShell för att komma åt resurser](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Connect-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Se också
* [Skapa en SQL-databas med C#](sql-database-get-started-csharp.md)
* [Ansluter till SQL-databas med hjälp av Azure Active Directory-autentisering](sql-database-aad-authentication.md)

