---
title: Tilldela användare med hjälp av Azure AD PIM katalogroller | Microsoft Docs
description: Lär dig mer om att tilldela directory roller till användare som använder Azure Active Directory Privileged Identity Management och Azure-portalen.
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
ms.openlocfilehash: 1aede38cabba7f9811f2b9320bc1e9a9da857f08
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621821"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>Tilldela användare med hjälp av Azure AD PIM katalogroller

Med Azure Active Directory (AD Azure), en Global administratör kan göra **permanent** directory rolltilldelningar. De här rolltilldelningarna kan skapas med den [Azure-portalen](../users-groups-roles/directory-assign-admin-roles.md) eller med hjälp av [PowerShell-kommandon](/powershell/module/azuread#directory_roles).

Azure AD Privileged Identity Management (PIM)-tjänsten kan också Privilegierade rolladministratörer att göra permanenta directory rolltilldelningar. Dessutom Privilegierade rolladministratörer kan utse användare **berättigade** för katalogroller. En berättigad administratör kan aktivera rollen när de behöver den, och sedan deras behörigheter går ut när de är klar. Läs om hur de roller som du kan hantera med hjälp av PIM [katalogroller som du kan hantera med hjälp av Azure AD PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Gör en användare som är tillgängliga för en roll

Följ dessa steg om du vill göra en användare som är berättigade till en Azure AD directory-roll.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i den [privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roll.

    Information om hur du ger en annan användaråtkomst för att hantera PIM finns [ge åtkomst till PIM](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD Privileged Identity Management**.

    Om du inte har aktiverat PIM i Azure portal ännu, går du till [Kom igång med Azure AD PIM](pim-getting-started.md).

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **roll (förhandsversion)** eller **medlemmar**.

    ![Azure AD-katalogroller](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Klicka på **Lägg till medlem** öppna Lägg till hanterade medlemmar.

1. Klicka på **Välj en roll**, klickar du på en roll som du vill hantera och klicka sedan på **Välj**.

    ![Välj en roll](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Klicka på **Välj medlemmar**, markera de användare som du vill tilldela rollen och klickar sedan på **Välj**.

    ![Välj en roll](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Lägg till hanterade medlemmar, klicka på **OK** att lägga till användaren till rollen.

     När rollen tilldelas den användaren som du har valt visas i listan över medlemmar som **berättigade** för rollen.

    ![Användare som är berättigade till en roll](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nu när du är berättigad till rollen kan informera dem om att de kan aktivera enligt anvisningarna i [aktivera eller inaktivera en roll](pim-how-to-activate-role.md).

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
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
