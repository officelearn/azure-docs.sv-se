---
title: "Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory | Microsoft Docs"
description: "Hur du väljer en enterprise-app för att tilldela en användare eller grupp till den i Azure Active Directory"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: daveba
ms.reviewer: luleon
ms.openlocfilehash: 65727ee9330a1a6650eb54595ebc93a7a693923c
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2017
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory
Om du vill tilldela en användare eller grupp till en enterprise-app, du måste ha behörighet att hantera enterprise-appen och du måste vara global administratör för katalogen.
> [!NOTE]
> För Microsoft Applications (till exempel Office 365-appar), använder du PowerShell för att tilldela användare till en enterprise-app.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Hur tilldelar åtkomst till en enterprise-app i Azure portal?
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **fler tjänster**, ange Azure Active Directory i textrutan och välj sedan **RETUR**.
3. På den **Azure Active Directory - *directoryname***  bladet (det vill säga Azure AD bladet för den katalog som du hanterar), Välj **företagsprogram**.

    ![Öppna företagsappar](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. På den **företagsprogram** bladet väljer **alla program**. Detta visar de appar som du kan hantera.
5. På den **företagsprogram - alla program** bladet väljer du en app.
6. På den ***appname*** bladet (det vill säga bladet med namnet på den valda appen i namnet), Välj **användare och grupper**.

    ![Att välja kommandot alla program](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. På den ***appname*** **-användaren & grupptilldelning** bladet väljer den **Lägg till** kommando.
8. På den **Lägg uppdrag** bladet väljer **användare och grupper**.

    ![Tilldela en användare eller grupp i appen](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. På den **användare och grupper** bladet Välj en eller flera användare eller grupper i listan och välj sedan den **Välj** längst ned på bladet.
10. På den **Lägg uppdrag** bladet väljer **rollen**. Klicka sedan på den **Välj roll** bladet Välj en roll ska tillämpas på de valda användare eller grupper och välj sedan den **OK** längst ned på bladet.
11. På den **Lägg uppdrag** bladet väljer den **tilldela** längst ned på bladet. Tilldelade användare eller grupper som har behörigheter som definieras av den valda rollen för den här enterprise-appen.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Hur tilldelar en användare till en enterprise-app med hjälp av PowerShell?

1. Öppna en kommandotolk i Windows PowerShell.

    >[!NOTE] 
    > Du måste installera modulen AzureAD (Använd kommandot `Install-Module -Name AzureAD`). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory PowerShell V2-modulen, Skriv Y och tryck på RETUR.

2. Kör `Connect-AzureAD` och logga in med ett användarkonto för Global administratör.
3. Använd följande skript för att tilldela en användare och roller till ett program:

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

Mer information om hur du tilldelar en användare till en roll för program finns i dokumentationen för [ny AzureADUserAppRoleAssignment](https://docs.microsoft.com/en-us/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

### <a name="example"></a>Exempel

Det här exemplet tilldelas användaren Britta Simon till den [Microsoft arbetsplats Analytics](https://products.office.com/en-us/business/workplace-analytics) program med hjälp av PowerShell.

1. Tilldela värden till variabler $username, $programnamn och $app_role_name i PowerShell. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. I det här exemplet vet vi inte vad är det exakta namnet på programrollen som vi vill tilldela Britta Simon. Kör följande kommandon för att hämta användare ($user) och tjänstens huvudnamn ($sp) med hjälp av användarens UPN och tjänstens huvudnamn visningsnamn.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Kör kommandot `$sp.AppRoles` ska visas rollerna som är tillgängliga för arbetsplats Analytics-programmet. I det här exemplet ska vi tilldelas Britta Simon rollen analytiker (begränsad åtkomst).
    
    ![Arbetsplats Analytics roll](media/active-directory-coreapps-assign-user-azure-portal/workplace-analytics-role.png)

4. Tilldela rollnamn för den `$app_role_name` variabeln.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    ```

5. Kör följande kommando för att tilldela användaren rollen app:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Nästa steg
* [Visa alla mina grupper](active-directory-groups-view-azure-portal.md)
* [Ta bort en användare eller grupp från en enterprise-app](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Inaktivera användarinloggningar för en enterprise-app](active-directory-coreapps-disable-app-azure-portal.md)
* [Ändra namnet eller logotypen av en enterprise-app](active-directory-coreapps-change-app-logo-user-azure-portal.md)
