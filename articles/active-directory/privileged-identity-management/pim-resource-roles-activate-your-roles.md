---
title: Aktivera Azure-resursroller i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du aktiverar dina Azure-resursroller i Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023153"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Aktivera mina Azure-resursroller i Privilegierad identitetshantering

Använd Privilegierad identitetshantering (PIM) för att tillåta berättigade rollmedlemmar för Azure-resurser att schemalägga aktivering för ett framtida datum och en framtida tid. De kan också välja en viss aktiveringstid inom det maximala (konfigurerat av administratörer).

Den här artikeln är för medlemmar som behöver aktivera sin Azure-resursroll i Privilegierad identitetshantering.

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver ta på dig en Azure-resursroll kan du begära aktivering med navigeringsalternativet **Mina roller** i Privilegierad identitetshantering.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD-privilegierad identitetshantering**. Information om hur du lägger till panelen Privilegierad identitetshantering på instrumentpanelen finns i [Börja använda Privilegierad identitetshantering](pim-getting-started.md).

1. Välj **Mina roller**.

    ![Sidan Mina roller som visar roller som du kan aktivera](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Välj **Azure-resursroller** om du vill visa en lista över dina kvalificerade Azure-resursroller.

   ![Mina roller - sidan Azure-resursroller](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Leta reda på den roll du vill aktivera i listan **Azure-resursroller.**

    ![Azure-resursroller – listan Mina kvalificerade roller](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Välj **Aktivera** om du vill öppna fönstret Aktivera.

1. Om din roll kräver multifaktorautentisering väljer du **Verifiera din identitet innan du fortsätter**. Du behöver bara autentisera en gång per session.

    ![Verifiera min identitet med MFA före rollaktivering](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Välj **Verifiera min identitet** och följ instruktionerna för att tillhandahålla ytterligare säkerhetsverifiering.

    ![Skärm för att tillhandahålla säkerhetsverifiering, till exempel en PIN-kod](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Om du vill ange ett minskat scope väljer du **Scope** för att öppna fönstret Resursfilter.

    Det är en bra idé att bara begära åtkomst till de resurser du behöver. I fönstret Resursfilter kan du ange de resursgrupper eller resurser som du behöver åtkomst till.

    ![Aktivera - Fönstret Resursfilter för att ange omfång](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Om det behövs anger du en anpassad starttid för aktivering. Medlemmen aktiveras efter den valda tiden.

1. Ange orsaken till aktiveringsbegäran i rutan **Orsak.**

    ![Fönstret Aktivera slutförd med omfattning, starttid, varaktighet och orsak](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Välj **Aktivera**.

    Om rollen inte kräver godkännande aktiveras den och läggs till i listan över aktiva roller. Om du vill använda rollen följer du stegen i nästa avsnitt.

    Om [rollen kräver godkännande](pim-resource-roles-approval-workflow.md) för att aktiveras visas ett meddelande i det övre högra hörnet i webbläsaren och informerar dig om att begäran väntar på godkännande.

    ![Aktiveringsbegäran väntar på godkännandemeddelande](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Använda en roll direkt efter aktivering

I händelse av fördröjning efter aktivering följer du dessa steg när du har aktiverat för att använda dina Azure-resursroller omedelbart.

1. Öppna Azure AD-privilegierad identitetshantering.

1. Välj **Mina roller** om du vill visa en lista över dina kvalificerade Azure AD-roller och Azure-resursroller.

1. Välj **Azure-resursroller**.

1. Välj fliken **Aktiva roller.**

1. När rollen är aktiv loggar du ut från portalen och loggar in igen.

    Rollen bör nu vara tillgänglig att använda.

## <a name="view-the-status-of-your-requests"></a>Visa status för dina begäranden

Du kan visa status för väntande begäranden som ska aktiveras.

1. Öppna Azure AD-privilegierad identitetshantering.

1. Välj **Mina begäranden om** du vill visa en lista över dina Azure AD-roll- och Azure-resursrollsförfrågningar.

    ![Mina begäranden - Azure-resurssida som visar dina väntande begäranden](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

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

    När du aktiverar en Azure-resursroll ser du stadierna i din aktivering. När alla steg har slutförts visas länken **Logga ut**. Du kan använda den här länken för att logga ut. Detta kommer att lösa de flesta fall för aktiveringsfördröjning.

1. I Privilegierad identitetshantering kontrollerar du att du är listad som medlem i rollen.

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-renew-extend.md)
- [Aktivera mina Azure AD-roller i privilegierad identitetshantering](pim-how-to-activate-role.md)
