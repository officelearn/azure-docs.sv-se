---
title: Självstudiekurs – Bevilja åtkomst för en användare med RBAC och Azure PowerShell | Microsoft Docs
description: Använda rollbaserad åtkomstkontroll (RBAC) för att ge en användare åtkomst för att visa allt i en prenumeration och hantera allt innehåll i en resursgrupp med hjälp av Azure PowerShell.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 4eeae864721040bda18409b48abf5d33b81ca609
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295906"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-azure-powershell"></a>Självstudiekurs – Bevilja åtkomst för en användare med RBAC och Azure PowerShell

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är sättet som du hantera åtkomst till resurser i Azure. I den här självstudien kommer du at ge en användare åtkomst för att visa allt i en prenumeration och hantera allt innehåll i en resursgrupp med hjälp av Azure PowerShell.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Beviljar åtkomst till en användare med olika omfång
> * Visar åtkomst
> * Tar bort åtkomst

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna genomföra den här kursen behöver du följande:

- Behörighet att skapa användare i Azure Active Directory (eller en befintlig grupp)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Rolltilldelningar

För att skapa åtkomst i RBAC skapar du rolltilldelningar. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång. Här följer två rolltilldelningar som du utför i den här självstudiekursen:

| Säkerhetsobjekt | Rolldefinition | Omfång |
| --- | --- | --- |
| Användare<br>(RBAC-kursanvändare) | [Läsare](built-in-roles.md#reader) | Prenumeration |
| Användare<br>(RBAC-kursanvändare)| [Deltagare](built-in-roles.md#contributor) | Resursgrupp<br>(rbac-tutorial-resource-user) |

   ![Rolltilldelningar för en användare](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>Skapa en användare

Om du vill tilldela en roll behöver du en användare, grupp eller tjänstens huvudnamn. Om du inte redan har en användare skapar du en.

1. Skapa ett lösenord som uppfyller kraven på lösenordskomplexitet i Azure Cloud Shell.

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. Skapa en ny användare för din domän med kommandot [New-AzureADUser](/powershell/module/azuread/new-azureaduser).

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du kan använda en resursgrupp för att visa hur du tilldelar en roll med resursgruppomfång.

1. Hämta en lista över regionplatser med hjälp av kommandot [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

   ```azurepowershell
   Get-AzureRmLocation | select Location
   ```

1. Välj en plats nära dig och tilldela en variabel.

   ```azurepowershell
   $location = "westus"
   ```

1. Skapa en ny resursgrupp med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

   ```azurepowershell
   New-AzureRmResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Bevilja åtkomst

Om du vill bevilja åtkomst för användaren använder du kommandot [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) för att tilldela en roll. Du måste ange säkerhetsobjekt, rolldefinition och omfång.

1. Hämta ID för din prenumeration med hjälp av kommandot [Get-AzureRmSubscription](/powershell/module/azurerm.resources/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Spara prenumerationsomfattningen i en variabel.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Tilldela rollen [Läsare](built-in-roles.md#reader) till användaren med prenumerationsomfång.

    ```azurepowershell
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. Tilldela rollen [Deltagare](built-in-roles.md#contributor) till användaren med resursgruppomfång.

    ```azurepowershell
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>Visar åtkomst

1. För att bekräfta åtkomsten till prenumerationen använder du kommandot [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) för att visa en lista över rolltilldelningarna.

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Du kan se att rollen Läsare har tilldelats till användaren RBAC-kursanvändare med prenumerationsomfång.

1. För att bekräfta åtkomsten till resursgruppen använder du kommandot [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) för att visa en lista över rolltilldelningarna.

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Du kan se att rollerna Läsare och Deltagare har tilldelats till RBAC-kursanvändare. Deltagarrollen är definitionsområdet rbac-tutorial-resource-group och rollen Läsare ärvs i prenumerationomfånget.

## <a name="optional-list-access-using-the-azure-portal"></a>(Valfritt) Visa åtkomst med hjälp av Azure Portal

1. Om du vill se rolltilldelningen i Azure-portalen kan du visa **åtkomstkontroll (IAM)**-bladet för prenumerationen.

    ![Rolltilldelningar för en användare med prenumerationsomfång](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. Visa bladet **Åtkomstkontroll (IAM)** för resursgruppen.

    ![Rolltilldelningar för en användare med resursgruppomfång](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>Tar bort åtkomst

Ta bort åtkomst för användare, grupper och program med [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) för att ta bort en rolltilldelning.

1. Använd följande kommando för att ta bort rolltilldelningen Deltagare från användarens resursgruppomfång.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Använd följande kommando för att ta bort rolltilldelningen Läsare från användarens prenumerationsomfång.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen och användaren.

1. Om du vill ta bort resursgruppen använder du kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

    ```azurepowershell
    Remove-AzureRmResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. När du ombeds bekräfta skriver du **Y**. Borttagningen tar några sekunder.

1. Ta bort en användare med hjälp av kommandot [Ta bort AzureADUser](/powershell/module/azuread/remove-azureaduser).

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera åtkomst med RBAC och PowerShell](role-assignments-powershell.md)