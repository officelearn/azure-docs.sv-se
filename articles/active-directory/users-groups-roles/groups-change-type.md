---
title: Ändra statiskt gruppmedlemskap till dynamiskt – Azure AD | Microsoft-dokument
description: Så här skapar du medlemskapsregler för att automatiskt fylla i grupper och en regelreferens.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34451fcc4597f77464e5e9566613e21e9fecdbc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027317"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Ändra statiskt gruppmedlemskap till dynamiskt i Azure Active Directory

Du kan ändra en grupps medlemskap från statisk till dynamisk (eller vice versa) i Azure Active Directory (Azure AD). Azure AD behåller samma gruppnamn och ID i systemet, så alla befintliga referenser till gruppen är fortfarande giltiga. Om du skapar en ny grupp i stället måste du uppdatera dessa referenser. Dynamiskt gruppmedlemskap eliminerar hanteringskostnader som lägger till och tar bort användare. I den här artikeln beskrivs hur du konverterar befintliga grupper från statiska till dynamiska medlemskap med antingen Azure AD Admin Center eller PowerShell-cmdletar.

> [!WARNING]
> När du ändrar en befintlig statisk grupp till en dynamisk grupp tas alla befintliga medlemmar bort från gruppen och sedan bearbetas medlemskapsregeln för att lägga till nya medlemmar. Om gruppen används för att kontrollera åtkomsten till appar eller resurser bör du vara medveten om att de ursprungliga medlemmarna kan förlora åtkomsten tills medlemskapsregeln har bearbetats fullständigt.
>
> Vi rekommenderar att du testar den nya medlemskapsregeln i förväg för att se till att det nya medlemskapet i gruppen blir som förväntat.

## <a name="change-the-membership-type-for-a-group"></a>Ändra medlemstyp för en grupp

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett konto som är en global administratör eller en användaradministratör i din klientorganisation.
2. Välj **grupper**.
3. Öppna den grupp som du vill ändra i listan **Alla grupper.**
4. Välj **egenskaper**.
5. På sidan **Egenskaper** för gruppen väljer du en **medlemstyp** av antingen Tilldelad (statisk), Dynamisk användare eller Dynamisk enhet, beroende på önskad medlemstyp. För dynamiskt medlemskap kan du använda regelverktyget för att välja alternativ för en enkel regel eller skriva en medlemskapsregel själv. 

Följande steg är ett exempel på att ändra en grupp från statiskt till dynamiskt medlemskap för en grupp användare.

1. På sidan **Egenskaper** för den valda gruppen väljer du en **medlemstyp** av **Dynamisk användare**och väljer sedan Ja i dialogrutan som förklarar ändringarna i gruppmedlemskapet för att fortsätta. 
  
   ![välja medlemstyp för dynamisk användare](./media/groups-change-type/select-group-to-convert.png)
  
2. Välj **Lägg till dynamisk fråga**och ange sedan regeln.
  
   ![ange regeln för den dynamiska gruppen](./media/groups-change-type/enter-rule.png)
  
3. När du har skapat regeln väljer du **Lägg till fråga** längst ned på sidan.
4. Välj **Spara** på sidan **Egenskaper** för gruppen om du vill spara ändringarna. Gruppens **medlemstyp** uppdateras omedelbart i grupplistan.

> [!TIP]
> Gruppkonvertering kan misslyckas om medlemskapsregeln du angav var felaktig. Ett meddelande visas i det övre högra hörnet på portalen att det innehåller en förklaring till varför regeln inte kan accepteras av systemet. Läs den noggrant för att förstå hur du kan justera regeln för att göra den giltig. Exempel på regelsyntax och en fullständig lista över egenskaper, operatorer och värden som stöds för en medlemskapsregel finns [i Dynamiska medlemskapsregler för grupper i Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Ändra medlemstyp för en grupp (PowerShell)

> [!NOTE]
> Om du vill ändra dynamiska gruppegenskaper måste du använda cmdlets från **förhandsversionen av** [Azure AD PowerShell Version 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Du kan installera förhandsgranskningen från [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview).

Här är ett exempel på funktioner som växlar medlemskapshantering på en befintlig grupp. I det här exemplet är det noga med att korrekt manipulera egenskapen GroupTypes och bevara alla värden som inte är relaterade till dynamiskt medlemskap.

```powershell
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
Så här gör du en grupp statisk:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Så här gör du en grupp dynamisk:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om grupper i Azure Active Directory.

* [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
