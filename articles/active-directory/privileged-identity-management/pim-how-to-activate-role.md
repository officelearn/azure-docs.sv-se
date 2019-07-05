---
title: Aktivera Azure AD-roller i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur du aktiverar Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b3e02222580da67a4eeb4159e4fc5dc3b9a98f
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501672"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivera Azure AD-roller i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) förenklar hur företag hantera privilegierad åtkomst till resurser i Azure AD och andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.  

Om du har gjorts kan en administrativ roll, som innebär att du kan aktivera rollen när du behöver utföra Privilegierade åtgärder. Till exempel om du hanterar ibland Office 365-funktioner kan kan din organisations Privilegierade rolladministratörer inte göra du permanent Global administratör, eftersom rollen påverkar andra tjänster för. I stället gör de du berättigad till Azure AD-roller, till exempel Exchange Online-administratör. Du kan begära för att aktivera rollen när du behöver ha dess behörighet, och du har administratören kan styra för en förutbestämd tidsperiod.

Den här artikeln är för administratörer som behöver aktivera sina Azure AD-rollen i PIM.

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver utföra på en Azure AD-roll kan du begära aktivering med hjälp av den **Mina roller** navigeringsalternativet i PIM.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**. Information om hur du lägger till PIM-panelen på instrumentpanelen finns i [börja använda PIM](pim-getting-started.md).

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina roller** att se en lista över dina berättigade Azure AD-roller.

    ![Azure AD-roller – Mina roller med berättigade eller aktiva roller lista](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Hitta en roll som du vill aktivera.

    ![Azure AD-roller - listan berättigade roller som visar Aktivera länk](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Klicka på **aktivera** att öppna rutan rollen aktivering.

1. Om din roll kräver multifaktorautentisering (MFA), klickar du på **verifiera din identitet innan du fortsätter**. Du behöver bara autentiseras en gång per session.

    ![Verifiera min identitet fönstret med MFA innan rollaktivering](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Klicka på **verifiera min identitet** och följ anvisningarna för att tillhandahålla ytterligare säkerhetsverifiering.

    ![Ytterligare verifiering sida där du tillfrågas så här kontaktar du](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Klicka på **aktivera** att öppna fönstret aktivering.

    ![Aktivering i fönstret för att ange starttid, varaktighet, biljett och orsak](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Om det behövs kan du ange en starttid för anpassade aktivering.

1. Ange varaktighet för aktivering.

1. I den **orsak för aktivering** anger orsaken till aktiveringsbegäran. Vissa roller kräver att du kan ange ett begäransnummer problem.

    ![Slutförda aktivering fönstret med en anpassad starttid, varaktighet, biljett samt orsak](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Klicka på **Aktivera**.

    Om rollen inte kräver godkännande, en **aktiveringsstatus** visas i fönstret som visar status för aktivering.

    ![Status för sida som visar tre steg med aktivering](./media/pim-how-to-activate-role/activation-status.png)

    När alla steg har slutförts klickar du på den **logga ut** länken för att logga ut från Azure-portalen. Du kan nu använda rollen när du loggar in igen på portalen.

    Om den [rollen kräver godkännande](./azure-ad-pim-approval-workflow.md) för att aktivera, visas ett meddelande i det övre högra hörnet i webbläsaren som informerar dig om begäran väntar på godkännande.

    ![Begäran om aktivering är meddelande om väntar på godkännande](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Visa status för dina begäranden

Du kan visa statusen för väntande förfrågningar om att aktivera.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina förfrågningar** att se en lista över dina önskemål.

    ![Azure AD-roller - listan begäranden](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Inaktivera en roll

När en roll har aktiverats, inaktiverar automatiskt när tidsgränsen (varaktighet för berättigande) har nåtts.

Om du har slutfört din administratörsåtgärder tidigt, kan du inaktivera en roll manuellt i Azure AD Privileged Identity Management.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina roller**.

1. Klicka på **aktiva roller** att se din lista över aktiva roller.

1. Hitta den roll som du är klar med och klicka sedan på **inaktivera**.

## <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran

Om du inte behöver aktivering av en roll som kräver godkännande kan avbryta du en väntande begäran när som helst.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina förfrågningar**.

1. För den roll som du vill avbryta, klickar du på den **Avbryt** knappen.

    När du klickar på Avbryt avbryts begäran. Om du vill aktivera rollen igen och måste du skicka en ny begäran om aktivering.

   ![Mina begäranden listan med knappen Avbryt markerad](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Felsöka

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Behörigheter beviljas inte efter aktivering av en roll

När du aktiverar en roll i PIM kan aktiveringen inte direkt spridas till alla portaler som kräver privilegierad roll. Ibland även om ändringen sprids kan web cachelagring i en portal resultera i att ändringen börjar inte gälla omedelbart. Om aktiveringen dröjer är här vad du bör göra.

1. Logga ut från Azure portal och sedan logga in igen.

    När du aktiverar en Azure AD-roll, visas olika aktiveringen. När alla steg har slutförts, visas en **logga ut** länk. Du kan använda den här länken för att logga ut. Detta kommer att lösa de flesta fall för aktiveringsfördröjning.

1. I PIM, kontrollerar du att du står som medlem i rollen.

## <a name="next-steps"></a>Nästa steg

- [Aktivera Mina roller för Azure-resurs i PIM](pim-resource-roles-activate-your-roles.md)
