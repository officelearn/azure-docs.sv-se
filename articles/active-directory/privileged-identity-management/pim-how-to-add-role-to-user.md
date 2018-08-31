---
title: Tilldela Azure AD-katalogroller i PIM | Microsoft Docs
description: Lär dig hur du tilldelar Azure AD-katalogroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 33bfe28bf612c47c9f42345dabccc017337c3d45
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190164"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>Tilldela Azure AD-katalogroller i PIM

Med Azure Active Directory (AD Azure), en Global administratör kan göra **permanent** directory rolltilldelningar. De här rolltilldelningarna kan skapas med den [Azure-portalen](../users-groups-roles/directory-assign-admin-roles.md) eller med hjälp av [PowerShell-kommandon](/powershell/module/azuread#directory_roles).

Azure AD Privileged Identity Management (PIM)-tjänsten kan också Privilegierade rolladministratörer att göra permanenta directory rolltilldelningar. Dessutom Privilegierade rolladministratörer kan utse användare **berättigade** för katalogroller. En berättigad administratör kan aktivera rollen när de behöver den, och sedan deras behörigheter går ut när de är klar. Läs om hur de roller som du kan hantera med hjälp av PIM [Azure AD-katalogroller som du kan hantera i PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Gör en användare som är tillgängliga för en roll

Följ dessa steg om du vill göra en användare som är berättigade till en Azure AD directory-roll.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i den [privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roll.

    Information om hur du ger en annan administratör för användaråtkomst för att hantera PIM finns [bevilja åtkomst till andra administratörer att hantera PIM](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD Privileged Identity Management**.

    Om du inte har startat PIM i Azure portal ännu, går du till [börja använda PIM](pim-getting-started.md).

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **roller** eller **medlemmar**.

    ![Azure AD-katalogroller](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Klicka på **Lägg till medlem** öppna Lägg till hanterade medlemmar.

1. Klicka på **Välj en roll**, klickar du på en roll som du vill hantera och klicka sedan på **Välj**.

    ![Välj en roll](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Klicka på **Välj medlemmar**, markera de användare som du vill tilldela rollen och klickar sedan på **Välj**.

    ![Välj en roll](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Lägg till hanterade medlemmar, klicka på **OK** att lägga till användaren till rollen.

1. Klicka på den roll som du tilldelats finns i listan över medlemmar i listan över roller.

     När rollen tilldelas den användaren som du har valt visas i listan över medlemmar som **berättigade** för rollen.

    ![Användare som är berättigade till en roll](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nu när du är berättigad till rollen kan informera dem om att de kan aktivera enligt anvisningarna i [aktivera mitt Azure AD-katalogroller i PIM](pim-how-to-activate-role.md).

    Berättigade administratörer uppmanas att registrera dig för Azure Multi-Factor Authentication (MFA) under aktiveringen. Om en användare kan inte registrera för MFA, eller använder ett Microsoft-konto (vanligtvis @outlook.com), måste du se dem permanent i deras roller.

## <a name="make-a-role-assignment-permanent"></a>Göra en rolltilldelning permanenta

Som standard är endast nya användare berättigade till en katalogroll. Följ dessa steg om du vill göra en rolltilldelning permanenta.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **medlemmar**.

    ![Listan över medlemmar](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Klicka på en **berättigade** roll som du vill göra permanenta.

1. Klicka på **mer** och klicka sedan på **gör permanent**.

    ![Göra rolltilldelning permanenta](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rollen visas nu som **permanent**.

    ![Listan över medlemmar med permanent ändring](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Ta bort en användare från en roll

Du kan ta bort användare från rolltilldelningar, men se till att det finns alltid minst en användare som är en permanent Global administratör. Om du inte vet vilka användare behöver fortfarande ha sin rolltilldelningar kan du [starta en åtkomstgranskning för rollen](pim-how-to-start-security-review.md).

Följ dessa steg om du vill ta bort en specifik användare från en katalogroll.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **medlemmar**.

    ![Listan över medlemmar](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Klicka på en rolltilldelning som du vill ta bort.

1. Klicka på **mer** och klicka sedan på **ta bort**.

    ![Ta bort en roll](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Klicka på meddelandet som ber dig bekräfta **Ja**.

    ![Ta bort en roll](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Rolltilldelningen har tagits bort.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure AD directory rollinställningar i PIM](pim-how-to-change-default-settings.md)
- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
