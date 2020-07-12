---
title: Hantera användar-och administratörs behörigheter – Azure Active Directory | Microsoft Docs
description: Lär dig hur du granskar och hanterar behörigheter för programmet i Azure AD. Om du till exempel vill återkalla alla behörigheter som beviljats till ett program.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277651"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Vidta åtgärder för överanvändning eller misstänkt program i Azure Active Directory

Lär dig hur du granskar och hanterar program behörigheter. Baserat på scenariot tillhandahåller den här artikeln olika åtgärder som du kan utföra för att skydda ditt program. Detta gäller för alla program som har lagts till i din Azure Active Directory (Azure AD)-klient via användare eller administrativt medgivande.

Mer information om samtycker till program finns i [Azure Active Directory medgivande Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Krav

För att kunna utföra åtgärderna nedan måste du logga in som global administratör, en program administratör eller en moln program administratör.

Om du vill begränsa åtkomsten till program måste du kräva användar tilldelning och sedan tilldela användare eller grupper till programmet.  Mer information finns i [metoder för att tilldela användare och grupper](methods-for-assigning-users-and-groups.md).

Du kan komma åt Azure AD-portalen för att hämta sammanhangsbaserade PowerShell-skript för att utföra åtgärderna.
 
1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **behörigheter**. I kommando fältet väljer du **gransknings behörigheter**.

![Granska behörigheterna](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Jag vill kontrol lera åtkomst till ett program

Vi rekommenderar att du begränsar åtkomsten till det här programmet genom att aktivera inställningen för användar tilldelning.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper** och ange sedan användar krav Kräv inställningen till Ja.
5. Välj **användare och grupper** och ta sedan bort oönskade användare som är kopplade till programmet.
6. Tilldela användare eller grupp (er) till programmet.

Valfritt kan du ta bort alla användare som har tilldelats programmet med hjälp av PowerShell.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Jag vill återkalla alla behörigheter för ett program

Med hjälp av PowerShell återkallar du alla behörigheter som har beviljats för programmet.

> [!NOTE]
> Om du återkallar den aktuella beviljade behörigheten stoppas inte användare för reconseing till programmen. Om du vill blockera användare från att godkänna programmet kan du läsa konfigurera hur slutanvändare [godkänner program](configure-user-consent.md).

Valfritt kan du inaktivera programmet för att blockera användare från att komma åt appen och programmet från att komma åt dina data.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper** och Ställ in aktiverat för användare för inloggning? till Nej.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>Programmet är misstänkt och jag vill undersöka

Vi rekommenderar att du begränsar åtkomsten till det här programmet genom att aktivera inställningen för användar tilldelning på och granska de behörigheter som användare och administratörer har beviljat programmet.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
3. Välj **Azure Active Directory**  >  **företags program**.
5. Välj det program som du vill begränsa åtkomsten till.
6. Välj **Egenskaper** och ange sedan användar krav Kräv inställningen till Ja.
7. Välj **behörigheter** och granska administratörs-och användarens behörigheter.

Valfritt kan du:

- Med PowerShell kan du ta bort alla användare som har tilldelats för att hindra dem från att logga in i programmet.
- Använd PowerShell och gör en ogiltig verifiering av uppdateringstoken för användare som har åtkomst till programmet.
- Återkalla alla behörigheter för det här programmet med hjälp av PowerShell
- Inaktivera programmet för att blockera användares åtkomst och stoppa programmens åtkomst till dina data.


## <a name="application-is-malicious-and-im-compromised"></a>Programmet är skadligt och har komprometterats

Vi rekommenderar att du inaktiverar programmet för att blockera användare från att komma åt appen och programmet från att komma åt dina data. Om du tar bort programmet i stället kommer slutanvändarna att kunna godkänna programmet och bevilja åtkomst till dina data.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper** och kopiera sedan objekt-ID: t.

### <a name="powershell-commands"></a>PowerShell-kommandon


Hämta objekt-ID för tjänstens huvud namn

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper** och kopiera sedan objekt-ID: t.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Ta bort alla användare som tilldelats programmet.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Återkalla behörigheter som beviljats till programmet

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Invalidera uppdateringstoken
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Nästa steg
- [Hantera medgivande till program och utvärdera medgivande förfrågan](manage-consent-requests.md)
- [Konfigurera användarmedgivande](configure-user-consent.md)
- [Konfigurera arbets flöde för administratörs medgivande](configure-admin-consent-workflow.md)
