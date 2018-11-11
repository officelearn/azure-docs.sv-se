---
title: Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory | Microsoft Docs
description: Så här väljer du en företagsapp att tilldela en användare eller grupp till den i Azure Active Directory
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
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: ee0b14123e193f219e403d2608368c27f953013d
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037982"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory
Om du vill tilldela en användare eller grupp till en företagsapp, du måste ha behörighet att hantera appen enterprise och du måste vara global administratör för katalogen.

> [!NOTE]
> Licensieringskrav för de funktioner som beskrivs i den här artikeln, finns i den [sidan med priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> For Microsoft Applications (till exempel Office 365-appar), använder du PowerShell för att tilldela användare till en företagsapp.


## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Hur tilldelar åtkomst till en enterprise-app i Azure-portalen?
1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **alla tjänster**, ange Azure Active Directory i textrutan och välj sedan **RETUR**.
3. Välj **företagsprogram**.

    ![Att öppna företagsappar](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. På den **företagsprogram** bladet väljer **alla program**. Här visas de appar som du kan hantera.
5. På den **företagsprogram – alla program** bladet väljer du en app.
6. På den ***appname*** bladet (det vill säga till bladet med namnet på den valda appen i rubriken) och välj **användare och grupper**.

    ![Att välja kommandot alla program](./media/assign-user-or-group-access-portal/select-app-users.png)
7. På den ***appname*** **-användare och grupptilldelning** bladet och välja den **Lägg till** kommando.
8. På den **Lägg till tilldelning** bladet väljer **användare och grupper**.

    ![Tilldela en användare eller grupp till appen](./media/assign-user-or-group-access-portal/assign-users.png)
9. På den **användare och grupper** bladet Välj en eller flera användare eller grupper i listan och välj sedan den **Välj** längst ned på bladet.
10. På den **Lägg till tilldelning** bladet väljer **rollen**. Klicka på den **Välj roll** bladet Välj en roll ska tillämpas på de valda användare eller grupper och välj sedan den **OK** längst ned på bladet.
11. På den **Lägg till tilldelning** bladet och välja den **tilldela** längst ned på bladet. Tilldelade användare eller grupper som har de behörigheter som definieras av den valda rollen för den här enterprise-appen.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Hur tilldelar jag en användare till en företagsapp med PowerShell?

1. Öppna en upphöjd Windows PowerShell-kommandotolk.

    >[!NOTE] 
    > Du måste installera AzureAD-modulen (Använd kommandot `Install-Module -Name AzureAD`). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory PowerShell V2-modulen, Y och tryck på RETUR.

2. Kör `Connect-AzureAD` och logga in med ett användarkonto för Global administratör.
3. Använd följande skript för att tilldela en användare och roll till ett program:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

Mer information om hur du tilldelar en användare till en programroll finns i dokumentationen för [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Om du vill tilldela en grupp till en företagsapp, du måste ersätta `Get-AzureADUser` med `Get-AzureADGroup`.

### <a name="example"></a>Exempel

Det här exemplet tilldelar användare Britta Simon till den [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) program med hjälp av PowerShell.

1. Tilldela motsvarande värden för variabler $username, $app_name och $app_role_name i PowerShell. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. I det här exemplet vet vi inte vad är det exakta namnet på programmet rollen som vi vill tilldela till Britta Simon. Kör följande kommandon för att hämta användare ($user) och visningsnamn för tjänstens huvudnamn ($sp) med hjälp av användar-UPN och tjänstens huvudnamn.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Kör kommandot `$sp.AppRoles` att visa rollerna som är tillgängliga för Workplace Analytics-programmet. I det här exemplet vill vi tilldela Britta Simon rollen analytiker (begränsad åtkomst).
    
    ![Workplace Analytics roll](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. Tilldela rollnamnet den `$app_role_name` variabeln.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. Kör följande kommando för att tilldela användaren till rollen app:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Nästa steg
* [Se alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Ta bort en användare eller grupp från en företagsapp](remove-user-or-group-access-portal.md)
* [Inaktivera användarinloggningar för en företagsapp](disable-user-sign-in-portal.md)
* [Ändra namnet eller logotyp i en företagsapp](change-name-or-logo-portal.md)
