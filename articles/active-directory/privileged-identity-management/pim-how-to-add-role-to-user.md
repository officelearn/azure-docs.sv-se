---
title: Tilldela Azure AD-roller i PIM - Azure Active Directory | Microsoft-dokument
description: Lär dig hur du tilldelar Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253278"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Tilldela Azure AD-roller i privilegierad identitetshantering

Med Azure Active Directory (Azure AD) kan en global administratör göra **permanenta** Azure AD-administratörsrolltilldelningar. Dessa rolltilldelningar kan skapas med hjälp av [Azure-portalen](../users-groups-roles/directory-assign-admin-roles.md) eller med [PowerShell-kommandon](/powershell/module/azuread#directory_roles).

Azure AD Privileged Identity Management (PIM) gör det också möjligt för privilegierade rolladministratörer att göra permanenta administratörsrolltilldelningar. Dessutom kan privilegierade rolladministratörer göra användare **kvalificerade** för Azure AD-administratörsroller. En behörig administratör kan aktivera rollen när de behöver den och sedan upphör deras behörigheter när de är klara.

## <a name="determine-your-version-of-pim"></a>Bestäm din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azure-resursroller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API:et](azure-ad-roles-features.md#api-changes). Medan den nya versionen distribueras, vilka procedurer som du följer i den här artikeln beror på vilken version av privilegierad identitetshantering du har för närvarande. Följ stegen i det här avsnittet för att avgöra vilken version av privilegierad identitetshantering du har. När du känner till din version av Privilegierad identitetshantering kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är i [rollen Privilegierad rolladministratör.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Öppna **Azure AD-privilegierad identitetshantering**. Om du har en banderoll högst upp på översiktssidan följer du instruktionerna på fliken **Ny version** i den här artikeln. Annars följer du instruktionerna på fliken **Föregående version.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Ny version](#tab/new)

## <a name="assign-a-role"></a>Tilldela en roll

Följ dessa steg för att göra en användare kvalificerad för en Azure AD-administratörsroll.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i [rollen Privilegierad rolladministratör.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Information om hur du ger en annan administratör åtkomst till hantering av privilegierad identitetshantering finns i [Bevilja åtkomst till andra administratörer för att hantera privilegierad identitetshantering](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

1. Välj **Roller** om du vill visa listan över roller för Azure AD-behörigheter.

    ![Azure AD-roller](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Välj **Lägg till medlem** om du vill öppna sidan Ny **tilldelning.**

1. Välj **Välj en roll** för att öppna sidan Välj en roll.

    ![Nytt tilldelningsfönster](./media/pim-how-to-add-role-to-user/select-role.png)

1. Välj en roll som du vill tilldela och klicka sedan på **Markera**.

1. Välj en medlem som du vill tilldela rollen till och välj sedan **Välj**.

1. Välj **Kvalificerat** eller **Aktivt**i listan **Tilldelningstyp** i fönstret **Inställningar för medlemskap** .

    - **Kvalificerade** tilldelningar kräver att medlemmen i rollen utför en åtgärd för att använda rollen. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare.

    - **Aktiva** tilldelningar kräver inte att medlemmen utför någon åtgärd för att använda rollen. Medlemmar som har tilldelats som aktiva har de privilegier som tilldelats rollen hela tiden.

1. Om tilldelningen ska vara permanent (permanent berättigad eller permanent tilldelad) markerar du kryssrutan **Permanent.**

    Beroende på rollinställningarna kanske kryssrutan inte visas eller kan vara omodifierbar.

1. Om du vill ange en viss tilldelningstid avmarkerar du kryssrutan och ändrar start- och/eller slutdatum- och tidsrutorna. När du är klar väljer du **Klar**.

    ![Inställningar för medlemskap - datum och tid](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Om du vill skapa den nya rolltilldelningen väljer du **Lägg till**. Ett meddelande om status visas.

    ![Nytt uppdrag - Anmälan](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Uppdatera eller ta bort en befintlig rolltilldelning

Följ dessa steg för att uppdatera eller ta bort en befintlig rolltilldelning.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

1. Välj **Roller** om du vill visa listan över roller för Azure AD.

1. Välj den roll som du vill uppdatera eller ta bort.

1. Hitta rolltilldelningen på flikarna **Kvalificerade roller** eller **Aktiva roller.**

    ![Uppdatera eller ta bort rolltilldelning](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Välj **Uppdatera** eller **Ta bort** om du vill uppdatera eller ta bort rolltilldelningen.

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Göra en användare kvalificerad för en roll

Följ dessa steg för att göra en användare kvalificerad för en Azure AD-administratörsroll.

1. Välj **Roller** eller **Medlemmar**.

    ![Azure AD-roller](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Välj **Lägg till medlem** om du vill öppna Lägg till hanterade **medlemmar**.

1. Välj **en roll,** välj en roll som du vill hantera och välj sedan **Välj**.

    ![Välj en roll](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Välj **Välj medlemmar**, välj de användare som du vill tilldela rollen och välj sedan **Välj**.

    ![Välj en roll](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. I **Lägg till hanterade medlemmar**väljer du **OK** för att lägga till användaren i rollen.

1. I listan över roller väljer du den roll som du just tilldelats för att se listan över medlemmar.

     När rollen har tilldelats visas den användare som du har valt i medlemslistan som **kvalificerad** för rollen.

    ![Användare som är berättigad till en roll](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nu när användaren är berättigad till rollen, låt dem veta att de kan aktivera den enligt instruktionerna i [Aktivera mina Azure AD-roller i Privilegierad identitetshantering](pim-how-to-activate-role.md).

    Kvalificerade administratörer uppmanas att registrera sig för Azure Multi-Factor Authentication (MFA) under aktiveringen. Om en användare inte kan registrera sig för MFA @outlook.comeller använder ett Microsoft-konto (till exempel ) måste du göra dem permanenta i alla sina roller.

## <a name="make-a-role-assignment-permanent"></a>Gör en rolltilldelning permanent

Som standard är nya användare endast *kvalificerade* för en Azure AD-administratörsroll. Följ dessa steg om du vill göra en rolltilldelning permanent.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

1. Välj **medlemmar**.

    ![Lista över medlemmar](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Välj en **kvalificerad** roll som du vill göra permanent.

1. Välj **Mer** och välj sedan **Gör perm**.

    ![Gör rolltilldelningen permanent](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rollen är nu listad som **permanent**.

    ![Lista över medlemmar med permanent ändring](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Ta bort en användare från en roll

Du kan ta bort användare från rolltilldelningar, men se till att det alltid finns minst en användare som är en permanent global administratör. Om du inte är säker på vilka användare som fortfarande behöver sina rolltilldelningar kan du [starta en åtkomstgranskning för rollen](pim-how-to-start-security-review.md).

Följ dessa steg för att ta bort en viss användare från en Azure AD-administratörsroll.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

1. Välj **medlemmar**.

    ![Lista över medlemmar](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Välj en rolltilldelning som du vill ta bort.

1. Välj **Mer** och välj sedan **Ta bort**.

    ![Ta bort en roll](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. I meddelandet som ber dig bekräfta väljer du **Ja**.

    ![Ta bort en roll](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Rolltilldelningen tas bort.

## <a name="authorization-error-when-assigning-roles"></a>Auktoriseringsfel vid tilldelning av roller

Om du nyligen har aktiverat Privilegierad identitetshantering för en prenumeration och du får ett auktoriseringsfel när du försöker göra en användare kvalificerad för en Azure AD-administratörsroll, kan det bero på att MS-PIM-tjänstens huvudnamn ännu inte har rätt behörighet. Ms-PIM-tjänstens huvudnamn måste ha rollen [Administratör för användaråtkomst för](../../role-based-access-control/built-in-roles.md#user-access-administrator) att tilldela roller till andra. I stället för att vänta tills MS-PIM har tilldelats rollen Administratör för användaråtkomst kan du tilldela den manuellt.

Följ dessa steg för att tilldela rollen Administratör för användaråtkomst till MS-PIM-tjänstens huvudnamn för en prenumeration.

1. Logga in på Azure-portalen som global administratör.

1. Välj **Alla tjänster** och sedan **Prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)**.

1. Välj **Rolltilldelningar** om du vill visa den aktuella listan över rolltilldelningar i prenumerationsomfånget.

   ![IAM-blad (Access Control Control) för en prenumeration](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Kontrollera om **MS-PIM-tjänstens** huvudnamn har tilldelats rollen **Administratör för användaråtkomst.**

1. Om inte väljer du **Lägg till rolltilldelning** för att öppna **fönstret Lägg till rolltilldelning.**

1. Välj rollen **Administratör för användaråtkomst i** listrutan **Roll.**

1. Leta reda på och välj huvudnamn för **MS-PIM-tjänsten** i listan **Välj.**

   ![Fönstret Lägga till rolltilldelning - Lägg till behörigheter för MS-PIM-tjänstens huvudnamn](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Välj **Spara** om du vill tilldela rollen.

   Efter en stund tilldelas MS-PIM-tjänstens huvudnamn rollen Administratör för användaråtkomst i prenumerationsomfånget.

   ![Åtkomstkontrollsida som visar administratörstilldelning för användaråtkomst för MS-PIM-tjänstens huvudnamn](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Nästa steg

- [Konfigurera rollinställningar för Azure AD-administratör i privilegierad identitetshantering](pim-how-to-change-default-settings.md)
- [Tilldela Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-assign-roles.md)