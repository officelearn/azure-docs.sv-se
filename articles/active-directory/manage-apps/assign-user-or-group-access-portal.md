---
title: Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory | Microsoft Docs
description: Så här väljer du en företagsapp att tilldela en användare eller grupp till den i Azure Active Directory
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: 6596a814841619d7b42599d381d79c98f672ec85
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196488"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory
Om du vill tilldela en användare eller grupp till en företagsapp, du måste ha behörighet att hantera appen enterprise och du måste vara global administratör för katalogen.

> [!NOTE]
> Licensieringskrav för de funktioner som beskrivs i den här artikeln, finns i den [sidan med priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> For Microsoft Applications (till exempel Office 365-appar), använder du PowerShell för att tilldela användare till en företagsapp.


## <a name="assign-a-user-to-an-app---portal"></a>Tilldela en användare till en app – portal
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

## <a name="allow-all-users-to-access-an-app---portal"></a>Tillåt alla användare åtkomst till en app – portal
Att tillåta alla användare åtkomst till ett program:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **alla tjänster**, ange Azure Active Directory i textrutan och välj sedan **RETUR**.
3. Välj **företagsprogram**.
4. På den **företagsprogram** bladet väljer **alla program**. Här visas de appar som du kan hantera.
5. På den **företagsprogram – alla program** bladet väljer du en app.
6. På den ***appname*** bladet väljer **egenskaper**.
7. På den  ***appname* -egenskaper** , anger du den **Användartilldelning krävs?** att ställa in **nr**. 

Den **Användartilldelning krävs?** alternativet:

- Påverkas inte om huruvida ett program visas på programåtkomstpanelen. Om du vill visa programmet på åtkomstpanelen, måste du tilldela en lämplig användare eller grupp till programmet.
- Endast fungerar med molnprogram som är konfigurerade för SAML enkel inloggning och lokala program som har konfigurerats med programproxy. Se [enkel inloggning för program](what-is-single-sign-on.md).
- Kräver att användarna samtycker till ett program. En administratör kan ge samtycke för alla användare.  Se [konfigurera sätt slutanvändarna samtycka till ett program](configure-user-consent.md).


## <a name="assign-a-user-to-an-app---powershell"></a>Tilldela en användare till en app – PowerShell

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
    
    ![Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

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
