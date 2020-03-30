---
title: Använda riskidentifieringar med Azure Active Directory Villkorlig åtkomst
description: I den här snabbstarten får du lära dig hur du kan konfigurera en Azure Active Directory (Azure AD) villkorlig åtkomstprincip för att blockera inloggningar baserat på sessionsrisker.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: be447b001b0b2e14999aac98ba2125f8cbfe9853
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77186616"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Snabbstart: Blockera åtkomst när en sessionsrisk identifieras med Azure Active Directory Villkorlig åtkomst  

Om du vill skydda din miljö kanske du vill blockera misstänkta användare från inloggning. [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analyserar varje inloggning och beräknar sannolikheten för att ett inloggningsförsök inte utfördes av den legitima ägaren av ett användarkonto. Sannolikheten (låg, medel, hög) anges i form av ett beräknat värde som kallas [inloggningsrisknivåer](concept-conditional-access-conditions.md#sign-in-risk). Genom att ange inloggningsriskvillkoret kan du konfigurera en princip för villkorlig åtkomst för att svara på specifika inloggningsrisknivåer.

Den här snabbstarten visar hur du konfigurerar en [princip för villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) som blockerar en inloggning när en konfigurerad inloggningsrisknivå har identifierats.

![Skapa princip](./media/app-sign-in-risk/1000.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra scenariot i den här självstudien behöver du:

- **Åtkomst till en Azure AD Premium P2-utgåva** – Även om villkorlig åtkomst är en Azure AD Premium P1-funktion behöver du en P2-utgåva eftersom scenariot i den här snabbstarten kräver identitetsskydd.
- **Identitetsskydd** - Scenariot i den här snabbstarten kräver att identitetsskydd aktiveras. Om du inte vet hur du aktiverar identitetsskydd läser du [Aktivera Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).
- **Tor Browser** - [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) är utformad för att hjälpa dig att bevara din integritet på nätet. Identity Protection identifierar en inloggning från en Tor-webbläsare som inloggningar från anonyma IP-adresser, som har en medelhög risknivå. Mer information finns i [Azure Active Directory-riskidentifieringar](../reports-monitoring/concept-risk-events.md).  
- **Ett testkonto som heter Alain Charon** - Om du inte vet hur du skapar ett testkonto läser [du Lägga till molnbaserade användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testa din inloggning

Målet med det här steget är att se till att ditt testkonto kan komma åt din klient med hjälp av Tor Browser.

**Så här testar du inloggningen:**

1. Logga in på din [Azure-portal](https://portal.azure.com) som **Alain Charon**.
1. Logga ut.

## <a name="create-your-conditional-access-policy"></a>Skapa principen villkorlig åtkomst

Scenariot i den här snabbstarten använder en inloggning från en Tor-webbläsare för att generera en upptäckt **inloggning från anonym-IP-adresser** riskidentifiering. Risknivån för denna riskdetektering är medelhög. Om du vill svara på den här riskidentifieringen ställer du in inloggningsriskvillkoret till medium. I en produktionsmiljö bör du ställa in inloggningsriskvillkoret antingen till hög eller till medelhög och hög.

Det här avsnittet visar hur du skapar den obligatoriska principen för villkorlig åtkomst. I din policy anger du:

| Inställning | Värde |
| --- | --- |
| Användare och grupper | Alain Charon  |
| Molnappar | Alla molnappar |
| Inanmälningsrisk | Medel |
| Bevilja | Blockera åtkomst |

![Skapa princip](./media/app-sign-in-risk/130.png)

**Så här konfigurerar du principen för villkorlig åtkomst:**

1. Logga in på din [Azure-portal](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Klicka på **Azure Active Directory**i Azure-portalen i det vänstra navigeringsfältet .

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Klicka på **Villkorlig åtkomst**i avsnittet **Säkerhet** på sidan Azure **Active Directory** .

   ![Villkorlig åtkomst](./media/app-sign-in-risk/03.png)

1. Klicka på **Lägg till**i verktygsfältet högst upp på sidan **Villkorlig åtkomst.**

   ![Namn](./media/app-sign-in-risk/108.png)

1. Skriv Blockera åtkomst för **medelhög risknivå**i textrutan **Namn** på den **nya** sidan .

   ![Namn](./media/app-sign-in-risk/104.png)

1. Klicka på Användare **och grupper**i avsnittet **Tilldelning** .

   ![Användare och grupper](./media/app-sign-in-risk/06.png)

1. På sidan **Användare och grupper:**

   ![Villkorlig åtkomst](./media/app-sign-in-risk/107.png)

   1. Klicka på **Markera användare och grupper**och välj sedan Användare och **grupper**.
   1. Klicka på **Markera**.
   1. Välj **Alain Charon**på sidan **Välj** och klicka sedan på **Markera**.
   1. Klicka på **Klar**på sidan **Användare och grupper** .
1. Klicka på **Molnappar**.

   ![Molnappar](./media/app-sign-in-risk/08.png)

1. På sidan **Molnappar:**

   ![Villkorlig åtkomst](./media/app-sign-in-risk/109.png)

   1. Klicka på **Alla molnappar**.
   1. Klicka på **Klar**.
1. Klicka på **Villkor**.

   ![Åtkomstkontroller](./media/app-sign-in-risk/19.png)

1. På sidan **Villkor:**

   ![Risknivån för inloggning](./media/app-sign-in-risk/21.png)

   1. Klicka på **Inloggningsrisk**.
   1. Som **Konfigurera**klickar du på **Ja**.
   1. Som inloggningsrisknivå väljer du **Medel**.
   1. Klicka på **Markera**.
   1. Klicka på **Klar**på sidan **Villkor** .
1. Klicka på **Bevilja**i avsnittet **Access-kontroller** .

   ![Åtkomstkontroller](./media/app-sign-in-risk/10.png)

1. På **grant-sidan:**

   ![Villkorlig åtkomst](./media/app-sign-in-risk/105.png)

   1. Välj **Blockera åtkomst**.
   1. Klicka på **Markera**.
1. Klicka på **På**i avsnittet **Aktivera princip** .

   ![Aktivera princip](./media/app-sign-in-risk/18.png)

1. Klicka på **Skapa**.

## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat principen villkorlig åtkomst vill du förmodligen veta om den fungerar som förväntat. Som ett första steg använder du verktyget Villkorlig åtkomst **vad händer om-principverktyget** för att simulera en inloggning av testanvändaren. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.  

När du kör **vad händer om principverktyget** för det här scenariot bör **blockera åtkomst för medelhög risknivå** visas under **Principer som ska gälla**.

![Användare](./media/app-sign-in-risk/117.png)

**Så här utvärderar du principen för villkorlig åtkomst:**

1. På sidan [Villkorlig åtkomst - Principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klickar du på Vad händer **om**på menyn högst upp .  

   ![What If](./media/app-sign-in-risk/14.png)

1. Klicka på **Användare,** välj **Alan Charon** på sidan **Användare** och klicka sedan på **Välj**.

   ![Användare](./media/app-sign-in-risk/116.png)

1. Som **inloggningsrisk**väljer du **Medel**.

   ![Användare](./media/app-sign-in-risk/119.png)

1. Klicka på **Vad händer om**.

## <a name="test-your-conditional-access-policy"></a>Testa principen för villkorlig åtkomst

I föregående avsnitt har du lärt dig hur du utvärderar en simulerad inloggning. Förutom en simulering bör du också testa principen villkorlig åtkomst för att se till att den fungerar som förväntat.

Testa din princip genom att försöka logga in på din [Azure-portal](https://portal.azure.com) som **Alan Charon** med tor-webbläsaren. Ditt inloggningsförsök ska blockeras av principen villkorlig åtkomst.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort testanvändaren, tor-webbläsaren och principen villkorlig åtkomst:

- Om du inte vet hur du tar bort en Azure AD-användare läser du [Ta bort användare från Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Om du vill ta bort principen markerar du principen och klickar sedan på **Ta bort** i verktygsfältet snabbåtkomst.

   ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Instruktioner för hur du tar bort Tor-webbläsaren finns i [Avinstallera](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kräv att användningsvillkor accepteras](require-tou.md)
> [Kräv MFA för specifika appar](app-based-mfa.md)
