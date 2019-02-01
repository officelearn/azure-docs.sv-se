---
title: Ändra statisk gruppmedlemskapstypen till dynamisk – Azure Active Directory | Microsoft Docs
description: Hur du skapar regler för att automatiskt fylla i grupper och en Regelreferens för medlemskap.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 7f8c9298c1e74e87411386a1d8ae36a34d986065
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508495"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Ändra medlemskap i statisk till dynamisk i Azure Active Directory

Du kan ändra en grupps medlemskap från statisk till dynamisk (eller vice versa) i Azure Active Directory (AD Azure). Azure AD behåller samma namn och ID i systemet, så att alla befintliga referenser i gruppen fortfarande är giltiga. Om du skapar en ny grupp i stället, skulle du behöva uppdatera dessa referenser. Dynamiskt gruppmedlemskap eliminerar management administration att lägga till och ta bort användare. Den här artikeln visar hur du konverterar befintliga grupper från statisk till dynamiskt medlemskap med hjälp av Azure AD-administrationscentret eller PowerShell-cmdletar.

> [!WARNING]
> När du ändrar en befintlig statisk grupp till en dynamisk grupp, alla befintliga medlemmar tas bort från gruppen och sedan medlemskapsregel bearbetas för att lägga till nya medlemmar. Om gruppen används för att styra åtkomsten till appar eller resurser, Tänk på att de ursprungliga medlemmarna kan förlora åtkomst tills medlemskapsregel bearbetas fullständigt.
>
> Vi rekommenderar att du testar den nya medlemskapsregeln i förväg för att se till att det nya medlemskapet i gruppen är som förväntat.

## <a name="change-the-membership-type-for-a-group"></a>Ändra medlemskapstypen av för en grupp

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör eller Användaradministratör konto i din klient.
2. Välj **grupper**.
3. Från den **alla grupper** öppna den grupp som du vill ändra.
4. Välj **egenskaper**.
5. På den **egenskaper** för gruppen, Välj en **Medlemskapstyp** tilldelad (statisk), dynamisk användare eller dynamisk enheten, beroende på din önskade Medlemskapstyp. Du kan använda regeln builder för dynamiskt medlemskap för att välja alternativ för en enkel regel eller skriva en medlemskapsregel själv. 

Följande är ett exempel på hur en grupp från statisk till dynamiskt medlemskap för en grupp användare.

1. På den **egenskaper** sida på den valda gruppen, Välj en **Medlemskapstyp** av **dynamisk användare**, Välj Ja i dialogrutan förklarar ändringarna i gruppen medlemskap att fortsätta. 
  
   ![Välj medlemskapstypen för dynamisk användare](./media/groups-change-type/select-group-to-convert.png)
  
2. Välj **Lägg till dynamisk fråga**, och ange sedan regeln.
  
   ![Ange regeln](./media/groups-change-type/enter-rule.png)
  
3. När du har skapat regeln, Välj **Lägg till fråga** längst ned på sidan.
4. Välj **spara** på den **egenskaper** för gruppen för att spara dina ändringar. Den **Medlemskapstyp** i gruppen uppdateras direkt i listan.

> [!TIP]
> Konverteringen kan misslyckas om medlemskapsregel som du har angett var felaktig. Ett meddelande visas i det övre högra hörnet i portalen som den innehåller en förklaring av varför regeln kan inte accepteras av systemet. Läs den noggrant för att förstå hur du kan justera regeln så att den blir giltigt. Exempel på regelsyntax och en fullständig lista över egenskaper som stöds, operatorer och värden för en medlemskapsregel finns i [regler för dynamiskt medlemskap för grupper i Azure Active Directory](groups-dynamic-membership.md).


## <a name="change-membership-type-for-a-group-powershell"></a>Ändra typ av medlemskap för en grupp (PowerShell)

> [!NOTE]
> Att ändra egenskaperna för dynamisk grupp behöver du använda cmdlet: ar från **förhandsversionen av** [Azure AD PowerShell Version 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Du kan installera förhandsversionen från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview).

Här är ett exempel på funktioner som växla hantering av medlemskap i en befintlig grupp. I det här exemplet är noggrant att ändra egenskapen GroupTypes och bevara alla värden som inte är relaterade till dynamiskt medlemskap korrekt.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Så här gör en grupp statiska:

```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Du gör en grupp dynamisk:

```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om grupper i Azure Active Directory.

* [Visa befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
