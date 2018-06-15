---
title: Ta bort en användare eller grupp från en enterprise-app i Azure Active Directory | Microsoft Docs
description: Hur du tar bort tilldelningen åtkomst av en användare eller grupp från en enterprise-app i Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: b6da8eed16b67db098ceb90079b7da7dfadcd5e3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303940"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Ta bort en användare eller grupp från en enterprise-app i Azure Active Directory
Det är enkelt att ta bort en användare eller grupp tilldelas åtkomst till en enterprise-program i Azure Active Directory (AD Azure). Du måste ha behörighet att hantera enterprise-appen och du måste vara global administratör för katalogen.

> [!NOTE]
> För Microsoft Applications (till exempel Office 365-appar), använder du PowerShell för att ta bort användare i en enterprise-app.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hur tar jag bort en användare eller grupptilldelning till en enterprise-app i Azure portal?
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **fler tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3. På den **Azure Active Directory - *directoryname***  sidan (det vill säga den Azure AD för den katalog som du hanterar), Välj **företagsprogram**.

    ![Öppna företagsappar](./media/remove-user-or-group-access-portal/open-enterprise-apps.png)
4. På den **företagsprogram** väljer **alla program**. Visas en lista över appar som du kan hantera.
5. På den **företagsprogram - alla program** väljer du en app.
6. På den ***appname*** (det vill säga sida med namnet på den valda appen i namnet), väljer du **användare och grupper**.

    ![Att välja användare eller grupper](./media/remove-user-or-group-access-portal/remove-app-users.png)
7. På den ***appname*** **-användaren & grupptilldelning** väljer du ett av flera användare eller grupper och välj sedan den **ta bort** kommando. Bekräfta beslutet i Kommandotolken.

    ![Att välja kommandot Ta bort](./media/remove-user-or-group-access-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hur tar jag bort en användare eller grupptilldelning till en enterprise-programmet med PowerShell?
1. Öppna en kommandotolk i Windows PowerShell.

    >[!NOTE] 
    > Du måste installera modulen AzureAD (Använd kommandot `Install-Module -Name AzureAD`). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory PowerShell V2-modulen, Skriv Y och tryck på RETUR.

2. Kör `Connect-AzureAD` och logga in med ett användarkonto för Global administratör.
3. Använd följande skript för att tilldela en användare och roller till ett program:

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

- [Visa alla mina grupper](../active-directory-groups-view-azure-portal.md)
- [Tilldela en användare eller grupp till en enterprise-app](assign-user-or-group-access-portal.md)
- [Inaktivera användarinloggningar för en enterprise-app](disable-user-sign-in-portal.md)
- [Ändra namnet eller logotypen av en enterprise-app](change-name-or-logo-portal.md)
