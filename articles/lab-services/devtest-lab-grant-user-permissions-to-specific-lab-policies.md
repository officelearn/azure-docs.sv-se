---
title: Bevilja användarbehörighet att specifika labbprinciper | Microsoft Docs
description: Lär dig att ge användarbehörighet till specifika labbprinciper i DevTest Labs baserat på varje användares behov
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
ms.openlocfilehash: 70469a9e8737a9df18628951a061c97081c74080
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127386"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Bevilja användarbehörighet att specifika labbprinciper
## <a name="overview"></a>Översikt
Den här artikeln visar hur du använder PowerShell för att ge användare behörighet till en viss labb. På så sätt kan kan behörigheter tillämpas beroende på varje användares behov. Du kanske vill ge en viss användare möjlighet att ändra principinställningarna för virtuell dator, men inte kostnaden principer.

## <a name="policies-as-resources"></a>Principer som resurser
Enligt beskrivningen i den [Azure rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md) artikeln RBAC Aktivera detaljerad åtkomsthantering av resurser för Azure. Med RBAC kan du hålla isär uppgifter i din DevOps-team och bevilja endast mängden åtkomst till användare som de behöver för att utföra sitt arbete.

I DevTest Labs är en princip för en resurstyp som gör att åtgärden RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Varje labb-princip är en resurs i typen av resurs och kan tilldelas som ett omfång i en RBAC-roll.

Till exempel för att ge användare läs/skrivbehörighet till den **tillåtna storlekar** princip, skapa en anpassad roll som fungerar med den **Microsoft.DevTestLab/labs/policySets/policies/** åtgärd , och sedan tilldela rätt användare till den här anpassade rollen i omfånget för **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Läs mer om anpassade roller i RBAC i den [anpassade roller åtkomstkontroll](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Skapa en anpassad roll för labb med hjälp av PowerShell
För att komma igång, måste du [installera Azure PowerShell](/powershell/azure/install-az-ps). 

När du har konfigurerat Azure PowerShell-cmdlets, kan du utföra följande uppgifter:

* Lista alla åtgärder/åtgärder för en resursprovider
* Lista åtgärder inom en viss roll:
* Skapa en anpassad roll

Följande PowerShell-skript visar exempel på hur du utför dessa uppgifter:

    ‘List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Tilldelar behörigheter till en användare för en specifik princip med hjälp av anpassade roller
När du har definierat dina anpassade roller, kan du tilldela dem till användare. För att tilldela en anpassad roll till en användare, måste du först skaffa den **ObjectId** som representerar den användaren. Gör detta genom att använda den **Get-AzADUser** cmdlet.

I följande exempel visas den **ObjectId** av den *SomeUser* användaren är 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

När du har den **ObjectId** för användaren och ett namn på anpassad roll kan du tilldela rollen till användaren med den **New AzRoleAssignment** cmdlet:

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

I exemplet ovan den **AllowedVmSizesInLab** principen används. Du kan använda någon av följande principer:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har behörighet användare till specifika labbprinciper här följer nästa steg att tänka på:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md)
* [Ange principer för labbet](devtest-lab-set-lab-policy.md)
* [Skapa en labbmall](devtest-lab-create-template.md)
* [Skapa anpassade artefakter för virtuella datorer](devtest-lab-artifact-author.md)
* [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)

