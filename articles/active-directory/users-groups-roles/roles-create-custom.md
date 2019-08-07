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
ms.openlocfilehash: e0e33cf9fa1c4661dd71fc41cb667b0373c9e955
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774820"
---
# <a name="create-a-custom-role-and-assign-at-resource-scope-in-azure-active-directory"></a>Skapa en anpassad roll och tilldela i resurs omfånget i Azure Active Directory

Den här artikeln beskriver hur du skapar nya anpassade roller i Azure Active Directory (Azure AD). Du kan skapa anpassade roller på fliken [roller och administratörer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) på översikts sidan för Azure AD. Rollen kan tilldelas antingen på katalog nivå omfånget eller endast för en app Registration Resource-omfattning.

Mer information finns i [Översikt över anpassade roller](roles-custom-overview.md) för att grunderna för anpassade roller.

## <a name="using-the-azure-ad-portal"></a>Använda Azure AD-portalen

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Skapa en ny anpassad roll för att ge åtkomst till hantering av app-registreringar

1. Logga in på [administrations centret](https://aad.portal.azure.com) för Azure AD med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.
1. Välj **Azure Active Directory** > **roller och administratörer** > **ny anpassad roll**.

   ![Skapa eller redigera roller från sidan roller och administratörer](./media/roles-create-custom/new-custom-role.png)

1. På fliken **grundläggande** anger du ett namn och en beskrivning för rollen och klickar sedan på **Nästa**.

   ![Ange ett namn och en beskrivning för en anpassad roll på fliken grundläggande](./media/roles-create-custom/basics-tab.png)

1. På fliken **behörigheter** väljer du de behörigheter som krävs för att hantera grundläggande egenskaper och egenskaper för autentiseringsuppgifter för app-registreringar. En detaljerad beskrivning av varje behörighet finns [under typer och behörigheter för program registrering i Azure Active Directory](./roles-custom-available-permissions.md).
   1. Ange först "autentiseringsuppgifter" i Sök fältet och välj `microsoft.directory/applications/credentials/update` behörigheten.

      ![Välj behörigheter för en anpassad roll på fliken behörigheter](./media/roles-create-custom/permissions-tab.png)

   1. Ange sedan "grundläggande" i Sök fältet, Välj `microsoft.directory/applications/basic/update` behörigheten och klicka sedan på **Nästa**.
1. På fliken **Granska + skapa** granskar du behörigheterna och väljer **skapa**.

Den anpassade rollen visas i listan över tillgängliga roller att tilldela.

## <a name="assign-a-role-scoped-to-a-resource"></a>Tilldela en roll som är begränsad till en resurs

Precis som inbyggda roller kan anpassade roller tilldelas till hela organisationen för att ge åtkomst över alla registrerade appar. Men anpassade roller kan också tilldelas till resurs omfånget. På så sätt kan du ge den tilldelas behörighet att uppdatera autentiseringsuppgifter och grundläggande egenskaper för en enskild app utan att behöva skapa en andra anpassad roll.

1. Om du inte redan gjort det loggar du in på [administrations centret för Azure AD](https://aad.portal.azure.com) med programutvecklare i Azure AD-organisationen.
1. Välj **Appregistreringar**.
1. Välj den app-registrering som du beviljar åtkomst för att hantera. Du kanske måste välja **alla program** om du vill se en fullständig lista över app-registreringar i din Azure AD-organisation.

    ![Välj app-registrering som ett resurs omfång för en roll tilldelning](./media/roles-create-custom/appreg-all-apps.png)

1. I appens registrering väljer du **roller och administratörer**. Om du inte redan har skapat ett följer du anvisningarna i [föregående procedur](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Välj rollen för att öppna **tilldelnings** sidan.
1. Välj **Lägg till tilldelning** för att lägga till en användare. Användaren beviljas inga behörigheter för någon annan app-registrering än den som valts.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Skapa en anpassad roll med hjälp av Azure AD PowerShell

### <a name="prepare-powershell"></a>Förbered PowerShell

Först måste du [Ladda ned Azure AD Preview PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).

Använd följande kommandon för att installera Azure AD PowerShell-modulen:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Kontrol lera att modulen är redo att användas genom att använda följande kommando:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
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
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Tilldela den anpassade rollen med hjälp av Azure AD PowerShell

Tilldela rollen med hjälp av PowerShell-skriptet nedan:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADRoleDefinition -Filter "displayName eq ' Application Registration Creator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScopes = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADRoleAssignment -ResourceScopes $resourceScopes -RoleDefinitionId $roleDefinition.objectId -PrincipalId $user.objectId
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
        "microsoft.directory/applications/basic/update",
        "microsoft.directory/applications/credentials/update"
    ]
    }
    ```

1. Skapa roll tilldelningen.

    HTTP-begäran om att skapa en anpassad roll definition.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
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
