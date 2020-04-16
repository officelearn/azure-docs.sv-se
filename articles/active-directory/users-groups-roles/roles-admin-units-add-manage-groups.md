---
title: Lägga till, ta bort och lista grupper i en administrativ enhet (förhandsversion) - Azure Active Directory | Microsoft-dokument
description: Hantera grupper och deras rollbehörigheter i en administrativ enhet i Azure Active Directory
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
ms.openlocfilehash: 78f45d9e1ca5b8237779fe096154c83749aa08fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428438"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Lägga till och hantera grupper i administrativa enheter i Azure Active Directory

I Azure Active Directory (Azure AD) kan du lägga till grupper i en administrativ enhet (AU) för mer detaljerad administrativt omfång för kontroll.

Steg för att förbereda hur du använder PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Komma igång](roles-admin-units-manage.md#getting-started).

## <a name="add-groups-to-an-au"></a>Lägga till grupper i en AU

### <a name="azure-portal"></a>Azure Portal

I förhandsgranskningen kan du tilldela grupper endast individuellt till en administrativ enhet. Det finns inget alternativ för masstilldelning av grupper till en administrativ enhet. Du kan tilldela en grupp till en administrativ enhet på något av de två sätten i portalen:

1. Från sidan **Azure AD > Groups**

    Öppna översiktssidan grupper i Azure AD och välj den grupp som måste tilldelas den administrativa enheten. På vänster sida väljer du **Administrativa enheter** som du vill visa de administrativa enheter som gruppen har tilldelats. På toppen hittar du alternativet Tilldela till administrativ enhet och klicka på den kommer att ge en panel på höger sida för att välja den administrativa enheten.

    ![tilldela en grupp individuellt till en administrativ enhet](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Från sidan **Azure AD > Administrative units > All Groups**

    Öppna bladet Alla grupper i Azure AD > administrativa enheter. Om det redan finns grupper som har tilldelats den administrativa enheten visas de på höger sida. Välj **Lägg till** högst upp och en höger panel visas i listningen av de grupper som är tillgängliga i din Azure AD-organisation. Välj en eller flera grupper som ska tilldelas de administrativa enheterna.

    ![välj en administrativ enhet och välj sedan Lägg till medlem](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

I det här exemplet används cmdlet Add-AzureADAdministrativeUnitMember för att lägga till gruppen i den administrativa enheten. Objekt-ID:et för den administrativa enheten och objekt-ID:et för den grupp som ska läggas till tas som argument. Det markerade avsnittet kan ändras efter behov för den specifika miljön.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Exempel:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Lista grupper i en AU

### <a name="azure-portal"></a>Azure Portal

Gå till **Azure AD > Administrativa enheter** i portalen. Välj den administrativa enhet som du vill visa användarna för. Som standard är **alla användare** redan markerade på den vänstra panelen. Välj **Alla grupper** och till höger hittar du listan över grupper som är medlemmar i den valda administrativa enheten.

![Välj en administrativ enhet som ska tas bort](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Detta hjälper dig att få alla medlemmar i den administrativa enheten. Om du vill visa alla grupper som är medlemmar i den administrativa enheten kan du använda kodavsnittet nedan:

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>Lista AUs för en grupp

### <a name="azure-portal"></a>Azure Portal

I Azure AD-portalen kan du öppna en grupps information genom att öppna **Grupper**. Välj en grupp för att öppna gruppens profil. Välj **Administrativa enheter** om du vill visa alla administrativa enheter där gruppen är medlem.

![Lista administrativa enheter för en grupp](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Ta bort en grupp från en AU

### <a name="azure-portal"></a>Azure Portal

Det finns två sätt att ta bort en grupp från en administrativ enhet i Azure-portalen.

Öppna Azure > **AD-grupper** och öppna profilen för grupp som du vill ta bort från administrativ enhet. **Azure AD** Välj **Administrativa enheter** i den vänstra panelen om du vill visa alla administrativa enheter där gruppen är medlem. Välj den administrativa enhet som du vill ta bort gruppen från och välj sedan **Ta bort från administrativ enhet**.

![Ta bort en grupp från en administrativ enhet](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Alternativt kan du gå till **Azure AD** > **Administrativa enheter** och välja den administrativa enhet där gruppen är medlem. Välj **Grupper** på den vänstra panelen om du vill visa medlemsgrupperna. Markera den grupp som ska tas bort från den administrativa enheten och välj sedan **Ta bort grupper**.

![Lista grupper i en administrativ enhet](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Nästa steg

- [Tilldela en roll till en administrativ enhet](roles-admin-units-assign-roles.md)
- [Hantera användare i en administrativ enhet](roles-admin-units-add-manage-users.md)
