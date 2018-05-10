---
title: Bevilja användarbehörighet att principer för specifika labbet | Microsoft Docs
description: Lär dig att ge användarbehörigheter principer för specifika labb i DevTest Labs baserat på varje användares behov
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2b81c23b5cf9ea5d4bfc47d36ae251f762ffad11
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Bevilja användarbehörighet att principer för specifika labbet
## <a name="overview"></a>Översikt
Den här artikeln visar hur du använder PowerShell för att ge användare behörighet till en viss labb-princip. På så sätt kan kan behörigheter användas beroende på varje användares behov. Du kanske vill ge en viss användare möjlighet att ändra principinställningarna VM, men inte kostnaden principer.

## <a name="policies-as-resources"></a>Principer som resurser
Enligt beskrivningen i den [Azure rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md) artikeln RBAC Aktivera detaljerad åtkomsthantering av resurser för Azure. Med RBAC kan du särskilja uppgifter inom DevOps-teamet och ge bara mängden åtkomst till användare som de behöver för att utföra sitt arbete.

I DevTest Labs är en princip för en resurstyp som aktiverar åtgärden RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Varje princip för labbet är en resurs i princip resurstypen och kan tilldelas som ett scope till ett RBAC-roll.

Till exempel för att ge användare läs/skrivbehörighet till den **tillåtna storlekar på VM** princip, skulle du skapa en anpassad roll som fungerar med den **Microsoft.DevTestLab/labs/policySets/policies/*** åtgärd, och sedan tilldela rätt användare till den här anpassade rollen i omfånget för **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Mer information om anpassade roller i RBAC finns det [anpassade roller åtkomstkontroll](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Skapa en anpassad labb-roll med hjälp av PowerShell
Du behöver läsa följande artikel som förklarar hur du installerar och konfigurerar du Azure PowerShell-cmdlets för att komma igång: [ https://azure.microsoft.com/blog/azps-1-0-pre ](https://azure.microsoft.com/blog/azps-1-0-pre).

När du har konfigurerat Azure PowerShell-cmdlets kan du utföra följande uppgifter:

* Visa en lista med alla operations/åtgärder för en resursprovider
* Lista över åtgärder i en viss roll:
* Skapa en anpassad roll

Följande PowerShell-skript visar exempel på hur du utför dessa uppgifter:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Tilldela behörigheter till en användare för en specifik princip som använder anpassade roller
När du har definierat din anpassade roller, kan du tilldela dem till användare. För att tilldela en anpassad roll till en användare, måste du först skaffa den **ObjectId** som representerar den aktuella användaren. Göra genom att använda den **Get-AzureRmADUser** cmdlet.

I följande exempel visas den **ObjectId** av den *SomeUser* användaren är 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

När du har den **ObjectId** för användaren och ett namn på anpassad roll kan du tilldela rollen användare med den **ny AzureRmRoleAssignment** cmdlet:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

I föregående exempel är den **AllowedVmSizesInLab** principen används. Du kan använda någon av följande principer:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har behörighet användaren att principer för specifika labbet här är att tänka på följande steg:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md)
* [Ange principer för labbet](devtest-lab-set-lab-policy.md)
* [Skapa en labbmall](devtest-lab-create-template.md)
* [Skapa anpassade artefakter för virtuella datorer](devtest-lab-artifact-author.md)
* [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)

