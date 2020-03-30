---
title: Skapa anpassade roller i Azure AD-rollbaserad åtkomstkontroll | Microsoft-dokument
description: Skapa och tilldela anpassade Azure AD-roller med resursomfattning på Azure Active Directory-resurser.
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025278"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Skapa och tilldela en anpassad roll i Azure Active Directory

I den här artikeln beskrivs hur du skapar nya anpassade roller i Azure Active Directory (Azure AD). Grunderna i anpassade roller finns i [översikten över anpassade roller](roles-custom-overview.md). Rollen kan tilldelas antingen på katalognivå eller endast ett resursomfång för appregistrering.

Anpassade roller kan skapas på fliken [Roller och administratörer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) på översiktssidan för Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Skapa en roll i Azure-portalen

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Skapa en ny anpassad roll för att ge åtkomst till hantering av appregistreringar

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med privilegierad rolladministratör eller globala administratörsbehörigheter i Azure AD-organisationen.
1. Välj Azure Active**Directory-roller** >  **Azure Active Directory** > och administratörer**Ny anpassad roll**.

   ![Skapa eller redigera roller från sidan Roller och administratörer](./media/roles-create-custom/new-custom-role.png)

1. Ange ett namn och en beskrivning för rollen på fliken **Grunderna** och klicka sedan på **Nästa**.

   ![ange ett namn och en beskrivning för en anpassad roll på fliken Grunderna](./media/roles-create-custom/basics-tab.png)

1. På fliken **Behörigheter** väljer du de behörigheter som krävs för att hantera grundläggande egenskaper och egenskaper för autentiseringsuppgifter för appregistreringar. En detaljerad beskrivning av varje behörighet finns i [Undertyper och behörigheter för programregistrering i Azure Active Directory](./roles-custom-available-permissions.md).
   1. Ange först "autentiseringsuppgifter" i sökfältet och välj behörighet. `microsoft.directory/applications/credentials/update`

      ![Välj behörigheter för en anpassad roll på fliken Behörigheter](./media/roles-create-custom/permissions-tab.png)

   1. Ange sedan "grundläggande" i sökfältet, `microsoft.directory/applications/basic/update` välj behörighet och klicka sedan på **Nästa**.
1. På fliken **Granska + skapa** granskar du behörigheterna och väljer **Skapa**.

Din anpassade roll visas i listan över tillgängliga roller att tilldela.

## <a name="create-a-role-using-powershell"></a>Skapa en roll med PowerShell

### <a name="prepare-powershell"></a>Förbered PowerShell

Först måste du [hämta Azure AD Preview PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).

Så här installerar du Azure AD PowerShell-modulen med följande kommandon:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Om du vill kontrollera att modulen är klar att användas använder du följande kommando:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Skapa den anpassade rollen

Skapa en ny roll med följande PowerShell-skript:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Tilldela den anpassade rollen med Azure AD PowerShell

Tilldela rollen med hjälp av powershell-skriptet nedan:

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

## <a name="create-a-role-with-graph-api"></a>Skapa en roll med Graph API

1. Skapa rolldefinitionen.

    HTTP-begäran om att skapa en anpassad rolldefinition.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Innehåll

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. Skapa rolltilldelningen.

    HTTP-begäran om att skapa en anpassad rolldefinition.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Innehåll

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Tilldela en anpassad roll begränsad till en resurs

Precis som inbyggda roller tilldelas anpassade roller som standard i standardomfattande organisationsomfattande scope för att bevilja åtkomstbehörigheter för alla appregistreringar i organisationen. Men till skillnad från inbyggda roller kan anpassade roller också tilldelas i omfattningen av en enda Azure AD-resurs. På så sätt kan du ge användaren behörighet att uppdatera autentiseringsuppgifter och grundläggande egenskaper för en enskild app utan att behöva skapa en andra anpassad roll.

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med programutvecklarebehörigheter i Azure AD-organisationen.
1. Välj **Appregistreringar**.
1. Välj den appregistrering som du ger åtkomst till hantera. Du kanske måste välja **Alla program** för att se den fullständiga listan över appregistreringar i din Azure AD-organisation.

    ![Välj appregistrering som resursomfattning för en rolltilldelning](./media/roles-create-custom/appreg-all-apps.png)

1. I appregistreringen väljer du **Roller och administratörer**. Om du inte redan har skapat någon finns instruktionerna i [föregående procedur](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Välj den roll som sidan **Tilldelningar** ska öppnas.
1. Välj **Lägg till tilldelning** om du vill lägga till en användare. Användaren beviljas alla behörigheter över endast den valda appregistreringen.

## <a name="next-steps"></a>Nästa steg

- Känn dig välkommen att dela med oss på [Azure AD administrativa roller forum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Mer information om roller och administratörsrolltilldelning finns i [Tilldela administratörsroller](directory-assign-admin-roles.md).
- Standardanvändarbehörigheter finns i en [jämförelse av standardbehörighet för gäst- och medlemsanvändare](../fundamentals/users-default-permissions.md).
