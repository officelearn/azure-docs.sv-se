---
title: Tilldela och lista roller med administrativ enhets omfattning (för hands version) – Azure Active Directory | Microsoft Docs
description: Använda administrativa enheter för att begränsa omfånget för roll tilldelningar i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af281846e2bd1a39e691d84e964d8a8f780a6f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870424"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Tilldela begränsade roller till en administrativ enhet

I Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med en omfattning som är begränsad till en eller flera administrativa enheter (Australien) för mer detaljerad administrativ kontroll.

Information om hur du förbereder för att använda PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Kom igång](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Tillgängliga roller

Roll  |  Beskrivning
----- |  -----------
Administratör för autentisering  |  Har åtkomst att visa, ange och återställa information om autentiseringsinformation för användare som inte är administratörer i den tilldelade administrativa enheten.
Grupp administratör  |  Kan hantera alla aspekter av grupper och grupper inställningar som namngivnings-och förfallo principer i den tilldelade administrativa enheten.
Support administratör  |  Kan återställa lösen ord för administratörer som inte har administratörer och supportavdelningen i den tilldelade administrativa enheten.
Licens administratör  |  Kan endast tilldela, ta bort och uppdatera licens tilldelningar inom den administrativa enheten.
Lösen ords administratör  |  Kan återställa lösen ord för icke-administratörer och lösen ords administratörer inom den tilldelade administrativa enheten.
Användaradministratör  |  Kan hantera alla aspekter av användare och grupper, inklusive att återställa lösen ord för begränsade administratörer inom den tilldelade administrativa enheten.

## <a name="assign-a-scoped-role"></a>Tilldela en omfattnings roll

### <a name="azure-portal"></a>Azure Portal

Gå till **Azure AD > administrativa enheter** i portalen. Välj den administrativa enhet som du vill tilldela rollen till en användare. I den vänstra rutan väljer du roller och administratörer för att visa en lista över alla tillgängliga roller.

![Välj en administrativ enhet för att ändra roll omfånget](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Välj den roll som ska tilldelas och välj sedan **Lägg till tilldelningar**. Då öppnas en panel till höger där du kan välja en eller flera användare som ska tilldelas rollen.

![Välj rollen som definitions område och välj sedan Lägg till tilldelningar](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

Det markerade avsnittet kan ändras efter behov för den aktuella miljön.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>Visa en lista över omfångs administratörer på en AU

### <a name="azure-portal"></a>Azure Portal

Alla roll tilldelningar som har gjorts med en administrativ enhets omfattning kan visas i [avsnittet administrativa enheter i Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Gå till **Azure AD > administrativa enheter** i portalen. Välj admin-enheten för roll tilldelningarna som du vill visa. Välj **roller och administratörer** och öppna en roll för att Visa tilldelningarna i admin-enheten.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

Det markerade avsnittet kan ändras efter behov för den aktuella miljön.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Nästa steg

- [Fel sökning av administrativa enheter och vanliga frågor och svar](roles-admin-units-faq-troubleshoot.md)
