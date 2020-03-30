---
title: Lista neka tilldelningar för Azure-resurser med Azure PowerShell
description: Lär dig hur du listar användare, grupper, tjänsthuvudnamn och hanterade identiteter som har nekats åtkomst till specifika Azure-resursåtgärder vid vissa scope med Hjälp av Azure PowerShell.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137399"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Lista neka tilldelningar för Azure-resurser med Azure PowerShell

[Neka tilldelningar](deny-assignments.md) blockera användare från att utföra specifika Azure-resursåtgärder även om en rolltilldelning ger dem åtkomst. I den här artikeln beskrivs hur du listar nekade tilldelningar med Azure PowerShell.

> [!NOTE]
> Du kan inte direkt skapa dina egna neka-tilldelningar. Information om hur neka tilldelningar skapas finns i [Neka tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Krav

Om du vill ha information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read`behörighet, som ingår i de flesta [inbyggda roller för Azure-resurser](built-in-roles.md)
- [PowerShell i Azure Cloud Shell](/azure/cloud-shell/overview) eller Azure [PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Visa lista över nekade tilldelningar

### <a name="list-all-deny-assignments"></a>Lista alla neka tilldelningar

Om du vill visa en lista över alla neka tilldelningar för den aktuella prenumerationen använder du [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Lista neka tilldelningar i ett resursgruppomfång

Om du vill visa en lista över alla neka tilldelningar i en resursgruppsomfång använder du [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Lista neka tilldelningar i ett prenumerationsomfång

Om du vill visa en lista över alla neka tilldelningar i ett prenumerationsomfång använder du [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). För att få prenumerations-ID:t hittar du det på **bladet Prenumerationer** i Azure-portalen eller så kan du använda [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Nästa steg

- [Förstå neka tilldelningar för Azure-resurser](deny-assignments.md)
- [Lista neka tilldelningar för Azure-resurser med Azure-portalen](deny-assignments-portal.md)
- [Lista neka tilldelningar för Azure-resurser med REST API](deny-assignments-rest.md)