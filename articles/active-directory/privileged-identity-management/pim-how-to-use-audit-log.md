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
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c4a157d8d5bcd281ca9fee488e58c455034e898
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022067"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Visa gransknings historik för Azure AD-roller i PIM

Du kan använda gransknings historiken Privileged Identity Management (PIM) för att se alla roll tilldelningar och aktiveringar under de senaste 30 dagarna för alla privilegierade roller. Om du vill se en fullständig gransknings historik för aktiviteten i din Azure Active Directory (Azure AD)-organisation, inklusive administratör, slutanvändare och synkroniseringsuppgift, kan du använda [rapporterna Azure Active Directory säkerhets-och aktivitets rapporter](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Visa granskningshistorik

Följ dessa steg om du vill visa gransknings historiken för Azure AD-roller.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **katalog roller gransknings historik**.

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
    | Tanke | Text som angavs i orsaks fältet under aktiveringen. |
    | dag | När en aktive rad roll upphör att gälla. Gäller endast berättigade roll tilldelningar. |

1. Sortera gransknings historiken genom att klicka på knapparna **tid**, **åtgärd**och **roll** .

## <a name="filter-audit-history"></a>Filtrera gransknings historik

1. Klicka på knappen **filter** överst på sidan gransknings historik.

    Fönstret **Parametrar för uppdaterings diagram** visas.

1. I **tidsintervall**väljer du ett tidsintervall.

1. I **roller**markerar du kryss rutorna för att ange de roller som du vill visa.

    ![Fönstret uppdatera diagram parametrar](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Klicka på **Slutför** om du vill visa den filtrerade gransknings historiken.

## <a name="next-steps"></a>Nästa steg

- [Visa aktivitet och gransknings historik för Azures resurs roller i Privileged Identity Management](azure-pim-resource-rbac.md)
