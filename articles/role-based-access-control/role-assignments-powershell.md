---
title: Lägga till eller ta bort Azure Role-tilldelningar med Azure PowerShell-Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av Azure PowerShell och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f3fc54829be301c063440bd3508472287b6db265
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648335"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] I den här artikeln beskrivs hur du tilldelar roller med hjälp av Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till eller ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)
- [PowerShell i Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Hämta objekt-ID: n

Om du vill lägga till eller ta bort roll tilldelningar kan du behöva ange ett unikt ID för ett objekt. ID: t har formatet: `11111111-1111-1111-1111-111111111111` . Du kan hämta ID: t med hjälp av Azure Portal eller Azure PowerShell.

### <a name="user"></a>Användare

För att hämta objekt-ID för en Azure AD-användare kan du använda [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Grupp

För att hämta objekt-ID för en Azure AD-grupp, kan du använda [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Program

För att hämta objekt-ID för ett Azure AD-tjänstens huvud namn (identitet som används av ett program) kan du använda [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). För ett huvud namn för tjänsten använder du objekt-ID: t och **inte** program-ID: t.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I Azure RBAC för att bevilja åtkomst lägger du till en roll tilldelning.

### <a name="user-at-a-resource-group-scope"></a>Användare vid en resurs grupps omfång

Om du vill lägga till en roll tilldelning för en användare i ett resurs grupps omfång använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

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

### <a name="using-the-unique-role-id"></a>Använda det unika roll-ID: t

Det finns ett par gånger när ett roll namn kan ändras, till exempel:

- Du använder din egen anpassade roll och du bestämmer dig för att ändra namnet.
- Du använder en förhands gransknings roll som har **(förhands granskning)** i namnet. När rollen släpps får rollen ett nytt namn.

> [!IMPORTANT]
> En för hands version tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Även om en roll får ett nytt namn ändras inte roll-ID: t. Om du använder skript eller automatisering för att skapa roll tilldelningar, är det en bra idé att använda det unika roll-ID: t i stället för roll namnet. Därför är skripten mer sannolika om du byter namn på en roll.

Om du vill lägga till en roll tilldelning med det unika roll-ID: t i stället för roll namnet använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -Scope <resource_group_name/resource/management groups>
```

I följande exempel tilldelas rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till *Alain \@ example.com* -användare i resurs grupps omfånget *Pharma-Sales* . Om du vill hämta det unika roll-ID: t kan du använda [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) eller se [inbyggda Azure-roller](built-in-roles.md).

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

### <a name="group-at-a-resource-scope"></a>Gruppera i ett resurs omfång

Om du vill lägga till en roll tilldelning för en grupp i ett resurs omfång använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Information om hur du hämtar objekt-ID för gruppen finns i [Hämta objekt](#get-object-ids)-ID: n.

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

### <a name="application-at-a-subscription-scope"></a>Program i ett prenumerations omfång

Om du vill lägga till en roll tilldelning för ett program i ett prenumerations omfång använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Information om hur du hämtar objekt-ID för programmet finns i [Hämta objekt](#get-object-ids)-ID: n.

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

### <a name="user-at-a-management-group-scope"></a>Användare i en hanterings grupps omfattning

Om du vill lägga till en roll tilldelning för en användare i en hanterings grupps omfattning använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Om du vill hämta ID för hanterings grupp kan du hitta det på bladet **hanterings grupper** i Azure Portal eller så kan du använda [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

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

I Azure RBAC tar du bort en roll tilldelning genom att använda [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)för att ta bort åtkomst.

I följande exempel tas roll tilldelningen för *virtuell dator deltagare* bort från *Alain \@ example.com* -användaren på resurs gruppen *Pharma-Sales* :

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

I följande exempel tas <role_name> rollen bort från <object_id> i ett prenumerations omfång.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

I följande exempel tar du bort <role_name> rollen från <object_id> i hanterings gruppens omfång.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Om du får fel meddelandet "den tillhandahållna informationen inte mappas till en roll tilldelning", se till att du även anger `-Scope` `-ResourceGroupName` parametrarna eller. Mer information finns i [Felsöka Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>Nästa steg

- [Visa en lista med Azure Role-tilldelningar med Azure PowerShell](role-assignments-list-powershell.md)
- [Självstudie: bevilja en grupp åtkomst till Azure-resurser med hjälp av Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Självstudie: skapa en anpassad Azure-roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md)
- [Hantera resurser med Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)