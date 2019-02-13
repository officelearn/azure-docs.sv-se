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
ms.subservice: pim
ms.date: 11/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0023ac374ef70593d0ab2d9589c99d0f37e19ff8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189607"
---
# <a name="activate-my-azure-ad-directory-roles-in-pim"></a>Aktivera mitt Azure AD-katalogroller i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) förenklar hur företag hantera privilegierad åtkomst till resurser i Azure AD och andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.  

Om du har gjorts kan en administrativ roll, som innebär att du kan aktivera rollen när du behöver utföra Privilegierade åtgärder. Till exempel om du hanterar ibland Office 365-funktioner kan kan din organisations Privilegierade rolladministratörer inte göra du permanent Global administratör, eftersom rollen påverkar andra tjänster för. I stället gör de du berättigad till Azure AD-roller, till exempel Exchange Online-administratör. Du kan begära för att aktivera rollen när du behöver ha dess behörighet, och du har administratören kan styra för en förutbestämd tidsperiod.

Den här artikeln är för administratörer som behöver aktivera sina Azure AD directory-rollen i PIM.

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver utföra på en Azure AD directory-roll kan du begära aktivering med hjälp av den **Mina roller** navigeringsalternativet i PIM.

1. Logga in på [Azure Portal](https://portal.azure.com/).

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

1. Klicka på **Aktivera**.

    Om rollen inte kräver godkännande, aktiveras den och har lagts till i listan över aktiva roller. Om du vill använda rollen direkt, följer du stegen i nästa avsnitt.

    Om den [rollen kräver godkännande](./azure-ad-pim-approval-workflow.md) för att aktivera, visas ett meddelande i det övre högra hörnet i webbläsaren som informerar dig om begäran väntar på godkännande.

    ![Förfrågan väntar på meddelande](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Använd en roll omedelbart efter aktiveringen

När du aktiverar en roll i PIM tar minst 10 minuter innan du kan få åtkomst till önskade administrativa portalen eller utföra funktioner inom en viss administrativa arbetsbelastning. Om du vill framtvinga en uppdatering av dina behörigheter kan du använda den **programåtkomst** sidan enligt beskrivningen i följande steg.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på den **programåtkomst** sidan.

    ![PIM-programåtkomst](./media/pim-how-to-activate-role/pim-application-access.png)

1. Klicka på den **Azure Active Directory** länk för att återaktivera portalen på den **alla användare** sidan.

    När du klickar på den här länken ogiltigförklara den aktuella token och tvinga Azure portal för att hämta en ny token som ska innehålla dina uppdaterade behörigheter.

## <a name="view-the-status-of-your-requests"></a>Visa status för dina begäranden

Du kan visa statusen för väntande förfrågningar om att aktivera.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **Mina förfrågningar** att se en lista över dina önskemål.

    ![Azure AD-katalogroller - Mina begäranden](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

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

## <a name="troubleshoot"></a>Felsöka

### <a name="permissions-not-granted-after-activating-a-role"></a>Behörigheter som beviljas inte efter aktivering av en roll

När du aktiverar en roll i PIM tar minst 10 minuter innan du kan få åtkomst till önskade administrativa portalen eller utföra funktioner inom en viss administrativa arbetsbelastning. Om du vill framtvinga en uppdatering av dina behörigheter kan du använda den **programåtkomst** sidan enligt beskrivningen tidigare i [Använd en roll omedelbart efter aktiveringen](#use-a-role-immediately-after-activation).

För ytterligare felsökning, se [felsökning förhöjd behörighet](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

## <a name="next-steps"></a>Nästa steg

- [Aktivera Mina roller för Azure-resurs i PIM](pim-resource-roles-activate-your-roles.md)
