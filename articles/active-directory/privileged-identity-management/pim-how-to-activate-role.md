---
title: Aktivera eller inaktivera en roll | Microsoft Docs
description: Lär dig hur du aktiverar roller för privilegierade identiteter med Azure Privileged Identity Management-programmet.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 0ad95cab1ae8072004a528e449cf828b32500be8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590403"
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Aktivera eller inaktivera roller i Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management förenklar hur företag hantera privilegierad åtkomst till resurser i Azure AD och andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.  

Om du har gjorts kan en administrativ roll, som innebär att du kan aktivera rollen när du behöver utföra Privilegierade åtgärder. Till exempel om du hanterar ibland Office 365-funktioner kan kan din organisations Privilegierade rolladministratörer inte göra du permanent Global administratör, eftersom rollen påverkar andra tjänster för. I stället gör de du berättigad till Azure AD-roller, till exempel Exchange Online-administratör. Du kan begära för att aktivera rollen när du behöver ha dess behörighet, och du har administratörsrättigheter för en förutbestämd tidsperiod.

Den här artikeln är för administratörer som behöver aktivera sin roll i Azure AD Privileged Identity Management (PIM). Vi går igenom stegen för att aktivera en roll när du behöver behörigheter och inaktivera rollen när du är klar. Privilegierade rolladministratörer kan dessutom kräva godkännande för att aktivera en roll (förhandsversion). Läs mer om [PIM godkännandearbetsflöden](./azure-ad-pim-approval-workflow.md) här.

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Använda Azure AD Privileged Identity Management-programmet i den [Azure-portalen](https://portal.azure.com/) begär rollaktivering, även om du kommer att fungera i en annan portal eller PowerShell. Om du inte har Azure AD Privileged Identity Management-program på Azure portal, följer du dessa steg för att komma igång.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ditt användarnamn i det övre högra hörnet av Azure-portalen och välj den katalog där du kommer du fungerar.
3. Välj **Alla tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Privileged Identity Management-programmet öppnas.

## <a name="activate-a-role"></a>Aktivera en roll
När du behöver utföra på en roll kan du begära aktivering genom att välja den **Mina roller** navigering alternativ i Azure AD Privileged Identity Management-programmet vänstra navigeringen kolumn.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och välj panelen Azure AD Privileged Identity Management.
2. Välj **Mina roller**. En lista över dina tilldelade berättigade roller visas i grupperingen överst på sidan.
3. Välj en roll som ska aktiveras.
4. Välj **aktivera**. Den **begär rollaktivering** bladet visas.
5. Vissa roller kräver Multi-Factor Authentication (MFA) innan du kan aktivera rollen. Du behöver bara autentiseras en gång per session.
   
    ![Verifiera med MFA innan rollaktivering – skärmbild](./media/pim-how-to-activate-role/PIM_activation_MFA.png)
6. Ange orsaken till att aktiveringsbegäran i textfältet.  Vissa roller kräver att du kan ange ett begäransnummer problem.
7. Välj **OK**.  Om rollen inte kräver godkännande, har nu aktiverats och att rollen visas i listan över aktiva roller (direkt under listan över möjliga rolltilldelningar). Om den [rollen kräver godkännande](./azure-ad-pim-approval-workflow.md) ett popup-meddelande visas om du vill aktivera, kort i det övre högra hörnet i webbläsaren som informerar dig om begäran väntar på godkännande.

    ![Förfrågan väntar på meddelande – skärmbild](./media/pim-how-to-activate-role/PIM_Request_Pending_Toast2.png)

## <a name="deactivate-a-role"></a>Inaktivera en roll
När en roll har aktiverats, inaktiverar automatiskt när tidsgränsen (varaktighet för berättigande) har nåtts.

Om du har slutfört dina administratörsuppgifter tidigt, kan du inaktivera en roll manuellt i Azure AD Privileged Identity Management-programmet.  Välj **Mina roller**, väljer du vilken roll som du är klar med från den **aktiva rolltilldelningar** gruppering och väljer **inaktivera**.  

## <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran
Om du inte behöver aktivering av en roll som kräver godkännande, kan du säga upp en väntande begäran när som helst. Välj bara den **Mina roller** navigering alternativ i Azure AD Privileged Identity Management-programmet vänstra navigeringen kolumn.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och välj panelen Azure AD Privileged Identity Management.
2. Välj **Mina roller**. En lista över dina tilldelade berättigade roller visas i grupperingen överst på sidan.
3. Välj en roll.
4. Välj den **aktivering väntar på godkännande** standardbanderoll på bladet rollen aktivering.
5. Välj **Avbryt** överst i den **väntar på godkännande** bladet.

   ![Avbryta väntande begäran skärmbild](./media/pim-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png)

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Azure AD Privileged Identity Management, har följande länkar mer information.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
