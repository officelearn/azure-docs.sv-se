---
title: Aktivera mina Azure AD-roller i PIM - Azure Active Directory | Microsoft-dokument
description: Lär dig hur du aktiverar Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499216"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivera mina Azure AD-roller i PIM

Azure Active Directory (Azure AD) Privilegierad identitetshantering (PIM) förenklar hur företag hanterar privilegierad åtkomst till resurser i Azure AD och andra Microsoft-onlinetjänster som Office 365 eller Microsoft Intune.  

Om du har blivit kvalificerad för en administrativ roll kan du aktivera den rollen när du behöver utföra privilegierade åtgärder. Om du till exempel ibland hanterar Office 365-funktioner kanske organisationens privilegierade rolladministratörer inte gör dig till en permanent global administratör, eftersom den rollen påverkar även andra tjänster. I stället gör de dig kvalificerad för Azure AD-roller som Exchange Online-administratör. Du kan begära att aktivera rollen när du behöver dess behörigheter, och sedan har du administratörskontroll för en förutbestämd tidsperiod.

Den här artikeln är för administratörer som behöver aktivera sin Azure AD-roll i Privilegierad identitetshantering.

## <a name="determine-your-version-of-pim"></a>Bestäm din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azure-resursroller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API:et](azure-ad-roles-features.md#api-changes). Medan den nya versionen distribueras, vilka procedurer som du följer i den här artikeln beror på vilken version av privilegierad identitetshantering du har för närvarande. Följ stegen i det här avsnittet för att avgöra vilken version av privilegierad identitetshantering du har. När du känner till din version av Privilegierad identitetshantering kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med [rollen Privilegierad rolladministratör.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Öppna **Azure AD-privilegierad identitetshantering**. Om du har en banderoll högst upp på översiktssidan följer du instruktionerna på fliken **Ny version** i den här artikeln. Annars följer du instruktionerna på fliken **Föregående version.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Ny version](#tab/new)

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver ta på dig en Azure AD-roll kan du begära aktivering med navigeringsalternativet **Mina roller** i Privilegierad identitetshantering.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD-privilegierad identitetshantering**. Information om hur du lägger till panelen Privilegierad identitetshantering på instrumentpanelen finns i [Börja använda Privilegierad identitetshantering](pim-getting-started.md).

1. Välj **Mina roller**och välj sedan Azure **AD-roller** för att se en lista över dina kvalificerade Azure AD-roller.

    ![Sidan Mina roller som visar roller som du kan aktivera](./media/pim-how-to-activate-role/my-roles.png)

1. Leta reda på den roll du vill aktivera i listan **Azure AD-roller.**

    ![Azure AD-roller – listan Mina kvalificerade roller](./media/pim-how-to-activate-role/activate-link.png)

1. Välj **Aktivera** om du vill öppna fönstret Aktivera.

    ![Azure AD-roller – aktiveringssidan innehåller varaktighet och omfattning](./media/pim-how-to-activate-role/activate-page.png)

1. Om din roll kräver multifaktorautentisering väljer du **Verifiera din identitet innan du fortsätter**. Du behöver bara autentisera en gång per session.

    ![Verifiera min identitet med MFA före rollaktivering](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Välj **Verifiera min identitet** och följ instruktionerna för att tillhandahålla ytterligare säkerhetsverifiering.

    ![Skärm för att tillhandahålla säkerhetsverifiering, till exempel en PIN-kod](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Om du vill ange ett minskat scope väljer du **Scope** för att öppna filterfönstret. I filterfönstret kan du ange de Azure AD-resurser som du behöver åtkomst till. Det är en bra idé att begära åtkomst till endast de resurser du behöver.

1. Om det behövs anger du en anpassad starttid för aktivering. Azure AD-rollen aktiveras efter den valda tiden.

1. Ange orsaken till aktiveringsbegäran i rutan **Orsak.**

1. Välj **Aktivera**.

    Om rollen inte kräver godkännande aktiveras den och läggs till i listan över aktiva roller. Om du vill använda rollen följer du stegen i nästa avsnitt.

    ![Fönstret Aktivera slutförd med omfattning, starttid, varaktighet och orsak](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    Om [rollen kräver godkännande](pim-resource-roles-approval-workflow.md) för att aktiveras visas ett meddelande i det övre högra hörnet i webbläsaren och informerar dig om att begäran väntar på godkännande.

    ![Aktiveringsbegäran väntar på godkännandemeddelande](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Använda en roll direkt efter aktivering

I händelse av fördröjning efter aktivering följer du dessa steg när du har aktiverat för att använda dina Azure AD-roller omedelbart.

1. Öppna Azure AD-privilegierad identitetshantering.

1. Välj **Mina roller** om du vill visa en lista över dina kvalificerade Azure AD-roller och Azure-resursroller.

1. Välj **Azure AD-roller**.

1. Välj fliken **Aktiva roller.**

1. När rollen är aktiv loggar du ut från portalen och loggar in igen.

    Rollen bör nu vara tillgänglig att använda.

## <a name="view-the-status-of-your-requests"></a>Visa status för dina begäranden

Du kan visa status för väntande begäranden som ska aktiveras.

1. Öppna Azure AD-privilegierad identitetshantering.

1. Välj **Mina begäranden om** du vill visa en lista över dina Azure AD-roll- och Azure-resursrollsförfrågningar.

    ![Mina begäranden - Azure AD-sida som visar dina väntande begäranden](./media/pim-how-to-activate-role/my-requests-page.png)

1. Bläddra åt höger för att visa kolumnen **Begärandestatus.**

## <a name="cancel-a-pending-request"></a>Avbryta en väntande begäran

Om du inte behöver aktivera en roll som kräver godkännande kan du när som helst avbryta en väntande begäran.

1. Öppna Azure AD-privilegierad identitetshantering.

1. Välj **Mina begäranden**.

1. Välj länken **Avbryt** för den roll som du vill avbryta.

    När du väljer Avbryt avbryts begäran. Om du vill aktivera rollen igen måste du skicka in en ny begäran om aktivering.

   ![Min lista över begäranden med Åtgärden Avbryt markerad](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Felsöka

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Behörigheter beviljas inte efter att en roll har aktiverats

När du aktiverar en roll i Privilegierad identitetshantering kanske aktiveringen inte omedelbart sprids till alla portaler som kräver den privilegierade rollen. Ibland kan webbcachelagringen i en portal resultera i att ändringen inte börjar gälla omedelbart, även om ändringen har spridits. Om din aktivering är försenad, här är vad du bör göra.

1. Logga ut från Azure-portalen och logga sedan in igen.

    När du aktiverar en Azure AD-roll ser du stadierna i din aktivering. När alla steg har slutförts visas länken **Logga ut**. Du kan använda den här länken för att logga ut. Detta kommer att lösa de flesta fall för aktiveringsfördröjning.

1. I Privilegierad identitetshantering kontrollerar du att du är listad som medlem i rollen.

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver ta på dig en Azure AD-roll kan du begära aktivering med navigeringsalternativet **Mina roller** i Privilegierad identitetshantering.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD-privilegierad identitetshantering**. Information om hur du lägger till panelen Privilegierad identitetshantering på instrumentpanelen finns i [Börja använda Privilegierad identitetshantering](pim-getting-started.md).

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina roller** om du vill visa en lista över dina kvalificerade Azure AD-roller.

    ![Azure AD-roller - Mina roller som visar listan över kvalificerade eller aktiva roller](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Hitta en roll som du vill aktivera.

    ![Azure AD-roller - Min lista över kvalificerade roller som visar länken Aktivera](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Klicka på **Aktivera** om du vill öppna informationsfönstret för rollaktivering.

1. Om din roll kräver MFA (Multifaktorautentisering) klickar du på **Verifiera din identitet innan du fortsätter**. Du behöver bara autentisera en gång per session.

    ![Verifiera min identitetsfönster med MFA före rollaktivering](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Klicka på **Verifiera min identitet** och följ instruktionerna för att tillhandahålla ytterligare säkerhetsverifiering.

    ![Ytterligare säkerhetsverifieringssida som frågar hur du kontaktar dig](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Öppna aktiveringsfönstret genom att klicka på **Aktivera.**

    ![Aktiveringsfönster för att ange starttid, varaktighet, biljett och orsak](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Om det behövs anger du en anpassad starttid för aktivering.

1. Ange aktiveringstiden.

1. Ange orsaken till aktiveringsbegäran i rutan **Aktiveringsorsak.** Vissa roller kräver att du anger ett problembiljettnummer.

    ![Fönstret Slutförd aktivering med anpassad starttid, varaktighet, biljett och orsak](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Klicka på **Aktivera**.

    Om rollen inte kräver godkännande visas ett **aktiveringsstatusfönster** som visar aktiveringens status.

    ![Sidan Aktiveringsstatus som visar de tre aktiveringsfaserna](./media/pim-how-to-activate-role/activation-status.png)

    När alla steg är klara klickar du på länken **Logga ut** för att logga ut från Azure-portalen. När du loggar in igen på portalen kan du nu använda rollen.

    Om [rollen kräver godkännande](./azure-ad-pim-approval-workflow.md) för att aktivera visas ett Azure-meddelande i det övre högra hörnet i din webbläsare och informerar dig om att begäran väntar på godkännande.

## <a name="view-the-status-of-your-requests"></a>Visa status för dina begäranden

Du kan visa status för väntande begäranden som ska aktiveras.

1. Öppna Azure AD-privilegierad identitetshantering.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina begäranden** om du vill visa en lista över dina begäranden.

    ![Azure AD-roller - Listan Mina begäranden](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Inaktivera en roll

När en roll har aktiverats inaktiveras den automatiskt när dess tidsgräns (stödberättigande varaktighet) har uppnåtts.

Om du slutför dina administratörsuppgifter tidigt kan du också inaktivera en roll manuellt i Azure AD Privileged Identity Management.

1. Öppna Azure AD-privilegierad identitetshantering.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina roller**.

1. Klicka på **Aktiva roller** om du vill visa listan över aktiva roller.

1. Hitta den roll du har gjort med och klicka sedan på **Inaktivera**.

## <a name="cancel-a-pending-request"></a>Avbryta en väntande begäran

Om du inte behöver aktivera en roll som kräver godkännande kan du när som helst avbryta en väntande begäran.

1. Öppna Azure AD-privilegierad identitetshantering.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina begäranden**.

1. Klicka på knappen **Avbryt** för den roll som du vill avbryta.

    När du klickar på Avbryt avbryts begäran. Om du vill aktivera rollen igen måste du skicka in en ny begäran om aktivering.

   ![Listan Mina begäranden med knappen Avbryt markerad](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Felsöka

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Behörigheter beviljas inte efter att en roll har aktiverats

När du aktiverar en roll i Privilegierad identitetshantering kanske aktiveringen inte omedelbart sprids till alla portaler som kräver den privilegierade rollen. Ibland kan webbcachelagringen i en portal resultera i att ändringen inte börjar gälla omedelbart, även om ändringen har spridits. Om din aktivering är försenad, här är vad du bör göra.

1. Logga ut från Azure-portalen och logga sedan in igen.

    När du aktiverar en Azure AD-roll ser du stadierna i din aktivering. När alla steg har slutförts visas länken **Logga ut**. Du kan använda den här länken för att logga ut. Detta kommer att lösa de flesta fall för aktiveringsfördröjning.

1. I Privilegierad identitetshantering kontrollerar du att du är listad som medlem i rollen.

 ---

## <a name="next-steps"></a>Nästa steg

- [Aktivera mina Azure AD-roller i privilegierad identitetshantering](pim-how-to-activate-role.md)
