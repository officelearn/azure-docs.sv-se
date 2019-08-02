---
title: Skapa en anpassad roll definition i rollbaserad åtkomst kontroll i Azure AD-Azure Active Directory | Microsoft Docs
description: Skapa anpassade Azure AD-roller med resurs omfång på Azure Active Directory resurser.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae773bc1a4e1831dbe462149bb827c26b7e74b96
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722244"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>Skapa en anpassad roll och tilldela en resurs omfattning i Azure Active Directory

Den här artikeln beskriver hur du skapar nya anpassade roller i Azure Active Directory (Azure AD). Du kan skapa anpassade roller på fliken **roller och administratörer** på översikts sidan för Azure AD eller [sidan program registrering](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview). Rollen kan tilldelas antingen på katalog nivåns omfång eller begränsas till enbart app-registreringar.

Mer information finns i [Översikt över anpassade roller](roles-custom-overview.md) för att grunderna för anpassade roller.

## <a name="using-the-azure-ad-portal"></a>Använda Azure AD-portalen

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Skapa en ny anpassad roll för att ge åtkomst till hantering av app-registreringar

1. Logga in på [administrations centret](https://aad.portal.azure.com) för Azure AD med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.
1. Välj **Azure Active Directory** > **roller och administratörer** > **ny anpassad roll**.

   ![Skapa eller redigera roller från sidan roller och administratörer](./media/roles-create-custom/new-custom-role.png)

1. Ange ett namn och en beskrivning för rollen på fliken **grundläggande** .

   ![Ange ett namn och en beskrivning för en anpassad roll på fliken grundläggande](./media/roles-create-custom/basics-tab.png)

1. För att välja de behörigheter som krävs för att hantera autentiseringsuppgifter för appens registrering och grundläggande egenskaper, till exempel namn:
   1. Ange "autentiseringsuppgifter" i Sök fältet och välj `microsoft.directory/applications/credentials/update` behörigheten.

      ![Välj behörigheter för en anpassad roll på fliken behörigheter](./media/roles-create-custom/permissions-tab.png)

   1. Ange "grundläggande" i Sök fältet, Välj `microsoft.directory/applications/basic/update` behörigheten och klicka sedan på **Nästa**.
1. På fliken **Granska + skapa** granskar du behörigheterna och väljer **skapa**.

Den anpassade rollen visas i listan över tillgängliga roller att tilldela.

## <a name="assign-a-role-scoped-to-a-resource"></a>Tilldela en roll som är begränsad till en resurs

Precis som inbyggda roller kan anpassade roller tilldelas till standard organisations omfång för att bevilja åtkomst över alla app-registreringar. Men anpassade roller kan också tilldelas i objekt omfånget. På så sätt kan du ge den tilldelas behörighet att uppdatera autentiseringsuppgifter och grundläggande egenskaper för en enskild app utan att behöva skapa en andra anpassad roll.

1. Logga in på [Azure AD administrations Center](https://aad.portal.azure.com) med programutvecklare behörigheter i Azure AD-organisationen.
1. Välj **Appregistreringar**.
1. Välj den app-registrering som du beviljar åtkomst för att hantera. Du kanske måste välja **alla program** om du vill se en fullständig lista över app-registreringar i din Azure AD-organisation.

    ![Välj app-registrering som ett resurs omfång för en roll tilldelning](./media/roles-create-custom/appreg-all-apps.png)

1. I appens registrering väljer du **roller och administratörer**. Om du inte redan har skapat ett följer du anvisningarna i [föregående procedur](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

    Om du tilldelar den här rollen från kontexten för en öppen app-registrering, har den tilldelade som har behörighet till den enda registrerade appen. Rollen som du tilldelar visas i listan på varje app-registrering. Den här åtkomst modellen, där en ägare kan tilldelas behörighet till särskilda Azure AD-resurser av roller, liknar den modell som används i [Azure RBAC](../../role-based-access-control/overview.md) för Azure Resource Access Control.

1. Välj rollen för att öppna **tilldelnings** sidan.
1. Välj **Lägg till tilldelning** för att lägga till en användare. Användaren beviljas inga behörigheter för någon annan app-registrering än den som valts.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Skapa en anpassad roll med hjälp av Azure AD PowerShell

### <a name="prepare-powershell"></a>Förbered PowerShell

Först måste du [Ladda ned Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/).

Använd följande kommandon för att installera Azure AD PowerShell-modulen:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Kontrol lera att modulen är redo att användas genom att använda följande kommando:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Skapa den anpassade rollen

Skapa en ny roll med hjälp av följande PowerShell-skript:

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Skapa en anpassad roll med hjälp av Microsoft Graph API

1. Skapa roll definitionen.

    HTTP-begäran om att skapa en anpassad roll definition.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
    {
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "allowedResourceActions": 
            [
                "microsoft.directory/applications/basic/read",
                "microsoft.directory/applications/credentials/update"
            ]
        }
    ]
    }
    ```

1. Skapa roll tilldelningen.

    HTTP-begäran om att skapa en anpassad roll definition.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
    ```

    Body

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScope":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>Nästa steg

- Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Mer information om roller och tilldelning av administratörs roller finns i [tilldela administratörs roller](directory-assign-admin-roles.md).
- För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md).
