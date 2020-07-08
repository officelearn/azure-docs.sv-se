---
title: Lägg till, ta bort och lista grupper i en administrativ enhet (förhands granskning) – Azure Active Directory | Microsoft Docs
description: Hantera grupper och deras roll behörigheter i en administrativ enhet i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 06/23/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76026313eea8c8fbb2f3e55321e2e4ebbe5dcfc7
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850913"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Lägg till och hantera grupper i administrativa enheter i Azure Active Directory

I Azure Active Directory (Azure AD) kan du lägga till grupper i en administrativ enhet (AU) för mer detaljerad administrativ omfattning av kontroll.

Information om hur du förbereder för att använda PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Kom igång](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Lägga till grupper i en AU

### <a name="azure-portal"></a>Azure Portal

I förhands granskningen kan du endast tilldela grupper individuellt till en administrativ enhet. Det finns inget alternativ för Mass tilldelning av grupper till en administrativ enhet. Du kan tilldela en grupp till en administrativ enhet på något av de två sätten på portalen:

1. Från sidan **Azure AD > grupper**

    Öppna sidan Översikt över grupper i Azure AD och välj den grupp som måste tilldelas den administrativa enheten. På den vänstra sidan väljer du **administrativa enheter** för att visa en lista över de administrativa enheter som gruppen är tilldelad till. Överst visas alternativet tilldela till administrativ enhet och om du klickar på den visas en panel på höger sida där du kan välja den administrativa enheten.

    ![tilldela en grupp individuellt till en administrativ enhet](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Från sidan **Azure AD > administrativa enheter > alla grupper**

    Öppna bladet alla grupper i Azure AD > administrativa enheter. Om det redan finns grupper som har tilldelats till den administrativa enheten visas de på höger sida. Välj **Lägg till** överst och en höger panel visas i listan över tillgängliga grupper i Azure AD-organisationen. Välj en eller flera grupper som ska tilldelas de administrativa enheterna.

    ![Välj en administrativ enhet och välj sedan Lägg till medlem](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

I det här exemplet används cmdlet Add-AzureADAdministrativeUnitMember för att lägga till gruppen i den administrativa enheten. Objekt-ID: t för den administrativa enheten och objekt-ID: t för den grupp som ska läggas till tas som argument. Det markerade avsnittet kan ändras efter behov för den aktuella miljön.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
}
```

Exempel:

```http
{
"@odata.id":"https://graph.microsoft.com/beta/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="list-groups-in-an-au"></a>Lista grupper i en AU

### <a name="azure-portal"></a>Azure Portal

Gå till **Azure AD > administrativa enheter** i portalen. Välj den administrativa enhet som du vill visa användarna för. Som standard väljs **alla användare** redan i den vänstra panelen. Välj **alla grupper** och till höger hittar du en lista över grupper som är medlemmar i den valda administrativa enheten.

![Lista grupper i en administrativ enhet](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

Detta hjälper dig att få alla medlemmar i den administrativa enheten. Om du vill visa alla grupper som är medlemmar i den administrativa enheten kan du använda kodfragmentet nedan:

```http
foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```
### <a name="microsoft-graph"></a>Microsoft Graph

```http
HTTP request
GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="list-aus-for-a-group"></a>Visa en grupps Australien

### <a name="azure-portal"></a>Azure Portal

I Azure AD-portalen kan du öppna en grupps information genom att öppna **grupper**. Välj en grupp för att öppna gruppens profil. Välj **administrativa enheter** för att visa en lista över alla administrativa enheter där gruppen är medlem.

![Lista administrativa enheter för en grupp](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-au"></a>Ta bort en grupp från en AU

### <a name="azure-portal"></a>Azure Portal

Det finns två sätt att ta bort en grupp från en administrativ enhet i Azure Portal.

Öppna **Azure AD**-  >  **grupper** och öppna profilen för gruppen som du vill ta bort från den administrativa enheten. Välj **administrativa enheter** i den vänstra panelen för att visa en lista över de administrativa enheter där gruppen är medlem. Välj den administrativa enhet som du vill ta bort gruppen från och välj sedan **ta bort från administrativ enhet**.

![Ta bort en grupp från en administrativ enhet](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Du kan också gå till administrativa enheter i **Azure AD**  >  **Administrative units** och välja den administrativa enhet där gruppen är medlem. Välj **grupper** i den vänstra panelen för att Visa medlems grupperna. Välj den grupp som ska tas bort från den administrativa enheten och välj sedan **ta bort grupper**.

![Lista grupper i en administrativ enhet](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>Nästa steg

- [Tilldela en roll till en administrativ enhet](roles-admin-units-assign-roles.md)
- [Hantera användare i en administrativ enhet](roles-admin-units-add-manage-users.md)
