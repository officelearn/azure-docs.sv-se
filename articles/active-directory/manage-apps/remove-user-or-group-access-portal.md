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
ms.date: 02/14/2018
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aac24072bcebb0095c12cee1cfa21cfb9868dc5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548600"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Ta bort en användare eller grupp från en företagsapp i Azure Active Directory
Det är enkelt att ta bort en användare eller en grupp tilldelas åtkomst till en av dina företagsprogram i Azure Active Directory (AD Azure). Du måste ha behörighet att hantera appen enterprise och du måste vara global administratör för katalogen.

> [!NOTE]
> For Microsoft Applications (till exempel Office 365-appar), ta bort användare i en företagsapp med hjälp av PowerShell.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hur tar jag bort en användare eller grupptilldelning till en företagsapp i Azure-portalen?
1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **fler tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3. På den **Azure Active Directory - *directoryname***  (det vill säga Azure AD-sidan för den katalog som du hanterar), väljer **företagsprogram**.

    ![Att öppna företagsappar](./media/remove-user-or-group-access-portal/open-enterprise-apps.png)
4. På den **företagsprogram** väljer **alla program**. Visas en lista över appar som du kan hantera.
5. På den **företagsprogram – alla program** väljer du en app.
6. På den ***appname*** (det vill säga sida med namnet på den valda appen i rubriken), väljer **användare och grupper**.

    ![Att välja användare eller grupper](./media/remove-user-or-group-access-portal/remove-app-users.png)
7. På den ***appname*** **-användare och grupptilldelning** väljer du en av flera användare eller grupper och välj sedan den **ta bort** kommando. Bekräfta ditt beslut i Kommandotolken.

    ![Att välja kommandot Remove](./media/remove-user-or-group-access-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hur tar jag bort en användare eller grupptilldelning till en företagsapp med PowerShell?
1. Öppna en upphöjd Windows PowerShell-kommandotolk.

    >[!NOTE] 
    > Du måste installera AzureAD-modulen (Använd kommandot `Install-Module -Name AzureAD`). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory PowerShell V2-modulen, Y och tryck på RETUR.

2. Kör `Connect-AzureAD` och logga in med ett användarkonto för Global administratör.
3. Använd följande skript för att ta bort en användare och roll från ett program:

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
