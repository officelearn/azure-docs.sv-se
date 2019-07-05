---
title: Aktivera Mina roller för Azure-resurs i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig mer om att aktivera din Azure-resursroller i Azure AD Privileged Identity Management (PIM).
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
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501658"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Aktivera Mina roller för Azure-resurs i PIM

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan berättigade rollmedlemmar för Azure-resurser schemalägga aktivering för ett framtida datum och tid. De kan också välja en specifik aktiveringsvaraktighet inom den Maxtid (konfigureras av administratörer).

Den här artikeln gäller för medlemmar som behöver aktivera sin Azure-resurs-roll i PIM.

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver utföra på en Azure-resurs-roll kan du begära aktivering med hjälp av den **Mina roller** navigeringsalternativet i PIM.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**. Information om hur du lägger till PIM-panelen på instrumentpanelen finns i [börja använda PIM](pim-getting-started.md).

1. Klicka på **Mina roller**.

    ![Mina rollsidan som visar roller som du kan aktivera](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Klicka på **Azure-resursroller** att se en lista över dina berättigade Azure-resursroller.

   ![Mina roller - rollsidan för Azure-resurs](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. I den **Azure-resursroller** listan, hitta den roll som du vill aktivera.

    ![Azure-resursroller - listan berättigade roller](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Klicka på **aktivera** att öppna fönstret Aktivera.

1. Om din roll kräver multifaktorautentisering (MFA), klickar du på **verifiera din identitet innan du fortsätter**. Du behöver bara autentiseras en gång per session.

    ![Verifiera min identitet med MFA innan rollaktivering](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Klicka på **verifiera min identitet** och följ anvisningarna för att tillhandahålla ytterligare säkerhetsverifiering.

    ![Skärmen för att ge en säkrare verifiering, till exempel en PIN-kod](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Om du vill ange ett lägre omfång, klickar du på **omfång** att öppna filterfönstret för resursen.

    Det är en bra idé att bara begära åtkomst till de resurser du behöver. Du kan ange resursgrupper eller resurser som du behöver åtkomst till i filterrutan resurs.

    ![Aktivera - resurs filterfönstret för att ange omfång](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Om det behövs kan du ange en starttid för anpassade aktivering. Medlemmen skulle aktiveras efter den valda tiden.

1. I den **orsak** anger orsaken till aktiveringsbegäran.

    ![Slutförda Aktivera fönstret med omfång, starttid, varaktighet och orsak](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Klicka på **Aktivera**.

    Om rollen inte kräver godkännande, aktiveras den och har lagts till i listan över aktiva roller. Om du vill använda rollen, följer du stegen i nästa avsnitt.

    Om den [rollen kräver godkännande](pim-resource-roles-approval-workflow.md) för att aktivera, visas ett meddelande i det övre högra hörnet i webbläsaren som informerar dig om begäran väntar på godkännande.

    ![Begäran om aktivering är meddelande om väntar på godkännande](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Använd en roll omedelbart efter aktiveringen

Följ dessa steg när du har aktiverat för att använda dina Azure-resursroller omedelbart vid eventuell fördröjning efter aktiveringen.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Mina roller** att se en lista över dina berättigade Azure AD-roller och Azure-resursroller.

1. Klicka på **Azure-resursroller**.

1. Klicka på den **aktiva roller** fliken.

1. När rollen är aktiv, logga ut från portalen och logga in igen.

    Nu bör rollen kan användas.

## <a name="view-the-status-of-your-requests"></a>Visa status för dina begäranden

Du kan visa statusen för väntande förfrågningar om att aktivera.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Mina förfrågningar** att se en lista över dina Azure AD-rollen och Azure-resursrollen begär.

    ![Mina begäranden - Azure-resurs-sidan som visar väntande förfrågningar](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Bläddra till höger för att visa den **begär Status** kolumn.

## <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran

Om du inte behöver aktivering av en roll som kräver godkännande kan avbryta du en väntande begäran när som helst.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Mina förfrågningar**.

1. För den roll som du vill avbryta, klickar du på den **Avbryt** länk.

    När du klickar på Avbryt avbryts begäran. Om du vill aktivera rollen igen och måste du skicka en ny begäran om aktivering.

   ![Min listan över begäranden med Avboka markerat](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Felsöka

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Behörigheter beviljas inte efter aktivering av en roll

När du aktiverar en roll i PIM kan aktiveringen inte direkt spridas till alla portaler som kräver privilegierad roll. Ibland även om ändringen sprids kan web cachelagring i en portal resultera i att ändringen börjar inte gälla omedelbart. Om aktiveringen dröjer är här vad du bör göra.

1. Logga ut från Azure portal och sedan logga in igen.

    När du aktiverar rollen för en Azure-resurs, visas olika aktiveringen. När alla steg har slutförts, visas en **logga ut** länk. Du kan använda den här länken för att logga ut. Detta kommer att lösa de flesta fall för aktiveringsfördröjning.

1. I PIM, kontrollerar du att du står som medlem i rollen.

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azure-resursroller i PIM](pim-resource-roles-renew-extend.md)
- [Aktivera Azure AD-roller i PIM](pim-how-to-activate-role.md)
