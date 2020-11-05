---
title: Tilldela anpassade roller med Azure PowerShell-Azure AD | Microsoft Docs
description: Hantera medlemmar i en anpassad administratörs roll för Azure AD med Azure PowerShell.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cd9148f1084727141f725d76f7ae756f44e210e
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379527"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Tilldela anpassade roller med resurs omfång med PowerShell i Azure Active Directory

Den här artikeln beskriver hur du skapar en roll tilldelning i en företagsomfattande omfattning i Azure Active Directory (Azure AD). Genom att tilldela en roll i hela organisationen beviljar du åtkomst i Azure AD-organisationen. Information om hur du skapar en roll tilldelning med ett omfång för en enda Azure AD-resurs finns i [så här skapar du en anpassad roll och tilldelar den vid resurs omfånget](custom-create.md). Den här artikeln använder modulen [Azure Active Directory PowerShell version 2](/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

Mer information om administratörs roller i Azure AD finns [i tilldela administratörs roller i Azure Active Directory](permissions-reference.md).

## <a name="required-permissions"></a>Behörigheter som krävs

Anslut till din Azure AD-organisation med ett globalt administratörs konto för att tilldela eller ta bort roller.

## <a name="prepare-powershell"></a>Förbered PowerShell

Installera Azure AD PowerShell-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importera sedan för hands versionen av Azure AD PowerShell med följande kommando:

``` PowerShell
import-module azureadpreview
```

Kontrol lera att modulen är redo att användas genom att matcha den version som returneras av följande kommando till den som visas här:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Nu kan du börja använda cmdlet: arna i modulen. En fullständig beskrivning av cmdletarna i Azure AD-modulen finns i referens dokumentationen för [Azure AD Preview-modulen](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Tilldela en roll till en användare eller tjänstens huvud namn med resurs omfånget

1. Öppna PowerShell-modulen för för hands versionen av Azure AD.
1. Logga in genom att köra kommandot `Connect-AzureAD` .
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

Om du vill tilldela rollen till ett huvud namn för tjänsten i stället för en användare använder du [cmdleten Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Åtgärder på roll definitions

Roll definitions objekt innehåller definitionen av den inbyggda eller anpassade rollen, tillsammans med de behörigheter som har beviljats av roll tilldelningen. Den här resursen visar både anpassade roll definitioner och inbyggda directoryRoles (som visas i roll definitions motsvarande form). I dag kan en Azure AD-organisation ha högst 30 unika anpassade RoleDefinitions definierade.

### <a name="create-operations-on-roledefinition"></a>Skapa åtgärder på roll definitions

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

### <a name="read-operations-on-roledefinition"></a>Läs åtgärder på roll definitions

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Uppdatera åtgärder på roll definitions

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Ta bort åtgärder på roll definitions

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Åtgärder på RoleAssignment

Roll tilldelningar innehåller information som länkar ett angivet säkerhets objekt (en användare eller ett program tjänst objekt) till en roll definition. Om det behövs kan du lägga till en omfattning för en enda Azure AD-resurs för de tilldelade behörigheterna.  Begränsning av behörighets omfånget stöds för inbyggda och anpassade roller.

### <a name="create-operations-on-roleassignment"></a>Skapa åtgärder på RoleAssignment

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

### <a name="read-operations-on-roleassignment"></a>Läs åtgärder på RoleAssignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Ta bort åtgärder på RoleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Nästa steg

- Dela med oss i [forum för administrativa roller i Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Mer information om roller och roll tilldelningar för Azure AD-administratörer finns i [tilldela administratörs roller](permissions-reference.md)
- För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md)