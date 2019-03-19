---
title: Bjud in extern gäster och tilldela administratörsroller i Azure AD - Privileged Identity Management | Microsoft Docs
description: Lär dig mer om att bjuda in gästanvändare och tilldela administratörsroller i Azure Active Directory i Azure AD Privileged Identity Management.
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
ms.date: 03/13/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68e76a4513d94cceb8e856c94ad6eae2bdab9c46
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57853264"
---
# <a name="invite-guest-users-and-assign-azure-resource-access-in-pim"></a>Bjud in gästanvändare och tilldela Azure resursåtkomst i PIM

Azure Active Directory (Azure AD) business-to-business (B2B) är en uppsättning funktioner i Azure AD som gör det möjligt för företag att samarbeta med externa gästanvändare (gäster) och leverantörer med hjälp av ett konto. När du kombinerar B2B med Azure AD Privileged Identity Management (PIM) kan fortsätta du att tillämpa dina krav på efterlevnad och styrning på gäster. Du kan till exempel använda funktionerna PIM för Azure identity uppgifter med gäster:

- Tilldela åtkomst till specifika Azure-resurser
- Aktivera just-in-time-åtkomst
- Ange tilldelning varaktighet och slutdatum
- Kräv MFA vid aktiv uppgift eller aktivering
- Utföra åtkomstgranskningar
- Använda aviseringar och granskningsloggar

Den här artikeln beskriver hur du Bjud in gäst för din organisation och hantera åtkomsten till Azure-resurser med Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>När skulle du bjuda in gäster

Här följer några scenarier när du kan bjuda in gäster till din organisation:

- Tillåt att en extern egen företagare leverantör som bara har ett e-postkonto för att få åtkomst till dina Azure-resurser för ett projekt.
- Tillåt att en extern partner i en stor organisation som använder en lokal Active Directory Federation Services för att komma åt ditt program för utgiftsrapport.
- Tillåt supporttekniker inte i din organisation (till exempel Microsoft-supporten) tillfälligt åtkomst till din Azure-resurs för att felsöka problem.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hur fungerar samarbete med B2B gästerna arbete?

När du använder B2B-samarbete, kan du bjuda in en extern användare till din organisation som gäst. Gästen verkar vara i din organisation, men gästen har inte några autentiseringsuppgifter som är kopplade till den. Varje gång som en gäst måste autentiseras, måste de autentiseras i deras organisation, hem och inte i din organisation. Det innebär att om gästen har inte längre åtkomst till sina hemorganisation kan de också skulle förlora åtkomsten till din organisation. Till exempel om gästen lämnar organisationen, förlora de automatiskt åtkomsten till eventuella resurser som delas med dem i Azure AD utan att du behöver göra något. Läs mer om B2B [vad är gästanvändares åtkomst i Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![B2B- och gästkonton](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Kontrollera inställningar för gäst-samarbete

För att se till att du kan bjuda in gäster till din organisation, bör du kontrollera inställningarna för gäst-samarbete.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Klicka på **Azure Active Directory** > **användarinställningar**.

1. Klicka på **hantera inställningar för externt samarbete**.

    ![Inställningar för externt samarbete](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Se till att den **administratörer och användare i gästinbjudarrollen kan bjuda in** anges för växeln **Ja**.

## <a name="invite-a-guest-and-assign-a-role"></a>Bjud in Gäst och tilldela en roll

Använda PIM kan du Bjud in Gäst och gör dem tillgängliga för en Azure-resurs roll precis som en medlemsanvändare.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i den [privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) eller [Användaradministratör](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) roll.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Använd den **resursfilter** att filtrera listan över hanterade resurser.

1. Klicka på den resurs som du vill hantera, till exempel en resurs, resursgrupp, prenumeration eller hanteringsgrupp.

    Du bör ange omfånget till vilka gästen behöver bara.

1. Klicka på under hantera, **roller** vill se en lista över roller för Azure-resurser.

    ![Azure-resursroller](./media/pim-resource-roles-external-users/resources-roles.png)

1. Klicka på den lägsta roll som användaren behöver.

    ![Vald roll](./media/pim-resource-roles-external-users/selected-role.png)

1. På rollsidan **Lägg till medlem** att öppna tilldelningsfönstret ny.

1. Klicka på **Välj en medlem eller en grupp**.

    ![Välj en medlem eller grupp](./media/pim-resource-roles-external-users/select-member-group.png)

1. Om du vill bjuda in en gäst, klickar du på **bjuda in**.

    ![Bjud in en gäst](./media/pim-resource-roles-external-users/invite-guest.png)

1. När du har valt en gäst, klickar du på **bjuda in**.

    Gästen ska läggas till som en vald medlem.

1. I den **Välj en medlem eller en grupp** fönstret klickar du på **Välj**.

1. I den **Medlemskapsinställningar** fönstret väljer du Tilldelningstyp och varaktighet.

    ![Medlemskapsinställningar](./media/pim-resource-roles-external-users/membership-settings.png)

1. För att slutföra tilldelningen klickar du på **klar** och sedan **Lägg till**.

    Gäst-rolltilldelning visas i listan med rollen.

    ![Rolltilldelning för gäst](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktivera rollen som en gäst

Som en extern användare måste du först acceptera inbjudan till din Azure AD-organisation och eventuellt aktivera din roll.

1. Öppna e-postmeddelandet med din inbjudan. E-postmeddelandet ser ut ungefär så här.

    ![E-postinbjudan](./media/pim-resource-roles-external-users/email-invite.png)

1. Klicka på den **börjar** länken i e-postmeddelandet.

1. När du har granskat behörigheterna, klickar du på **acceptera**.

    ![Granska behörigheter](./media/pim-resource-roles-external-users/invite-accept.png)

1. Du kan bli ombedd att acceptera användningsvillkor och ange om du vill förbli inloggad.

    Azure-portalen öppnas. Om du är bara berättigad för en roll kan du inte åtkomst till resurser.

1. Aktivera din roll genom att öppna e-postmeddelande med din aktivera rollen länk. E-postmeddelandet ser ut ungefär så här.

    ![E-postinbjudan](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Klicka på **aktivera rollen** att öppna dina berättigade roller i PIM.

    ![Mina roller - berättigade](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Under åtgärden, klickar du på den **aktivera** länk.

    Beroende på rollinställningar behöver du ange viss information för att aktivera rollen.

1. När du har angett inställningarna för rollen, klickar du på **aktivera** aktiverar rollen.

    ![Aktivera roll](./media/pim-resource-roles-external-users/activate-role.png)

    Om inte administratören behöver godkänna din begäran ska ha åtkomst till de angivna resurserna.

## <a name="view-activity-for-a-guest"></a>Visa aktivitet för gäst

Du kan visa granskningsloggarna för att hålla reda på vad gäster gör precis som en medlemsanvändare.

1. Som administratör, öppna PIM och välj den resurs som har delats.

1. Klicka på **resursgranskning** att visa aktiviteten för den resursen. Nedan visas ett exempel på aktiviteten för en resursgrupp.

    ![Resursgranskning](./media/pim-resource-roles-external-users/audit-resource.png)

1. Om du vill visa aktiviteten för gästen, klickar du på **Azure Active Directory** > **användare** > gästen namn.

1. Klicka på **granskningsloggar** att se granskningsloggarna för organisationen. Om det behövs kan du ange filter.

    ![organisation granskning](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela administratörsroller i Azure AD i PIM](pim-how-to-add-role-to-user.md)
- [Vad är gästanvändares åtkomst i Azure Active Directory B2B?](../b2b/what-is-b2b.md)
