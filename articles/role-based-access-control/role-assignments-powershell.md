---
title: Hantera åtkomst till Azure-resurser med RBAC och Azure PowerShell | Microsoft Docs
description: Lär dig hur du hanterar åtkomst till Azure-resurser för användare, grupper och program med rollbaserad åtkomst kontroll (RBAC) och Azure PowerShell. Detta innefattar hur du listar åtkomst, ger åtkomst och tar bort åtkomst.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5a6ed66efa0f73f957c3acb048136a5328f9c264
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750166"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>Hantera åtkomst till Azure-resurser med RBAC och Azure PowerShell

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är det du använder för att hantera åtkomst till Azure-resurser. Den här artikeln beskriver hur du hanterar åtkomst för användare, grupper och program som använder RBAC och Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Om du vill hantera åtkomst behöver du något av följande:

* [PowerShell i Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>Visa roller

### <a name="list-all-available-roles"></a>Visa en lista över alla tillgängliga roller

Om du vill visa en lista över RBAC-roller som är tillgängliga för tilldelning och granska de åtgärder som de beviljar åtkomst till, använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>Lista en speciell roll

Använd [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)för att visa en lista med en speciell roll.

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

## <a name="list-a-role-definition"></a>Lista en roll definition

### <a name="list-a-role-definition-in-json-format"></a>Lista en roll definition i JSON-format

Om du vill visa en roll definition i JSON-format använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-actions-of-a-role"></a>Lista åtgärder för en roll

Om du vill visa en lista över åtgärder för en speciell roll använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="list-access"></a>Visar åtkomst

I RBAC, för att lista åtkomst, listas roll tilldelningarna.

### <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

Om du vill visa en lista över alla roller som har tilldelats en angiven användare använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Om du vill visa en lista över alla roller som har tilldelats en angiven användare och de roller som är tilldelade de grupper som användaren tillhör använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Lista roll tilldelningar i ett resurs grupps omfång

Om du vill visa en lista över alla roll tilldelningar i ett resurs grupps omfång använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Lista roll tilldelningar i ett prenumerations omfång

Om du vill visa en lista över alla roll tilldelningar i en prenumerations omfattning använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). För att hämta prenumerations-ID kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Lista roll tilldelningar i ett hanterings grupps omfång

Om du vill visa en lista över alla roll tilldelningar i ett hanterings grupps omfång använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Om du vill hämta ID för hanterings grupp kan du hitta det på bladet **hanterings grupper** i Azure Portal eller så kan du använda [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Lista roll tilldelningar för klassisk tjänst administratör och medadministratörer

Använd [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)för att visa en lista över roll tilldelningar för den klassiska prenumerations administratören och medadministratörerna.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Bevilja åtkomst

För att skapa åtkomst i RBAC skapar du rolltilldelningar.

### <a name="search-for-object-ids"></a>Sök efter objekt-ID: n

Om du vill tilldela en roll måste du identifiera både objektet (användare, grupp eller program) och omfånget.

För att hämta prenumerations-ID kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

Använd [Get-AzADUser](/powershell/module/az.resources/get-azaduser)för att hämta objekt-ID för en Azure AD-användare.

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
```

Använd [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)för att hämta objekt-ID för en Azure AD-grupp.

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
```

Använd [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)för att hämta objekt-ID för tjänstens huvud namn eller program för Azure AD.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Skapa en roll tilldelning för en användare i ett resurs grupps omfång

Använd [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)för att bevilja åtkomst till en användare i ett resurs grupps omfång.

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

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Skapa en roll tilldelning med hjälp av unikt roll-ID

Det finns ett par gånger när ett roll namn kan ändras, till exempel:

- Du använder din egen anpassade roll och du bestämmer dig för att ändra namnet.
- Du använder en förhands gransknings roll som har **(förhands granskning)** i namnet. När rollen släpps får rollen ett nytt namn.

> [!IMPORTANT]
> En för hands version tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Även om en roll får ett nytt namn ändras inte roll-ID: t. Om du använder skript eller automatisering för att skapa roll tilldelningar, är det en bra idé att använda det unika roll-ID: t i stället för roll namnet. Därför är skripten mer sannolika om du byter namn på en roll.

Om du vill skapa en roll tilldelning med hjälp av det unika roll-ID: t i stället för roll namnet använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

I följande exempel tilldelas rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) för att *alain@example.com* användare i resurs grupps omfånget *Pharma-Sales* . Om du vill hämta det unika roll-ID: t kan du använda [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) eller se [inbyggda roller för Azure-resurser](built-in-roles.md).

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

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>Skapa en roll tilldelning för en grupp vid ett resurs omfång

Om du vill bevilja åtkomst till en grupp vid ett resurs omfång använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

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

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>Skapa en roll tilldelning för ett program i ett prenumerations omfång

Om du vill bevilja åtkomst till ett program i ett prenumerations omfång använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <application_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
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

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Skapa en roll tilldelning för en användare i en hanterings grupps omfattning

Om du vill bevilja åtkomst till en användare i ett hanterings grupps omfång använder du [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Om du vill hämta ID för hanterings grupp kan du hitta det på bladet **hanterings grupper** i Azure Portal eller så kan du använda [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

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

## <a name="remove-access"></a>Tar bort åtkomst

I RBAC för att ta bort åtkomst tar du bort en roll tilldelning genom att använda [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

I följande exempel tar vi bort roll tilldelningen för den *virtuella datorn deltagare* från *Alain \@example. com* -användare i resurs gruppen *Pharma-Sales* :

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

I följande exempel tas rollen < role_name > bort från < object_id > i ett prenumerations omfång.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

I följande exempel tas rollen < role_name > bort från < object_id > i hanterings gruppens omfång.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Om du får fel meddelandet "den tillhandahållna informationen inte mappas till en roll tilldelning", se till att du även anger `-Scope`-eller `-ResourceGroupName` parametrar. Mer information finns i [FELSÖKA RBAC för Azure-resurser](troubleshooting.md#role-assignments-with-unknown-security-principal).

## <a name="next-steps"></a>Nästa steg

- [Självstudie: bevilja en grupp åtkomst till Azure-resurser med RBAC och Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Självstudie: skapa en anpassad roll för Azure-resurser med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md)
- [Hantera resurser med Azure PowerShell](../azure-resource-manager/manage-resources-powershell.md)
