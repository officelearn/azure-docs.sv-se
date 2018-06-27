---
title: Självstudiekurs – Bevilja åtkomst för en grupp med RBAC och Azure PowerShell | Microsoft Docs
description: Använda rollbaserad åtkomstkontroll (RBAC) för att ge en grupp åtkomst för att visa allt i en prenumeration och hantera allt innehåll i en resursgrupp med hjälp av Azure PowerShell.
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
ms.openlocfilehash: 5012ce22204c3ac32b22fe2cd842ce1300b426f8
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295821"
---
# <a name="tutorial-grant-access-for-a-group-using-rbac-and-azure-powershell"></a>Självstudiekurs – Bevilja åtkomst för en grupp med RBAC och Azure PowerShell

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är sättet som du hantera åtkomst till resurser i Azure. I den här självstudien kommer du at ge en grupp åtkomst för att visa allt i en prenumeration och hantera allt innehåll i en resursgrupp med hjälp av Azure PowerShell.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Beviljar åtkomst för en grupp med olika omfång
> * Visar åtkomst
> * Tar bort åtkomst

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna genomföra den här kursen behöver du följande:

- Behörighet att skapa grupper i Azure Active Directory (eller en befintlig grupp)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Rolltilldelningar

För att skapa åtkomst i RBAC skapar du rolltilldelningar. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång. Här följer två rolltilldelningar som du utför i den här självstudiekursen:

| Säkerhetsobjekt | Rolldefinition | Omfång |
| --- | --- | --- |
| Grupp<br>(RBAC-kursgrupp) | [Läsare](built-in-roles.md#reader) | Prenumeration |
| Grupp<br>(RBAC-kursgrupp)| [Deltagare](built-in-roles.md#contributor) | Resursgrupp<br>(rbac-tutorial-resource-group) |

   ![Rolltilldelningar för en grupp](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Skapa en grupp

Om du vill tilldela en roll behöver du en användare, grupp eller tjänstens huvudnamn. Om du inte redan har en grupp skapar du en.

- Azure Cloud Shell skapar du en ny grupp med hjälp av kommandot [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Om du inte har behörighet för att skapa grupper kan du prova [Självstudier: Bevilja åtkomst för en användare med RBAC och Azure PowerShell](tutorial-role-assignments-user-powershell.md) i stället.

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

Om du vill bevilja åtkomst för gruppen använder du kommandot [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) för att tilldela en roll. Du måste ange säkerhetsobjekt, rolldefinition och omfång.

1. Hämta objekt-ID för en grupp med kommandot [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Spara gruppens objekt-ID i en variabel.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

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

1. Tilldela rollen [Läsare](built-in-roles.md#reader) i gruppen i prenumerationsomfattningen.

    ```azurepowershell
    New-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Tilldela rollen [Deltagare](built-in-roles.md#contributor) i gruppen i resursgruppomfattningen.

    ```azurepowershell
    New-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Visar åtkomst

1. För att bekräfta åtkomsten till prenumerationen använder du kommandot [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) för att visa en lista över rolltilldelningarna.

    ```azurepowershell
    Get-AzureRmRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Du kan se att rollen Läsare har tilldelats till gruppen RBAC-kursgrupp med prenumerationsomfång.

1. För att bekräfta åtkomsten till resursgruppen använder du kommandot [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) för att visa en lista över rolltilldelningarna.

    ```azurepowershell
    Get-AzureRmRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Du kan se att rollerna Läsare och Deltagare har tilldelats till gruppen RBAC-kursgrupp. Deltagarrollen är definitionsområdet rbac-tutorial-resource-group och rollen Läsare ärvs i prenumerationomfånget.

## <a name="optional-list-access-using-the-azure-portal"></a>(Valfritt) Visa åtkomst med hjälp av Azure Portal

1. Om du vill se rolltilldelningen i Azure-portalen kan du visa **åtkomstkontroll (IAM)**-bladet för prenumerationen.

    ![Rolltilldelningar för en grupps prenumerationsomfång](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Visa bladet **Åtkomstkontroll (IAM)** för resursgruppen.

    ![Rolltilldelningar för en grupps resursgruppomfång](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Tar bort åtkomst

Ta bort åtkomst för användare, grupper och program med [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) för att ta bort en rolltilldelning.

1. Använd följande kommando för att ta bort rolltilldelningen Deltagare från gruppens resursgruppomfång.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Använd följande kommando för att ta bort rolltilldelningen Läsare från gruppens prenumerationsomfång.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen och gruppen.

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

1. Ta bort gruppen med kommandot [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup).

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Om du får ett felmeddelande när du försöker ta bort gruppen kan du också ta bort gruppen i portalen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera åtkomst med RBAC och PowerShell](role-assignments-powershell.md)