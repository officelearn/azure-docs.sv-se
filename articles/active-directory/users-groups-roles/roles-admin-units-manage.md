---
title: Lägga till och ta bort administrativa enheter (förhandsversion) – Azure Active Directory | Microsoft-dokument
description: Använda administrativa enheter för att begränsa omfattningen av rollbehörigheter i Azure Active Directory
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
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428165"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Hantera administrativa enheter i Azure Active Directory

För mer detaljerad administrativ kontroll i Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med ett scope som är begränsat till en eller flera administrativa enheter (AUs).

## <a name="getting-started"></a>Komma igång

1. Om du vill köra frågor från följande instruktioner via [Graph Explorer](https://aka.ms/ge)kontrollerar du följande:

    1. Gå till Azure AD i portalen och välj sedan Graph Explorer i programmen och ge administratörsgodkännande till Graph Explorer.

        ![välj Graph Explorer och ge administratörsmedgivande på den här sidan](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. Kontrollera att du väljer betaversionen i Graph Explorer.

        ![välj betaversionen före POST-åtgärden](./media/roles-admin-units-manage/select-beta-version.png)

1. Använd förhandsversionen av Azure AD PowerShell. Detaljerade instruktioner finns här.

## <a name="add-an-administrative-unit"></a>Lägga till en administrativ enhet

### <a name="azure-portal"></a>Azure Portal

1. Gå till Active Directory i portalen och välj Administrativa enheter på den vänstra panelen.

    ![navigera till administrativa enheter i Azure Active Directory](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Välj **Lägg till*** och ange namnet på den administrativa enheten och kan eventuellt lägga till en beskrivning för den administrativa enheten.

    ![välj Lägg till och ange sedan ett namn för den administrativa enheten](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Välj **Lägg till** för att slutföra den administrativa enheten.

### <a name="powershell"></a>PowerShell

Installera Azure AD PowerShell (förhandsversion) innan du försöker utföra åtgärderna nedan:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

De värden som markeras ovan kan ändras efter behov.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Ta bort en administrativ enhet

I Azure Active Directory (Azure AD) kan du ta bort en administratörsenhet som du inte längre behöver som en enhet med omfång för administrativa roller.

### <a name="azure-portal"></a>Azure Portal

Gå till **Azure AD > Administrativa enheter** i portalen. Välj den administrativa enhet som ska tas bort och välj sedan **Ta bort**. När du har bekräftat **Ja**tas den administrativa enheten bort.

![Välj en administrativ enhet som ska tas bort](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Det markerade avsnittet kan ändras efter behov för den specifika miljön.

### <a name="graph-api"></a>Graph API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Nästa steg

[Hantera användare på administrativ enhet](roles-admin-units-add-manage-users.md)
[Hantera grupper i administrativ enhet](roles-admin-units-add-manage-groups.md)
