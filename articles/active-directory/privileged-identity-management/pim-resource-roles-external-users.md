---
title: Tilldela Azure-resursroller till gäster i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du bjuder in externa gästanvändare och tilldelar Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021941"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Bjuda in gästanvändare och tilldela Azure-resursroller i Privilegierad identitetshantering

Azure Active Directory (Azure AD) gästanvändare är en del av samarbetsfunktionerna för business-to-business (B2B) i Azure AD så att du kan hantera externa gästanvändare och leverantörer som gäster i Azure AD. När du kombinerar B2B-samarbete med Azure AD Privileged Identity Management (PIM) kan du utöka dina efterlevnads- och styrningskrav till gäster. Du kan till exempel använda dessa privilegierade identitetshanteringsfunktioner för Azure-identitetsuppgifter med gäster:

- Tilldela åtkomst till specifika Azure-resurser
- Aktivera åtkomst just-in-time
- Ange tilldelningens varaktighet och slutdatum
- Kräv multifaktorautentisering vid aktiv tilldelning eller aktivering
- Utföra åtkomstgranskningar
- Använda aviseringar och granskningsloggar

I den här artikeln beskrivs hur du bjuder in en gäst till din organisation och hanterar deras åtkomst till Azure-resurser med privilegierad identitetshantering.

## <a name="when-would-you-invite-guests"></a>När skulle du bjuda in gäster?

Här är några exempel på när du kan bjuda in gäster till din organisation:

- Tillåt en extern leverantör som är egenföretagare och som bara har ett e-postkonto för att komma åt dina Azure-resurser för ett projekt.
- Tillåt en extern partner i en stor organisation som använder lokala Active Directory Federation Services för att komma åt ditt utgiftsprogram.
- Tillåt supporttekniker som inte finns i din organisation (till exempel Microsoft-support) att tillfälligt komma åt din Azure-resurs för att felsöka problem.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hur fungerar samarbete med B2B-gäster?

När du använder B2B-samarbete kan du bjuda in en extern användare till din organisation som gäst. Gästen kan hanteras som användare i din organisation, men en gäst måste autentiseras i sin hemorganisation och inte i din Azure AD-organisation. Detta innebär att om gästen inte längre har tillgång till sin hemorganisation, förlorar de också åtkomst till din organisation. Om gästen till exempel lämnar sin organisation förlorar de automatiskt åtkomst till alla resurser som du har delat med dem i Azure AD utan att du behöver göra något. Mer information om B2B-samarbete finns [i Vad är gästanvändaråtkomst i Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagram som visar hur en gästanvändare autentiseras i sin hemkatalog](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Kontrollera inställningar för gästsamarbete

Om du vill vara säker på att du kan bjuda in gäster till din organisation bör du kontrollera inställningarna för gästsamarbete.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj**Användarinställningar för** **Azure Active Directory** > .

1. Välj **Hantera inställningar för externt samarbete**.

    ![Sida för externa samarbetsinställningar som visar inställningar för behörighet, inbjudan och samarbetsbegränsning](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Se till att **administratörerna och användarna i gästombjudtarrollen kan bjuda in** växeln är inställd på **Ja**.

## <a name="invite-a-guest-and-assign-a-role"></a>Bjuda in en gäst och tilldela en roll

Med privilegierad identitetshantering kan du bjuda in en gäst och göra dem kvalificerade för en Azure-resursroll.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i rollen [Privilegierad rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) eller [Användaradministratör.](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**.

1. Använd **resursfiltret** för att filtrera listan över hanterade resurser.

1. Välj den resurs som du vill hantera, till exempel en resurs, resursgrupp, prenumeration eller hanteringsgrupp.

    Du bör ställa in omfattningen till endast vad gästen behöver.

1. Under Hantera väljer du **Roller** för att visa listan över roller för Azure-resurser.

    ![Lista över Azure-resurserroller som visar antalet aktiva och kvalificerade användare](./media/pim-resource-roles-external-users/resources-roles.png)

1. Välj den minsta roll som användaren behöver.

    ![Vald rollsida med de aktuella medlemmarna i den rollen](./media/pim-resource-roles-external-users/selected-role.png)

1. På rollsidan väljer du **Lägg till medlem** för att öppna fönstret Ny tilldelning.

1. Klicka på **Välj en medlem eller grupp**.

    ![Ny tilldelning – Välj en medlems- eller gruppfönster med användare och grupper tillsammans med alternativet Bjud in](./media/pim-resource-roles-external-users/select-member-group.png)

1. Om du vill bjuda in en gäst klickar du på **Bjud in**.

    ![Bjud in en gästsida med rutor för att ange en e-postadress och ange ett personligt meddelande](./media/pim-resource-roles-external-users/invite-guest.png)

1. När du har valt en gäst klickar du på **Bjud in**.

    Gästen ska läggas till som en vald medlem.

1. Klicka på **Markera**i fönstret **Välj medlem eller grupp** .

1. Välj tilldelningstyp och varaktighet i fönstret **Inställningar för medlemskap.**

    ![Ny tilldelning - Sidan Inställningar för medlemskap med alternativ för att ange tilldelningstyp, startdatum och slutdatum](./media/pim-resource-roles-external-users/membership-settings.png)

1. Om du vill slutföra tilldelningen väljer du **Klar** och sedan **Lägg till**.

    Gästrolltilldelningen visas i din rolllista.

    ![Rollsida som visar gästen som kvalificerad](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktivera rollen som gäst

Om du är en extern användare måste du acceptera inbjudan att vara gäst i Azure AD-organisationen och eventuellt aktivera din rolltilldelning.

1. Öppna e-postmeddelandet med din inbjudan. E-postmeddelandet kommer att se ut ungefär så här.

    ![E-postt inbjudan med katalognamn, personligt meddelande och en Kom igång-länk](./media/pim-resource-roles-external-users/email-invite.png)

1. Välj länken **Kom igång** i e-postmeddelandet.

1. När du har granskat behörigheterna klickar du på **Acceptera**.

    ![Sidan Granska behörigheter i en webbläsare med en lista över behörigheter som organisationen vill att du ska granska](./media/pim-resource-roles-external-users/invite-accept.png)

1. Du kan bli ombedd att acceptera ett användningsvillkor och ange om du vill vara inloggad. Om du är berättigad *till* en roll i Azure-portalen har du ännu inte åtkomst till resurser.

1. Om du vill aktivera din rolltilldelning öppnar du e-postmeddelandet med din aktivera rolllänk. E-postmeddelandet kommer att se ut ungefär så här.

    ![E-post som anger att du är berättigad till en roll med en aktivera rolllänk](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Välj **Aktivera roll** om du vill öppna dina kvalificerade roller i Privilegierad identitetshantering.

    ![Sidan Mina roller i Privilegierad identitetshantering visar dina kvalificerade roller](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Välj länken **Aktivera** under Åtgärd.

    Beroende på rollinställningarna måste du ange viss information för att aktivera rollen.

1. När du har angett inställningarna för rollen klickar du på **Aktivera** för att aktivera rollen.

    ![Aktivera sidbeskrivningsscope och alternativ för att ange starttid, varaktighet och orsak](./media/pim-resource-roles-external-users/activate-role.png)

    Om inte administratören måste godkänna din begäran bör du ha åtkomst till de angivna resurserna.

## <a name="view-activity-for-a-guest"></a>Visa aktivitet för en gäst

Du kan visa granskningsloggar för att hålla reda på vad gästerna gör.

1. Som administratör öppnar du Privilegierad identitetshantering och väljer den resurs som har delats.

1. Välj **Resursgranskning** om du vill visa aktiviteten för resursen. Följande visar ett exempel på aktiviteten för en resursgrupp.

    ![Azure-resurser – Resursgranskningssida med tid, beställare och åtgärd](./media/pim-resource-roles-external-users/audit-resource.png)

1. Om du vill visa aktiviteten för gästen väljer du*Gästnamn*för **Azure Active Directory** > **Users** > .

1. Välj **Granskningsloggar** om du vill visa granskningsloggarna för organisationen. Om det behövs kan du ange filter.

    ![Kataloggranskningsloggar listar datum, mål, initierat av och aktivitet](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-administratörsroller i privilegierad identitetshantering](pim-how-to-add-role-to-user.md)
- [Vad är gästanvändaråtkomst i Azure AD B2B-samarbete?](../b2b/what-is-b2b.md)
