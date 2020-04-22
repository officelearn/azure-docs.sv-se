---
title: Lägga till och ta bort administrativa enheter (förhandsversion) – Azure Active Directory | Microsoft-dokument
description: Använd administrativa enheter för att begränsa omfattningen av rollbehörigheter i Azure Active Directory.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684920"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Hantera administrativa enheter i Azure Active Directory

För mer detaljerad administrativ kontroll i Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med ett scope som är begränsat till en eller flera administrativa enheter (AUs).

## <a name="get-started"></a>Kom igång

1. Så här kör du frågor från följande instruktioner via [Graph Explorer:](https://aka.ms/ge)

    a. Gå till Azure AD i Azure-portalen. I programlistan väljer du **Graph Explorer**och väljer sedan **Bevilja administratörsgodkännande till Graph Explorer**.

    ![Skärmbild som visar länk till "Bevilja administratörssamtycke"](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. Välj **betaversionen** i Graph Explorer.

    ![Skärmbild som visar den valda betaversionen](./media/roles-admin-units-manage/select-beta-version.png)

1. Använd förhandsversionen av Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Lägga till en administrativ enhet

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Gå till Azure AD i Azure-portalen och välj sedan **Administrativa enheter**i den vänstra rutan .

    ![Skärmbild av länken Administrativa enheter (förhandsversion) i Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Välj **Lägg till** och ange sedan namnet på den administrativa enheten. Du kan också lägga till en beskrivning av den administrativa enheten.

    ![Skärmbild av knappen Lägg till och textrutan för att ange namnet på den administrativa enheten](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Välj **Lägg till** för att slutföra den administrativa enheten.

### <a name="use-powershell"></a>Använd PowerShell

Installera Azure AD PowerShell (förhandsversion) innan du försöker köra följande kommandon:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Du kan ändra de värden som omges av citattecken efter behov.

### <a name="use-microsoft-graph"></a>Använda Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Ta bort en administrativ enhet

I Azure AD kan du ta bort en administrativ enhet som du inte längre behöver som en enhet av scope för administrativa roller.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Gå till Azure AD**Administrative-enheter**i **Azure Portal.** >  
1. Välj den administrativa enhet som ska tas bort och välj sedan **Ta bort**. 
1. Om du vill bekräfta att du vill ta bort den administrativa enheten väljer du **Ja**. Den administrativa enheten tas bort.

![Skärmbild av knappen Ta bort administrationsenhet och bekräftelsefönster](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Använd PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Du kan ändra de värden som omges av citattecken, efter behov för den specifika miljön.

### <a name="use-the-graph-api"></a>Använda graph-API:et

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Nästa steg

* [Hantera användare i en administrativ enhet](roles-admin-units-add-manage-users.md)
* [Hantera grupper i en administrativ enhet](roles-admin-units-add-manage-groups.md)
