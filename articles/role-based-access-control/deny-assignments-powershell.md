---
title: Visa lista över nekade tilldelningar för Azure-resurser med Azure PowerShell
description: Lär dig hur du visar en lista över användare, grupper, tjänstens huvud namn och hanterade identiteter som har nekats åtkomst till specifika Azure-resurs åtgärder vid specifika omfång med hjälp av Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137399"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Visa lista över nekade tilldelningar för Azure-resurser med Azure PowerShell

[Neka tilldelningar](deny-assignments.md) blockera användare från att utföra vissa åtgärder för Azure-resurser även om en roll tilldelning ger dem åtkomst. I den här artikeln beskrivs hur du visar en lista över nekade tilldelningar med Azure PowerShell.

> [!NOTE]
> Du kan inte skapa egna neka-tilldelningar direkt. För information om hur neka-tilldelningar skapas, se [neka tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Krav

För att få information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read`behörighet, som ingår i de flesta [inbyggda roller för Azure-resurser](built-in-roles.md)
- [PowerShell i Azure Cloud Shell](/azure/cloud-shell/overview) eller [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Visa lista över nekade tilldelningar

### <a name="list-all-deny-assignments"></a>Lista alla neka-tilldelningar

Om du vill visa alla neka-tilldelningar för den aktuella prenumerationen använder du [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Visa lista över neka tilldelningar i en resurs grupp omfånget

Använd [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)om du vill visa alla neka-tilldelningar vid en resurs grupps definitions område.

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Visa lista över neka tilldelningar i ett prenumerations omfång

Om du vill visa alla neka-tilldelningar i en prenumerations omfattning använder du [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). För att hämta prenumerations-ID kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Nästa steg

- [Förstå neka-tilldelningar för Azure-resurser](deny-assignments.md)
- [Visa lista över nekade tilldelningar för Azure-resurser med hjälp av Azure Portal](deny-assignments-portal.md)
- [Visa lista över nekade tilldelningar för Azure-resurser med hjälp av REST API](deny-assignments-rest.md)