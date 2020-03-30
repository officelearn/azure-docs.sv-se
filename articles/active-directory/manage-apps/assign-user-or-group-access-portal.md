---
title: Tilldela en användare eller grupp till en företagsapp i Azure AD
description: Så här väljer du en företagsapp som ska tilldela en användare eller grupp till den i Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409200"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Tilldela en användare eller grupp till en företagsapp i Azure Active Directory

Den här artikeln visar hur du tilldelar användare eller grupper till företagsprogram i Azure Active Directory (Azure AD), antingen inifrån Azure-portalen eller med hjälp av PowerShell. När du tilldelar en användare till ett program visas programmet på [användarens åtkomstpanel Mina appar](https://myapps.microsoft.com/) för enkel åtkomst. Om programmet exponerar roller kan du också tilldela en viss roll till användaren.

För större kontroll kan vissa typer av företagsprogram konfigureras för att [kräva användartilldelning](#configure-an-application-to-require-user-assignment). 

Om du vill [tilldela en användare eller grupp till en företagsapp](#assign-users-or-groups-to-an-app-via-the-azure-portal)måste du logga in som global administratör, programadministratör, molnprogramadministratör eller tilldelad ägare till företagsappen.

> [!NOTE]
> Gruppbaserad tilldelning kräver Azure Active Directory Premium P1 eller P2-utgåva. Gruppbaserad tilldelning stöds endast för säkerhetsgrupper. Kapslade gruppmedlemskap och Office 365-grupper stöds för närvarande inte. Mer licenskrav för de funktioner som beskrivs i den här artikeln finns på [prissidan för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurera ett program så att det kräver användartilldelning

Med följande typer av program har du möjlighet att kräva att användare ska tilldelas programmet innan de kan komma åt det:

- Program som konfigurerats för federerad enkel inloggning (SSO) med SAML-baserad autentisering
- Programproxyprogram som använder Azure Active Directory före autentisering
- Program som bygger på Azure AD-programplattformen som använder OAuth 2.0 / OpenID Connect-autentisering efter att en användare eller administratör har samtyckt till det programmet.

När användartilldelning krävs kan endast de användare som du uttryckligen tilldelar programmet logga in. De kan komma åt appen på sidan Mina appar eller via en direktlänk. 

När tilldelning *inte krävs*, antingen för att du har angett det här alternativet till **Nej** eller för att programmet använder ett annat SSO-läge, kan alla användare komma åt programmet om de har en direktlänk till programmet eller **URL:en för användaråtkomst** på programmets **egenskapssida.** 

Den här inställningen påverkar inte om ett program visas på åtkomstpanelen Mina appar eller inte. Program visas på användarnas åtkomstpaneler för Mina appar när du har tilldelat en användare eller grupp till programmet. Bakgrund finns i [Hantera åtkomst till appar](what-is-access-management.md).


Så här kräver du användartilldelning för ett program:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett administratörskonto eller som ägare av programmet.

2. Välj **Azure Active Directory**. Välj **Enterprise-program**på menyn till vänster .

3. Välj programmet i listan. Om du inte ser programmet börjar du skriva namnet i sökrutan. Eller använd filterkontrollerna för att välja programtyp, status eller synlighet och välj sedan **Använd**.

4. Välj **Egenskaper**på menyn till vänster .

5. Kontrollera att **användartilldelningen krävs?** växlingsknappen är inställd på **Ja**.

   > [!NOTE]
   > Om **växlingsknappen För användartilldelning** inte är tillgänglig kan du använda PowerShell för att ange egenskapen appRoleAssignmentRequired på tjänstens huvudnamn.

6. Välj knappen **Spara** högst upp på skärmen.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Tilldela användare eller grupper till en app via Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett globalt administratörs-, programadministratörs- eller molnprogramadministratörskonto eller som tilldelad ägare till företagsappen.
2. Välj **Azure Active Directory**. Välj **Enterprise-program**på menyn till vänster .
3. Välj programmet i listan. Om du inte ser programmet börjar du skriva namnet i sökrutan. Eller använd filterkontrollerna för att välja programtyp, status eller synlighet och välj sedan **Använd**.
4. Välj **Användare och grupper**i menyn till vänster .
   > [!NOTE]
   > Om du vill tilldela användare till Microsoft Applications, till exempel Office 365-appar, använder vissa av dessa program PowerShell. 
5. Välj knappen **Lägg till användare.**
6. Välj **Användare och grupper**i fönstret Lägg till **tilldelning** .
7. Markera den användare eller grupp som du vill tilldela programmet eller börja skriva namnet på användaren eller gruppen i sökrutan. Du kan välja flera användare och grupper och dina val visas under **Markerade objekt**.
8. När du är klar klickar du på **Markera**.

   ![Tilldela en användare eller grupp till appen](./media/assign-user-or-group-access-portal/assign-users.png)

9. Markera en eller flera användare eller grupper i listan i fönstret **Användare och grupper** och välj sedan knappen **Välj** längst ned i fönstret.
10. Om programmet stöder det kan du tilldela en roll till användaren eller gruppen. Välj **Roll** i fönstret Lägg till **tilldelning**. Välj sedan en roll som ska tillämpas på de markerade användarna eller grupperna i fönstret **Välj roll** och välj sedan **OK** längst ned i fönstret. 

    > [!NOTE]
    > Om programmet inte stöder rollval tilldelas standardåtkomstrollen. I det här fallet hanterar programmet nivån på åtkomstanvändare har.

2. Markera knappen **Tilldela** längst ned i fönstret I fönstret **Lägg till tilldelning.**

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Tilldela användare eller grupper till en app via PowerShell

1. Öppna en upphöjd Windows PowerShell-kommandotolk.

   > [!NOTE]
   > Du måste installera AzureAD-modulen `Install-Module -Name AzureAD`(använd kommandot ). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory V2 PowerShell-modulen skriver du Y och trycker på RETUR.

1. Kör `Connect-AzureAD` och logga in med ett globalt administratörsanvändarkonto.
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

Mer information om hur du tilldelar en användare till en programroll finns i dokumentationen för [Ny-AzureADUserAppRoleAstilldelning](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Om du vill tilldela en grupp till `Get-AzureADUser` `Get-AzureADGroup`en företagsapp måste du ersätta med .

### <a name="example"></a>Exempel

I det här exemplet tilldelas användaren Britta Simon till [Microsoft Workplace Analytics-programmet](https://products.office.com/business/workplace-analytics) med PowerShell.

1. I PowerShell tilldelar du motsvarande värden till de variabler $username, $app_name och $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. I det här exemplet vet vi inte vad som är det exakta namnet på den programroll som vi vill tilldela Britta Simon. Kör följande kommandon för att hämta användaren ($user) och tjänstens huvudnamn ($sp) med hjälp av användarens UPN och tjänstens huvudnamn.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Kör kommandot `$sp.AppRoles` för att visa de roller som är tillgängliga för Workplace Analytics-programmet. I det här exemplet vill vi tilldela rollen Britta Simon analytiker (begränsad åtkomst).

   ![Visar de roller som är tillgängliga för en användare med hjälp av Workplace Analytics-roll](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Tilldela rollnamnet till `$app_role_name` variabeln.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Kör följande kommando för att tilldela användaren till approllen:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Relaterade artiklar

- [Läs mer om slutanvändarens åtkomst till program](end-user-experiences.md)
- [Planera en azure AD-åtkomstpaneldistribution](access-panel-deployment-plan.md)
- [Hantera åtkomst till appar](what-is-access-management.md)
 
## <a name="next-steps"></a>Nästa steg

- [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Ta bort en användar- eller grupptilldelning från en företagsapp](remove-user-or-group-access-portal.md)
- [Inaktivera användarloggningar för en företagsapp](disable-user-sign-in-portal.md)
- [Ändra namn eller logotyp för en företagsapp](change-name-or-logo-portal.md)
