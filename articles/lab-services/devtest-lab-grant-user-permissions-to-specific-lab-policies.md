---
title: Bevilja användarbehörigheter till specifika labbprinciper | Microsoft-dokument
description: Lär dig hur du beviljar användarbehörigheter till specifika labbprinciper i DevTest Labs baserat på varje användares behov
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284218"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Bevilja användarbehörigheter till specifika labbprinciper
## <a name="overview"></a>Översikt
Den här artikeln visar hur du använder PowerShell för att ge användare behörighet till en viss labbprincip. På så sätt kan behörigheter tillämpas baserat på varje användares behov. Du kanske till exempel vill ge en viss användare möjlighet att ändra vm-principinställningarna, men inte kostnadsprinciperna.

## <a name="policies-as-resources"></a>Principer som resurser
Som beskrivs i azure [rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md) artikel, RBAC möjliggör finkornig åtkomsthantering av resurser för Azure. Med HJÄLP AV RBAC kan du segregera uppgifter inom ditt DevOps-team och endast bevilja åtkomst till användare som de behöver för att utföra sina jobb.

I DevTest Labs är en princip en resurstyp som aktiverar RBAC-åtgärden **Microsoft.DevTestLab/labs/policySets/policies/**. Varje labbprincip är en resurs i principresurstypen och kan tilldelas som ett omfång till en RBAC-roll.

För att till exempel ge användare läs-/skrivbehörighet till principen **Tillåtna vm-storlekar** skapar du en anpassad roll som fungerar med åtgärden **Microsoft.DevTestLab/labs/policySets/policies/och** sedan tilldela lämpliga användare till den här anpassade rollen i omfattningen av **Microsoft.DevTestLab/labs/policySets/policies/policies/AllowedVmSizesInLab**.

Mer information om anpassade roller i RBAC finns i [åtkomstkontrollen för anpassade roller](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Skapa en anpassad labbroll med PowerShell
För att komma igång måste du [installera Azure PowerShell](/powershell/azure/install-az-ps). 

När du har konfigurerat Azure PowerShell-cmdlets kan du utföra följande uppgifter:

* Lista alla åtgärder/åtgärder för en resursprovider
* Lista åtgärder i en viss roll:
* Skapa en anpassad roll

Följande PowerShell-skript illustrerar exempel på hur du utför dessa uppgifter:

    # List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    # List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    # Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Tilldela behörigheter till en användare för en viss princip med hjälp av anpassade roller
När du har definierat dina anpassade roller kan du tilldela dem till användare. För att tilldela en anpassad roll till en användare måste du först hämta **ObjectId** som representerar den användaren. För att göra det, använd **Get-AzADUser** cmdlet.

I följande exempel är **ObjectId** för *användaren SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

När du har **ObjectId** för användaren och ett anpassat rollnamn kan du tilldela den rollen till användaren med cmdleten **New-AzRoleAssignment:**

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

I föregående exempel används principen **AllowedVmSizesInLab.** Du kan använda någon av följande poliser:

* MaxVmsTillåtalowedPerUser
* MaxVmsTillåtaperlab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har beviljat användarbehörigheter till specifika labbprinciper bör du tänka på följande:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md)
* [Ange labbprinciper](devtest-lab-set-lab-policy.md)
* [Skapa en labbmall](devtest-lab-create-template.md)
* [Skapa anpassade artefakter för dina virtuella datorer](devtest-lab-artifact-author.md)
* [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)

