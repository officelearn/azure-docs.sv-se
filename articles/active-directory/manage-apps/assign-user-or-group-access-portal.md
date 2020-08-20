---
title: Tilldela en användare eller grupp till en Enterprise-App i Azure AD
description: Så här väljer du en företags app för att tilldela en användare eller grupp till den i Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7875bfc55d4530f7f56a96599491cab4a98ced04
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642035"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Tilldela en användare eller grupp till en företagsapp i Azure Active Directory

Den här artikeln visar hur du tilldelar användare eller grupper till företags program i Azure Active Directory (Azure AD), antingen i Azure Portal eller med hjälp av PowerShell. När du tilldelar en användare till ett program visas programmet i användarens [Mina appar](https://myapps.microsoft.com/) för enkel åtkomst. Om programmet exponerar roller kan du också tilldela användaren en specifik roll.

För bättre kontroll kan vissa typer av företags program konfigureras för att [kräva användar tilldelning](#configure-an-application-to-require-user-assignment). 

Om du vill [tilldela en användare eller grupp till en företags app](#assign-users-or-groups-to-an-app-via-the-azure-portal)måste du logga in som global administratör, program administratör, moln program administratör eller tilldelad ägare till företags appen.

> [!IMPORTANT]
> När du tilldelar en grupp till ett program får endast användare i gruppen åtkomst. Tilldelningen överlappar inte kapslade grupper.

> [!NOTE]
> Gruppbaserad tilldelning kräver Azure Active Directory Premium P1 eller P2 Edition. Gruppbaserad tilldelning stöds endast för säkerhets grupper. Kapslade grupp medlemskap och Office 365-grupper stöds inte för närvarande. Mer licensierings krav för de funktioner som beskrivs i den här artikeln finns på [sidan Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurera ett program för att kräva användar tilldelning

Med följande typer av program kan du välja att kräva att användarna ska tilldelas programmet innan de kan komma åt det:

- Program som kon figurer ATS för federerad enkel inloggning (SSO) med SAML-baserad autentisering
- Program för programproxy som använder Azure Active Directory förautentisering
- Program som bygger på Azure AD-programplattformen som använder OAuth 2,0/OpenID Connect-autentisering när en användare eller administratör har samtyckt till det programmet.

När användar tilldelning krävs kommer endast de användare som du uttryckligen tilldelar till programmet (antingen genom direkt användar tilldelning eller baserat på grupp medlemskap) att kunna logga in. De kan komma åt appen på sidan Mina appar eller genom att använda en direkt länk. 

Om tilldelning *inte krävs*, antingen på grund av att du har ställt in det här alternativet på **Nej** eller på att programmet använder ett annat SSO-läge, kan alla användare komma åt programmet om de har en direkt länk till programmet eller URL: en för **användar åtkomst** på programmets **egenskaps** sida. 

Den här inställningen påverkar inte om ett program visas i Mina appar. Program visas på användarnas åtkomst paneler för Mina appar när du har tilldelat en användare eller grupp till programmet. I bakgrunden, se [Hantera åtkomst till appar](what-is-access-management.md).


Så här kräver du användar tilldelning för ett program:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett administratörs konto eller som ägare till programmet.

2. Välj **Azure Active Directory**. I den vänstra navigerings menyn väljer du **företags program**.

3. Välj programmet i listan. Om du inte ser programmet börjar du skriva dess namn i sökrutan. Eller Använd filter kontrollerna för att välja program typ, status eller synlighet och välj sedan **Använd**.

4. I den vänstra navigerings menyn väljer du **Egenskaper**.

5. Kontrol lera att **användar tilldelningen krävs?** växlingen är inställd på **Ja**.

   > [!NOTE]
   > Om **användar tilldelningen krävs?** växla är inte tillgänglig, du kan använda PowerShell för att ange egenskapen appRoleAssignmentRequired för tjänstens huvud namn.

6. Välj knappen **Spara** överst på skärmen.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Tilldela användare eller grupper till en app via Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) med ett globalt administratörs-, program administratörs-eller moln program administratörs konto eller som tilldelad ägare till företags appen.
2. Välj **Azure Active Directory**. I den vänstra navigerings menyn väljer du **företags program**.
3. Välj programmet i listan. Om du inte ser programmet börjar du skriva dess namn i sökrutan. Eller Använd filter kontrollerna för att välja program typ, status eller synlighet och välj sedan **Använd**.
4. I den vänstra navigerings menyn väljer **du användare och grupper**.
   > [!NOTE]
   > Om du vill tilldela användare till Microsoft-program, till exempel Office 365-appar, använder vissa av dessa appar PowerShell. 
5. Välj knappen **Lägg till användare** .
6. I fönstret **Lägg till tilldelning** väljer **du användare och grupper**.
7. Välj den användare eller grupp som du vill tilldela till programmet eller börja skriva in namnet på användaren eller gruppen i sökrutan. Du kan välja flera användare och grupper och dina val visas under **valda objekt**.
8. När du är färdig klickar du på **Välj**.

   ![Tilldela en användare eller grupp till appen](./media/assign-user-or-group-access-portal/assign-users.png)

9. I fönstret **användare och grupper** väljer du en eller flera användare eller grupper i listan och väljer sedan knappen **Välj** längst ned i fönstret.
10. Om programmet stöder det kan du tilldela en roll till användaren eller gruppen. I fönstret **Lägg till tilldelning** väljer du **Välj roll**. Välj sedan en roll som ska gälla för de valda användarna eller grupperna i fönstret **Välj roll** och välj sedan **OK** längst ned i fönstret. 

    > [!NOTE]
    > Om programmet inte har stöd för roll val tilldelas standard åtkomst rollen. I det här fallet hanterar programmet åtkomst nivån för användare.

2. I fönstret **Lägg till tilldelning** väljer du knappen **tilldela** längst ned i fönstret.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Tilldela användare eller grupper till en app via PowerShell

1. Öppna en upphöjd kommando tolk i Windows PowerShell.

   > [!NOTE]
   > Du måste installera AzureAD-modulen (Använd kommandot `Install-Module -Name AzureAD` ). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory v2 PowerShell-modulen skriver du Y och trycker på RETUR.

1. Kör `Connect-AzureAD` och logga in med ett globalt administratörs användar konto.
1. Använd följande skript för att tilldela en användare och roll till ett program:

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

Mer information om hur du tilldelar en användare till en program roll finns i dokumentationen för [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Om du vill tilldela en grupp till en företags app måste du ersätta `Get-AzureADUser` med `Get-AzureADGroup` och ersätta `New-AzureADUserAppRoleAssignment` med `New-AzureADGroupAppRoleAssignment` .

Mer information om hur du tilldelar en grupp till en program roll finns i dokumentationen för [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0).

### <a name="example"></a>Exempel

I det här exemplet tilldelas användaren Britta Simon till Microsoft-programmet för [företags analys](https://products.office.com/business/workplace-analytics) med hjälp av PowerShell.

1. I PowerShell tilldelar du motsvarande värden till variablerna $username $app _name och $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. I det här exemplet vet vi inte vad som är det exakta namnet på den program roll som vi vill tilldela till Britta Simon. Kör följande kommandon för att hämta användaren ($user) och tjänstens huvud namn ($sp) med hjälp av användarens UPN och visnings namnen för tjänstens huvud namn.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Kör kommandot `$sp.AppRoles` för att visa de roller som är tillgängliga för programmet för arbets styrnings analys. I det här exemplet vill vi tilldela Britta Simon rollen analytiker (begränsad åtkomst).

   ![Visar de roller som är tillgängliga för en användare med rollen arbets plats analys](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Tilldela roll namnet till `$app_role_name` variabeln.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Kör följande kommando för att tilldela användaren till app-rollen:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Relaterade artiklar

- [Läs mer om slut användar åtkomst till program](end-user-experiences.md)
- [Planera distributionen av Azure AD Mina appar](access-panel-deployment-plan.md)
- [Hantera åtkomst till appar](what-is-access-management.md)
 
## <a name="next-steps"></a>Nästa steg

- [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Ta bort en användare eller grupp tilldelning från en företags app](remove-user-or-group-access-portal.md)
- [Inaktivera användar inloggningar för en företags app](disable-user-sign-in-portal.md)
- [Ändra namn eller logo typ för en företags app](change-name-or-logo-portal.md)
