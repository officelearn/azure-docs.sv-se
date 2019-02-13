---
title: Bjud in extern användare och tilldela Azure-resursroller i PIM | Microsoft Docs
description: Lär dig hur du bjuder in externa användare och tilldela roller i Azure-resurs i Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90d0d3d3f484044a0ffbab7a3c24a76c40aa74c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208292"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>Bjud in extern användare och tilldela Azure-resursroller i PIM

Azure Activity Directory (Azure AD) business-to-business (B2B) är en uppsättning funktioner i Azure AD som gör det möjligt för organisationer att samarbeta med externa användare och leverantörer med hjälp av ett konto. När du kombinerar B2B med Azure AD Privileged Identity Management (PIM) kan fortsätta du att tillämpa dina krav på efterlevnad och styrning för externa användare. Du kan till exempel använda funktionerna PIM för Azure-resurser med externa användare:

- Tilldela åtkomst till specifika Azure-resurser
- Aktivera just-in-time-åtkomst
- Ange tilldelning varaktighet och slutdatum
- Kräv MFA vid aktiv uppgift eller aktivering
- Utföra åtkomstgranskningar
- Använda aviseringar och granskningsloggar

Den här artikeln beskriver hur du bjuder in en extern användare till din katalog och hantera åtkomsten till Azure-resurser med PIM.

## <a name="when-would-you-invite-external-users"></a>När bjuder du externa användare?

Här följer några scenarier när du kan bjuda in externa användare till din katalog:

- Tillåt att en extern egen företagare leverantör som bara har ett e-postkonto för att få åtkomst till dina Azure-resurser för ett projekt.
- Tillåt att en extern partner i en stor organisation som använder en lokal Active Directory Federation Services för att komma åt ditt program för utgiftsrapport.
- Tillåt supporttekniker inte i din organisation (till exempel Microsoft-supporten) tillfälligt åtkomst till din Azure-resurs för att felsöka problem.

## <a name="how-does-external-collaboration-using-b2b-work"></a>Hur fungerar externt samarbete med B2B fungera?

När du använder B2B, kan du bjuda in en extern användare till din katalog. Den externa användaren verkar vara i din katalog, men användaren har inte några autentiseringsuppgifter som är kopplade till den. Varje gång som en extern användare har autentiseras, måste de autentiseras i deras hemkatalog och inte din katalog. Det innebär att om den externa användaren har inte längre åtkomst till deras hemkatalog, de automatiskt förlorar åtkomst till din katalog. Till exempel om den externa användaren lämnar organisationen, förlora de automatiskt åtkomsten till eventuella resurser som delas med dem i din katalog utan att du behöver göra något. Läs mer om B2B [vad är gästanvändares åtkomst i Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![B2B och extern användare](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>Kontrollera inställningar för externt samarbete

Om du vill se till att du kan bjuda in externa användare till din katalog, bör du kontrollera inställningar för externt samarbete.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Klicka på **Azure Active Directory** > **användarinställningar**.

1. Klicka på **hantera inställningar för externt samarbete**.

    ![Inställningar för externt samarbete](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Se till att den **administratörer och användare i gästinbjudarrollen kan bjuda in** anges för växeln **Ja**.

## <a name="invite-an-external-user-and-assign-a-role"></a>Bjud in en extern användare och tilldela en roll

Använda PIM kan du bjuda in en extern användare och gör dem tillgängliga för en Azure-resurs roll precis som en medlemsanvändare.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i den [privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) eller [Användarkontoadministratören](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator) roll.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Använd den **resursfilter** att filtrera listan över hanterade resurser.

1. Klicka på den resurs som du vill hantera, till exempel en resurs, resursgrupp, prenumeration eller hanteringsgrupp.

    Du bör ange omfånget för den externa användaren behöver bara.

1. Klicka på under hantera, **roller** vill se en lista över roller för Azure-resurser.

    ![Azure-resursroller](./media/pim-resource-roles-external-users/resources-roles.png)

1. Klicka på den lägsta roll som användaren behöver.

    ![Vald roll](./media/pim-resource-roles-external-users/selected-role.png)

1. På rollsidan **Lägg till medlem** att öppna tilldelningsfönstret ny.

1. Klicka på **Välj en medlem eller en grupp**.

    ![Välj en medlem eller grupp](./media/pim-resource-roles-external-users/select-member-group.png)

1. Om du vill bjuda in en extern användare, klickar du på **bjuda in**.

    ![Bjud in en gäst](./media/pim-resource-roles-external-users/invite-guest.png)

1. När du har angett en extern användare, klickar du på **bjuda in**.

    Den externa användaren ska läggas till som en vald medlem.

1. I fönstret Välj en medlem eller grupp klickar du på **Välj**.

1. I inställningsfönstret medlemskap väljer du Tilldelningstyp och varaktighet.

    ![Medlemskapsinställningar](./media/pim-resource-roles-external-users/membership-settings.png)

1. För att slutföra tilldelningen klickar du på **klar** och sedan **Lägg till**.

    Rolltilldelning för extern användare visas i listan med rollen.

    ![Rolltilldelningen för extern användare](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>Aktivera rollen som en extern användare

Som en extern användare måste du först acceptera inbjudan till Azure AD-katalog och eventuellt aktivera din roll.

1. Öppna e-postmeddelandet med din kataloginbjudan. E-postmeddelandet ser ut ungefär så här.

    ![E-postinbjudan](./media/pim-resource-roles-external-users/email-invite.png)

1. Klicka på den **börjar** länken i e-postmeddelandet.

1. När du har granskat behörigheterna, klickar du på **acceptera**.

    ![Granska behörigheter](./media/pim-resource-roles-external-users/invite-accept.png)

1. Du kan bli ombedd att acceptera användningsvillkor och ange om du vill förbli inloggad.

    Azure-portalen öppnas. Om du är bara berättigad för en roll kan du inte åtkomst till resurser.

1. Aktivera din roll genom att öppna e-postmeddelande med din aktivera rollen länk. E-postmeddelandet ser ut ungefär så här.

    ![E-postinbjudan](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Klicka på **aktivera rollen** att öppna dina berättigade roller i PIM.

    ![Mina roller - kvalificerade](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Under åtgärden, klickar du på den **aktivera** länk.

    Beroende på rollinställningar behöver du ange viss information för att aktivera rollen.

1. När du har angett inställningarna för rollen, klickar du på **aktivera** aktiverar rollen.

    ![Aktivera roll](./media/pim-resource-roles-external-users/activate-role.png)

    Om inte administratören behöver godkänna din begäran ska ha åtkomst till de angivna resurserna.

## <a name="view-activity-for-an-external-user"></a>Visa aktivitet för en extern användare

Du kan visa granskningsloggarna för att hålla reda på vad externa användare gör precis som en medlemsanvändare.

1. Som administratör, öppna PIM och välj den resurs som har delats.

1. Klicka på **resursgranskning** att visa aktiviteten för den resursen. Nedan visas ett exempel på aktiviteten för en resursgrupp.

    ![Resursgranskning](./media/pim-resource-roles-external-users/audit-resource.png)

1. Om du vill visa aktivitet för den externa användaren, klickar du på **Azure Active Directory** > **användare** > extern användare.

1. Klicka på **granskningsloggar** att se granskningsloggarna för katalogen. Om det behövs kan du ange filter.

    ![Kataloggranskning](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-katalogroller i PIM](pim-how-to-add-role-to-user.md)
- [Vad är gästanvändares åtkomst i Azure Active Directory B2B?](../b2b/what-is-b2b.md)
