---
title: Tilldela Azure AD-roller i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur du tilldelar Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07259d90c7119dec4ca9139e10af2fb20a439425
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492417"
---
# <a name="assign-azure-ad-roles-in-pim"></a>Tilldela Azure AD-roller i PIM

Med Azure Active Directory (AD Azure), en Global administratör kan göra **permanent** rolltilldelningar i Azure AD-administratör. De här rolltilldelningarna kan skapas med den [Azure-portalen](../users-groups-roles/directory-assign-admin-roles.md) eller med hjälp av [PowerShell-kommandon](/powershell/module/azuread#directory_roles).

Tjänsten Azure AD Privileged Identity Management (PIM) kan också Privilegierade Rolladministratörer skapa permanent administratör rolltilldelningar. Dessutom kan administratörer med privilegierad roll kan utse användare **berättigade** för Azure AD-administratörsroller. En berättigad administratör kan aktivera rollen när de behöver den, och sedan deras behörigheter går ut när de är klar.

## <a name="make-a-user-eligible-for-a-role"></a>Gör en användare som är tillgängliga för en roll

Följ dessa steg om du vill göra en användare som är berättigade till en Azure AD-administratörsroll.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i den [privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roll.

    Information om hur du ger en annan administratör för användaråtkomst för att hantera PIM finns [bevilja åtkomst till andra administratörer att hantera PIM](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD Privileged Identity Management**.

    Om du inte har startat PIM i Azure portal ännu, går du till [börja använda PIM](pim-getting-started.md).

1. Klicka på **Azure AD-roller**.

1. Klicka på **roller** eller **medlemmar**.

    ![Azure AD-roller](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Klicka på **Lägg till medlem** öppna Lägg till hanterade medlemmar.

1. Klicka på **Välj en roll**, klickar du på en roll som du vill hantera och klicka sedan på **Välj**.

    ![Välj en roll](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Klicka på **Välj medlemmar**, markera de användare som du vill tilldela rollen och klickar sedan på **Välj**.

    ![Välj en roll](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Lägg till hanterade medlemmar, klicka på **OK** att lägga till användaren till rollen.

1. Klicka på den roll som du tilldelats finns i listan över medlemmar i listan över roller.

     När rollen tilldelas den användaren som du har valt visas i listan över medlemmar som **berättigade** för rollen.

    ![Användare som är berättigade till en roll](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nu när du är berättigad till rollen kan informera dem om att de kan aktivera enligt anvisningarna i [aktivera Mina Azure AD-roller i PIM](pim-how-to-activate-role.md).

    Berättigade administratörer uppmanas att registrera dig för Azure Multi-Factor Authentication (MFA) under aktiveringen. Om en användare kan inte registrera för MFA, eller använder ett Microsoft-konto (vanligtvis @outlook.com), måste du se dem permanent i deras roller.

## <a name="make-a-role-assignment-permanent"></a>Göra en rolltilldelning permanenta

Som standard är endast nya användare berättigade till en Azure AD-administratörsroll. Följ dessa steg om du vill göra en rolltilldelning permanenta.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **medlemmar**.

    ![Listan över medlemmar](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Klicka på en **berättigade** roll som du vill göra permanenta.

1. Klicka på **mer** och klicka sedan på **gör permanent**.

    ![Göra rolltilldelning permanenta](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rollen visas nu som **permanent**.

    ![Listan över medlemmar med permanent ändring](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Ta bort en användare från en roll

Du kan ta bort användare från rolltilldelningar, men se till att det finns alltid minst en användare som är en permanent Global administratör. Om du inte vet vilka användare behöver fortfarande ha sin rolltilldelningar kan du [starta en åtkomstgranskning för rollen](pim-how-to-start-security-review.md).

Följ dessa steg om du vill ta bort en specifik användare från en Azure AD-administratörsroll.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **medlemmar**.

    ![Listan över medlemmar](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Klicka på en rolltilldelning som du vill ta bort.

1. Klicka på **mer** och klicka sedan på **ta bort**.

    ![Ta bort en roll](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Klicka på meddelandet som ber dig bekräfta **Ja**.

    ![Ta bort en roll](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Rolltilldelningen har tagits bort.

## <a name="authorization-error-when-assigning-roles"></a>Behörighetsfel när du tilldelar roller

Om du nyligen har aktiverat PIM för en prenumeration och du får ett felmeddelande för auktorisering när du försöker göra en användare som är berättigade till en Azure AD-administratörsroll, kanske eftersom tjänstens huvudnamn som MS-PIM ännu inte har de behörigheter som krävs. Tjänstens huvudnamn som MS-PIM måste ha den [administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) roll att tilldela roller till andra. I stället för att vänta tills MS-PIM har tilldelats rollen Administratör för användaråtkomst, kan du tilldela den manuellt.

Följ dessa steg om du vill tilldela rollen Administratör för användaråtkomst till MS-PIM tjänstens huvudnamn för en prenumeration.

1. Logga in på Azure portal som Global administratör.

1. Välj **alla tjänster** och sedan **prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)**.

1. Välj **rolltilldelningar** att se den aktuella listan över rolltilldelningar prenumerationsområde.

   ![Åtkomstkontroll (IAM)-bladet för en prenumeration](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Kontrollera om den **MS-PIM** tjänstens huvudnamn är tilldelad den **administratör för användaråtkomst** roll.

1. Om inte, Välj **Lägg till rolltilldelning** att öppna den **Lägg till rolltilldelning** fönstret.

1. I den **rollen** listrutan, väljer den **administratör för användaråtkomst** roll.

1. I den **Välj** listan, hitta och välj den **MS-PIM** tjänstens huvudnamn.

   ![Lägg till behörigheter för MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Välj **spara** du tilldela rollen.

   Efter en liten stund tilldelas MS-PIM-tjänstens huvudnamn rollen Administratör för användaråtkomst prenumerationsområde.

   ![Rollen Administratör för användaråtkomst för MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure AD-administratör rollinställningar i PIM](pim-how-to-change-default-settings.md)
- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
