---
title: Visa gransknings rapport för Azure AD-roller i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du visar gransknings historiken för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4a2eccc02d13bf5a2dfc8bf3ceb7887e4962489
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498511"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Visa gransknings historik för Azure AD-roller i PIM

Du kan använda gransknings historiken Privileged Identity Management (PIM) för att se alla roll tilldelningar och aktiveringar under de senaste 30 dagarna för alla privilegierade roller. Om du vill se en fullständig gransknings historik för aktiviteten i din Azure Active Directory (Azure AD)-organisation, inklusive administratör, slutanvändare och synkroniseringsuppgift, kan du använda [rapporterna Azure Active Directory säkerhets-och aktivitets rapporter](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Fastställ din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azures resurs roller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API: et](azure-ad-roles-features.md#api-changes). Även om den nya versionen publiceras, vilka procedurer som du följer i den här artikeln beror på vilken version av Privileged Identity Management du för närvarande har. Följ stegen i det här avsnittet för att ta reda på vilken version av Privileged Identity Management du har. När du känner till din version av Privileged Identity Management kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som har rollen [privilegierad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Öppna **Azure AD Privileged Identity Management**. Om du har en banderoll överst på sidan Översikt, följer du anvisningarna på fliken **ny version** i den här artikeln. Annars följer du anvisningarna på fliken **tidigare version** .

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Ny version](#tab/new)

Följ dessa steg om du vill visa gransknings historiken för Azure AD-roller.

## <a name="view-resource-audit-history"></a>Visa resurs gransknings historik

Resurs granskning ger dig en översikt över all aktivitet som är kopplad till dina Azure AD-roller.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **resurs granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    ![Resurs gransknings lista med filter](media/pim-how-to-use-audit-log/resource-audit.png)

## <a name="view-my-audit"></a>Visa min granskning

I min granskning kan du Visa din personliga roll aktivitet.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj den resurs som du vill visa gransknings historik för.

1. Välj **min granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    ![Gransknings lista för den aktuella användaren](media/pim-how-to-use-audit-log/audit-time-span.png)

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="view-audit-history"></a>Visa granskningshistorik

Följ dessa steg om du vill visa gransknings historiken för Azure AD-roller.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **gransknings historik för katalog roller**.

    Beroende på din gransknings historik visas ett stapeldiagram tillsammans med totalt antal aktiveringar, Max aktiveringar per dag och genomsnittlig aktivering per dag.

    ![Gransknings historik för katalog roller](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Längst ned på sidan visas en tabell med information om varje åtgärd i den tillgängliga gransknings historiken. Kolumnerna har följande betydelser:

    | Kolumn | Beskrivning |
    | --- | --- |
    | Tid | När åtgärden utfördes. |
    | Begär Ande | Användare som begärde roll aktiveringen eller ändringen. Om värdet är **Azure-system**kontrollerar du Azures gransknings historik för mer information. |
    | Åtgärd | Åtgärder som vidtas av begär Ande. Åtgärder kan omfatta tilldela, ta bort, aktivera, inaktivera eller AddedOutsidePIM. |
    | Medlem | Användare som aktiverar eller har tilldelats en roll. |
    | Roll | Roll som tilldelats eller Aktiver ATS av användaren. |
    | REA soning | Text som angavs i orsaks fältet under aktiveringen. |
    | Dag | När en aktive rad roll upphör att gälla. Gäller endast berättigade roll tilldelningar. |

1. Sortera gransknings historiken genom att klicka på knapparna **tid**, **åtgärd**och **roll** .

## <a name="filter-audit-history"></a>Filtrera gransknings historik

1. Klicka på knappen **filter** överst på sidan gransknings historik.

    Fönstret **Parametrar för uppdaterings diagram** visas.

1. I **tidsintervall**väljer du ett tidsintervall.

1. I **roller**markerar du kryss rutorna för att ange de roller som du vill visa.

    ![Fönstret uppdatera diagram parametrar](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Välj **färdig** om du vill visa den filtrerade gransknings historiken.

---

## <a name="next-steps"></a>Nästa steg

- [Visa aktivitet och gransknings historik för Azures resurs roller i Privileged Identity Management](azure-pim-resource-rbac.md)
