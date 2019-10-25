---
title: Tilldela Azure AD-roller i Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Lär dig hur du tilldelar Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad03e69df4ca03d58bba98b912ade354f90042e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809211"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Tilldela Azure AD-roller i Privileged Identity Management

Med Azure Active Directory (Azure AD) kan en global administratör göra **permanenta** Azure AD-administrativa roll tilldelningar. Roll tilldelningarna kan skapas med hjälp av [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) eller med [PowerShell-kommandon](/powershell/module/azuread#directory_roles).

Tjänsten Azure AD Privileged Identity Management (PIM) gör det också möjligt för privilegierade roll administratörer att göra permanenta administratörs roll tilldelningar. Dessutom kan administratörer med privilegierade roller göra användare **berättigade** till administratörs roller i Azure AD. En berättigad administratör kan aktivera rollen när de behöver den och sedan upphör deras behörigheter när de är klara.

## <a name="make-a-user-eligible-for-a-role"></a>Gör en användare berättigad till en roll

Följ dessa steg om du vill göra en användare tillgänglig för en administratörs roll för Azure AD.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Information om hur du beviljar en annan administratörs åtkomst till att hantera Privileged Identity Management finns i [bevilja åtkomst till andra administratörer för att hantera Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD Privileged Identity Management**.

    Om du inte har startat Privileged Identity Management i Azure Portal ännu går du till [börja använda Privileged Identity Management](pim-getting-started.md).

1. Välj **Azure AD-roller**.

1. Välj **roller** eller **medlemmar**.

    ![Meny alternativ för Azure AD-roller med roller och medlemmar markerade](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Välj **Lägg till medlem** för att öppna Lägg till hanterade medlemmar.

1. Välj **Välj en roll**, Välj en roll som du vill hantera och välj sedan **Välj**.

    ![Välj ett roll fönster som visar Azure AD-roller](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Välj **Välj medlemmar**, Välj de användare som du vill tilldela rollen och välj sedan **Välj**.

    ![Fönstret Välj medlemmar där du kan välja en användare](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. I Lägg till hanterade medlemmar väljer du **OK** för att lägga till användaren i rollen.

1. I listan över roller väljer du den roll som du just har tilldelat för att visa listan över medlemmar.

     När rollen har tilldelats visas den användare som du har valt i listan medlemmar som **giltig** för rollen.

    ![Medlemmar i en roll visas tillsammans med deras aktiverings tillstånd](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nu när användaren är berättigad till rollen kan de se att de kan aktivera den enligt anvisningarna i [Aktivera mina Azure AD-roller i Privileged Identity Management](pim-how-to-activate-role.md).

    Berättigade administratörer uppmanas att registrera sig för Azure Multi-Factor Authentication (MFA) under aktiveringen. Om en användare inte kan registrera sig för MFA, eller använder en Microsoft-konto (vanligt vis @outlook.com), måste du göra dem permanenta i alla sina roller.

## <a name="make-a-role-assignment-permanent"></a>Gör en roll tilldelning permanent

Som standard är nya användare endast berättigade till en administratörs roll för Azure AD. Följ de här stegen om du vill göra en roll tilldelning permanent.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **medlemmar**.

    ![Azure AD-roller – medlems lista som visar roll-och aktiverings tillstånd](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Välj en **berättigad** roll som du vill göra permanent.

1. Välj **mer** och välj sedan **skapa**behörighet.

    ![I fönstret visas en användare som är berättigad till en roll med fler meny alternativ öppna](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rollen visas nu som **permanent**.

    ![Medlems lista som visar roll-och aktiverings status som nu är permanent](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Ta bort en användare från en roll

Du kan ta bort användare från roll tilldelningar, men se till att det alltid finns minst en användare som är en permanent global administratör. Om du inte är säker på vilka användare som fortfarande behöver sina roll tilldelningar kan du [starta en åtkomst granskning för rollen](pim-how-to-start-security-review.md).

Följ dessa steg om du vill ta bort en särskild användare från en administratörs roll för Azure AD.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **medlemmar**.

    ![Azure AD-roller – medlems lista som visar roll och aktivering stat](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Välj den roll tilldelning som du vill ta bort.

1. Välj **mer** och **ta sedan bort**.

    ![Fönster som visar en användare som har en permanent roll med fler meny alternativ öppna](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. När du uppmanas att bekräfta åtgärden väljer du **Ja**.

    ![Meddelande som frågar om du vill ta bort medlemmen från rollen](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Roll tilldelningen tas bort.

## <a name="authorization-error-when-assigning-roles"></a>Auktoriseringsfel vid tilldelning av roller

Scenario: som en aktiv ägare eller administratör för användar åtkomst för en Azure-resurs kan du se din resurs inuti Privileged Identity Management men inte utföra några åtgärder som att göra en berättigad tilldelning eller Visa en lista över roll tilldelningar från resurs översikts sida. Alla dessa åtgärder resulterar i ett auktoriseringsfel.

För att tilldela roller måste MS-PIM-tjänstens huvud konto tilldelas [rollen administratör för användar åtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) i rollbaserad åtkomst kontroll för Azure-resurs åtkomst (till skillnad från administrations roller för Azure AD). I stället för att vänta tills MS-PIM tilldelas rollen administratör för användar åtkomst kan du tilldela den manuellt.

Följande steg tilldelar rollen administratör för användar åtkomst till tjänstens huvud namn för MS-PIM för en prenumeration.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör i din Azure AD-organisation.

1. Välj **alla tjänster** och sedan **prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)** .

1. Välj **roll tilldelningar** för att se den aktuella listan över roll tilldelningar i prenumerations omfattningen.

   ![Åtkomst kontroll (IAM) bladet för en prenumeration](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Kontrol lera om tjänstens huvud namn för **MS-PIM** har tilldelats rollen **administratör för användar åtkomst** .

1. Om inte väljer du **Lägg till roll tilldelning** för att öppna fönstret **Lägg till roll tilldelning** .

1. Välj rollen **användar åtkomst administratör** i list rutan **roll** .

1. I listan **Välj** letar du reda på och väljer tjänstens huvud namn för **MS-PIM** .

   ![Fönstret Lägg till roll tilldelning – Lägg till behörigheter för MS-PIM-tjänstens huvud namn](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Välj **Spara** för att tilldela rollen.

   Efter en liten stund tilldelas tjänstens huvud namn rollen administratör för användar åtkomst i prenumerations omfånget.

   ![Åtkomst kontroll (IAM) bladet som visar roll tilldelning av användar åtkomst administratör för MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningarna för Azure AD admin-rollen i Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Tilldela Azure-resurs roller i Privileged Identity Management](pim-resource-roles-assign-roles.md)
