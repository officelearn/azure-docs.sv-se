---
title: Lägga till och ta bort administrativa enheter (förhands granskning) – Azure Active Directory | Microsoft Docs
description: Använd administrativa enheter för att begränsa omfånget för roll behörigheter i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 977a90419c142e576fcf484562875d12c8dad451
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851766"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Hantera administrativa enheter i Azure Active Directory

För mer detaljerad administrativ kontroll i Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med en omfattning som är begränsad till en eller flera administrativa enheter (Australien).

## <a name="get-started"></a>Kom igång

1. Om du vill köra frågor från följande instruktioner via [Graph Explorer](https://aka.ms/ge)gör du följande:

    a. I Azure Portal går du till Azure AD. I listan program väljer du **Graph Explorer**och väljer sedan **bevilja administrativt medgivande till Graph Explorer**.

    ![Skärm bild som visar länk till "bevilja administrativt medgivande"](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. I Graph Explorer väljer du **beta** versionen.

    ![Skärm bild som visar den valda Beta versionen](./media/roles-admin-units-manage/select-beta-version.png)

1. Använd för hands versionen av Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Lägg till en administrativ enhet

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I Azure Portal går du till Azure AD och väljer sedan **administrativa enheter**i den vänstra rutan.

    ![Skärm bild av länken administrativa enheter (förhands granskning) i Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Välj **Lägg till** och ange sedan namnet på den administrativa enheten. Du kan också lägga till en beskrivning av den administrativa enheten.

    ![Skärm bild av knappen Lägg till och text rutan för att ange namnet på den administrativa enheten](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Välj **Lägg till** för att slutföra den administrativa enheten.

### <a name="use-powershell"></a>Använd PowerShell

Installera Azure AD PowerShell (för hands version) innan du försöker köra följande kommandon:

```powershell
Connect-AzureAD
New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Du kan ändra de värden som omges av citat tecken, om det behövs.

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Ta bort en administrativ enhet

I Azure AD kan du ta bort en administrativ enhet som du inte längre behöver som enhets omfång för administrativa roller.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I Azure Portal går du till **Azure AD**  >  **administrations enheter**för Azure AD. 
1. Välj den administrativa enhet som ska tas bort och välj sedan **ta bort**. 
1. Bekräfta att du vill ta bort den administrativa enheten genom att välja **Ja**. Den administrativa enheten tas bort.

![Skärm bild av den administrativa enhets knappen Ta bort och bekräftelse fönstret](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Använd PowerShell

```powershell
$delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId
```

Du kan ändra de värden som omges av citat tecken, vilket krävs för den aktuella miljön.

### <a name="use-the-graph-api"></a>Använd Graph API

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>Nästa steg

* [Hantera användare i en administrativ enhet](roles-admin-units-add-manage-users.md)
* [Hantera grupper i en administrativ enhet](roles-admin-units-add-manage-groups.md)
