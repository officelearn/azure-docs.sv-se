---
title: Lägga till, ta bort och lista användare i en administrativ enhet (förhandsversion) – Azure Active Directory | Microsoft-dokument
description: Hantera användare och deras rollbehörigheter i en administrativ enhet i Azure Active Directory
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
ms.openlocfilehash: 92d96abd343266372984918fd0c942ec1cae865f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428308"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Lägga till och hantera användare i en administrativ enhet i Azure Active Directory

I Azure Active Directory (Azure AD) kan du lägga till användare i en administrativ enhet (AU) för mer detaljerad administrativt omfång för kontroll.

Steg för att förbereda hur du använder PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Komma igång](roles-admin-units-manage.md#getting-started).

## <a name="add-users-to-an-au"></a>Lägga till användare i en AU

### <a name="azure-portal"></a>Azure Portal

Du kan tilldela användare till administrativa enheter på två sätt.

1. Individuellt uppdrag

    1. Du kan gå till Azure AD i portalen och välja Användare och välja den användare som ska tilldelas en administrativ enhet. Du kan sedan välja Administrativa enheter på den vänstra panelen. Användaren kan tilldelas en eller flera administrativa enheter genom att klicka på Tilldela till administrativ enhet och välja de administrativa enheter där användaren ska tilldelas.

       ![välj Lägg till och ange sedan ett namn för den administrativa enheten](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Du kan gå till Azure AD i portalen och välja Administrativa enheter i den vänstra rutan och sedan välja den administrativa enhet där användarna ska tilldelas. Välj Alla användare i den vänstra rutan och välj sedan Lägg till medlem. Du kan sedan gå vidare och välja en eller flera användare som ska tilldelas den administrativa enheten från den högra rutan.

        ![välj en administrativ enhet och välj sedan Lägg till medlem](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Masstilldelning

    Gå till Azure AD i portalen och välj Administrativa enheter. Välj den administrativa enhet där användare ska läggas till. Fortsätt genom att klicka på Alla användare -> Lägg till medlemmar från CSV-filen. Du kan sedan ladda ner CSV-mallen och redigera filen. Formatet är enkelt och behöver en enda UPN som ska läggas till i varje rad. När filen är klar sparar du den på en lämplig plats och överför den sedan i steg 3 som markerats i ögonblicksbilden.

    ![masstilldela användare till en administrativ enhet](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $UserObj.ObjectId

I exemplet ovan används cmdlet Add-AzureADAdministrativeUnitMember för att lägga till användaren i den administrativa enheten. Objekt-ID:t för den administrativa enhet där användaren ska läggas till och objekt-ID:et för den användare som ska läggas till tas som argument. Det markerade avsnittet kan ändras efter behov för den specifika miljön.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Exempel:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Lista administrativa enheter för en användare

### <a name="azure-portal"></a>Azure Portal

I Azure-portalen kan du öppna en användares profil genom att gå till Azure AD >-användare. Klicka på användaren för att öppna användarens profil.

![Öppna en användares profil i Azure Active Directory](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Välj **Administrativa enheter** på den vänstra panelen om du vill visa listan över administrativa enheter där användaren har tilldelats.

![Lista administrativa enheter för en användare](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Ta bort en enskild användare från en AU

### <a name="azure-portal"></a>Azure Portal

Du kan ta bort en användare från en administrativ enhet på två sätt. I Azure-portalen kan du öppna en användares profil genom att gå till Azure > **AD-användare**. **Azure AD** Välj den användare som ska öppnas för användarens profil. Välj den administrativa enhet som du vill att användaren ska tas bort från och välj **Ta bort från den administrativa enheten**.

![Ta bort en användare från en administrativ enhet från användarprofilen](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Du kan också ta bort en användare i **Azure AD** > **Administrativa enheter** genom att välja den administrativa enhet som du vill ta bort användare från. Markera användaren och välj **Ta bort medlem**.
  
![Ta bort en användare på administrativ enhetsnivå](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Massborttagning av mer än en användare

Du kan gå till Azure AD > administrativa enheter och välja den administrativa enhet som du vill ta bort användare från. Klicka på Bulk ta bort medlem. Ladda ner CSV-mallen för att tillhandahålla listan över användare som ska tas bort.

Redigera den nedladdade CSV-mallen med relevanta användarposter. Ta inte bort de två första raderna i mallen. Lägg till en användare UPN på varje rad.

![Redigera CSV-filen för massanvändare borttagning från administrativa enheter](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

När du har sparat posterna i filen laddar du upp filen och väljer **Skicka**.

![Skicka massöverföringsfilen](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela en roll till en administrativ enhet](roles-admin-units-assign-roles.md)
- [Lägga till grupper i en administrativ enhet](roles-admin-units-add-manage-groups.md)
