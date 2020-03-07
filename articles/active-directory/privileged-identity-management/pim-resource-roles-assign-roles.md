---
title: Tilldela Azure-resurs roller i Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Lär dig hur du tilldelar Azure Resource roles i Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375484"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Tilldela Azure-resurs roller i Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan hantera de inbyggda Azure-resurs rollerna, samt anpassade roller, inklusive (men inte begränsat till):

- Ägare
- Administratör för användaråtkomst
- Deltagare
- Säkerhetsadministratör
- Säkerhets hanterare

> [!NOTE]
> Användare eller medlemmar i en grupp som har tilldelats prenumerations rollerna ägare eller användar åtkomst administratör och globala Azure AD-administratörer som aktiverar prenumerations hantering i Azure AD har resurs administratörs behörighet som standard. Dessa administratörer kan tilldela roller, konfigurera roll inställningar och granska åtkomst med hjälp av Privileged Identity Management för Azure-resurser. En användare kan inte hantera Privileged Identity Management för resurser utan resurs administratörs behörighet. Visa listan över [inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Tilldela en roll

Följ dessa steg om du vill göra en användare tillgänglig för en Azure-resurs roll.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Information om hur du beviljar en annan administratörs åtkomst till att hantera Privileged Identity Management finns i [bevilja åtkomst till andra administratörer för att hantera Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser**.

1. Använd **resurs filtret** om du vill filtrera listan över hanterade resurser.

    ![Lista över Azure-resurser som ska hanteras](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Välj den resurs som du vill hantera, till exempel en prenumeration eller hanterings grupp.

1. Under hantera väljer du **roller** för att se listan över roller för Azure-resurser.

    ![Roller för Azure-resurser](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Välj **Lägg till medlem** för att öppna fönstret nytt uppdrag.

1. Välj **Välj en roll** för att öppna fönstret Välj en roll.

    ![Nytt tilldelnings fönster](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Välj en roll som du vill tilldela och klicka sedan på **Välj**.

    Fönstret Välj en medlem eller grupp öppnas.

1. Välj en medlem eller grupp som du vill tilldela rollen och klicka sedan på **Välj**.

    ![Välj en medlem eller grupp fönster](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Fönstret medlemskaps inställningar öppnas.

1. I listan **tilldelnings typ** väljer du **kvalificerad** eller **aktiv**.

    ![Fönstret medlemskaps inställningar](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management för Azure-resurser innehåller två olika tilldelnings typer:

    - **Berättigade** tilldelningar kräver att rollen medlem av rollen utför en åtgärd för att använda rollen. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare.

    - **Aktiva** tilldelningar kräver inte att medlemmen utför någon åtgärd för att använda rollen. Medlemmar som tilldelas som aktiva har behörigheten alltid tilldelade till rollen.

1. Om tilldelningen ska vara permanent (permanent kvalificerad eller permanent tilldelad) markerar du kryss rutan **permanent** .

    Beroende på roll inställningarna kanske kryss rutan inte visas eller kan vara avändrings bar.

1. Om du vill ange en varaktighet för en speciell tilldelning avmarkerar du kryss rutan och ändrar rutorna start och/eller slutdatum och tid.

    ![Medlemskaps inställningar-datum och tid](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. När du är färdig väljer du **klart**.

    ![Ny tilldelning – Lägg till](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Välj **Lägg till**för att skapa den nya roll tilldelningen. Ett meddelande om status visas.

    ![Ny tilldelning – meddelande](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Uppdatera eller ta bort en befintlig roll tilldelning

Följ dessa steg om du vill uppdatera eller ta bort en befintlig roll tilldelning.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill hantera, till exempel en prenumeration eller hanterings grupp.

1. Under hantera väljer du **roller** för att se listan över roller för Azure-resurser.

    ![Azure-resurs roller – Välj roll](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Välj den roll som du vill uppdatera eller ta bort.

1. Hitta roll tilldelningen på flikarna **berättigade roller** eller **aktiva roller** .

    ![Uppdatera eller ta bort roll tilldelning](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Välj **Uppdatera** eller **ta bort** om du vill uppdatera eller ta bort roll tilldelningen.

    Information om hur du utökar en roll tilldelning finns i [utöka eller förnya Azures resurs roller i Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azures resurs roller i Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Konfigurera inställningar för Azure-resurs roll i Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure AD-roller i Privileged Identity Management](pim-how-to-add-role-to-user.md)
