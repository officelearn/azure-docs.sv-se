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
ms.date: 04/01/2018
ms.openlocfilehash: f9b66a430e1ca5b24853a5ff31ce13393b12b0c5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063803"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Hämta värdena som krävs för att autentisera ett program för att få åtkomst till SQL-databas från kod
Du måste registrera din app i Azure Active Directory (AAD)-domän i prenumerationen där dina Azure-resurser har tagits fram för att skapa och hantera SQL-databas från kod.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Skapa tjänstens huvudnamn för åtkomst till resurser från ett program
Du måste ha senast [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) installerade och körs. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

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
* [Ansluta till SQL Database med hjälp av Azure Active Directory-autentisering](sql-database-aad-authentication.md)

