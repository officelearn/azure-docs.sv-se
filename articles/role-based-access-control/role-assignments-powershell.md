---
title: Lägga till eller ta bort rolltilldelningar med RBAC och Azure PowerShell
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänsthuvudnamn eller hanterade identiteter med hjälp av Azure Role-based Access Control (RBAC) och Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283217"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]I den här artikeln beskrivs hur du tilldelar roller med Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Om du vill lägga till eller ta bort rolltilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write`och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)
- [PowerShell i Azure Cloud Shell](/azure/cloud-shell/overview) eller Azure [PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Hämta objekt-ID:er

Om du vill lägga till eller ta bort rolltilldelningar kan du behöva ange ett objekts unika ID. ID:et har `11111111-1111-1111-1111-111111111111`formatet: . Du kan hämta ID:t med Azure-portalen eller Azure PowerShell.

### <a name="user"></a>Användare

Om du vill hämta objekt-ID:t för en Azure AD-användare kan du använda [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Grupp

Om du vill hämta objekt-ID:t för en Azure AD-grupp kan du använda [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Program

Om du vill hämta objekt-ID:t för ett Azure AD-tjänsthuvudnamn (identitet som används av ett program) kan du använda [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). För ett tjänsthuvudnamn använder du objekt-ID:t och **inte** program-ID: et.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I RBAC, för att bevilja åtkomst, lägger du till en rolltilldelning.

### <a name="user-at-a-resource-group-scope"></a>Användare i ett resursgruppomfång

Om du vill lägga till en rolltilldelning för en användare i ett resursgruppomfång använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Använda det unika roll-ID:et

Det finns ett par gånger när ett rollnamn kan ändras, till exempel:

- Du använder din egen anpassade roll och du bestämmer dig för att ändra namnet.
- Du använder en förhandsgranskningsroll som har **(Förhandsgranska)** i namnet. När rollen släpps får rollen ett nytt namn.

> [!IMPORTANT]
> En förhandsversion tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Även om en roll har bytt namn ändras inte roll-ID:et. Om du använder skript eller automatisering för att skapa dina rolltilldelningar är det bäst att använda det unika roll-ID:t i stället för rollnamnet. Om en roll har bytt namn är det därför mer sannolikt att skripten fungerar.

Om du vill lägga till en rolltilldelning med det unika roll-ID:et i stället för rollnamnet använder du [Ny-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

I följande exempel tilldelas rollen [Deltagare i virtuell dator](built-in-roles.md#virtual-machine-contributor) till *alain\@example.com* användare i resursgruppomfånget för *pharma-sales.* För att få det unika roll-ID:t kan du använda [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) eller se [Inbyggda roller för Azure-resurser](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Gruppera i ett resursomfattning

Om du vill lägga till en rolltilldelning för en grupp i ett resursomfattning använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Information om hur du hämtar objekt-ID:et för gruppen finns i [Hämta objekt-ID: er](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>Program i ett prenumerationsomfång

Om du vill lägga till en rolltilldelning för ett program i ett prenumerationsomfång använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Information om hur du hämtar programmets objekt-ID finns i [Hämta objekt-ID: er](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>Användare i en hanteringsgruppomfattning

Om du vill lägga till en rolltilldelning för en användare i en hanteringsgruppsomfång använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Om du vill hämta hanteringsgrupp-ID:t hittar du det på bladet **Hanteringsgrupper** i Azure-portalen eller så kan du använda [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I RBAC, för att ta bort åtkomst, tar du bort en rolltilldelning med hjälp av [Ta bort-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

I följande exempel tas rolltilldelningen för deltagare i *virtuell dator* från *\@alain-example.com* användare i resursgruppen för *läkemedelsförsäljning:*

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

I följande exempel tas <role_name> roll från <object_id> i ett prenumerationsomfång.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

I följande exempel tas <role_name> roll från <object_id> i hanteringsgruppens omfattning.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Om du får felmeddelandet felmeddelande: "Den angivna informationen mappas inte till `-Scope` en `-ResourceGroupName` rolltilldelning" kontrollerar du att du också anger parametrarna eller. Mer information finns i [Felsöka RBAC för Azure-resurser](troubleshooting.md#role-assignments-with-unknown-security-principal).

## <a name="next-steps"></a>Nästa steg

- [Lista rolltilldelningar med Azure RBAC och Azure PowerShell](role-assignments-list-powershell.md)
- [Självstudiekurs: Bevilja en grupp åtkomst till Azure-resurser med RBAC och Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Självstudiekurs: Skapa en anpassad roll för Azure-resurser med Azure PowerShell](tutorial-custom-role-powershell.md)
- [Hantera resurser med Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
