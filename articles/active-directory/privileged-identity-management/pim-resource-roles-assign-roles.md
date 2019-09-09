---
title: Tilldela Azure-resurs roller i PIM – Azure Active Directory | Microsoft Docs
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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2850f17c96ef031f9e1b8e11558ab369e4175b0
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804281"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Tilldela Azure-resurs roller i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan hantera de inbyggda Azure-resurs rollerna, samt anpassade roller, inklusive (men inte begränsat till):

- Ägare
- Administratör för användaråtkomst
- Deltagare
- Säkerhetsadministratör
- Security Manager med mera

> [!NOTE]
> Användare eller medlemmar i en grupp som tilldelats rollen ägare eller administratör för användar åtkomst och globala administratörer som aktiverar prenumerations hantering i Azure AD är resurs administratörer. Administratörerna kan tilldela roller, konfigurera roll inställningar och granska åtkomst med hjälp av PIM för Azure-resurser. Det vill säga att kontot inte har behörighet att hantera PIM för resurser om användaren inte har en resurs administratörs roll. Visa listan över [inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Tilldela en roll

Följ dessa steg om du vill göra en användare tillgänglig för en Azure-resurs roll.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Information om hur du beviljar en annan administratörs åtkomst för att hantera PIM finns i [bevilja åtkomst till andra administratörer för att hantera PIM](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD Privileged Identity Management**.

    Om du inte har startat PIM i Azure Portal ännu går du till [börja använda PIM](pim-getting-started.md).

1. Klicka på **Azure-resurser**.

1. Använd **resurs filtret** om du vill filtrera listan över hanterade resurser.

    ![Lista över Azure-resurser som ska hanteras](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Klicka på den resurs som du vill hantera, till exempel en prenumeration eller hanterings grupp.

1. Under hantera klickar du på **roller** för att se listan över roller för Azure-resurser.

    ![Roller för Azure-resurser](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Klicka på **Lägg till medlem** för att öppna fönstret nytt uppdrag.

1. Klicka på **Välj en roll** för att öppna fönstret Välj en roll.

    ![Nytt tilldelnings fönster](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Klicka på en roll som du vill tilldela och klicka sedan på **Välj**.

    Fönstret Välj en medlem eller grupp öppnas.

1. Klicka på en medlem eller grupp som du vill tilldela rollen och klicka sedan på **Välj**.

    ![Välj en medlem eller grupp fönster](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Fönstret medlemskaps inställningar öppnas.

1. I listan **tilldelnings typ** väljer du **kvalificerad** eller **aktiv**.

    ![Fönstret medlemskaps inställningar](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    PIM för Azure-resurser innehåller två olika tilldelnings typer:

    - **Berättigade** tilldelningar kräver att rollen medlem av rollen utför en åtgärd för att använda rollen. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare.

    - **Aktiva** tilldelningar kräver inte att medlemmen utför någon åtgärd för att använda rollen. Medlemmar som tilldelas som aktiva har behörigheten alltid tilldelade till rollen.

1. Om tilldelningen ska vara permanent (permanent kvalificerad eller permanent tilldelad) markerar du kryss rutan **permanent** .

    Beroende på roll inställningarna kanske kryss rutan inte visas eller kan vara avändrings bar.

1. Om du vill ange en varaktighet för en speciell tilldelning avmarkerar du kryss rutan och ändrar rutorna start och/eller slutdatum och tid.

    ![Medlemskaps inställningar-datum och tid](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Klicka på **Klar** när du är klar.

    ![Ny tilldelning – Lägg till](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Klicka på **Lägg till**för att skapa den nya roll tilldelningen. Ett meddelande om status visas.

    ![Ny tilldelning – meddelande](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Uppdatera eller ta bort en befintlig roll tilldelning

Följ dessa steg om du vill uppdatera eller ta bort en befintlig roll tilldelning.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill hantera, till exempel en prenumeration eller hanterings grupp.

1. Under hantera klickar du på **roller** för att se listan över roller för Azure-resurser.

    ![Azure-resurs roller – Välj roll](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Klicka på den roll som du vill uppdatera eller ta bort.

1. Hitta roll tilldelningen på flikarna **berättigade roller** eller **aktiva roller** .

    ![Uppdatera eller ta bort roll tilldelning](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Klicka på **Uppdatera** eller **ta bort** om du vill uppdatera eller ta bort roll tilldelningen.

    Information om hur du utökar en roll tilldelning finns i [utöka eller förnya Azure-resurs roller i PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azure Resource roles i PIM](pim-resource-roles-renew-extend.md)
- [Konfigurera inställningar för Azure-resurs roller i PIM](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure AD-roller i PIM](pim-how-to-add-role-to-user.md)
