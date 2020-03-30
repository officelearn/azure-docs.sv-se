---
title: Ta bort gränser för att skapa appregistreringar – Azure AD | Microsoft-dokument
description: Tilldela en anpassad roll för att bevilja obegränsade appregistreringar i Azure AD Active Directory
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
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559053"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Snabbstart: Bevilja behörighet att skapa obegränsade appregistreringar

I den här snabbstarten skapar du en anpassad roll med behörighet att skapa ett obegränsat antal appregistreringar och sedan tilldela den rollen till en användare. Den tilldelade användaren kan sedan använda Azure AD-portalen, Azure AD PowerShell eller Microsoft Graph API för att skapa programregistreringar. Till skillnad från den inbyggda rollen Programutvecklare ger den här anpassade rollen möjlighet att skapa ett obegränsat antal programregistreringar. Rollen Programutvecklare ger möjligheten, men det totala antalet skapade objekt är begränsat till 250 för att förhindra att [objektkvoten](directory-service-limits-restrictions.md)för hela katalogen påverkas .

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisite"></a>Krav

Den minst privilegierade roll som krävs för att skapa och tilldela azure AD-anpassade roller är administratören för privilegierad roll.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Skapa en ny anpassad roll med Azure AD-portalen

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med administratör för privilegierad roll eller globala administratörsbehörigheter i Azure AD-organisationen.
1. Välj **Azure Active Directory**, välj Roller och **administratörer**och välj sedan Ny **anpassad roll**.

    ![Skapa eller redigera roller från sidan Roller och administratörer](./media/roles-create-custom/new-custom-role.png)

1. På fliken **Grunderna** anger du "Application Registration Creator" för namnet på rollen och "Kan skapa ett obegränsat antal programregistreringar" för rollbeskrivningen och välj sedan **Nästa**.

    ![ange ett namn och en beskrivning för en anpassad roll på fliken Grunderna](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. På fliken **Behörigheter** anger du "microsoft.directory/applications/create" i sökrutan och markerar sedan kryssrutorna bredvid önskade behörigheter och väljer sedan **Nästa**.

    ![Välj behörigheter för en anpassad roll på fliken Behörigheter](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. På fliken **Granska + skapa** granskar du behörigheterna och väljer **Skapa**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Tilldela rollen till en användare med Azure AD-portalen

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med privilegierad rolladministratör eller globala administratörsbehörigheter i din Azure AD-organisation.
1. Välj **Azure Active Directory** och välj sedan Roller och **administratörer**.
1. Välj rollen Skapare av programregistrering och välj **Lägg till tilldelning**.
1. Markera önskad användare och klicka på **Välj** om du vill lägga till användaren i rollen.

Klart! I den här snabbstarten har du skapat en anpassad roll med behörighet att skapa ett obegränsat antal appregistreringar och sedan tilldela den rollen till en användare.

> [!TIP]
> Om du vill tilldela rollen till ett program med Azure AD-portalen anger du namnet på programmet i sökrutan på tilldelningssidan. Program visas inte som standard i listan, men returneras i sökresultaten.

### <a name="app-registration-permissions"></a>Behörigheter för appregistrering

Det finns två behörigheter tillgängliga för att bevilja möjligheten att skapa programregistreringar, var och en med olika beteende.

- microsoft.directory/applications/createAsOwner: Tilldela den här behörigheten resulterar i att skaparen läggs till som den första ägaren av den skapade appregistreringen och den skapade appregistreringen räknas mot skaparens 250 skapade objektkvot.
- microsoft.directory/applicationPolicies/create: Tilldela den här behörigheten resulterar i att skaparen inte läggs till som den första ägaren av den skapade appregistreringen och den skapade appregistreringen räknas inte mot skaparens 250 skapade objektkvot. Använd den här behörigheten noggrant eftersom det inte finns något som hindrar förvärvaren från att skapa appregistreringar förrän kvoten på katalognivå har nåtts. Om båda behörigheterna har tilldelats har den här behörigheten företräde.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Skapa en anpassad roll med Azure AD PowerShell

Skapa en ny roll med följande PowerShell-skript:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Tilldela den anpassade rollen med Azure AD PowerShell

#### <a name="prepare-powershell"></a>Förbered PowerShell

Installera först Azure AD PowerShell-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importera sedan förhandsversionen av Azure AD PowerShell med följande kommando:

```powershell
import-module azureadpreview
```

Om du vill kontrollera att modulen är klar att användas matchar du den version som returneras av följande kommando till det som anges här:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Tilldela den anpassade rollen

Tilldela rollen med hjälp av powershell-skriptet nedan:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Skapa en anpassad roll med Microsoft Graph API

HTTP-begäran om att skapa den anpassade rollen.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Innehåll

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Tilldela den anpassade rollen med Microsoft Graph API

Rolltilldelningen kombinerar ett huvud-ID för säkerhet (som kan vara ett användar- eller tjänsthuvudnamn), ett rolldefinitions-ID och ett Azure AD-resursomfattning.

HTTP-begäran om att tilldela en anpassad roll.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Innehåll

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Nästa steg

- Känn dig välkommen att dela med oss på [Azure AD administrativa roller forum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Mer information om roller och administratörsrolltilldelning finns i [Tilldela administratörsroller](directory-assign-admin-roles.md).
- Standardanvändarbehörigheter finns i en [jämförelse av standardbehörighet för gäst- och medlemsanvändare](../fundamentals/users-default-permissions.md).
