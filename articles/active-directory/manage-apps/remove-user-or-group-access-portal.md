---
title: Ta bort en användare eller grupp från en företagsapp i Azure Active Directory | Microsoft Docs
description: Ta bort tilldelningen för åtkomst av en användare eller grupp från en företagsapp i Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97759ae992ebe38aa85e9b4724edeebb5285db4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443089"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Ta bort en användare eller grupp från en företagsapp i Azure Active Directory
Det är enkelt att ta bort en användare eller grupp från tilldelad åtkomst till en av dina företagsprogram i Azure Active Directory (AD Azure). Du behöver behörighet att hantera appen enterprise. Och du måste vara global administratör för katalogen.

> [!NOTE]
> For Microsoft Applications (till exempel Office 365-appar), ta bort användare i en företagsapp med hjälp av PowerShell.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hur tar jag bort en användare eller grupptilldelning till en företagsapp i Azure-portalen?
1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
1. På den **Azure Active Directory - *directoryname***  (det vill säga Azure AD-sidan för den katalog som du hanterar), väljer **företagsprogram**.
1. På den **företagsprogram – alla program** sidan visas en lista över appar som du kan hantera. Välj en app.
1. På den ***appname*** översiktssidan (det vill säga sida med namnet på den valda appen i rubriken), Välj **användare och grupper**.
1. På den ***appname*** **-användare och grupptilldelning** väljer du en av flera användare eller grupper och välj sedan den **ta bort** kommando. Bekräfta ditt beslut i Kommandotolken.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hur tar jag bort en användare eller grupptilldelning till en företagsapp med PowerShell?
1. Öppna en upphöjd Windows PowerShell-kommandotolk.

    >[!NOTE] 
    > Du måste installera AzureAD-modulen (Använd kommandot `Install-Module -Name AzureAD`). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory PowerShell V2-modulen, Y och tryck på RETUR.

1. Kör `Connect-AzureAD` och logga in med ett användarkonto för Global administratör.
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

- [Se alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
- [Inaktivera användarinloggningar för en företagsapp](disable-user-sign-in-portal.md)
- [Ändra namnet eller logotyp i en företagsapp](change-name-or-logo-portal.md)
