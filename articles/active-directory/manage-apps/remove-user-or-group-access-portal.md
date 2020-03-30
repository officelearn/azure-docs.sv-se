---
title: Ta bort användar- eller grupptilldelningar från en app i Azure AD
description: Så här tar du bort åtkomsttilldelningen för en användare eller grupp från en företagsapp i Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: edf918b57212cf2adfbffb358a0257d9dbea85e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275868"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Ta bort en användar- eller grupptilldelning från en företagsapp i Azure Active Directory

Det är enkelt att ta bort en användare eller en grupp från tilldelad åtkomst till ett av dina företagsprogram i Azure Active Directory (Azure AD). Du behöver rätt behörighet för att hantera företagsappen. Och du måste vara global administratör för katalogen.

> [!NOTE]
> För Microsoft-program (till exempel Office 365-appar) använder du PowerShell för att ta bort användare till ett företagsapp.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hur tar jag bort en användar- eller grupptilldelning till en företagsapp i Azure-portalen?

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **Alla tjänster,** ange **Azure Active Directory** i textrutan och välj sedan **Retur**.
1. På sidan **Azure Active Directory - *katalognamn* ** (det vill säga sidan Azure AD för den katalog du hanterar) väljer du **Enterprise-program**.
1. På sidan **Företagsprogram – Alla program** visas en lista över de appar du kan hantera. Välj en app.
1. På översiktssidan ***för appnamn*** (det vill än sidan med namnet på den valda appen i rubriken) väljer du **Användare & Grupper**.
1. På ***sidan Appname*** **- Användare & Grupptilldelning** väljer du en av flera användare eller grupper och väljer sedan kommandot Ta **bort.** Bekräfta ditt beslut vid prompten.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hur tar jag bort en användar- eller grupptilldelning till en företagsapp med PowerShell?

1. Öppna en upphöjd Windows PowerShell-kommandotolk.

   > [!NOTE]
   > Du måste installera AzureAD-modulen `Install-Module -Name AzureAD`(använd kommandot ). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory V2 PowerShell-modulen skriver du Y och trycker på RETUR.

1. Kör `Connect-AzureAD` och logga in med ett globalt administratörsanvändarkonto.
1. Använd följande skript för att ta bort en användare och roll från ett program:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="next-steps"></a>Nästa steg

- [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
- [Inaktivera användarloggningar för en företagsapp](disable-user-sign-in-portal.md)
- [Ändra namn eller logotyp för en företagsapp](change-name-or-logo-portal.md)
