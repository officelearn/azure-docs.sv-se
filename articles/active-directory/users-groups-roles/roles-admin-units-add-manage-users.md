---
title: Lägga till, ta bort och lista användare i en administrativ enhet – Azure Active Directory | Microsoft Docs
description: Hantera användare och deras roll behörigheter i en administrativ enhet i Azure Active Directory
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
ms.openlocfilehash: 22f35d88f3bb36d63d533941d27f72336714077c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630286"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Lägga till och hantera användare i en administrativ enhet i Azure Active Directory

I Azure Active Directory (Azure AD) kan du lägga till användare i en administrativ enhet (AU) för mer detaljerad administrativ omfattning av kontroll.

Information om hur du förbereder för att använda PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Kom igång](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Lägga till användare i en AU

### <a name="azure-portal"></a>Azure Portal

Du kan tilldela användare till administrativa enheter på två sätt.

1. Enskild tilldelning

    1. Du kan gå till Azure AD i portalen och välja användare och välja den användare som ska tilldelas en administrativ enhet. Du kan sedan välja administrativa enheter i den vänstra panelen. Användaren kan tilldelas en eller flera administrativa enheter genom att klicka på * * tilldela till administrativ enhet och välja de administrativa enheter där användaren ska tilldelas.

       ![Välj Lägg till och ange sedan ett namn för den administrativa enheten](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Du kan gå till Azure AD i portalen och välja administrativa enheter i den vänstra rutan och sedan välja den administrativa enhet där användarna ska tilldelas. Välj **alla användare** och välj sedan **Lägg till medlem**. Du kan sedan gå vidare och välja en eller flera användare som ska tilldelas den administrativa enheten från den högra rutan.

        ![Välj en administrativ enhet och välj sedan Lägg till medlem](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Mass tilldelning

    Gå till Azure AD i portalen och välj administrativa enheter. Välj den administrativa enhet där användare ska läggas till. Fortsätt genom att klicka på alla användare – > lägga till medlemmar från CSV-filen. Du kan sedan Hämta CSV-mallen och redigera filen. Formatet är enkelt och behöver ett enda UPN för att läggas till i varje rad. När filen är klar sparar du den på en lämplig plats och laddar sedan upp den i steg 3 som marker ATS i ögonblicks bilden.

    ![tilldela användare till en administrativ enhet](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

I exemplet ovan används cmdlet Add-AzureADAdministrativeUnitMember för att lägga till användaren till den administrativa enheten. Objekt-ID för den administrativa enhet där användaren ska läggas till och objekt-ID: t för den användare som ska läggas till tas som argument. Det markerade avsnittet kan ändras efter behov för den aktuella miljön.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Exempel:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>Lista administrativa enheter för en användare

### <a name="azure-portal"></a>Azure Portal

I Azure Portal kan du öppna en användar profil genom att:

1. Öppna **Azure AD**-  >  **användare**.

1. Välj användaren för att öppna användarens profil.

1. Välj **administrativa enheter** om du vill se en lista över administrativa enheter där användaren har tilldelats.

   ![Lista de administrativa enheterna för en användare](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
Obs: get-AzureADAdministrativeUnitMember returnerar endast 100 medlemmar.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Ta bort en enskild användare från en AU

### <a name="azure-portal"></a>Azure Portal

Det finns två sätt att ta bort en användare från en administrativ enhet. I Azure Portal kan du öppna en användar profil genom att gå till **Azure AD**-  >  **användare**. Välj användaren för att öppna användarens profil. Välj den administrativa enhet som du vill att användaren ska tas bort från och välj **ta bort från administrativ enhet**.

![Ta bort en användare från en administrativ enhet från användar profilen](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Du kan också ta bort en användare i **Azure AD**-  >  **administrativa enheter** genom att välja den administrativa enhet som du vill ta bort användare från. Välj användaren och välj **ta bort medlem**.
  
![Ta bort en användare på den administrativa enhets nivån](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref

## <a name="bulk-remove-more-than-one-user"></a>Mass borttagning av fler än en användare

Du kan gå till Azure AD > administrativa enheter och välja den administrativa enhet som du vill ta bort användare från. Klicka på Mass borttagnings medlem. Hämta CSV-mallen för att tillhandahålla listan över användare som ska tas bort.

Redigera den hämtade CSV-mallen med relevanta användar poster. Ta inte bort de två första raderna i mallen. Lägg till en användares UPN på varje rad.

![Redigera CSV-filen för borttagning av Mass användare från administrativa enheter](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

När du har sparat posterna i filen laddar du upp filen och väljer **Skicka**.

![Skicka mass uppladdnings filen](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela en roll till en administrativ enhet](roles-admin-units-assign-roles.md)
- [Lägga till grupper i en administrativ enhet](roles-admin-units-add-manage-groups.md)
