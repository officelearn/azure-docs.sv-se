---
title: Visa gransknings historik för Azure AD-roller i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du visar gransknings historiken för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3547ff218e9a15151e4abe2ceff53292d3f01ac0
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804318"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Visa gransknings historik för Azure AD-roller i PIM

Du kan använda gransknings historiken Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att se alla roll tilldelningar och aktiveringar under de senaste 30 dagarna för alla privilegierade roller. Om du vill se en fullständig gransknings historik för aktiviteten i din katalog, inklusive administratör, slutanvändare och synkroniseringsuppgift, kan du använda [Azure Active Directory säkerhets-och aktivitets rapporter](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Visa granskningshistorik

Följ dessa steg om du vill visa gransknings historiken för Azure AD-roller.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **katalog roller gransknings historik**.

    Beroende på din gransknings historik visas ett stapeldiagram tillsammans med totalt antal aktiveringar, Max aktiveringar per dag och genomsnittlig aktivering per dag.

    ![Granskningshistorik för katalogrollerna](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Längst ned på sidan visas en tabell med information om varje åtgärd i den tillgängliga gransknings historiken. Kolumnerna har följande betydelser:

    | Kolumn | Beskrivning |
    | --- | --- |
    | Time | När åtgärden utfördes. |
    | Beställare | Användare som begärde roll aktiveringen eller ändringen. Om värdet är **Azure-system**kontrollerar du Azures gransknings historik för mer information. |
    | Action | Åtgärder som vidtas av begär Ande. Åtgärder kan omfatta tilldela, ta bort, aktivera, inaktivera eller AddedOutsidePIM. |
    | Medlem | Användare som aktiverar eller har tilldelats en roll. |
    | Role | Roll som tilldelats eller Aktiver ATS av användaren. |
    | Tanke | Text som angavs i orsaks fältet under aktiveringen. |
    | Utgångsdatum | När en aktive rad roll upphör att gälla. Gäller endast berättigade roll tilldelningar. |

1. Sortera gransknings historiken genom att klicka på knapparna **tid**, **åtgärd**och **roll** .

## <a name="filter-audit-history"></a>Filtrera gransknings historik

1. Klicka på knappen **filter** överst på sidan gransknings historik.

    Fönstret **Parametrar för uppdaterings diagram** visas.

1. I **tidsintervall**väljer du ett tidsintervall.

1. I **roller**, lägger du till markeringar för de roller som du vill visa.

    ![Fönstret uppdatera diagram parametrar](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Klicka på **Slutför** om du vill visa den filtrerade gransknings historiken.

## <a name="next-steps"></a>Nästa steg

- [Visa aktivitet och gransknings historik för Azure Resource roles i PIM](azure-pim-resource-rbac.md)
