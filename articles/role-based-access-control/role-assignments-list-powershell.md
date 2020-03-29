---
title: Lista rolltilldelningar med Azure RBAC och Azure PowerShell
description: Lär dig hur du tar reda på vilka resurser användare, grupper, tjänsthuvudnamn eller hanterade identiteter har åtkomst till med hjälp av RBAC (Azure Role-based Access Control) och Azure PowerShell.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0ec3153e5b1bfbe04a079d1cfc44e8e8709784d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931158"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>Lista rolltilldelningar med Azure RBAC och Azure PowerShell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]I den här artikeln beskrivs hur du listar rolltilldelningar med Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Om din organisation har lagt ut hanteringsfunktioner på entreprenad till en tjänsteleverantör som använder [Azure-delegerad resurshantering](../lighthouse/concepts/azure-delegated-resource-management.md)visas inte rolltilldelningar som auktoriserats av den tjänsteleverantören här.

## <a name="prerequisites"></a>Krav

- [PowerShell i Azure Cloud Shell](/azure/cloud-shell/overview) eller Azure [PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Lista rolltilldelningar för den aktuella prenumerationen

Det enklaste sättet att få en lista över alla rolltilldelningar i den aktuella prenumerationen (inklusive ärvda rolltilldelningar från rot- och hanteringsgrupper) är att använda [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) utan några parametrar.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Lista med rolltilldelningar för en prenumeration

Om du vill visa alla rolltilldelningar i ett prenumerationsomfång använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). För att få prenumerations-ID:t hittar du det på **bladet Prenumerationer** i Azure-portalen eller så kan du använda [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

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

Om du vill visa en lista över alla roller som har tilldelats en angiven användare och de roller som har tilldelats de grupper som användaren tillhör använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Visa rolltilldelningar för en resursgrupp

Om du vill visa alla rolltilldelningar i ett resursgruppomfång använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

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

## <a name="list-role-assignments-for-a-management-group"></a>Lista rolltilldelningar för en hanteringsgrupp

Om du vill visa alla rolltilldelningar i ett hanteringsgruppomfång använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Om du vill hämta hanteringsgrupp-ID:t hittar du det på bladet **Hanteringsgrupper** i Azure-portalen eller så kan du använda [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Lista rolltilldelningar för klassisk tjänstadministratör och medadministratörer

Om du vill visa rolltilldelningar för den klassiska prenumerationsadministratören och medadministratörerna använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Lista rolltilldelningar för en hanterad identitet

1. Hämta objekt-ID:t för den systemtilldelade eller användartilldelade hanterade identiteten. 

    Om du vill hämta objekt-ID:t för en användartilldelad hanterad identitet kan du använda [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. Om du vill visa rolltilldelningar använder du [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure PowerShell](role-assignments-powershell.md)
