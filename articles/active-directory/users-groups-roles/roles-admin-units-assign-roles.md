---
title: Tilldela och lista roller med administrativt enhetsomfång (förhandsversion) - Azure Active Directory | Microsoft-dokument
description: Använda administrativa enheter för att begränsa omfattningen av rolltilldelningar i Azure Active Directory
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
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870424"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Tilldela scoped-roller till en administrativ enhet

I Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med ett scope som är begränsat till en eller flera administrativa enheter (AUs) för mer detaljerad administrativ kontroll.

Steg för att förbereda hur du använder PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Kom igång](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Tillgängliga roller

Roll  |  Beskrivning
----- |  -----------
Administratör för autentisering  |  Har åtkomst till visa, ange och återställa information om autentiseringsmetod för alla icke-administratörsanvändare endast i den tilldelade administrativa enheten.
Gruppadministratör  |  Kan hantera alla aspekter av grupper och gruppinställningar som namngivning och förfallodatum principer i den tilldelade administrativa enheten bara.
Administratör för helpdesk  |  Kan återställa lösenord för icke-administratörer och Helpdesk-administratörer i den tilldelade administrativa enheten.
Licensadministratör  |  Kan endast tilldela, ta bort och uppdatera licenstilldelningar inom den administrativa enheten.
Lösenordsadministratör  |  Kan återställa lösenord för icke-administratörer och lösenordsadministratörer inom den tilldelade administrativa enheten.
Användaradministratör  |  Kan hantera alla aspekter av användare och grupper, inklusive återställning av lösenord för begränsade administratörer inom den tilldelade administrativa enheten.

## <a name="assign-a-scoped-role"></a>Tilldela en begränsad roll

### <a name="azure-portal"></a>Azure Portal

Gå till **Azure AD > Administrativa enheter** i portalen. Välj den administrativa enhet som du vill tilldela rollen till en användare. I den vänstra rutan väljer du Roller och administratörer om du vill visa alla tillgängliga roller.

![Välj en administrativ enhet för att ändra rollomfånget](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Välj den roll som ska tilldelas och välj sedan **Lägg till tilldelningar**. Då öppnas en panel till höger där du kan välja en eller flera användare som ska tilldelas rollen.

![Välj den roll som ska scope och välj sedan Lägg till tilldelningar](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

Det markerade avsnittet kan ändras efter behov för den specifika miljön.

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

## <a name="list-the-scoped-admins-on-an-au"></a>Lista de begränsade administratörerna på en AU

### <a name="azure-portal"></a>Azure Portal

Alla rolltilldelningar som görs med ett administrativt enhetsomfång kan visas i [avsnittet Administrativa enheter](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)i Azure AD . Gå till **Azure AD > Administrativa enheter** i portalen. Välj administratörsenhet för de rolltilldelningar som du vill lista. Välj **Roller och administratörer** och öppna en roll för att visa tilldelningarna i administratörsenheten.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

Det markerade avsnittet kan ändras efter behov för den specifika miljön.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Nästa steg

- [Felsökning och vanliga frågor om administrativa enheter](roles-admin-units-faq-troubleshoot.md)
