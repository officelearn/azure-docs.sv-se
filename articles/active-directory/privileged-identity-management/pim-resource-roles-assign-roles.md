---
title: Tilldela Azure-resursroller i PIM | Microsoft Docs
description: Lär dig hur du tilldelar Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 19c1e5bfdd7a510a52e23cc3f216a6b0c4448ce7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155229"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Tilldela Azure-resursroller i PIM

Azure AD PIM kan hantera inbyggda Azure-resursroller, samt anpassade roller, inklusive (men inte begränsat till):

- Ägare
- Administratör för användaråtkomst
- Deltagare
- Säkerhetsadministratör
- Säkerhetshanteraren och mycket mer

>[!NOTE]
Användare eller medlemmar i en grupp som tilldelats ägare eller administratör för användaråtkomst roller och globala administratörer som aktiverar prenumerationshantering i Azure AD är resursen administratörer. Dessa administratörer kan tilldela roller, konfigurera inställningar för serverrollen och granska åtkomst med hjälp av PIM för Azure-resurser. Visa en lista över [inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Tilldela en roll

Följ dessa steg om du vill göra en användare som är berättigade till en Azure-resurs-roll.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i den [privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roll.

    Information om hur du ger en annan administratör för användaråtkomst för att hantera PIM finns [bevilja åtkomst till andra administratörer att hantera PIM](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD Privileged Identity Management**.

    Om du inte har startat PIM i Azure portal ännu, går du till [börja använda PIM](pim-getting-started.md).

1. Klicka på **Azure-resurser**.

1. Använd den **resursfilter** att filtrera listan över hanterade resurser.

    ![Lista över Azure-resurser att hantera](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Klicka på resursen som du vill hantera, till exempel en prenumerations- eller grupp.

1. Klicka på under hantera, **roller** vill se en lista över roller för Azure-resurser.

    ![Azure-resursroller](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Klicka på **Lägg till medlem** att öppna tilldelningsfönstret ny.

1. Klicka på **Välj en roll** att öppna dialogrutan Välj en roll-fönstret.

    ![Ny tilldelningsfönstret](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Klicka på en roll som du vill tilldela och klicka sedan på **Välj**.

    Fönstret Välj en medlem eller grupp öppnas.

1. Klicka på en medlem eller en grupp som du vill tilldela rollen och klickar sedan på **Välj**.

    ![Välj ett fönster som är medlem eller grupp](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Inställningsfönstret medlemskap öppnas.

1. I den **Tilldelningstyp** väljer **berättigade** eller **Active**.

    ![Medlemskap i inställningsfönstret](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    PIM för Azure-resurser tillhandahåller två distinkta tilldelningstyperna:

    - **Berättigade** tilldelningar kräver medlem i rollen att utföra en åtgärd för att använda rollen. Åtgärder kan innehålla utför en kontroll av multifaktorautentisering (MFA), vilket ger en motivering eller begära godkännande från utnämnda godkännare.

    - **Aktiva** tilldelningar inte kräver medlemmen som ska utföra alla åtgärder för att använda rollen. Medlemmar som är tilldelad som aktiv har de behörigheter som tilldelats rollen hela tiden.

1. Om tilldelningen ska vara permanent (permanent berättigad eller permanent tilldelade), Välj den **permanent** markerar du kryssrutan.

    Beroende på rollinställningar, markera kryssrutan visas inte eller kan vara unmodifiable.

1. Avmarkera kryssrutan om du vill ange en viss tilldelningsvaraktighet, och ändra start/slut eller datum och tid rutorna.

    ![Inställningar för medlemskap - datum och tid](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Klicka på **Klar** när du är klar.

    ![Ny tilldelning - Lägg till](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Klicka för att skapa den nya rolltilldelningen **Lägg till**. En avisering om status visas.

    ![Ny tilldelning - meddelande](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Uppdatera eller ta bort en rolltilldelning

Följ dessa steg om du vill uppdatera eller ta bort en rolltilldelning.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på resursen som du vill hantera, till exempel en prenumerations- eller grupp.

1. Klicka på under hantera, **roller** vill se en lista över roller för Azure-resurser.

    ![Azure-resursroller – Välj roll](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Klicka på den roll som du vill uppdatera eller ta bort.

1. Hitta rolltilldelningen på den **berättigade roller** eller **aktiva roller** flikar.

    ![Uppdatera eller ta bort rolltilldelning](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Klicka på **uppdatera** eller **ta bort** att uppdatera eller ta bort rolltilldelningen.

    Information om hur du utökar en rolltilldelning finns i [utöka eller förnya Azure-resursroller i PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azure-resursroller i PIM](pim-resource-roles-renew-extend.md)
- [Konfigurera Azure-resurs rollinställningar i PIM](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure AD-katalogroller i PIM](pim-how-to-add-role-to-user.md)
