---
title: Tilldela och lista roller med den administrativa enhetens omfattning – Azure Active Directory | Microsoft Docs
description: Använda administrativa enheter för att begränsa omfånget för roll tilldelningar i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66a4810b3a84cac55a49744025b6ac71c3f1c0a7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377948"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Tilldela begränsade roller till en administrativ enhet

I Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med en omfattning som är begränsad till en eller flera administrativa enheter (Australien) för mer detaljerad administrativ kontroll.

Information om hur du förbereder för att använda PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Kom igång](admin-units-manage.md#get-started).

## <a name="roles-available"></a>Tillgängliga roller

Roll  |  Beskrivning
----- |  -----------
Administratör för autentisering  |  Har åtkomst att visa, ange och återställa information om autentiseringsinformation för användare som inte är administratörer i den tilldelade administrativa enheten.
Grupp administratör  |  Kan hantera alla aspekter av grupper och grupper inställningar som namngivnings-och förfallo principer i den tilldelade administrativa enheten.
Support administratör  |  Kan återställa lösen ord för administratörer som inte har administratörer och supportavdelningen i den tilldelade administrativa enheten.
Licens administratör  |  Kan endast tilldela, ta bort och uppdatera licens tilldelningar inom den administrativa enheten.
Lösen ords administratör  |  Kan återställa lösen ord för icke-administratörer och lösen ords administratörer inom den tilldelade administrativa enheten.
Användaradministratör  |  Kan hantera alla aspekter av användare och grupper, inklusive att återställa lösen ord för begränsade administratörer inom den tilldelade administrativa enheten.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Säkerhets objekt som kan tilldelas en omfattnings roll

Följande säkerhets objekt kan tilldelas en roll med en administrativ enhets omfattning:

* Användare
* Roll tilldelnings bara moln grupper (förhands granskning)
* Tjänstens huvudnamn (SPN)

## <a name="assign-a-scoped-role"></a>Tilldela en omfattnings roll

### <a name="azure-portal"></a>Azure Portal

Gå till **Azure AD > administrativa enheter** i portalen. Välj den administrativa enhet som du vill tilldela rollen till en användare. I den vänstra rutan väljer du roller och administratörer för att visa en lista över alla tillgängliga roller.

![Välj en administrativ enhet för att ändra roll omfånget](./media/admin-units-assign-roles/select-role-to-scope.png)

Välj den roll som ska tilldelas och välj sedan **Lägg till tilldelningar**. En panel öppnas till höger där du kan välja en eller flera användare som ska tilldelas rollen.

![Välj rollen som definitions område och välj sedan Lägg till tilldelningar](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> Om du vill tilldela en roll på en administrativ enhet med hjälp av PIM följer du stegen [här](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

Det markerade avsnittet kan ändras efter behov för den aktuella miljön.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>Visa en lista över omfångs administratörer på en AU

### <a name="azure-portal"></a>Azure Portal

Alla roll tilldelningar som har gjorts med en administrativ enhets omfattning kan visas i [avsnittet administrativa enheter i Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Gå till **Azure AD > administrativa enheter** i portalen. Välj admin-enheten för roll tilldelningarna som du vill visa. Välj **roller och administratörer** och öppna en roll för att Visa tilldelningarna i admin-enheten.

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

Det markerade avsnittet kan ändras efter behov för den aktuella miljön.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Nästa steg

- [Använd moln grupper för att hantera roll tilldelningar](groups-concept.md)
- [Felsökningsroller tilldelade till molngrupper](groups-faq-troubleshooting.md)
