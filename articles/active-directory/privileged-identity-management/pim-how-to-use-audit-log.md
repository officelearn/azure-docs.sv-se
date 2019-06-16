---
title: Visa granskningshistorik för Azure AD-roller i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur du visar granskningshistorik för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053921"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Visa granskningshistorik för Azure AD-roller i PIM

Du kan använda granskningshistorik för Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att se alla rolltilldelningar och aktiveringar under de senaste 30 dagarna för alla Privilegierade roller. Om du vill visa fullständig granskningshistorik för aktiviteten i din katalog, inklusive administratören, slutanvändare och synkroniseringsåtgärden, kan du använda den [säkerhets- och rapporter i Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Visa granskningshistorik

Följ dessa steg om du vill visa granskningshistorik för Azure AD-roller.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i den [privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roll.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **granskningshistorik för katalogrollerna**.

    Beroende på din granskningshistorik är ett stapeldiagram visas tillsammans med Totalt antal aktiveringar, max aktiveringar per dag och genomsnittlig aktiveringar per dag.

    ![Granskningshistorik för katalogrollerna](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Längst ned på sidan visas en tabell med information om varje åtgärd i tillgängliga granskningshistoriken. Kolumnerna som har följande:

    | Kolumn | Beskrivning |
    | --- | --- |
    | Tid | När åtgärden inträffade. |
    | Begärande | Användare som begärde rollaktivering eller ändra. Om värdet är **Azure System**, kontrollera Azure granskningshistoriken för mer information. |
    | Åtgärd | Åtgärder som vidtas av förfrågaren. Åtgärder kan innehålla tilldela, ta bort tilldelning, aktivera, inaktivera eller AddedOutsidePIM. |
    | Medlem | Användare som är att aktivera eller tilldelats en roll. |
    | Roll | Rollen tilldelning eller aktivering av användaren. |
    | Tanke | Text som angavs i fältet Orsak under aktiveringen. |
    | upphör att gälla | När en aktiverad roll upphör att gälla. Gäller bara för berättigade rolltilldelningar. |

1. Om du vill sortera granskningshistoriken, klickar du på den **tid**, **åtgärd**, och **rollen** knappar.

## <a name="filter-audit-history"></a>Filtrera granskningshistorik

1. Längst upp på sidan Granska tidigare klickar du på den **Filter** knappen.

    Den **uppdatera diagramparametrar** visas fönstret.

1. I **tidsintervall**, Välj ett tidsintervall.

1. I **roller**, lägga till bockar för de roller som du vill visa.

    ![Uppdatera diagram parameterfönstret](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Klicka på **klar** att visa filtrerade granskningshistoriken.

## <a name="next-steps"></a>Nästa steg

- [Visa aktivitet och granskningshistorik för Azure-resursroller i PIM](azure-pim-resource-rbac.md)
