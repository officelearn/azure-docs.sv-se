---
title: Säker WebHook-leverans med Azure AD i Azure Event Grid
description: Beskriver hur du levererar händelser till HTTPS-slutpunkter som skyddas av Azure Active Directory med Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931541"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publicera händelser till Azure Active Directory-skyddade slutpunkter

I den här artikeln beskrivs hur du kan dra nytta av Azure Active Directory för att skydda anslutningen mellan din händelseprenumeration och din webhook-slutpunkt. En översikt över Azure AD-program och tjänsthuvudnamn finns i [översikt över Microsoft identity platform (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

Den här artikeln använder Azure-portalen för demonstration, men funktionen kan också aktiveras med CLI, PowerShell eller SDK:erna.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Skapa ett Azure AD-program

Börja med att skapa ett Azure AD-program för din skyddade slutpunkt. Se https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurera ditt skyddade API så att det anropas av en daemonapp.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Aktivera händelserutnät för att använda ditt Azure AD-program

Använd PowerShell-skriptet nedan för att skapa en roll- och tjänstprincip i ditt Azure AD-program. Du behöver klient-ID och objekt-ID från ditt Azure AD-program:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Ändra PowerShell-skriptets $myTenantId för att använda ditt Azure AD-klient-ID.
1. Ändra PowerShell-skriptets $myAzureADApplicationObjectId för att använda objekt-ID för ditt Azure AD-program
1. Kör det ändrade skriptet.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Konfigurera händelseprenumerationen

I skapandet flödet för din händelseprenumeration väljer du slutpunktstypen "Webbkrok". När du har gett slutpunkts-URI klickar du på fliken ytterligare funktioner högst upp i bladet skapa händelseprenumerationer.

![Välj webbkrok för slutpunktstyp](./media/secure-webhook-delivery/select-webhook.png)

På fliken Ytterligare funktioner markerar du kryssrutan "Använd AAD-autentisering" och konfigurerar klient-ID och program-ID:

* Kopiera Azure AD-klient-ID från utdata av skriptet och ange det i fältet AAD-klient-ID.
* Kopiera Azure AD-program-ID från utdata av skriptet och ange det i fältet AAD-program-ID.

    ![Säker Webhook-åtgärd](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelseleveranser finns i [Övervaka leverans av händelserutnätsmeddelanden](monitor-event-delivery.md).
* Mer information om autentiseringsnyckeln finns i [säkerhet och autentisering av Händelserutnät](security-authentication.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
