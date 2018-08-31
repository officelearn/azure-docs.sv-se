---
title: Aktivera mitt Azure AD-katalogroller i PIM | Microsoft Docs
description: Lär dig hur du aktiverar Azure AD-katalogroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7c990fc0d385af9527bf55339b2fa617e589ec0e
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190594"
---
# <a name="activate-my-azure-ad-directory-roles-in-pim"></a>Aktivera mitt Azure AD-katalogroller i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) förenklar hur företag hantera privilegierad åtkomst till resurser i Azure AD och andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.  

Om du har gjorts kan en administrativ roll, som innebär att du kan aktivera rollen när du behöver utföra Privilegierade åtgärder. Till exempel om du hanterar ibland Office 365-funktioner kan kan din organisations Privilegierade rolladministratörer inte göra du permanent Global administratör, eftersom rollen påverkar andra tjänster för. I stället gör de du berättigad till Azure AD-roller, till exempel Exchange Online-administratör. Du kan begära för att aktivera rollen när du behöver ha dess behörighet, och du har administratören kan styra för en förutbestämd tidsperiod.

Den här artikeln är för administratörer som behöver aktivera sin roll i PIM.

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver utföra på en roll kan du begära aktivering med hjälp av den **Mina roller** navigeringsalternativet i PIM.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**. Information om hur du lägger till PIM-panelen på instrumentpanelen finns i [börja använda PIM](pim-getting-started.md).

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **Mina roller** att se en lista över dina berättigade Azure AD-katalogroller.

    ![Azure AD-katalogroller - Mina roller](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Hitta en roll som du vill aktivera.

    ![Azure AD-katalogroller - listan roller](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Klicka på **aktivera** att öppna rutan rollen aktivering.

1. Om din roll kräver multifaktorautentisering (MFA), klickar du på **verifiera din identitet innan du fortsätter**. Du behöver bara autentiseras en gång per session.

    ![Verifiera med MFA innan rollaktivering](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Klicka på **verifiera min identitet** och följ anvisningarna för att tillhandahålla ytterligare säkerhetsverifiering.

    ![Ytterligare säkerhetskontroll](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Klicka på **aktivera** att öppna fönstret aktivering.

    ![Aktivera fönstret](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Om det behövs kan du ange en starttid för anpassade aktivering.

1. Ange varaktighet för aktivering.

1. I den **orsak för aktivering** anger orsaken till aktiveringsbegäran. Vissa roller kräver att du kan ange ett begäransnummer problem.

    ![Slutförda aktivering fönstret](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Klicka på **aktivera**.

    Om rollen inte kräver godkännande, har nu aktiverats och att rollen visas i listan över aktiva roller. Om den [rollen kräver godkännande](./azure-ad-pim-approval-workflow.md) för att aktivera, visas ett meddelande i det övre högra hörnet i webbläsaren som informerar dig om begäran väntar på godkännande.

    ![Förfrågan väntar på meddelande](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Visa status för dina begäranden

Du kan visa statusen för väntande förfrågningar om att aktivera.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **Mina förfrågningar** att se en lista över dina önskemål.

    ![Azure AD-katalogroller - Mina begäranden](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="use-a-role-immediately-after-activation"></a>Använd en roll omedelbart efter aktiveringen

På grund av cachelagring, görs inte aktiveringar direkt i Azure portal utan att en uppdatering. Om du vill minska risken för fördröjningar när du har aktiverat en roll kan du använda den **programåtkomst** i portalen. Program som nås från den här sidan söka efter nya rolltilldelningar omedelbart.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på den **programåtkomst** sidan.

    ![PIM-programåtkomst](./media/pim-how-to-activate-role/pim-application-access.png)

1. Klicka på **Azure Active Directory** att öppna portalen på den **alla användare** sidan.

    När du klickar på den här länken, du tvingar fram en uppdatering och det är en kontroll för nya Azure AD-rolltilldelningar.

## <a name="deactivate-a-role"></a>Inaktivera en roll

När en roll har aktiverats, inaktiverar automatiskt när tidsgränsen (varaktighet för berättigande) har nåtts.

Om du har slutfört din administratörsåtgärder tidigt, kan du inaktivera en roll manuellt i Azure AD Privileged Identity Management.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **Mina roller**.

1. Klicka på **aktiva roller** att se din lista över aktiva roller.

1. Hitta den roll som du är klar med och klicka sedan på **inaktivera**.

## <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran

Om du inte behöver aktivering av en roll som kräver godkännande kan avbryta du en väntande begäran när som helst.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **Mina förfrågningar**.

1. För den roll som du vill avbryta, klickar du på den **Avbryt** knappen.

    När du klickar på Avbryt avbryts begäran. Om du vill aktivera rollen igen och måste du skicka en ny begäran om aktivering.

   ![Avbryta väntande begäran](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="next-steps"></a>Nästa steg

- [Aktivera Mina roller för Azure-resurs i PIM](pim-resource-roles-activate-your-roles.md)
