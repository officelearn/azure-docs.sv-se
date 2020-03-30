---
title: Tilldela anpassade roller med Azure PowerShell - Azure AD | Microsoft-dokument
description: Hantera medlemmar i en anpassad Azure AD-administratörsroll med Azure PowerShell.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025321"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Tilldela anpassade roller med resursomfattning med PowerShell i Azure Active Directory

I den här artikeln beskrivs hur du skapar en rolltilldelning i organisationsomfattande omfång i Azure Active Directory (Azure AD). Genom att tilldela en roll i organisationsomfattande omfattning får du åtkomst i Azure AD-organisationen. Information om hur du skapar en rolltilldelning med ett scope för en enda Azure AD-resurs finns i [Så här skapar du en anpassad roll och tilldelar den i resursomfattning](roles-create-custom.md). Den här artikeln använder [Azure Active Directory PowerShell Version 2-modulen.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)

Mer information om Azure AD-administratörsroller finns [i Tilldela administratörsroller i Azure Active Directory](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Nödvändiga behörigheter

Anslut till din Azure AD-klient med ett globalt administratörskonto för att tilldela eller ta bort roller.

## <a name="prepare-powershell"></a>Förbered PowerShell

Installera Azure AD PowerShell-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importera sedan förhandsversionen av Azure AD PowerShell med följande kommando:

``` PowerShell
import-module azureadpreview
```

Om du vill kontrollera att modulen är klar att användas matchar du den version som returneras av följande kommando till det som anges här:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Nu kan du börja använda cmdlets i modulen. En fullständig beskrivning av cmdlets i Azure AD-modulen finns i referensdokumentationen för [Azure AD.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Tilldela en roll till en användare eller tjänsthuvudnamn med resursomfattning

1. Öppna Azure AD-förhandsversionen av PowerShell-modulen.
1. Logga in genom att `Connect-AzureAD`köra kommandot .
1. Skapa en ny roll med hjälp av följande PowerShell-skript.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Om du vill tilldela rollen till ett tjänsthuvudnamn i stället för en användare använder du [cmdleten Get-AzureADMSServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Åtgärder för rolldefinition

Rolldefinitionsobjekt innehåller definitionen av den inbyggda eller anpassade rollen, tillsammans med de behörigheter som beviljas av rolltilldelningen. Den här resursen visar både anpassade rolldefinitioner och inbyggda katalogroller (som visas i motsvarande rolldefinitionsformulär). Idag kan en Azure AD-organisation ha högst 30 unika anpassade RoleDefinitions definierade.

### <a name="create-operations-on-roledefinition"></a>Skapa åtgärder på RollDefinition

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Läs åtgärder på RoleDefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Uppdatera åtgärder på RollDefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Ta bort åtgärder på RoleDefinition

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Åtgärder för rolltilldelning

Rolltilldelningar innehåller information som kopplar ett visst säkerhetsobjekt (ett användar- eller programtjänsthuvudnamn) till en rolldefinition. Om det behövs kan du lägga till ett scope för en enda Azure AD-resurs för de tilldelade behörigheterna.  Att begränsa behörigheternas omfattning stöds för inbyggda och anpassade roller.

### <a name="create-operations-on-roleassignment"></a>Skapa åtgärder på rolltilldelning

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-operations-on-roleassignment"></a>Läs åtgärder på rolltilldelning

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Ta bort åtgärder på rolltilldelning

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Nästa steg

- Dela med oss på Forumet för [administrativa azure ad-roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Mer information om roller och azure AD-administratörsrolltilldelningar finns i [Tilldela administratörsroller](directory-assign-admin-roles.md).
- Standardanvändarbehörigheter finns i en [jämförelse av standardbehörighet för gäst- och medlemsanvändare](../fundamentals/users-default-permissions.md).
