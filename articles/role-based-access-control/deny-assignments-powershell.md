---
title: Lista neka tilldelningar för Azure-resurser med Azure PowerShell | Microsoft Docs
description: Lär dig mer om att lista användare, grupper, tjänstens huvudnamn och hanterade identiteter som har nekats åtkomst till specifika Azure-resurs åtgärder på viss scope med hjälp av Azure PowerShell.
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
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110490"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Lista neka tilldelningar för Azure-resurser med Azure PowerShell

[Neka tilldelningar](deny-assignments.md) blockera användare från att utföra åtgärder för specifika Azure-resurs, även om en rolltilldelning ger dem åtkomst. Den här artikeln beskriver hur du listar neka tilldelningar med Azure PowerShell.

> [!NOTE]
> Du kan inte direkt skapa en egen neka tilldelningar. Information om hur neka tilldelningar har skapats, finns i [neka tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill ha information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read` behörigheter, som ingår i de flesta [inbyggda roller för Azure-resurser](built-in-roles.md)
- [PowerShell i Azure Cloudshell](/azure/cloud-shell/overview) eller [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Lista neka tilldelningar

### <a name="list-all-deny-assignments"></a>Lista över alla neka tilldelningar

Visa en lista över alla neka tilldelningar för den aktuella prenumerationen, Använd [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Lista neka tilldelningar en resursgruppomfånget

Visa en lista över alla neka tilldelningar resursgruppomfånget en, Använd [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Lista neka tilldelningar en prenumerationsomfånget

Visa en lista över alla neka tilldelningar prenumerationsomfånget, Använd [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). För att få prenumerations-ID kan du hittar den på den **prenumerationer** -bladet i Azure portal eller du kan använda [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Nästa steg

- [Förstå neka tilldelningar för Azure-resurser](deny-assignments.md)
- [Lista neka tilldelningar för Azure-resurser med Azure portal](deny-assignments-portal.md)
- [Lista neka tilldelningar för Azure-resurser med hjälp av REST-API](deny-assignments-rest.md)