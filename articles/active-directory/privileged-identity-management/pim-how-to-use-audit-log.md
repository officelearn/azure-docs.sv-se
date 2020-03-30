---
title: Visa granskningsloggrapport för Azure AD-roller i Azure AD PIM | Microsoft-dokument
description: Lär dig hur du visar granskningslogghistoriken för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329532"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Visa granskningshistorik för Azure AD-roller i privilegierad identitetshantering

Du kan använda PIM-granskningshistoriken (Privileged Identity Management) för att se alla rolltilldelningar och aktiveringar under de senaste 30 dagarna för alla privilegierade roller. Om du vill se den fullständiga granskningshistoriken för aktivitet i din Azure Active Directory-organisation (Azure AD), inklusive administratörs-, slutanvändar- och synkroniseringsaktivitet, kan du använda [säkerhets- och aktivitetsrapporterna i Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Bestäm din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azure-resursroller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API:et](azure-ad-roles-features.md#api-changes). Medan den nya versionen distribueras, vilka procedurer som du följer i den här artikeln beror på vilken version av privilegierad identitetshantering du har för närvarande. Följ stegen i det här avsnittet för att avgöra vilken version av privilegierad identitetshantering du har. När du känner till din version av Privilegierad identitetshantering kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är i [rollen Privilegierad rolladministratör.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Öppna **Azure AD-privilegierad identitetshantering**. Om du har en banderoll högst upp på översiktssidan följer du instruktionerna på fliken **Ny version** i den här artikeln. Annars följer du instruktionerna på fliken **Föregående version.**

    [![Azure AD-roller ny version](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Välj flik för din version")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Ny version](#tab/new)

Följ dessa steg för att visa granskningshistoriken för Azure AD-roller.

## <a name="view-resource-audit-history"></a>Visa historik för resursgranskning

Resursgranskning ger dig en vy över all aktivitet som är associerad med dina Azure AD-roller.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

1. Välj **Resursgranskning**.

1. Filtrera historiken med hjälp av ett fördefinierat datum eller anpassat intervall.

    ![Lista över resursgranskning med filter](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Visa min granskning

Med min granskning kan du visa din personliga rollaktivitet.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

1. Välj den resurs som du vill visa granskningshistorik för.

1. Välj **Min granskning**.

1. Filtrera historiken med hjälp av ett fördefinierat datum eller anpassat intervall.

    ![Granskningslista för den aktuella användaren](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="view-audit-history"></a>Visa granskningshistorik

Följ dessa steg för att visa granskningshistoriken för Azure AD-roller.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i rollen [Administratör för privilegierad roll.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

1. Välj **granskningshistorik för katalogroller**.

    Beroende på granskningshistoriken visas ett stapeldiagram tillsammans med de totala aktiveringarna, maxaktiveringarna per dag och genomsnittliga aktiveringar per dag.

    [![Azure AD-roller ny version](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Visa granskningshistorik för katalogroller")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Längst ned på sidan visas en tabell med information om varje åtgärd i den tillgängliga granskningshistoriken. Kolumnerna har följande betydelser:

    | Kolumn | Beskrivning |
    | --- | --- |
    | Tid | När åtgärden inträffade. |
    | Requestor | Användare som begärde rollaktivering eller ändring. Om värdet är **Azure System**kontrollerar du Azure-granskningshistoriken för mer information. |
    | Åtgärd | Åtgärder som vidtas av beställaren. Åtgärder kan omfatta Tilldela, Ta bort tilldelning, Aktivera, Inaktivera eller AddedOutsidePIM. |
    | Medlem | Användare som aktiverar eller tilldelas en roll. |
    | Roll | Roll som tilldelats eller aktiverats av användaren. |
    | Resonemang | Text som har angetts i orsaksfältet under aktiveringen. |
    | Förfallodatum | När en aktiverad roll upphör att gälla. Gäller endast för kvalificerade rolltilldelningar. |

1. Om du vill sortera granskningshistoriken klickar du på knapparna **Tid,** **Åtgärd**och **Roll.**

## <a name="filter-audit-history"></a>Historik för filtergranskning

1. Klicka på knappen Filter högst upp på sidan **granskningshistorik.**

    Fönstret **Uppdatera diagramparametrar** visas.

1. Välj ett **tidsintervall i Tidsintervall.**

1. Markera kryssrutorna i **Roller**för att ange vilka roller du vill visa.

    ![Fönstret Uppdatera diagramparametrar](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Välj **Klar** om du vill visa den filtrerade granskningshistoriken.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Få orsak, godkännare och biljettnummer för godkännandehändelser

1. Logga in på [Azure-portalen](https://aad.portal.azure.com) med administratörsbehörigheter för privilegierad roll och öppna Azure AD.
1. Välj **Granskningsloggar**.
1. Använd **filtret Tjänst** om du bara vill visa granskningshändelser för tjänsten Privilegierad identitetshantering. På sidan **Granskningsloggar** kan du:

    - Se orsaken till en granskningshändelse i kolumnen **Statusorsak.**
    - Se godkännaren i kolumnen **Initierad av (aktör)** för händelsen "Lägg till medlem i godkänd rollbegäran".

    [![Azure AD-roller ny version](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrera granskningsloggen för PIM-tjänsten")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Välj en granskningslogghändelse om du vill visa biljettnumret på fliken **Aktivitet** i **informationsfönstret.**
  
    [![Azure AD-roller ny version](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Kontrollera biljettnumret för revisionshändelsen")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. Du kan visa beställaren (den person som aktiverar rollen) på fliken **Mål** i **informationsfönstret** för en granskningshändelse. Det finns två måltyper för Azure AD-roller:

    - Rollen (**Typ** = Roll)
    - Beställaren (**Typ** = Användare)

Vanligtvis är granskningslogghändelsen omedelbart ovanför godkännandehändelsen en händelse för "Lägg till medlem i roll **slutförd" där den initierade av (aktören)** är beställaren. I de flesta fall behöver du inte hitta beställaren i godkännandebegäran från ett granskningsperspektiv.

---

## <a name="next-steps"></a>Nästa steg

- [Visa aktivitets- och granskningshistorik för Azure-resursroller i Privilegierad identitetshantering](azure-pim-resource-rbac.md)
