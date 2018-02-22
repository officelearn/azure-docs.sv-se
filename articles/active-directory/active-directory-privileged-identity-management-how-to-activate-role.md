---
title: "Så här aktiverar eller inaktiverar du en roll | Microsoft Docs"
description: "Lär dig mer om att aktivera roller för privilegierade identiteter med Azure Privileged Identity Management-programmet."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 1ce9e2e7-452b-4f66-9588-0d9cd2539e45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a524e1f9d739b42cd63de82291d98ff8c59f2f5f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Så här aktiverar eller inaktiverar roller i Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management förenklar hur företag hantera privilegierad åtkomst till resurser i Azure AD och andra Microsoft online services som Office 365 eller Microsoft Intune.  

Om du har gjorts tillgängliga för en administrativ roll som innebär att du kan aktivera rollen när du behöver utföra Privilegierade åtgärder. Till exempel om du hanterar ibland Office 365-funktioner kan din organisation Privilegierade roller administratörer kan inte göra du permanent Global administratör, eftersom rollen påverkar andra tjänster för. I stället gör de du berättigad till Azure AD-roller såsom Exchange Online-administratör. Du kan begära för att aktivera rollen när du behöver ha dess behörighet, och du har admin-kontroll för en förinställd tidsperiod.

Den här artikeln är administratörer som behöver aktivera deras roll i Azure AD Privileged Identity Management (PIM). Den vägleder dig genom stegen för att aktivera en roll om du behöver behörigheter och inaktivera rollen när du är klar. Privilegierade rollen administratörer kan dessutom kräva godkännande för att aktivera en roll (förhandsversion). Lär dig mer om [PIM godkännandearbetsflöden](./privileged-identity-management/azure-ad-pim-approval-workflow.md) här.

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Använda Azure AD Privileged Identity Management-programmet i den [Azure-portalen](https://portal.azure.com/) begära rollaktivering, även om du ska köras i en annan portal eller PowerShell. Om du inte har programmet Azure AD Privileged Identity Management på Azure-portalen, följer du dessa steg för att komma igång.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ditt användarnamn i det övre högra hörnet i Azure-portalen och välj den katalog där du kommer du att driva.
3. Välj **alla tjänster** och använda Filter-textrutan för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Privileged Identity Management-programmet öppnas.

## <a name="activate-a-role"></a>Aktivera en roll
När du behöver ta på en roll kan du begära aktivering genom att välja den **Mina roller** navigeringsalternativet i Azure AD Privileged Identity Management-programmet vänstra navigeringsfönstret kolumn.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och välj panelen Azure AD Privileged Identity Management.
2. Välj **min roller**. En lista över tilldelade berättigade rollerna visas i grupperingen överst på sidan.
3. Välj en roll som ska aktiveras.
4. Välj **aktivera**. Den **begär rollaktivering** bladet visas.
5. Vissa roller kräver Multi-Factor Authentication (MFA) innan du kan aktivera rollen. Du behöver bara autentisera en gång per session.
   
    ![Kontrollera med MFA innan rollaktivering – skärmbild][2]
6. Ange orsaken till aktiveringsbegäran i textfältet.  Vissa roller kräver att du kan ange en Biljettnummer problem.
7. Välj **OK**.  Om rollen inte kräver godkännande har nu aktiverats och rollen visas i listan över aktiva roller (direkt under listan med tillgängliga rolltilldelningar). Om den [rollen kräver godkännande](./privileged-identity-management/azure-ad-pim-approval-workflow.md) ett popup-meddelande visas om du vill aktivera, kortfattat i det övre högra hörnet i webbläsaren om begäran väntar på godkännande.

    ![Begära väntande meddelanden – skärmbild][3]

## <a name="deactivate-a-role"></a>Inaktivera en roll
När en roll har aktiverats kan inaktiveras automatiskt när tidsgränsen (berättigade varaktighet) har uppnåtts.

Om du har slutfört dina administratörsuppgifter tidigt, kan du inaktivera en roll manuellt i Azure AD Privileged Identity Management-programmet.  Välj **Mina roller**, väljer du vilken roll som du är klar med från den **Active rolltilldelningar** gruppering och välj **inaktivera**.  

## <a name="cancel-a-pending-request"></a>Avbryta en väntande begäran
Om du inte behöver aktivering av en roll som kräver godkännande, kan du avbryta en väntande begäran när som helst. Bara väljer den **Mina roller** navigeringsalternativet i Azure AD Privileged Identity Management-programmet vänstra navigeringsfönstret kolumn.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och välj panelen Azure AD Privileged Identity Management.
2. Välj **min roller**. En lista över tilldelade berättigade rollerna visas i grupperingen överst på sidan.
3. Välj en roll.
4. Välj den **aktivering väntar på godkännande** banderoll i bladet rollen aktivering information.
5. Välj **Avbryt** överst i den **godkännande** bladet.

   ![Avbryta väntande begäran skärmbild][4]

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Azure AD Privileged Identity Management har följande länkar mer information.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
