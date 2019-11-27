---
title: Tilldela en användare eller grupp till en Enterprise-App i Azure AD
description: Så här väljer du en företags app för att tilldela en användare eller grupp till den i Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a5135f97ffb7d29c9fd928382ca4344beaa654d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274727"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Tilldela en användare eller grupp till en företags app i Azure Active Directory

Om du vill tilldela en användare eller grupp till en företags app bör du ha tilldelat någon av dessa administratörs roller: global administratör, program administratör, moln program administratör eller tilldelad som ägare till företags appen.  För Microsoft-program (till exempel Office 365-appar) använder du PowerShell för att tilldela användare till en företags app.

> [!NOTE]
> För licensierings krav för de funktioner som beskrivs i den här artikeln, se [sidan Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Tilldela en användare till en app – Portal

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **alla tjänster**, ange Azure Active Directory i text rutan och välj sedan **RETUR**.
1. Välj **företags program**.
1. I fönstret **företags program – alla program** visas en lista över de appar som du kan hantera. Välj en app.
1. I fönstret ***APPNAME*** (det vill säga fönstret med namnet på den valda appen i rubriken) väljer **du användare & grupper**.
1. I fönstret ***APPNAME*** **-User och Groups** väljer du **Lägg till användare**.
1. I fönstret **Lägg till tilldelning** väljer **du användare och grupper**.

   ![Tilldela en användare eller grupp till appen](./media/assign-user-or-group-access-portal/assign-users.png)

1. I fönstret **användare och grupper** väljer du en eller flera användare eller grupper i listan och väljer sedan knappen **Välj** längst ned i fönstret.
1. I fönstret **Lägg till tilldelning** väljer du **roll**. Välj sedan en roll som ska gälla för de valda användarna eller grupperna i fönstret **Välj roll** och välj sedan **OK** längst ned i fönstret.
1. I fönstret **Lägg till tilldelning** väljer du knappen **tilldela** längst ned i fönstret. Tilldelade användare eller grupper har de behörigheter som definieras av den valda rollen för den här företags appen.

## <a name="allow-all-users-to-access-an-app---portal"></a>Ge alla användare åtkomst till en app – Portal

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **alla tjänster**, ange Azure Active Directory i text rutan och välj sedan **RETUR**.
1. Välj **företags program**.
1. I fönstret **företags program** väljer du **alla program**. Här visas de appar som du kan hantera.
1. Välj en app i fönstret **företags program – alla program** .
1. I fönstret ***APPNAME*** väljer du **Egenskaper**.
1. I fönstret  ***APPNAME* -Properties** ställer du in **användar tilldelning krävs?** inställningen till **Nej**.

**Användar tilldelning krävs?** alternativ:

- Om det här alternativet är inställt på Ja måste användarna först tilldelas till det här programmet innan de kan komma åt det.
- Om det här alternativet är inställt på Nej kommer alla användare som navigerar till programmets URL för djup länk eller program-URL direkt beviljas åtkomst
- Påverkar inte om ett program visas på program åtkomst panelen. Om du vill visa programmet på åtkomst panelen måste du tilldela en lämplig användare eller grupp till programmet.
- Fungerar endast med de moln program som är konfigurerade för SAML enkel inloggning, programproxy-program som använder Azure Active Directory förautentisering eller program som skapats direkt på Azure AD-programplattformen som använder OAuth 2,0/OpenID Connect-autentisering när en användare eller administratör har samtyckt till det programmet. Se [enkel inloggning för program](what-is-single-sign-on.md). Mer information finns i [Konfigurera hur slutanvändare godkänner ett program](configure-user-consent.md).
- Det här alternativet har ingen påverkan när ett program har kon figurer ATS för något annat läge för enkel inloggning.

## <a name="assign-a-user-to-an-app---powershell"></a>Tilldela en användare till en app – PowerShell

1. Öppna en upphöjd kommando tolk i Windows PowerShell.

   > [!NOTE]
   > Du måste installera AzureAD-modulen (Använd kommandot `Install-Module -Name AzureAD`). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory v2 PowerShell-modulen skriver du Y och trycker på RETUR.

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

Mer information om hur du tilldelar en användare till en program roll finns i dokumentationen för [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Om du vill tilldela en grupp till en företags app måste du ersätta `Get-AzureADUser` med `Get-AzureADGroup`.

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

1. Tilldela roll namnet till variabeln `$app_role_name`.

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

## <a name="next-steps"></a>Nästa steg

- [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Ta bort en användare eller grupp tilldelning från en företags app](remove-user-or-group-access-portal.md)
- [Inaktivera användar inloggningar för en företags app](disable-user-sign-in-portal.md)
- [Ändra namn eller logo typ för en företags app](change-name-or-logo-portal.md)
