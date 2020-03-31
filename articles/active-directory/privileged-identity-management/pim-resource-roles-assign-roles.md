---
title: Tilldela Azure-resursroller i Privilegierad identitetshantering - Azure Active Directory | Microsoft-dokument
description: Lär dig hur du tilldelar Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266564"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Tilldela Azure-resursroller i Privilegierad identitetshantering

Azure Active Directory (Azure AD) Privilegierad identitetshantering (PIM) kan hantera de inbyggda Azure-resursrollerna, samt anpassade roller, inklusive (men inte begränsat till):

- Ägare
- Administratör för användaråtkomst
- Deltagare
- Säkerhetsadministratör
- Säkerhetschef

> [!NOTE]
> Användare eller medlemmar i en grupp som tilldelats prenumerationsrollerna Ägare eller Användaråtkomstadministratör och Azure AD Global-administratörer som aktiverar prenumerationshantering i Azure AD har resursadministratörsbehörigheter som standard. Dessa administratörer kan tilldela roller, konfigurera rollinställningar och granska åtkomst med privilegierad identitetshantering för Azure-resurser. En användare kan inte hantera privilegierad identitetshantering för resurser utan resursadministratörsbehörighet. Visa listan över [inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Tilldela en roll

Följ dessa steg för att göra en användare kvalificerad för en Azure-resursroll.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i [rollen Privilegierad rolladministratör.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Information om hur du ger en annan administratör åtkomst till hantering av privilegierad identitetshantering finns i [Bevilja åtkomst till andra administratörer för att hantera privilegierad identitetshantering](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**.

1. Använd **resursfiltret** för att filtrera listan över hanterade resurser.

    ![Lista över Azure-resurser som ska hanteras](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Välj den resurs som du vill hantera, till exempel en prenumeration eller hanteringsgrupp.

1. Under Hantera väljer du **Roller** för att visa listan över roller för Azure-resurser.

    ![Azure-resursroller](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Välj **Lägg till medlem** om du vill öppna fönstret Nytt tilldelningsfönster.

1. Välj **Välj en roll** om du vill öppna fönstret Välj en roll.

    ![Nytt tilldelningsfönster](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Välj en roll som du vill tilldela och klicka sedan på **Markera**.

    Fönstret Välj en medlem eller grupp öppnas.

1. Markera en medlem eller grupp som du vill tilldela rollen och klicka sedan på **Välj**.

    ![Markera ett medlems- eller gruppfönster](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Fönstret Inställningar för medlemskap öppnas.

1. Välj Kvalificerat eller **Aktivt**i listan **Tilldelningstyp** . **Eligible**

    ![Fönstret Inställningar för medlemskap](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privilegierad identitetshantering för Azure-resurser innehåller två olika tilldelningstyper:

    - **Kvalificerade** tilldelningar kräver att medlemmen i rollen utför en åtgärd för att använda rollen. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare.

    - **Aktiva** tilldelningar kräver inte att medlemmen utför någon åtgärd för att använda rollen. Medlemmar som har tilldelats som aktiva har de privilegier som tilldelats rollen hela tiden.

1. Om tilldelningen ska vara permanent (permanent berättigad eller permanent tilldelad) markerar du kryssrutan **Permanent.**

    Beroende på rollinställningarna kanske kryssrutan inte visas eller kan vara omodifierbar.

1. Om du vill ange en viss tilldelningstid avmarkerar du kryssrutan och ändrar start- och/eller slutdatum- och tidsrutorna.

    ![Inställningar för medlemskap - datum och tid](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. När du är klar väljer du **Klar**.

    ![Nytt uppdrag - Lägg till](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Om du vill skapa den nya rolltilldelningen väljer du **Lägg till**. Ett meddelande om status visas.

    ![Nytt uppdrag - Anmälan](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Uppdatera eller ta bort en befintlig rolltilldelning

Följ dessa steg för att uppdatera eller ta bort en befintlig rolltilldelning.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill hantera, till exempel en prenumeration eller hanteringsgrupp.

1. Under Hantera väljer du **Roller** för att visa listan över roller för Azure-resurser.

    ![Azure-resursroller - Välj roll](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Välj den roll som du vill uppdatera eller ta bort.

1. Hitta rolltilldelningen på flikarna **Kvalificerade roller** eller **Aktiva roller.**

    ![Uppdatera eller ta bort rolltilldelning](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Välj **Uppdatera** eller **Ta bort** om du vill uppdatera eller ta bort rolltilldelningen.

    Information om hur du utökar en rolltilldelning finns i [Utöka eller förnya Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-renew-extend.md)
- [Konfigurera Azure-resursrollinställningar i Privilegierad identitetshantering](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure AD-roller i privilegierad identitetshantering](pim-how-to-add-role-to-user.md)
