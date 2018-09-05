---
title: Aktivera Mina roller för Azure-resurs i PIM | Microsoft Docs
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
ms.component: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59bce2c61db5838bb21a29757d4e354311ecffd5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666255"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Aktivera Mina roller för Azure-resurs i PIM

Använda Azure AD Privileged Identity Management (PIM), kan berättigade rollmedlemmar för Azure-resurser schemalägga aktivering för ett framtida datum och tid. De kan också välja en specifik aktiveringsvaraktighet inom den Maxtid (konfigureras av administratörer).

Den här artikeln gäller för medlemmar som behöver aktivera sin Azure-resurs-roll i PIM.

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver utföra på en Azure-resurs-roll kan du begära aktivering med hjälp av den **Mina roller** navigeringsalternativet i PIM.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**. Information om hur du lägger till PIM-panelen på instrumentpanelen finns i [börja använda PIM](pim-getting-started.md).

1. Klicka på **Mina roller** att se en lista över dina berättigade Azure AD-katalogroller och Azure-resursroller.

    ![Azure AD-katalogroller och Azure-resursroller - Mina roller](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. I den **Azure-resursroller** listan, hitta den roll som du vill aktivera.

    ![Azure-resursroller - listan roller](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Klicka på **aktivera** att öppna fönstret Aktivera.

1. Om din roll kräver multifaktorautentisering (MFA), klickar du på **verifiera din identitet innan du fortsätter**. Du behöver bara autentiseras en gång per session.

    ![Verifiera med MFA innan rollaktivering](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Klicka på **verifiera min identitet** och följ anvisningarna för att tillhandahålla ytterligare säkerhetsverifiering.

    ![Ytterligare säkerhetskontroll](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Om du vill ange ett lägre omfång, klickar du på **omfång** att öppna filterfönstret för resursen.

    Det är en bra idé att bara begära åtkomst till de resurser du behöver. Du kan ange resursgrupper eller resurser som du behöver åtkomst till i filterrutan resurs.

    ![Aktivera - resursfilter](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Om det behövs kan du ange en starttid för anpassade aktivering. Medlemmen skulle aktiveras efter den valda tiden.

1. I den **orsak** anger orsaken till aktiveringsbegäran.

    ![Slutförda Aktivera fönstret](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Klicka på **aktivera**.

    Om rollen inte kräver godkännande, har nu aktiverats och att rollen visas i listan över aktiva roller. Om den [rollen kräver godkännande](pim-resource-roles-approval-workflow.md) för att aktivera, visas ett meddelande i det övre högra hörnet i webbläsaren som informerar dig om begäran väntar på godkännande.

    ![Förfrågan väntar på meddelande](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Visa status för dina begäranden

Du kan visa statusen för väntande förfrågningar om att aktivera.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Mina förfrågningar** att se en lista över dina Azure AD directory-roll och rollen för Azure-resurs-begäranden.

    ![Azure AD-katalogroller och Azure-resursroller - Mina begäranden](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Bläddra till höger för att visa den **begär Status** kolumn.

## <a name="use-a-role-immediately-after-activation"></a>Använd en roll omedelbart efter aktiveringen

På grund av cachelagring, görs inte aktiveringar direkt i Azure portal utan att en uppdatering. Om du vill minska risken för fördröjningar när du har aktiverat en roll kan du använda den **programåtkomst** i portalen. Program som nås från den här sidan söka efter nya rolltilldelningar omedelbart.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på den **programåtkomst** sidan.

    ![Programåtkomst med PIM - skärmbild](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Klicka på **Azure-resurser** att öppna portalen på den **alla resurser** sidan.

    När du klickar på den här länken du tvingar fram en uppdatering och det finns en kontroll för nya Azure-resurs-rolltilldelningar.

## <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran

Om du inte behöver aktivering av en roll som kräver godkännande kan avbryta du en väntande begäran när som helst.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Mina förfrågningar**.

1. För den roll som du vill avbryta, klickar du på den **Avbryt** länk.

    När du klickar på Avbryt avbryts begäran. Om du vill aktivera rollen igen och måste du skicka en ny begäran om aktivering.

   ![Avbryta väntande begäran](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azure-resursroller i PIM](pim-resource-roles-renew-extend.md)
- [Aktivera mitt Azure AD-katalogroller i PIM](pim-how-to-activate-role.md)