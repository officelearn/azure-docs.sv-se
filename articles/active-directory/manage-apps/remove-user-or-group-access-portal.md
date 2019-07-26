---
title: Ta bort användar-eller grupp tilldelningar från en app i Azure Active Directory | Microsoft Docs
description: Så här tar du bort åtkomst tilldelningen för en användare eller grupp från en företags app i Azure Active Directory
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
ms.openlocfilehash: 380816283156969c47f45a9b47435688df91f4ca
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381042"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Ta bort en användare eller grupp tilldelning från en företags app i Azure Active Directory

Det är enkelt att ta bort en användare eller en grupp från tilldelad åtkomst till ett företags program i Azure Active Directory (Azure AD). Du behöver rätt behörighet för att hantera företags appen. Och du måste vara global administratör för katalogen.

> [!NOTE]
> För Microsoft-program (till exempel Office 365-appar) använder du PowerShell för att ta bort användare till en företags app.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hur gör jag för att ta bort en användare eller grupp tilldelning till en företags app i Azure Portal?

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **alla tjänster**, ange **Azure Active Directory** i text rutan och välj sedan **RETUR**.
1. På sidan **Azure Active Directory- *DirectoryName***  (det vill säga Azure AD-sidan för den katalog som du hanterar) väljer du **företags program**.
1. På sidan **företags program – alla program** visas en lista över de appar som du kan hantera. Välj en app.
1. På sidan ***APPNAME*** -översikt (det vill säga sidan med namnet på den valda appen i rubriken) väljer **du användare & grupper**.
1. På sidan ***APPNAME*** **-User & grupp tilldelning** väljer du en eller flera användare eller grupper och väljer sedan kommandot **ta bort** . Bekräfta ditt beslut vid prompten.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hur gör jag för att ta bort en användare eller grupp tilldelning till en företags app med hjälp av PowerShell?

1. Öppna en upphöjd kommando tolk i Windows PowerShell.

   > [!NOTE]
   > Du måste installera AzureAD-modulen (Använd kommandot `Install-Module -Name AzureAD`). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory v2 PowerShell-modulen skriver du Y och trycker på RETUR.

1. Kör `Connect-AzureAD` och logga in med ett globalt administratörs användar konto.
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
- [Tilldela en användare eller grupp till en företags app](assign-user-or-group-access-portal.md)
- [Inaktivera användar inloggningar för en företags app](disable-user-sign-in-portal.md)
- [Ändra namn eller logo typ för en företags app](change-name-or-logo-portal.md)
